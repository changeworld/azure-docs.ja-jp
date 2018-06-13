---
title: Azure Network Watcher のインスタンスの作成 | Microsoft Docs
description: Azure リージョンで Network Watcher を有効にする方法について説明します。
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 9d3e579cd58bc6c7d67b29998ea5a48a65548b0a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
ms.locfileid: "30904002"
---
# <a name="create-an-azure-network-watcher-instance"></a>Azure Network Watcher のインスタンスの作成

Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 シナリオ レベルの監視により、エンド ツー エンドのネットワーク レベル ビューで問題を診断できるようになります。 Network Watcher に搭載されているネットワークの診断および監視ツールを使用して、Azure 内のネットワークを把握および診断し、洞察を得ることができます。

## <a name="create-a-network-watcher-in-the-portal"></a>ポータルで Network Watcher を作成する

**[すべてのサービス]** > **[ネットワーク]** > **[Network Watcher]** の順に移動します。 Network Watcher を有効にするすべてのサブスクリプションを選択できます。 このアクションにより、利用可能なすべてのリージョンに Network Watcher が作成されます。

![Network Watcher の作成](./media/network-watcher-create/figure1.png)

ポータルを使用して Network Watcher を有効にすると、Network Watcher インスタンスの名前が自動的に *NetworkWatcher_region_name* に設定されます (*region_name* は、インスタンスが有効にされている Azure リージョン)。 たとえば、米国中西部リージョンで有効にされた Network Watcher は *NetworkWatcher_westcentralus* という名前になります。

Network Watcher インスタンスが *NetworkWatcherRG* という名前のリソース グループに自動的に作成されます。 このリソース グループが存在しない場合は、新たに作成されます。

Network Watcher インスタンスの名前とその配置先のリソース グループをカスタマイズするには、以下のセクションで説明している PowerShell、Azure CLI、REST API、ARMClient のいずれかの方法を使用します。 いずれの方法でも、Network Watcher を作成する前に、配置先のリソース グループが存在している必要があります。  

## <a name="create-a-network-watcher-with-powershell"></a>PowerShell を使用して Network Watcher を作成する

Network Watcher のインスタンスを作成するには、次のコマンド例を実行します。

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Azure CLI を使用して Network Watcher を作成する

Network Watcher のインスタンスを作成するには、次のコマンド例を実行します。

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>REST API を使用して Network Watcher を作成する

PowerShell を使用して REST API を呼び出すには、ARMClient を使用します。 ARMClient は、[Chocolatey 上の ARMClient](https://chocolatey.org/packages/ARMClient) に関するページの Chocolatey にあります

### <a name="log-in-with-armclient"></a>ARMClient でログインする

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>ネットワーク ウォッチャーを作成する

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>次の手順

これで Network Watcher のインスタンスが作成できました。利用可能な機能については、以下をご覧ください。

* [トポロジ](network-watcher-topology-overview.md)
* [パケット キャプチャ](network-watcher-packet-capture-overview.md)
* [IP フロー検証](network-watcher-ip-flow-verify-overview.md)
* [次ホップ](network-watcher-next-hop-overview.md)
* [セキュリティ グループ ビュー](network-watcher-security-group-view-overview.md)
* [NSG フロー ログの記録](network-watcher-nsg-flow-logging-overview.md)
* [Virtual Network Gateway のトラブルシューティング](network-watcher-troubleshoot-overview.md)

Network Watcher インスタンスが作成されたら、仮想マシン内でのパケット キャプチャを有効にすることができます。 方法については、[アラートでトリガーされるパケット キャプチャの作成](network-watcher-alert-triggered-packet-capture.md)に関する記事をご覧ください
