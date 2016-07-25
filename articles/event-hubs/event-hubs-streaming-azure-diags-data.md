<properties
	pageTitle="Event Hubs を利用し、ホット パスの Azure 診断データをストリーム配信する | Microsoft Azure"
	description="共通シナリオのガイダンスを含む、Event Hubs で Azure 診断を構成する方法全体を説明します。"
	services="event-hubs"
	documentationCenter="na"
	authors="sethmanheim"
	manager="timlt"
	editor="" />
<tags
	ms.service="event-hubs"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="07/12/2016"
	ms.author="sethm" />

# Event Hubs を利用してホット パスの Azure 診断データをストリーム配信する

Azure 診断では柔軟な方法でクラウド サービスの仮想マシン (VM) からメトリックとログを収集し、その結果を Azure Storage に転送できます。2016 年 3 月 (SDK 2.9) の期間から、診断を完全カスタムのデータ ソースにシンクし、Azure Event Hubs を利用してホット パス データを数秒で転送できるようになりました。

サポートされているデータ型:

- Windows イベント トレーシング (ETW) イベント
- パフォーマンス カウンター
- Windows イベント ログ
- アプリケーション ログ
- Azure 診断インフラストラクチャ ログ

この記事では、Event Hubs で Azure 診断を構成する方法全体を説明します。次の一般的なシナリオ向けのガイダンスも提供しています。

- Event Hubs にシンクするログとメトリックをカスタマイズする方法
- 各環境の構成を変更する方法
- Event Hubs のストリーム データを表示する方法
- 接続のトラブルシューティング方法

## 前提条件
Azure 診断の Event Hubs シンクは、Azure SDK 2.9 以降の Cloud Services、VM、仮想マシン スケール セット、および Service Fabric と、これに対応する Azure Tools for Visual Studio でサポートされます。

- Azure 診断拡張 1.6 ([Azure SDK for .NET 2.9 以降](https://azure.microsoft.com/downloads/)では既定でこれが対象となります)
- [Visual Studio 2013 以降](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- *.wadcfgx* ファイルと次のいずれかの方法の利用によるアプリケーションの Azure 診断の既存の構成
	- Visual Studio: [Azure クラウド サービスおよび仮想マシン用の診断の構成](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
	- Windows PowerShell: [PowerShell を使用した Azure Cloud Services での診断の有効化](../cloud-services/cloud-services-diagnostics-powershell.md)
- 「[Event Hubs の使用](./event-hubs-csharp-ephcs-getstarted.md)」という記事に基づいてプロビジョニングされた Event Hubs 名前空間

## Azure 診断を Event Hubs シンクに接続する
Azure 診断では常に、ログとメトリックが既定で Azure Storage アカウントにシンクされます。*.wadcfgx* ファイルの **PublicConfig** セクションの **WadCfg** 要素に新しい **Sinks** セクションを追加することで、アプリケーションを追加で Event Hubs にシンクできます。Visual Studio の場合、*.wadcfgx* ファイルは **[クラウド サービス プロジェクト]**、**[ロール]**、**[(ロール名)]**、**diagnostics.wadcfgx** ファイルに保存されています。

	  <SinksConfig>
	    <Sink name="HotPath">
	      <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
	    </Sink>
	  </SinksConfig>

この例では、イベント ハブの URL はイベント ハブの完全修飾名前空間に設定されています (ServiceBus 名前空間 + “/” + イベント ハブ名)。

イベント ハブのURL は [Azure ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)の Event Hubs ダッシュボードに表示されます。

構成ファイルで同じ値が一貫して使用される限り、**シンク**名には任意の有効な文字列を設定できます。

>[AZURE.NOTE]  このセクションで構成される *applicationInsights* など、付加的なシンクが存在する場合があります。Azure 診断では、1 つまたは複数のシンクを定義できます。ただし、**PrivateConfig** セクションで各シンクを宣言する必要があります。

Event Hubs シンクは、*.wadcfgx* 構成ファイルの **PrivateConfig** セクションでも宣言し、定義する必要があります。

	  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <StorageAccount name="" key="" endpoint="" />
	    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="9B3SwghJOGEUvXigc6zHPInl2iYxrgsKHZoy4nm9CUI=" />
	  </PrivateConfig>

**SharedAccessKeyName** は **ServiceBus/EventHub** 名前空間に定義されている共有アクセス署名 (SAS) のキーとポリシーに一致する必要があります。[Azure ポータル](https://manage.windowsazure.com)の Event Hubs のダッシュボードに移動し、**[構成]** タブを選択して、"送信" のアクセス許可を持つ名前付きのポリシー (たとえば、"SendRule") を設定します。**StorageAccount** は **PrivateConfig** でも宣言されています。正常に動作している場合、ここでは値を変更する必要はありません。この例では、値を空のまま残しました。これはダウン ストリームの資産によって、値が設定されることを意味します。たとえば、*ServiceConfiguration.Cloud.cscfg* 環境構成ファイルによって、環境に適した名前とキーが設定されます。

>[AZURE.WARNING] イベント ハブ SAS キーは *.wadcfgx* ファイルにプレーン テキストで保存されることに注意してください。多くの場合、このキーは、ソース コード管理にチェックインされるか、ビルド サーバーの資産として利用できます。そのため、適切に保護する必要があります。ここでは "送信のみ" のアクセス許可を持つ SAS キーを使用することが推奨されます。悪意のあるユーザーはイベント ハブに書き込むことができても、リッスンしたり、操作したりすることはできません。

## Azure 診断のログとメトリックを Event Hubs とシンクするように構成する
先に説明したように、診断のあらゆる既定データとカスタム データ、つまり、メトリックとログが、設定された間隔で、Azure Storage に自動的にシンクされます。Event Hubs とあらゆる付加的シンクでは、イベント ハブとシンクするルートまたはリーフ ノードを階層から指定できます。これには、ETW イベント、パフォーマンス カウンター、Windows イベント ログ、アプリケーション ログがあります。

どのくらいのデータ ポイントを Event Hubs に実際に転送するのかを考慮することが重要です。一般的に、開発者は、直ちに利用して解釈する必要がある低待機時間ホット パス データを転送します。アラートや自動スケール規則を監視するシステムがその例です。開発者は、Azure Stream Analytics、ElasticSearch、カスタム監視システム、お気に入りのサード パーティー製監視システムなど、代替の分析ストアまたは検索ストアも構成きます。

次に構成例をいくつか示します。

        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>

次の例では、階層の親ノード **PerformanceCounters** にシンクが適用されます。つまり、すべての子 **PerformanceCounters** が Event Hubs に送信されます。

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
        </PerformanceCounters>

前の例では、Requests Queued、Requests Rejected、% Processor time という 3 つのカウンターにのみシンクが適用されています。

次の例は、このサービスの正常性のために使用される重要なメトリックになる送信データの量を開発者が制限する方法を示しています。

        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />

この例では、シンクはログに適用され、エラー レベル トレースのみを対象にフィルター処理されます。

## クラウド サービスのアプリケーションと診断構成をデプロイし、更新する

Visual Studio には、アプリケーションと Event Hubs シンク構成をデプロイするための最も簡単な方法が用意されています。ファイルを表示し、編集するには、Visual Studio で *.wadcfgx* ファイルを開き、編集して保存します。ファイルのパスは **[クラウド サービス プロジェクト]**、**[ロール]**、**[(ロール名)]**、**diagnostics.wadcfgx** です。

現時点では、Visual Studio、Visual Studio Team System、MSBuild に基づき /t:publish target を使用するすべてのコマンドまたはスクリプトのすべてのデプロイとデプロイ更新アクションで、パッケージ化プロセスに *.wadcfgx* が含まれます。さらに、デプロイと更新は、VM 上で適切な Azure 診断エージェント拡張機能を使用することで、ファイルを Azure にデプロイします。

アプリケーションと Azure 診断構成をデプロイすると、直後にイベント ハブのダッシュボードにアクティビティが表示されます。これは、選択したリスナー クライアントまたは分析ツールでホット パス データを表示できるようになったことを示します。

次の図のイベント ハブのダッシュボードでは、午後 11 時を過ぎたあたりから、診断データがイベント ハブに正常に送信されていることがわかります。更新された *.wadcfgx* ファイルと適切に構成されたシンクによってアプリケーションがデプロイされると、このように表示されます。

![][0]

>[AZURE.NOTE] Azure 診断構成ファイル (.wadcfgx) を更新するとき、Visual Studio 発行または Windows PowerShell スクリプトを利用し、アプリケーション全体と構成に更新プログラムをプッシュすることが推奨されます。

## ホット パス データの表示

先に説明したように、Event Hubs データのリッスンと処理にはさまざまな使用例があります。

1 つの簡単な手法は、イベント ハブをリッスンするスモール テスト コンソール アプリケーションを作成し、出力ストリームを印刷することです。コンソール アプリケーションに次のコードを配置できます。詳細は「[Event Hubs の使用](./event-hubs-csharp-ephcs-getstarted.md)」をご覧ください。

コンソール アプリケーションには [EventProcessor Nuget パッケージ](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/)を含める必要があるということにご注意ください。

下の **Main** 関数の山かっこの値をリソースの値で必ず置換します。

	//Console application code for EventHub test client
	using System;
	using System.Collections.Generic;
	using System.Diagnostics;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.ServiceBus.Messaging;

	namespace EventHubListener
	{
	    class SimpleEventProcessor : IEventProcessor
	    {
	        Stopwatch checkpointStopWatch;

	        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	        {
	            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
	            if (reason == CloseReason.Shutdown)
	            {
	                await context.CheckpointAsync();
	            }
	        }

	        Task IEventProcessor.OpenAsync(PartitionContext context)
	        {
	            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
	            this.checkpointStopWatch = new Stopwatch();
	            this.checkpointStopWatch.Start();
	            return Task.FromResult<object>(null);
	        }

	        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
	        {
	            foreach (EventData eventData in messages)
	            {
	                string data = Encoding.UTF8.GetString(eventData.GetBytes());

	                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
	                    context.Lease.PartitionId, data));

	                foreach (var x in eventData.Properties)
	                {
	                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
	                }
	            }

	            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
	            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
	            {
	                await context.CheckpointAsync();
	                this.checkpointStopWatch.Restart();
	            }
	        }
	    }

	    class Program
	    {
	        static void Main(string[] args)
	        {
	            string eventHubConnectionString = "Endpoint= <Your Connection String>”
	            string eventHubName = "<EventHub Name>";
	            string storageAccountName = "<Storage Account Name>";
	            string storageAccountKey = "<Storage Account Key>”;
	            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

	            string eventProcessorHostName = Guid.NewGuid().ToString();
	            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
	            Console.WriteLine("Registering EventProcessor...");
	            var options = new EventProcessorOptions();
	            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
	            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

	            Console.WriteLine("Receiving. Press enter key to stop worker.");
	            Console.ReadLine();
	            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
	        }
	    }
	}

## Event Hubs シンクのトラブルシューティング

- イベント ハブに受信または送信イベント アクティビティが予想どおり表示されません。

	イベント ハブが正しくプロビジョニングされていることを確認してください。*.wadcfgx* の **PrivateConfig** セクションのすべての接続情報がポータルに表示されるリソースの値に一致する必要があります。ポータルで定義された SAS ポリシー ("SendRule" など) が割り当てられていて、"送信" のアクセス許可が与えられていることを確認します。

- 更新後、イベント ハブで受信または送信イベント アクティビティの表示が消えます。

	最初に、イベント ハブと構成情報が前の説明に従っていることを確認します。デプロイの更新で **PrivateConfig** がリセットされることがあります。推奨される修正方法は、*.wadcfgx* の変更をすべてプロジェクトで行い、完全なアプリケーション更新をプッシュすることです。それができない場合は、SAS キーを含む完全な **PrivateConfig** が診断の更新によりプッシュされるようにします。

- 提案された内容を試しても、イベント ハブが正常に動作しません。

	ログとエラーが含まれる Azure Storage テーブル **WADDiagnosticInfrastructureLogsTable** で Azure 診断自体を探してください。選択肢の 1 つは、[Azure ストレージ エクスプローラー](http://www.storageexplorer.com) などのツールを利用してこのストレージ アカウントに接続して、このテーブルを表示し、過去 24 時間の TimeStamp のクエリを追加することです。ツールを使用して .csv ファイルをエクスポートし、Microsoft Excel などのアプリケーションで開くことができます。Excel を使用すると、"EventHubs" のようなコーリング カード文字列を簡単に検索し、報告したエラーを確認できます。

## 次のステップ
• [Event Hubs の詳細を参照します。](https://azure.microsoft.com/services/event-hubs/)

## 付録: 完全な Azure 診断構成ファイル (.wadcfgx) の例
	<?xml version="1.0" encoding="utf-8"?>
	<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <WadCfg>
	      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
	        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
	        <Directories scheduledTransferPeriod="PT1M">
	          <IISLogs containerName="wad-iis-logfiles" />
	          <FailedRequestLogs containerName="wad-failedrequestlogs" />
	        </Directories>
	        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
	          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
	        </PerformanceCounters>
	        <WindowsEventLog scheduledTransferPeriod="PT1M">
	          <DataSource name="Application!*" />
	        </WindowsEventLog>
	        <CrashDumps>
	          <CrashDumpConfiguration processName="WaIISHost.exe" />
	          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
	          <CrashDumpConfiguration processName="w3wp.exe" />
	        </CrashDumps>
	        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
	      </DiagnosticMonitorConfiguration>
	      <SinksConfig>
	        <Sink name="HotPath">
	          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
	        </Sink>
	        <Sink name="applicationInsights">
	          <ApplicationInsights />
	          <Channels>
	            <Channel logLevel="Error" name="errors" />
	          </Channels>
	        </Sink>
	      </SinksConfig>
	    </WadCfg>
	    <StorageAccount />
	  </PublicConfig>
	  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <StorageAccount name="" key="" endpoint="" />
	    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
	  </PrivateConfig>
	  <IsEnabled>true</IsEnabled>
	</DiagnosticsConfiguration>

この例の補完的な *ServiceConfiguration.Cloud.cscfg* は次のようになります。

	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
	  <Role name="MyFixIt.WorkerRole">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
	    </ConfigurationSettings>
	  </Role>
	</ServiceConfiguration>

<!-- Images. -->
[0]: ./media/event-hubs-streaming-azure-diags-data/dashboard.png

<!---HONumber=AcomDC_0713_2016-->