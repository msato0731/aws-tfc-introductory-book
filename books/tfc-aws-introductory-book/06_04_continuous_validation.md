---
title: "　Continuous validation"
---


## 概要

Health機能の一部で、checkブロックをしたチェックを継続的に行う機能です。

Terraform 1.5で、checkブロックが追加されました。

checkブロックを利用することで、Terraformで作成したリソースに対してテストを行うことができます。

```text
例)
- ALBをTerraformでデプロイ、checkブロックでhttpsでALBにアクセス ステータスコードが200を返すか
- AWS BudgetsをTerraformでデプロイ、checkブロックでAWS Budgetsの金額が一定量を超えたら警告
```

checkブロックの内容は、planとapply時に行われます。

しかし、Budgetsの金額のようなcheckはplanとapply時だけではなく継続的に行いたいです。

Continuous validationを利用することで、planとapplyのタイミングだけではなく継続的にテストを行うことができます。

## ユースケース

- checkブロックを利用したテストを継続的に行いたい

## 参考

- [Health \- HCP Terraform \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/cloud-docs/workspaces/health?_gl=1*rszby1*_ga*Mzg1NTY4ODI4LjE2NTQxNTU5NjI.*_ga_P7S46ZYEKW*MTY2NzA5NDYxNy4yMC4xLjE2NjcwOTUwNTYuMC4wLjA.)
- [Checks \- Configuration Language \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/language/checks?product_intent=terraform)
- [Terraform version 1\.5の新機能達を使ってみた \| DevelopersIO](https://dev.classmethod.jp/articles/terraform-version-1-5/)