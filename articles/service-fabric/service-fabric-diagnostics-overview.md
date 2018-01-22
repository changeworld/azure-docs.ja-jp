---
title: "Azure Service Fabric の監視と診断の概要 | Microsoft Docs"
description: "Azure Service Fabric のクラスター、アプリケーション、およびサービスの監視と診断について説明します。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: dekapur
ms.openlocfilehash: 43a45a31efffcd623e6381049876c3607663ec4f
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Azure Service Fabric での監視と診断

この記事では、Service Fabric クラスターで実行されるアプリケーションの監視と診断を設定する概要を示します。 監視と診断は、あらゆるクラウド環境でアプリケーションやサービスを開発、テスト、およびデプロイするために非常に重要です。 監視により、アプリケーションの使用方法、リソース使用率、クラスターの全体的な正常性を追跡できます。 この情報を使用して、クラスター内の問題を診断して修正し、問題の再発を防ぐことができます。 

監視と診断の主な目標は次のとおりです。
* インフラストラクチャの問題の検出と診断
* アプリケーションの問題の検出
* リソース使用率の把握
* アプリケーション、サービス、およびインフラストラクチャのパフォーマンスの追跡

Service Fabric クラスターでは、データの監視と診断をアプリケーション、プラットフォーム (クラスター)、インフラストラクチャの 3 つのレベルで行います。 
* [アプリケーション レベル](service-fabric-diagnostics-event-generation-app.md)には、アプリケーションのパフォーマンスに関するデータと、追加されたその他のカスタム ログが含まれます。 アプリケーションの監視データは、アプリケーション自体から Application Insights (AI) に届けます。 これは、AI SDK、EventFlow、またはその他好みのパイプラインを使用して取得できます。
* [プラットフォーム レベル](service-fabric-diagnostics-event-generation-infra.md)には、クラスターやクラスター上で実行されるアプリケーションの管理に関連した、クラスターで実行されている操作のイベントが含まれます。 プラットフォーム監視データは、OMS Log Analytics に送信され、Service Fabric Analytics ソリューションを使用して受信イベントの視覚化に役立てられます。 このデータは通常、Windows または Linux Azure 診断の拡張機能を使用して、OMS Log Analytics によりアクセスされる場所からストレージ アカウントに送信されます。 
* インフラストラクチャ レベルは[パフォーマンス監視](service-fabric-diagnostics-event-generation-perf.md)に重点を置き、主要メトリックとパフォーマンス カウンターを監視して、リソースの使用率と負荷を判断します。 パフォーマンス監視は、エージェントを使用して行うことができます。OMS (Microsoft Monitoring) エージェントを使用することをお勧めします。それにより、パフォーマンス データがプラットフォーム イベントと同じ場所に配置され、クラスター全体で変更を関連付けられるため、より優れた診断エクスペリエンスが可能になります。 

![診断の概要グラフ](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="monitoring-scenarios"></a>監視シナリオ

このセクションでは、Service Fabric クラスターの監視 (アプリケーション、クラスター、パフォーマンス、正常性の監視) の主なシナリオについて説明します。 各シナリオで、監視の目的と全体的な手法について説明します。 これらの詳細や、Azure リソースにおけるその他の一般的な監視推奨事項の詳細については、[監視と診断のベスト プラクティス](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)に関する記事でご覧いただけます。 

これらのシナリオも、上記で説明した 3 つのレベルの監視および診断データに大まかに対応しています。たとえば、各シナリオをクラスター内で適切に処理するには、対応するレベルの監視および診断データを受信する必要があります。 正常性監視シナリオは例外です。なぜなら、範囲がクラスターとクラスターで実行されるすべてにわたるためです。

## <a name="application-monitoring"></a>アプリケーションの監視
アプリケーションの監視では、構築したアプリケーションの機能とコンポーネントがどのように使われているかを追跡します。 お客様は、アプリケーションを監視して、ユーザーに影響が及ぶ問題を把握したいと考えます。 アプリケーションの監視は、以下に役立ちます。
* アプリケーションの負荷やユーザー トラフィックを判断する。ユーザーのニーズを満たすために、またはアプリケーションの潜在的なボトルネックに対処するために、サービスを拡張する必要があるかどうかを判断できます。
* サービス通信とサービス リモート処理の問題をクラスター全体で特定する。
* ユーザーがアプリケーションで行っている処理を把握する。アプリケーションをインストルメント化することで、機能の開発を進め、アプリのエラーをより適切に診断できるようになります。

Service Fabric でのアプリケーション レベルの監視では、Application Insights (AI) を使用することをお勧めします。 AI と Service Fabric との統合により、Visual Studio や Azure Portal のツール エクスペリエンスを利用したり、Service Fabric サービス コンテキストを理解したり、または、AI ダッシュボードや Application Map でのリモート処理が可能となり、すぐに使える包括的なログ エクスペリエンスを実現できます。 AI を使用する場合、多くのログが自動的に作成、収集されますが、アプリケーションにカスタム ログを追加し、そのデータを AI に表示されるようにすることをお勧めします。それによって診断エクスペリエンスがさらに豊かになり、今後の問題の対処に役立ちます。 [Application Insights を使用したイベント分析](service-fabric-diagnostics-event-analysis-appinsights.md)に関する記事で、Service Fabric での AI の使用の詳細についてご覧ください。 

アプリケーションを監視するためのコードのインストルメント化を開始するには、[アプリケーション レベルのイベントとログの生成](service-fabric-diagnostics-event-generation-app.md)に関する記事をご覧ください。 

### <a name="monitoring-application-availability"></a>アプリケーションの可用性の監視
クラスター内のすべてが想定どおりに実行されているように見え、正常であるとレポートされていても、アプリケーションにアクセスできない (到達できない) ように見える場合があります。 このような事象が起こるケースは多くはありませんが、これがいつ発生するかを把握し、できるだけ早く問題を軽減できるようにすることが重要です。 可用性の監視は、システム コンポーネントの可用性の追跡に幅広く関連し、システムの全体的な "アップタイム" を把握することができます。 クラスターでは、アプリケーションの観点から可用性が重視されます。アプリケーション ライフ サイクル管理のシナリオがダウンタイムの原因とならないように、プラットフォームが調整します。 しかし、クラスター内のさまざまな問題がアプリケーションのアップタイムに影響を与える可能性があり、そのような場合、アプリケーション所有者はたいてい、それをすぐに把握したいと考えます。 お客様のその他すべてのアプリケーションと並行して、クラスターにウォッチドッグを配置することをお勧めします。 このようなウォッチドッグの目的は、アプリケーションの適切なエンドポイントを一定の時間間隔でチェックし、エンドポイントがアクセス可能であることを報告することです。 この処理は、エンドポイントに ping し、一定の時間間隔でレポートを返す外部サービスを利用して行うこともできます。

## <a name="cluster-monitoring"></a>クラスターの監視
Service Fabric クラスターの監視は、プラットフォームとそこで実行されるすべてのワークロードを想定どおりに実行するために重要です。 Service Fabric の目的の 1 つは、ハードウェア障害中もアプリケーションを実行し続けることです。 これを実現するには、プラットフォームのシステム サービスの機能を利用します。この機能は、インフラストラクチャの問題を特定し、ワークロードをクラスター内の別のノードにすばやくフェールオーバーします。 しかし、このようなケースで、もしシステム サービス自体に問題があるとしたらどうなるでしょうか。 または、ワークロードの移動でサービス配置のルールに違反したらどうなるでしょうか。 クラスターを監視すると、クラスターで発生しているアクティビティに関する最新の情報を常に把握し、それを問題の診断や修正に効果的に役立てることができます。 お客様が監視したいと考える主な事項には、次のようなものがあります。
* アプリケーションの配置やクラスター内での作業分散の観点から、Service Fabric が想定どおりに動作しているか。 
* クラスター構成の変更で行っている操作が、想定どおりに認識され、実行されているか。 これは、クラスターのスケーリング時に特に関連します。
* Service Fabric がデータとクラスター内のサービス間通信を適切に処理しているか。

Service Fabric は、稼動チャネルや、データおよびメッセージング チャネルを通して、すぐに使える包括的なイベント セットを提供します。 Windows では、これらは関連する `logLevelKeywordFilters` セットを備えた単一の ETW プロバイダーの形式で、さまざまなチャネルから選択するのに使用されます。 Linux では、すべてのプラットフォーム イベントが LTTng から提供され、1 つのテーブルに格納されます。ここで、必要に応じてイベントをフィルター処理できます。 

これらのチャネルには、選別され、構造化されたイベントが含まれ、これを使用してクラスターの状態をより詳細に把握できます。 クラスターの作成時には、既定で "診断" が有効になります。それにより、Azure Storage テーブルが設定され、後でクエリを実行できるように、そこにこれらのチャネルからのイベントが送信されます。 クラスターの監視の詳細については、「[プラットフォーム レベルのイベントとログの生成](service-fabric-diagnostics-event-generation-infra.md)」でご覧いただけます。 クラスターの監視には、OMS Log Analytics を使用されることをお勧めします。 OMS Log Analytics では、Service Fabric 固有のソリューションである Service Fabric Analytics が提供されます。Service Fabric Analytics は、Service Fabric クラスターを監視するためのカスタム ダッシュボードを備えており、そこでクラスターのイベントにクエリを実行したり、アラートを設定したりできます。 [OMS を使用したイベント分析](service-fabric-diagnostics-event-analysis-oms.md)に関する記事で、これに関する詳細をご覧ください。 

### <a name="watchdogs"></a>ウォッチドッグ
一般に、ウォッチドッグとは、複数のサービスにわたって正常性と負荷を監視し、エンドポイントに ping を送信し、クラスター内のあらゆるものの正常性を報告することができる個別のサービスを指します。 このサービスにより、1 つのサービスの観点では検出されないエラーを防ぐことができます。 またウォッチドッグは、ユーザー操作なしで修復アクションを実行するコードをホストする場所としても適しています。たとえば、特定の時間間隔でストレージ内のログ ファイル クリーンアップを行うなどです。 ウォッチドッグ サービスの実装サンプルは、[ここ](https://github.com/Azure-Samples/service-fabric-watchdog-service)で見つけることができます。

## <a name="performance-monitoring"></a>パフォーマンスの監視
基盤となるインフラストラクチャを監視することは、クラスターやリソース使用率の状態を把握する上で重要です。 システム パフォーマンスの測定はいくつかの要因に依存します。その各要因は通常、主要業績評価指標 (KPI) を使用して測定されます。 Service Fabric 関連の KPI は、パフォーマンス カウンターとして、クラスター内のノードから収集できるメトリックに対応させることができます。
これらの KPI は以下に役立ちます。
* リソースの使用率と負荷の把握。クラスターのスケーリングやサービス プロセスの最適化を目的として行います。
* インフラストラクチャの問題の予測。多くの問題では、発生前にパフォーマンスの急激な変化 (低下) があるため、ネットワーク I/O や CPU 使用率などの KPI を使用して、インフラストラクチャの問題を予測し、診断できます。

インフラストラクチャ レベルで収集されるパフォーマンス カウンターの一覧は、「[パフォーマンス メトリック](service-fabric-diagnostics-event-generation-perf.md)」でご覧いただけます。 

アプリケーション レベルのパフォーマンス監視の場合、Service Fabric は、Reliable Services および Reliable Actors プログラミング モデル向けのパフォーマンス カウンター セットを提供します。 これらのモデルのいずれかを使用している場合、アクターが上下に適切にスピンするようにしたり、Reliable Services の要求が迅速に処理されるようにしたりするのに役立つ KPI をこれらのパフォーマンス カウンターが提供します。 この詳細については、[Reliable Services のリモート処理の監視](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters)および[Reliable Actors のパフォーマンスの監視](service-fabric-reliable-actors-diagnostics.md#performance-counters)に関する記事をご覧ください。 これに加え、Application Insights にも、アプリケーションで構成されている場合に収集されるパフォーマンス メトリックのセットが用意されています。

OMS エージェントを使用して、適切なパフォーマンス カウンターを収集し、これらの KPI を OMS Log Analytics に表示できます。 Azure 診断エージェントの拡張機能 (またはその他の類似のエージェント) を使用して、分析用のメトリックを収集し、保存することもできます。 

## <a name="health-monitoring"></a>正常性の監視
Service Fabric プラットフォームには正常性モデルが組み込まれており、これがクラスター内のエンティティの状態に関する拡張可能な正常性レポートを提供します。 各ノード、アプリケーション、サービス、パーティション、レプリカ、またはインスタンスには、継続的に更新可能な正常性状態が存在します。 正常性状態は、"OK"、"警告"、"エラー" のいずれかになります。 正常性状態は、クラスター内の問題に基づいてエンティティごとに出力される正常性レポートによって変更されます。 エンティティの正常性状態は、次に示すとおり、Service Fabric Explorer (SFX) でいつでもチェックできます。また、プラットフォームの Health API を使用してクエリを実行することもできます。 独自の正常性レポートを追加するか、または Health API を使用して、正常性レポートをカスタマイズしたり、エンティティの正常性状態を変更したりすることもできます。 正常性モデルの詳細については、「[Service Fabric の正常性モニタリングの概要](service-fabric-health-introduction.md)」でご覧いただけます。

![SFX 正常性ダッシュボード](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

SFX で最新の正常性レポートを参照するのに加え、各レポートをイベントとして入手できます。 正常性イベントは稼動チャネルを介して収集可能で ([Azure 診断によるイベント集計](service-fabric-diagnostics-event-aggregation-wad.md#collect-health-and-load-events)に関する記事を参照)、後でアラートやクエリで使用できるように OMS Log Analytics に保存されます。 これは、アプリケーションの可用性に影響を与えそうな問題を検出するのに役立ちます。そのため、適切な障害シナリオにアラートを設定する (OMS を使用したカスタム アラート) ことをお勧めします。

## <a name="monitoring-workflow"></a>ワークフローの監視 

監視と診断の全体的なワークフローは、次の 3 つの手順で構成されます。

1. **イベントの生成**: これには、インフラストラクチャ、プラットフォーム (クラスター)、アプリケーション レベルのイベント (ログ、トレース、カスタム イベント) が含まれます。
2. **イベントの集計**: この段階は、実質的には、イベントを分析可能なツールにイベントを届けるためのパイプラインです。これには、Azure 診断の拡張機能や EventFlow が含まれます。
3. **分析**: 分析 (視覚化、クエリ実行、アラートなど) で使用できるように、ツールでイベントにアクセスできる必要があります。

この 3 つの領域すべてに対応する多数の製品があり、それぞれで異なるテクノロジを自由に選択できます。 各製品を連携させて、クラスターのエンド ツー エンドの監視ソリューションを実現できるようにすることが重要です。

## <a name="event-generation"></a>イベントの生成

監視および診断ワークフローの最初の手順は、イベントとログ データの作成と生成の設定です。 これらのイベント、ログ、パフォーマンス カウンターは、3 つのレベルすべてで出力されます。それは、アプリケーション レベル (クラスターにデプロイされたアプリやサービスに追加されるインストルメンテーション)、プラットフォーム レベル (Service Fabric の操作に応じてクラスターから出力されるイベント)、およびインフラストラクチャ レベル (各ノードのパフォーマンス メトリック) の 3 つです。 それらの各レベルで収集された診断データはカスタマイズ可能ですが、Service Fabric では既定のインストルメンテーションが有効です。 

提供される内容とインストルメンテーションの追加方法については、[プラットフォーム レベルのイベント](service-fabric-diagnostics-event-generation-infra.md)と[アプリケーション レベルのイベント](service-fabric-diagnostics-event-generation-app.md)に関するページを参照してください。 ここで決定しなければならない主な事項は、アプリケーションをインストルメント化する方法です。 .NET アプリケーションの場合は ILogger を使用することをお勧めしますが、EventSource や Serilog、またはその他類似のライブラリを探すこともできます。 Java の場合は、Log4j を使用することをお勧めします。 これら以外にも、アプリケーションの特性に応じて使用可能なその他のオプションがいくつか提供されています。 お客様固有のユース ケースに最適なものをお探しください。または、最も使いやすいものをお選びいただいても構いません。 

利用するログ記録プロバイダーを決定したら、ログが適切に集計され、保存されていることを確認する必要があります。

## <a name="event-aggregation"></a>イベントの集計

アプリケーションやクラスターで生成されるログとイベントの収集では、通常、[Azure 診断拡張機能](service-fabric-diagnostics-event-aggregation-wad.md) (エージェント ベースのログの収集に近い) または [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) (プロセス内ログ収集) を使用することをお勧めしています。 Application Insights を使用し、.NET または Java で開発している場合は、EventFlow ではなく Application Insights SDK を使用することをお勧めします。

多くの場合、類似のジョブを実行するには、これらのいずれか 1 つのみを使用すれば十分ですが、Azure 診断の拡張機能エージェントとプロセス内収集パイプライン (AI SDK または EventFlow) を組み合わせると、さらに信頼性の高い包括的な監視ワークフローになります。 Azure 診断の拡張機能エージェントがプラットフォーム レベルのイベントで使用されるのに対し、AI SDK や EventFlow (プロセス内収集) はアプリケーション レベルのログで使用されます。 

これらのうちの 1 つのみを使用する場合、次の重要な点に注意する必要があります。
* Azure 診断の拡張機能を使用したアプリケーション ログの収集は、ログの送信元と送信先のセットがあまり変わらず、送信元と送信先の間に単純なマッピングがある場合に、Service Fabric サービスに適したオプションです。 その理由は、Azure 診断の構成はリソース マネージャー レイヤーで行われるため、構成に大幅な変更を加えるにはクラスター全体を更新することが必要となるためです。 つまり、多くの場合、AI SDK や EventFlow を使用してアクセスするよりも効率は下がることになります。
* EventFlow を使用すると、サービスから直接、分析および視覚化プラットフォーム、またはストレージにログを送信することができます。 ほかのライブラリ (ILogger や Serilog など) を同じ目的に使うこともできますが、EventFlow はプロセス内ログ収集専用に設計されており、Service Fabric サービスをサポートするという利点があります。 これにより、構成やデプロイしやすさの面で利点が生まれ、さまざまなログ ライブラリや分析ツールをより柔軟にサポートできるようになります。 

## <a name="event-analysis"></a>イベントの分析

監視データと診断データの分析と視覚化に関しては、優れたプラットフォームが市場にいくつか存在します。 推奨するのは Service Fabric と統合可能な [OMS](service-fabric-diagnostics-event-analysis-oms.md) と、[Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) の 2 つです。 さらに、[Elastic Stack](https://www.elastic.co/products) (特に、オフライン環境でクラスターの実行を検討している場合)、[Splunk](https://www.splunk.com/)、またはその他の好みのプラットフォームを検討してもよいでしょう。 

プラットフォーム選びの重要なポイントは、ユーザー インターフェイスやクエリのオプションをどれだけ使い慣れているかということ、データを視覚化し、簡単に読み取れるダッシュボードを作成する機能や、監視を強化するための追加ツール (自動アラートなど) が含まれているということです。

選択したプラットフォームだけでなく、Service Fabric クラスターを Azure リソースとして設定する場合には、Azure のすぐに使用できるマシン用パフォーマンス監視機能も使用できます。

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) を使用して、Service Fabric クラスターの基盤となる Azure リソースの多くを監視できます。 [仮想マシン スケール セット](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)と個々の[仮想マシン](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)の一連のメトリックが自動的に収集され、Azure Portal に表示されます。 収集された情報を表示するには、Azure Portal でService Fabric クラスターが含まれているリソース グループを選択します。 次に、表示する仮想マシン スケール セットを選択します。 左側のナビゲーションの **[監視]** セクションで**メトリック**を選択すると、値のグラフが表示されます。

![収集されたメトリック情報の Azure Portal での表示](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

グラフをカスタマイズするには、[Microsoft Azure のメトリック](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)に関する記事に記載された手順に従います。 また、「[Azure Monitor での Azure サービス アラートの作成](../monitoring-and-diagnostics/monitoring-overview-alerts.md)」で説明するように、これらのメトリックに基づいてアラートを作成することもできます。 

## <a name="next-steps"></a>次の手順

* 「[プラットフォーム レベルのイベントとログの生成](service-fabric-diagnostics-event-generation-infra.md)」で、Service Fabric が提供するプラットフォームおよびイベント監視の詳細についてご覧ください。
* アプリケーションのインストルメント化の開始については、[アプリケーション レベルのイベントとログの生成](service-fabric-diagnostics-event-generation-app.md)に関する記事をご覧ください。
* 「[Service Fabric での ASP.NET Core アプリケーションの監視と診断](service-fabric-tutorial-monitoring-aspnet.md)」のチュートアルもご覧ください。

