---
title: オートメーション フレームワークのカスタム名前付けモジュールを構成する
description: Azure 上の SAP デプロイ自動化フレームワークのカスタム名前付け規則を実装する方法について説明します。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 8e055971ea587e81ded1cb6317579a6a9bdcc8cb
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725214"
---
# <a name="configure-custom-naming-module"></a>カスタム名前付けモジュールを構成する

[Azure 上の SAP デプロイ自動化フレームワーク](automation-deployment-framework.md)では、Azure [リソース名前付け](automation-naming.md)の標準の名前付け規則が使用されています。 

Terraform モジュール `sap_namegenerator` は、オートメーション フレームワークがデプロイするすべてのリソースの名前を定義します。 このモジュールはリポジトリ内の `/deploy/terraform/terraform-units/modules/sap_namegenerator/` にあります。 

フレームワークでは、[パラメーター ファイル](automation-configure-system.md)を使用して、一部のリソースに独自の名前を指定することもサポートしています。 これらの機能では十分でない場合は、オートメーションによって使用される名前付けモジュールを変更することで、カスタム名前付けロジックを使用することもできます。

モジュール内には、リソースの名前付け用に複数のファイルがあります。

- 仮想マシン (VM) とコンピューター名は (`vm.tf`) で定義されています。
- リソース グループの名前付けは (`resourcegroup.tf`) で定義されています。
- (`keyvault.tf`) のキー コンテナー
- リソース サフィックス (`variables_local.tf`)

さまざまなリソース名は、Terraform コードのプレフィックスによって識別されます。
- SAP Deployer のデプロイでは、プレフィックス `deployer_` が付いたリソース名が使用されます。
- SAP ライブラリのデプロイでは、プレフィックス `library` が付いたリソース名が使用されます。
- SAP ランドスケープのデプロイでは、プレフィックス `vnet_` が付いたリソース名が使用されます。
- SAP システムのデプロイでは、プレフィックス `sdu_` が付いたリソース名が使用されます。

計算された名前は、すべての terraform モジュールで使用されるデータ辞書に返されます。 

## <a name="using-custom-names"></a>カスタム名の使用

一部のリソース名は、tfvars パラメーター ファイルにパラメーターを指定することで変更できます。

| リソース               | パラメーター               | Notes                                                              |
| ---------------------- | ----------------------- | ------------------------------------------------------------------ |
| `Prefix`               | `custom_prefix`         | これは、リソース グループ内のすべてのリソースのプレフィックスとして使用されます |
| `Resource group`       | `resourcegroup_name`    |                                                                    |
| `admin subnet name`    | `admin_subnet_name`     |                                                                    |
| `admin nsg name`       | `admin_subnet_nsg_name` |                                                                    |
| `db subnet name`       | `db_subnet_name`        |                                                                    |
| `db nsg name`          | `db_subnet_nsg_name`    |                                                                    |
| `app subnet name`      | `app_subnet_name`       |                                                                    |
| `app nsg name`         | `app_subnet_nsg_name`   |                                                                    |
| `web subnet name`      | `web_subnet_name`       |                                                                    |
| `web nsg name`         | `web_subnet_nsg_name`   |                                                                    |
| `admin nsg name`       | `admin_subnet_nsg_name` |                                                                    |

## <a name="changing-the-naming-module"></a>名前付けモジュールの変更

カスタム名前付け用に Terraform 環境を準備するには、まずカスタム名前付けモジュールを作成する必要があります。 最も簡単な方法は、既存のモジュールをコピーし、コピーしたモジュールで必要な変更を行う方法です。

1. Terraform 環境にルート レベルのフォルダーを作成します。 たとえば、`Azure_SAP_Automated_Deployment` のようにします。
1. 新しいルート レベル フォルダーに移動します。
1. [オートメーション フレームワーク リポジトリ](https://github.com/Azure/sap-hana)を複製します。 この手順では、新しいフォルダー `sap-hana` を作成します。
1. `Contoso_naming` というルート レベルのフォルダー内にフォルダーを作成します。
1. `sap-hana` フォルダーに移動します
1. git の適切なブランチを確認してください。
1. `sap-hana` フォルダー内の `\deploy\terraform\terraform-units\modules` フォルダーに移動します。
1. `sap_namegenerator` フォルダーを `Contoso_naming` フォルダーにコピーします。

名前付けモジュールは、ルート terraform フォルダーから呼び出されます。

```terraform
module "sap_namegenerator" {
  source           = "../../terraform-units/modules/sap_namegenerator"
  environment      = local.infrastructure.environment
  location         = local.infrastructure.region
  codename         = lower(try(local.infrastructure.codename, ""))
  random_id        = module.common_infrastructure.random_id
  sap_vnet_name    = local.vnet_logical_name
  sap_sid          = local.sap_sid
  db_sid           = local.db_sid
  app_ostype       = try(local.application.os.os_type, "LINUX")
  anchor_ostype    = upper(try(local.anchor_vms.os.os_type, "LINUX"))
  db_ostype        = try(local.databases[0].os.os_type, "LINUX")
  db_server_count  = var.database_server_count
  app_server_count = try(local.application.application_server_count, 0)
  web_server_count = try(local.application.webdispatcher_count, 0)
  scs_server_count = local.application.scs_high_availability ? 2 * local.application.scs_server_count : local.application.scs_server_count  app_zones        = local.app_zones
  scs_zones        = local.scs_zones
  web_zones        = local.web_zones
  db_zones         = local.db_zones
  resource_offset  = try(var.options.resource_offset, 0)
  custom_prefix    = var.custom_prefix
}
```

次に、他の Terraform モジュール ファイルをカスタム名前付けモジュールにポイントする必要があります。 これらのモジュール ファイルには次のものが含まれます。

- `deploy\terraform\run\sap_system\module.tf`
- `deploy\terraform\bootstrap\sap_deployer\module.tf`
- `deploy\terraform\bootstrap\sap_library\module.tf`
- `deploy\terraform\run\sap_library\module.tf`
- `deploy\terraform\run\sap_deployer\module.tf`

ファイルごとに、モジュール `sap_namegenerator` のソースを変更して新しい名前付けモジュールの場所を指すようにします。 たとえば、`module "sap_namegenerator" { source = "../../terraform-units/modules/sap_namegenerator"` は `module "sap_namegenerator" { source = "../../../../Contoso_naming"` になります。

## <a name="change-resource-group-naming-logic"></a>リソース グループの名前付けロジックを変更する

リソース グループの名前付けロジックを変更するには、カスタム名前付けモジュール フォルダー (例: `Workspaces\Contoso_naming`) に移動します。 次に、ファイル `resourcegroup.tf` を編集します。 独自の名前付けロジックを使用して、次のコードを変更します。

```json
locals {

  // Resource group naming
  sdu_name = length(var.codename) > 0 ? (
    upper(format("%s-%s-%s_%s-%s", local.env_verified, local.location_short, local.sap_vnet_verified, var.codename, var.sap_sid))) : (
    upper(format("%s-%s-%s-%s", local.env_verified, local.location_short, local.sap_vnet_verified, var.sap_sid))
  )

  deployer_name  = upper(format("%s-%s-%s", local.deployer_env_verified, local.deployer_location_short, local.dep_vnet_verified))
  landscape_name = upper(format("%s-%s-%s", local.landscape_env_verified, local.location_short, local.sap_vnet_verified))
  library_name   = upper(format("%s-%s", local.library_env_verified, local.location_short))

  // Storage account names must be between 3 and 24 characters in length and use numbers and lower-case letters only. The name must be unique.
  deployer_storageaccount_name       = substr(replace(lower(format("%s%s%sdiag%s", local.deployer_env_verified, local.deployer_location_short, local.dep_vnet_verified, local.random_id_verified)), "/[^a-z0-9]/", ""), 0, var.azlimits.stgaccnt)
  landscape_storageaccount_name      = substr(replace(lower(format("%s%s%sdiag%s", local.landscape_env_verified, local.location_short, local.sap_vnet_verified, local.random_id_verified)), "/[^a-z0-9]/", ""), 0, var.azlimits.stgaccnt)
  library_storageaccount_name        = substr(replace(lower(format("%s%ssaplib%s", local.library_env_verified, local.location_short, local.random_id_verified)), "/[^a-z0-9]/", ""), 0, var.azlimits.stgaccnt)
  sdu_storageaccount_name            = substr(replace(lower(format("%s%s%sdiag%s", local.env_verified, local.location_short, local.sap_vnet_verified, local.random_id_verified)), "/[^a-z0-9]/", ""), 0, var.azlimits.stgaccnt)
  terraformstate_storageaccount_name = substr(replace(lower(format("%s%stfstate%s", local.library_env_verified, local.location_short, local.random_id_verified)), "/[^a-z0-9]/", ""), 0, var.azlimits.stgaccnt)

}
```

## <a name="change-resource-suffixes"></a>リソース サフィックスを変更する

リソース サフィックスを変更するには、カスタム名前付けモジュール フォルダー (例: `Workspaces\Contoso_naming`) に移動します。 次に、ファイル `variables_local.tf` を編集します。 独自のリソース サフィックスを使用して、次のマップを変更します。

> [!NOTE]
> マップの **値** を変更するだけです。 マップ **キー** は変更しないでください。Terraform のコードによって使用されます。
> たとえば、管理者ネットワーク インターフェイス コンポーネントの名前を変更する場合は、`"admin-nic"           = "-admin-nic"` を `"admin-nic"           = "yourNICname"` に変更します。

```json
variable resource_suffixes {
  type        = map(string)
  description = "Extension of resource name"

  default = {
    "admin_nic"           = "-admin-nic"
    "admin_subnet"        = "admin-subnet"
    "admin_subnet_nsg"    = "adminSubnet-nsg"
    "app_alb"             = "app-alb"
    "app_avset"           = "app-avset"
    "app_subnet"          = "app-subnet"
    "app_subnet_nsg"      = "appSubnet-nsg"
    "db_alb"              = "db-alb"
    "db_alb_bepool"       = "dbAlb-bePool"
    "db_alb_feip"         = "dbAlb-feip"
    "db_alb_hp"           = "dbAlb-hp"
    "db_alb_rule"         = "dbAlb-rule_"
    "db_avset"            = "db-avset"
    "db_nic"              = "-db-nic"
    "db_subnet"           = "db-subnet"
    "db_subnet_nsg"       = "dbSubnet-nsg"
    "deployer_rg"         = "-INFRASTRUCTURE"
    "deployer_state"      = "_DEPLOYER.terraform.tfstate"
    "deployer_subnet"     = "_deployment-subnet"
    "deployer_subnet_nsg" = "_deployment-nsg"
    "iscsi_subnet"        = "iscsi-subnet"
    "iscsi_subnet_nsg"    = "iscsiSubnet-nsg"
    "library_rg"          = "-SAP_LIBRARY"
    "library_state"       = "_SAP-LIBRARY.terraform.tfstate"
    "kv"                  = ""
    "msi"                 = "-msi"
    "nic"                 = "-nic"
    "osdisk"              = "-OsDisk"
    "pip"                 = "-pip"
    "ppg"                 = "-ppg"
    "sapbits"             = "sapbits"
    "storage_nic"         = "-storage-nic"
    "storage_subnet"      = "_storage-subnet"
    "storage_subnet_nsg"  = "_storageSubnet-nsg"
    "scs_alb"             = "scs-alb"
    "scs_alb_bepool"      = "scsAlb-bePool"
    "scs_alb_feip"        = "scsAlb-feip"
    "scs_alb_hp"          = "scsAlb-hp"
    "scs_alb_rule"        = "scsAlb-rule_"
    "scs_avset"           = "scs-avset"
    "scs_ers_feip"        = "scsErs-feip"
    "scs_ers_hp"          = "scsErs-hp"
    "scs_ers_rule"        = "scsErs-rule_"
    "scs_scs_rule"        = "scsScs-rule_"
    "sdu_rg"              = ""
    "tfstate"             = "tfstate"
    "vm"                  = ""
    "vnet"                = "-vnet"
    "vnet_rg"             = "-INFRASTRUCTURE"
    "web_alb"             = "web-alb"
    "web_alb_bepool"      = "webAlb-bePool"
    "web_alb_feip"        = "webAlb-feip"
    "web_alb_hp"          = "webAlb-hp"
    "web_alb_inrule"      = "webAlb-inRule"
    "web_avset"           = "web-avset"
    "web_subnet"          = "web-subnet"
    "web_subnet_nsg"      = "webSubnet-nsg"

  }
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [名前付け規則について確認してください](automation-naming.md)
