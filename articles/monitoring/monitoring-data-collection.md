---
title: Azure での監視データの収集 | Microsoft Docs
description: Azure 内でアプリケーションとサービスから収集される監視データと、監視データを分析するために使用されるツールの概要。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: bwren
ms.openlocfilehash: d3ebd512f8244de74c009ac8a2936ed8e817dad9
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38992464"
---
# <a name="collecting-monitoring-data-in-azure"></a>Azure での監視データの収集
この記事では、Azure 内でアプリケーションとサービスから収集される監視データと、監視データを分析するために使用されるツールの概要を示します。 

## <a name="types-of-monitoring-data"></a>監視データの種類
すべての監視データは、2 つの基本的な種類 (メトリックまたはログ) のいずれかに属します。 種類ごとに異なる特性があり、以下に示す特定のシナリオに最適です。

### <a name="metrics"></a>メトリック
メトリックは、特定の時点におけるシステムの何らかの側面を表す数値です。 それには、値そのもの、値が収集された時刻、値が表している測定値の種類、および値が関連付けられている特定のリソースを含む個別のデータが含まれます。 メトリックは、値の変化とは無関係に、一定の間隔で収集されます。 たとえば、プロセッサ使用率を仮想マシンから 毎分収集したり、アプリケーションにログインしたユーザーの数を 10 分間隔で収集したりします。

メトリックは軽量であり、リアルタイムに近いシナリオをサポートできます。 メトリックは頻繁にサンプリングでき、比較的単純なロジックですばやく起動できるため、アラートを発行するときに特に役に立ちます。 たとえば、メトリックがしきい値を超えた場合や 2 つのメトリックの値の差が特定の値に達した場合に、アラートを発行できます。

通常は、個々のメトリックが単独で提供する分析情報はほとんどありません。 それらが提供するのは、単純なしきい値との比較以外のコンテキストを持たない単一の値です。 ただし、それらは、他のメトリックと組み合わせてパターンや傾向を識別したり、特定の値に関するコンテキストを提供するログと組み合わせたりするときに役に立ちます。 たとえば、アプリケーションの特定の時点での特定のユーザー数からアプリケーションの正常性に関する情報を得られることはほとんどありません。 ただし、同じメトリックの複数の値によって示されるユーザー数の突然の低下は、問題を示唆している場合があります。 アプリケーションによってスローされ、異なるメトリックによって示される過剰な例外によって、低下を引き起こしているアプリケーションの問題を識別できることがあります。 アプリケーションによって作成された、アプリケーションの特定のコンポーネントで発生した障害を識別するイベントは、根本原因の特定を支援する可能性があります。

ログに基づくアラートは、メトリックに基づくアラートのように反応が早くはありませんが、より複雑なロジックを含めることができます。 複数のソースからのデータに対して複雑な分析を実行するクエリの結果に基づくアラートを作成できます。

### <a name="logs"></a>ログ
ログには、種類ごとに異なるプロパティ セットを持つレコードに編成されたさまざまな種類のデータが含まれます。 ログは、メトリックスのような数値を含むことができますが、通常は、詳細な説明付きのテキスト データが含まれます。 さらに、それらは、構造が違うことと、多くの場合定期的に収集されないという点で、メトリックとは異なります。

ログ エントリの一般的な種類は、イベントです。 イベントは、アプリケーションまたはサービスによって作成されたときに散発的に収集され、通常は、単独で完全なコンテキストを示す十分な情報が含まれています。  たとえば、イベントは、特定のリソースが作成または変更されたこと、トラフィックの増加に応答して新しいホストが開始されたこと、またはアプリケーションでエラーが検出されたことを示すことができます。

ログは、さまざまなソースからのデータを組み合わせて複雑な分析と一定期間のトレンド分析を行うときに特に役に立ちます。 データの形式は多様である可能性があるため、アプリケーションは、必要な構造を使用するカスタム ログを作成できます。 メトリックは、他の監視データと組み合わせてトレンド分析やその他のデータ分析を行うために、ログ内にレプリケートすることもできます。


## <a name="monitoring-tools-in-azure"></a>Azure の監視ツール
Azure の監視データは、次のセクションで説明されている複数のソースを使用して収集され、分析されます。

### <a name="azure-metrics"></a>Azure メトリック
Azure のリソースとアプリケーションからのメトリックは、Azure メトリックに収集されます。 メトリック データは、Azure Portal の仮想マシンなどの特定の Azure リソース用のページに統合され、各ページには、選択したマシンの CPU とネットワークの使用率などのメトリックのグラフが含まれます。 それは、一定期間の複数のメトリックの値をグラフ化できる[メトリックス エクスプローラー](../monitoring-and-diagnostics/monitoring-metric-charts.md)を使用して分析することもできます。  グラフは、対話形式で表示したり、ダッシュボードにピン留めして他の視覚化と一緒に表示したりできます。 メトリックは、[Azure monitoring REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md) を使用して取得することもできます。

[Azure での監視データのソース](monitoring-data-sources.md)に関するページで、さまざまな種類の Azure リソースによって収集されたメトリック データの詳細を確認できます。 

![メトリックス エクスプローラー](media/monitoring-data-collection/metrics-explorer.png)


### <a name="azure-activity-log"></a>Azure アクティビティ ログ 
[Azure アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)は、構成と Azure サービスの正常性に関するログを格納します。 アクティビティ ログ エクスプローラーを使用して、これらのログを Azure Portal に表示できますが、通常は、他のログ データと一緒に分析されるために [Log Analytics にコピー](../log-analytics/log-analytics-activity.md)されます。

アクティビティ ログ エクスプローラーを使用して、特定の条件と一致するようにフィルター処理されたアクティビティ ログを表示できます。  また、ほとんどのリソースには、Azure Portal のメニューの中に、そのリソース用にフィルター処理されたアクティビティ ログ エクスプローラーを表示する [アクティビティ ログ] オプションがあります。 アクティビティ ログは、[Azure monitoring REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md) を使用して取得することもできます。

![アクティビティ ログ エクスプローラー](media/monitoring-data-collection/activity-log-explorer.png)


### <a name="log-analytics"></a>Log Analytics
Log Analytics は、Azure で管理を行うための一般的なデータ プラットフォームを提供します。 それは、Azure 内にログを格納して分析するために使用されるプライマリ サービスであり、仮想マシン、管理ソリューション、およびさまざまな Azure リソースを含む多様なソースからのデータを収集します。 メトリックとアクティビティ ログを含むその他のソースからのデータを Log Analytics にコピーして、監視データの完全な中央リポジトリを作成できます。

Log Analytics には、収集したデータを分析するための高度なクエリ言語があります。  [ログ検索ポータル](../log-analytics/log-analytics-log-search-portals.md)を使用して、クエリを対話形式で記述してテストし、その結果を分析できます。 ログ検索の結果を視覚化するための[ビューを作成](../log-analytics/log-analytics-view-designer.md)したり、Azure ダッシュボードにクエリの結果を直接貼り付けたりできます。  管理ソリューションには、それらが収集したデータを分析するための Log Analytics のログ検索とビューが含まれます。 Application Insights などの他のサービスは、Log Analytics にデータを格納し、分析するための追加のツールを提供します。  

![ログ](media/monitoring-data-collection/logs.png)

### <a name="application-insights"></a>Application Insights
Application Insights は、さまざまなプラットフォームにインストールされている Web アプリケーションのテレメトリを収集します。 それは、Azure メトリックと Log Analytics にデータを格納し、データを分析したり、データを分析して視覚化したりするための既存のツールに加え、高度なツールの幅広いセットを提供します。 これにより、アラート、ログ検索、他の監視目的で使用するダッシュボードなどの共通するサービスのセットを活用できます。


![App Insights](media/monitoring-data-collection/app-insights.png)

### <a name="service-map"></a>Service Map
Service Map は、仮想マシンのプロセスと依存関係を視覚的に表現します。 それは、データの大半を Log Analytics に格納して、他の管理データと一緒に分析できるようにします。 また、Service Map コンソールは、Log Analytics からデータを取得し、分析対象の仮想マシンのコンテキストでそれを提示します。

![Service Map](media/monitoring-data-collection/service-map.png)


## <a name="transferring-monitoring-data"></a>監視データの転送

### <a name="metrics-to-logs"></a>メトリックからログへ
メトリックは、高度なクエリ言語を使用して他の種類のデータと一緒に複雑な分析を実行する Log Analytics にレプリケートすることもできます。 さらに、ログ データは、メトリックよりも長期間保持できます。それにより、一定期間のトレンド分析を実行できます。 メトリックまたはその他のパフォーマンス データは、Log Analytics に格納されているときはログとして機能します。 ほぼリアルタイムの分析とアラートをサポートする場合はメトリックを使用し、他のデータと共にトレンド分析や分析を行う場合はログを使用します。

Azure リソースからメトリックを収集するためのガイダンスについては、「[Log Analytics で Azure サービスのログとメトリックを使用できるように収集する](../log-analytics/log-analytics-azure-storage.md)」を参照してください。 Azure PaaS リソースからのリソースのメトリックを収集するためのガイダンスについては、「[Log Analytics を使用した Azure PaaS リソース メトリックの収集を構成する](../log-analytics/log-analytics-collect-azurepass-posh.md)」を参照してください。

### <a name="logs-to-metrics"></a>ログからメトリックへ
前述のように、メトリックはログよりも反応が早いため、短い待機時間と低コストでアラートを作成できます。 Log Analytics は、メトリックに適しているが、Azure メトリックには格納されない大量の数値データを収集します。 一般的な例は、エージェントと管理ソリューションから収集されるパフォーマンス データです。 これらの値の一部を Azure メトリックにコピーして、メトリックス エクスプローラーでアラートの発行と分析を行うために使用できます。

この機能の説明については、[現在限定されたパブリック プレビュー状態にあるログのメトリック アラートの高速化](https://azure.microsoft.com/blog/faster-metric-alerts-for-logs-now-in-limited-public-preview/)に関する記事を参照してください。 値のサポートの一覧については、[サポートされているメトリックと新しいメトリック アラートの作成方法](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md)に関する記事を参照してください。

### <a name="event-hub"></a>Event Hub
Azure のツールを使用した監視データの分析に加え、セキュリティ情報とイベント管理 (SIEM) 製品などの外部ツールに監視データを転送できます。 これは、通常は、[Azure Event Hub](https://docs.microsoft.com/azure/event-hubs/) を使用して行われます。 

さまざまな種類の監視データのガイダンスについては、「[外部ツールで使用する Azure 監視データのイベント ハブへのストリーミング](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- Azure のさまざまなリソースで[入手できる監視データ](monitoring-data-sources.md)を確認します。 