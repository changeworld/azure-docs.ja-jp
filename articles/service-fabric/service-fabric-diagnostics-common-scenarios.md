---
title: Azure Service Fabric で一般的なシナリオを診断する | Microsoft Docs
description: Azure Service Fabric で一般的なシナリオのトラブルシューティングを行う方法について説明します
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
ms.date: 05/16/2018
ms.author: srrengar
ms.openlocfilehash: bd7a7e0288ced0219a0600034b273d1acba6b09b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660242"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Service Fabric で一般的なシナリオを診断する

この記事では、Service Fabric での監視と診断の領域でユーザーが遭遇した一般的なシナリオについて説明します。 紹介するシナリオは、Service Fabric の 3 つのレイヤー (アプリケーション、クラスター、インフラストラクチャ) すべてに対応しています。 各ソリューションでは、それぞれのシナリオを完了するために Application Insights、Log Analytics、Azure 監視ツールが使用されます。 各ソリューションの手順では、Service Fabric のコンテキストで Application Insights と Log Analytics を使用する方法の概要をユーザーに説明します。

## <a name="prerequisites-and-recommendations"></a>前提条件と推奨事項

この記事のソリューションでは、次のツールを使用します。 これらを設定および構成しておくことをお勧めします。

* [Service Fabric での Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* [クラスターでの Azure 診断の有効化](service-fabric-diagnostics-event-aggregation-wad.md)
* [OMS Log Analytics ワークスペースの設定](service-fabric-diagnostics-oms-setup.md)
* [パフォーマンス カウンターを追跡するための OMS エージェント](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>アプリケーションのハンドルされない例外を表示するにはどうすればよいですか

1. アプリケーションが構成されている Application Insights リソースに移動します。
2. 左上の *[検索]* をクリックします。 次に、隣のパネルで [フィルター] をクリックします。

    ![AI の概要](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. さまざまな種類のイベント (トレース、要求、カスタム イベント) が表示されます。 [例外] をフィルターとして選択します。

    ![AI のフィルター一覧](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Service Fabric Application Insights SDK を使用している場合、一覧にある例外をクリックすると、サービス コンテキストなどの詳細を確認できます。

    ![AI の例外](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>サービスで使用された HTTP 呼び出しを表示する方法を教えてください

1. 同じ Application Insights リソースで、例外ではなく "要求" でフィルターを行って、実行されたすべての要求を表示できます。
2. Service Fabric Application Insights SDK を使用している場合、互いに接続されたサービスの視覚表現と、成功/失敗した要求の数を確認できます。 左側の [アプリケーション マップ] をクリックします。

    ![AI のアプリ マップ ブレード](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![AI のアプリ マップ](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    アプリケーション マップの詳細については、[アプリケーション マップのドキュメント](../application-insights/app-insights-app-map.md)をご覧ください。

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>ノードがダウンした場合に通知されるアラートを作成する方法を教えてください

1. ノード イベントは Service Fabric クラスターによって追跡されます。 **ServiceFabric(NameofResourceGroup)** という名前の Service Fabric Analytics ソリューション リソースに移動します。
2. [概要] ブレードの下部にあるグラフをクリックします。

    ![OMS ソリューション](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. さまざまなメトリックを示す多数のグラフとタイルが表示されます。 いずれかのグラフをクリックすると、[ログ検索] に移動します。 ここで、クラスター イベントまたはパフォーマンス カウンターを照会できます。
4. 次のクエリを入力します。 これらのイベント ID は、[ノード イベントのリファレンス](service-fabric-diagnostics-event-generation-operational.md#application-events)で確認できます。

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId >= 25623 or EventId <= 25626
    ```

5. 上部の [新しいアラート ルール] をクリックします。これで、このクエリに基づいてイベントが到着した場合にいつも、選択した通信手段でアラートが届きます。

    ![OMS の新しいアラート](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>アプリケーション アップグレードのロールバックに関するアラートを受け取るには、どうすればよいですか

1. 前と同じ [ログ検索] ウィンドウで、アップグレードのロールバックに関する次のクエリを入力します。 これらのイベント ID は、[アプリケーション イベントのリファレンス](service-fabric-diagnostics-event-generation-operational.md#application-events)で確認できます。

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId == 29623 or EventId == 29624
    ```

2. 上部の [新しいアラート ルール] をクリックします。これで、このクエリに基づいてイベントが到着した場合にいつもアラートが届きます。

## <a name="how-do-i-see-container-metrics"></a>コンテナー メトリックの確認方法を教えてください

すべてのグラフと同じビューに、コンテナーのパフォーマンスに関するタイルがいくつか表示されます。 これらのタイルの作成には、OMS エージェントと[コンテナー監視ソリューション](service-fabric-diagnostics-oms-containers.md)が必要です。

![OMS のコンテナー メトリック](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>コンテナーの**内部**からテレメトリをインストルメント化するには、[コンテナー用の Application Insights NuGet パッケージ](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios)を追加する必要があります。

## <a name="how-can-i-monitor-performance-counters"></a>パフォーマンス カウンターを監視するにはどうすればよいですか

1. OMS エージェントをクラスターに追加したら、追跡したい特定のパフォーマンス カウンターを追加する必要があります。ポータルでソリューションのページから OMS ワークスペースのページに移動します。ワークスペースのタブは左側のメニューにあります。

    ![[OMS ワークスペース] タブ](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. ワークスペースのページが表示されたら、同じく左側のメニューにある [詳細設定] をクリックします。

    ![OMS の詳細設定](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. [データ]、[Windows パフォーマンス カウンター] (Linux マシンの場合は [データ]、[Linux パフォーマンス カウンター]) の順にクリックして、OMS エージェント経由でノードから特定のカウンターを収集することを開始します。 追加するカウンターの形式の例を次に示します。

    * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
    * `Processor(_Total)\\% Processor Time`
    * `Service Fabric Service(*)\\Average milliseconds per request`

    このクイック スタートでは、VotingData と VotingWeb というプロセス名が使用されます。そのため、これらのカウンターの追跡は次のようになります。

    * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
    * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

    ![OMS のパフォーマンス カウンター](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. これで、インフラストラクチャで処理されているワークロードと、リソース使用率に基づいて設定された関連アラートを確認できるようになります。 たとえば、プロセッサ使用率の合計が 90% を上回るか、または 5% 未満になったときに通知されるアラートを設定したい場合があります。 この場合に使用するカウンター名は "% Processor Time" です。 これは、次のクエリのアラート ルールを作成することで実行できます。

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Reliable Services と Reliable Actors のパフォーマンスを追跡する方法を教えてください

アプリケーションで Reliable Services または Reliable Actors のパフォーマンスを追跡するには、Service Fabric アクター、Service Fabric アクター メソッド、Service Fabric サービス、Service Fabric サービス メソッドのカウンターも追加する必要があります。 これらのカウンターは上記のシナリオと同様の方法で追加できます。以下に、OMS で追加するリライアブル サービスとアクターのパフォーマンス カウンターの例を示します。

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

[Reliable Services](service-fabric-reliable-serviceremoting-diagnostics.md) と [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) に関するパフォーマンス カウンターの完全な一覧を確認するには、それぞれのリンクをご利用ください。

## <a name="next-steps"></a>次の手順

* [AI のアラートを設定して](../application-insights/app-insights-alerts.md)、パフォーマンスまたは使用状況の変化について通知を受けます
* [Application Insights のスマート検出](../application-insights/app-insights-proactive-diagnostics.md)は、 AI に送信されるテレメトリのプロアクティブ分析を実行し、潜在的なパフォーマンスの問題を警告します
* 検出と診断に役立つ OMS Log Analytics [アラート](../log-analytics/log-analytics-alerts.md)についてさらに学習する。
* オンプレミス クラスター用に、OMS はデータを OMS に送信するために使用できるゲートウェイ (HTTP 転送プロキシ) を提供します。 詳細については、[インターネットにアクセスできないコンピューターを OMS ゲートウェイを使って OMS に接続する](../log-analytics/log-analytics-oms-gateway.md)を参照してください
* Log Analytic の一部として提供されている[ログ検索とクエリ](../log-analytics/log-analytics-log-searches.md)機能に詳しくなる
* Log Analytics および Log Analytics が提供するサービスの詳しい概要について、[Log Analytics とは何か](../operations-management-suite/operations-management-suite-overview.md)に関するページで確認する
