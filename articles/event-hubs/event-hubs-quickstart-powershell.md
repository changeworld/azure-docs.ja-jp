---
title: PowerShell を使用したイベント ハブの作成 - Azure Event Hubs | Microsoft Docs
description: このクイック スタートでは、Azure PowerShell を使用してイベント ハブを作成したうえで、.NET Standard SDK を使用してイベントの送受信を行う方法について説明します。
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d91f98f4f54c1b7c46b3390427c6c389ec01d3c9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088172"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>クイック スタート: Azure PowerShell を使用したイベント ハブの作成

Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 Event Hubs の詳しい概要については、[Event Hubs の概要](event-hubs-about.md)と [Event Hubs の機能](event-hubs-features.md)に関するページをご覧ください。

このクイック スタートでは、Azure PowerShell を使用してイベント ハブを作成します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のものが必要です。

- Azure のサブスクリプション。 お持ちでない場合は、開始する前に[無料アカウントを作成][]してください。
- [Visual Studio 2017 Update 3 (バージョン 15.3, 26730.01)](https://www.visualstudio.com/vs) 以降。
- [.NET Standard SDK](https://www.microsoft.com/net/download/windows) バージョン 2.0 以降。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

ローカルで PowerShell を使用している場合、このクイック スタートを完了するには、最新バージョンの PowerShell を実行する必要があります。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

リソース グループは Azure リソースの論理的なコレクションであり、イベント ハブを作成するにはリソース グループが必要です。 

次の例では、米国東部リージョンにリソース グループを作成します。 `myResourceGroup` は、使用したいリソース グループの名前に置き換えます。

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します

リソース グループが作成されたら、そのリソース グループ内に Event Hubs 名前空間を作成します。 Event Hubs 名前空間によって、イベント ハブを作成できる一意の完全修飾ドメイン名が提供されます。 `namespace_name` は、名前空間の一意の名前に置き換えます。

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>イベント ハブの作成

Event Hubs 名前空間ができたところで、その名前空間内にイベント ハブを作成します。  
`MessageRetentionInDays` に許可されている期間は、1 - 7 日間です。

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

お疲れさまでした。 Azure PowerShell を使用して Event Hubs 名前空間を作成し、その名前空間内にイベント ハブを作成しました。 

## <a name="next-steps"></a>次の手順

この記事では、Event Hubs 名前空間を作成し、サンプル アプリケーションを使用してイベント ハブからイベントを送受信しました。 イベント ハブに対してイベントを送信または受信するためのステップ バイ ステップの手順については、次のチュートリアルをご覧ください。 

- **イベントをイベント ハブに送信する**: [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)、[Java](event-hubs-java-get-started-send.md)、[Python](event-hubs-python-get-started-send.md)、[Node.js](event-hubs-node-get-started-send.md)、[Go](event-hubs-go-get-started-send.md)、[C](event-hubs-c-getstarted-send.md)
- **イベント ハブからイベントを受信する**: [.NET Core](event-hubs-dotnet-standard-getstarted-receive-eph.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md)、[Java](event-hubs-java-get-started-receive-eph.md)、[Python](event-hubs-python-get-started-receive.md)、[Node.js](event-hubs-node-get-started-receive.md)、[Go](event-hubs-go-get-started-receive-eph.md)、[Apache Storm](event-hubs-storm-getstarted-receive.md)

[無料アカウントを作成]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
