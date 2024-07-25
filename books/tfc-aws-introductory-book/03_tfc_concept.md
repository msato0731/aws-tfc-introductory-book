---
title: "Chapter3: HCP Terraformの基本概念"
---

:::message
本チャプターでは、HCP Terraformの構成要素であるOrganizationやWorkspaceについて解説します。
:::

<!-- Run TaskやPrivate Registory Policy Setも追加したい -->

![](/images/chapter_3/01-tfc-concept.png)

## Organization

HCP Terraformの一番大きい管理単位です。

料金プランはOrganization単位で選択します。

## Project

後述するWorkspaceをグルーピングする単位です。

Project単位でユーザーに権限を渡したりすることができます。

## Workspace

Terraformリソースを管理する単位です。

Workspaceでは、Terraformの実行やStateファイルの管理などを行うことができます。

Terraformの実行フローの設定もWorkspaceで行います。

CLIやVCS(GitHubやGitLab)他のWorkspaceの実行などをトリガーに実行フローを作成することができます。

Workspaceは1つのStateファイルを管理します。

そのため、StateファイルごとにWorkspaceを作成する必要があります。

## Variables

Workspaceには、Variables(変数)を渡すことができます。

Variablesには2種類あります。

- Environment Variables
- Terraform Variables

### Environment Variables

HCP TerraformではRunのタイミングでVMが立ち上がり、VM上で`terraform plan`や`terraform apply`が実行されます。

`Environment Variables`を設定することで、VMに変数を渡すことができます。

例えば、AWS上にリソースをデプロイしたい場合は、IAMロールARNやIAMアクセスキーなどAWSの認証情報を`Environment Variables`として設定します。

### Terraform Variables

`Terraform Variables`はTerraformコード上の`variable`に対して、値を渡したいときに使用できます。

`Environment Variables`は通常の文字列のみ渡すことができますが、`Terraform Variables`は`HCL`で値を渡すこともできます。

## Variables Set

通常の`Variables`はWorkspace単位で設定するため、複数のWorkspaceで使い回すことはできません。

`Variables Set`を使用することでOrganization全体であったり、特定のWorkspaceやProjectにVariablesを適用できます。

ちなみに、Workspace単位のVariablesと同じ値を`Variables Set`で渡した場合は、Workspace単位のVariablesが優先されます。
