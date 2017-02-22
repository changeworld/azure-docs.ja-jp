---
title: "Azure Service Fabric の監視と診断の概要 | Microsoft Docs"
description: "Azure、開発環境、またはオンプレミスでホストされる Microsoft Azure Service Fabric アプリケーションを監視および診断する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: ms-toddabel
manager: mfussell
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/9/2017
ms.author: toddabel
translationtype: Human Translation
ms.sourcegitcommit: 1683b52d4e5e6370a737038128f48b8ea8263038
ms.openlocfilehash: 27df2afd97107dd13a7aa2dd79343ec9425c0aed


---
# <a name="monitor-and-diagnose-azure-service-fabric-applications"></a>Azure Service Fabric アプリケーションの監視と診断

監視、検出、診断、トラブルシューティングでは、ビジネスの知見の提供、リソース使用量の監視、ハードウェアおよびソフトウェアの障害やパフォーマンスの問題の検出、サービスの動作に関する潜在的な問題の診断を行うことで、ユーザー エクスペリエンスの中断を最小限に抑えてサービスを続行することを可能にします。 監視と診断は実際にデプロイされている運用環境において不可欠ですが、実際のセットアップに移行したときに確実に機能するように、サービスの開発中に同様のモデルを採用するかどうかで、その有効性が左右されます。 Service Fabric により、サービスの開発者は、1 台のコンピューターのローカルの開発のセットアップと実際の運用クラスターのセットアップの両方でシームレスに操作できる診断を簡単に実装できます。 

監視は意味の広い言葉です。監視には、コードのインストルメント化、インストルメンテーション ログの収集、ログの分析、ログ データに基づく洞察の視覚化、ログの値と洞察に基づくアラートの作成、インフラストラクチャの監視、顧客に影響する問題をエンジニアが検出および診断できるようにすることが含まれます。 この記事では、Azure またはオンプレミスでホストされ、.NET を使用して Windows または Linux 上にデプロイされた Service Fabric クラスターの監視の概要を説明します。 まず、問題を&3; つの部分に分けましょう。
- コードまたはインフラストラクチャのインストルメント化
- 生成されたイベントの収集
- 保存、集計、視覚化、および分析

この&3; つの側面のすべてに対応する製品もありますが、多くのお客様が分野ごとに異なるテクノロジを選択しています。 各テクノロジを連携させて、アプリケーションのエンド ツー エンドの監視ソリューションを実現することが重要です。 

## <a name="monitoring-infrastructure"></a>インフラストラクチャの監視

Service Fabric を使用すると、インフラストラクチャの障害時にもアプリケーションを実行し続けることができますが、アプリケーションのオペレーターは、エラーがアプリケーションと基になるインフラストラクチャのどちらで発生しているのかを理解する必要があります。 また、インフラストラクチャを追加または削除する時期を把握するための容量計画にも、インフラストラクチャの監視が必要となります。 Service Fabric デプロイメントを構成するインフラストラクチャとアプリケーションは、どちらも監視とトラブルシューティングを行う上で重要です。 アプリケーションを使用できるのであれば、インフラストラクチャに問題があると考えられます。

### <a name="azure-monitoring"></a>Azure 監視

Azure にデプロイされたクラスターの場合、[Azure 監視](../monitoring-and-diagnostics/monitoring-overview.md)によって、Service Fabric クラスターの基盤となる Azure リソースの多くを監視できます。 [仮想マシン スケール セット (VMSS)](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) と個々の [VM](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) について、一連のメトリックが自動的に収集され、Azure Portal に表示されます。 Azure Portal でこの情報を表示するには、Service Fabric クラスターが含まれているリソース グループを選択し、表示する VMSS を選択します。 次に、監視のナビゲーション セクションでメトリックを選択すると、値のグラフが表示されます。

![収集されたメトリック情報の Azure Portal での表示](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

グラフは、「[Microsoft Azure のメトリックの概要](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)」に記載された手順に従ってカスタマイズできます。 また、「[Azure Portal での Azure サービス アラートの作成](../monitoring-and-diagnostics/insights-alerts-portal.md)」で説明するように、これらのメトリックに基づいてアラートを作成することもできます。 「Azure メトリック アラートでの webhook の構成(../monitoring-and-diagnostics/insights-webhooks-alerts.md)」で説明するように、webhook を使用してアラートを通知サービスに送信できます。 Azure 監視では、サブスクリプションを&1; つだけサポートします。 複数のサブスクリプションのサポートが必要な場合や、追加機能が必要な場合は、Operations Management Suite に含まれる [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) が、オンプレミスとクラウド ベースの両方のインフラストラクチャに対応する包括的な IT 管理ソリューションを提供します。 Azure 監視のデータを Log Analytics に直接ルーティングできるので、環境全体のメトリックとログを&1; か所で確認できます。

Operations Management Suite は、オンプレミスのインフラストラクチャを監視する方法として推奨されますが、会社でインフラストラクチャの監視に使用している既存のソリューションを使用することもできます。

### <a name="service-fabric-support-logs"></a>Service Fabric のサポート ログ

Azure Service Fabric クラスターに関する支援を得るために Microsoft サポートに連絡する必要があるときは、ほとんどの場合、サポート ログが必要となります。 クラスターが Azure 内でホストされている場合は、クラスターの作成の一環として、これらのログが自動的に構成され、収集されます。 ログは、クラスターのリソース グループにある専用のストレージ アカウントに保存されます。 このストレージ アカウントの固定名はありませんが、アカウント内に "fabric" で始まる BLOB コンテナーとテーブルがあります。 クラスターがスタンドアロン クラスターの場合は、[スタンドアロン Azure Service Fabric クラスターの作成と管理](service-fabric-cluster-creation-for-windows-server.md)に関する記事と「[スタンドアロン Windows クラスターの構成設定](service-fabric-cluster-manifest.md)」のガイダンスに従って、これらのログの収集を構成する必要があります。 スタンドアロン Service Fabric の場合、ログをローカルのファイル共有に送信する必要があります。 これらのログは、サポートを受けるために**必須**となりますが、Microsoft カスタマー サポート チーム以外の人が使用するためのものではありません。 これらは、開発者が関心を持つようなログではありません。

## <a name="instrument-your-code"></a>コードをインストルメント化する

コードのインストルメント化は、サービスの監視の他のほとんどの側面の基礎となります。 必要とされるインストルメンテーションの量に驚くことも少なくありませんが、このインストルメンテーションが、問題点を把握し、修正する必要があるものを診断するための唯一の方法であるということを考慮してください。 運用環境のサービスへのデバッガーの接続は技術的には可能ですが、通常は行わないため、詳細なインストルメンテーション データを入手することが重要です。 この大量の情報を生成するときに、すべてのイベントをローカル ノードから配布すると、コストがかかる可能性があります。 多くのサービスでは、大量のインストルメンテーション データを処理するために、次のような二部戦略を使用しています。
* すべてのイベントは、数日の短期間、ローカルのローリング ログ ファイルに保持され、デバッグに必要な場合にのみ収集されます。 一般に、コストとリソース使用率を削減するために、詳細な診断に必要なイベントはノード上に残されます。
* エラー イベント、ハート ビート イベント、パフォーマンス イベントなど、サービスの正常性を示すイベントは、中央リポジトリに送信されます。中央リポジトリでこれらのイベントを使用して、問題のあるサービスのアラートを生成できます。

コードを自動的にインストルメント化するソリューションもあります。 これらの製品が役立つこともありますが、ほとんどの場合、手動でのインストルメント化が必要となります。 最終的には、アプリケーションのフォレンジックなデバッグを実行できるだけの十分な情報が必要です。 以下のセクションでは、コードをインストルメント化するためのさまざまな方法と使用する方法の選択について説明します。

### <a name="eventsource"></a>EventSource

Visual Studio でテンプレートから Azure Service Fabric ソリューションを作成すると、EventSource 派生クラス (*ServiceEventSource* または *ActorEventSource*) が生成されます。 これにより、アプリケーションまたはサービスに適したイベントを追加できるテンプレートが提供されます。 EventSource の名前は一意である**必要があり**、"MyCompany-&lt;solution&gt;-&lt;project&gt;" という先頭の文字列から名前を変更する必要があります。 同じ名前を使用する複数の EventSource 定義があると、ランタイムに問題が発生します。 定義済みの各イベントには一意の識別子が必要です。 識別子が一意でない場合、ランタイム エラーが発生します。 多くの場合、個々の開発チーム間での競合を回避するために、識別子の値の範囲が事前に割り当てられています。 EventSource の詳細については、[Vance のブログ](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)または [MSDN ドキュメント](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx)をご覧ください。

#### <a name="using-structured-eventsource-events"></a>構造化された EventSource イベントの使用

下記のイベントは、サービスの種類の登録など、それぞれ特定のケース用に定義されています。 このようにしてメッセージが定義されていると、データをエラー テキストと共にパッケージ化できます。 これにより、指定したプロパティの名前や値に基づく適切な検索とフィルター処理が可能になります。 インストルメンテーション出力を構造化すると使いやすくなりますが、ユース ケースごとに新しいイベントを定義するためによく考える必要があり、時間がかかります。 アプリケーション全体で共有できるイベント定義もあります。たとえば、メソッド開始/停止イベントは、アプリケーション内の多くのサービスで再利用されます。 注文システムなど、ドメイン固有のサービスでは CreateOrder イベントを使用することがありますが、このイベントには独自のイベントが含まれます。 多くの場合、この方法では多数のイベントが生成されるので、プロジェクト チーム間で識別子の調整が必要になる可能性があります。 Service Fabric の構造化された EventSource の完全な例については、パーティ クラスター サンプル内の [PartyCluster.ApplicationDeployService.ServiceEventSource](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster/blob/master/src/PartyCluster.ApplicationDeployService/ServiceEventSource.cs) を参照してください。

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // Instance constructor is private to enforce singleton semantics
        private ServiceEventSource() : base() { }

        ...

        // ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event and the code implementation of the event.
        private const int ServiceTypeRegisteredEventId = 3;
        [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
        public void ServiceTypeRegistered(int hostProcessId, string serviceType)
        {
            WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
        }

        // ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event and the code implementation of the event.
        private const int ServiceHostInitializationFailedEventId = 4;
        [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
        public void ServiceHostInitializationFailed(string exception)
        {
            WriteEvent(ServiceHostInitializationFailedEventId, exception);
        }
```
#### <a name="using-eventsource-generically"></a>EventSource の汎用的な使用

特定のイベントの定義は難しい場合があるため、多くの開発者は、一般に情報を文字列として出力するパラメーターの共通セットを使用して少数のイベントを定義しています。 構造化された側面の大部分が失われるので、結果の検索とフィルター処理が困難になります。 この方法では、ログ記録レベルに通常は対応する少数のイベントを定義します。 次のスニペットでは、デバッグおよびエラー メッセージを定義しています。
```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // Instance constructor is private to enforce singleton semantics
        private ServiceEventSource() : base() { }

        ...

        private const int DebugEventId = 10;
        [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
        public void Debug(string msg)
        {
            WriteEvent(DebugEventId, msg);
        }

        private const int ErrorEventId = 11;
        [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
        public void Error(string error, string msg)
        {
            WriteEvent(ErrorEventId, error, msg);
        }
```
構造化されたインストルメンテーションと汎用のインストルメンテーションを組み合わせた方法を使用するのが適している場合もあります。 この場合、構造化されたインストルメンテーションをエラーとメトリックの報告に使用し、汎用イベントはトラブルシューティングでエンジニアが使用する詳細なログ記録に使用できます。

### <a name="aspnet-core-logging"></a>ASP.NET Core のログ記録

コードをインストルメント化する方法の選択は難しい場合があります。選択を誤り、再度インストルメント化する必要がある場合、コード ベースを再検討することになり、コード ベースの安定性が失われる可能性があります。 このリスクを軽減するために、開発者は ASP.NET Core で提供される [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/) などのインストルメンテーション ライブラリを選択できます。 このライブラリには、既存のコードへの影響を最小限に抑えながら、自分が選んだプロバイダーを使用できる [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) インターフェイスが用意されています。 このライブラリのもう&1; つの優れた点は、Windows および Linux 上の .NET Core だけでなく、完全な .NET フレームワーク内でもコードを使用できるので、.NET と .NET Core 間でインストルメンテーション コードを標準化できることです。

#### <a name="how-to-use-microsoftextensionslogging-within-service-fabric"></a>Service Fabric 内で Microsoft.Extensions.Logging を使用する方法

1. インストルメント化するプロジェクトに、**Microsoft.Extensions.Logging** NuGet パッケージを追加します。 任意のプロバイダー パッケージを追加することもできます。ここでは、サード パーティ製パッケージを後ほど追加します。 詳細については、「[Logging in ASP.NET Core (ASP.NET Core のログ記録)](https://docs.microsoft.com/aspnet/core/fundamentals/logging)」をご覧ください。

2. "Microsoft.Extensions.Logging" の **using** ディレクティブをサービス ファイルに追加します。

3. サービス クラス内でプライベート変数を定義します。

    ```csharp
        private ILogger _logger = null;
    ```

4. サービス クラスのコンストラクターに、次のコードを追加します。
    
    ```csharp
        _logger = new LoggerFactory().CreateLogger<Stateless>();
    ```

5. 独自の方法でコードのインストルメント化を開始します。 いくつかのサンプルを次に示します。
    
    ```csharp

        _logger.LogDebug("Debug level event from Microsoft.Logging");
        _logger.LogInformation("Informational level event from Microsoft.Logging");

        // In this variant, we're adding structured properties RequestName and Duration that has values MyRequest and the duration of the request.
        // More on why you'll want to do this later.
        _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);

    ```

#### <a name="using-other-logging-providers"></a>他のログ記録プロバイダーの使用

この方法で動作するサード パーティ プロバイダーもあります。例として、[SeriLog](https://serilog.net/)、[NLog](http://nlog-project.org/)、[loggr](https://github.com/imobile3/Loggr.Extensions.Logging) の&3; つが挙げられます。 これらの各プロバイダーは、ASP.Net Core のログ記録に接続することができ、個別に使用することもできます。 SeriLog はロガーから送信されるすべてのメッセージを強化できる機能を備えており、サービスの名前、種類、パーティションの情報を出力する際に役立ちます。 ASP.NET Core インフラストラクチャ内でこの機能を使用するには、次の手順を実行します。

1. **Serilog**、**Serilog.Extensions.Logging**、**Serilog.Sinks.Observable** の各 NuGet パッケージをプロジェクトに追加します。 この例では、**SeriLog.Sinks.Literate** も追加します。さらにすぐれた方法を後ほど紹介します。
2. SeriLog で LoggerConfiguration とロガー インスタンスを作成します。

    ```csharp

        Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();

    ```

3. SeriLog.ILogger 引数をサービス コンストラクターに追加し、新しく作成されたロガーを渡します。
    
    ```csharp

        ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();

    ```

4. サービス コンストラクターに次のコードを追加します。このコードでは、サービスの ServiceTypeName、ServiceName、PartitionId、InstanceId の各プロパティのプロパティ エンリッチャーを作成します。 また、コードで Microsoft.Extensions.Logging.ILogger を使用できるように、ASP.NET Core のログ ファクトリに追加します。
    
    ```csharp
        public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
            : base(context)
        {
            PropertyEnricher[] properties = new PropertyEnricher[]
            {
                new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
                new PropertyEnricher("ServiceName", context.ServiceName),
                new PropertyEnricher("PartitionId", context.PartitionId),
                new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
            };

            serilog.ForContext(properties);

            _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
        }
    ```

5. SeriLog を使用せずに ASP.NET Core を使用する場合と同様にコードをインストルメント化します。

> [!NOTE] 
> Service Fabric は&1; つのプロセス内で同じサービスの種類の複数のインスタンスをホストできるため、この方法では静的 Log.Logger を使用しないことをお勧めします。 静的 Log.Logger を使用すると、プロパティ エンリッチャーの最後のライターが、実行されているすべてのインスタンスに示される値を持つことになります。 これが、_logger 変数がサービス クラスのプライベート メンバー変数である理由の&1; つです。 これは、サービス間で使用できる共通コードで _logger を使用できるようにする必要があることも意味します。 

### <a name="which-one-should-i-use"></a>使用する方法の選択

アプリケーションでパフォーマンスが懸念される場合、EventSource は**一般に**使用するリソースが少なく、ASP.NET Core のログ記録やサード パーティ製ソリューションを使用するよりもパフォーマンスが向上するため、EventSource が最適な方法です。  多くのサービスではパフォーマンスは問題になりませんが、サービスがパフォーマンス指向の場合は、EventSource を使用することをお勧めします。 EventSource で構造化されたログ記録と同じ利点を得るには、エンジニアリング チームによる大規模な投資が必要となります。 プロジェクトのためにどれを選べばよいかを判断する最良の方法は、それぞれの方法で行うことの簡易プロトタイプを実行することです。その後で、ニーズに最適な方法を選択します。

## <a name="event-and-log-collection"></a>イベントとログの収集

### <a name="azure-diagnostics"></a>Azure 診断

Azure 監視で既に提供されているもの以外に、Azure には各サービスのイベントを中央の場所に収集する手段も用意されています。 [Windows](service-fabric-diagnostics-how-to-setup-wad.md) および [Linux](service-fabric-diagnostics-how-to-setup-lad.md) でイベント収集を構成する方法を示す&2; つの記事があります。 これらの記事では、イベント データを収集し、Azure Storage に送信する方法を説明しています。 これは、診断を有効にすることによって、ポータルまたは Resource Manager テンプレートで簡単に実行できます。 診断を有効にすると、Service Fabric で自動的に生成されるイベント ソースから収集されます。

- Reliable Actors プログラミング モデルを使用している場合は、EventSource イベントとパフォーマンス カウンター。 イベントは、「[Reliable Actors の診断とパフォーマンス監視](service-fabric-reliable-actors-diagnostics.md)」で列挙されています。
- Reliable Services プログラミング モデルを使用している場合は EventSource イベント。 イベントは、「[ステートフル Reliable Services の診断機能](service-fabric-reliable-services-diagnostics.md)」で列挙されています。
- システム イベントは ETW イベントとして出力されます。 サービス配置や開始/停止イベントなど、このカテゴリの一部として Service Fabric から出力される多数のイベントがあります。 出力されたイベントを確認する最適な方法は、ローカル コンピューターでの実行時に、[Visual Studio の診断ビューアー](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)を使用することです。 これらのイベントはネイティブ ETW イベントであるため、収集方法にいくつかの制限があります。
- Service Fabric リリース 5.4 で、正常性と負荷のメトリック イベントが公開されました。 これにより、これらのイベントの収集を履歴レポートとアラートで使用できるようになります。 これらのイベントもネイティブ ETW イベントであるため、収集方法にいくつかの制限があります。

診断が有効になっていれば、構成時にこれらのイベントは、クラスターを作成したときに作成した Azure Storage アカウントのいずれかに表示されます。 テーブル名は、*WADServiceFabricReliableActorEventTable*、*WADServiceFabricReliableServiceEventTable*、*WADServiceFabricSystemEventTable* です。 正常性イベントは既定で追加されないので、Resource Manager テンプレートを変更する必要があります。 詳細については、「[Azure 診断でログを収集する方法](service-fabric-diagnostics-how-to-setup-wad.md)」をご覧ください。

これらの記事では、カスタム イベントを Azure Storage に取得する方法も説明しています。 Azure 診断に取り込むパフォーマンス カウンターや VM の他の監視情報を構成する方法に関する Azure 診断の既存の記事は、Service Fabric クラスターにも適用できます。 たとえば、送信先として Azure Table Storage を使用しない場合は、[Event Hubs を利用してホット パスの Azure 診断データをストリーム配信する](../event-hubs/event-hubs-streaming-azure-diags-data.md)方法に関する記事があります。 イベントが Event Hubs に取り込まれたら、それらのイベントを読み取り、選択した場所に送信できます。 [Azure 診断情報を Application Insights と統合](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)する方法に関する記事もあります。

Azure 診断を使用することの欠点の&1; つは、Resource Manager テンプレートを使用して構成を行うため、構成が VMSS レベルに限定されることです。 VMSS は Service Fabric 内のノードの種類に対応します。 つまり、その種類のノードで実行される可能性のあるすべてのアプリケーションとサービス用に各ノードの種類を構成する必要があります。 そのため、構成されているアプリケーションとサービスの数によっては、多数の EventSource が使用される可能性があります。 さらに、いずれかのアプリケーションの構成が変更されるたびに、Resource Manager デプロイメントを実行する必要があります。 監視の構成はサービスの構成と共に移動するのが理想的です。

Azure 診断は、Azure にデプロイされている Service Fabric クラスターでのみ機能しますが、Windows クラスターと Linux クラスターの両方で利用できます。

### <a name="eventflow"></a>EventFlow

[Visual Studio チームによってリリースされた EventFlow](https://github.com/Azure/diagnostics-eventflows) は、ノードから&1; つ以上の監視対象にイベントをルーティングするためのメカニズムを提供します。 EventFlow は NuGet パッケージとしてサービス プロジェクトに含まれるため、EventFlow のコードと構成はサービスと共に移動します。これにより、Azure 診断で説明したノードごとの構成の問題が排除されます。 EventFlow はサービス プロセス内で実行され、構成済みの出力に直接接続されます。 この直接接続により、EventFlow はサービスの Azure、コンテナー、オンプレミスの各デプロイメントで機能します。 各 EventFlow パイプラインは外部接続を行うため、多数のレプリカを同じノードで実行するときには注意が必要です。 多数のプロセスをホストしている場合、最終的に多数の送信接続が発生することになります。 Service Fabric アプリケーションでは、ServiceType のすべてのレプリカが同じプロセス内で実行され、送信接続の数が限られるため、これはそれほど問題にはなりません。 また、EventFlow ではイベントのフィルター処理も行われるので、指定したフィルターと一致するイベントだけが送信されます。 Service Fabric で EventFlow を使用する方法の詳細については、「[Azure Service Fabric サービス プロセスからログを直接収集する](service-fabric-diagnostic-collect-logs-without-an-agent.md)」をご覧ください。

> [!NOTE]
> Service Fabric の今後のリリースでは、EventSource ホスト アプリケーションを使用できるようになり、ETW ベースの入力のリッスン、ノード レベルのメトリックの収集、ローリング ログ ファイルのサポートが可能になります。

EventFlow は非常に簡単に使用できます。
1. NuGet パッケージをサービス プロジェクトに追加します。
2. サービスの **Main** 関数内で EventFlow パイプラインを作成し、出力を構成します。 この例では、出力として SeriLog を使用する方法を示しています。
    ```csharp

        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                using (var pipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MonitoringE2E-Stateless-Pipeline"))
                {
                    Log.Logger = new LoggerConfiguration().WriteTo.EventFlow(pipeline).CreateLogger();

                    // The ServiceManifest.XML file defines one or more service type names.
                    // Registering a service maps a service type name to a .NET type.
                    // When Service Fabric creates an instance of this service type,
                    // an instance of the class is created in this host process.

                    ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless).Name);
                    
                    // Prevents this host process from terminating so services keep running.
                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }

    ```
3. サービスの PackageRoot | Config フォルダーに、*eventFlowConfig.json* という名前のファイルを作成します。 このファイル内の構成は次のようになります。
    ```json
        {
        "inputs": [
            {
            "type": "EventSource",
            "sources": [
                { "providerName": "Microsoft-ServiceFabric-Services" },
                { "providerName": "Microsoft-ServiceFabric-Actors" },
                { "providerName": "MyCompany-MonitoringE2E-Stateless" }
            ]
            },
            {
            "type": "Serilog"
            }
        ],
        "filters": [
            {
            "type": "drop",
            "include": "Level == Verbose"
            },
            {
            "type": "metadata",
            "metadata": "request",
            "requestNameProperty": "RequestName",
            "include":  "RequestName==MyRequest",
            "durationProperty": "Duration",
            "durationUnit": "milliseconds"
            }
        ],
        "outputs": [
            {
            "type": "StdOutput"
            },
            {
            "type": "ApplicationInsights",
            "instrumentationKey": "== instrumentation key here =="
            }
        ],
        "schemaVersion": "2016-08-11",
        "extensions": []
        }
    ```
この構成では、2 つの入力が定義されており、Service Fabric によって作成された&2; つの EventSource ベースのソースとサービスの EventSource があります。 ETW を使用するシステム レベルのイベントと正常性イベントは EventFlow で使用できないことに注意してください。 これは、ETW ソースをリッスンするには高レベルの権限が必要ですが、高い特権でサービスを実行してはいけないためです。 もう&1; つの入力である SeriLog は、**Main** メソッド内で構成されます。  フィルターもいくつか適用されます。最初のフィルターは、イベント レベルが verbose (詳細) であるすべてのイベントを破棄するよう EventFlow に伝えます。 他のフィルター定義については後ほど説明します。 2 つの出力も構成されています。標準出力は、Visual Studio 内の出力ウィンドウに書き込みます。 もう&1; つの出力は ApplicationInsights です。インストルメンテーション キーを必ず追加してください。

4. 最後の手順として、コードをインストルメント化します。 この例では、いくつかの方法で RunAsync をインストルメント化します。 次のコードでは、引き続き SeriLog を使用しており、使用されている構文の一部は SeriLog に固有のものです。 選択したログ記録ソリューションの特定の機能に注意してください。 ここでは&3; つのイベントが生成されます。1 つはデバッグ レベルのイベント、2 つは情報レベルのイベントです。2 つ目の情報レベルのイベントは要求の期間を追跡します。 上記の EventFlow の構成により、デバッグ レベルのイベントは出力に送信されません。

    ```csharp
        Stopwatch sw = Stopwatch.StartNew();

        while (true)
        {
            cancellationToken.ThrowIfCancellationRequested();

            sw.Restart();

            // Delay a random interval to provide a more interesting request duration.
            await Task.Delay(TimeSpan.FromMilliseconds(DateTime.Now.Millisecond), cancellationToken);

            ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);
            _logger.LogDebug("Debug level event from Microsoft.Logging");
            _logger.LogInformation("Informational level event from Microsoft.Logging");
            _logger.LogInformation("{RequestName} {Duration}", "MyRequest", sw.ElapsedMilliseconds);
        }
    ```

Application Insights でイベントを表示するには、Azure Portal を開き、ApplicationInsights リソースに移動します。 左上の [検索] をクリックすると、イベントが表示されます。

![Application Insights のイベントの [検索] ビュー](./media/service-fabric-diagnostics-overview/ai-search-events.png)

この図の下部にトレースがあります。 イベントが&2; つしかないことがわかります。デバッグ レベルのイベントは EventFlow によって破棄されています。 では、トレースの上の要求エントリとは何でしょうか。 これは、3 番目の "_logger" インストルメンテーション行であり、イベントが Application Insights 内で要求メトリックに変換されたことを示しています。 type が "metadata" のフィルター定義に戻りましょう。 この定義では、"MyRequest" という値が指定された "RequestName" プロパティを持つイベントと、"Duration" という別のプロパティに要求の期間がミリ秒単位で含まれることを宣言しています。 これが、Application Insights で要求イベントに表示されているものです。 EventSource を含め、EventFlow のサポートされているどの入力でも同じ方法を使用できます。

クラスターが、ポリシー上の理由からクラウド ベースのソリューションに接続できないスタンドアロン クラスターの場合、EventFlow は出力として Elastic Search をサポートします。ただし、他の出力を書き込むこともでき、プル要求が促進されます。 ASP.NET Core のログ記録の一部のサード パーティ プロバイダーには、オンプレミスのインストールをサポートするソリューションもあります。

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric の正常性と負荷のレポート

Service Fabric には、次の記事で詳述する独自の正常性モデルがあります。
- [Service Fabric の正常性モニタリングの概要](service-fabric-health-introduction.md)
- [サービス正常性のレポートとチェック](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Service Fabric のカスタム正常性レポートの追加](service-fabric-report-health.md)
- [Service Fabric の正常性レポートの確認](service-fabric-view-entities-aggregated-health.md)

正常性監視は、サービスの運用のさまざまな側面に不可欠です。 最も重要なのは、Service Fabric が指定されたアプリケーションのアップグレードを実行しているときに、サービスの各アップグレード ドメインがアップグレードされ、顧客が利用できるようになった後、次のアップグレード ドメインに移る前に正常性チェックに合格する必要があります。 正常性を実現できない場合、アプリケーションを既知の良好な状態にしておいて、デプロイメントがロールバックされます。 サービスがロールバックされる前に影響を受けたお客様もいましたが、ほとんどのお客様では問題は発生しませんでした。 また、人間のオペレーターによる操作を待たなくても、比較的速やかに問題が解決されました。 コードに組み込まれている正常性チェックが増えるほど、デプロイメントの問題に対するシステムの耐性が向上します。

サービスの正常性のもう&1; つの側面は、サービスのメトリックの報告です。 メトリックは、リソース使用量のバランスを取るために使用され、システムの正常性を示すインジケーターとして使用できるため、Service Fabric において重要です。 たとえば、アプリケーションに多数のサービスが含まれており、各インスタンスから&1; 秒あたりの要求数 (RPS) メトリックが報告されているとします。 サービスのいずれかが別のサービスよりも多くのリソースを使用している場合、Service Fabric はクラスター内でサービス インスタンスを移動させ、リソース使用率を均等に維持することを試みます。 このしくみの詳細については、「[Service Fabric のリソース使用量と負荷をメトリックで管理する](service-fabric-cluster-resource-manager-metrics.md)」をご覧ください。

メトリックにより、サービスの実行状況に関する知見も得られます。また、長期間にわたり、メトリックを使用して、サービスが求められているパラメーターの範囲内で動作していることを確認することもできます。 たとえば、月曜日の午前 9 時の監視時点での平均 RPS が 1000 の場合、傾向に基づいて、RPS が 500 を下回るか、1500 を超えた場合に通知する正常性レポートを設定できます。 すべてがまったく問題ない場合もありますが、顧客の優れたエクスペリエンスを確保するために、正常性レポートは一見の価値があります。 サービスでは、正常性のために報告対象とする一連のメトリックを定義できますが、クラスターのリソースのバランスに影響しないように、メトリックの重みを&0; に設定します。 どのメトリックも最初は重みを&0; にし、これがクラスターのリソースのバランスに及ぼす影響を確実に理解できるまで重みを増やさないようにすることをお勧めします。

> [!TIP]
> 重み付けされたメトリックが多すぎないように注意してください。サービス インスタンスが移動されている理由を理解しにくくなる可能性があります。少数のメトリックでも十分に役立ちます。

メトリックと正常性レポートの候補となるのは、アプリケーションの正常性とパフォーマンスを示すことができるものです。 CPU パフォーマンス カウンターではノードの使用状況がわかりますが、1 つのノードで複数のサービスが実行されている場合があるため、特定のサービスが正常かどうかを実際に示すわけではありません。 一方、RPS、処理された項目数、要求の待機時間などのメトリックは、いずれも特定のサービスの正常性を示すことができます。

正常性を報告するには、次のようなコードを追加します。
```csharp
        if (!result.HasValue)
        {
            HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
            this.Partition.ReportInstanceHealth(healthInformation);
        }
```

メトリックを報告するには、サービスに次のようなコードを追加します。
```csharp
        this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
```

## <a name="watchdogs"></a>ウォッチドッグ

ウォッチドッグは、複数のサービスにわたって正常性と負荷を監視し、正常性モデル階層内のあらゆるものの正常性を報告することができる別のサービスです。 このサービスにより、1 つのサービスの観点では検出されないエラーを防ぐことができます。 また、ウォッチドッグは、人が介入せずに既知の状況に対して修復アクションを実行できるコードをホストするのに適した場所でもあります。

## <a name="visualization-analysis-and-alerting"></a>視覚化、分析、およびアラート

監視の最後の部分は、イベント ストリームの視覚化、サービスのパフォーマンスに関するレポート、問題が検出されたときのアラートです。 監視のこの側面に使用される多くのソリューションがあります。Application Insights や OMS を使用して、イベントのストリームに基づくアラートを作成できます。 PowerBI または [Kibana](https://www.elastic.co/products/kibana) や [Splunk](https://www.splunk.com/) などのサード パーティ製ソリューションを使用して、データを視覚化できます。

## <a name="next-steps"></a>次のステップ

* [Azure 診断でログを収集する方法](service-fabric-diagnostics-how-to-setup-wad.md)
* [Azure Service Fabric サービス プロセスからログを直接収集する](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [Service Fabric のリソース使用量と負荷をメトリックで管理する](service-fabric-cluster-resource-manager-metrics.md)




<!--HONumber=Feb17_HO2-->


