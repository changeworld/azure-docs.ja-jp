---
title: Azure Bastion の構成設定について
description: Azure Bastion の使用可能な構成設定について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: 71a86e1440d96a37fd72afc67aaaaa7d122c6d4f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426679"
---
# <a name="about-bastion-configuration-settings"></a>Bastion の構成設定について

この記事のセクションでは、Azure Bastion のリソースと設定について説明します。

## <a name="skus"></a><a name="skus"></a>SKU

SKU はサービス レベルとも呼ばれます。 Azure Bastion では、Basic と Standard の 2 種類の SKU がサポートされています。 SKU は、Bastion を構成するときのワークフロー中に Azure portal 内で構成されます。 [Basic SKU は Standard SKU にアップグレード](#upgradesku)できます。

* **Basic SKU** は基本機能を備えています。この機能により、Azure Bastion は、ターゲット アプリケーションの VM 上でパブリック IP アドレスを公開することなく、Virtual Machines (VM) への RDP/SSH 接続を管理できます。
* Standard SKU を使用すると、Premium の機能により、Azure Bastion によるさらに大規模なリモート接続が可能になります。

次の表に、機能と対応する SKU を示します。 

[!INCLUDE [Azure Bastion SKUs](../../includes/bastion-sku.md)]

### <a name="configuration-methods"></a>構成方法

現時点では、Standard SKU を指定する場合は、Azure portal を使用する必要があります。 Azure CLI または Azure PowerShell を使用して Bastion を構成する場合、SKU を指定することはできません。既定値は Basic SKU です。

| 方法 | 値 | リンク |
| --- | --- | --- |
| Azure portal | サービス レベル: Basic <br>標準 | [クイックスタート: VM 設定から Bastion を構成する](quickstart-host-portal.md)<br>[チュートリアル - Bastion を構成する](tutorial-create-host-portal.md) |
| Azure PowerShell | Basic のみ - 設定なし |[Bastion を構成する - PowerShell](bastion-create-host-powershell.md) |
| Azure CLI |  Basic のみ - 設定なし | [Bastion を構成する - CLI](create-host-cli.md) |

### <a name="upgrade-a-sku"></a><a name="upgradesku"></a>SKU のアップグレード

Azure Bastion では、Basic SKU から Standard SKU へのアップグレードがサポートされています。

> [!NOTE]
> Standard SKU から Basic SKU へのダウングレードはサポートされていません。 ダウングレードするには、Azure Bastion を削除して再作成する必要があります。
>

#### <a name="configuration-methods"></a>構成方法

この設定を構成するには、次の方法を使用します。

| 方法 | 値 | リンク |
| --- | --- | --- |
| Azure portal |階層  | [SKU のアップグレード](upgrade-sku.md)|

## <a name="instances-and-host-scaling"></a><a name="instance"></a>インスタンスとホストのスケーリング

インスタンスとは、Azure Bastion を構成するときに作成される、最適化された Azure VM のことです。 これは Azure によって完全に管理され、Azure Bastion に必要なすべてのプロセスを実行します。 インスタンスは、スケール ユニットとも呼ばれます。 クライアント VM には、Azure Bastion インスタンス経由で接続します。 Basic SKU を使用して Azure Bastion を構成すると、2 つのインスタンスが作成されます。 Standard SKU を使用する場合は、インスタンスの数を指定できます。 これは **ホストのスケーリング** と呼ばれます。 

インスタンスごとに 10 から 12 の同時 RDP/SSH 接続をサポートできます。 インスタンスあたりの接続数は、クライアント VM に接続するときに実行するアクションによって異なります。 たとえば、データ処理の多い作業を行っている場合は、インスタンスの処理の負荷が大きくなります。 同時接続セッション数を超えると、追加のスケール ユニット (インスタンス) が必要になります。 

インスタンスは AzureBastionSubnet 内に作成されます。 ホストのスケーリングの場合、AzureBastionSubnet は /26 以上である必要があります。 サブネットを小さくすると、作成できるインスタンスの数が制限されます。 AzureBastionSubnet の詳細については、この記事の[サブネット](#subnet)に関するセクションをご覧ください。

### <a name="configuration-methods"></a>構成方法

この設定を構成するには、次の方法を使用します。

| 方法 | 値 | リンク |
| --- | --- | --- |
| Azure portal |インスタンス数  | [ホストのスケーリングの構成](configure-host-scaling.md)|


## <a name="azure-bastion-subnet"></a><a name="subnet"></a>Azure Bastion サブネット

Azure Bastion では、専用サブネット **AzureBastionSubnet** が必要です。 このサブネットは、Azure Bastion がデプロイされている Virtual Network に作成する必要があります。 サブネットには次の構成が必要です。

* サブネットの名前は *AzureBastionSubnet* にしてください。
* サブネットのサイズは /26 以上 (/25、/24 など) にしてください。
* ホストのスケーリングの場合は、/26 以上のサブネットをお勧めします。 サブネット領域を小さくすると、スケール ユニットの数が制限されます。 詳細については、この記事の[ホストのスケーリング](#instance)に関するセクションをご覧ください。
* サブネットは、bastion ホストと同じ VNet およびリソース グループに存在する必要があります。
* サブネットに追加のリソースを含めることはできません。

### <a name="configuration-methods"></a>構成方法

この設定を構成するには、次の方法を使用します。

| 方法 | 値 | リンク |
| --- | --- |--- |
| Azure portal | Subnet  |[クイックスタート: VM 設定から Bastion を構成する](quickstart-host-portal.md)<br>[チュートリアル - Bastion を構成する](tutorial-create-host-portal.md)|
| Azure PowerShell | -subnetName|[cmdlet](/powershell/module/az.network/new-azbastion#parameters) |
| Azure CLI |  --subnet-name | [command](/cli/azure/network/vnet#az_network_vnet_create) |

## <a name="public-ip-address"></a><a name="public-ip"></a>パブリック IP アドレス

Azure Bastion にはパブリック IP アドレスが必要です。 パブリック IP には次の構成が必要です。

* パブリック IP アドレスの SKU は、**Standard** にしてください。
* パブリック IP アドレスの割り当て方法は、**静的** にしてください。
* パブリック IP アドレスの名前は、このパブリック IP アドレスを参照するためのリソース名です。
* 作成済みのパブリック IP アドレスは、そのアドレスが Azure Bastion で求められる条件を満たし、まだ使用されていないものであれば、使用することができます。

### <a name="configuration-methods"></a>構成方法

この設定を構成するには、次の方法を使用します。

| 方法 | 値 | リンク |
| --- | --- |--- |
| Azure portal | パブリック IP アドレス |[Azure portal](https://portal.azure.com)|
| Azure PowerShell | -PublicIpAddress| [cmdlet](/powershell/module/az.network/new-azbastion#parameters)  |
| Azure CLI | --public-ip create |[command](/cli/azure/network/public-ip)
|

## <a name="next-steps"></a>次のステップ

よくあるご質問については、「[Azure Bastion に関する FAQ](bastion-faq.md)」を参照してください。
