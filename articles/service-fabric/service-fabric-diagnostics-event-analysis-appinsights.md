---
title: Application Insights を使用した Azure Service Fabric イベント分析 | Microsoft Docs
description: Azure Service Fabric クラスターの監視と診断に Application Insights を使用したイベントの視覚化と分析について説明します。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/04/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: fa04e7a3c0d6f19603befed026b316eba6e46eb7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Application Insights を使用したイベント分析と視覚化

Azure Application Insights は、アプリケーションの監視と診断のための拡張可能なプラットフォームです。 このプラットフォームには、強力な分析ツールとクエリ ツール、カスタマイズ可能なダッシュボードや視覚化、さらには自動アラート通知を含むオプションが含まれています。 これは、Service Fabric アプリケーションとサービスの監視と診断用に推奨されるプラットフォームです。 この記事を読むと、次の一般的な問題に対処できます

* アプリケーションとサービスの内部で何が行われているかを理解し、テレメトリを収集する方法
* 互いに通信しているアプリケーション (特にサービス) のトラブルシューティングを行う方法
* サービスのパフォーマンスに関するメトリック (たとえば、ページの読み込み時間や http 要求など) を取得する方法

この記事の目的は、App Insights 内から情報を取得し、トラブルシューティングを行う方法を示すことです。 Service Fabric で AI を設定および構成する方法については、この[チュートリアル](service-fabric-tutorial-monitoring-aspnet.md)をご覧ください。

## <a name="monitoring-in-app-insights"></a>App Insights 内での監視

Application Insights には、Service Fabric ですぐに利用できる豊富な機能が用意されています。 [概要] ページでは、AI はサービスに関する重要な情報 (応答時間や処理された要求の数など) を提供します。 上部にある [検索] をクリックすると、アプリケーション内での最近の要求の一覧を確認できます。 さらに、失敗した要求をここで確認し、発生したエラーを診断できます。

![AI の概要](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

前の図の右側のパネルでは、リスト内に要求およびイベントという 2 つの主要な種類のエントリがあります。 要求は、HTTP 要求を通じてアプリの API に対して行われた呼び出しです。イベントはカスタム イベントで、コード内の任意の場所に追加できるテレメトリとして機能します。 アプリケーションのインストルメント化については、「[カスタムのイベントとメトリックのための Application Insights API](../application-insights/app-insights-api-custom-events-metrics.md)」で詳しく調べることができます。 要求をクリックすると、次の図に示すような詳細が表示されます。これには、AI Service Fabric NuGet パッケージ内で収集される Service Fabric 固有のデータが含まれます。 この情報はアプリケーションのトラブルシューティングと状態の確認に役立ち、このすべての情報を Application Insights 内で検索できます

![AI 要求の詳細](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights には、取得したすべてのデータに対してクエリを実行するための指定ビューがあります。 AI ポータルに移動するには、[概要] ページの上部にある [メトリックス エクスプローラー] をクリックします。 ここで、Kusto クエリ言語を使用して前述のカスタム イベント、要求、例外、パフォーマンス カウンター、その他のメトリックに対してクエリを実行できます。 次の例は、過去 1 時間のすべての要求を示しています。

![AI 要求の詳細](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

App Insights ポータルの機能をさらに調べるには、[Application Insights ポータルに関するドキュメント](../application-insights/app-insights-dashboards.md)をご覧ください。

### <a name="configuring-ai-with-wad"></a>WAD を使用した AI の設定

>[!NOTE]
>これは現在、Windows クラスターにのみ当てはまります。

WAD から Azure AI にデータを送信する主な方法は 2 つあります。この方法は、[この記事](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)で説明しているように、AI シンクを WAD 構成に追加することで実行できます。

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Azure ポータルでクラスターを作成するときに、AI インストルメンテーション キーを追加する

![AIKey の追加](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

クラスターを作成するときに、診断が "オン" になっている場合、Application Insights のインストルメンテーション キーを入力するオプションのフィールドが表示されます。 ここに AI IKey を貼り付けると、クラスターの展開に使用する Resource Manager テンプレートで AI シンクが自動的に構成されます。

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>Resource Manager テンプレートに AI シンクを追加する

Resource Manager テンプレートの "WadCfg" に、次の 2 つの変更を含めることによって "シンク" を追加します。

1. `DiagnosticMonitorConfiguration` の宣言の完了直後にシンク構成を追加します。

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. `DiagnosticMonitorConfiguration` にシンクを含めます。`WadCfg` の `DiagnosticMonitorConfiguration` に次の行を追加します (`EtwProviders` の宣言の直前に)。

    ```json
    "sinks": "applicationInsights"
    ```

上記の両方のコード スニペットには、シンクを記述するために "applicationInsights" という名前が使用されていました。 これは要件ではなく、シンクの名前が "sinks" に含まれている限り、任意の文字列で名前を設定できます。

現時点では、クラスターのログは AI のログ ビューアーに**トレース**として表示されます。 プラットフォームからのトレースのほとんどは "情報" レベルであるため、シンクの構成を変更して "重大" または "エラー" タイプのログのみを送信することも検討できます。 これは、[この記事](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)で説明するように、シンクに "チャネル" を追加することで実現できます。

>[!NOTE]
>ポータルまたは Resource Manager テンプレートのいずれかで間違った AI Key を使用した場合、手動でキーを変更してクラスターを更新するか、再デプロイする必要があります。

### <a name="configuring-ai-with-eventflow"></a>EventFlow を使用した AI の設定

EventFlow を使用してイベントを集計する場合は、必ず `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`NuGet パッケージをインポートしてください。 *eventFlowConfig.json* の *outputs* セクションには、次のコードが必要です。

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

フィルターに必要な変更を加えるだけでなく、他の入力を (それぞれの NuGet パッケージとともに) 含めるようにしてください。

## <a name="aisdk"></a>AI.SDK

EventFlow と WAD は集計ソリューションとして使用することをお勧めします。これは診断と監視を行うモジュール性の高いアプローチが可能になるためです。つまり、EventFlow からの出力を変更する場合、実際のインストルメンテーションを変更する必要はなく、構成ファイルを簡単に変更するだけで済みます。 ただし、Application Insights に投資することとし、別のプラットフォームに変更する可能性が低い場合、AI の新しい SDK を使用してイベントを集計し、AI に送信することを検討した方がよいでしょう。 つまり、データを AI に送信するように EventFlow を設定する必要はなくなり、代わりに ApplicationInsight の Service Fabric NuGet パッケージをインストールすることになります。 パッケージの詳細は、[こちら](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)をご覧ください。

[Application Insights のマイクロサービスとコンテナーのサポート](https://azure.microsoft.com/blog/app-insights-microservices/)により、開発中の新機能 (現在はまだベータ版) が表示され、AI を使用した、より豊かで、細かい設定が不要なモニタリング オプションを利用できます。 これには、依存関係の追跡 (クラスター内すべてのサービスやアプリケーションおよびそれらの間の通信の AppMap 構築に使用されます)、サービスからのトレースの関連付けの向上 (アプリやサービスのワークフローの問題点を特定するのに役立ちます) が含まれます。

.NET で開発中で、Service Fabric のプログラミング モデルの一部を使用する可能性があり、イベントとログ データを視覚化して分析するためのプラットフォームとして AI を使用する場合は、AI SDK ルートをたどる監視および診断ワークフローを作成することをお勧めします。 AI を使用したログの収集と表示を開始するには、[こちら](../application-insights/app-insights-asp-net-more.md)と[こちら](../application-insights/app-insights-asp-net-trace-logs.md)をご覧ください。

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Azure ポータルで AI リソースをナビゲートする

イベントとログの出力として AI を構成すると、数分後に AI リソースに情報が表示されます。 AI リソースに移動すると、AI リソース ダッシュボードに移動します。 AI タスク バーの **[検索]** をクリックすると、受信した最新のトレースが表示され、それらのトレースをフィルター処理できます。

*メトリックス エクスプローラー*は、アプリケーションやサービス、クラスターが報告するメトリックに基づいたカスタム ダッシュボードを作成する便利なツールです。 収集するデータに基づいたグラフを自分でいくつか設定するには、「[Application Insights を使用したメトリックの探索](../application-insights/app-insights-metrics-explorer.md)」を参照してください。

**[Analytics]** をクリックすると、Application Insights Analytics ポータルに移動します。このポータルでは、より多くのオプションを使用し、より広い範囲でイベントとトレースを照会できます。 この詳細については、「[Application Insights の Analytics](../application-insights/app-insights-analytics.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

* [AI のアラートを設定して](../application-insights/app-insights-alerts.md)、パフォーマンスまたは使用状況の変化について通知を受けます
* [Application Insights のスマート検出](../application-insights/app-insights-proactive-diagnostics.md)は、 AI に送信されるテレメトリのプロアクティブ分析を実行し、潜在的なパフォーマンスの問題を警告します
