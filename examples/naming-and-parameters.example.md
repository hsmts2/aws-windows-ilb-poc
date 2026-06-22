# 命名・パラメータ例

## 概要

このドキュメントでは、CloudFormation で作成される主なリソース名と、検証環境・本番環境の CIDR 例を整理します。

<br>

## 命名例

CloudFormation のデフォルト値でデプロイした場合、主な Name タグとリソース名は以下になります。

| 種別 | 検証環境 | 本番環境 |
| --- | --- | --- |
| VPC | `aws-windows-ilb-poc-stg-vpc` | `aws-windows-ilb-poc-prod-vpc` |
| Internet Gateway | `aws-windows-ilb-poc-stg-igw` | `aws-windows-ilb-poc-prod-igw` |
| Public Subnet A | `aws-windows-ilb-poc-stg-public-subnet-a` | `aws-windows-ilb-poc-prod-public-subnet-a` |
| Public Subnet C | `aws-windows-ilb-poc-stg-public-subnet-c` | `aws-windows-ilb-poc-prod-public-subnet-c` |
| App Subnet A | `aws-windows-ilb-poc-stg-app-subnet-a` | `aws-windows-ilb-poc-prod-app-subnet-a` |
| App Subnet C | `aws-windows-ilb-poc-stg-app-subnet-c` | `aws-windows-ilb-poc-prod-app-subnet-c` |
| DB Subnet A | `aws-windows-ilb-poc-stg-db-subnet-a` | `aws-windows-ilb-poc-prod-db-subnet-a` |
| DB Subnet C | `aws-windows-ilb-poc-stg-db-subnet-c` | `aws-windows-ilb-poc-prod-db-subnet-c` |
| NAT Gateway A | `aws-windows-ilb-poc-stg-nat-a` | `aws-windows-ilb-poc-prod-nat-a` |
| NAT Gateway C | なし | `aws-windows-ilb-poc-prod-nat-c` |
| Bastion Host | `aws-windows-ilb-poc-stg-bastion-01` | `aws-windows-ilb-poc-prod-bastion-01` |
| Linux Server 01 | `aws-windows-ilb-poc-stg-linux-app-01` | `aws-windows-ilb-poc-prod-linux-app-01` |
| Linux Server 02 | なし | `aws-windows-ilb-poc-prod-linux-app-02` |
| Windows Server 01 | `aws-windows-ilb-poc-stg-windows-app-01` | `aws-windows-ilb-poc-prod-windows-app-01` |
| Windows Server 02 | なし | `aws-windows-ilb-poc-prod-windows-app-02` |
| Linux Internal ALB | `aws-windows-ilb-poc-stg-app-ilb` | `aws-windows-ilb-poc-prod-app-ilb` |
| Windows Internal ALB | `aws-windows-ilb-poc-stg-win-ilb` | `aws-windows-ilb-poc-prod-win-ilb` |
| Linux Target Group | `aws-windows-ilb-poc-stg-app-tg` | `aws-windows-ilb-poc-prod-app-tg` |
| Windows Target Group | `aws-windows-ilb-poc-stg-win-tg` | `aws-windows-ilb-poc-prod-win-tg` |
| RDS | `aws-windows-ilb-poc-stg-db` | `aws-windows-ilb-poc-prod-db` |

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
