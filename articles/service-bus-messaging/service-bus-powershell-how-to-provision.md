---
title: "PowerShell で Azure Service Bus を管理する | Microsoft Docs"
description: "PowerShell スクリプトで Service Bus を管理する"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: aff8e2ce-bc8b-489f-aca9-a18782be0375
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 61f31c8ad0463776937f366d145595f04cc42d2e
ms.openlocfilehash: 24dd8757942488aa8364cc6cf968cfc17299699f


---
# <a name="manage-service-bus-with-powershell"></a>PowerShell で Service Bus を管理する
## <a name="overview"></a>概要
Microsoft Azure PowerShell は、Azure のワークロードのデプロイメントと管理を制御し自動化するために使用できるスクリプティング環境です。 この記事では、PowerShell を使用して、名前空間、キュー、Event Hubs などの Service Bus エンティティをローカルの Azure PowerShell コンソールを使用し、プロビジョニングして管理する方法を説明します。

## <a name="prerequisites"></a>前提条件
この記事に記載されていることを開始する前に、以下の前提条件を満たしている必要があります。

* Azure サブスクリプション。 Azure はサブスクリプション方式のプラットフォームです。 サブスクリプションの入手方法の詳細については、[購入オプション][Purchase Options]、[メンバー プラン][Member Offers]、または[無料試用版][Free Trial]に関するページをご覧ください。
* Azure PowerShell を搭載するコンピューター 手順については、[Azure PowerShell のインストールと構成][Install and configure Azure PowerShell]に関するページをご覧ください。
* PowerShell スクリプト、NuGet パッケージ、.NET Framework の一般的理解。

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Service Bus 用の .NET アセンブリへの参照を含む
Service Bus の管理で利用できる PowerShell コマンドレットの数は限定されています。 既存のコマンドレットを使用して公開されていないエンティティをプロビジョニングするには、[Service Bus NuGet パッケージ][Service Bus NuGet package]にある Service Bus 用の .NET クライアントを使用します。

まず、このスクリプトが NuGet パッケージでインストールされる **Microsoft.ServiceBus.dll** アセンブリを見つけることができるかどうかを確認します。 柔軟性を持たせるために、スクリプトでは次のステップを実行します。

1. 呼び出されたパスを決定します。
2. `packages`という名前のフォルダーが見つかるまでパスを走査します。 このフォルダーは NuGet パッケージをインストールする際に作成されます。
3. **Microsoft.ServiceBus.dll** という名前のアセンブリの `packages` フォルダーを反復的に検索します。
4. アセンブリを参照するので、タイプは後で利用できるようになります。

次は、こうした手順を PowerShell スクリプトで実装する方法を示しています。

```powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Host "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Host "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error "Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script."
}
```

## <a name="provision-a-service-bus-namespace"></a>Service Bus 名前空間のプロビジョニング
次の&2; つの PowerShell コマンドレットは Service Bus 名前空間の操作をサポートします。 .NET SDK API の代わりに、[Get-AzureSBNamespace][Get-AzureSBNamespace] と [New-AzureSBNamespace][New-AzureSBNamespace] を使用できます。

この例では、スクリプトでローカル変数 `$Namespace` と `$Location` を作成します。

* `$Namespace` は操作する Service Bus 名前空間の名前です。
* `$Location` はスクリプトで名前空間をプロビジョニングするデータセンターを識別します。
* `$CurrentNamespace` はスクリプトで取得 (または作成) する参照名前空間を保存します。

実際のスクリプトでは `$Namespace` と `$Location` はパラメーターとして渡すことができます。

スクリプトのこの部分では、次を行います。

1. 指定される Service Bus の名前空間を取得しようとします。
2. 名前空間が見つかると、記述されているものを報告します。
3. 名前空間が見つからない場合、名前空間を作成し、新しく作成した名前空間を取得します。
   
    ```powershell
    $Namespace = "MyServiceBusNS"
    $Location = "West US"
   
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
    }
    else
    {
        Write-Host "The [$Namespace] namespace does not exist."
        Write-Host "Creating the [$Namespace] namespace in the [$Location] region..."
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```

その他の Service Bus エンティティをプロビジョニングするには、SDK から [NamespaceManager][NamespaceManager] クラスのインスタンスを作成します。
[Get-AzureSBAuthorizationRule][Get-AzureSBAuthorizationRule] コマンドレットを使用して、接続文字列を指定するために使用する承認規則を取得できます。 `NamespaceManager` インスタンスへの参照は `$NamespaceManager` 変数に保存されます。 `$NamespaceManager` は、別のエンティティをプロビジョニングするために後ほどスクリプトで使用します。

```powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Host "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Host "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-other-service-bus-entities"></a>その他の Service Bus エンティティのプロビジョニング
キュー、トピック、Event Hubs などのエンティティをプロビジョニングするには、[.NET API for Service Bus][.NET API for Service Bus] を使用できます。 この記事では Event Hubs にのみ注目しますが、他のエンティティの手順も似ています。 さらに、その他のエンティティを含むより詳細な例は、この記事の最後をご覧ください。

スクリプトのこの部分ではさらに&4; つのローカル変数を作成します。 こうした変数は、 `EventHubDescription` オブジェクトのインスタンス化に使用されます。 このスクリプトは、次を実行します。

1. `NamespaceManager` オブジェクトを使用して、`$Path` により識別される Event Hub が存在するかどうかを最初に確認します。
2. 存在しない場合は`EventHubDescription` を作成して `NamespaceManager` クラスの `CreateEventHubIfNotExists` メソッドに渡します。
3. Event Hub が利用できるようになったことを確認したら、`ConsumerGroupDescription` と `NamespaceManager` を使いコンシューマー グループを作成します。
   
    ```powershell
    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"
   
    # Check to see if the Event Hub already exists
    if ($NamespaceManager.EventHubExists($Path))
    {
        Write-Host "The [$Path] event hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Host "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
        $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
        $EventHubDescription.PartitionCount = $PartitionCount
        $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
        $EventHubDescription.UserMetadata = $UserMetadata
        $EventHubDescription.Path = $Path
        $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription)
        Write-Host "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
    }
   
    # Create the consumer group if it doesn't exist
    Write-Host "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
    $ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
    $ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
    $NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription)
    Write-Host "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
    ```

## <a name="migrate-a-namespace-to-another-azure-subscription"></a>別の Azure サブスクリプションへの名前空間の移行
次の一連のコマンドでは、別の Azure サブスクリプションへ名前空間を移行します。 この操作を実行するには、名前空間がアクティブになっており、PowerShell コマンドを実行するユーザーが、ソースとターゲットのサブスクリプションの管理者である必要があります。

```powershell
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>次のステップ
この記事では、PowerShell を使用した Service Bus エンティティのプロビジョニングにおける基本的なアウトラインを提供します。 .NET クライアント ライブラリを使用して実行できることはすべて、PowerShell スクリプトでも実行できます。

次のブログの投稿に詳しい例が掲載されています。

* [PowerShell スクリプトを使用してService Bus キュー、トピック、サブスクリプションを作成する方法 (ブログの投稿)](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [PowerShell スクリプトを使用して Service Bus の名前空間と Event Hub を作成する方法 (ブログの投稿)](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

既製のスクリプトも次のページからダウンロードできます。

* [Service Bus PowerShell Scripts](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[Purchase Options]: http://azure.microsoft.com/pricing/purchase-options/
[Member Offers]: http://azure.microsoft.com/pricing/member-offers/
[Free Trial]: http://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Service Bus NuGet package]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/servicemanagement/azure.compute/v1.6.1/Get-AzureSBNamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/servicemanagement/azure.compute/v1.6.1/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/servicemanagement/azure.compute/v1.6.1/get-azuresbauthorizationrule
[NamespaceManager]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager



<!--HONumber=Jan17_HO2-->


