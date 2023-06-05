---
title: "Terraform Cloudとは"
---

:::message
本チャプターでは、Terraform Cloudとはなにか？ どういった機能があるのか？などのTerraform Cloudの概要を解説します。
:::

## Terraform Cloudとは

Terraform CloudはTerraformを組織で利用するために必要なデプロイパイプラインやガバナンス機能を提供するSaaS製品です。

[HashiCorp Terraform \- Provision & Manage any Infrastructure](https://www.hashicorp.com/products/terraform)

同様の機能を持ったTerraform Enterpriseという製品もありますが、こちらは自社のサーバーにTerraform Enterpriseをインストールすることで使用できます。

本書では、Terraform Cloudを前提としています。

## Terraform Cloudのメリットと利用シーン

Terraform OSS版を使用していて、以下のような課題を感じたことはありませんか。

- Terraformのデプロイを自動化して、ヒューマンエラーを無くしたい
- Terraform利用のガバナンスを強化したい
- 組織内でTerraformコードを再利用して、運用効率を上げたい
- Stateファイルを管理するインフラの構築・運用が負担になっている

課題の解決にTerraform Cloudが役立つかもしれません。

Terraform Cloudには以下の機能があり、課題の解決に役立つかも知れません。

- CI/CDパイプライン
- ポリシー適用
- Private Registry
- Stateファイル管理

<!-- TODO: Secrets管理も含めるか -->

### CI/CDパイプライン

Terraform Cloudでは、VCS(GitHub、GitLab等)と連携したデプロイパイプラインを作成できます。

トリガーとなるブランチや実行フォルダの設定・手動承認の有無など柔軟に設定することができます。

TerraformのCI/CDパイプラインを、Github ActionsであったりCodeシリーズで作成することも可能です。

上記の場合は、ツール上でTerraformをCI/CDするためにパイプラインの作り込みが必要になります。

Terraform Cloudを使用することで、Terraformに最適化されたCI/CDパイプラインを少ない工数で作成することができます。

### ポリシー適用

Terraform Cloudでは、OPAやSentinelを使ってポリシーを定義することができます。

例えば、以下のようなポリシーを作成することができます。

- EC2インスタンスのインスタンスサイズがt2.micro~mediumのみ作成を許可
- Nameタグがついていないインスタンスは作成不可
- 作成するリソースのコストが100USD/月未満の場合は許可

ポリシーをすべての環境に適用することもできるため、ガバナンスを効かせやすくなります。

### Private Registry

通常Terraform Moduleを他のリポジトリで使うには、Terraform Registryにアップロードする必要があります。(Public Module)

組織内で使うModuleやProviderをに、インターネット上に公開するのが好ましくないパターンはあると思います。

Terraform CloudではPrivate Registryという機能を提供しており、組織内にModuleやProviderを公開することができます。

### Stateファイル管理

Terraformの運用規模が大きくなると、State管理用のリソース(AWSだったら、S3やDynamoDB)の管理が負担になってくることがあります。

Terraform CloudはStateファイルを管理することができます。

Stateファイル管理用のリソースを自分で用意する必要がなくなり、運用の負荷を下げることができます。

他にも以下のメリットがあります。

- 変更履歴の確認やロールバックをGUI上で実行できる
- Terraform Cloud上でStateファイルのアクセス制御を行うことができる
