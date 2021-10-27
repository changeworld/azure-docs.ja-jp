---
title: Event Grid ソースとしての Azure Communication Services - 概要
description: この記事では、Event Grid イベント ソースとして Azure Communication Services を使用する方法について説明します。
ms.topic: conceptual
ms.date: 06/11/2021
ms.author: mikben
ms.openlocfilehash: 590e0a53ca701a6b293dd52b2f7c30c5e873df30
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129493"
---
# <a name="event-handling-in-azure-communication-services"></a>Azure Communication Services でのイベント処理

Azure Communication Services は [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) と統合され、信頼性が高く、スケーラブルで安全な方法でリアルタイムのイベント通知を配信します。 この記事の目的は、ご利用のアプリケーションを構成して Communication Services イベントをリッスンできるようにすることです。 たとえば、Communication Services リソースに関連付けられている電話番号で SMS メッセージを受信するたびに、データベースを更新し、作業項目を作成し、プッシュ通知を配信したい場合があります。

Azure Event Grid は、発行 - サブスクライブ モデルを使用する、フル マネージドのイベント ルーティング サービスです。 Event Grid には、[Azure Functions](../azure-functions/functions-overview.md) や [Azure Logic Apps](../azure-functions/functions-overview.md) のような Azure サービスのサポートが組み込まれています。 また、Webhook を使用することで、Azure 以外のサービスにイベント アラートを配信することもできます。 Event Grid がサポートするイベント ハンドラーの完全な一覧については、「[Azure Event Grid の概要](overview.md)」をご覧ください。

:::image type="content" source="./media/overview/functional-model.png" alt-text="Azure Event Grid のイベント モデルを示す図。":::

> [!NOTE]
> データ所在地とイベント処理の関係の詳細については、[データ所在地の概念説明ドキュメント](../communication-services/concepts/privacy.md)を参照してください。

## <a name="events-types"></a>イベントの種類

Event Grid は、[イベント サブスクリプション](concepts.md#event-subscriptions)を使って、イベント メッセージをサブスクライバーにルーティングします。

Azure Communication Services から出力されるイベントの種類は次のとおりです。

* [チャット イベント](./communication-services-chat-events.md)
* [テレフォニーと SMS イベント](./communication-services-telephony-sms-events.md)
* [音声およびビデオによる通話イベント](./communication-services-voice-video-events.md)
* [プレゼンス イベント](./communication-services-presence-events.md)

Azure portal または Azure CLI を使用して、Communication Services リソースによって発行されたイベントをサブスクライブできます。 

## <a name="event-subjects"></a>イベントの件名

すべての Communication Services イベントの `subject` フィールドは、イベントの対象となるユーザー、電話番号、またはエンティティを識別します。 単純な [Event Grid のフィルター処理](event-filtering.md)を可能にするために、共通プレフィックスが使用されます。

| 件名のプレフィックス                              | Communication Service エンティティ |
| ------------------------------------------- | ---------------------------- |
| `phonenumber/`                              | PSTN の電話番号            |
| `user/`                                     | Communication Services ユーザー  |
| `thread/`                                   | チャット スレッド。                 |

次の例は、Communication Services リソースが所有する市外局番が 555 のすべての電話番号に送信されるすべての SMS メッセージと配信レポートに対するフィルターを示しています。

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Communication.SMSReceived",
    "Microsoft.Communication.SMSDeliveryReportReceived"
  ],
  "subjectBeginsWith": "phonenumber/1555",
}
```

## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](./overview.md)に関する記事を参照してください。
* Azure Event Grid の概念の概要については、[Event Grid の概念](./concepts.md)に関する記事を参照してください。
* Azure Event Grid のシステム トピックの概要については、「[Azure Event Grid でのシステム トピック](./system-topics.md)」を参照してください。
