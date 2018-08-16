---
title: Azure Event Grid サブスクリプションの配信不能と再試行に関する方針
description: Event Grid のイベント配信オプションをカスタマイズする方法について説明します。 配信不能メッセージの送信先を設定し、配信の再試行時間を指定します。
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: tomfitz
ms.openlocfilehash: 5a37fadc179157ba590b31a79fcd98f223cb1869
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39501951"
---
# <a name="dead-letter-and-retry-policies"></a>配信不能と再試行に関する方針

イベント サブスクリプションの作成時には、イベント配信の設定をカスタマイズできます。 Event Grid がメッセージ配信を試行する期間を設定できます。 エンドポイントに配信できないイベントの格納に使用するストレージ アカウントを設定できます。

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>配信不能の場所の設定

Event Grid がイベントを配信できない場合は、配信不能イベントをストレージ アカウントに送信できます。 このプロセスは配信不能処理と呼ばれます。 既定では、Event Grid は配信不能処理を有効にしません。 この処理を有効にするには、イベント サブスクリプションの作成時に、配信不能イベントを保持するようにストレージ アカウントを指定する必要があります。 このストレージ アカウントからイベントをプルして配信を解決します。

Event Grid では、すべての再試行を行った場合、または配信が成功しないことを示すエラー メッセージを受信した場合に、配信不能の場所にイベントを送信します。 たとえば、Event Grid では、イベントの配信時に不適切な形式であることを示すエラーを受信すると、そのイベントを配信不能の場所に送信します。 イベントの配信を最後に試してから配信不能メッセージがその宛先に届くまで、5 分間の遅延があります。 この遅延の目的は、BLOB ストレージの操作数を減らすことにあります。 配信不能メッセージの送信先が 4 時間利用できない場合、イベントは破棄されます。

配信不能の場所を設定するには、コンテナーを含むストレージ アカウントが必要です。 イベント サブスクリプションの作成時に、このコンテナーのエンドポイントを指定します。 エンドポイントの形式は次のとおりです。`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

次のスクリプトは、既存のストレージ アカウントのリソース ID を取得し、そのストレージ アカウント内のコンテナーを配信不能のエンドポイントとして使用するイベント サブスクリプションを作成します。

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

storagename=demostorage
containername=testcontainer

storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Event Grid を使用して配信不能イベントに応答するには、配信不能 Blob ストレージ用の[イベント サブスクリプションを作成](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)します。 配信不能 Blob ストレージが配信不能イベントを受信するたびに、Event Grid はハンドラーに通知します。 ハンドラーは、配信不能イベントを調整するためのアクションで応答します。 

配信不能処理を無効にするには、コマンドを再実行してイベント サブスクリプションを作成しますが、`deadletter-endpoint` の値は指定しないでください。 イベント サブスクリプションを削除する必要はありません。

## <a name="set-retry-policy"></a>再試行ポリシーの設定

Event Grid サブスクリプションの作成時には、Event Grid がイベント配信を試行する期間の値を設定できます。 既定では、Event Grid は 24 時間 (1440 分) で最大 30 回イベント配信を試行します。 Event Grid サブスクリプションには、これらの値のいずれかを設定できます。 イベントの有効期限の値は 1 から 1440 までの整数にする必要があります。 最大配信試行数の値は 1 から 30 までの整数にする必要があります。

[再試行の間隔](delivery-and-retry.md#retry-intervals-and-duration)を構成することはできません。

イベントの Time to Live を 1440 分以外の値に設定するには、次のコマンドを使用します。

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

最大再試行回数を 30 以外の値に設定するには、次のコマンドを使用します。

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

`event-ttl` と `max-deliver-attempts` の両方を設定する場合、Event Grid では最初の項目を使用して、再試行の間に期限切れになるようにします。

## <a name="next-steps"></a>次の手順

* Azure 関数アプリを利用して配信不能イベントを処理するサンプル アプリケーションについては、「[Azure Event Grid Dead Letter Samples for .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/)」 (.NET 向け Azure Event Grid 配信不能サンプル) を参照してください。
* イベント配信と再試行については、「[Event Grid によるメッセージ配信と再試行](delivery-and-retry.md)」をご覧ください。
* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。
