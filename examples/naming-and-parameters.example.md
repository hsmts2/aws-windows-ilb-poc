# 命名・パラメータ例

## 概要

公開用リポジトリでは、実環境の名称や ID を使用せず、役割が分かるサンプル名に置き換えます。

CIDR は、構成差分を説明しやすくするため、検証環境と本番環境のレンジを公開用の設計値として記載します。

<br>

## 命名例

| 種別 | 検証環境 | 本番環境 |
| --- | --- | --- |
| VPC | `validation-vpc` | `production-vpc` |
| Public Subnet | `validation-public-subnet-a` | `production-public-subnet-a` |
| Private App Subnet | `validation-private-app-subnet-a` | `production-private-app-subnet-a` |
| Private DB Subnet | `validation-private-db-subnet-a` | `production-private-db-subnet-a` |
| Linux Server | `validation-linux-app-01` | `production-linux-app-01` |
| Windows Server | `validation-windows-app-01` | `production-windows-app-01` |
| Internal ALB | `validation-windows-ilb` | `production-windows-ilb` |
| Bastion Host | `validation-bastion-01` | `production-bastion-01` |
| RDS | `validation-db` | `production-db` |

<br>

## CIDR 例

### 検証環境

| 項目 | サンプル値 |
| --- | --- |
| VPC CIDR | `172.28.48.0/20` |
| Public Subnet A | `172.28.48.0/24` |
| Public Subnet C | `172.28.49.0/24` |
| Private App Subnet A | `172.28.50.0/24` |
| Private App Subnet C | `172.28.51.0/24` |
| Private DB Subnet A | `172.28.52.0/24` |
| Private DB Subnet C | `172.28.53.0/24` |

### 本番環境

| 項目 | サンプル値 |
| --- | --- |
| VPC CIDR | `172.28.32.0/20` |
| Public Subnet A | `172.28.32.0/24` |
| Public Subnet C | `172.28.33.0/24` |
| Private App Subnet A | `172.28.34.0/24` |
| Private App Subnet C | `172.28.35.0/24` |
| Private DB Subnet A | `172.28.36.0/24` |
| Private DB Subnet C | `172.28.37.0/24` |

<br>

## プレースホルダー例

| 項目 | サンプル値 |
| --- | --- |
| 管理元CIDR | `<YOUR_ADMIN_IP>/32` |
| EC2 キーペア | `<YOUR_KEY_NAME>` |
| Amazon Linux 2023 AMI | `<YOUR_AL2023_AMI_ID>` |
| Windows Server AMI | `<YOUR_WINDOWS_AMI_ID>` |
| RDS パスワード | `<YOUR_DB_MASTER_PASSWORD>` |

<br>

## 匿名化ルール

| 実情報 | 公開用の扱い |
| --- | --- |
| 実VPC名 | サンプル名へ置換 |
| 実Subnet名 | サンプル名へ置換 |
| 実EC2名 | 役割ベースのサンプル名へ置換 |
| EC2インスタンスID | 記載しない |
| グローバルIPアドレス | 記載しない |
| 顧客名 / 案件名 / 拠点名 | 記載しない |
| 接続先 / 通知先 | 記載しない |
| 認証情報 | 記載しない |
