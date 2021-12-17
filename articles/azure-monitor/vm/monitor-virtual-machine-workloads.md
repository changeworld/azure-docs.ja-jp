---
title: 'Azure Monitor を使用して仮想マシンを監視する: ワークロード'
description: Azure Monitor で仮想マシンのゲストのワークロードを監視する方法について説明します。
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 64ef920e727baf559b71cac416404fb740e47625
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788555"
---
# <a name="monitor-virtual-machines-with-azure-monitor-workloads"></a>Azure Monitor を使用して仮想マシンを監視する: ワークロード
この記事は、シナリオ「[Azure Monitor で仮想マシンとそのワークロードを監視する](monitor-virtual-machine.md)」の一部です。 仮想マシンのゲスト オペレーティング システムで実行されているワークロードを監視する方法について説明します。 この記事には、仮想マシン上のさまざまなデータ ソースの分析とアラートの詳細が含まれています。

## <a name="configure-additional-data-collection"></a>追加のデータ収集を構成する
VM 分析情報では、有効になっているマシンのゲスト オペレーティング システムからパフォーマンス データのみが収集されます。 Log Analytics ワークスペースを構成することにより、エージェントから追加のパフォーマンス データ、イベント、およびその他の監視データの収集を有効にすることができます。 ワークスペースに接続するエージェントによって自動的に構成がダウンロードされ、定義されたデータの収集がすぐに開始されるため、これは 1 回だけ構成されます。

使用可能なデータ ソースの一覧と構成方法の詳細については、「[Azure Monitor のエージェント データ ソース](../agents/agent-data-sources.md)」を参照してください。

> [!NOTE]
> さまざまなマシンのデータ収集を選択的に構成することはできません。 ワークスペースに接続されているすべてのマシンで、そのワークスペースの構成が使用されます。

> [!IMPORTANT]
> 必要なデータのみを収集する場合は注意してください。 ワークスペースで収集されたすべてのデータにコストが関連付けられます。 収集するデータでは、特定の分析とアラートのシナリオのみがサポートされる必要があります。

## <a name="convert-management-pack-logic"></a>管理パックのロジックを変換する
現在、Azure Monitor を実装しているかなりの数のお客様が、System Center Operations Manager の管理パックを使用して仮想マシンのワークロードを監視しています。 プラットフォームが根本的に異なるため、Operations Manager から Azure Monitor にアセットを変換する移行ツールはありません。 代わりに、Operations Manager の使用を継続しながら、移行によって標準的な Azure Monitor 実装を構成します。 Azure Monitor をカスタマイズして、さまざまなアプリケーションやコンポーネントの要件を満たす場合や、より多くの機能が追加された場合は、Operations Manager で、さまざまな管理パックおよびエージェントの、インベントリからの削除を開始できます。

管理パックの機能全体をレプリケートするのではなく、管理パックによって提供される重要な監視を分析します。 前のセクションで説明した方法を使用して、それらの監視要件をレプリケートできるかどうかを判断します。 多くの場合、特定の管理パックを廃止することができる十分な機能をレプリケートするデータ収集とアラート ルールを Azure Monitor で構成できます。 管理パックには、多くの場合、数百から数千のルールとモニターが含まれていることがあります。

ほとんどのシナリオでは、Operations Manager によってデータ収集とアラート条件が同じルールまたはモニターに結合されます。 Azure Monitor で、すべてのアラート シナリオのデータ収集とアラート ルールを構成する必要があります。

1 つの戦略は、環境内でアラートがトリガーされたモニターとルールに焦点を当てることです。 **アラート** や **最も頻度の高いアラート** などの [Operations Manager で使用できる既存のレポート](/system-center/scom/manage-reports-installed-during-setup)を参照してください。これは、時間の経過とともにアラートを識別するために役立ちます。 Operations データベースで次のクエリを実行して、最新の最も頻度の高いアラートを評価することもできます。

```sql
select AlertName, COUNT(AlertName) as 'Total Alerts' from
Alert.vAlertResolutionState ars
inner join Alert.vAlertDetail adt on ars.AlertGuid = adt.AlertGuid
inner join Alert.vAlert alt on ars.AlertGuid = alt.AlertGuid
group by AlertName
order by 'Total Alerts' DESC
```

出力を評価して、移行する特定のアラートを識別します。 調整されたアラート、または問題があることがわかっているアラートは無視します。 管理パックを確認して、発生したことがない重要なアラートを識別します。

## <a name="windows-or-syslog-event"></a>Windows イベントまたは Syslog イベント
この一般的な監視シナリオでは、オペレーティング システムとアプリケーションによって、Windows イベントまたは Syslog に書き込まれます。 1 つのイベントが見つかったらすぐにアラートを作成します。 または、特定の時間枠内で一致する一連のイベントを待機することもできます。

これらのイベントを収集するには、[Windows イベント](../agents/data-sources-windows-events.md)または [Syslog イベント](../agents/data-sources-windows-events.md)を収集するように Log Analytics ワークスペースを構成します。 ワークスペースでのこのデータの取り込みと保有にはコストがかかります。

Windows イベントは Log Analytics ワークスペースの[イベント](/azure/azure-monitor/reference/tables/event) テーブルに格納され、Syslog イベントは [Syslog](/azure/azure-monitor/reference/tables/syslog) テーブルに格納されます。

### <a name="sample-log-queries"></a>サンプル ログ クエリ

- **コンピューターのイベント ログとイベントの種類別にイベントの数をカウントします。**

    ```kusto
    Event
    | summarize count() by Computer, EventLog, EventLevelName
    | sort by Computer, EventLog, EventLevelName
    ```

- **コンピューターのイベント ログとイベント ID 別にイベントの数をカウントします。**
    
    ```kusto
    Event
    | summarize count() by Computer, EventLog, EventLevelName
    | sort by Computer, EventLog, EventLevelName
    ```

### <a name="sample-alert-rules"></a>サンプルのアラート ルール
次の例では、特定の Windows イベントが作成されたときにアラートが作成されます。 メトリック測定アラート ルールを使用して、コンピューターごとに個別のアラートが作成されます。

- **特定の Windows イベントに対するアラート ルールを作成します。**

   この例では、アプリケーション ログのイベントを表示します。 しきい値として 0 および連続する違反が 0 より大きいことを指定します。

    ```kusto
    Event 
    | where EventLog == "Application"
    | where EventID == 123 
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **特定の重大度の Syslog イベントに対するアラート ルールを作成します。**

   次の例では、エラー認可イベントを表示します。 しきい値として 0 および連続する違反が 0 より大きいことを指定します。

    ```kusto
    Syslog
    | where Facility == "auth"
    | where SeverityLevel == "err"
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

## <a name="custom-performance-counters"></a>カスタム パフォーマンス カウンター
VM 分析情報によって収集されない、アプリケーションまたはゲスト オペレーティング システムによって作成されたパフォーマンス カウンターが必要な場合があります。 この[パフォーマンス データ](../agents/data-sources-windows-events.md)を収集するように Log Analytics ワークスペースを構成します。 ワークスペースでのこのデータの取り込みと保有にはコストがかかります。 VM 分析情報によって既に収集されているパフォーマンス データを収集しないように注意してください。

ワークスペースによって構成されたパフォーマンス データは、[Perf](/azure/azure-monitor/reference/tables/perf) テーブルに格納されます。 このテーブルの構造は、VM 分析情報で使用される [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) テーブルとは異なります。

### <a name="sample-log-queries"></a>サンプル ログ クエリ

カスタム パフォーマンス カウンターを使用するログ クエリの例については、「[パフォーマンス レコードを使用してクエリをログに記録する](../agents/data-sources-performance-counters.md#log-queries-with-performance-records)」を参照してください。

### <a name="sample-alerts"></a>サンプルのアラート

- **カウンターの最大値に関するアラートを作成します。**
    
    ```kusto
    Perf 
    | where CounterName == "My Counter" 
    | summarize AggregatedValue = max(CounterValue) by Computer
    ```

- **カウンターの平均値に関するアラートを作成します。**

    ```kusto
    Perf 
    | where CounterName == "My Counter" 
    | summarize AggregatedValue = avg(CounterValue) by Computer
    ```

## <a name="text-logs"></a>テキスト ログ
一部のアプリケーションでは、仮想マシンに格納されているテキスト ログに書き込まれるイベントが書き込まれます。 Log Analytics ワークスペースで[カスタム ログ](../agents/data-sources-custom-logs.md)を定義して、これらのイベントを収集します。 テキスト ログの場所とその詳細な構成を定義します。 ワークスペースでのこのデータの取り込みと保有にはコストがかかります。

テキスト ログからのイベントは、**MyTable_CL** のような名前のテーブルに格納されます。 ログを構成するときに、ログの名前と構造を定義します。

### <a name="sample-log-queries"></a>サンプル ログ クエリ
ここで使用する列名は、例として示しているだけです。 特定のログを定義するときに、そのログの列名を定義します。 ほとんどの場合、ご使用のログの列名は異なります。

- **コード別にイベントの数をカウントします。**
    
    ```kusto
    MyApp_CL
    | summarize count() by code
    ```

### <a name="sample-alert-rule"></a>サンプルのアラート ルール

- **エラー イベントに対するアラート ルールを作成します。**
    
    ```kusto
    MyApp_CL
    | where status == "Error"
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```
## <a name="iis-logs"></a>IIS ログ
Windows マシンで実行される IIS では、ログがテキスト ファイルに書き込まれます。 [IIS ログ](../agents/data-sources-iis-logs.md)を収集するように Log Analytics ワークスペースを構成します。 ワークスペースでのこのデータの取り込みと保有にはコストがかかります。

IIS ログのレコードは、Log Analytics ワークスペースの [W3CIISLog](/azure/azure-monitor/reference/tables/w3ciislog) テーブルに格納されます。

### <a name="sample-log-queries"></a>サンプル ログ クエリ

- **www.contoso.com というホストの URL 別の IIS ログ エントリの数。**
    
    ```kusto
    W3CIISLog 
    | where csHost=="www.contoso.com" 
    | summarize count() by csUriStem
    ```

- **各 IIS マシンによって受信された合計バイト数を確認します。**

    ```kusto
    W3CIISLog 
    | summarize sum(csBytes) by Computer
    ```

### <a name="sample-alert-rule"></a>サンプルのアラート ルール

- **リターン状態が 500 のレコードに対するアラート ルールを作成します。**
    
    ```kusto
    W3CIISLog 
    | where scStatus==500
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

## <a name="service-or-daemon"></a>サービスまたはデーモン
Windows サービスまたは Linux デーモンの状態を監視するには、[Azure Automation](../../automation/automation-intro.md) で[変更履歴とインベントリ](../../automation/change-tracking/overview.md) ソリューションを有効にします。 Azure Monitor には、サービスまたはデーモンの状態を監視する機能はありません。 Windows イベント ログでイベントを検索するなど、使用できる方法はいくつかありますが、この方法は信頼性がありません。 [VMProcess](/azure/azure-monitor/reference/tables/vmprocess) テーブルから、マシンで実行されているサービスに関連付けられているプロセスを検索することもできます。 このテーブルは 1 時間ごとに更新されるだけなので、通常はアラートには十分ではありません。

> [!NOTE]
> 変更履歴と分析ソリューションは、VM 分析情報の[変更分析](vminsights-change-analysis.md)機能とは異なります。 この機能はパブリック プレビューの段階であり、このシナリオにはまだ含まれていません。

仮想マシンで Change Tracking ソリューションを有効にするためのさまざまなオプションについては、「[Change Tracking と Inventory の有効化](../../automation/change-tracking/overview.md#enable-change-tracking-and-inventory)」を参照してください。 このソリューションには、仮想マシンを大規模に構成する方法が含まれています。 このソリューションをサポートするには、[Azure Automation アカウントを作成する](../../automation/quickstarts/create-account-portal.md)必要があります。

変更履歴とインベントリを有効にすると、Log Analytics ワークスペースに 2 つの新しいテーブルが作成されます。 ログ クエリのアラート ルールには、次のテーブルを使用します。

| テーブル | 説明 |
|:---|:---|
| [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationdata) | ゲスト内の構成データへの変更 |
| [ConfigurationData](/azure/azure-monitor/reference/tables/configurationdata) | ゲスト内の構成データについて最後に報告された状態 |


### <a name="sample-log-queries"></a>サンプル ログ クエリ

- **最近開始されたすべてのサービスとデーモンを一覧表示します。**
    
    ```kusto
    ConfigurationChange
    | where ConfigChangeType == "Daemons" or ConfigChangeType == "WindowsServices"
    | where SvcState == "Running"
    | sort by Computer, SvcName
    ```

### <a name="alert-rule-samples"></a>アラート ルールのサンプル

- **特定のサービスが停止されたタイミングに基づくアラート ルールを作成します。**

    
    ```kusto
    ConfigurationData
    | where SvcName == "W3SVC" 
    | where SvcState == "Stopped"
    | where ConfigDataType == "WindowsServices"
    | where SvcStartupType == "Auto"
    | summarize AggregatedValue = count() by Computer, SvcName, SvcDisplayName, SvcState, bin(TimeGenerated, 15m)
    ```

- **一連のサービスのいずれかが停止されたタイミングに基づくアラート ルールを作成します。**
    
    ```kusto
    let services = dynamic(["omskd","cshost","schedule","wuauserv","heathservice","efs","wsusservice","SrmSvc","CertSvc","wmsvc","vpxd","winmgmt","netman","smsexec","w3svc","sms_site_vss_writer","ccmexe","spooler","eventsystem","netlogon","kdc","ntds","lsmserv","gpsvc","dns","dfsr","dfs","dhcp","DNSCache","dmserver","messenger","w32time","plugplay","rpcss","lanmanserver","lmhosts","eventlog","lanmanworkstation","wnirm","mpssvc","dhcpserver","VSS","ClusSvc","MSExchangeTransport","MSExchangeIS"]);
    ConfigurationData
    | where ConfigDataType == "WindowsServices"
    | where SvcStartupType == "Auto"
    | where SvcName in (services)
    | where SvcState == "Stopped"
    | project TimeGenerated, Computer, SvcName, SvcDisplayName, SvcState
    | summarize AggregatedValue = count() by Computer, SvcName, SvcDisplayName, SvcState, bin(TimeGenerated, 15m)
    ```

## <a name="port-monitoring"></a>ポートの監視
ポートの監視では、マシンで特定のポートがリッスンされていることを確認します。 ここでは、ポート監視のために考えられる 2 つの戦略について説明します。

### <a name="dependency-agent-tables"></a>Dependency Agent テーブル
[VMConnection](/azure/azure-monitor/reference/tables/vmconnection) と [VMBoundPort](/azure/azure-monitor/reference/tables/vmboundport) を使用して、マシン上の接続とポートを分析します。 VMBoundPort テーブルは、コンピューター上で実行されている各プロセスとリッスンしているポートで 1 分ごとに更新されます。 ハートビートなしアラートに似たログ クエリ アラートを作成して、停止したプロセスを見つけたり、マシンが特定のポートでリッスンしていないときにアラートを生成したりすることができます。

### <a name="sample-log-queries"></a>サンプル ログ クエリ

- **VM で開いているポートの数を確認します。これは、構成とセキュリティの脆弱性がある VM を評価するために役立ちます。**

    ```kusto
    VMBoundPort
    | where Ip != "127.0.0.1"
    | summarize by Computer, Machine, Port, Protocol
    | summarize OpenPorts=count() by Computer, Machine
    | order by OpenPorts desc
    ```

- **VM 上のバインドされたポートを一覧表示します。これは、構成とセキュリティの脆弱性がある VM を評価するために役立ちます。**

    ```kusto
    VMBoundPort
    | distinct Computer, Port, ProcessName
    ```


- **アプリケーションまたはサービスがどのように構成されているかを判断するために、ポート別にネットワーク アクティビティを分析します。**

    ```kusto
    VMBoundPort
    | where Ip != "127.0.0.1"
    | summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
    | project-away TimeGenerated
    | order by Machine, Computer, Port, Ip, ProcessName
    ```

- **VM で送受信されたバイト数の傾向を確認します。**

    ```kusto
    VMConnection
    | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer
    | order by Computer desc
    | render timechart
    ```

- **時間の経過に伴う接続エラーを使用して、障害率が安定している (または変化している) かどうかを判断します。**

    ```kusto
    VMConnection
    | where Computer == <replace this with a computer name, e.g. ‘acme-demo’>
    | extend bythehour = datetime_part("hour", TimeGenerated)
    | project bythehour, LinksFailed
    | summarize failCount = count() by bythehour
    | sort by bythehour asc
    | render timechart
    ```

- **リンクの状態の傾向を調べて、マシンの動作と接続状態を分析します。**

    ```kusto
    VMConnection
    | where Computer == <replace this with a computer name, e.g. ‘acme-demo’>
    | summarize  dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h)
    | render timechart
    ```

### <a name="connection-manager"></a>接続マネージャー
[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) の[接続モニター](../../network-watcher/connection-monitor-overview.md)機能は、仮想マシン上のポートへの接続をテストするために使用されます。 テストでは、マシンでポートがリッスンされていて、ネットワーク上でアクセスできることを確認します。
接続マネージャーを使用するには、テストを開始するクライアント マシンに Network Watcher 拡張機能が必要となります。 テスト対象のマシンにインストールする必要はありません。 詳細については、「[チュートリアル - Azure portal を使用してネットワーク通信を監視する](../../network-watcher/connection-monitor.md)」を参照してください。

接続マネージャーには追加料金が発生します。 詳細については、「[Network Watcher の価格](https://azure.microsoft.com/pricing/details/network-watcher/)」を参照してください。

## <a name="run-a-process-on-a-local-machine"></a>ローカル コンピューターでプロセスを実行する
一部のワークロードを監視するには、ローカル プロセスが必要です。 たとえば、ローカル コンピューター上で実行され、アプリケーションに接続してデータを収集または処理する PowerShell スクリプトがあります。 [Azure Automation](../../automation/automation-intro.md) の一部である [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) を使用して、ローカルの PowerShell スクリプトを実行できます。 Hybrid Runbook Worker に対しては直接課金されませんが、使用される Runbook ごとにコストが発生します。

Runbook では、ローカル コンピューター上の任意のリソースにアクセスして、必要なデータを収集できます。 Azure Monitor にデータを直接送信したり、アラートを作成したりすることはできません。 アラートを作成するには、Runbook でカスタム ログにエントリを書き込み、Azure Monitor によってそのログが収集されるように構成します。 そのログ エントリに対して実行されるログ クエリのアラート ルールを作成します。

## <a name="synthetic-transactions"></a>代理トランザクション
代理トランザクションでは、マシン上で実行されているアプリケーションまたはサービスに接続して、ユーザー接続や実際のユーザー トラフィックをシミュレートします。 アプリケーションが使用可能な場合は、そのマシンが正常に実行されていると見なすことができます。 Azure Monitor の [Application Insights](../app/app-insights-overview.md) によって、この機能が提供されます。 これは、インターネットからアクセスできるアプリケーションに対してのみ機能します。 内部アプリケーションの場合、テストを実行する特定の Microsoft URL からのアクセスを許可するように、ファイアウォールを開く必要があります。 または、System Center Operations Manager などの代替の監視ソリューションを使用することができます。

|方法 | 説明 |
|:---|:---|
| [URL テスト](../app/monitor-web-app-availability.md) | HTTP が使用可能であること、および Web ページが返されることを確認します |
| [複数ステップのテスト](../app/availability-multistep.md) | ユーザー セッションをシミュレートします |

## <a name="sql-server"></a>SQL Server

[SQL Insights](../insights/sql-insights-overview.md) を使用して、仮想マシン上で実行されている SQL Server を監視します。

## <a name="next-steps"></a>次のステップ

* [ログ クエリを使用して Azure Monitor ログのデータを分析する方法について学習する](../logs/get-started-queries.md)
* [Azure Monitor のメトリックとログを使用したアラートについて学習する](../alerts/alerts-overview.md)