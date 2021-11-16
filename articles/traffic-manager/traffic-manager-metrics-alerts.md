---
title: Azure Traffic Manager のメトリックとアラート
description: この記事では、Azure の Traffic Manager で使用できるメトリックとアラートについて説明します。
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: duau
ms.openlocfilehash: eaa1ef35432a0e31376bcff8f50bc8bdffbfaf58
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054670"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Traffic Manager のメトリックとアラート

Traffic Manager は、DNS ベースの負荷分散を提供します。これには、複数のルーティング方法およびエンドポイント監視オプションが含まれます。 この記事では、お客様が使用できるメトリックおよび関連するアラートについて説明します。 

## <a name="metrics-available-in-traffic-manager"></a>Traffic Manager で使用可能なメトリック 

Traffic Manager は、プロファイルごとに次のメトリックを提供します。これらは、Traffic Manager の使用状況およびそのプロファイルのエンドポイントの状態を理解するためにお客様が使用できます。  

### <a name="queries-by-endpoint-returned"></a>エンドポイント別の返されたクエリ数
[このメトリック](../azure-monitor/essentials/metrics-supported.md)を使用して、指定した期間にわたって Traffic Manager プロファイルが処理したクエリの数を表示します。 クエリの応答で Traffic Manager からエンドポイントが返された回数を把握できるようにエンドポイント レベルの粒度で同じ情報を表示することも可能です。

次の例の図 1 は、Traffic Manager プロファイルによって返されたすべてのクエリの応答を示しています。 

  
![すべてのクエリの集計ビュー](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*図 1: すべてのクエリを含む集計ビュー*
  
図 2 は同じ情報を表示しますが、エンドポイント別に分割されます。 その結果、特定のエンドポイントが返されるクエリの応答数を確認できます。

![Traffic Manager メトリック - クエリ数のエンドポイント別の分割ビュー](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*図 2: 返されたエンドポイント別に表示したクエリ数の分割ビュー*

## <a name="endpoint-status-by-endpoint"></a>エンドポイント別のエンドポイント状態
[このメトリック](../azure-monitor/essentials/metrics-supported.md#microsoftnetworktrafficmanagerprofiles)を使用して、プロファイルのエンドポイントの正常性状態を理解します。 これには次の 2 つの値があります。
 - エンドポイントが動作中の場合、**1** を使用します。
 - エンドポイントが停止中の場合、**0** を使用します。

このメトリックは、すべてのメトリックの状態を表す集計値として表示 (図 3) または分割して (図 4 参照) 特定のエンドポイントの状態を示すことができます。 前者の場合、集計レベルが **Avg** として選択されている場合、このメトリックの値はすべてのエンドポイントの状態の算術平均です。 たとえば、プロファイルに 2 つのエンドポイントがあり、1 つのみが正常である場合、図 3 に示したように、このメトリックの値は **0.50** になります。 


![Traffic Manager メトリック - エンドポイントの状態の複合ビュー](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*図 3: エンドポイントの状態メトリックの複合ビュー – 選択した “Avg” 集計*


![Traffic Manager メトリック - エンドポイントの状態の分割ビュー](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*図 4: エンドポイントの状態メトリックの分割ビュー*

[Azure Monitor サービス](../azure-monitor/essentials/metrics-supported.md)のポータル、[REST API](/rest/api/monitor/)、[Azure CLI](/cli/azure/monitor)、および [Azure PowerShell](/powershell/module/az.applicationinsights) から、または Traffic Manager のポータル操作のメトリック選択によってこれらのメトリックを使用できます。

## <a name="alerts-on-traffic-manager-metrics"></a>Traffic Manager メトリックのアラート
Traffic Manager からメトリックをプロセスおよび表示することに加えて、Azure Monitor ではお客様はこれらのメトリックに関連するアラートを構成して受信できます。 アラートを引き起こすためにこれらのメトリックで満たす必要がある条件、これらの条件を監視する頻度、アラートの送信方法を選択できます。 詳細については、[Azure Monitor アラートのドキュメント](../azure-monitor/alerts/alerts-metric.md)をご覧ください。

プローブがダウンしたときにシステムに通知されるようにするには、アラート監視が重要です。 非常に機密性の高い監視は、邪魔になることがあります。 Traffic Manager では、回復性を向上させるために複数のプローブをデプロイします。 プローブの状態のしきい値は、0.5 未満である必要があります。 **アップ** 状態の平均が 0.5 を下回る場合 (つまり、プローブの 50% 未満がアップの場合)、エンドポイント障害のアラートが出される必要があります。

> [!NOTE]
> 回復性を向上させるために、複数のプローブがデプロイされます。 送信されている多数のプローブのうち 1 つがダウンしている場合、エンドポイントがダウンしていることを反映しているとは限りません。 返されたプローブの大半がダウンしている場合、エンドポイントはダウンとしてのみ分類されます。

次の構成は、アラート設定の一例です。

:::image type="content" source="./media/traffic-manager-metrics-alerts/alert-example.png" alt-text="プローブしきい値アラートの例のスクリーンショット。":::

プローブと監視の詳細については、「[Traffic Manager エンドポイントの監視](traffic-manager-monitoring.md)」を参照してください。

## <a name="next-steps"></a>次の手順
- [Azure Monitor サービス](../azure-monitor/essentials/metrics-supported.md)を確認する
- [Azure Monitor を使用してグラフを作成](../azure-monitor/essentials/metrics-getting-started.md#create-your-first-metric-chart)する方法を確認する