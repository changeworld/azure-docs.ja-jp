---
title: Azure Traffic Manager のメトリックとアラート | Microsoft Docs
description: この記事では、Azure の Traffic Manager で使用できるメトリックについて説明します。
services: traffic-manager
documentationcenter: ''
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: kumud
ms.openlocfilehash: bb7817b082da11de3071925d01a3402902410a6f
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437971"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Traffic Manager のメトリックとアラート

Traffic Manager は、DNS ベースの負荷分散を提供します。これには、複数のルーティング方法およびエンドポイント監視オプションが含まれます。 この記事では、お客様が使用できるメトリックおよび関連するアラートについて説明します。 

## <a name="metrics-available-in-traffic-manager"></a>Traffic Manager で使用可能なメトリック 

Traffic Manager は、プロファイルごとに次のメトリックを提供します。これらは、Traffic Manager の使用状況およびそのプロファイルのエンドポイントの状態を理解するためにお客様が使用できます。  

### <a name="queries-by-endpoint-returned"></a>エンドポイント別の返されたクエリ数
[このメトリック](../azure-monitor/platform/metrics-supported.md)を使用して、指定された期間に Traffic Manager プロファイルによって処理されたクエリ数を表示します。 クエリの応答で Traffic Manager からエンドポイントが返された回数を把握できるようにエンドポイント レベルの粒度で同じ情報を表示することも可能です。

次の例では、図 1 は Traffic Manager プロファイルによって返されたすべてのクエリの応答を表示します。 

  
![Traffic Manager メトリック - すべてのクエリの集計ビュー](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

"*図 1:すべてのクエリを含む集計ビュー*"
  
図 2 は同じ情報を表示しますが、エンドポイント別に分割されます。 その結果、特定のエンドポイントが返されるクエリの応答数を確認できます。

![Traffic Manager メトリック - クエリ数のエンドポイント別の分割ビュー](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

"*図 2:返されたエンドポイント別に表示したクエリ数の分割ビュー*"

## <a name="endpoint-status-by-endpoint"></a>エンドポイント別のエンドポイント状態
[このメトリック](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles)を使用して、プロファイルのエンドポイントの正常性状態を理解します。 これには次の 2 つの値があります。
 - エンドポイントが動作中の場合、**1** を使用します。
 - エンドポイントが停止中の場合、**0** を使用します。

このメトリックは、すべてのメトリックの状態を表す集計値として表示 (図 3) または分割して (図 4 参照) 特定のエンドポイントの状態を示すことができます。 前者の場合、集計レベルが **Avg** として選択されている場合、このメトリックの値はすべてのエンドポイントの状態の算術平均です。 たとえば、プロファイルに 2 つのエンドポイントがあり、1 つのみが正常である場合、図 3 で示すようにこのメトリックの値は **0.50** になります。 


![Traffic Manager メトリック - エンドポイントの状態の複合ビュー](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

"*図 3:エンドポイントの状態メトリックの複合ビュー – "Avg" 集計を選択*"


![Traffic Manager メトリック - エンドポイントの状態の分割ビュー](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

"*図 4:エンドポイントの状態メトリックの分割ビュー*"

[Azure Monitor サービス](../azure-monitor/platform/metrics-supported.md)のポータル、[REST API](https://docs.microsoft.com/rest/api/monitor/)、[Azure CLI](https://docs.microsoft.com/cli/azure/monitor)、および [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights) から、または Traffic Manager のポータル操作のメトリック選択によってこれらのメトリックを使用できます。

## <a name="alerts-on-traffic-manager-metrics"></a>Traffic Manager メトリックのアラート
Traffic Manager からメトリックをプロセスおよび表示することに加えて、Azure Monitor ではお客様はこれらのメトリックに関連するアラートを構成して受信できます。 アラートを引き起こすためにこれらのメトリックで満たす必要がある条件、これらの条件を監視する頻度、アラートの送信方法を選択できます。 詳細については、[Azure Monitor アラートのドキュメント](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)をご覧ください。

## <a name="next-steps"></a>次の手順
- [Azure Monitor サービス](../azure-monitor/platform/metrics-supported.md)を確認する
- [Azure Monitor を使用して新しいグラフを作成](../azure-monitor/platform/metrics-charts.md#create-a-new-chart)する方法を確認する
