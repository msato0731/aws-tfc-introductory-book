---
title: "　Workspace Explorer"
---

## 概要

Workspaceの可視性を向上させる機能です。

HCP Terraformの利用規模が増えてくると、Workspaceの数が増えてきます。
例えば、Terraformのバージョアップする際に、1つ1つWorkspaceの設定を確認するのは大変です。

Workspace Explorerを利用することで、各種クエリを使用して複数Workspaceに対して必要な情報を取得できます。

![](/images/chapter_6/01-explorer-1.png)
![](/images/chapter_6/01-explorer-2.png)

## ユースケース

- 古いバージョン(Terraform・モジュール)を使用しているWorkspaceを特定したい
- Runが失敗しているWorkspaceを一覧表示したい

## 参考

- [Explorer for Workspace Visibility \- HCP Terraform \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/cloud-docs/workspaces/explorer)
- [HCP TerraformにExplorer機能が追加されました \| DevelopersIO](https://dev.classmethod.jp/articles/terraform-cloud-explorer/)