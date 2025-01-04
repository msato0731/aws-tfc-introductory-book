---
title: "　Policy as code"
---

## 概要

OPAまたはSentinelで定義したPolicyをチェックする機能です。

![](/images//chapter_6/08-policy.png)

例えば、以下のようなポリシーを定義してポリシーに違反していたらApply不可といった設定が可能です。

- セキュリティグループのingressで「0.0.0.0/0」を開けることを拒否
- 金曜日にデプロイすることを拒否

ポリシーを1から作るのが大変な場合は、Policy Librariesが参考になります。

現状Sentinelのみですが、各種クラウドサービスのPolicyセットが公開されています。

[Browse Policies \| Terraform Registry](https://registry.terraform.io/browse/policies)

## ユースケース

- Policyを設定してセキュリティ確保やレビュー負荷を低減させたい

## 参考

- [(チュートリアル)Enforce OPA policies using HCP Terraform and Styra \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/tutorials/cloud/validation-enforcement)
- [HCP TerraformのOPA Policy適用のチュートリアルをやってみた \| DevelopersIO](https://dev.classmethod.jp/articles/terraform-cloud-opa-policy-tutorial/)
