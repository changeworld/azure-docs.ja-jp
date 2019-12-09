---
title: CloudEvents スキーマ内のイベントで Azure Event Grid を使用する
description: CloudEvents スキーマを Azure Event Grid 内のイベント用に設定する方法について説明します。
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 6a0e24ce7fa11c6373fbaada40cd9f1b1e7f55a2
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325468"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Event Grid に CloudEvents v1.0 スキーマを使用する

Azure Event Grid は、[既定のイベント スキーマ](event-schema.md)に加え、[CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) および [HTTP プロトコル バインディング](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)の JSON 実装のイベントをネイティブでサポートします。 [CloudEvents](https://cloudevents.io/) は、イベント データを記述するための[オープンな仕様](https://github.com/cloudevents/spec/blob/v1.0/spec.md)です。

CloudEvents を使用すると、クラウド ベースのイベントを発行したり使用したりするための共通のイベント スキーマを提供し、相互運用性を簡略化することができます。 このスキーマを使用すれば、ツールを統一化したり、イベントのルーティングや処理方法を標準化したり、外部のイベント スキーマを共通の方法で逆シリアル化することができます。 共通のスキーマを使用することで、プラットフォーム間での作業をより簡単に統合できます。

CloudEvents は、[Cloud Native Computing Foundation](https://www.cncf.io/) を通じ、複数の[コラボレーター](https://github.com/cloudevents/spec/blob/master/community/contributors.md) (マイクロソフトを含む) によって構築されています。 現在、バージョン 1.0 が提供されています。

この記事では、Event Grid で CloudEvents スキーマを使用する方法について説明します。

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>プレビュー機能のインストール

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent スキーマ

次に示すのは、CloudEvents 形式の Azure Blob Storage イベントの例です。

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

CloudEvents v0.1 で使用できるフィールド、その種類、定義の詳細については、[こちら](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)を参照してください。

CloudEvents スキーマと Event Grid スキーマで配信されるイベントのヘッダー値は同じです (`content-type` を除く)。 CloudEvents スキーマの場合、そのヘッダー値は `"content-type":"application/cloudevents+json; charset=utf-8"` です。 Event Grid スキーマの場合、そのヘッダー値は `"content-type":"application/json; charset=utf-8"` です。

## <a name="configure-event-grid-for-cloudevents"></a>Event Grid を CloudEvents 用に構成する

Event Grid は、CloudEvents スキーマ内のイベントの入力と出力の両方に使用できます。 CloudEvents は、システム イベント (Blob Storage イベントや IoT Hub イベントなど) とカスタム イベントに使用できます。 また、これらのイベントをネットワーク上で相互に変換することもできます。


| 入力スキーマ       | 出力スキーマ
|--------------------|---------------------
| CloudEvents 形式 | CloudEvents 形式
| Event Grid 形式  | CloudEvents 形式
| CloudEvents 形式 | Event Grid 形式
| Event Grid 形式  | Event Grid 形式

いずれのイベント スキーマについても、Event Grid では、イベント グリッド トピックへの発行時やイベント サブスクリプションの作成時に検証が必要です。 詳細については、「[Event Grid security and authentication](security-authentication.md)」(Event Grid のセキュリティと認証) を参照してください。

### <a name="input-schema"></a>入力スキーマ

カスタム トピックを作成するときは、カスタム トピックに対する入力スキーマを設定します。

Azure CLI では、次を使用します。

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

PowerShell では、次を使用します。

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

CloudEvents の現在のバージョンでは、イベントのバッチ処理はサポートされていません。 CloudEvent スキーマを使ったイベントをトピックに 対して発行するには、各イベントを個別に発行してください。

### <a name="output-schema"></a>出力スキーマ

イベント サブスクリプションを作成するときは、出力スキーマを設定します。

Azure CLI では、次を使用します。

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

PowerShell では、次を使用します。
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 現時点では、CloudEvents スキーマでイベントを配信する際に、Azure Functions アプリの Event Grid トリガーを使用することはできません。 HTTP トリガーを使用します。 CloudEvents スキーマでイベントを受け取る HTTP トリガーを実装する例については、「[Event Grid トリガーとして HTTP トリガーを使用する](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger)」を参照してください。

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>CloudEvents v1.0 を使用したエンドポイントの検証

Event Grid を既に使い慣れている場合、不正使用を防ぐための Event Grid のエンドポイント検証ハンドシェイクをご存じかもしれません。 CloudEvents v1.0 では、HTTP OPTIONS メソッドを使用して、独自の[不正使用防止のセマンティクス](security-authentication.md#webhook-event-delivery)が実装されています。 詳細については、 [こちら](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)を参照してください。 出力に CloudEvents スキーマを使用すると、Event Grid では、Event Grid の検証イベント メカニズムではなく CloudEvents v1.0 の不正使用防止が使用されます。

## <a name="next-steps"></a>次の手順

* イベント配信の監視について詳しくは、「[Event Grid メッセージ配信の監視](monitor-event-delivery.md)」をご覧ください。
* CloudEvents テストし、問題についてコメントし、改良に[協力](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md)する。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
