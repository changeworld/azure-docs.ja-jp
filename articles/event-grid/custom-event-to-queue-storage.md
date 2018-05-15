---
title: Azure Event Grid のカスタム イベントをストレージ キューに送信する | Microsoft Docs
description: Azure Event Grid と Azure CLI を使用して、トピックを発行したり、そのイベントをサブスクライブしたりします。 ストレージ キューはエンドポイントに対して使われます。
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 04/30/2018
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: 6b408dd8c8f0bfd7f7180b10cc9a4882d6950981
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="route-custom-events-to-azure-queue-storage-with-azure-cli-and-event-grid"></a>Azure CLI と Event Grid を使ってカスタム イベントを Azure Queue Storage にルーティングする

Azure Event Grid は、クラウドのイベント処理サービスです。 Azure Queue Storage は、サポートされているイベント ハンドラーの 1 つです。 この記事では、Azure CLI からカスタム トピックを作成してトピックをサブスクライブし、イベントをトリガーして結果を表示します。 イベントを Queue Storage に送信します。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用することを選択した場合、この記事では、最新バージョンの Azure CLI (2.0.24 以降) が実行されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。

Cloud Shell を使用していない場合は、先に `az login` でサインインする必要があります。

## <a name="create-a-resource-group"></a>リソース グループの作成

Event Grid のトピックは Azure リソースであり、Azure リソース グループに配置する必要があります。 リソース グループは、Azure リソースをまとめてデプロイして管理するための論理上のコレクションです。

[az group create](/cli/azure/group#az_group_create) コマンドでリソース グループを作成します。 

次の例では、*gridResourceGroup* という名前のリソース グループを *westus2* の場所に作成します。

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>カスタム トピックの作成

Event Grid のトピックは、イベントの送信先となるユーザー定義のエンドポイントになります。 次の例では、リソース グループにカスタム トピックを作成します。 `<topic_name>` は、トピックの一意の名前に置き換えてください。 トピック名は、DNS エントリによって表されるため、一意である必要があります。

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="create-queue-storage"></a>キュー ストレージを作成する

トピックをサブスクライブする前に、イベント メッセージ用のエンドポイントを作成しましょう。 イベントを収集するためのキュー ストレージを作成します。

```azurecli-interactive
storagename="<unique-storage-name>"
queuename="eventqueue"

az storage account create -n $storagename -g gridResourceGroup -l westus2 --sku Standard_LRS
az storage queue create --name $queuename --account-name $storagename
```

## <a name="subscribe-to-a-topic"></a>トピックのサブスクライブ

どのイベントを追跡するかは、トピックをサブスクライブすることによって Event Grid に伝えます。次の例では、作成したトピックをサブスクライブし、エンドポイントのキュー ストレージのリソース ID を渡します。 キュー ストレージ ID は次のような形式です。

`/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/queueservices/default/queues/<queue-name>`

次のスクリプトは、キューのストレージ アカウントのリソース ID を取得します。 キュー ストレージの ID を作成して、イベント グリッドのトピックをサブスクライブします。 エンドポイントの種類を `storagequeue` に設定し、エンドポイントのキュー ID を使います。

```azurecli-interactive
storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)
queueid="$storageid/queueservices/default/queues/$queuename"

az eventgrid event-subscription create \
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --name <event_subscription_name> \
  --endpoint-type storagequeue \
  --endpoint $queueid
```

## <a name="send-an-event-to-your-topic"></a>トピックへのイベントの送信

イベントをトリガーして、Event Grid がメッセージをエンドポイントに配信するようすを見てみましょう。 まず、カスタム トピックの URL とキーを取得します。 `<topic_name>` には、先ほどと同じトピック名を使用してください。

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

この記事では、単純化するために、トピックに送信するサンプル イベント データを使用します。 通常はイベント データをアプリケーションまたは Azure サービスから送信することになります。 CURL は、HTTP 要求を送信するユーティリティです。 この記事では、CURL を使用して、イベントをトピックに送信します。  次の例では、3 つのイベントをイベント グリッド トピックに送信します。

```azurecli-interactive
for i in 1 2 3
do
   body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
   curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
done
```

ポータルでキュー ストレージに移動し、イベント グリッドがキューにこれら 3 つのイベントを送信したことを確認します。

![メッセージを表示する](./media/custom-event-to-queue-storage/messages.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ
引き続きこのイベントを使用する場合は、この記事で作成したリソースをクリーンアップしないでください。 それ以外の場合は、次のコマンドを使用して、この記事で作成したリソースを削除します。

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>次の手順

トピックを作成し、イベントをサブスクライブする方法がわかったら、Event Grid でできることについて、さらに情報を収集しましょう。

- [Event Grid について](overview.md)
- [Blob Storage のイベントをカスタム Web エンドポイントにルーティングする](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Azure Event Grid と Logic Apps で仮想マシンの変更を監視する](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md)
