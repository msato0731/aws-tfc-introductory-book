---
title: "Chapter2: HCP Terraformとは"
---

:::message
本チャプターでは、HCP Terraformとはなにか？ どういった機能があるのか？などのHCP Terraformの概要を解説します。
:::

## HCP Terraformとは

HCP TerraformはTerraformを組織で利用するために必要なデプロイパイプラインやガバナンス機能を提供するSaaS製品です。

[HashiCorp Terraform \- Provision & Manage any Infrastructure](https://www.hashicorp.com/products/terraform)

![](/images/chapter_2/tfc-image.png)

> 画像は[Run Task Integretion](https://developer.hashicorp.com/terraform/cloud-docs/integrations/run-tasks)から引用

同様の機能を持ったTerraform Enterpriseという製品もありますが、こちらは自社のサーバーにTerraform Enterpriseをインストールすることで使用できます。

本書では、HCP Terraformを前提としています。

## HCP Terraformのメリットと利用シーン

Terraform OSS版を使用していて、以下のような課題を感じたことはありませんか。

- Terraformのデプロイを自動化して、ヒューマンエラーを無くしたい
- Terraform利用のガバナンスを強化したい
- 組織内でTerraformコードを再利用して、運用効率を上げたい
- Stateファイルを管理するインフラの構築・運用が負担になっている

HCP Terraformには以下の機能があり、上記の課題の解決に役立つかも知れません。

- CI/CDパイプライン
- ポリシー適用
- Private Registry
- Stateファイル管理

<!-- TODO: Secrets管理も含めるか -->

### CI/CDパイプライン

HCP Terraformでは、VCS(GitHub、GitLab等)と連携したデプロイパイプラインを作成できます。

トリガーとなるブランチや実行フォルダの設定・手動承認の有無など柔軟に設定することができます。

TerraformのCI/CDパイプラインを、Github ActionsであったりCodeシリーズで作成することも可能です。

上記の場合は、ツール上でTerraformをCI/CDするためにパイプラインの作り込みが必要になります。

HCP Terraformを使用することで、Terraformに最適化されたCI/CDパイプラインを少ない工数で作成することができます。

### ポリシー適用

HCP Terraformでは、OPAやSentinelを使ってポリシーを定義することができます。

例えば、以下のようなポリシーを作成することができます。

- EC2インスタンスのインスタンスサイズがt2.micro~mediumのみ作成を許可
- Nameタグがついていないインスタンスは作成不可
- 作成するリソースのコストが100USD/月未満の場合は許可

ポリシーをすべての環境に適用することもできるため、ガバナンスを効かせやすくなります。

### Private Registry

通常Terraform Moduleを他のリポジトリで使うには、Terraform Registryにアップロードする必要があります。(Public Module)

組織内で使うModuleやProviderをに、インターネット上に公開するのが好ましくないパターンはあると思います。

HCP TerraformではPrivate Registryという機能を提供しており、組織内にModuleやProviderを公開することができます。

### Stateファイル管理

Terraformの運用規模が大きくなると、State管理用のリソース(AWSだったら、S3やDynamoDB)の管理が負担になってくることがあります。

HCP TerraformはStateファイルを管理することができます。

Stateファイル管理用のリソースを自分で用意する必要がなくなり、運用の負荷を下げることができます。

他にも以下のメリットがあります。

- 変更履歴の確認やロールバックをGUI上で実行できる
- HCP Terraform上でStateファイルのアクセス制御を行うことができる
