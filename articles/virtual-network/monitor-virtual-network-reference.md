---
title: Azure 仮想ネットワーク データの監視に関するリファレンス
description: Azure 仮想ネットワークを監視する際に必要となる重要なリファレンス資料
author: duongau
ms.topic: reference
ms.author: duau
ms.service: expressroute
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: 2494c0c0cdb2b4a7b06b4cf246f70a46deb8a3a4
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113109865"
---
# <a name="monitoring-azure-virtual-network-data-reference"></a>Azure 仮想ネットワーク データの監視に関するリファレンス

Azure 仮想ネットワークの監視データの収集と分析の詳細については、「[Azure 仮想ネットワークの監視](monitor-virtual-network.md)」を参照してください。

## <a name="metrics"></a>メトリック

このセクションでは、Azure 仮想ネットワーク用に自動的に収集されるすべてのプラットフォーム メトリックを示します。  

| メトリックの種類 | リソース プロバイダー/種類の名前空間<br/> および個々のメトリックへのリンク |
|-------|-----|
| 仮想ネットワーク | [Microsoft.Network/virtualNetworks](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkvirtualnetworks) |
| ネットワーク インターフェイス | [Microsoft.Network/networkInterfaces](../azure-monitor/essentials/metrics-supported.md#microsoftnetworknetworkinterfaces) |
| パブリック IP アドレス | [Microsoft.Network/publicIPAddresses](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses) |
| NAT ゲートウェイ | [Microsoft.Network/natGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)

詳細については、[Azure Monitor でサポートされているすべてのプラットフォーム メトリック](../azure-monitor/essentials/metrics-supported.md)の一覧を参照してください。

## <a name="metric-dimensions"></a>メトリック ディメンション

メトリック ディメンションの詳細については、「[多次元メトリック](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)」を参照してください。

Azure 仮想ネットワークでは、次のディメンションがメトリックに関連付けられています。

### <a name="dimensions-for-nat-gateway"></a>NAT ゲートウェイのディメンション

| ディメンション名 | 説明 |
| ------------------- | ----------------- |
| **方向 (Out - In)** | トラフィック フローの方向。 サポートされている値は In と Out です。 |
| **プロトコル** | トランスポート プロトコルの種類。 サポートされている値は TCP と UDP です。 |

## <a name="resource-logs"></a>リソース ログ

このセクションでは、Azure 仮想ネットワークで使用されるリソースに関して収集できるリソース ログの種類を示します。 

[Azure Monitorでサポートされているすべてのリソース ログのカテゴリの種類](../azure-monitor/essentials/resource-logs-schema.md)の一覧も参照してください。

|リソース ログの種類 | リソース プロバイダー/種類の名前空間<br/> および個々のメトリックへのリンク |
|-------|-----|
| ネットワーク セキュリティ グループ | [Microsoft.Network/networksecuritygroups](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworknetworksecuritygroups) |
| パブリック IP アドレス | [Microsoft.Network/publicIPAddresses](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkpublicipaddresses) |

## <a name="azure-monitor-logs-tables"></a>Azure Monitor ログ テーブル

このセクションでは、Azure 仮想ネットワークに関連し、Log Analytics によるクエリに使用できるすべての Azure Monitor ログの Kusto テーブルを参照します。 

|リソースの種類 | メモ |
|-------|-----|
| 仮想ネットワーク | [Microsoft.Network/virtualNetworks](/azure/azure-monitor/reference/tables/tables-resourcetype#virtual-networks) |
| ネットワーク インターフェイス | [Microsoft.Network/networkInterface](/azure/azure-monitor/reference/tables/tables-resourcetype#network-interfaces) |
| パブリック IP アドレス | [Microsoft.Network/publicIP](/azure/azure-monitor/reference/tables/tables-resourcetype#public-ip-addresses) |

### <a name="diagnostics-tables"></a>診断テーブル

**仮想ネットワーク**

Azure 仮想ネットワークに診断ログはありません。

## <a name="activity-log"></a>アクティビティ ログ

次の表は、アクティビティ ログで作成される可能性がある Azure 仮想ネットワークに関連する操作を示しています。

| 操作 | 説明 |
|:---|:---|
| すべての管理操作 | Azure 仮想ネットワークの作成、更新、削除を含むすべての管理操作。 |
| 仮想ネットワークを作成または更新する | 仮想ネットワークが作成または更新されました。 |
| 仮想ネットワークを削除する | 仮想ネットワークが削除されました。|

アクティビティ ログ エントリのスキーマの詳細については、[アクティビティ ログのスキーマ](../azure-monitor/essentials/activity-log-schema.md)に関する記事を参照してください。

## <a name="see-also"></a>関連項目

- Azure 仮想ネットワークの監視の詳細については、「[Azure 仮想ネットワークを監視する](monitor-virtual-network.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。
