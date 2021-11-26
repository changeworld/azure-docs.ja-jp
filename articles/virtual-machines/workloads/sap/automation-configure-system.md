---
title: SAP システム パラメーターを自動化用に構成する
description: パラメーター JSON ファイルを使用して、Azure 上の SAP デプロイ自動化フレームワークの SAP システム プロパティを定義します。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 6b18d33ce3b05809a6dfeb61b3f8337c4d0701cb
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725180"
---
# <a name="configure-sap-system-parameters-for-automation"></a>SAP システム パラメーターを自動化用に構成する

[Azure 上の SAP デプロイ自動化フレームワーク](automation-deployment-framework.md)の構成は、パラメーター ファイルを介して行います。 SAP システム プロパティに関する情報は tfvars ファイルで指定します。自動化フレームワークではこのファイルをデプロイに使用します。 

SAP ワークロード ゾーンの構成は、Terraform tfvars 変数ファイルを使用して行います。

## <a name="terraform-parameters"></a>Terraform パラメーター

次の表に Terraform パラメーターを示します。デプロイ スクリプトを使用しない場合、これらのパラメーターは手動で入力する必要があります。


> [!div class="mx-tdCol2BreakAll "]
> | 変数                  | 説明                                                                                                      | Type       |
> | ------------------------- | ---------------------------------------------------------------------------------------------------------------- | ---------- | 
> | `tfstate_resource_id`     | Terraform 状態ファイルが入る SAP ライブラリ内のストレージ アカウントの Azure リソース識別子 | 必須 * |
> | `deployer_tfstate_key`    | デプロイ機能の状態ファイルの名前                                                                      | 必須 * |
> | `landscaper_tfstate_key`  | ワークロード ゾーンの状態ファイルの名前                                                                 | 必須 * |

\* = 手動デプロイでは必須
## <a name="generic-parameters"></a>ジェネリック パラメーター

次の表に、リソース グループとリソースの名前付けを定義するパラメーターを示します。


> [!div class="mx-tdCol2BreakAll "]
> | 変数                | 説明                           | Type       | 
> | ----------------------- | ------------------------------------- | ---------- | 
> | `environment`           | ワークロード ゾーンの 5 文字の識別子。 たとえば、運用環境では `PROD`、非運用環境では `NP` です。 | Mandatory |
> | `location`              | デプロイする Azure リージョン。     | 必須   | 
> | `resource_group_name`   | 作成するリソース グループの名前 | オプション   |
> | `resource_group_arm_id` | 既存のリソース グループの Azure リソース識別子 | オプション   | 

## <a name="network-parameters"></a>ネットワーク パラメーター

ワークロード ゾーンのデプロイでサブネットがデプロイされていない場合、システムの tfvars ファイルで追加できます。

自動化フレームワークでは、新しい環境のデプロイでの仮想ネットワークとサブネットの作成 (グリーン フィールド) と既存の環境のデプロイでの既存の仮想ネットワークと既存のサブネットの使用 (ブラウン フィールド) が両方ともサポートされ、新しい環境のデプロイと既存の環境のデプロイを組み合わせることもできます。
 - グリーン フィールドのシナリオでは、仮想ネットワークのアドレス空間とサブネット アドレスのプレフィックスを指定する必要があります 
 - ブラウン フィールドのシナリオでは、仮想ネットワークとサブネットの Azure リソース識別子を指定する必要があります

仮想ネットワークのアドレス空間は、すべてのリソースをホストするのに十分な大きさにします

次の表に、ネットワーク パラメーターを示します。


> [!div class="mx-tdCol2BreakAll "]
> | 変数                         | 説明                                                          | Type      | Notes  |
> | -------------------------------- | -------------------------------------------------------------------- | --------- | ------ |
> | `network_logical_name`           | ネットワークの論理名                                      | 必須  |        |       
> | `network_address_space`          | 仮想ネットワークのアドレス範囲                            | Mandatory | 新しい環境のデプロイの場合   |
> | `admin_subnet_name`              | "admin" サブネットの名前                                       | オプション  |         |
> | `admin_subnet_address_prefix`    | "admin" サブネットのアドレス範囲                             | Mandatory | 新しい環境のデプロイの場合  |
> | `admin_subnet_arm_id`            | "admin" サブネットの Azure リソース識別子                 | Mandatory | 既存の環境のデプロイの場合 |
> | `admin_subnet_nsg_name`          | "admin" ネットワーク セキュリティ グループの名前                  | オプション  |         |
> | `admin_subnet_nsg_arm_id`        | "admin" ネットワーク セキュリティ グループの Azure リソース識別子 | Mandatory | 既存の環境のデプロイの場合 |
> | `db_subnet_name`                 | "db" サブネットの名前                                          | オプション  |         |
> | `db_subnet_address_prefix`       | "db" サブネットのアドレス範囲                                | Mandatory | 新しい環境のデプロイの場合  |
> | `db_subnet_arm_id`               | "db" サブネットの Azure リソース識別子                    | Mandatory | 既存の環境のデプロイの場合 |
> | `db_subnet_nsg_name`             | "db" ネットワーク セキュリティ グループの名前                     | オプション  |          |
> | `db_subnet_nsg_arm_id`           | "db" ネットワーク セキュリティ グループの Azure リソース識別子    | Mandatory | 既存の環境のデプロイの場合 |
> | `app_subnet_name`                | "app" サブネットの名前                                         | オプション  |          |
> | `app_subnet_address_prefix`      | "app" サブネットのアドレス範囲                               | Mandatory | 新しい環境のデプロイの場合  |
> | `app_subnet_arm_id`              | "app" サブネットの Azure リソース識別子                   | Mandatory | 既存の環境のデプロイの場合 |
> | `app_subnet_nsg_name`            | "app" ネットワーク セキュリティ グループの名前                    | オプション  |          |
> | `app_subnet_nsg_arm_id`          | "app" ネットワーク セキュリティ グループの Azure リソース識別子   | Mandatory | 既存の環境のデプロイの場合 |
> | `web_subnet_name`                | "web" サブネットの名前                                         | オプション  |          |
> | `web_subnet_address_prefix`      | "web" サブネットのアドレス範囲                               | Mandatory | 新しい環境のデプロイの場合  |
> | `web_subnet_arm_id`              | "web" サブネットの Azure リソース識別子                   | Mandatory | 既存の環境のデプロイの場合 |
> | `web_subnet_nsg_name`            | "web" ネットワーク セキュリティ グループの名前                    | オプション  |          |
> | `web_subnet_nsg_arm_id`          | "web" ネットワーク セキュリティ グループの Azure リソース識別子   | Mandatory | 既存の環境のデプロイの場合 |

\* = 既存の環境のデプロイでは必須

### <a name="database-tier-parameters"></a>データベース層のパラメーター

データベース層では、データベース層のインフラストラクチャを定義します。サポートされているデータベースのバックエンドは次のとおりです。

- `HANA`
- `DB2`
- `ORACLE`
- `ORACLE-ASM`
- `ASE`
- `SQLSERVER`
- `NONE` (この場合、データベース層はデプロイされません)


> [!div class="mx-tdCol2BreakAll "]
> | 変数                          | 説明                                                                              | Type         | Notes              |
> | --------------------------------  | -----------------------------------------------------------------------------------------| -----------  | ------------------ |
> | `database_platform`               | データベース バックエンドを定義します                                                             | 必須     |                    |
> | `database_high_availability`      | データベース層を高可用性でデプロイする場合に定義します                                | オプション     | 「[高可用性構成](automation-configure-system.md#high-availability-configuration)」を参照してください |
> | `database_server_count`           | データベース サーバーの数を定義します                                                   | オプション     | 既定値は 1。 |
> | `database_vm_names`               | 既定の名前付けを許容できない場合に、データベース サーバーの仮想マシン名を定義します | オプション    |                    |
> | `database_size`                   | データベースのサイズ設定情報を定義します                                                  | 必須     | [カスタムのサイズ設定](automation-configure-extra-disks.md)に関する記事を参照してください |
> | `database_sid`                    | データベース SID を定義します                                                                 | 必須     |                    |
> | `db_disk_sizes_filename`          | カスタムのデータベース サイズ設定を定義します                                                       | オプション     | [カスタムのサイズ設定](automation-configure-extra-disks.md)に関する記事を参照してください |
> | `database_sid`                    | データベース SID を定義します                                                                 | 必須     |                    |
> | `database_vm_image`               | 使用する仮想マシン イメージを定義します。以下を参照してください                                      | オプション     |                    |
> | `database_vm_use_DHCP`            | Azure サブネットで指定される IP アドレスを使用する必要がある (動的) かどうかを制御します             | オプション     |                    |
> | `database_vm_db_nic_ips`          | データベース サーバーの静的 IP アドレスを定義します (データベース サブネット)               | オプション     |                    |
> | `database_vm_admin_nic_ips`       | データベース サーバーの静的 IP アドレスを定義します (admin サブネット)                  | オプション     |                    |
> | `database_vm_authentication_type` | データベース仮想マシンの認証の種類 (キー/パスワード) を定義します         | オプション     |                    | 
> | `database_vm_zones`               | 可用性ゾーンを定義します                                                           | オプション     |                    |
> | `database_vm_avset_arm_ids`       | 既存の可用性セットの Azure リソース ID を定義します                                | オプション     | 主に ANF ピン留めと共に使用されます| 
> | `database_no_avset`               | データベース仮想マシンを可用性セットなしでデプロイするかどうかを制御します         | オプション     | 既定値は false です   |
> | `database_no_ppg`                 | データベース サーバーを近接配置グループに配置しないかどうかを制御します       | オプション     | 既定値は false です   |

仮想マシンとオペレーティング システム イメージは、次の構造を使用して定義します。 

```python 
{
  os_type="linux"
  source_image_id=""
  publisher="SUSE"
  offer="sles-sap-15-sp2"
  sku="gen2"
  version="8.2.2021040902"
}
```

### <a name="common-application-tier-parameters"></a>共通アプリケーション層のパラメーター

アプリケーション層ではアプリケーション層のインフラストラクチャを定義します。これはアプリケーション サーバー、セントラル サービス サーバー、Web ディスパッチ サーバーで構成されます


> [!div class="mx-tdCol2BreakAll "]
> | 変数                           | 説明                                                                 | Type       | Notes  |
> | ---------------------------------- | --------------------------------------------------------------------------- | -----------| ------ |
> | `enable_app_tier_deployment`         | アプリケーション層をデプロイするかどうかを定義します                                 | オプション     |        |
> | `sid`                                |  SAP アプリケーション SID を定義します                                            | 必須     |        | 
> | `app_disk_sizes_filename`            | アプリケーション層サーバーのカスタム ディスク サイズ ファイルを定義します          | オプション   | [カスタムのサイズ設定](automation-configure-extra-disks.md)に関する記事を参照してください |
> | `app_tier_authentication_type`     | アプリケーション層仮想マシンの認証の種類を定義します | オプション   |       |
> | `app_tier_use_DHCP`                | Azure サブネットで指定される IP アドレスを使用する必要がある (動的) かどうかを制御します     | オプション   |       |
> | `app_tier_dual_nics`                 | アプリケーション層サーバーに 2 つのネットワーク インターフェイスを設けるかどうかを定義します     | オプション     |       |
> | `app_tier_vm_sizing`                 | アプリケーション層サーバーの VM SKU とディスク レイアウトを定義する参照値 | オプション |

### <a name="application-server-parameters"></a>アプリケーション サーバーのパラメーター


> [!div class="mx-tdCol2BreakAll "]
> | 変数                               | 説明                                                                  | Type       | Notes  |
> | -------------------------------------- | ---------------------------------------------------------------------------- | -----------| ------ |
> | `application_server_count`             | アプリケーション サーバーの数を定義します                                    | 必須     | |
> | `application_server_sku`                 | 使用する仮想マシンの SKU を定義します                                       | オプション   | | 
> | `application_server_image`             | 使用する仮想マシン イメージを定義します                                     | 必須   | | 
> | `application_server_zones`             | アプリケーション サーバーをデプロイする可用性ゾーンを定義します | オプション   | | 
> | `application_server_app_nic_ips[]`     | アプリケーション サーバーの IP アドレスの一覧 (app サブネット)                 | オプション   | `app_tier_use_DHCP` を使用する場合は無視されます |
> | `application_server_app_admin_nic_ips` | アプリケーション サーバーの IP アドレスの一覧 (admin サブネット)               | オプション   | `app_tier_use_DHCP` を使用する場合は無視されます |
> | `application_server_no_ppg`            | アプリケーション サーバーの近接配置グループを制御します                        | オプション   | |
> | `application_server_no_avset`            | アプリケーション サーバーの可用性セットの配置を制御します                       | オプション   | |
> | `application_server_tags`                | アプリケーション サーバーに適用するタグの一覧を定義します              | オプション   | |

### <a name="sap-central-services-parameters"></a>SAP セントラル サービスのパラメーター


> [!div class="mx-tdCol2BreakAll "]
> | 変数                               | 説明                                                          | Type      | Notes  |
> | -------------------------------------- | -------------------------------------------------------------------- | ----------| ------ |
> | `scs_high_availability`                | セントラル サービスを高可用性にするかどうかを定義します                  | オプション    | 「[高可用性構成](automation-configure-system.md#high-availability-configuration)」を参照してください |
> | `scs_instance_number`                    | SCS のインスタンス番号                                           | オプション  |        |
> | `ers_instance_number`                    | ERS のインスタンス番号                                           | オプション  |        |
> | `scs_server_count`                     | SCS サーバーの数を定義します                                    | 必須    |        |
> | `scs_server_sku`                         | 使用する仮想マシンの SKU を定義します                               | オプション  |        | 
> | `scs_server_image`                     | 使用する仮想マシン イメージを定義します                             | 必須  |        | 
> | `scs_server_zones`                     | SCS サーバーをデプロイする可用性ゾーンを定義します | オプション  |        | 
> | `scs_server_app_nic_ips[]`             | SCS サーバーの IP アドレスの一覧 (app サブネット)                 | オプション  | `app_tier_use_DHCP` を使用する場合は無視されます |
> | `scs_server_app_admin_nic_ips`         | SCS サーバーの IP アドレスの一覧 (admin サブネット)               | オプション  | `app_tier_use_DHCP` を使用する場合は無視されます |
> | `scs_server_no_ppg`                    | SCS サーバーの近接配置グループを制御します                        | オプション  |         |
> | `scs_server_no_avset`                    | SCS サーバーの可用性セットの配置を制御します                       | オプション  |         |
> | `scs_server_tags`                        | SCS サーバーに適用するタグの一覧を定義します              | オプション  |         |


### <a name="web-dispatcher-parameters"></a>Web ディスパッチャーのパラメーター


> [!div class="mx-tdCol2BreakAll "]
> | 変数                                | 説明                                                              | Type      | Notes  |
> | --------------------------------------- | ------------------------------------------------------------------------ | --------- | ------ |
> | `webdispatcher_server_count`              | Web ディスパッチャー サーバーの数を定義します                             | 必須  |        |
> | `webdispatcher_server_sku`              | 使用する仮想マシンの SKU を定義します                                   | オプション  |        | 
> | `webdispatcher_server_image`              | 使用する仮想マシン イメージを定義します                                 | オプション  |        | 
> | `webdispatcher_server_zones`              | Web ディスパッチャーをデプロイする可用性ゾーンを定義します | オプション  |        |
> | `webdispatcher_server_app_nic_ips[]`    | Web ディスパッチャー サーバーの IP アドレスの一覧 (app サブネット)          | オプション  | `app_tier_use_DHCP` を使用する場合は無視されます |
> | `webdispatcher_server_app_admin_nic_ips`| Web ディスパッチャー サーバーの IP アドレスの一覧 (admin サブネット)        | オプション  | `app_tier_use_DHCP` を使用する場合は無視されます |
> | `webdispatcher_server_no_ppg`           | Web 近接配置グループの配置を制御します                         | オプション  | |
> | `webdispatcher_server_no_avset`         | Web ディスパッチャーの可用性セットの配置を定義します                        | オプション  | |
> | `webdispatcher_server_tags`             | Web ディスパッチャー サーバーに適用するタグの一覧を定義します       | オプション  | |

### <a name="anchor-virtual-machine-parameters"></a>アンカー仮想マシンのパラメーター

SAP デプロイ自動化フレームワークでは、アンカー仮想マシンを設けることができます。 アンカー仮想マシンは、最初にデプロイする仮想マシンであり、近接配置グループのアンカーとして使用します。

次の表に、アンカー仮想マシンに関連するパラメーターを示します。 


> [!div class="mx-tdCol2BreakAll "]
> | 変数                           | 説明                                                                       | Type        | 
> | ---------------------------------- | --------------------------------------------------------------------------------- | ----------- | 
> | `deploy_anchor_vm`                 | アンカー仮想マシンを使用するかどうかを定義します                                     | オプション      | 
> | `anchor_vm_sku`                    | 使用する VM SKU を定義します。 たとえば、Standard_D4s_v3 です。              | オプション    | 
> | `anchor_vm_image`                    | 使用する VM イメージを定義します。 次のサンプル コードを参照してください。                              | オプション     | 
> | `anchor_vm_use_DHCP`               | Azure サブネットによって提供される動的 IP アドレスを使用するかどうかを制御します。           | オプション    | 
> | `anchor_vm_accelerated_networking` | 高速ネットワークを使用するようにアンカー VM を構成するかどうかを定義します | オプション    | 
> | `anchor_vm_authentication_type`    | アンカー VM のキーとパスワードの認証の種類を定義します       | オプション       | 

仮想マシンとオペレーティング システム イメージは、次の構造を使用して定義します。 
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

SAP システムのデプロイでは、既定で SAP ワークロード ゾーンの資格情報が使用されます。 SAP システムに一意の資格情報が必要な場合は、これらのパラメーターで指定できます。


> [!div class="mx-tdCol2BreakAll "]
> | 変数                           | 説明                          | Type        | 
> | ---------------------------------- | -------------------------------------| ----------- | 
> | `automation_username`              | 管理者アカウントの名前           | オプション    |
> | `automation_password`              | 管理者パスワード               | オプション    |
> | `automation_path_to_public_key`    | 既存の公開キーへのパス          | オプション    |
> | `automation_path_to_private_key`   | 既存の秘密キーへのパス         | オプション    |


## <a name="other-parameters"></a>その他のパラメーター


> [!div class="mx-tdCol2BreakAll "]
> | 変数                                       | 説明                           | Type        | 
> | ---------------------------------------------- | ------------------------------------- | ----------- | 
> | `resource_offset`                              | リソースの名前付けのオフセットを提供します。 複数のリソースを作成する場合の、リソースの名前付けのオフセット数値。 既定値は 0 です。これにより、disk0、disk1 などの名前付けパターンが作成されます。 オフセットを 1 に設定すると、disk1、disk2 などの名前付けパターンが作成されます。 | オプション    |
> | `disk_encryption_set_id`                       | お客様指定のキーを使用したマネージド ディスクの暗号化に使用されるディスク暗号化キー | オプション   |
> | `use_loadbalancers_for_standalone_deployments` | ロード バランサーをスタンドアロン インストール用にデプロイするかどうかを制御します | オプション |
> | `bom_name`                                     | 使用する SAP BOM の名前                                           | オプション |


## <a name="azure-netapp-support"></a>Azure NetApp のサポート


> [!div class="mx-tdCol2BreakAll "]
> | 変数                           | 説明                                                             | Type        |
> | ---------------------------------- | ----------------------------------------------------------------------- | ----------- |
> | `use_ANF`                          | 指定した場合、Azure NetApp Files のアカウントと容量プールをデプロイします  | オプション    |
> | `anf_sapmnt_volume_size`           | "sapmnt" ボリュームのサイズ (GB 単位) を定義します                        | オプション    |
> | `anf_transport_volume_size`        | "saptransport'" ボリュームのサイズ (GB 単位) を定義します                  | オプション    |


## <a name="high-availability-configuration"></a>高可用性構成

データベース層と SCS 層の高可用性構成は、`database_high_availability` および `scs_high_availability` フラグを使用して構成します。 

高可用性構成では、Azure フェンス エージェントで Pacemaker を使用します。 フェンス エージェントは、仮想マシンを停止および開始するためのアクセス許可を持つ一意のサービス プリンシパルを使用するように構成する必要があります。 詳細については、[フェンス エージェントの作成](high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device)に関する記事を参照してください

```azurecli-interactive
az ad sp create-for-rbac --role="Linux Fence Agent Role" --scopes="/subscriptions/<subscriptionID>" --name="<prefix>-Fencing-Agent"
```

`<prefix>` はお使いの環境の名前のプレフィックス (`DEV-WEEU-SAP01` など) に置き換え、`<subscriptionID>` はワークロード ゾーンのサブスクリプション ID に置き換えます。
  
> [!IMPORTANT]
> フェンス エージェント サービス プリンシパルの名前は、テナント内で一意であることが必要です。 スクリプトでは、ロール "Linux Fence Agent Role" が作成済みであることを想定しています
>
> フェンス エージェントの SPN の値を記録します。
   > - appId
   > - password
   > - tenant

フェンス エージェントの詳細は、事前に定義された名前付け規則を使用して、ワークロード ゾーンのキー コンテナーに格納する必要があります。 `<prefix>` はお使いの環境の名前のプレフィックス (`DEV-WEEU-SAP01` など) に置き換え、`<workload_kv_name>` はワークロード ゾーン リソース グループのキー コンテナーの名前に置き換えます。その他の値については、前の手順で記録した値を使用し、スクリプトを実行します。


```azurecli

    ```azurecli-interactive
    az keyvault secret set --name "<prefix>-fencing-spn-id" --vault-name "<workload_kv_name>" --value "<appId>";
    az keyvault secret set --name "<prefix>-fencing-spn-pwd" --vault-name "<workload_kv_name>" --value "<password>";
    az keyvault secret set --name "<prefix>-fencing-spn-tenant" --vault-name "<workload_kv_name>" --value "<tenant>";
    ```
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [SAP システムをデプロイする](automation-deploy-system.md)

