---
title: Azure Event Grid スキーマへのカスタム フィールドのマップ
description: カスタム スキーマを Azure Event Grid スキーマに変換する方法について説明します。
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 32f93f383ec4044afb0696fcef1705c9ed65d673
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38578919"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Event Grid スキーマへのカスタム フィールドのマップ

イベント データと必要な [Event Grid スキーマ](event-schema.md)が一致しない場合でも、Event Grid を使用して、イベントをサブスクライバーにルーティングできます。 この記事では、カスタム スキーマを Event Grid スキーマにマップする方法について説明します。

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>元のイベント スキーマ

次の形式でイベントを送信するアプリケーションがあるとします。

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

この形式は必要なスキーマと一致しませんが、Event Grid を使用して、フィールドをスキーマにマップできます。 つまり、元のスキーマの値を受け取ることができます。

## <a name="create-custom-topic-with-mapped-fields"></a>フィールドをマップしたカスタム トピックの作成

カスタム トピックを作成する際に、元のイベントのフィールドを Event Grid スキーマにマップする方法を指定します。 マッピングのカスタマイズに使用するプロパティは 3 つあります。

* `--input-schema` パラメーターでは、スキーマの種類を指定します。 指定可能なオプションは、*cloudeventv01schema*、*customeventschema*、*eventgridschema* です。 既定値は eventgridschema です。 カスタム スキーマと Event Grid スキーマの間のカスタム マッピングを作成する場合は、customeventschema を使用します。 イベントが CloudEvents スキーマの場合は、cloudeventv01schema を使用します。

* `--input-mapping-default-values` パラメーターでは、Event Grid スキーマのフィールドの既定値を指定します。 *subject*、*eventtype*、*dataversion* の既定値を設定できます。 通常、これらの 3 つのフィールドのいずれかに対応するフィールドがカスタム スキーマに含まれていない場合に、このパラメーターを使用します。 たとえば、dataversion が常に **1.0** に設定されるように指定できます。

* `--input-mapping-fields` パラメーターでは、カスタム スキーマのフィールドを Event Grid スキーマにマップします。 値はスペースで区切ったキー/値のペアで指定します。 キー名には、Event Grid フィールドの名前を使用します。 値には、カスタム フィールドの名前を使用します。 キー名には、*id*、*topic*、*eventtime*、*subject*、*eventtype*、*dataversion* を使用できます。

次の例では、マッピングされたフィールドと既定のフィールドが含まれるカスタム トピックを作成します。

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

## <a name="subscribe-to-event-grid-topic"></a>Event Grid トピックのサブスクライブ

カスタム トピックをサブスクライブする場合は、イベントの受信に使用するスキーマを指定します。 `--event-delivery-schema` パラメーターを使用し、*cloudeventv01schema*、*eventgridschema*、または *inputeventschema* に設定します。 既定値は eventgridschema です。

このセクションの例では、イベント ハンドラーに Queue Storage を使用しています。 詳細については、[Azure Queue Storage へのカスタム イベントのルーティング](custom-event-to-queue-storage.md)に関するページを参照してください。

次の例では、Event Grid トピックをサブスクライブし、既定の Event Grid スキーマを使用します。

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub1 \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

次の例では、イベントの入力スキーマを使用します。

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub2 \
  --event-delivery-schema inputeventschema \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

## <a name="publish-event-to-topic"></a>トピックへのイベントの公開

これで、カスタム トピックにイベントを送信し、マッピングの結果を確認する準備が整いました。 次のスクリプトで[サンプル スキーマ](#original-event-schema)を使ってイベントを投稿します。

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/mapeventfields.json)'")

curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

それでは、Queue Storage を確認してください。 2 つのサブスクリプションで異なるスキーマを使ってイベントが配信されています。

最初のサブスクリプションでは、Event Grid スキーマを使用しました。 配信されたイベントの形式は次のとおりです。

```json
{
  "Id": "016b3d68-881f-4ea3-8a9c-ed9246582abe",
  "EventTime": "2018-05-01T20:00:25.2606434Z",
  "EventType": "Created",
  "DataVersion": "1.0",
  "MetadataVersion": "1",
  "Topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "Subject": "DefaultSubject",
  "Data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": { "someDataField1": "SomeDataFieldValue" } 
  }
}
```

これらのフィールドには、カスタム トピックのマッピングが含まれています。 **myEventTypeField** は **EventType** にマップされています。 **DataVersion** と **Subject** の既定値が使用されています。 **Data** オブジェクトには元のイベント スキーマのフィールドが含まれています。

2 つ目のサブスクリプションでは、入力イベント スキーマを使用しました。 配信されたイベントの形式は次のとおりです。

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": { "someDataField1": "SomeDataFieldValue" }
}
```

元のフィールドが配信されていることに注目してください。

## <a name="next-steps"></a>次の手順

* イベント配信と再試行については、「[Event Grid によるメッセージ配信と再試行](delivery-and-retry.md)」をご覧ください。
* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。
