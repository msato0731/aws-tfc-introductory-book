---
title: "　EC2とVPC用のtfファイル用意"
---

:::message
本節では、Terraform Cloudを使用してデプロイする、EC2とVPC作成用のTerraformファイルを用意します。

:::

## ディレクトリ構成

`prod`と`stg`ディレクトリを作成して、それぞれ`main.tf`ファイルを配置します。

```bash
├── ./prod
│   └── ./prod/main.tf
└── ./stg
    └── ./stg/main.tf
```

## VPCの作成

## EC2の作成
