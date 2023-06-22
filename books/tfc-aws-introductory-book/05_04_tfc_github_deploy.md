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

作成したProject配下にWorkspaceを作成します。

![](/images/chapter_5/04-project-2.png)

![](/images/chapter_5/04-workspace-1.png)

今回はGitHubのイベントをトリガーに各種Terraformコマンドを実行したいため、`Version control workflow`を選択します。

GitHubを選択して、サンプルコードをPushしたリポジトリを選択します。

![](/images/chapter_5/04-workspace-2.png)

![](/images/chapter_5/04-workspace-3.png)

Workspace名は`prod-aws-tfc-introductory-book`とします。

STG環境用のWorkspaceも必要なため、Workspace名だけ変えて同様の手順を繰り返してください。(`stg-aws-tfc-introductory-book`)

![](/images/chapter_5/04-workspace-4.png)

Project配下に以下2つのWorkspaceが作成できたらOKです。

![](/images/chapter_5/04-workspace-5.png)

## Workspaceの設定

Runを実行する前に、Workspaceにて以下の作業を行う必要があります。

1. Terraform 実行ディレクトリの設定
2. Apply方法の設定

### Terraform 実行ディレクトリの設定

どのディレクトリでTerraformを実行するか指定する必要があります。

今回の場合は、tfファイルが`infra/chapter5/<環境名>`にあります。

このディレクトリを実行ディレクトリとして設定します。

`Workspace名`　-> `Settings` -> `General`の順に選択して、`Terraform Working Directory
`に設定します。

![](/images/chapter_5/04-workspace-setting-1.png)
