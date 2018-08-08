---
title: Azure で監視データを収集する | Microsoft Docs
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
ms.openlocfilehash: 35580d71aa2592fa94f42cfdbad3c192acc303c5
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363987"
---
# <a name="collect-monitoring-data-in-azure"></a>Azure で監視データを収集する
この記事では、Azure 内でアプリケーションとサービスから収集される監視データの概要を示します。 データの分析に使用できるツールについても説明します。 

## <a name="types-of-monitoring-data"></a>監視データの種類
すべての監視データは、2 つの基本的な種類 (メトリックまたはログ) のいずれかに属します。 種類ごとに異なる特性があり、特定のシナリオに最適です。

### <a name="metrics"></a>メトリック
メトリックは、特定の時点におけるシステムの何らかの側面を表す数値です。 以下が含まれます。

* 値自体を含む個別データ。
* 値が収集された時刻。
* 値が表す測定の種類。
* 値が関連付けられるリソース。 

メトリックは、値の変化とは無関係に、一定の間隔で収集されます。 たとえば、プロセッサ使用率を仮想マシンから 毎分収集したり、アプリケーションにログインしたユーザーの数を 10 分間隔で収集したりします。

メトリックは軽量であり、リアルタイムに近いシナリオをサポートできます。 メトリックは頻繁にサンプリングでき、比較的単純なロジックですばやく起動できるため、アラートを発行するときに役に立ちます。 たとえば、メトリックがしきい値を超えたときにアラートを生成できます。 または、2 つのメトリックの差が特定の値に達したときに、アラートを生成できます。

通常は、個々のメトリックが単独で提供する分析情報はほとんどありません。 それらが提供するのは、単純なしきい値との比較以外のコンテキストを持たない単一の値です。 それらは、他のメトリックと組み合わせてパターンや傾向を識別したり、特定の値に関するコンテキストを提供するログと組み合わせたりするときに役に立ちます。 

たとえば、アプリケーションの特定の時点での特定のユーザー数からアプリケーションの正常性に関する情報を得られることはほとんどありません。 ただし、同じメトリックの複数の値によって示されるユーザー数の突然の低下は、問題を示唆している場合があります。 アプリケーションによってスローされ、異なるメトリックによって示される過剰な例外によって、低下を引き起こしているアプリケーションの問題を識別できることがあります。 コンポーネントでのエラーを識別するためにアプリケーションが作成するイベントは、根本原因を特定するのに役立ちます。

ログに基づくアラートは、メトリックに基づくアラートのように反応が早くはありませんが、より複雑なロジックを含めることができます。 複数のソースからのデータに対して複雑な分析を実行するクエリの結果に基づくアラートを作成できます。

### <a name="logs"></a>ログ
ログには、種類ごとに異なるプロパティ セットを持つレコードに編成されたさまざまな種類のデータが含まれます。 ログは、メトリックスのような数値を含むことができますが、通常は、詳細な説明付きのテキスト データが含まれます。 さらに、それらは、構造が違うことと、多くの場合定期的に収集されないという点で、メトリックとは異なります。

ログ エントリの一般的な種類は、イベントです。 イベントは散発的に収集されます。 イベントは、アプリケーションまたはサービスによって作成され、通常は、単独で完全なコンテキストを示す十分な情報が含まれています。 たとえば、イベントは、特定のリソースが作成または変更されたこと、トラフィックの増加に応答して新しいホストが開始されたこと、またはアプリケーションでエラーが検出されたことを示すことができます。

ログは、さまざまなソースからのデータを組み合わせて複雑な分析と一定期間のトレンド分析を行うときに特に役に立ちます。 データの形式は多様である可能性があるため、アプリケーションは、必要な構造を使用するカスタム ログを作成できます。 メトリックは、他の監視データと組み合わせてトレンド分析やその他のデータ分析を行うために、ログ内にレプリケートすることもできます。


## <a name="monitoring-tools-in-azure"></a>Azure の監視ツール
Azure の監視データは以下のソースによって収集および分析されます。

### <a name="azure-monitor"></a>Azure Monitor
Azure のリソースとアプリケーションからのメトリックは、Azure Monitor に収集されます。 メトリック データは、Azure リソースに対する Azure portal のページに統合されます。 仮想マシンの場合、CPU やネットワークの使用率などのメトリックのグラフが選択したコンピューターに対して表示されます。 

それは、一定期間の複数のメトリックの値をグラフ化するる[メトリックス エクスプローラー](../monitoring-and-diagnostics/monitoring-metric-charts.md)を使用してデータを分析することもできます。 グラフは、対話形式で表示したり、ダッシュボードにピン留めして他の視覚化と一緒に表示したりできます。 [Azure monitoring REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md) を使用してメトリックを取得することもできます。

さまざまな種類の Azure リソースによって収集されるメトリック データについて詳しくは、「[Azure で使用する監視データのソース](monitoring-data-sources.md)」をご覧ください。 

![メトリックス エクスプローラー](media/monitoring-data-collection/metrics-explorer.png)


### <a name="activity-log"></a>アクティビティ ログ 
[Azure アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)は、構成と Azure サービスの正常性に関するログを格納します。 アクティビティ ログ エクスプローラーを使用して、これらのログを Azure portal に表示できますが、通常は、他のログ データと一緒に分析されるために [Azure Log Analytics にコピー](../log-analytics/log-analytics-activity.md)されます。

アクティビティ ログ エクスプローラーを使用して、特定の条件と一致するようにフィルター処理されたアクティビティ ログを表示できます。 Azure portal では、ほとんどのリソースのメニューに **[アクティビティ ログ]** オプションもあります。 このオプションでは、そのリソースだけにフィルター処理されたアクティビティ ログ エクスプローラーが表示されます。 アクティビティ ログは、[Azure Monitoring REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md) を使用して取得することもできます。

![アクティビティ ログ エクスプローラー](media/monitoring-data-collection/activity-log-explorer.png)


### <a name="log-analytics"></a>Log Analytics
Log Analytics は、Azure で管理を行うための一般的なデータ プラットフォームを提供します。 これは、Azure でログの保存と分析に使用される主要なサービスです。 仮想マシン上のエージェント、管理ソリューション、Azure リソースなど、さまざまなソースからデータを収集します。 メトリックとアクティビティ ログを含む他のソースからデータをコピーして、監視データの完全な中央リポジトリを作成できます。

Log Analytics には、収集したデータを分析するための高度なクエリ言語があります。 [ログ検索ポータル](../log-analytics/log-analytics-log-search-portals.md)を使用して、クエリを対話形式で記述してテストし、その結果を分析できます。 ログ検索の結果を視覚化するための[ビューを作成](../log-analytics/log-analytics-view-designer.md)したり、Azure ダッシュボードにクエリの結果を直接貼り付けたりできます。  

管理ソリューションには、それらが収集したデータを分析するための Log Analytics のログ検索とビューが含まれます。 Azure Application Insights などの他のサービスは、Log Analytics にデータを格納し、分析するための追加のツールを提供します。  

![ログ](media/monitoring-data-collection/logs.png)

### <a name="application-insights"></a>Application Insights
Application Insights は、さまざまなプラットフォームにインストールされている Web アプリケーションのテレメトリを収集します。 データは Azure Monitor と Log Analytics に格納されます。 また、データを分析して視覚化するための広範なツール セットが用意されています。 これらの機能により、アラート、ログ検索、他の監視目的で使用するダッシュボードなどの共通するサービスのセットを活用できます。


![Application Insights](media/monitoring-data-collection/app-insights.png)

### <a name="service-map"></a>サービス マップ
Service Map は、仮想マシンのプロセスと依存関係を視覚的に表現します。 それは、データの大半を Log Analytics に格納して、他の管理データと一緒に分析できるようにします。 また、Service Map コンソールは、Log Analytics からデータを取得し、分析対象の仮想マシンのコンテキストでそれを提示します。

![サービス マップ](media/monitoring-data-collection/service-map.png)


## <a name="transferring-monitoring-data"></a>監視データの転送

### <a name="metrics-to-logs"></a>メトリックからログへ
メトリックを Log Analytics にレプリケートし、高度なクエリ言語を使用して他の種類のデータと一緒に複雑な分析を実行することもできます。 さらに、ログ データは、メトリックよりも長期間保持できます。それにより、一定期間のトレンド分析を実行できます。 メトリックまたはその他のパフォーマンス データは、Log Analytics に格納されているときはログとして機能します。 ほぼリアルタイムの分析とアラートをサポートする場合はメトリックを使用し、他のデータと共にトレンド分析や分析を行う場合はログを使用します。

Azure リソースからメトリックを収集するためのガイダンスについては、「[Log Analytics で Azure サービスのログとメトリックを使用できるように収集する](../log-analytics/log-analytics-azure-storage.md)」を参照してください。 Azure PaaS リソースからのリソースのメトリックを収集するためのガイダンスについては、「[Log Analytics を使用した Azure PaaS リソース メトリックの収集を構成する](../log-analytics/log-analytics-collect-azurepass-posh.md)」を参照してください。

### <a name="logs-to-metrics"></a>ログからメトリックへ
前述のように、メトリックはログよりも反応が早いため、短い待機時間と低コストでアラートを作成できます。 Log Analytics は、メトリックに適しているが、Azure Monitor には格納されない大量の数値データを収集します。 

一般的な例は、エージェントと管理ソリューションから収集されるパフォーマンス データです。 これらの値の一部を Azure Monitor にコピーして、メトリックス エクスプローラーでアラートの発行と分析を行うために使用できます。

この機能の説明については、[現在限定されたパブリック プレビュー状態にあるログのメトリック アラートの高速化](https://azure.microsoft.com/blog/faster-metric-alerts-for-logs-now-in-limited-public-preview/)に関する記事を参照してください。 値のサポートの一覧については、[サポートされているメトリックと新しいメトリック アラートの作成方法](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md)に関する記事を参照してください。

### <a name="event-hubs"></a>Event Hubs
Azure のツールを使用した監視データの分析に加え、セキュリティ情報とイベント管理 (SIEM) 製品などの外部ツールに監視データを転送できます。 この転送は、通常、[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) を通して行われます。 

さまざまな種類の監視データのガイダンスについては、「[外部ツールで使用する Azure 監視データのイベント ハブへのストリーミング](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- Azure のさまざまなリソースで[入手できる監視データ](monitoring-data-sources.md)を確認します。 