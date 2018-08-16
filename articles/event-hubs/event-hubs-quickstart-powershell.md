---
title: Azure クイック スタート - PowerShell を使用したイベント ストリームの処理 | Microsoft Docs
description: このクイック スタートでは、PowerShell とサンプル .NET アプリケーションを使用して Azure Event Hubs イベントを送受信する方法について説明します。
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/26/2018
ms.author: shvija
ms.openlocfilehash: fd2b8b2e8bd075e029a07519ced186424798fd9c
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003612"
---
# <a name="quickstart-process-event-streams-using-powershell-and-net-standard"></a>クイック スタート: PowerShell と .NET Standard を使用したイベント ストリームの処理

Azure Event Hubs は高度にスケーラブルなデータ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるインジェスト サービスでもあります。 このクイック スタートでは、Azure PowerShell を使用してイベント ハブを作成したうえで、.NET Standard SDK を使用してイベント ハブとの間で送受信を行う方法について説明します。

このクイック スタートを完了するには、Azure サブスクリプションが必要です。 お持ちでない場合は、開始する前に[無料アカウントを作成][]してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のものが必要です。

- [Visual Studio 2017 Update 3 (バージョン 15.3, 26730.01)](http://www.visualstudio.com/vs) 以降。
- [.NET Standard SDK](https://www.microsoft.com/net/download/windows) バージョン 2.0 以降。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

ローカルで PowerShell を使用している場合、このクイック スタートを完了するには、最新バージョンの PowerShell を実行する必要があります。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0)に関するページを参照してください。

## <a name="provision-resources"></a>リソースをプロビジョニングする

### <a name="create-a-resource-group"></a>リソース グループの作成

リソース グループは Azure リソースの論理的なコレクションであり、イベント ハブを作成するにはリソース グループが必要です。 

次の例では、米国東部リージョンにリソース グループを作成します。 `myResourceGroup` は、使用したいリソース グループの名前に置き換えます。

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

### <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します

リソース グループが作成されたら、そのリソース グループ内に Event Hubs 名前空間を作成します。 Event Hubs 名前空間によって、イベント ハブを作成できる一意の完全修飾ドメイン名が提供されます。 `namespace_name` は、名前空間の一意の名前に置き換えます。

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

### <a name="create-an-event-hub"></a>イベント ハブの作成

Event Hubs 名前空間ができたところで、その名前空間内にイベント ハブを作成します。

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name
```

### <a name="create-a-storage-account-for-event-processor-host"></a>イベント プロセッサ ホストのストレージ アカウントの作成

イベント プロセッサ ホストは、チェックポイントと並列レシーバーの管理によって Event Hubs からのイベントの受信を簡素化します。 チェックポイント処理を実行するために、イベント プロセッサ ホストではストレージ アカウントが必要です。 ストレージ アカウントを作成してそのキーを取得するには、次のコマンドを実行します。

```azurepowershell-interactive
# Create a standard general purpose storage account 
New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name storage_account_name -Location eastus -SkuName Standard_LRS 
e
# Retrieve the storage account key for accessing it
Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name storage_account_name
```

### <a name="get-the-connection-string"></a>接続文字列を取得する

イベント ハブに接続してイベントを処理するには、接続文字列が必要です。 接続文字列を取得するには、次を実行します。

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Name RootManageSharedAccessKey
```

## <a name="stream-into-event-hubs"></a>Event Hubs へのストリーム

これで、イベント ハブへのストリーム配信を開始できます。 [Event Hubs リポジトリ](https://github.com/Azure/azure-event-hubs)からサンプルをダウンロードまたは git clone できます。

### <a name="ingest-events"></a>イベントの取り込み

ストリーミング イベントを開始するには、GitHub から [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) をダウンロードするか、次のコマンドを発行して [Event Hubs GitHub リポジトリ](https://github.com/Azure/azure-event-hubs)を複製します。

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

\azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleSender フォルダーに移動して、SampleSender.sln ファイルを Visual Studio に読み込みます。

次に、[Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet パッケージをプロジェクトに追加します。

Program.cs ファイルで、次のプレースホルダーを実際のイベント ハブ名と接続文字列に置き換えます。

```C#
private const string EhConnectionString = "Event Hubs connection string";
private const string EhEntityPath = "Event Hub name";

```

次に、サンプルをビルドして実行します。 イベントがイベント ハブに取り込まれるのを確認できます。

![][3]

### <a name="receive-and-process-events"></a>イベントの受信と処理

次に、イベント プロセッサ ホスト レシーバーのサンプルをダウンロードします。これが、先ほど送信したメッセージを受信します。 GitHub から [SampleEphReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) をダウンロードするか、次のコマンドを発行して [Event Hubs GitHub リポジトリ](https://github.com/Azure/azure-event-hubs)を複製します。

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

\azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleEphReceiver フォルダーに移動して、SampleEphReceiver.sln ソリューション ファイルを Visual Studio に読み込みます。

次に、[Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet パッケージと [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) NuGet パッケージをプロジェクトに追加します。

Program.cs ファイルで、次の定数を対応する実際の値に置き換えます。

```C#
private const string EventHubConnectionString = "Event Hubs connection string";
private const string EventHubName = "Event Hub name";
private const string StorageContainerName = "Storage account container name";
private const string StorageAccountName = "Storage account name";
private const string StorageAccountKey = "Storage account key";
```

次に、サンプルをビルドして実行します。 サンプル アプリケーションによってイベントが受信されるのを確認できます。

![][4]

Azure portal では、次に示すように、特定の Event Hubs 名前空間でイベントが処理されている速度を表示できます。

![][5]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートを完了したら、リソース グループのほか、その中の名前空間、ストレージ アカウント、イベント ハブを削除できます。 `myResourceGroup` は、作成したリソース グループの名前に置き換えます。 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次の手順

この記事では、イベント ハブからイベントを送受信するために必要な Event Hubs 名前空間などのリソースを作成しました。 さらに学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Event Hubs データ ストリームでデータの異常を視覚化する](event-hubs-tutorial-visualize-anomalies.md)

[無料アカウントを作成]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
