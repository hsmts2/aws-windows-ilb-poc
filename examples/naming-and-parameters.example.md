# 命名・パラメータ例

## 概要

公開用リポジトリでは、実環境の名称や値を使用せず、サンプル値のみを記載します。

## 命名例

| 種別 | サンプル名 |
| --- | --- |
| VPC | `sample-prod-vpc` |
| Public Subnet | `sample-prod-public-subnet-a` |
| Private Subnet | `sample-prod-private-app-subnet-a` |
| Internal Load Balancer | `sample-prod-internal-lb` |
| Windows Server | `sample-prod-windows-app-01` |
| Bastion Host | `sample-prod-bastion-01` |
| RDS | `sample-prod-db` |

## パラメータ例

| 項目 | サンプル値 |
| --- | --- |
| VPC CIDR | `10.0.0.0/16` |
| Public Subnet CIDR | `10.0.0.0/24` |
| Private App Subnet CIDR | `10.0.10.0/24` |
| Private DB Subnet CIDR | `10.0.20.0/24` |
| Allowed Admin CIDR | `<YOUR_ADMIN_IP>/32` |
| Key Pair | `<YOUR_KEY_NAME>` |

## 匿名化ルール

| 実情報 | 公開用の扱い |
| --- | --- |
| 実VPC名 | サンプル名へ置換 |
| 実Subnet名 | サンプル名へ置換 |
| 実EC2名 | 役割ベースのサンプル名へ置換 |
| EC2インスタンスID | 記載しない |
| 実CIDR / 実IP | サンプルCIDRへ置換 |
| 顧客名 / 案件名 / 拠点名 | 記載しない |
| 接続先 / 通知先 | 記載しない |
| 認証情報 | 記載しない |
