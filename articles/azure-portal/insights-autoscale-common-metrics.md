<properties
	pageTitle="Azure Insights: Azure Insights の自動スケールの一般的なメトリック | Microsoft Azure"
	description="Cloud Services、Virtual Machines、Web Apps の自動スケールに一般的に使用されるメトリックについて説明します。"
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Azure Insights の自動スケールの一般的なメトリック

Azure Insights の自動スケールを使用すると、テレメトリ データ (メトリック) に基づいて、実行インスタンス数を増減してスケールすることができます。このドキュメントでは、一般的なメトリックについて説明します。必要に応じて利用してください。Cloud Services とサーバー ファームの Azure ポータルで、スケールに使用するリソースのメトリックを選択できます。ただし、スケールには、さまざまなリソースのメトリックを選択できます。

ここでは、使用するスケールに適したメトリックを見つける方法について説明し、メトリックの一覧を示します。以下の説明は、仮想マシン スケール セットのスケールにも適用されます。

## コンピューティング メトリック
Azure VM v2 には、既定で、構成済みの診断拡張機能が付属しており、次のメトリックが有効に設定されています。

- [Windows VM v2 のゲスト メトリック](#compute-metrics-for-windows-vm-v2-as-a-guest-os)
- [Linux VM v2 のゲスト メトリック](#compute-metrics-for-linux-vm-v2-as-a-guest-os)

VMSS リソースに使用できるメトリックを確認するには、`Get MetricDefinitions` API/PoSH/CLI を使用します。

VM スケール セットを使用し、特定のメトリックが一覧に表示されない場合は、診断拡張機能で*無効*になっている可能性があります。

特定のメトリックが、目的の頻度でサンプリングまたは転送が行われない場合は、診断の構成を更新することができます。

上記のいずれかに該当する場合は、[Windows を実行する仮想マシンで PowerShell を使用して Azure 診断を有効にする](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)方法のページを参照し、PowerShell で Azure VM 診断拡張機能を構成してメトリックを有効に変更してください。また、この記事には、診断構成ファイルの例も紹介されています。

### ゲスト OS としての Windows VM v2 のコンピューティング メトリック

診断拡張機能を使用して Azure で新しい VM (v2) を作成すると、診断は有効です。

メトリックの一覧を生成するには、PowerShell で次のコマンドを実行します。


```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

次のメトリックのアラートを作成できます。

|メトリックの名前|	単位|
|---|---|
|\\Processor(\_Total)\\% Processor Time |Percent|
|\\Processor(\_Total)\\% Privileged Time |Percent|
|\\Processor(\_Total)\\% User Time |Percent|
|\\Processor Information(\_Total)\\Processor Frequency |Count|
|\\System\\Processes| Count|
|\\Process(\_Total)\\Thread Count| Count|
|\\Process(\_Total)\\Handle Count |Count|
|\\Memory\\% Committed Bytes In Use |Percent|
|\\Memory\\Available Bytes| Bytes|
|\\Memory\\Committed Bytes |Bytes|
|\\Memory\\Commit Limit| Bytes|
|\\Memory\\Pool Paged Bytes| Bytes|
|\\Memory\\Pool Nonpaged Bytes| Bytes|
|\\PhysicalDisk(\_Total)\\% Disk Time| Percent|
|\\PhysicalDisk(\_Total)\\% Disk Read Time| Percent|
|\\PhysicalDisk(\_Total)\\% Disk Write Time| Percent|
|\\PhysicalDisk(\_Total)\\Disk Transfers/sec |CountPerSecond|
|\\PhysicalDisk(\_Total)\\Disk Reads/sec |CountPerSecond|
|\\PhysicalDisk(\_Total)\\Disk Writes/sec |CountPerSecond|
|\\PhysicalDisk(\_Total)\\Disk Bytes/sec |BytesPerSecond|
|\\PhysicalDisk(\_Total)\\Disk Read Bytes/sec| BytesPerSecond|
|\\PhysicalDisk(\_Total)\\Disk Write Bytes/sec |BytesPerSecond|
|\\PhysicalDisk(\_Total)\\Avg.Disk Queue Length| Count|
|\\PhysicalDisk(\_Total)\\Avg.Disk Read Queue Length| Count|
|\\PhysicalDisk(\_Total)\\Avg.Disk Write Queue Length |Count|
|\\LogicalDisk(\_Total)\\% Free Space| Percent|
|\\LogicalDisk(\_Total)\\Free Megabytes| Count|



### ゲスト OS としての Linux VM v2 のコンピューティング メトリック

診断拡張機能を使用して Azure で新しい VM (v2) を作成すると、既定で診断は有効です。

メトリックの一覧を生成するには、PowerShell で次のコマンドを実行します。

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 次のメトリックのアラートを作成できます。

|メトリックの名前|	単位|
|---|---|
|\\Memory\\AvailableMemory |Bytes|
|\\Memory\\PercentAvailableMemory|	Percent|
|\\Memory\\UsedMemory|	Bytes|
|\\Memory\\PercentUsedMemory|	Percent|
|\\Memory\\PercentUsedByCache |Percent|
|\\Memory\\PagesPerSec|	CountPerSecond|
|\\Memory\\PagesReadPerSec|	CountPerSecond|
|\\Memory\\PagesWrittenPerSec |CountPerSecond|
|\\Memory\\AvailableSwap |Bytes|
|\\Memory\\PercentAvailableSwap|	Percent|
|\\Memory\\UsedSwap |Bytes|
|\\Memory\\PercentUsedSwap|	Percent|
|\\Processor\\PercentIdleTime|	Percent|
|\\Processor\\PercentUserTime|	Percent|
|\\Processor\\PercentNiceTime |Percent|
|\\Processor\\PercentPrivilegedTime |Percent|
|\\Processor\\PercentInterruptTime|	Percent|
|\\Processor\\PercentDPCTime|	Percent|
|\\Processor\\PercentProcessorTime |Percent|
|\\Processor\\PercentIOWaitTime |Percent|
|\\PhysicalDisk\\BytesPerSecond|	BytesPerSecond|
|\\PhysicalDisk\\ReadBytesPerSecond|	BytesPerSecond|
|\\PhysicalDisk\\WriteBytesPerSecond|	BytesPerSecond|
|\\PhysicalDisk\\TransfersPerSecond |CountPerSecond|
|\\PhysicalDisk\\ReadsPerSecond |CountPerSecond|
|\\PhysicalDisk\\WritesPerSecond |CountPerSecond|
|\\PhysicalDisk\\AverageReadTime|	Seconds|
|\\PhysicalDisk\\AverageWriteTime |Seconds|
|\\PhysicalDisk\\AverageTransferTime|	Seconds|
|\\PhysicalDisk\\AverageDiskQueueLength|	Count|
|\\NetworkInterface\\BytesTransmitted |Bytes|
|\\NetworkInterface\\BytesReceived |Bytes|
|\\NetworkInterface\\PacketsTransmitted |Count|
|\\NetworkInterface\\PacketsReceived |Count|
|\\NetworkInterface\\BytesTotal |Bytes|
|\\NetworkInterface\\TotalRxErrors|	Count|
|\\NetworkInterface\\TotalTxErrors|	Count|
|\\NetworkInterface\\TotalCollisions|	Count|




## 一般的に使用される Web (サーバー ファーム) メトリック

HTTP キューの長さなどの一般的な Web サーバー メトリックに基づいて、自動スケールを実行することもできます。このメトリック名は **HttpQueueLength** です。使用できるサーバー ファーム (Web Apps) メトリックで使用できる一覧については、次のセクションを参照してください。

### Web Apps のメトリック

Web Apps メトリックの一覧を生成するには、PowerShell で次のコマンドを実行します。

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

これらのメトリックに基づいてアラートしたり、スケールすることができます。

|メトリックの名前|	単位|
|---|---|
|CpuPercentage|	Percent|
|MemoryPercentage |Percent|
|DiskQueueLength|	Count|
|HttpQueueLength|	Count|
|BytesReceived|	Bytes|
|BytesSent|	Bytes|


## 一般的に使用される Storage のメトリック
Storage キューの長さ (Storage キュー内のメッセージ数) に応じてスケールすることができます。Storage キューの長さは特殊なメトリックであり、適用されるしきい値は、1 インスタンスあたりのメッセージ数です。つまり、2 つのインスタンスがあり、しきい値が 100 に設定されている場合、キュー内の合計メッセージ数が 200 になるとスケールされます。たとえば、1 インスタンスあたり 100 メッセージです。

この値は、Azure ポータルの **[設定]** ブレードで構成できます。VM スケール セットの場合、ARM テンプレートの [自動スケール] 設定で *ApproximateMessageCount* として *metricName* を使用するように更新し、*metricResourceUri* として Storage キューの ID を渡すことができます。


```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

## 一般的に使用される Service Bus のメトリック

Service Bus キューの長さ (Service Bus キュー内のメッセージ数) に応じてスケールすることができます。Service Bus キューの長さは特殊なメトリックであり、指定されるしきい値は、1 インスタンスあたりのメッセージ数です。つまり、2 つのインスタンスがあり、しきい値が 100 に設定されている場合、キュー内の合計メッセージ数が 200 になるとスケールされます。たとえば、1 インスタンスあたり 100 メッセージです。

VM スケール セットの場合、ARM テンプレートの [自動スケール] 設定で *ApproximateMessageCount* として *metricName* を使用するように更新し、*metricResourceUri* として Storage キューの ID を渡すことができます。

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

>[AZURE.NOTE] Service Bus の場合、リソース グループの概念は存在しませんが、Azure Resource Manager でリージョンごとに既定のリソース グループが作成されます。通常、リソース グループは 'Default-ServiceBus-[region]' 形式です。たとえば、'Default-ServiceBus-EastUS'、'Default-ServiceBus-WestUS'、'Default-ServiceBus-AustraliaEast' などです。

<!---HONumber=AcomDC_0330_2016------>
