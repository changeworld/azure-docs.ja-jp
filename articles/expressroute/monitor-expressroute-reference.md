---
title: ExpressRoute の監視データのリファレンス
description: ExpressRoute を監視する際に必要となる重要なリファレンス資料
author: duongau
ms.topic: reference
ms.author: duau
ms.service: expressroute
ms.custom: subject-monitoring
ms.date: 06/22/2021
ms.openlocfilehash: cd5a4ef0196843069d50f333089f8bffca956acc
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289808"
---
# <a name="monitoring-expressroute-data-reference"></a>ExpressRoute の監視データのリファレンス

この記事では、ExpressRoute のパフォーマンスと可用性を分析するために収集されるログとメトリック データのリファレンスを提供します。
ExpressRoute の監視データの収集と分析の詳細については、「[ExpressRoute の監視](monitor-expressroute.md)」を参照してください。

## <a name="metrics"></a>メトリック

このセクションには、ExpressRoute の自動的に収集されるすべてのプラットフォーム メトリックが一覧表示されています。 詳細については、[Azure Monitor でサポートされているすべてのプラットフォーム メトリック](../azure-monitor/essentials/metrics-supported.md)の一覧を参照してください。

| メトリックの種類 | リソース プロバイダー/種類の名前空間<br/> および個々のメトリックへのリンク |
|-------|-----|
| ExpressRoute 回線 | [Microsoft.Network/expressRouteCircuits](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
| ExpressRoute 回線ピアリング | [Microsoft.Network/expressRouteCircuits/peerings](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutecircuitspeerings) |
| ExpressRoute ゲートウェイ | [Microsoft.Network/expressRouteGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressroutegateways) |
| ExpressRoute Direct | [Microsoft.Network/expressRoutePorts](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkexpressrouteports) |

>[!NOTE]
> *GlobalGlobalReachBitsInPerSecond* と *GlobalGlobalReachBitsOutPerSecond* の使用は、少なくとも 1 つの Global Reach 接続が確立されている場合にのみ表示されます。
>

## <a name="metric-dimensions"></a>メトリック ディメンション

メトリック ディメンションの詳細については、「[多次元メトリック](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)」を参照してください。

ExpressRoute では、次のディメンションがそのメトリックに関連付けられています。

### <a name="dimension-for-expressroute-circuit"></a>ExpressRoute 回線のディメンション

| ディメンション名 | 説明 |
| ------------------- | ----------------- |
| **PeeringType** | 構成されるピアリングの種類。 サポートされている値は、Microsoft ピアリングとプライベート ピアリングです。 |
| **ピアリング** | サポートされている値は Primary と Secondary です。 |
| **PeeredCircuitSkey** | Global Reach を使用して接続されるリモートの ExpressRoute 回線サービス キー。 |

### <a name="dimension-for-expressroute-gateway"></a>ExpressRoute ゲートウェイのディメンション

| ディメンション名 | 説明 |
| ------------------- | ----------------- |
| **roleInstance** | ゲートウェイ インスタンス。 各 ExpressRoute ゲートウェイは複数のインスタンスで構成され、サポートされる値は GatewayTenantWork_IN_X (X は最小が 0 で、最大がゲートウェイ インスタンス数 -1 です) です。 |

### <a name="dimension-for-express-direct"></a>Express Direct のディメンション

| ディメンション名 | 説明 |
| ------------------- | ----------------- |
| **リンク** | 物理リンク。 各 ExpressRoute Direct ポート ペアは冗長性のために 2 つの物理リンクで構成され、サポートされる値は link1 と link2 です。 |

## <a name="resource-logs"></a>リソース ログ

このセクションでは、ExpressRoute 用に収集できるリソース ログの種類を示します。 

|リソース ログの種類 | リソース プロバイダー/種類の名前空間<br/> および個々のメトリックへのリンク |
|-------|-----|
| ExpressRoute 回線 | [Microsoft.Network/expressRouteCircuits](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkexpressroutecircuits) |

[Azure Monitorでサポートされているすべてのリソース ログのカテゴリの種類](../azure-monitor/essentials/resource-logs-schema.md)の一覧も参照してください。

## <a name="azure-monitor-logs-tables"></a>Azure Monitor ログ テーブル

Azure ExpressRoute では、Azure Monitor ログの Kusto テーブルが使用されます。 これらのテーブルに対して Log Analytics を使用してクエリを実行できます。 すべての Azure Monitor ログ/Log Analytics テーブルのリファレンスについては、[Azure Monitor ログ テーブル リファレンス](/azure/azure-monitor/reference/tables/tables-resourcetype)に関するページを参照してください。

## <a name="activity-log"></a>アクティビティ ログ

次の表は、アクティビティ ログで作成される可能性がある ExpressRoute に関連する操作を示しています。

| 操作 | 説明 |
|:---|:---|
| すべての管理操作 | ExpressRoute 回線の作成、更新、削除を含むすべての管理操作。 |
| ExpressRoute 回線の作成または更新 | ExpressRoute 回線が作成または更新されました。 |
| ExpressRoute 回線の削除 | ExpressRoute 回線が削除されました。|

アクティビティ ログ エントリのスキーマの詳細については、[アクティビティ ログのスキーマ](../azure-monitor/essentials/activity-log-schema.md)に関する記事を参照してください。

## <a name="schemas"></a>スキーマ

診断ログの上位スキーマについて詳しくは、「[Azure 診断ログでサポートされているサービス、スキーマ、カテゴリ](../azure-monitor/essentials/resource-logs-schema.md)」をご覧ください。

Log Analytics 経由でメトリックを確認すると、出力に次の列が含まれます。

|**列**|**Type**|**説明**|
| --- | --- | --- |
|TimeGrain|string|PT1M (メトリック値が 1 分ごとにプッシュされます)|
|Count|real|通常は 2 に等しい (各 MSEE により、1 分ごとにメトリック値が 1 つプッシュされます)|
|最小値|real|2 つの MSEE によってプッシュされる 2 つのメトリック値の最小値|
|最大値|real|2 つの MSEE によってプッシュされる 2 つのメトリック値の最大値|
|Average|real|(最小値 + 最大値)/2 に等しい|
|合計|real|両方の MSEE からの 2 つのメトリック値の合計 (クエリが実行されたメトリックの場合、メインの値に焦点が当たる)|

## <a name="see-also"></a>関連項目

- Azure ExpressRoute の監視については、「[Azure ExpressRoute の監視](monitor-expressroute.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。