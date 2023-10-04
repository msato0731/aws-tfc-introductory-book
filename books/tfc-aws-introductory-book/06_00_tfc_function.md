---
title: "Chapter6: Terraform Cloud その他の機能"
---

:::message
ハンズオンでTerraform Cloudの基本的な操作を体験してもらいました。
本セクションでは、ハンズオンで紹介しきれなかった機能の一部を紹介します。

:::

## Terraform Cloudの機能

| カテゴリ                        | 機能名                       | 概要                                                                            |
|-----------------------------|---------------------------|-------------------------------------------------------------------------------|
| Visibility & Optimization   | Workspace Explorer        | Workspaceの情報を一覧で確認できる機能                                                       |
|                             | Cost Estimation           | Plan時に作成するリソースのコスト予測を実施する機能<br/>作成・削除・変更によるコストの変動を把握可能                        |
|                             | Drift detection           | インフラストラクチャの変化を追跡し、Terraformコードと異なる状態（Drift）が発生した場合に警告する機能                     |
|                             | Continuous validation     | カスタムアサーションを定期的に検証するための機能<br/>                                                 |
|                             | Ephemeral workspaces<br/> | 一時的なビルドやテストのための環境を作成する機能<br/>定義した時間を経過後に自動的にWorkspace内のリソースは削除される             |
| Unified workflow management | Private Registry          | 組織内だけで使用可能なTerraformモジュール/プロパイダーを提供する機能                                       |
|                             | No Code Provisioning      | 事前に用意したモジュールを使って、GUIでリソース作成できるを機能                                             |
| Policy & Security           | Policy as code            | ポリシー(OPA・Sentinel)を定義し、実行前にチェックを実施<br/>                                       |
|                             | Run Task                  | サードパーティツールとTerraform Cloudを統合する機能<br/>Plan時にサードパーティツールによるセキュリティチェックを組み込むなどが可能 |

[HashiCorp Terraform: Enterprise Pricing, Packages & Features](https://www.hashicorp.com/products/terraform/pricing)
