---
title: オートメーション フレームワーク用の SAP システムのデプロイについて
description: Azure 上の SAP デプロイ自動化フレームワーク内の SAP システム デプロイ プロセスの概要。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: a1f79dcd7f68f426a9c9d515d96048c7e7bc0123
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725223"
---
# <a name="sap-system-deployment-for-the-automation-framework"></a>オートメーション フレームワーク用の SAP システムのデプロイ

[SAP システム](automation-deployment-framework.md#sap-concepts)の作成は、[Azure 上の SAP デプロイ オートメーション フレームワーク](automation-deployment-framework.md)のプロセスの一部です。 SAP システムによって、[SAP アプリケーション](automation-deployment-framework.md#sap-concepts)用の仮想マシン (VM) とサポート コンポーネントが作成されます。 

SAP システムによって、次のデプロイが行われます。

- [データベース層](#database-tier)。データベース VM、そのディスク、Standard Azure Load Balancer がデプロイされます。 この層で [HANA データベース](automation-configure-extra-disks.md#hana-databases)または [AnyDB データベース](automation-configure-extra-disks.md#anydb-databases)を実行できます。
- [SAP セントラル サービス層](#central-services-tier)。顧客が定義した数の VM と Azure Standard Load Balancer がデプロイされます。
- [アプリケーション層](#application-tier)。 VM とそのディスクがデプロイされます。
- [Web ディスパッチャー層](#web-dispatcher-tier)

## <a name="application-tier"></a>アプリケーション層

アプリケーション層では、顧客が定義した数の VM がデプロイされます。 これらの VM は、サイズが **Standard_D4s_v3** で、30 GB のオペレーティング システム (OS) ディスクと 512 GB のデータ ディスクを搭載しています。

アプリケーション サーバーの数を設定するには、パラメーター ファイルでこの層のパラメーター `application_server_count` を定義します。 たとえば、`application_server_count= 3` のようにします。


## <a name="central-services-tier"></a>セントラル サービス層

SAP セントラル サービス (SCS) 層では、顧客が定義した数の VM がデプロイされます。 これらの VM は、サイズが **Standard_D4s_v3** で、30 GB の OS ディスクと 512 GB のデータ ディスクを搭載しています。 この層では、[Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md) もデプロイされます。

SCS サーバーの数を設定するには、パラメーター ファイルでこの層のパラメーター `scs_server_count` を定義します。 たとえば、`scs_server_count=1` のようにします。


## <a name="web-dispatcher-tier"></a>Web ディスパッチャー層

Web ディスパッチャー層では、顧客が定義した数の VM がデプロイされます。  この層では、[Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md) もデプロイされます。

Web サーバーの数を設定するには、パラメーター ファイルでこの層のパラメーター `web_server_count` を定義します。 たとえば、`web_server_count = 2` のようにします。

## <a name="database-tier"></a>データベース層

データベース層では、VM、そのディスク、そして [Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md) がデプロイされます。 データベース VM には、[HANA データベース](automation-configure-extra-disks.md#hana-databases)または[AnyDB データベース](automation-configure-extra-disks.md#anydb-databases)を使用することができます。

この層のパラメーター `size` を使用して、データベース VM のサイズを設定できます。 例えば、HANA データベースの場合は`"size": "S4Demo"`、AnyDB データベースの場合は、`"size": "1 TB"` です。 使用可能な値については、[HANA データベース VM オプション](automation-configure-extra-disks.md#hana-databases)および [AnyDB データベース VM オプション](automation-configure-extra-disks.md#anydb-databases)の表の **サイズ** パラメーターを参照してください。

既定では、オートメーション フレームワークによって HANA データベースのデプロイに適切なディスク構成がデプロイされます。 HANA データベースのデプロイの場合、フレームワークは VM サイズに基づいて既定のディスク構成を計算します。 ただし、AnyDB データベースのデプロイの場合、フレームワークはデータベース サイズに基づいて既定のディスク構成を計算します。 デプロイでカスタム JSON ファイルを作成することで、必要に応じてディスク サイズを設定できます。 例については、[次の JSON コード サンプルを参照し、ご使用の構成の必要に応じて値を置き換えます](automation-configure-extra-disks.md#custom-sizing-file)。 次に、データベース層のパラメーター ファイルに `db_disk_sizes_filename` パラメーターを定義します。 たとえば、`db_disk_sizes_filename = "path/to/JSON/file"` のようにします。

また、[新しいシステムにディスクを追加](automation-configure-extra-disks.md#custom-sizing-file)したり、[既存のシステムにディスクを追加](automation-configure-extra-disks.md#add-extra-disks-to-existing-system)したりすることもできます。

## <a name="core-configuration"></a>コア構成

次のパラメーター ファイルの例では、必須のパラメーターのみを示します。

```bash
# The environment value is a mandatory field, it is used for partitioning the environments, for example (PROD and NP)
environment="DEV"

# The location value is a mandatory field, it is used to control where the resources are deployed
location="westeurope"

# The network logical name is mandatory - it is used in the naming convention and should map to the workload virtual network logical name 
network_name="SAP01"

# sid is a mandatory field that defines the SAP Application SID
sid="RH7"

app_tier_vm_sizing="Production"
app_tier_use_DHCP=true

database_platform="HANA"

database_size="S4Demo"
database_sid="XDB"

database_vm_use_DHCP=true

database_vm_image={
  os_type="linux"
  source_image_id=""
  publisher="SUSE"
  offer="sles-sap-15-sp2"
  sku="gen2"
  version="latest"
}

# application_server_count defines how many application servers to deploy
application_server_count=2

application_server_image= {
  os_type=""
  source_image_id=""
  publisher="SUSE"
  offer="sles-sap-15-sp2"
  sku="gen2"
  version="latest"
}

scs_server_count=1

# scs_instance_number
scs_instance_number="01"

# ers_instance_number
ers_instance_number="02"

# webdispatcher_server_count defines how many web dispatchers to deploy
webdispatcher_server_count=0


```

## <a name="deploying-the-sap-system"></a>SAP システムのデプロイ
   
サンプルの SAP システム構成ファイル `DEV-WEEU-SAP01-X01.tfvars` は、`~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-WEEU-SAP01-X01` フォルダーにあります。

次のコマンドを実行すると、SAP システムがデプロイされます。

# <a name="linux"></a>[Linux](#tab/linux)

> [!TIP]
> Deployer からこのタスクを実行します。

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/SYSTEM/DEV-WEEU-SAP01-X01

${DEPLOYMENT_REPO_PATH}/deploy/scripts/installer.sh          \
        --parameterfile DEV-WEEU-SAP01-X01.tfvars           \
        --type sap_system
```
# <a name="windows"></a>[Windows](#tab/windows)

```powershell

cd C:\Azure_SAP_Automated_Deployment\WORKSPACES\SYSTEM\DEV-WEEU-SAP01-X01

New-SAPSystem -Parameterfile DEV-WEEU-SAP01-X01.tfvars 
-Type sap_system
```

---


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [オートメーション フレームワークを使用したワークロード ゾーンのデプロイについて](automation-software.md)
