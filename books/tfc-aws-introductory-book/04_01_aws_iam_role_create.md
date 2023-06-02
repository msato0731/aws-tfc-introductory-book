---
title: "Terraform CloudでAWSにリソースをデプロイする - Terraform Cloud用IAMロール作成"
---

:::message
本チャプターでは、Terraform CloudでAWSを操作するためのIAMロールを作成します。

:::

## Terraform Cloud用IAMロールの作成

Terraform Cloudは、OpenID Connectを使用してAWSやAzure、Google Cloudに対して動的なクレデンシャルを生成できます。

永続的なIAMユーザーを作成せずに、IAMロールを使用して認証ができます。

Terraform CloudでAWSリソースを操作できるように、AWSアカウントにTerraform Cloud用のIAMロールを用意します。

Terraform Cloud用のIAMロールはTerraformで作成します。

### IAMユーザーの作成

IAMロール作成後は不要になりますが、Terraform Cloud用のIAMロールを作成するTerraformを流すために一時的にIAMユーザーとIAMアクセスキーを作成します。

CloudShellを開いて、以下のコマンドを実行します。

![](/images/chapter_4/aws-iam-role-1.png)

```bash
aws iam create-user --user-name tmp-tfc-user
aws iam attach-user-policy --user-name tmp-tfc-user --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
aws iam create-access-key --user-name tmp-tfc-user
```

`aws iam create-access-key`コマンドで出力される`AccessKeyId`と`SecretAccessKey`は、この後使うためメモしておいてください。

### Terraform Cloud用のIAMロールを作成

<!-- TODO: IAMロール用のTerraformの説明 -->

ローカルでコンソールを開いて以下の作業を行います。

本書のサンプルコードリポジトリをGit Cloneして、IAMロール作成用のフォルダに移動します。

```bash
git clone git@github.com:msato0731/aws-tfc-introductory-book-samples.git
cd trust/iam_role
```

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

:::details trust/test/main.tf
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
:::

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
