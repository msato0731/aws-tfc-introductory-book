---
title: "　No Code Provisioning"
---

## 概要

Terraform CloudのGUI上から値を渡してリソースを作成できる機能です。

![](/images/chapter_6/07-no-code-provisioning.png)

利用の流れは以下の通りです。

1. (管理者)Terraform Moduleを作成
2. (管理者)Private RegistryにModuleを登録
3. (管理者)Module登録時に`No Code Provisioning`の設定を有効化
4. (利用者)Private RegistryからModuleを選択
5. (利用者)GUIで必要な値を渡す
6. (利用者)リソースが作成される
   1. Terraform Cloud上でWorkspaceが作成される
   2. Terraform Moduleのコードに従ってリソースが作成される

Moduleは基本的には通常のModuleと同様ですが、特徴的なのがModule側にProviderの定義を行うことです。

通常Moduleを呼び出すTerraformコード上で、Providerの宣言を行います。

しかし、No Code Provisioningの使用上、Moduleを呼び出すTerraformコードを用意しないためです。

## ユースケース

- リソース作成をセルフサービス化したい

## 参考

- [Designing No\-Code Ready Modules \- No\-Code Provisioning \- Terraform Cloud \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/cloud-docs/no-code-provisioning/module-design?product_intent=terraform)
- [(チュートリアル)Share modules in the private registry \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/tutorials/modules/module-private-registry-share)
- [Terraform Cloud No\-Code Provisioningやってみた \| DevelopersIO](https://dev.classmethod.jp/articles/terraform-cloud-no-code-provisioning/)