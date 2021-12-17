---
title: Kubernetes 上の Azure Event Grid - 概念
description: この記事では、Azure Arc を使用する Kubernetes 上の Azure Event Grid の主要概念について説明します (プレビュー)
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 911a67644ac50906ee7e4b3009658043cfa022d6
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414577"
---
# <a name="event-grid-on-kubernetes---concepts"></a>Kubernetes 上の Event Grid - 概念
この記事では、Azure Arc を使用する Kubernetes 上の Event Grid での主要な概念について説明します (プレビュー)。

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]

## <a name="events"></a>イベント
イベントとは、ソフトウェア システムの操作に関する事実を通知するデータ レコードです。 通常は、イベントにより、システムによって発生したシグナルまたはシステムによって検出されたシグナルによる状態の変化が通知されます。 イベントには、次の 2 種類の情報が含まれます。 

- 状態変化の発生を表す[イベント データ](https://github.com/cloudevents/spec/blob/master/spec.md#event-data)。 
- イベントの発生に関するコンテキスト情報を提供する[コンテキスト属性](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes)。     

    イベント データとコンテキスト属性はどちらも、イベントのフィルター処理に使用できます。 

Kubernetes 上の Event Grid によって、[CloudEvents](https://github.com/cloudevents/spec/tree/master) スキーマ仕様がサポートされています。 CloudEvents スキーマを使用するイベントの例を次に示します。 Event Grid では、最大 1 MB のサイズのイベントがサポートされています。

```json
[{
       "specVersion": "1.0",
       "type" : "orderCreated",
       "source": "myCompanyName/us/webCommerceChannel/myOnlineCommerceSiteBrandName",
       "id" : "eventId-n",
       "time" : "2020-12-25T20:54:07+00:00",
       "subject" : "account/acct-123224/order/o-123456",
       "dataSchema" : "1.0",
       "data" : {
          "orderId" : "123",
          "orderType" : "PO",
          "reference" : "https://www.myCompanyName.com/orders/123"
      }
}]
```

## <a name="source"></a>source
ソース属性により、イベントが発生したコンテキストが説明されます。 ソースはイベントの発生元である場合があります。 ただし、イベントを作成して発行するプロデューサーが存在する場合もあります。 そして、これらのプロデューサーはソースとは異なります。 わかりやすくするため、この記事では、ソースがイベントのプロデューサーであるものとします。 

各イベント ソースにより、1 つまたは複数のイベントの種類のイベントが生成されます。 イベントのソースとして、アプリケーションでは、状態の変化を通知する一連の関連イベントを定義します。 すべてのイベントには、イベントの発生元、イベントの発生日時、一意識別子などの一般的な情報が保持されています。 すべてのイベントには、特定のイベントの種類にのみ関連する情報も含まれます。 最大 1 MB のサイズのイベントのサポートは現在、プレビュー段階です。

イベントに含まれるプロパティについては、[CloudEvents スキーマ](event-schemas.md#cloudevent-schema)に関する記事を参照してください。

## <a name="publishers"></a>ディストリビューターのプロパティ
イベント パブリッシャーは、イベント サブスクライバーに配信されるイベントを Event Grid に送信するアプリケーションまたはシステムです。

## <a name="topics"></a>トピック
トピックは、Event Grid へのイベントをパブリッシャーが送信する先のエンドポイントを公開する入力チャネルの形式です。

トピックは、関連するイベントを収集するために使用できます。 関連イベントのカテゴリごとにトピックを作成できます。 通常、ソースは密接に関連するイベントの種類のセット ("MyApp.OrderCreated"、"MyApp.OderDeleted"、"MyApp.OrderRejected" など) に関連付けられているため、ソースを使用してイベントをカテゴリに整理できる場合があります。 

ユーザー アカウントの管理と注文の処理に関連するイベントを送信するアプリケーションがあるものとします。 1 つのイベント サブスクライバーが両方のカテゴリのイベントを使用することに関心を持つことはあまりありません。 このような場合は、2 つのカスタム トピックを作成し、イベント ハンドラーが自分と関係のあるトピックをサブスクライブできるようにします。 小規模なソリューションの場合、すべてのイベントを 1 つのトピックに送信することをお勧めします。 

## <a name="event-subscribers"></a>イベント サブスクライバー
イベント サブスクライバーは、Event Grid によるイベントの配信先であるエンドポイントを公開する、マイクロサービスなどのソフトウェア システムです。 

## <a name="event-subscriptions"></a>イベントのサブスクリプション
イベント サブスクリプションは、トピックのどのイベントをユーザーが受け取りたいと思っているか (イベント フィルター処理)、およびそれらの送信先 (イベント ルーティング) を、Event Grid に伝えます。 ユーザーは、イベント サブスクリプションを作成するときに、イベントを処理するためのエンドポイントを指定します。 ユーザーは、イベント サブスクリプションでフィルター句を構成することにより、エンドポイントへの配信を希望するイベントを選択できます。 

## <a name="event-handlers"></a>イベント ハンドラー
イベント ハンドラーは、イベントの送信先であるエンドポイントを公開するソフトウェア システムです。 ハンドラーによりイベントが受け取られ、アクションを実行してイベントが処理されます。 Event Grid は、複数の種類のハンドラーをサポートします。 ハンドラーとしては、Kubernetes または Azure でホストされているサポートされる Azure サービス、またはホストされている任意の場所で Webhook (エンドポイント) を公開する独自のソリューションを使用できます。 Event Grid は、ハンドラーの種類に応じたさまざまなメカニズムに従って、イベントの配信を保証します。 送信先のイベント ハンドラーが HTTP Webhook である場合、ハンドラーによって状態コード 200 – OK が返されるまで、イベントは再試行されます。 詳細については、[イベント ハンドラー](event-handlers.md)に関する記事を参照してください。

## <a name="sas-authentication"></a>SAS 認証
Kubernetes 上の Event Grid からは、トピックへのイベント発行用に SAS キー ベースの認証が提供されます。

## <a name="event-delivery"></a>イベント配信
Kubernetes 上の Event Grid から、信頼性の高い配信と再試行のメカニズムが提供されます。 イベントがイベント ハンドラーのエンドポイントによって受信されたことを Event Grid が確認できない場合、イベントは再配信されます。 詳細については、[Event Grid のメッセージの配信と再試行](delivery-retry.md)に関する記事を参照してください。

## <a name="batch-event-publishing"></a>バッチ イベントの発行
トピックを使用する場合、イベントは常に配列で発行される必要があります。 低スループットのシナリオでは、配列に含まれるイベントは 1 つだけです。 高ボリュームのユース ケースでは、効率性を高めるために、発行ごとに複数のイベントをバッチ処理することをお勧めします。 バッチのサイズは最大 1 MB に指定できます。 その場合でも、各イベントは 1 MB 以下にする必要があります。 詳細については、[バッチ イベントの配信](batch-event-delivery.md)に関する記事を参照してください。

## <a name="event-grid-on-kubernetes-components"></a>Kubernetes 上の Event Grid のコンポーネント

- **Event Grid オペレーター** により、[オペレーター パターン](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/)が実装されています。 それにより、Kubernetes の API サーバーに対して行われたコントロール プレーン要求の結果として発生する Event Grid リソースの状態変化が監視されます。 Event Grid のいずれかのリソースの状態に影響を与える要求がある場合、Event Grid オペレーターによりその状態が Event Grid ブローカーと同期されます。
- **Event Grid ブローカー** は、コントロール プレーンとデータ プレーンの両方の操作として機能します。

   コントロール プレーン サービスとしての役割は、Event Grid の状態を、Event Grid オペレーターによって通知された目的の状態にすることです。 たとえば、新しいトピックを作成する要求が行われると、その要求が満たされ、サービス メタデータが更新されます。

   データ プレーン サービスとしては、すべてのイベント発行要求を処理し、イベント サブスクリプションで構成されている送信先にイベントを配信します。

## <a name="next-steps"></a>次の手順
まず初めに、「[トピックとサブスクリプションを作成する](create-topic-subscription.md)」を参照してください。