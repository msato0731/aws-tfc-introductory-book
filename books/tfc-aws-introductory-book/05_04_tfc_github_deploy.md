---
title: "　Terraform CloudとGitHubリポジトリを接続してデプロイを実行"
---

:::message
本節では、前節で作成したTerraformコードを使用して、Terraform Cloud経由でリソースをデプロイします。

:::

## Projectの作成

Terraform Cloudで、Projectを作成します。

![](/images/chapter_5/04-project-1.png)

Project名は任意の名前をつけてください。(例: aws-tfc-introductory-book)

![](/images/chapter_5/04-project-2.png)

今回は、Project内にSTGとPRODの2つのWorkspaceを作成します。

## Variables Setの作成

AWSリソースをProject配下のWorkspaceが操作できるように、Terraform Cloud用のIAM Role ARNを持つVariables Setを作成します。

作成したVariables SetをProjectに割り当てることで、配下のWorkspaceでAWSリソースの操作ができるようになります。

IAM Role ARNは前の節「Terraform Cloud用IAMロール作成」で使用したARNと同じです。

AWS CLIで確認する場合は、以下のコマンドで確認できます。

```bash
aws iam get-role --role-name tfc-role --query Role.Arn --output text
```

Terraform Cloudのポータルサイト上で、`Settings` -> `Variables sets`の順に選択します。

以下のように必要な項目を設定して、Variables setsを作成します。

![](/images/chapter_5/04-project-2.png)


| Variable category  |  Key  |  Value  |  Sensitive  |
| ---- | ---- | ---- | ---- |
|  Environment variable  |  TFC_AWS_PROVIDER_AUTH  |  true  | No  |
|  Environment variable  |  TFC_AWS_RUN_ROLE_ARN  |  <`role_arn`>  |  No  |

## Workspaceの作成(VCS Driven)



