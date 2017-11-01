---
title: "Azure Portal を使用した Azure Event Grid のカスタム イベント | Microsoft Docs"
description: "Azure Event Grid と PowerShell を使用して、トピックを発行したり、そのイベントをサブスクライブしたりします。"
services: event-grid
keywords: 
author: djrosanova
ms.author: darosa
ms.date: 10/11/2017
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: 0fe498b7b6dcf59bc5caef8ff5a40053e0498f85
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2017
---
# <a name="create-and-route-custom-events-with-the-azure-portal-and-event-grid"></a>Azure Portal と Event Grid を使ったカスタム イベントの作成とルーティング

Azure Event Grid は、クラウドのイベント処理サービスです。 この記事では、Azure Portal を使用して、カスタム トピックを作成してそのトピックをサブスクライブし、イベントをトリガーして結果を表示します。 通常、webhook や Azure Functions など、イベントに応答するエンドポイントが、イベントの送信先になります。 ただし、この記事では、単純化するために、メッセージをただ収集するだけの URL に対してイベントを送信します。 この URL は、[RequestBin](https://requestb.in/) というオープン ソースのサードパーティ ツールを使用して作成します。

>[!NOTE]
>**RequestBin** は、高いスループットが要求される用途を意図したオープン ソース ツールではありません。 ここでは、あくまでデモンストレーションを目的としてこのツールを使用しています。 一度に複数のイベントをプッシュすると、一部のイベントがツールから見えない場合があります。

最後に、イベント データがエンドポイントに送信されたことを確認します。

![イベント データ](./media/custom-event-quickstart-portal/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-resource-group"></a>リソース グループの作成

Event Grid のトピックは Azure リソースであり、Azure リソース グループに配置する必要があります。 リソース グループは、Azure リソースをまとめてデプロイして管理するための論理上のコレクションです。

1. 左側のナビゲーションで、**[リソース グループ]** を選択します。 その後、**[追加]** を選択します。

   ![Create resource group](./media/custom-event-quickstart-portal/create-resource-group.png)

1. リソース グループ名を *gridResourceGroup* に、その場所を *westus2* に設定します。 **[作成]**を選択します。

   ![リソース グループの値の指定](./media/custom-event-quickstart-portal/provide-resource-group-values.png)

## <a name="create-a-custom-topic"></a>カスタム トピックの作成

トピックは、イベントの送信先となるユーザー定義のエンドポイントになります。 

1. リソース グループ内にトピックを作成するには、**[その他のサービス]** を選択し、"*event grid*" を検索します。 利用可能なオプションから **[Event Grid トピック]** を選択します。

   ![Event Grid トピックの作成](./media/custom-event-quickstart-portal/create-event-grid-topic.png)

1. **[追加]**を選択します。

   ![Event Grid トピックの追加](./media/custom-event-quickstart-portal/add-topic.png)

1. トピックの名前を指定します。 トピック名は、DNS エントリによって表されるため、一意である必要があります。 プレビュー リリースの Event Grid では、**westus2** と **westcentralus** の場所がサポートされます。 先ほど作成したリソース グループを選択します。 **[作成]**を選択します。

   ![Event Grid トピックの値の指定](./media/custom-event-quickstart-portal/provide-topic-values.png)

1. このトピックが作成されたら、**[更新]** を選択してトピックを表示します。

   ![Event Grid トピックの表示](./media/custom-event-quickstart-portal/see-topic.png)

## <a name="create-a-message-endpoint"></a>メッセージ エンドポイントの作成

トピックをサブスクライブする前に、イベント メッセージ用のエンドポイントを作成しましょう。 ここではイベントに応答するコードを作成する代わりに、皆さんが確認できるように、メッセージを収集するエンドポイントを作成します。 RequestBin は、エンドポイントを作成してそこに送信された要求を確認することができるオープン ソースのサードパーティ ツールです。 [RequestBin](https://requestb.in/) にアクセスし、**[Create a RequestBin]\(RequestBin の作成\)** をクリックします。  Bin URL をコピーしてください。トピックをサブスクライブするときにこの URL が必要になります。

## <a name="subscribe-to-a-topic"></a>トピックのサブスクライブ

どのイベントを追跡するかは、トピックをサブスクライブすることによって Event Grid に伝えます。 

1. Event Grid サブスクリプションを作成するには、もう一度 **[その他のサービス]** を選択し、"*event grid*" を検索します。 利用可能なオプションから **[Event Grid サブスクリプション]** を選択します。

   ![Event Grid サブスクリプションの作成](./media/custom-event-quickstart-portal/create-subscription.png)

1. **[+ イベント サブスクリプション]** を選択します。

   ![Event Grid サブスクリプションの追加](./media/custom-event-quickstart-portal/add-subscription.png)

1. イベント サブスクリプションに一意の名前を指定します。 トピックの種類については、**[Event Grid トピック]** を選択します。 インスタンスについては、作成したカスタム トピックを選択します。 イベント通知のエンドポイントとして、RequestBin の URL を指定します。 値の指定が完了したら、**[作成]** を選択します。

   ![Event Grid サブスクリプションの値の指定](./media/custom-event-quickstart-portal/provide-subscription-values.png)

では、イベントをトリガーして、Event Grid がメッセージをエンドポイントに配信するようすを見てみましょう。 この記事では、単純化するために、Cloud Shell を使用してサンプルのイベント データをトピックに送信します。 通常はイベント データをアプリケーションまたは Azure サービスから送信することになります。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="send-an-event-to-your-topic"></a>トピックへのイベントの送信

まず、トピックの URL とキーを取得します。 `<topic_name>` には、実際のトピック名を使用します。

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

次の例では、サンプルのイベント データを取得します。

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

`echo "$body"` を実行すると、イベント全体を確認できます。 JSON の `data` 要素がイベントのペイロードです。 このフィールドには、適切な形式の JSON であればどのようなものでも格納することができます。 また、高度なルーティングやフィルタリングを行う場合には、subject フィールドを使用することもできます。

CURL は、HTTP 要求を行うユーティリティです。 この記事では、CURL を使用して、イベントをトピックに送信します。 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

以上でイベントがトリガーされ、そのメッセージが、Event Grid によってサブスクライブ時に構成したエンドポイントに送信されました。 先ほど作成した RequestBin URL にブラウザーでアクセスします。 または、開いている RequestBin ブラウザーで、最新の情報に更新するボタンをクリックしてください。 先ほど送信したイベントが表示されます。

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

引き続きこのイベントを使用する場合は、この記事で作成したリソースをクリーンアップしないでください。 続行する予定がない場合は、この記事で作成したリソースを削除してください。

リソース グループを選択し、**[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

トピックを作成し、イベントをサブスクライブする方法がわかったら、Event Grid でできることについて、さらに情報を収集しましょう。

- [Event Grid について](overview.md)
- [Blob Storage のイベントをカスタム Web エンドポイントにルーティングする](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Azure Event Grid と Logic Apps で仮想マシンの変更を監視する](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md)
