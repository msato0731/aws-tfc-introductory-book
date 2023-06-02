---
title: "Terraform CloudでAWSにリソースをデプロイする - 作成する構成について"
---

:::message

本チャプターでは、ハンズオンで作成する構成について紹介します。

:::

## 全体構成

Terraform CloudのWorkspaceはGitHubと連携させます。

AWS環境を本番とSTGで分けている想定で、Workspaceをそれぞの環境ごとに作成します。

本来なら環境ごとにAWSアカウントを分割したほうが良いですが、ハンズオンの都合で1つのAWSアカウントに2つの環境がある想定とします。

Terraform CloudはIAM Roleを使用して、各AWSリソースの操作を行います。

1つのAWSアカウントのため、IAM Roleも1つだけ(※1)用意してVariables Setに登録してそれぞれのWorkspaceで使用できるようにします。

![](/images/chapter_4/01-tfc-aws-book-archi-1.png)

※1 WorkspaceごとにIAM Roleを作成して、それぞれ最小権限を渡すことも可能です。
以下の理由から、IAM Roleを分けずに1つだけ作成しています。

- ハンズオンで作成する構成をシンプルにするため
- Variables Setにも触れたい

## 作成するAWSリソース

![](/images/chapter_4/01-tfc-aws-book-archi-2.png)

## デプロイフロー
