---
title: Log Analytics を使用した Azure Service Fabric イベント分析 | Microsoft Docs
description: Azure Service Fabric クラスターの監視と診断に Log Analytics を使用したイベントの視覚化と分析について説明します。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/29/2018
ms.author: srrengar
ms.openlocfilehash: 49d9b5306a0fcf51cc0de036c725fca8345cd0ec
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2018
ms.locfileid: "36302184"
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>Log Analytics を使用したイベントの分析と視覚化
Log Analytics では、クラウド内でホストされているアプリケーションとサービスからテレメトリが収集および分析され、可用性とパフォーマンスを最大限にできるように分析ツールが提供されます。 この記事では、Log Analytics でクエリを実行して洞察を取得する方法と、クラスター内の処理をトラブルシューティングする方法について説明します。 次のような一般的な質問に対応します。

* 正常性イベントをどのようにトラブルシューティングすればいいか。
* ノードがダウンするタイミングを知るにはどうすればいいか。
* 自分のアプリケーションのサービスが起動または停止されたかを知るにはどうすればいいか。

## <a name="log-analytics-workspace"></a>Log Analytics ワークスペース

Log Analytics は、Azure ストレージ テーブルまたはエージェントなどの管理対象リソースからデータを収集し、中央レポジトリ内で保守します。 それらのデータは分析、アラート、および視覚化、さらにエクスポートに使用できます。 Log Analytics は、イベント、パフォーマンス データ、またはその他のカスタム データをサポートします。 [診断の拡張機能を設定してイベントを集計する手順](service-fabric-diagnostics-event-aggregation-wad.md)および [Log Analytics ワークスペースを作成してストレージ内のイベントから読み取りを行う手順](service-fabric-diagnostics-oms-setup.md)を確認して、データが確実に Log Analytics へ配信されるようにしてください。

Log Analytics がデータを受け取った後、Azure にはいくつかの*管理ソリューション*が用意されています。これらは受け取るデータを監視するために事前にパッケージ化されたソリューションで、いくつかのシナリオに合わせてカスタマイズされています。 *Service Fabric Analytics* ソリューションと*コンテナー* ソリューションが含まれており、これらは Service Fabric クラスターを使用する際の診断と監視に最も関連性の高いソリューションです。 この記事では、ワークスペースを使って作成される、Service Fabric Analytics ソリューションを使用する方法について説明します。

## <a name="access-the-service-fabric-analytics-solution"></a>Service Fabric Analytics ソリューションへのアクセス

1. Azure Portal で Service Fabric Analytics ソリューションを作成したリソース グループに移動します。

2. リソースの [**ServiceFabric\<nameOfOMSWorkspace\>**] を選択します。

2. 概要には、有効なソリューションごとのグラフ形式のタイルが表示されます (Service Fabric のタイルも含まれています)。 **Service Fabric** グラフ (下にある最初の画像) をクリックして、Service Fabric Analytics ソリューション (下にある 2 つ目の画像) に進みます。

    ![Service Fabric ソリューション](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

    ![Service Fabric ソリューション](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

上の画像は、Service Fabric Analytics ソリューションのホーム ページです。 これは、クラスター内で行われる処理のスナップショット ビューです。 クラスター作成時に診断を有効にした場合は、以下に対するイベントが表示されます。 

* [操作チャネル](service-fabric-diagnostics-event-generation-operational.md): Service Fabric プラットフォーム (システム サービスのコレクション) が実行するより高いレベルの操作。
* [Reliable Actors プログラミング モデル イベント](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services プログラミング モデル イベント](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>[診断の拡張機能の構成を更新する](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)ことで、操作チャネルだけでなく、より詳細なシステム イベントが収集できます。

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>ノードでの操作を含む Service Fabric Events を表示する

1. Service Fabric Analytics ページで、**Service Fabric Events** グラフをクリックします。

    ![Service Fabric ソリューションの操作チャネル](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

2. 一覧内のイベントを表示するには、**[一覧]** をクリックします。 ここで、収集済みのすべてのシステム イベントが表示されます。 参照用として、下図のシステム イベントは Azure Storage アカウントの WADServiceFabricSystemEventsTable に由来し、次に確認する Reliable Service イベントや Reliable Actor イベントも同様に、それぞれのテーブルに由来します。
    
    ![クエリの操作チャネル](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

または、左側にある虫眼鏡をクリックし、Kusto クエリ言語を使って検索対象を見つけることもできます。 たとえば、クラスター内のノードに対して行われたすべての操作を検索するは、次のクエリを使用できます。 使用されているイベント ID は、[操作チャネルのイベント リファレンス](service-fabric-diagnostics-event-generation-operational.md)で確認できます。

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

特定のノード (Computer) やシステム サービス (TaskName) など、より多くのフィールドに対してクエリを実行できます。

### <a name="view-service-fabric-reliable-service-and-actor-events"></a>Service Fabric Reliable Service および Actor イベントの表示

1. Service Fabric Analytics ページで、**Reliable Services** グラフをクリックします。

    ![Service Fabric ソリューションの Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

2. 一覧内のイベントを表示するには、**[一覧]** をクリックします。 ここで、Reliable Services のイベントを確認できます。 通常はデプロイおよびアップグレードで行われるサービス runasync の開始時点および完了時点に関する、さまざまなイベントを表示できます。 

    ![クエリの Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Reliable Actor のイベントも、同様の方式で表示できます。 Reliable Actor により詳細なイベントを設定するには、診断の拡張機能の構成 (以下に示します) で `scheduledTransferKeywordFilter` を変更する必要があります。 これらの値の詳細については、「[Reliable Actors の診断とパフォーマンス監視](service-fabric-reliable-actors-diagnostics.md#keywords)」を参照してください。

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

Kusto クエリ言語は優れています。 実行可能なもう 1 つの重要なクエリでは、どのノードで大部分のイベントが生成されているかを検出します。 次のスクリーン ショットのクエリは、特定のサービスとノードで集計された Service Fabric の操作イベントを示しています。

![クエリのノードごとのイベント](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>次の手順

* インフラストラクチャの監視、つまりパフォーマンス カウンターを有効にするために、[Log Analytics エージェントの追加](service-fabric-diagnostics-oms-agent.md)に関するページにアクセスしてください。 エージェントによって、パフォーマンス カウンターが収集され、既存のワークスペースに追加されます。
* オンプレミス クラスター用に、Log Analytics ではデータを Log Analytics に送信するために使用できるゲートウェイ (HTTP 転送プロキシ) を提供します。 詳細については、「[インターネットにアクセスできないコンピューターを OMS ゲートウェイを使って接続する](../log-analytics/log-analytics-oms-gateway.md)」を参照してください
* 検出と診断に役立つ[自動アラート](../log-analytics/log-analytics-alerts.md)を構成する
* Log Analytic の一部として提供されている[ログ検索とクエリ](../log-analytics/log-analytics-log-searches.md)機能に詳しくなる
* Log Analytics および Log Analytics が提供するサービスの詳しい概要について、[Log Analytics とは何か](../operations-management-suite/operations-management-suite-overview.md)に関するページで確認する
