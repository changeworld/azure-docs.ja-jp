---
title: Azure Service Fabric の監視と診断の概要 | Microsoft Docs
description: Azure Service Fabric のクラスター、アプリケーション、およびサービスの監視と診断について説明します。
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
ms.date: 04/25/2018
ms.author: dekapur;srrengar
ms.openlocfilehash: dd2446fda204f4026ac8080c658ca1aa9419f1bd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Azure Service Fabric での監視と診断

この記事では Azure Service Fabric の監視と診断の概要について説明します。 監視と診断は、あらゆるクラウド環境でアプリケーションやサービスを開発、テスト、およびデプロイするために非常に重要です。 監視により、アプリケーションの使用方法、リソース使用率、クラスターの全体的な正常性を追跡できます。 この情報を使用して、問題の診断と修正を行い、問題の再発を防ぐことができます。 

## <a name="application-monitoring"></a>アプリケーションの監視
アプリケーションの監視は、アプリケーションの機能やコンポーネントがどのように使用されているかを追跡します。 お客様は、アプリケーションを監視して、ユーザーに影響が及ぶ問題を把握したいと考えます。 アプリケーションの監視は、次のシナリオで役立ちます。
* アプリケーションの負荷やユーザー トラフィックを判断する。ユーザーのニーズを満たすために、またはアプリケーションの潜在的なボトルネックに対処するために、サービスを拡張する必要があるかどうかを判断できます。
* サービス通信とサービス リモート処理の問題をクラスター全体で特定する。
* ユーザーがアプリケーションで行っている処理を把握する。アプリケーションのテレメトリを収集することで、機能の開発を進め、アプリのエラーをより適切に診断できるようになります。
* 実行中のコンテナー内の処理を監視する

Service Fabric は、適切なトレースとテレメトリを使用してアプリケーション コードをインストルメント化するための多くのオプションをサポートしています。 Application Insights (AI) を使用することをお勧めします。 AI と Service Fabric の統合には、Visual Studio と Azure Portal のツール エクスペリエンスだけでなく、Service Fabric 固有のメトリックが含まれているため、すぐに使える包括的なロギング エクスペリエンスが実現します。 AI を使用すると、多くのログが自動的に作成され、収集されますが、アプリケーションにさらにカスタム ログを追加して、診断エクスペリエンスをさらに豊かにすることをお勧めします。 [Application Insights を使用したイベント分析](service-fabric-diagnostics-event-analysis-appinsights.md)に関する記事で、Application Insights と Service Fabric の概要を参照してください。

## <a name="platform-cluster-monitoring"></a>プラットフォーム (クラスター) の監視
Service Fabric クラスターの監視は、プラットフォームとすべてのワークロードを想定どおりに実行されていることを確認するために重要です。 Service Fabric の目標の 1 つは、ハードウェア障害に対するアプリケーションの回復力を維持することです。 この目標を実現するには、プラットフォームのシステム サービスの機能を利用します。この機能は、インフラストラクチャの問題を特定し、ワークロードをクラスター内の別のノードにすばやくフェールオーバーします。 しかし、このようなケースで、もしシステム サービス自体に問題があるとしたらどうなるでしょうか。 または、ワークロードの移動でサービス配置のルールに違反したらどうなるでしょうか。 クラスターを監視すると、クラスターで発生しているアクティビティに関する最新の情報を常に把握し、それを問題の診断や修正に効果的に役立てることができます。 お客様が監視したいと考える主な事項には、次のようなものがあります。
* アプリケーションの配置やクラスター内での作業分散の観点から、Service Fabric が想定どおりに動作しているか。 
* クラスター上で実行されたユーザー アクションは、想定どおりに承認され、実行されているか。 これは、クラスターのスケーリング時に特に関連します。
* Service Fabric がデータとクラスター内のサービス間通信を適切に処理しているか。

Service Fabric は、標準で、包括的なイベントのセットを提供します。 これらの [Service Fabric イベント](service-fabric-diagnostics-events.md)には、EventStore API または稼働チャネル (プラットフォームによって公開されたイベント チャネル) を通してアクセスできます。 
* EventStore - (バージョン 6.2 以降では Windows において利用可能で、この記事の最終更新日時点で Linux への対応はまだ作業中の) EventStore は、一連の API (REST エンドポイントを介して、または、クライアント ライブラリからアクセス可能) を介してこれらのイベントを公開しています。 EventStore に関する詳細については、[「EventStore の概要](service-fabric-diagnostics-eventstore.md)」を参照してください。
* Service Fabric イベントのチャネル - Windows では、Service Fabric イベントは、稼働およびデータのチャネルとメッセージング チャネルの選択に使用される、一連の関連する `logLevelKeywordFilters` を持つ単一の ETW プロバイダーから提供されます。これが、発信される Service Fabric イベントを必要に応じて取り出してフィルター処理する方法となっています。 Linux では、Service Fabric イベントは LTTng 経由で到着し、1 つの Storage テーブルに格納されます。ここで、必要に応じてイベントをフィルター処理できます。 これらのチャネルには、選別され、構造化されたイベントが含まれ、これを使用してクラスターの状態をより詳細に把握できます。 クラスターの作成時には、既定で診断が有効になります。それにより、Azure Storage テーブルが作成され、後でクエリを実行できるように、そこにこれらのチャネルからのイベントが送信されます。 

EventStore は、すばやい分析や、クラスターがどのように動作しているか、そして、物事が想定したとおりに起きているかどうかの概要を把握するために使用することをお勧めします。 クラスターによって生成されたログとイベントを収集するには、通常、[Azure 診断拡張機能](service-fabric-diagnostics-event-aggregation-wad.md)を使用することをお勧めします。 この機能は OMS Log Analytics の Service Fabric 固有のソリューションである Service Fabric Analytics と緊密に統合されています。Service Fabric Analytics は Service Fabric クラスターを監視するためのカスタム ダッシュボードを備えており、クラスターのイベントのクエリを実行し、アラートを設定することができます。 [OMS を使用したイベント分析](service-fabric-diagnostics-event-analysis-oms.md)に関する記事で、これに関する詳細をご覧ください。 

 クラスターの監視の詳細については、「[プラットフォーム レベルのイベントとログの生成](service-fabric-diagnostics-event-generation-infra.md)」でご覧いただけます。


 ![OMS SF ソリューション](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

## <a name="performance-monitoring"></a>パフォーマンスの監視
基盤となるインフラストラクチャを監視することは、クラスターやリソース使用率の状態を把握する上で重要です。 システム パフォーマンスの測定はいくつかの要因に依存します。その各要因は通常、主要業績評価指標 (KPI) を使用して測定されます。 Service Fabric 関連の KPI は、パフォーマンス カウンターとして、クラスター内のノードから収集できるメトリックに対応させることができます。
これらの KPI は以下に役立ちます。
* リソースの使用率と負荷の把握。クラスターのスケーリングやサービス プロセスの最適化を目的として行います。
* インフラストラクチャの問題の予測。多くの問題では、発生前にパフォーマンスの急激な変化 (低下) があるため、ネットワーク I/O や CPU 使用率などの KPI を使用して、インフラストラクチャの問題を予測し、診断できます。

インフラストラクチャ レベルで収集されるパフォーマンス カウンターの一覧は、「[パフォーマンス メトリック](service-fabric-diagnostics-event-generation-perf.md)」でご覧いただけます。 

Service Fabric は、Reliable Services および Reliable Actors プログラミング モデル向けのパフォーマンス カウンター セットを提供しています。 これらのモデルのいずれかを使用している場合、アクターが上下に適切にスピンするようにしたり、Reliable Services の要求が迅速に処理されるようにしたりするのに役立つ KPI をこれらのパフォーマンス カウンターが提供します。 詳細については、[Reliable Services のリモート処理の監視](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters)および [Reliable Actors のパフォーマンスの監視](service-fabric-reliable-actors-diagnostics.md#performance-counters)に関する記事を参照してください。 これに加え、Application Insights にも、アプリケーションで構成されている場合に収集されるパフォーマンス メトリックのセットが用意されています。

[OMS エージェント](service-fabric-diagnostics-oms-agent.md)を使用して、適切なパフォーマンス カウンターを収集し、これらの KPI を OMS Log Analytics に表示できます。

![診断の概要グラフ](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="health-monitoring"></a>正常性の監視
Service Fabric プラットフォームには正常性モデルが組み込まれており、これがクラスター内のエンティティの状態に関する拡張可能な正常性レポートを提供します。 各ノード、アプリケーション、サービス、パーティション、レプリカ、またはインスタンスには、継続的に更新可能な正常性状態が存在します。 正常性状態は、"OK"、"警告"、"エラー" のいずれかになります。 正常性状態は、クラスター内の問題に基づいてエンティティごとに出力される正常性レポートによって変更されます。 エンティティの正常性状態は、次に示すとおり、Service Fabric Explorer (SFX) でいつでもチェックできます。また、プラットフォームの Health API を使用してクエリを実行することもできます。 独自の正常性レポートを追加するか、または Health API を使用して、正常性レポートをカスタマイズしたり、エンティティの正常性状態を変更したりすることもできます。 正常性モデルの詳細については、「[Service Fabric の正常性モニタリングの概要](service-fabric-health-introduction.md)」でご覧いただけます。

![SFX 正常性ダッシュボード](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

SFX で最新の正常性レポートを参照するのに加え、各レポートをイベントとして入手できます。 正常性イベントは稼動チャネルを介して収集可能で ([Azure 診断によるイベント集計](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)に関する記事を参照)、後でアラートやクエリで使用できるように Log Analytics に保存されます。 これは、アプリケーションの可用性に影響を与えそうな問題を検出するのに役立ちます。そのため、適切な障害シナリオにアラート (Log Analytics を使用したカスタム アラート) を設定することをお勧めします。

## <a name="other-logging-solutions"></a>その他のログ記録ソリューション

[OMS](service-fabric-diagnostics-event-analysis-oms.md) と [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) が Service Fabric との統合で構築されている 2 つのソリューションをお勧めしましたが、多くのイベントは etw プロバイダーを介して出力されるので、他のログ記録ソリューションを使用して拡張できます。 さらに、[Elastic Stack](https://www.elastic.co/products) (特に、オフライン環境でクラスターの実行を検討している場合)、[Splunk](https://www.splunk.com/)、[Dynatrace](https://www.dynatrace.com/)、またはその他の好みのプラットフォームを検討してもよいでしょう。 

プラットフォーム選びの重要なポイントは、ユーザー インターフェイスやクエリのオプションをどれだけ使い慣れているかということ、データを視覚化し、簡単に読み取れるダッシュボードを作成する機能や、監視を強化するための追加ツール (自動アラートなど) が含まれているということです。

## <a name="next-steps"></a>次の手順

* アプリケーションのインストルメント化の開始については、[アプリケーション レベルのイベントとログの生成](service-fabric-diagnostics-event-generation-app.md)に関するページを参照してください。
* Service Fabric が提供するプラットフォームおよびイベント監視の詳細については、[プラットフォーム レベルのイベントとログの生成](service-fabric-diagnostics-event-generation-infra.md)に関するページを参照してください。
* アプリケーションの AI を設定する手順をについては、「[Service Fabric の ASP.NET Core アプリケーションを監視および診断する](service-fabric-tutorial-monitoring-aspnet.md)」を参照してください。
* コンテナーを監視するために OMS Log Analytics を設定する方法については、[Azure Service Fabric での Windows コンテナーの監視と診断](service-fabric-tutorial-monitoring-wincontainers.md)に関するページを参照してください。
* Azure リソースの一般的な監視の推奨事項については、[監視と診断のベスト プラクティス](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)に関するページを参照してください。 
