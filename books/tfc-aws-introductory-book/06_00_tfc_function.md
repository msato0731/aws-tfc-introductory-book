---
title: "Chapter6: Terraform Cloud その他の機能"
---

:::message
ハンズオンでTerraform Cloudの基本的な操作を体験してもらいました。
本セクションでは、ハンズオン紹介しきれなかった機能の一部を紹介します。

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


### Workspace Explorer

Workspaceの可視性を向上させる機能です。

Terraform Cloudの利用規模が増えてくると、Workspaceの数が増えてきます。
例えば、Terraformのバージョアップする際に、1つ1つWorkspaceの設定を確認するのは大変です。

Workspace Explorerを利用することで、各種クエリを使用して複数Workspaceに対して必要な情報を取得できます。

![](/images/chapter_6/01-explorer-1.png)
![](/images/chapter_6/01-explorer-2.png)

#### ユースケース

- 古いバージョン(Terraform・モジュール)を使用しているWorkspaceを特定したい
- Runが失敗しているWorkspaceを一覧表示したい

#### 参考

- [Explorer for Workspace Visibility \- Terraform Cloud \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/cloud-docs/workspaces/explorer)
- [Terraform CloudにExplorer機能が追加されました \| DevelopersIO](https://dev.classmethod.jp/articles/terraform-cloud-explorer/)

### Cost Estimation

作成されるリソースのコストを推定する機能です。(デフォルトで有効)

RunのPlan時に実行されます。

以下のように、時間(1ごと時間)/月/月差分をが表示されます。

![](/images/chapter_6/02-cost-estimation.png)

この機能で推定されるコストはPolicy機能でも利用できます。

例えば、100USD以上のリソースのデプロイ禁止するといったポリシーを作成できます。

#### ユースケース

- 作成するリソースの推定金額を確認したい

#### 参考

- [Overview \- Cost Estimation \- Terraform Cloud \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/cloud-docs/cost-estimation)

### Drift detection

Health機能の一部で、Terraformコードと実際のインフラの差異(ドリフト)を検出する機能です。

Terraformで作成したリソースをコードを変更せずに、手動で変更するとコードと実際の設定値の間にドリフトが発生します。

ドリフトを放置しておくと、次にTerraformを適用した際に意図しない変更が発生する可能性があります。

Drift detectionを利用することで、ドリフトを早期に検知することが可能です。

注意点として、Health機能のチェック間隔は24時間のためドリフト後、即検知ということはできません。

![](/images/chapter_6/03-drift-detection.png)

#### ユースケース

- ドリフトを早期に検知したい

#### 参考

- [Health \- Terraform Cloud \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/cloud-docs/workspaces/health?_gl=1*rszby1*_ga*Mzg1NTY4ODI4LjE2NTQxNTU5NjI.*_ga_P7S46ZYEKW*MTY2NzA5NDYxNy4yMC4xLjE2NjcwOTUwNTYuMC4wLjA.)
- [(チュートリアル)Manage resource drift \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/tutorials/state/resource-drift)
- [Terraform CloudでAWSリソースの手動変更を検出してみる\(Drift Detection\) \| DevelopersIO](https://dev.classmethod.jp/articles/terraform-cloud-drift-detection/)

### Continuous validation

Health機能の一部で、checkブロックをしたチェックを継続的に行う機能です。

Terraform 1.5で、checkブロックが追加されました。

checkブロックを利用することで、Terraformで作成したリソースに対してテストを行うことができます。

```
例)
- ALBをTerraformでデプロイ、checkブロックでhttpsでALBにアクセス ステータスコードが200を返すか
- AWS BudgetsをTerraformでデプロイ、checkブロックでAWS Budgetsの金額が一定量を超えたら警告
```

checkブロックの内容は、planとapply時に行われます。

しかし、Budgetsの金額のようなcheckはplanとapply時だけではなく継続的に行いたいです。

Continuous validationを利用することで、planとapplyのタイミングだけではなく継続的にテストを行うことができます。

### ユースケース

- checkブロックを利用したテストを継続的に行いたい

#### 参考

- [Health \- Terraform Cloud \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/cloud-docs/workspaces/health?_gl=1*rszby1*_ga*Mzg1NTY4ODI4LjE2NTQxNTU5NjI.*_ga_P7S46ZYEKW*MTY2NzA5NDYxNy4yMC4xLjE2NjcwOTUwNTYuMC4wLjA.)
- [Checks \- Configuration Language \| Terraform \| HashiCorp Developer](https://developer.hashicorp.com/terraform/language/checks?product_intent=terraform)
- [Terraform version 1\.5の新機能達を使ってみた \| DevelopersIO](https://dev.classmethod.jp/articles/terraform-version-1-5/)

### Ephemeral workspaces

Workspaceに削除日を指定することで、自動でWorkspaceのリソースを削除する機能です。

指定時間になると、Destroy用のRunが実行されます。

リソースを自動で削除する仕組みは、色々な方法で構築することができます。

自前で作る場合、構築や運用の手間がかかります。

Ephemeral workspacesを使うことで、少ない手間で自動削除をｄ実現ｄ。

![](/images/chapter_6/05-ephemeral-workspaces.png)

#### ユースケース

- 検証用のリソースを自動で削除したい

#### 参考

- [\[アップデート\]Terraform Cloudにリソースの自動削除ができるephemeral workspaces機能が追加されました\(パブリックベータ\) \| DevelopersIO](https://dev.classmethod.jp/articles/tfc-ephemeral-workspaces/)