---
title: "Terraform CloudでAWSにリソースをデプロイする - Terraform Cloud用IAMロール作成"
---

:::message
本チャプターでは、Terraform CloudでAWSを操作するためのIAMロールを作成します。

:::

![](/images/chapter_5/02-iam-role-archi.png)

## Terraform Cloud用IAMロールの作成

Terraform Cloudは、OpenID Connectを使用してAWSやAzure、Google Cloudに対して動的なクレデンシャルを生成できます。

永続的なIAMユーザーを作成せずに、IAMロールを使用して認証ができます。

Terraform CloudでAWSリソースを操作できるように、AWSアカウントにTerraform Cloud用のIAMロールを用意します。

Terraform Cloud用のIAMロールはTerraformで作成します。

### IAMユーザーの作成

IAMロール作成後は不要になりますが、Terraform Cloud用のIAMロールを作成するTerraformを流すために一時的にIAMユーザーとIAMアクセスキーを作成します。

CloudShellを開いて、以下のコマンドを実行します。

![](/images/chapter_5/aws-iam-role-1.png)

```bash
aws iam create-user --user-name tmp-tfc-user
aws iam attach-user-policy --user-name tmp-tfc-user --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
aws iam create-access-key --user-name tmp-tfc-user
```

`aws iam create-access-key`コマンドで出力される`AccessKeyId`と`SecretAccessKey`は、この後使うためメモしておいてください。

### Terraform Cloud用のIAMロールを作成

<!-- TODO: IAMロール用のTerraformの説明 -->

#### tfファイルの用意

IAMロール用のTerraformを書いていきます。

ローカルでコンソールを開いて以下の作業を行います。

```bash
mkdir -p trust/iam_role
cd trust/iam_role
```

以下のファイルを用意します。

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

主に、Terraform Cloud用のIAMロールとアタッチするIAMポリシーを定義しています。

OIDC用のサムプリントは、べた書きしなくても`data "tls_certificate"`で取得することが可能です。

今回はTerraform CloudのOrganizationのすべてのWorkspaceに対して、`assume_role_policy`でIAM Roleの引き受けを許可しています。

特定のWorkspaceやProjectまたRun・Planといったフェーズごとに引き受けられる条件を指定することも可能です。

権限はEC2とSQSを許可しています。EC2の他にSQSを許可している理由は、動作確認でSQSのリソースを作成するためです。

<!-- TODO: その他のファイル -->

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

動作確認では、SQSを作成します。

```hcl: trust/test/main.tf
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0.1"
    }
  }
  cloud {
    workspaces {
      name = "tfc-iam-role-test"
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

Terraform Cloudを使用したことがない場合は、以下のコマンドを実行してローカルからTerraform Cloudに接続するための認証情報を作成します。

```bash
terraform login
```

次にテスト用のリソースディレクトリに移動して、Workspaceを作成します。

```bash
cd trust/test
export TF_CLOUD_ORGANIZATION=<Organization名>
terraform init
```

Terraform Cloudの画面を見てみると、`tfc-iam-role-test`というWorkspaceが作成されていることを確認できます。

![](/images/chapter_4/aws-iam-role-2.png)

Workspaceが作成したTerraform Cloud用のIAMロールを使用するように設定する必要があります。

Workspace `tfc-iam-role-test` -> Variablesの順に選択します。

以下のWorkspace variablesを設定します。

| Variable category  |  Key  |  Value  |  Sensitive  |
| ---- | ---- | ---- | ---- |
|  Environment variable  |  TFC_AWS_PROVIDER_AUTH  |  true  | No  |
|  Environment variable  |  TFC_AWS_RUN_ROLE_ARN  |  <IAM Role用TerraformのOutputs `role_arn`>  |  No  |

![](/images/chapter_4/aws-iam-role-3.png)

Variablesの設定ができたら、Terraformを実行します。
Terraform Cloud上でterraformコマンドが実行されるため、ローカルにはAWS認証情報は不要です。

```bash
terraform plan
terraform apply
```

<!-- テスト用リソース削除の手順も -->

### IAMユーザーの削除

IAMロールを使って、Terraform CloudからAWSリソースを操作できるようになったためIAMユーザーは削除します。

```bash
aws iam delete-access-key --user-name tmp-tfc-user --access-key-id <AccessKeyId>
aws iam detach-user-policy --user-name tmp-tfc-user --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
aws iam delete-user --user-name tmp-tfc-user
```
