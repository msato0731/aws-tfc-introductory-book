---
title: "Terraform CloudとAWSの環境準備 - Terraform Cloud用IAMロール作成"
---

:::message
本チャプターでは、AWSアカウントの準備とTerraform CloudでAWSを操作するためのIAMロールを作成します。

:::

## AWSアカウントの作成

もし、AWSアカウントを持っていない場合は、以下の手順でAWSアカウントを作成してください。

[AWS アカウント作成の流れ【AWS 公式】](https://aws.amazon.com/jp/register-flow/)

## Terraform Cloud用IAMロールの作成

Terraform CloudでAWSリソースを操作できるように、AWSアカウントにTerraform Cloud用のIAMロールを用意します。

Terraform Cloud用のIAMロールはTerraformで作成します。

### IAMユーザーの作成

IAMロール作成後は不要になりますが、Terraform Cloud用のIAMロールを作成するTerraformを流すために一時的にIAMユーザーとIAMアクセスキーを作成します。

CloudShellを開いて、以下のコマンドを実行します。

![](/images/chapter_3/aws-iam-role-1.png)

```bash
aws iam create-user --user-name tmp-tfc-user
aws iam attach-user-policy --user-name tmp-tfc-user --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
aws iam create-access-key --user-name tmp-tfc-user
```

`aws iam create-access-key`コマンドで出力される`AccessKeyId`と`SecretAccessKey`は、この後使うためメモしておいてください。

### Terraform Cloud用のIAMロールを作成

ローカルでコンソールを開いて以下の作業を行います。

本書のサンプルコードリポジトリをGit Cloneして、IAMロール作成用のフォルダに移動します。

```bash
git clone git@github.com:msato0731/aws-tfc-introductory-book-samples.git
cd trust/
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

### 動作確認

### IAMユーザーの削除

IAMロールを使って、Terraform CloudからAWSリソースを操作できるようになったためIAMユーザーは削除します。

```bash
aws iam delete-access-key --user-name tmp-tfc-user --access-key-id <AccessKeyId>
aws iam detach-user-policy --user-name tmp-tfc-user --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
aws iam delete-user --user-name tmp-tfc-user
```
