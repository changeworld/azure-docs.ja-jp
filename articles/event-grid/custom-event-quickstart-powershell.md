---
title: "PowerShell を使用した Azure Event Grid のカスタム イベント | Microsoft Docs"
description: "Azure Event Grid と PowerShell を使用して、トピックを発行したり、そのイベントをサブスクライブしたりします。"
services: event-grid
keywords: 
author: tfitzmac
ms.author: tomfitz
ms.date: 01/30/2018
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: 2d8fc892a91f0dfd4ba7a5c8561bcb222bf81965
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="create-and-route-custom-events-with-azure-powershell-and-event-grid"></a>Azure PowerShell と Event Grid を使用したカスタム イベントの作成とルーティング

Azure Event Grid は、クラウドのイベント処理サービスです。 この記事では、Azure PowerShell を使用してカスタム トピックを作成してそのトピックをサブスクライブし、イベントをトリガーして結果を表示します。 通常、webhook や Azure Functions など、イベントに応答するエンドポイントが、イベントの送信先になります。 ただし、この記事では、単純化するために、メッセージをただ収集するだけの URL に対してイベントを送信します。 この URL は、サードパーティ ツール ([RequestBin](https://requestb.in/) または [Hookbin](https://hookbin.com/)) を使用して作成します。

>[!NOTE]
>**RequestBin** と **Hookbin** は、高スループットの使用を目的にしていません。 これらのツールの使用は、あくまでデモンストレーションが目的です。 一度に複数のイベントをプッシュすると、一部のイベントがツールから見えない場合があります。

最後に、イベント データがエンドポイントに送信されたことを確認します。

![イベント データ](./media/custom-event-quickstart-powershell/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

この記事では、Azure PowerShell の最新バージョンを実行していることを前提にしています。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

Event Grid のトピックは Azure リソースであり、Azure リソース グループに配置する必要があります。 リソース グループは、Azure リソースをまとめてデプロイして管理するための論理上のコレクションです。

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドでリソース グループを作成します。

次の例では、*gridResourceGroup* という名前のリソース グループを *westus2* の場所に作成します。

```powershell
New-AzureRmResourceGroup -Name gridResourceGroup -Location westus2
```

## <a name="create-a-custom-topic"></a>カスタム トピックの作成

トピックは、イベントの送信先となるユーザー定義のエンドポイントになります。 次の例では、リソース グループにトピックを作成します。 `<topic_name>` は、トピックの一意の名前に置き換えてください。 トピック名は、DNS エントリによって表されるため、一意である必要があります。

```powershell
New-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Location westus2 -Name <topic_name>
```

## <a name="create-a-message-endpoint"></a>メッセージ エンドポイントの作成

トピックをサブスクライブする前に、イベント メッセージ用のエンドポイントを作成しましょう。 ここではイベントに応答するコードを作成する代わりに、皆さんが確認できるように、メッセージを収集するエンドポイントを作成します。 RequestBin と Hookbin は、エンドポイントを作成し、そこに送信された要求を表示できるサードパーティ ツールです。 [RequestBin](https://requestb.in/) に移動して **[Create a RequestBin]\(RequestBin の作成\)** をクリックします。または、[Hookbin](https://hookbin.com/) に移動して **[Create New Endpoint]\(新しいエンドポイントの作成\)** をクリックします。  Bin URL をコピーしてください。トピックをサブスクライブするときにこの URL が必要になります。

## <a name="subscribe-to-a-topic"></a>トピックのサブスクライブ

どのイベントを追跡するかは、トピックをサブスクライブすることによって Event Grid に伝えます。次の例では、作成したトピックをサブスクライブし、RequestBin または Hookbin からの URL をイベント通知のエンドポイントとして渡しています。 `<event_subscription_name>` は、実際のサブスクリプションの一意の名前に、`<endpoint_URL>` は、前のセクションで得た値に置き換えてください。 サブスクライブ時にエンドポイントを指定することによって、そのエンドポイントに対するイベントのルーティングが Event Grid によって行われます。 `<topic_name>` には、先ほど作成した値を使用します。

```powershell
New-AzureRmEventGridSubscription -EventSubscriptionName <event_subscription_name> -Endpoint <endpoint_URL> -ResourceGroupName gridResourceGroup -TopicName <topic_name>
```

## <a name="send-an-event-to-your-topic"></a>トピックへのイベントの送信

では、イベントをトリガーして、Event Grid がメッセージをエンドポイントに配信するようすを見てみましょう。 まず、トピックの URL とキーを取得します。 `<topic_name>` には、先ほどと同じトピック名を使用してください。

```powershell
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName gridResourceGroup -Name <topic-name>
```

この記事では、単純化するために、トピックに送信するサンプル イベント データを設定します。 通常はイベント データをアプリケーションまたは Azure サービスから送信することになります。 次の例では、イベント データを取得しています。

```powershell
$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$body = "[{`"id`": `"$eventID`",`"eventType`": `"recordInserted`",`"subject`": `"myapp/vehicles/motorcycles`",`"eventTime`": `"$eventDate`",`"data`":{`"make`": `"Ducati`",`"model`": `"Monster`"},`"dataVersion`": `"1.0`"}]"
```

`$body` を見ると、イベント全体を理解できます。 JSON の `data` 要素がイベントのペイロードです。 このフィールドには、適切な形式の JSON であればどのようなものでも格納することができます。 また、高度なルーティングやフィルタリングを行う場合には、subject フィールドを使用することもできます。

次に、イベントをトピックに送信します。

```powershell
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

以上でイベントがトリガーされ、そのメッセージが、Event Grid によってサブスクライブ時に構成したエンドポイントに送信されました。 先ほど作成したエンドポイント URL にブラウザーでアクセスします。 または、開いているブラウザーで、最新の情報に更新するボタンをクリックしてください。 先ほど送信したイベントが表示されます。

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2018-01-25T15:58:13",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

引き続きこのイベントを使用する場合は、この記事で作成したリソースをクリーンアップしないでください。 引き続き使用する予定がない場合は、次のコマンドを使用して、この記事で作成したリソースを削除します。

```powershell
Remove-AzureRmResourceGroup -Name gridResourceGroup
```

## <a name="next-steps"></a>次の手順

トピックを作成し、イベントをサブスクライブする方法がわかったら、Event Grid でできることについて、さらに情報を収集しましょう。

- [Event Grid について](overview.md)
- [Blob Storage のイベントをカスタム Web エンドポイントにルーティングする](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Azure Event Grid と Logic Apps で仮想マシンの変更を監視する](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md)
