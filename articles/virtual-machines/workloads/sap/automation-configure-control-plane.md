---
title: 自動化フレームワークのコントロール プレーンを構成する
description: Azure 上の SAP デプロイ自動化フレームワーク用にデプロイのコントロール プレーンを構成します。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: f44a3c7ec4d6f46dcfaf238200a9284e1f7d4efa
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725181"
---
# <a name="configure-the-control-plane"></a>コントロール プレーンを構成する

[Azure 上の SAP デプロイ自動化フレームワーク](automation-deployment-framework.md)のコントロール プレーンは、次のコンポーネントで構成されます。
 - デプロイ機能
 - SAP ライブラリ

:::image type="content" source="./media/automation-deployment-framework/control-plane.png" alt-text="コントロール プレーンの図。":::

## <a name="deployer"></a>デプロイ機能

[デプロイ機能](automation-deployment-framework.md#deployment-components)は、[SAP 自動化フレームワーク](automation-deployment-framework.md)の実行エンジンです。 これは、Terraform と Ansible のコマンドの実行に使用される事前構成済みの仮想マシン (VM) です。 

デプロイ機能の構成は、Terraform tfvars 変数ファイルで行います。

## <a name="terraform-parameters"></a>Terraform パラメーター

次の表に Terraform パラメーターを示します。デプロイ スクリプトを使用しない場合、これらのパラメーターは手動で入力する必要があります

> [!div class="mx-tdCol2BreakAll "]
> | 変数                | 説明                           | Type       |
> | ----------------------- | ------------------------------------- | ---------- | 
> | `tfstate_resource_id`   | Terraform 状態ファイルを含む SAP ライブラリ内のストレージ アカウントの Azure リソース識別子 | 必須   | 


### <a name="generic-parameters"></a>ジェネリック パラメーター

次の表に、リソース グループとリソースの名前付けを定義するパラメーターを示します。

> [!div class="mx-tdCol2BreakAll "]
> | 変数                | 説明                           | Type       | 
> | ----------------------- | ------------------------------------- | ---------- | 
> | `environment`           | ワークロード ゾーンの 5 文字の識別子。 たとえば、運用環境では `PROD`、非運用環境では `NP` です。 | Mandatory |
> | `location`              | デプロイする Azure リージョン。     | 必須   | 
> | `resource_group_name`   | 作成するリソース グループの名前 | オプション   |
> | `resource_group_arm_id` | 既存のリソース グループの Azure リソース識別子 | オプション   | 

### <a name="network-parameters"></a>ネットワーク パラメーター

自動化フレームワークでは、仮想ネットワークとサブネットの作成 (グリーン フィールド) および既存の仮想ネットワークと既存のサブネットの使用 (ブラウン フィールド) の両方をサポートし、グリーン フィールドとブラウン フィールドを組み合わせることもできます。
 - グリーン フィールドのシナリオでは、仮想ネットワークのアドレス空間とサブネット アドレスのプレフィックスを指定する必要があります 
 - ブラウン フィールドのシナリオでは、仮想ネットワークとサブネットの Azure リソース識別子を指定する必要があります

仮想ネットワーク アドレス空間の推奨 CIDR は /27 です。これにより、32 個の IP アドレス用の領域を確保できます。 CIDR 値 /28 の場合、使用できる IP アドレスは 16 個のみです。 Azure Firewall を含める場合は、CIDR 値 /25 を使用します。Azure Firewall では /26 の範囲が必要であるためです。 

管理サブネットの推奨 CIDR 値は /28 であり、16 個の IP アドレスを使用できます。
ファイアウォール サブネットの推奨 CIDR 値は /26 であり、64 個の IP アドレスを使用できます。

次の表に、ネットワーク パラメーターを示します。

> [!div class="mx-tdCol2BreakAll "]
> | 変数                                    | 説明                                                      | Type       | Notes  |
> | ------------------------------------------  | ---------------------------------------------------------------- | ---------- | ------ |
> | `management_network_name`                   | ネットワークの論理名 (DEV-WEEU-MGMT01-INFRASTRUCTURE) | 必須   | |
> | `management_network_arm_id`                 | 仮想ネットワークの Azure リソース識別子            | オプション   | 既存の環境のデプロイの場合 |
> | `management_network_address_space`          | 仮想ネットワークのアドレス範囲                        | Mandatory  | 新しい環境のデプロイの場合 |
> | `management_subnet_name`                    | サブネットの名前                                           | オプション   | |
> | `management_subnet_address_prefix`          | サブネットのアドレス範囲                                 | Mandatory  | 新しい環境のデプロイの場合 |
> | `management_subnet_arm_id`                  | サブネットの Azure リソース識別子                     | Mandatory  | 既存の環境のデプロイの場合 |
> | `management_subnet_nsg_name`                | ネットワーク セキュリティ グループの名前                      | オプション   | |
> | `management_subnet_nsg_arm_id`              | ネットワーク セキュリティ グループの Azure リソース識別子     | Mandatory  | 既存の環境のデプロイでは必須 |
> | `management_subnet_nsg_allowed_ips`         | Azure Firewall に追加する許可された IP アドレスの範囲           | オプション   | |  
> | `management_firewall_subnet_arm_id`         | ネットワーク セキュリティ グループの Azure リソース識別子     | Mandatory  | 既存の環境のデプロイの場合 |
> | `management_firewall_subnet_address_prefix` | サブネットのアドレス範囲                                 | Mandatory  | 新しい環境のデプロイの場合 | 

### <a name="deployer-virtual-machine-parameters"></a>デプロイ機能仮想マシンのパラメーター

次の表に、デプロイ機能仮想マシンに関連するパラメーターを示します。 

> [!div class="mx-tdCol2BreakAll "]
> | 変数                        | 説明                                                                  | Type       |
> | ------------------------------- | ---------------------------------------------------------------------------- | ---------- |
> | `deployer_size`                 | 使用する仮想マシン SKU を定義します (例: Standard_D4s_v3)        | オプション   |
> | `deployer_image`                | 使用する仮想マシン イメージを定義します。以下を参照してください                          | オプション   |
> | `deployer_disk_type`            | ディスクの種類を定義します (例: Premium_LRS)                               | オプション   |
> | `deployer_use_DHCP`             | Azure サブネットで指定される IP アドレスを使用する必要がある (動的) かどうかを制御します | オプション   |
> | `deployer_private_ip_address`   | 使用するプライベート IP アドレスを定義します                                        | オプション   |
> | `deployer_enable_public_ip`     | デプロイ機能にパブリック IP があるかどうかを定義します                                      | オプション   |

仮想マシン イメージは、次の構造を使用して定義します。 
```python 
{ 
  os_type=""
  source_image_id=""
  publisher="Canonical"
  offer="0001-com-ubuntu-server-focal"
  sku="20_04-lts"
  version="latest"
}
```

### <a name="authentication-parameters"></a>認証パラメーター

次の表では、仮想マシンの認証の定義に使用されるパラメーターを定義します


> [!div class="mx-tdCol2BreakAll "]
> | 変数                                         | 説明                                         | Type      | 
> | ------------------------------------------------ | --------------------------------------------------- | --------- | 
> | `deployer_vm_authentication_type`                | デプロイ機能の既定の認証を定義します | オプション  |
> | `deployer_authentication_username`               | 管理者アカウントの名前                          | オプション  |
> | `deployer_authentication_password`               | 管理者パスワード                              | オプション  |
> | `deployer_authentication_path_to_public_key`     | 認証に使用される公開キーへのパス      | オプション  |
> | `deployer_authentication_path_to_private_key`    | 認証に使用される秘密キーへのパス     | オプション  |

### <a name="key-vault-parameters"></a>キー コンテナーのパラメーター

次の表では、キー コンテナーの情報を定義するために使用されるパラメーターを定義します

> [!div class="mx-tdCol2BreakAll "]
> | 変数                             | 説明                                                                 | Type       | 
> | ------------------------------------ | --------------------------------------------------------------------------- | ---------- | 
> | `user_keyvault_id`                   | ユーザー キー コンテナーの Azure リソース識別子                            | オプション   |
> | `spn_keyvault_id`                    | SPN の詳細を含むユーザー キー コンテナーの Azure リソース識別子 | オプション   |
> | `deployer_private_key_secret_name`   | デプロイ機能の秘密キーの Azure Key Vault シークレット名                | オプション   |
> | `deployer_public_key_secret_name`    | デプロイ機能の公開キーの Azure Key Vault シークレット名                 | オプション   |
> | `deployer_username_secret_name`      | デプロイ機能のユーザー名の Azure Key Vault シークレット名                   | オプション   |
> | `deployer_password_secret_name`      | デプロイ機能のパスワードの Azure Key Vault シークレット名                   | オプション   |

### <a name="other-parameters"></a>その他のパラメーター

> [!div class="mx-tdCol2BreakAll "]
> | 変数                             | 説明                           | Type        | 
> | ------------------------------------ | ------------------------------------- | ----------- | 
> | `firewall_deployment`                | Azure ファイアウォールをデプロイするかどうかを制御するブール型のフラグ | Mandatory   | 
> | `enable_purge_control_for_keyvaults` | キー コンテナーで消去のコントロールを有効にするかどうかを制御するブール型のフラグ。 テスト デプロイにのみ使用します | オプション    | 
> | `use_private_endpoint`               | プライベート エンドポイントを使用するかどうかを制御するブール型のフラグ。 | オプション    | 

### <a name="example-parameters-file-for-deployer-required-parameters-only"></a>デプロイ機能のサンプル パラメーター ファイル (必須パラメーターのみ)

```bash
# The environment value is a mandatory field, it is used for partitioning the environments, for example (PROD and NP)
environment="MGMT"
# The location/region value is a mandatory field, it is used to control where the resources are deployed
location="westeurope"

# management_network_address_space is the address space for management virtual network
management_network_address_space="10.10.20.0/25"
# management_subnet_address_prefix is the address prefix for the management subnet
management_subnet_address_prefix="10.10.20.64/28"
# management_firewall_subnet_address_prefix is the address prefix for the firewall subnet
management_firewall_subnet_address_prefix="10.10.20.0/26"

deployer_enable_public_ip=true
firewall_deployment=true
```


## <a name="sap-library"></a>SAP ライブラリ

[SAP ライブラリ](automation-deployment-framework.md#deployment-components)は、コントロール プレーンで Terraform 状態ファイルとダウンロードされた SAP インストール メディア用の永続ストレージを提供します。 

SAP ライブラリの構成は、Terraform tfvars 変数ファイルで行います。

### <a name="terraform-parameters"></a>Terraform パラメーター

次の表に Terraform パラメーターを示します。デプロイ スクリプトを使用しないときは、これらのパラメーターを手動で入力する必要があります

> [!div class="mx-tdCol2BreakAll "]
> | 変数                | 説明                           | Type       | 
> | ----------------------- | ------------------------------------- | ---------- | 
> | `deployer_tfstate_key`  | デプロイ機能の状態ファイル名  | 必須   | 

### <a name="generic-parameters"></a>ジェネリック パラメーター

次の表に、リソース グループとリソースの名前付けを定義するパラメーターを示します。


> [!div class="mx-tdCol2BreakAll "]
> | 変数                | 説明                           | Type       |
> | ----------------------- | ------------------------------------- | ---------- |
> | `environment`           | ワークロード ゾーンの 5 文字の識別子。 たとえば、運用環境では `PROD`、非運用環境では `NP` です。 | Mandatory |
> | `location`              | デプロイする Azure リージョン。     | 必須   | 
> | `resource_group_name`   | 作成するリソース グループの名前 | オプション   |
> | `resource_group_arm_id` | 既存のリソース グループの Azure リソース識別子 | オプション   | 

### <a name="deployer-parameters"></a>デプロイ機能のパラメーター

次の表に、リソース グループとリソースの名前付けを定義するパラメーターを示します。

> [!div class="mx-tdCol2BreakAll "]
> | 変数               | 説明                           | Type |
> | ---------------------  | ------------------------------------- | ----- | 
> | `deployer_environment` | ワークロード ゾーンの 5 文字の識別子。 たとえば、運用環境では `PROD`、非運用環境では `NP` です。 | Mandatory |
> | `deployer_location`    | デプロイする Azure リージョン。   | Mandatory |
> | `deployer_vnet`        | デプロイ機能 VNet の論理名 | Mandatory |


### <a name="sap-installation-media-storage-account"></a>SAP インストール メディアのストレージ アカウント

> [!div class="mx-tdCol2BreakAll "]
> | 変数                  | 説明                 | Type       | 
> | ------------------------- | --------------------------- | ---------- | 
> | `library_sapmedia_arm_id` | Azure リソース識別子   | オプション   |

### <a name="terraform-remote-state-storage-account"></a>Terraform リモート状態ストレージ アカウント

> [!div class="mx-tdCol2BreakAll "]
> | 変数                         | 説明                | Type       | 
> | -------------------------------- | -------------------------- | ---------- | 
> | `library_terraform_state_arm_id` | Azure リソース識別子  | オプション   |

### <a name="extra-parameters"></a>追加のパラメーター


> [!div class="mx-tdCol2BreakAll "]
> | 変数                           | 説明                      | Type     | 
> | ---------------------------------- | -------------------------------- | -------- | 
> | `dns_label`                        | プライベート DNS ゾーンの DNS 名 | オプション | 
> | `use_private_endpoint`             | プライベート エンドポイントを使用する            | オプション | 

### <a name="example-parameters-file-for-sap-library-required-parameters-only"></a>SAP ライブラリのサンプル パラメーター ファイル (必須パラメーターのみ)

```bash
# The environment value is a mandatory field, it is used for partitioning the environments, for example (PROD and NP)
environment="MGMT"
# The location/region value is a mandatory field, it is used to control where the resources are deployed
location="westeurope"
# The deployer_environment value is a mandatory field, it is used for identifying the deployer
deployer_environment="MGMT"
# The deployer_location value is a mandatory field, it is used for identifying the deployer
deployer_location="westeurope"
# The deployer_vnet value is a mandatory field, it is used for identifying the deployer
deployer_vnet="DEP00"

```


## <a name="next-step"></a>次のステップ

> [!div class="nextstepaction"]
> [SAP システムを構成する](automation-configure-system.md)
