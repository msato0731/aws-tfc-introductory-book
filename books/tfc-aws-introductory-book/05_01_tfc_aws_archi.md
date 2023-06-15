---
title: "　作成する構成について"
---

:::message

本節では、ハンズオンで作成する構成について紹介します。

:::

## 全体構成

Terraform CloudのWorkspaceはGitHubと連携させます。

AWS環境を本番とSTGで分けている想定で、Workspaceをそれぞの環境ごとに作成します。

本来なら環境ごとにAWSアカウントを分割したほうが良いですが、ハンズオンの都合で1つのAWSアカウントに2つの環境がある想定とします。

Terraform CloudはIAM Roleを使用して、各AWSリソースの操作を行います。

1つのAWSアカウントのため、IAM Roleも1つだけ(※1)用意してVariables Setに登録してそれぞれのWorkspaceで使用できるようにします。

![](/images/chapter_5/01-tfc-aws-book-archi-1.png)

※1 WorkspaceごとにIAM Roleを作成して、それぞれ最小権限を渡すことも可能です。
以下の理由から、IAM Roleを分けずに1つだけ作成しています。

- ハンズオンで作成する構成をシンプルにするため
- Variables Setにも触れたい

## 作成するAWSリソース

VPCとEC2というシンプルな構成を作成します。

VPCを本番/STGの環境ごとに分割します。

![](/images/chapter_5/01-tfc-aws-book-archi-2.png)

## デプロイフロー

以下のデプロイフローを実装します。

1. Pull Request時にそれぞれの環境の`terraform plan`を実行
2. Merge時にSTG環境は`terraform apply`を自動実行
3. Merge後に手動承認後、本番環境に`terraform apply`を自動実行

本番/STG環境両方をMerge後に自動で`terraform apply`を実行することも可能です。(両方に手動承認も可能)

慎重にデプロイ作業を行いため、手動承認を行いたいケースもあります。
そのため、本番は手動承認ありのフローとしました。

今回はmainブランチをリリース用のブランチとして使用しますが、Terraform CloudではWorkspaceごとにブランチを設定できるため環境ごとにブランチを分ける方法も可能です。

<!-- 絵のterraform applyとかしている枠にTerraform Cloudのアイコンを付ける -->

![](/images/chapter_5/01-tfc-aws-book-archi-3.png)
