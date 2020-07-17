---
title: Azure Event Grid イベントに対するイベント ハンドラーとしての Service Bus のキューとトピック
description: Azure Event Grid のイベントに対するイベント ハンドラーとして Service Bus のキューとトピックを使用する方法を説明します。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 201d3203d845ce84207d103750709fe2ff93f022
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596321"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Azure Event Grid イベントに対するイベント ハンドラーとしての Service Bus のキューとトピック
イベント ハンドラーは、イベントの送信先となる場所です。 ハンドラーは、さらにいくつかのアクションを行ってイベントを処理します。 一部の Azure サービスは、イベントを処理するように自動的に構成されます。**Azure Service Bus** はその 1 つです。 

Event Grid からのイベントに対するハンドラーとして、Service Bus のキューまたはトピックを使用できます。 

## <a name="service-bus-queues"></a>Service Bus キュー
エンタープライズ アプリケーションでのバッファー処理または音声コマンドのシナリオで使用するために、Event Grid のイベントを Service Bus キューに直接ルーティングすることができます。

Azure portal で、イベント サブスクリプションを作成するときに、エンドポイントの種類として **[Service Bus キュー]** を選択し、 **[エンドポイントの選択]** をクリックして Service Bus キューを選択します。

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>CLI を使って Service Bus キューハンドラーを追加する

Azure CLI の場合、次の例でイベント グリッド トピックを Service Bus キューにサブスクライブして接続します。

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Service Bus トピック

Event Grid のイベントを Service Bus トピックに直接ルーティングして、Service Bus トピックやコマンドと制御メッセージング シナリオで Azure システム イベントを処理することができます。

Azure portal で、イベント サブスクリプションを作成するときに、エンドポイントの種類として **[Service Bus トピック]** を選択し、 **[エンドポイントの選択]** をクリックして Service Bus トピックを選択します。

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>CLI を使って Service Bus トピック ハンドラーを追加する

Azure CLI の場合、次の例でイベント グリッド トピックを Service Bus キューにサブスクライブして接続します。

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="message-properties"></a>メッセージのプロパティ
Event Grid からのイベントのイベント ハンドラーとして**Service Bus のトピックまたはキュー**を使用する場合は、次のメッセージ ヘッダーを設定します。 

| プロパティ名 | 説明 |
| ------------- | ----------- | 
| aeg-subscription-name | イベント サブスクリプションの名前。 |
| aeg-delivery-count | <p>イベントに対して行われた試行の回数。</p> <p>例:"1"</p> |
| aeg-event-type | <p>イベントの種類。</p><p> 例:"Microsoft.Storage.blobCreated"</p> | 
| aeg-metadata-version | <p>イベントのメタデータ バージョン。</p> <p>例:"1"。</p><p> **Event Grid のイベント スキーマ**の場合、このプロパティはメタデータのバージョンを表し、**クラウド イベント スキーマ**の場合は**仕様のバージョン**を表します。 </p>|
| aeg-data-version | <p>イベントのデータ バージョン。</p><p>例:"1"。</p><p>**Event Grid のイベント スキーマ**の場合、このプロパティはデータのバージョンを表し、**クラウド イベント スキーマ**の場合は適用されません。</p> |

## <a name="message-headers"></a>メッセージ ヘッダー
ブローカー メッセージとして Service Bus のキューまたはトピックにイベントを送信する場合、ブローカー メッセージの `messageid` は**イベント ID** です。

イベント ID は、イベントの再配信間で保持されるので、Service Bus のエンティティで**重複検出**をオンにすることで、配信の重複を回避できます。 Service Bus エンティティでの重複検出の期間を、イベントの有効期限 (TTL) または最大再試行期間のいずれか長い方に設定することをお勧めします。

## <a name="next-steps"></a>次のステップ
サポートされているイベント ハンドラーの一覧については、「[イベント ハンドラー](event-handlers.md)」を参照してください。 
