---
title: 自動化フレームワークの命名標準
description: Azure 上の SAP デプロイ自動化フレームワークの名前付け規則について説明します。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: reference
ms.service: virtual-machines-sap
ms.openlocfilehash: 628eb0dc4cef93b8782e38660709ee93230cc616
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725174"
---
# <a name="naming-conventions-for-sap-automation-framework"></a>SAP 自動化フレームワークの名前付け規則

[Azure 上の SAP デプロイ自動化フレームワーク](automation-deployment-framework.md)では、標準の名前付け規則が使用されています。 一貫性のある名前付けは、Terraform で自動化フレームワークを正しく実行するために役立ちます。 標準の名前付けは、自動化フレームワークをスムーズにデプロイするために役立ちます。 たとえば、一貫性のある名前付けを行うと次の操作を行う場合に役立ちます。

- サポートされている任意の Azure リージョンに SAP 仮想ネットワーク インフラストラクチャをデプロイする。

- パーティション分割された仮想ネットワークで複数のデプロイを実行する。 

- SAP システムを任意の SAP ワークロード ゾーンにデプロイする。 

- 通常のインスタンスおよび高可用性 (HA) インスタンスを実行する

- ディザスター リカバリーとフォール フォワードの動作を実行する。

デプロイを開始する前に、標準的な用語、区分パス、変数名を確認してください。 必要に応じて、[カスタムの名前付けを構成する](automation-naming-module.md)こともできます。

## <a name="placeholder-values"></a>プレースホルダーの値

名前付け規則の例では、次のプレースホルダー値が使用されています。

| プレースホルダー | 概念 | 文字数制限 | 例 | 
| ----------- | ------- | --------------- | ------- |
| `{ENVIRONMENT}` | 環境 | 5 | `DEV`, `PROTO`, `NP`, `PROD` |
| `{REGION_MAP}` | [リージョン](#azure-region-names) マップ | 4 | `westus` の `weus` |
| `{SAP_VNET}` | SAP 仮想ネットワーク (VNet) | 7 |  `SAP0` |
| `{SID}` | SAP システム ID | 3 | `X01` |
| `{PREFIX}` | SAP リソース プレフィックス | | `DEV-WEEU-SAP01-X01` |
| `{DEPLOY_VNET}` | 配置機能 VNet | 7 |  |
| `{REMOTE_VNET}` | リモート VNet | 7 |  |
| `{LOCAL_VNET}` |ローカル VNet | 7 |  |
| `{CODENAME}` | バージョンの論理名 |  | `version1`, `beta` |
| `{VM_NAME}` | VM 名 |  |  |
| `{SUBNET}` | Subnet |  |  |
| `{DBSID}` | データベース システム ID |  |  |
| `{DIAG}` | | 5 |  |
| `{RND}` | | 3 |  |
| `{USER}` | | 12 |  |
| `{COMPUTER_NAME}` | | 14 |  |

### <a name="deployer-names"></a>配置機能名

**形式** 列の説明については、[プレースホルダー値の定義](#placeholder-values)を参照してください。

| 概念 | 文字数制限 | Format | 例 |
| ------- | --------------- | ------ | ------- |
| リソース グループ | 80 | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}-INFRASTRUCTURE` |  `MGMT-WEEU-DEP00-INFRASTRUCTURE` |
| 仮想ネットワーク | 38 (64)  | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}-vnet` | `MGMT-WEEU-DEP00-vnet` |
| Subnet | 80 | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_deployment-subnet` | `MGMT-WEEU-DEP00_deployment-subnet` |
| ストレージ アカウント | 24 | `{ENVIRONMENT}{REGION_MAP}{SAP_VNET}{DIAG}{RND}` | `mgmtweeudep00diagxxx` |
| ネットワーク セキュリティ グループ (NSG) | 80 | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_deployment-nsg` | `MGMT-WEEU-DEP00_deployment-nsg` |
| ルート テーブル | | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_routeTable` | `MGMT-WEEU-DEP00_route-table` |
| ネットワーク インターフェイスのコンポーネント | 80 | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_{COMPUTER_NAME}-nic` | `-ipconfig1` (`ip_configuration` ブロックには必要ありません。) |
| ディスク | |`{vm.name}-deploy00` | `PROTO-WUS2-DEPLOY_deploy00-disk00` |
| VM | | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_deploy##` | `MGMT-WEEU-DEP00_permweeudep00deploy00` |
| オペレーティング システム (OS) ディスク | | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_deploy##-OsDisk` | `PERM-WEEU-DEP00_permweeudep00deploy00-OsDisk` |
| コンピューター名 | | `{environment[_map]}{DEPLOY_VNET}{region_map}deploy##` | `MGMT-WEEU-DEP00_permweeudep00deploy00` |
| Key Vault | 24 | `{ENVIRONMENT}{REGION_MAP}{DEPLOY_VNET}{USER}{RND}` (デプロイ資格情報) | `MGMTWEEUDEP00userxxx` |
| パブリック IP アドレス | | `{ENVIRONMENT}-{REGION_MAP}-{DEPLOY_VNET}_{COMPUTER_NAME}-pip` | `MGMT-WEEU-DEP00_permweeudep00deploy00-pip` |

### <a name="sap-library-names"></a>SAP ライブラリ名

**形式** 列の説明については、[プレースホルダー値の定義](#placeholder-values)を参照してください。

| 概念 | 文字数制限 | Format | 例 |
| ------- | --------------- | ------ | ------- |
| Resource group | 80 | `{ENVIRONMENT}-{REGION_MAP}-SAP_LIBRARY` | `MGMT-WEEU-SAP_LIBRARY` |
| ストレージ アカウント | 24 | `{ENVIRONMENT}{REGION_MAP}saplib(12CHAR){RND}` | `mgmtweeusaplibxxx` |
| ストレージ アカウント | 24 | `{ENVIRONMENT}{REGION_MAP}tfstate(12CHAR){RND}` | `mgmtweeutfstatexxx` |

### <a name="sap-workload-zone-names"></a>SAP ワークロード ゾーン名

**形式** 列の説明については、[プレースホルダー値の定義](#placeholder-values)を参照してください。

| 概念         | 文字数制限 | Format | 例 |
| --------------- | --------------- | ------ | ------- |
| Resource group  | 80              | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}-INFRASTRUCTURE` | `DEV-WEEU-SAP01-INFRASTRUCTURE` |
| 仮想ネットワーク | 38 (64)         | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}-vnet` | `DEV-WEEU-SAP01-vnet` |
| ピアリング         | 80              | `{LOCAL_VNET}_to_{REMOTE_VNET}` | `DEV-WEEU-SAP01-vnet_to_MGMT-WEEU-DEP00-vnet` |
| Subnet          | 80              | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_utility-subnet` | `DEV-WEEU-SAP01_db-subnet` |
| ネットワーク セキュリティ グループ | 80 | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_utility-nsg` | `DEV-WEEU-SAP01_dbSubnet-nsg` |
| ルート テーブル | | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_routeTable` | `DEV-WEEU-SAP01_route-table` |
| ストレージ アカウント | 80              | `{ENVIRONMENT}{REGION_MAP}{SAP_VNET}diag(5CHAR){RND}` | `devweeusap01diagxxx` |
| ユーザー定義のルート | | `{remote_vnet}_Hub-udr` | |
| ユーザー定義のルート (ファイアウォール)| | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_firewall-route` |`DEV-WEEU-SAP01_firewall-route` |
| 可用性セット (AV セット) | | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_iscsi-avset` | |
| ネットワーク インターフェイスのコンポーネント | 80 | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_iscsi##-nic` | |
| ディスク | | `{vm.name}-iscsi00` または `${azurerm_virtual_machine.iscsi.*.name}-iscsi00` (コード) | `DEV-WEEU-SAP01_iscsi00-iscsi00` |
| VM | | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_iscsi##` | |
| OS ディスク | `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}_iscsi##-OsDisk` | |
| コンピューター名 | | `{ENVIRONMENT}_{REGION_MAP}{SAP_VNET}{region_map}iscsi##` | |
| Key Vault | 24 | `{ENVIRONMENT}{REGION_MAP}{SAP_VNET}{USER}{RND}` | `DEVWEEUSAP01userxxx` |
| NetApp アカウント |  | `{ENVIRONMENT}{REGION_MAP}{SAP_VNET}_netapp_account` | `DEV-WEEU-SAP01_netapp_account` |
| NetApp 容量プール | 24 | `{ENVIRONMENT}{REGION_MAP}{SAP_VNET}_netapp_pool` | `DEV-WEEU-SAP01_netapp_pool` |

### <a name="sap-system-names"></a>SAP システム名

**形式** 列の説明については、[プレースホルダー値の定義](#placeholder-values)を参照してください。

| 概念         | 文字数制限 | Format | 例 |
| --------------- | --------------- | ------ | ------- |
| Resource prefix (リソース プレフィックス) | 80              | `{ENVIRONMENT}-{REGION_MAP}-{SAP-VNET}-{SID}` または `{ENVIRONMENT}-{REGION_MAP}-{SAP-VNET}_{CODENAME}-{SID}` | `DEV-WEEU-SAP01-X01` |
| Resource group  | 80              | `{PREFIX}` | `DEV-WEEU-SAP01-X01` |
| Azure 近接配置グループ (PPG) | | `{PREFIX}_ppg` | |
| 可用性セット | | `{PREFIX}_app-avset` | `DEV-WEEU-SAP01-X01_app-avset` |
| Subnet          | 80              | `{PREFIX}_utility-subnet` | `DEV-WEEU-SAP01_X01_db-subnet` |
| ネットワーク セキュリティ グループ | 80 | `{PREFIX}_utility-nsg` | `DEV-WEEU-SAP01_X01_dbSubnet-nsg` |
| ネットワーク インターフェイスのコンポーネント | | `{PREFIX}_{VM_NAME}-{SUBNET}-nic` | `-app-nic`, `-web-nic`, `-admin-nic`, `-db-nic` |
| コンピューター名 (データベース) | 14 | `{SID}d{DBSID}##{OS flag l/w}{primary/secondary 0/1}{RND}` | `DEV-WEEU-SAP01-X01_x01dxdb00l0xxx` |
| コンピューター名 (データベース以外) | 14 | `{SID}{ROLE}##{OS flag l/w}{RND}` | `DEV-WEEU-SAP01-X01_x01app01l538`, `DEV-WEEU-SAP01-X01_x01scs01l538` |
| VM | | `{PREFIX}_{COMPUTER-NAME}` | |
| ディスク | | `{PREFIX}_{VM_NAME}-{disk_type}{counter}` | `{VM-NAME}-sap00`, `{VM-NAME}-data00`, `{VM-NAME}-log00`, `{VM-NAME}-backup00` |
| OS ディスク | | `{PREFIX}_{VM_NAME}-osDisk` | `DEV-WEEU-SAP01-X01_x01scs00lxxx-OsDisk` |
| Azure Load Balancer (ユーティリティ)| 80 | `{PREFIX}_db-alb` | `DEV-WEEU-SAP01-X01_db-alb` |
| ロード バランサーのフロントエンド IP アドレス (ユーティリティ)| | `{PREFIX}_dbAlb-feip` | `DEV-WEEU-SAP01-X01_dbAlb-feip` |
| ロード バランサーのバックエンド プール (ユーティリティ)| | `{PREFIX}_dbAlb-bePool` | `DEV-WEEU-SAP01-X01_dbAlb-bePool` |
| ロード バランサーの正常性プローブ (ユーティリティ)| | `{PREFIX}_dbAlb-hp` | `DEV-WEEU-SAP01-X01_dbAlb-hp`|
| キー コンテナー (ユーザー) | 24 | `{SHORTPREFIX}u{RND}` | `DEVWEEUSAP01uX01xxx` |
| NetApp ボリューム (ユーティリティ) | 24 | `{PREFIX}-utility` | `DEV-WEEU-SAP01-X01_sapmnt` |



> [!NOTE]
> ディスクの番号付けは 0 から始まります。 名前付け規則では、2 文字の形式を使用します (`00` など)。

## <a name="azure-region-names"></a>Azure リージョン名

自動化フレームワークでは、短い形式の Azure リージョン名が使用されます。 短い Azure リージョン名は、通常のリージョン名にマップされています。

名前ジェネレーターの構成ファイル `../../../deploy/terraform/terraform-units/modules/sap_namegenerator/variables_local.tf` の `_region_mapping` 変数の下にマッピングを設定できます。

その後、任意の場所 (区分パスなど) で `_region_mapping` 変数を使用できます。 区分パスの形式は `{ENVIRONMENT}-{REGION_MAP}-{SAP_VNET}-{ARTIFACT}` です。ここで、

- `{ENVIRONMENT}` は、環境またはワークロード ゾーンの名前です。
- `{REGION_MAP}` は、Azure リージョン名の短い形式です。
- `{SAP_VNET}` は、環境内の SAP 仮想ネットワークです。
- `{ARTIFACT}` は、仮想ネットワーク内のデプロイ成果物です (`INFRASTRUCTURE` など)。

`_region_mapping` 変数は次のように使用できます。

```
"${upper(var.__environment)}-${upper(element(split(",", lookup(var.__region_mapping, var.__region, "-,unknown")),1))}-${upper(var.__SAP_VNET)}-INFRASTRUCTURE"
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム名前付けモジュールの構成について学習する](automation-naming-module.md)
