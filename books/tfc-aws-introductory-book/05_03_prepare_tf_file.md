---
title: "　tfファイル作成"
---

:::message
本節では、Terraform Cloudを使用してデプロイする、EC2とVPC作成用のTerraformファイルを用意します。

:::

<!-- TODO 作業範囲の図 -->

## サンプルリポジトリのfork

[サンプルリポジトリ](https://github.com/msato0731/aws-tfc-introductory-book-samples)をforkして、本チャプターのディレクトリに移動します。

```bash
git clone <forkしたリポジトリ>
cd aws-tfc-introductory-book-samples/infra/chapter5/
```

## ディレクトリ構成

`prod`と`stg`ディレクトリがあり、それぞれ`main.tf`ファイルを配置しています。

```bash
aws-tfc-introductory-book-samples
└── infra
  └── chapter5
    ├── ./prod
    │   └── ./prod/main.tf
    └── ./stg
        └── ./stg/main.tf
```

## tfファイルの作成

以下のファイルをprodとstgのそれぞれのディレクトリに用意します。

今回はファイルの中身が同じなのであまり意味がないですが、`main.tf`で環境差異を吸収するパターンを想定しています。

```hcl: main.tf
provider "aws" {
  region = "ap-northeast-1"
}

data "aws_availability_zones" "available" {}

locals {
  name     = "${basename(path.cwd)}-tfc-aws-book"
  vpc_cidr = "10.0.0.0/16"
  azs      = slice(data.aws_availability_zones.available.names, 0, 3)
}

module "vpc" {
  source             = "terraform-aws-modules/vpc/aws"
  name               = local.name
  cidr               = local.vpc_cidr
  azs                = local.azs
  private_subnets    = [for k, v in local.azs : cidrsubnet(local.vpc_cidr, 8, k)]
  enable_nat_gateway = false
}

data "aws_ami" "this" {
  most_recent = true
  owners      = ["amazon"]
  filter {
    name   = "name"
    values = ["al2023-ami-2023*"]
  }
}

resource "aws_instance" "main" {
  ami           = data.aws_ami.this.id
  instance_type = "t3.micro"
  subnet_id     = module.vpc.private_subnets[0]
  tags = {
    Name = local.name
  }
}

```

ファイルが用意できたら、GitHubにPushします。

任意の名前(例: `aws-tfc-introductory-book-samples`)で[GitHubリポジトリを作成](https://github.com/new)して、Pushしてください。

```bash
git init
git add .
git commit -m "first commit"
git push origin HEAD
```
