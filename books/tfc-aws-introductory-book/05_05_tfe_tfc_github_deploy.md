---
title: "　(tfe provider利用版)HCP TerraformとGitHubリポジトリを接続してデプロイを実行"
---

:::message
tfe providerを使うことでHCP Terraformの設定をTerraformで管理できます。

https://registry.terraform.io/providers/hashicorp/tfe/latest

前節「HCP TerraformとGitHubリポジトリを接続してデプロイを実行」のHCP Terraformの設定をTerraformで管理する方法を紹介します。

:::

![](/images/chapter_5/04-00-diagram.png)

## 前提

この手順は前節「HCP TerraformとGitHubリポジトリを接続してデプロイを実行」を実施していなくても実行できます。

以下が手順の前提条件です。

- 「HCP Terraform用IAMロール作成」まで完了している
- 今回利用するGitHub OrganizationsにHCP Terraform用GitHub Appをインストール済み
  - 前節まで実施済みであれば、インストール済み

GitHub Appのインストール状況は、VCS Driven Workspace作成時に今回選択したいリポジトリが表示されていればインストール済みです。

GitHub App未インストールの場合は、「HCP TerraformとGitHubリポジトリを接続してデプロイを実行」の「Workspace作成(VCS Driven)」の部分を実行する必要があります。

GitHub AppはHCP Terraform WorkspaceでGitHubとの接続を行う際にインストールされます。

## tfファイルの説明

[サンプルリポジトリ](https://github.com/msato0731/aws-tfc-introductory-book-samples)の`infra/chapter5/tfe`が今回利用するコードです。

```bash
cd infra/chapter5/tfe
cat main.tf
```

Project・Variable Set・Workspaceを作成するコードです。

```hcl: main.tf
data "tfe_github_app_installation" "this" {
  name = var.github_organization_name
}

resource "tfe_project" "this" {
  organization = var.organization_name
  name         = var.project_name
}

resource "tfe_variable_set" "this" {
  name         = var.variable_set_name
  organization = var.organization_name
}

resource "tfe_project_variable_set" "this" {
  project_id      = tfe_project.this.id
  variable_set_id = tfe_variable_set.this.id
}

resource "tfe_variable" "aws_provider_auth" {
  key             = "TFC_AWS_PROVIDER_AUTH"
  value           = "true"
  category        = "env"
  variable_set_id = tfe_variable_set.this.id
}

resource "tfe_variable" "aws_run_role_arn" {
  key             = "TFC_AWS_RUN_ROLE_ARN"
  value           = var.aws_run_role_arn
  category        = "env"
  variable_set_id = tfe_variable_set.this.id
}

resource "tfe_workspace" "prod" {
  name                  = "prod-${var.workspace_name_suffix}"
  organization          = var.organization_name
  project_id            = tfe_project.this.id
  auto_apply            = false
  file_triggers_enabled = false
  terraform_version     = "~> 1.10.2"
  working_directory     = "infra/aws/chapter5/prod"

  vcs_repo {
    identifier                 = "${var.github_organization_name}/aws-tfc-introductory-book-samples"
    branch                     = "main"
    github_app_installation_id = data.tfe_github_app_installation.this.id
  }
}

resource "tfe_workspace" "stg" {
  name                  = "stg-${var.workspace_name_suffix}"
  organization          = var.organization_name
  project_id            = tfe_project.this.id
  auto_apply            = true
  file_triggers_enabled = false
  terraform_version     = "~> 1.10.2"
  working_directory     = "infra/aws/chapter5/stg"

  vcs_repo {
    identifier                 = "${var.github_organization_name}/aws-tfc-introductory-book-samples"
    branch                     = "main"
    github_app_installation_id = data.tfe_github_app_installation.this.id
  }
}
```

## HCP Terraform関連リソース作成(Project・Variables Set・Workspace)

Terraformでリソースを作成します。

各自の環境で異なる値を表現するために、terraform.tfvarsを用意します。

```bash
cp terraform.tfvars.sample terraform.tfvars
```

以下のように編集します。

任意の箇所は、デフォルトで作成される値から変えたい場合にお使いください。(例: 複数名で1 Organizationsを使って手順を実施・前節の環境を残しつつ手順を実行したい等)

```hcl: terraform.tfvars
organization_name        = "<HCP Terraform Organizations名>"
# project_name             = "" # 任意
# variable_set_name        = "" # 任意
# workspace_name_suffix    = "" # 任意
github_organization_name = "<GitHub Organization名またはユーザー名>"
aws_run_role_arn         = "<HCP Terraform用IAMロール ARN>"
```

```bash
terraform init
terraform plan
terraform apply
```

:::message
今回は手順の簡略化のために、tfe provider用のTerraformのバックエンドはローカルとなっています。
tfe provider管理用のWorkspaceを手動で作成し、tfe provider用のTerraformのバックエンドにHCP Terraformを利用する方法もあります。
ガバナンスの観点から、HCP Terraformをバックエンドにする方法が好ましいです。
:::

## 動作確認

[前節](https://zenn.dev/chario/books/tfc-aws-introductory-book/viewer/05_04_tfc_github_deploy#%E5%8B%95%E4%BD%9C%E7%A2%BA%E8%AA%8D)をご確認ください。

## 後片付け

### AWS関連リソース削除

[前節](https://zenn.dev/chario/books/tfc-aws-introductory-book/viewer/05_04_tfc_github_deploy#%E3%83%AA%E3%82%BD%E3%83%BC%E3%82%B9%E5%89%8A%E9%99%A4)をご確認ください。

### HCP Terraform関連リソース削除

HCP Terraform関連リソースもTerraformで管理しているため、Terraform経由で削除可能です。

```bash
terraform destroy
```

destroy実行時に以下のエラーが出る場合は、AWSリソースが残っている可能性があります。

```bash
│ Error: error deleting workspace ws-xxxxxxxxx: This workspace has 16 resources under management and must be force deleted by setting force_delete = true
```

前の手順「AWS関連リソース削除」を再度実行してください。
