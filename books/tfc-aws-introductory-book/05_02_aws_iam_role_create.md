---
title: "　Terraform Cloud用IAMロール作成"
---

:::message
本セクションでは、Terraform CloudでAWSを操作するためのIAMロールを作成します。

:::

![](/images/chapter_5/02-diagram.png)

## Terraform Cloud用IAMロールの作成

Terraform Cloudは、OpenID Connectを使用してAWSやAzure、Google Cloudに対して動的なクレデンシャルを生成できます。

永続的なIAMユーザーを作成せずに、IAMロールを使用して認証ができます。

Terraform CloudでAWSリソースを操作できるように、AWSアカウントにTerraform Cloud用のIAMロールを用意します。

Terraform Cloud用のIAMロールはTerraformで作成します。

### IAMユーザーの作成

IAMロール作成後は不要になりますが、Terraform Cloud用のIAMロールを作成するTerraformを流すために一時的にIAMユーザーとIAMアクセスキーを作成します。

CloudShellを開いて、以下のコマンドを実行します。

![](/images/chapter_5/02-aws-iam-role-1.png)

```bash
aws iam create-user --user-name tmp-tfc-user
aws iam attach-user-policy --user-name tmp-tfc-user --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
aws iam create-access-key --user-name tmp-tfc-user
```

`aws iam create-access-key`コマンドで出力される`AccessKeyId`と`SecretAccessKey`は、この後使うためメモしておいてください。

### Terraform Cloud用のIAMロールを作成

#### tfファイルの用意

IAMロール用のTerraformを書いていきます。

ローカルでコンソールを開いて以下の作業を行います。

```bash
mkdir -p trust/iam_role
cd trust/iam_role
```

以下のファイルを用意します。

<!-- TODO: 必要に応じて権限の見直し -->

```hcl: main.tf
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0.1"
    }
  }
}

provider "aws" {
}

locals {
  tfc_hostname = "app.terraform.io"
}

data "tls_certificate" "tfc_certificate" {
  url = "https://${local.tfc_hostname}"
}

resource "aws_iam_openid_connect_provider" "tfc_provider" {
  url             = data.tls_certificate.tfc_certificate.url
  client_id_list  = ["aws.workload.identity"]
  thumbprint_list = [data.tls_certificate.tfc_certificate.certificates[0].sha1_fingerprint]
}

resource "aws_iam_role" "tfc_role" {
  name = "tfc-role"

  assume_role_policy = <<EOF
{
 "Version": "2012-10-17",
 "Statement": [
   {
     "Effect": "Allow",
     "Principal": {
       "Federated": "${aws_iam_openid_connect_provider.tfc_provider.arn}"
     },
     "Action": "sts:AssumeRoleWithWebIdentity",
     "Condition": {
       "StringEquals": {
         "${local.tfc_hostname}:aud": "${one(aws_iam_openid_connect_provider.tfc_provider.client_id_list)}"
       },
       "StringLike": {
         "${local.tfc_hostname}:sub": "organization:${var.tfc_organization_name}:project:*:workspace:*:run_phase:*"
       }
     }
   }
 ]
}
EOF
}

resource "aws_iam_policy" "tfc_policy" {
  name        = "tfc-policy"
  description = "TFC run policy"

  policy = <<EOF
{
 "Version": "2012-10-17",
 "Statement": [
   {
     "Effect": "Allow",
     "Action": [
       "ec2:*",
       "ssm:*",
       "sqs:*"
     ],
     "Resource": "*"
   }
 ]
}
EOF
}

resource "aws_iam_role_policy_attachment" "tfc_policy_attachment" {
  role       = aws_iam_role.tfc_role.name
  policy_arn = aws_iam_policy.tfc_policy.arn
}
```

Terraform Cloud用のIAMロールとアタッチするIAMポリシーを定義しています。

今回はTerraform CloudのOrganizationのすべてのWorkspaceに対して、`assume_role_policy`でIAM Roleの引き受けを許可しています。

特定のWorkspaceやProjectまたRun・Planといったフェーズごとに引き受けられる条件を指定することも可能です。

権限はEC2とSQSを許可しています。EC2の他にSQSを許可している理由は、動作確認でSQSのリソースを作成するためです。

```hcl: variables.tf
variable "tfc_organization_name" {
  type        = string
  description = "The name of your Terraform Cloud organization"
}
```

```hcl: terraform.tfvars
tfc_organization_name = "<YOUR_ORG>"
```

Terraform CloudのOrganization名は、Variablesとして定義します。

`terraform.tfvars`の`<YOUR_ORG>`の部分を自分のOrganization名に変更してください。

Organization名の確認方法はいくつかありますが、Terraform Cluodの画面上から確認するのが簡単です。

以下のスクショの`tfc-aws-book-test`の部分がOrganization名です。

![](/images/chapter_5/02-aws-iam-role-2.png)

```hcl: outputs.tf
output "role_arn" {
  description = "ARN for trust relationship role"
  value       = aws_iam_role.tfc_role.arn
}
```

作成したIAM Role ARNは、Terraform Cloudにセットする必要があるためOutputsとして出力します。

#### Terraformの実行

先程作成した`AccessKeyId`と`SecretAccessKey`を環境変数に設定します。

```bash
export AWS_ACCESS_KEY_ID=<AccessKeyId>
export AWS_SECRET_ACCESS_KEY=<SecretAccessKey>
```

terraform applyを実行して、IAMロールを作成します。

```bash
terraform init
terraform plan
terraform apply
```

apply時の出力されるOutputsの`role_arn`をこの後使うのため、メモしておきます。

### 動作確認

IAMロールを作成できたら、動作確認をします。

#### 動作確認用のtfファイル用意

動作確認では、SQSを作成します。

以下のファイルを用意します。

```hcl: trust/test/main.tf
terraform {
  cloud {
    organization = "<Organization名>" # 書き換える
    workspaces {
      name = "tfc-iam-role-test" # Organization内で一意な必要あり、必要に応じて置き換える
    }
  }
}

provider "aws" {
  region = "ap-northeast-1"
}

resource "aws_sqs_queue" "my_queue" {
  name = "my-queue"
}
```

ローカルからCLIでTerraform Cloudを利用する場合(CLI Driven Workflow)、`cloud`ブロックの設定が必要です。

Organization名は自身の環境にあった名前に書き換えてください。

Workspace名(`tfc-iam-role-test`の部分)、Workspace名はOrganization内で一意である必要があります。

すでに同じ名前のWorkspaceがある場合は、置き換えてください。

:::message
本書で主に使用するVCS Driven Workflowでは、`cloud`ブロックの設定は無視されWorkspace設定に従って動作します。

そのため、本書ではVCS Driven Workflowで使用するTerraformコード上では`cloud`ブロックの設定は行いません。(上記のSQS作成のコードはCLI Driven Workflow)

[Terraform Cloud Settings \- Terraform CLI \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/cli/cloud/settings)
:::

#### ローカルからTerraform Cloudへの接続

以下のコマンドを実行してローカルからTerraform Cloudに接続するための認証情報を作成します。

`terraform login`コマンドを実行することで、Terraform Cloudのトークンを作成しローカルに保存することができます。

```bash
$ terraform login
Terraform will request an API token for app.terraform.io using your browser.

If login is successful, Terraform will store the token in plain text in
the following file for use by subsequent commands:
    /Users/hoge/.terraform.d/credentials.tfrc.json

Do you want to proceed?
  Only 'yes' will be accepted to confirm.

  Enter a value: yes


---------------------------------------------------------------------------------

Terraform must now open a web browser to the tokens page for app.terraform.io.

If a browser does not open this automatically, open the following URL to proceed:
    https://app.terraform.io/app/settings/tokens?source=terraform-login


---------------------------------------------------------------------------------

Generate a token using your browser, and copy-paste it into this prompt.

Terraform will store the token in plain text in the following file
for use by subsequent commands:
    /Users/hoge/.terraform.d/credentials.tfrc.json

Token for app.terraform.io:
  Enter a value: <作成したトークンを入力>
```

コマンドを実行することで、ブラウザが立ち上がりTerraform Cloudのトークン作成画面に遷移します。
(自動でブラウザが立ち上がらない場合は、上記コマンドの出力結果に表示されるリンク(`https://app.terraform.io/app/settings/tokens?source=terraform-login`)にブラウザでアクセスしてください。)

![](/images/chapter_5/02-aws-iam-role-terraform-token-1.png)
![](/images/chapter_5/02-aws-iam-role-terraform-token-2.png)


Terraform Cloudのコンソール上で作成したトークンを、先程のコマンドを実行したコンソールに貼り付けたら完了です。

#### Workspaceとリソースの作成

次にテスト用のリソースディレクトリに移動して、Workspaceを作成します。

```bash
cd trust/test
terraform init
```

Terraform Cloudの画面を見てみると、`tfc-iam-role-test`というWorkspaceが作成されていることを確認できます。

![](/images/chapter_5/02-aws-iam-role-3.png)

Workspaceが作成したTerraform Cloud用のIAMロールを使用するように設定する必要があります。

Workspace `tfc-iam-role-test` -> Variablesの順に選択します。

以下のWorkspace variablesを設定します。

| Variable category  |  Key  |  Value  |  Sensitive  |
| ---- | ---- | ---- | ---- |
|  Environment variable  |  TFC_AWS_PROVIDER_AUTH  |  true  | No  |
|  Environment variable  |  TFC_AWS_RUN_ROLE_ARN  |  <IAM Role用TerraformのOutputs `role_arn`>  |  No  |

![](/images/chapter_5/02-aws-iam-role-4.png)

:::message alert
よくある間違いとして、Variable categoryを誤って、**Terraform variable**にしてしまうケースがあります。
上記のVariableは`Environment variable`で設定します。
![](/images/chapter_5/02-aws-iam-role-4-variables.png)

以下のようなエラーが出る場合は、Variable Categoryを間違えている可能性が高いため、Variable categoryを見直してみてください。
`Warning: Value for undeclared variable`
`The root module does not declare a variable named "TFC_AWS_RUN_ROLE_ARN"`
:::

Variablesの設定ができたら、Terraformを実行します。
Terraform Cloud上でterraformコマンドが実行されるため、ローカルにAWS認証情報は不要です。

```bash
terraform plan
terraform apply
```

Terraformで定義した、SQSキュー`my-queue`が作成されたことが確認できたら、成功です。

![](/images/chapter_5/02-aws-iam-role-5.png)

### 動作確認用のリソース削除

確認できたら、テスト用のリソースは削除しておきます。

```bash
terraform destroy
```

Workspaceも削除します。

`tfc-iam-role-test` -> `Settings` -> `Destruction and Deletion` -> `Force Delete from Terraform Cloud`の順番に選択して、Workspaceの削除を実行します。

![](/images/chapter_5/02-aws-iam-role-6.png)

### IAMユーザーの削除

これまでの操作で、IAMロールを使ってTerraform CloudからAWSリソースを操作をできるようになりました。

最初に作成したIAMユーザーは不要になったため、以下のコマンドで削除します。

```bash
aws iam delete-access-key --user-name tmp-tfc-user --access-key-id <AccessKeyId>
aws iam detach-user-policy --user-name tmp-tfc-user --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
aws iam delete-user --user-name tmp-tfc-user
```
