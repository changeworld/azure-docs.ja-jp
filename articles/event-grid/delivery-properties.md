---
title: Azure Event Grid - 配信されたイベントにカスタム ヘッダーを設定する
description: 配信されたイベントにカスタム ヘッダー (または配信プロパティ) を設定する方法について説明します。
ms.topic: conceptual
ms.date: 08/13/2021
ms.openlocfilehash: 3600d74d91ad218f3fcab99002762d605fba3139
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831352"
---
# <a name="custom-delivery-properties"></a>カスタム配信プロパティ
イベント サブスクリプションを使用すると、配信されたイベントに含まれる HTTP ヘッダーを設定できます。 この機能を使用すると、宛先に必要なカスタム ヘッダーを設定できます。 イベント サブスクリプションを作成するときに、最大 10 個のヘッダーを設定できます。 各ヘッダーの値は、4,096 (4 K) バイトより大きくすることはできません。

次の宛先に配信されるイベントにカスタム ヘッダーを設定できます。

- Webhooks
- Azure Service Bus のトピックとキュー
- Azure Event Hubs
- Relay ハイブリッド接続

Azure portal でイベント サブスクリプションを作成する場合は、 **[配信プロパティ]** タブを使用してカスタム HTTP ヘッダーを設定できます。 このページでは、固定と動的ヘッダー値を設定できます。

## <a name="setting-static-header-values"></a>静的ヘッダー値の設定
固定値を使用してヘッダーを設定するには、対応するフィールドにヘッダーの名前とその値を指定します。

:::image type="content" source="./media/delivery-properties/static-header-property.png" alt-text="配信プロパティ - 静的":::

機密データを指定する場合は、 **[Is secret?]\(秘密である\)** チェックボックスをオンにできます。 機密データは Azure portal に表示されません。 

## <a name="setting-dynamic-header-values"></a>動的ヘッダー値の設定
受信イベントのプロパティに基づいてヘッダーの値を設定できます。 JsonPath 構文を使用して、送信要求のヘッダーの値として使用する受信イベントのプロパティ値を参照します。 たとえば、イベント データの受信イベント プロパティ **system** の値を使用して、**Channel** という名前のヘッダーの値を設定するには、次の方法でイベント サブスクリプションを構成します。

:::image type="content" source="./media/delivery-properties/dynamic-header-property.png" alt-text="配信プロパティ - 動的":::

## <a name="use-azure-cli"></a>Azure CLI の使用
`az eventgrid event-subscription create` コマンドを使用してサブスクリプションを作成するときに `--delivery-attribute-mapping` パラメーターを使用します。 次に例を示します。

```azurecli
az eventgrid event-subscription create -n es1 \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1
    --endpoint-type storagequeue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/sa1/queueservices/default/queues/q1 \
    --enable-advanced-filtering-on-arrays true
    --delivery-attribute-mapping staticproperty1 static somestaticvalue2 true 
    --delivery-attribute-mapping staticproperty2 static somestaticvalue3 false 
    --delivery-attribute-mapping dynamicproperty1 dynamic data.key1
```

## <a name="examples"></a>例
ここでは、配信プロパティの使用例をいくつか紹介します。

### <a name="setting-the-authorization-header-with-a-bearer-token-non-normative-example"></a>ベアラー トークンを使用した Authorization ヘッダーの設定 (非標準例)

Webhook ハンドラーで要求を識別するために、Authorization ヘッダーに値を設定します。 [Azure Active Directory で Webhook を保護して](secure-webhook-delivery.md)いない場合は、Authorization ヘッダーを設定できます。

| ヘッダー名   | ヘッダーの種類 | ヘッダー値 |
| :--           | :--         | :--            |
|`Authorization` | スタティック | `BEARER SlAV32hkKG...`|

送信要求に、イベント サブスクリプションに設定されたヘッダーが含まれるようになりました。

```console
POST /home.html HTTP/1.1
Host: acme.com

Authorization: BEARER SlAV32hkKG...
```

> [!NOTE]
> Authorization ヘッダーを定義することは、宛先が Webhook の場合、適切なオプションです。 [リソース ID を使用してサブスクライブしている関数](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate#azurefunctioneventsubscriptiondestination)、Service Bus、Event Hubs、および Hybrid Connections には使用しないでください。Event Grid と使用した場合、これらの宛先では独自の認証スキームがサポートされているからです。

### <a name="service-bus-example"></a>Service Bus の例
Azure Service Bus は、単一メッセージを送信する際に、以下のメッセージプロパティの使用をサポートしています。 

| ヘッダー名 | ヘッダーの種類 |
| :-- | :-- |
| `MessageId` | 動的 |  
| `PartitionKey` | 静的または動的 |
| `SessionId` | 静的または動的 |
| `CorrelationId` | 静的または動的 |
| `Label` | 静的または動的 |
| `ReplyTo` | 静的または動的 | 
| `ReplyToSessionId` | 静的または動的 |
| `To` |静的または動的 |
| `ViaPartitionKey` | 静的または動的 |

> [!NOTE]
> - `MessageId` の既定値は、Event Grid のイベントの内部 ID です。 これはオーバーライドできます。 たとえば、「 `data.field` 」のように入力します。
> - 設定できるのは `SessionId` か `MessageId` のみです。 

### <a name="event-hubs-example"></a>Event Hubs の例

イベントをイベント ハブ内の特定のパーティションに発行する必要がある場合は、イベント サブスクリプションに `ParitionKey` プロパティを設定して、対象のイベント ハブ パーティションを識別するパーティション キーを指定します。

| ヘッダー名 | ヘッダーの種類 |
| :-- | :-- |
|`PartitionKey` | スタティック |


### <a name="configure-time-to-live-on-outgoing-events-to-azure-storage-queues"></a>Azure Storage キューへの送信イベントの有効期限を構成する
Azure Storage キューの宛先の場合は、送信メッセージが Azure Storage キューに配信されてからのみ、その有効期限を構成できます。 時間が指定されていない場合、メッセージの既定の有効期限は 7 日間です。 また、イベントを無期限に設定することもできます。

:::image type="content" source="./media/delivery-properties/delivery-properties-storage-queue.png" alt-text="配信プロパティ - Storage キュー":::

## <a name="next-steps"></a>次の手順
イベント配信の詳細については、次の記事を参照してください。

- [配信と再試行](delivery-and-retry.md)
- [Webhook のイベント配信](webhook-event-delivery.md)
- [イベント フィルター](event-filtering.md)
