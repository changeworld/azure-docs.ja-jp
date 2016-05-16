<properties
	pageTitle="PowerShell で Service Bus を管理する | Microsoft Azure"
	description="PowerShell スクリプトで Service Bus を管理する"
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="sethm"/>

# PowerShell で Service Bus を管理する

## 概要

Microsoft Azure PowerShell は、Azure のワークロードのデプロイメントと管理を制御し自動化するために使用できるスクリプティング環境です。この記事では、PowerShell を使用して、名前空間、キュー、Event Hubs などの Service Bus エンティティをローカルの Azure PowerShell コンソールを使用し、プロビジョニングして管理する方法を説明します。

## 前提条件

この記事を読み始める前に、次の項目を用意する必要があります。

- Azure サブスクリプション。Azure はサブスクリプション方式のプラットフォームです。サブスクリプションの入手方法の詳細については、[購入オプション][]、[メンバー プラン][]、または[無料評価版][]に関するページを参照してください。

- Azure PowerShell を搭載するコンピューター手順については、[Azure PowerShell のインストールおよび構成に関するページ][]を参照してください。

- PowerShell スクリプト、NuGet パッケージ、.NET Framework の一般的理解。

## Service Bus 用の .NET アセンブリへの参照を含む

Service Bus の管理で利用できる PowerShell コマンドレットの数は限定されています。既存のコマンドレットを使用しないエンティティをプロビジョニングするには、[Service Bus NuGet パッケージ]にある Service Bus 用の .NET クライアントを使用できます。

まず、このスクリプトが NuGet パッケージでインストールされる **Microsoft.ServiceBus.dll** アセンブリを見つけることができるかどうかを確認します。柔軟性を持たせるために、スクリプトでは次のステップを実行します。

1. 呼び出されたパスを決定します。
2. `packages` という名前のフォルダーが見つかるまでパスを走査します。このフォルダーは NuGet パッケージをインストールする際に作成されます。
3. **Microsoft.ServiceBus.dll** という名前のアセンブリの `packages` フォルダーを反復的に検索します。
4. アセンブリを参照するので、タイプは後で利用できるようになります。

次は、こうした手順を PowerShell スクリプトで実装する方法を示しています。

```
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## Service Bus 名前空間のプロビジョニング

次の 2 つの PowerShell コマンドレットは Service Bus 名前空間の操作をサポートします。.NET SDK API の代わりに、[Get-AzureSBNamespace][] と [New-AzureSBNamespace][] が使用できます。

この例では、スクリプトでローカル変数 `$Namespace` と `$Location` を作成します。

- `$Namespace` は操作する Service Bus 名前空間の名前です。
- `$Location` はスクリプトで名前空間をプロビジョニングするデータセンターを識別します。
- `$CurrentNamespace` はスクリプトで取得 (または作成) する参照名前空間を保存します。

実際のスクリプトでは `$Namespace` と `$Location` はパラメーターとして渡すことができます。

スクリプトのこの部分では、次を行います。

1. 指定される Service Bus の名前空間を取得しようとします。
2. 名前空間が見つかると、記述されているものを報告します。
3. 名前空間が見つからない場合、名前空間を作成し、新しく作成した名前空間を取得します。

	```
	$Namespace = "MyServiceBusNS"
	$Location = "West US"
	
	# Query to see if the namespace currently exists
	$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
	
	# Check if the namespace already exists or needs to be created
	if ($CurrentNamespace)
	{
	    Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
	}
	else
	{
	    Write-Host "The [$Namespace] namespace does not exist."
	    Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
	    New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
	    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
	    Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
	}
	```

その他の Service Bus エンティティをプロビジョニングするには、SDK から [NamespaceManager][] クラスのインスタンスを作成します。[Get-AzureSBAuthorizationRule][] コマンドレットを使用して接続文字列を指定するために使用する承認規則を取得できます。`NamespaceManager` インスタンスへの参照は `$NamespaceManager` 変数に保存されます。`$NamespaceManager` は、別のエンティティをプロビジョニングするために後ほどスクリプトで使用します。

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## その他の Service Bus エンティティのプロビジョニング

キュー、トピック、Event Hubs などのエンティティをプロビジョニングするには、[.NET API for Service Bus][] を使用します。この記事では Event Hubs にのみ注目しますが、他のエンティティの手順も似ています。さらに、その他のエンティティを含むより詳細な例は、この記事の最後をご覧ください。

スクリプトのこの部分ではさらに 4 つのローカル変数を作成します。こうした変数は、`EventHubDescription` オブジェクトのインスタンス化に使用されます。スクリプトでは次を実行します。

1. `NamespaceManager` オブジェクトを使用して、`$Path` により識別される Event Hub が存在するかどうかを最初に確認します。
2. 存在しない場合は`EventHubDescription` を作成して `NamespaceManager` クラスの `CreateEventHubIfNotExists` メソッドに渡します。
3. Event Hub が利用できるようになったことを確認したら、`ConsumerGroupDescription` と `NamespaceManager` を使いコンシューマー グループを作成します。

	```
	$Path  = "MyEventHub"
	$PartitionCount = 12
	$MessageRetentionInDays = 7
	$UserMetadata = $null
	$ConsumerGroupName = "MyConsumerGroup"
		
	# Check to see if the Event Hub already exists
	if ($NamespaceManager.EventHubExists($Path))
	{
	    Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
	}
	else
	{
	    Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
	    $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
	    $EventHubDescription.PartitionCount = $PartitionCount
	    $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
	    $EventHubDescription.UserMetadata = $UserMetadata
	    $EventHubDescription.Path = $Path
	    $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
	    Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
	}
		
	# Create the consumer group if it doesn't exist
	Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
	$ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
	$ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
	$NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
	Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
	```

## 別の Azure サブスクリプションへの名前空間の移行

次の一連のコマンドは、Azure サブスクリプション間で名前空間を移動します。この操作を実行するには、名前空間が既にアクティブである必要があります。また、PowerShell コマンドを実行するユーザーは、ソース サブスクリプションとターゲット サブスクリプションの両方の管理者である必要があります。

```
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRP' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRP' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## 次のステップ

この記事では、PowerShell を使用した Service Bus エンティティのプロビジョニングにおける基本的なアウトラインを提供します。.NET クライアント ライブラリを使用して実行できることはすべて、PowerShell スクリプトでも実行できます。

次のブログの投稿に詳しい例が掲載されています。

- [PowerShell スクリプトを使用してService Bus キュー、トピック、サブスクリプションを作成する方法 (ブログの投稿)](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [PowerShell スクリプトを使用して Service Bus の名前空間と Event Hub を作成する方法 (ブログの投稿)](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

既製のスクリプトも次のページからダウンロードできます。
- [Service Bus PowerShell Scripts](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[購入オプション]: http://azure.microsoft.com/pricing/purchase-options/
[メンバー プラン]: http://azure.microsoft.com/pricing/member-offers/
[無料評価版]: http://azure.microsoft.com/pricing/free-trial/
[Azure PowerShell のインストールおよび構成に関するページ]: ../powershell-install-configure.md
[Service Bus NuGet パッケージ]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[.NET API for Service Bus]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.aspx
[NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

<!---HONumber=AcomDC_0504_2016-->