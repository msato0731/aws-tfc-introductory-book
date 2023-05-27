---
title: "Terraform Cloudの基本概念"
---

## Organization

Terraform Cloudの一番大きい管理単位です。

料金プランはOrganization単位で選択します。

## Project

後述するWorkspaceをグルーピングする単位です。

Project単位でユーザーに権限を渡したりすることができます。

## Workspace

Terraformリソースを管理する単位です。

Workspaceには、以下の機能があります。

- Terraformの実行
- Stateファイルの管理
- シークレットの管理

Workspaceは1つのStateファイルを管理します。

そのため、StateファイルごとにWorkspaceを作成します。

## Variables

## States

## Runs