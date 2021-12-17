---
title: Azure 仮想ネットワークの監視
description: Azure 仮想ネットワークを監視する方法については、こちらでご確認ください
services: virtual-network
author: duongau
ms.author: duau
ms.service: virtual-network
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: bec4d18ebc762ecf573281341e907db4376f26b2
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297639"
---
# <a name="monitoring-azure-virtual-network"></a>Azure 仮想ネットワークの監視

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 

この記事では、Azure 仮想ネットワークによって生成される監視データについて説明します。 Azure 仮想ネットワークでは、[Azure Monitor](../azure-monitor/overview.md) が使用されます。 Azure Monitor を使用するすべての Azure サービスにとって共通する機能に詳しくない場合は、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。

## <a name="monitoring-data"></a>データの監視 

Azure 仮想ネットワークでは、他の Azure リソースと同じ種類の監視データが収集されます。これについては、[Azure リソースのデータの監視に関するページ](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)をご覧ください。 

Azure 仮想ネットワークによって作成されるメトリックとログの詳細については、[Azure 仮想ネットワークの監視データのリファレンス](monitor-virtual-network-reference.md)をご覧ください。

## <a name="collection-and-routing"></a>収集とルーティング

プラットフォーム メトリックとアクティビティ ログは自動的に収集および格納されますが、診断設定を使用して他の場所にルーティングすることもできます。  

リソース ログは、診断設定を作成して 1 つ以上の場所にルーティングするまでは収集および格納されません。

Azure portal、CLI、または PowerShell を使用して診断設定を作成するプロセスの詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。 診断設定を作成するときは、収集するログのカテゴリを指定します。 "*Azure 仮想ネットワーク*" のカテゴリの一覧については、[Azure 仮想ネットワークの監視データのリファレンス](monitor-virtual-network-reference.md#resource-logs)に記載されています。

> [!IMPORTANT]
> これらの設定を有効にするには、追加の Azure サービス (ストレージ アカウント、イベント ハブ、または Log Analytics) が必要であり、コストが増加する可能性があります。 推定コストを計算するには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator)にアクセスしてください。

収集できるメトリックとログについては、次のセクションで説明します。

## <a name="analyzing-metrics"></a>メトリックの分析

現在、Azure Monitor では、メトリックス エクスプローラーからの "*Azure 仮想ネットワーク*" メトリックの分析がサポートされていません。 "*Azure 仮想ネットワーク*" メトリックを表示するには、分析する仮想ネットワークの **[監視]** で、 **[メトリック]** を選択します。

:::image type="content" source="./media/monitor-virtual-network/metrics.png" alt-text="仮想ネットワークのメトリック ダッシュボードのスクリーンショット。" lightbox="./media/monitor-virtual-network/metrics-expanded.png":::

Azure 仮想ネットワークに関して収集されるプラットフォーム メトリックの一覧については、[Azure 仮想ネットワークの監視データのリファレンスの「メトリック」](monitor-virtual-network-reference.md#metrics)を参照してください。

参考のために、[Azure Monitor でサポートされているすべてのリソース メトリック](../azure-monitor/essentials/metrics-supported.md)の一覧を確認できます。

## <a name="analyzing-logs"></a>ログの分析

Azure 仮想ネットワークでは、リソース ログはサポートされていません。

仮想ネットワーク内のリソースに関して収集されるプラットフォーム メトリックの一覧については、[仮想ネットワークの監視データのリファレンス](monitor-virtual-network-reference.md#resource-logs)をご覧ください   

[アクティビティ ログ](../azure-monitor/essentials/activity-log.md)は、サブスクリプションレベルのイベントに関する分析情報を提供する Azure のプラットフォーム ログの一種です。 個別に表示できるほか、Azure Monitor ログにルーティングして、Log Analytics を使用してより複雑なクエリを実行することもできます。  

## <a name="alerts"></a>警告

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 これにより、ユーザーが気付く前に、管理者が問題を識別して対処できます。 アラートは[メトリック](../azure-monitor/alerts/alerts-metric-overview.md)、[ログ](../azure-monitor/alerts/alerts-unified-log.md)、[アクティビティ ログ](../azure-monitor/alerts/activity-log-alerts.md)に対して設定できます。 アラートの種類に応じて、さまざまな利点と欠点があります。

次の表に、Azure 仮想ネットワークの一般的および推奨されるアクティビティ アラート ルールを示します。

| アラートの種類 | 条件 | 説明  |
|:---|:---|:---|
| 仮想ネットワークを作成または更新する | イベントのレベル: すべて選択済み、状態: すべて選択済み、イベント開始者: すべてのサービスとユーザー | ユーザーが仮想ネットワークを作成したか、構成変更を行ったとき。 |
| 仮想ネットワークを削除する | イベントのレベル: すべて選択済み、状態: 開始 | ユーザーが仮想ネットワークを削除したとき。 |

## <a name="next-steps"></a>次のステップ

* Azure 仮想ネットワークによって作成されるメトリック、ログ、その他の重要な値のリファレンスについては、[仮想ネットワークの監視データのリファレンス](monitor-virtual-network-reference.md)をご覧ください。
* Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/overview.md)」を参照してください。