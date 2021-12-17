---
title: Kubernetes 上の Azure Event Grid - イベントのフィルター処理
description: この記事では、Azure Event Grid サブスクリプションを作成するときにイベントをフィルター処理する方法について説明します。
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: e4dbdbb2f46546391b6e749a94bfa655bb9de45e
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2021
ms.locfileid: "112417345"
---
# <a name="event-grid-on-kubernetes---event-filtering-for-subscriptions"></a>Kubernetes 上の Event Grid - サブスクリプションのイベント フィルター処理
Kubernetes 上の Event Grid では、json ペイロードの任意のプロパティに対してフィルターを指定できます。 これらのフィルターは一連の AND 条件としてモデル化されており、外側の各条件には省略可能な内側の OR 条件があります。 AND 条件ごとに、次の値を指定します。

- OperatorType - 比較の種類。
- Key - フィルターを適用するプロパティへの json パス。
- Value - フィルターの実行対象となる参照値 (または) Values - フィルターの実行対象となる一連の参照値。

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]



## <a name="filter-by-event-type"></a>イベントの種類でフィルター処理する
既定では、イベント ソースに関するすべての[イベントの種類](event-schemas.md) (`type` 属性) がエンドポイントに送信されます。 特定のイベントの種類のみをご利用のエンドポイントに送信するように決めることができます。 イベントの種類でフィルター処理を行うための JSON 構文を次に示します。

```json
"filter": {
  "includedEventTypes": [
    "orderCreated",
    "orderUpdated"
  ]
}
```

上の例では、種類が `orderCreated` および`orderUpdated` のイベントのみサブスクライバー エンドポイントに送信されます。 

イベントの例を次に示します。

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

## <a name="filter-by-subject"></a>件名でフィルター処理する
件名によるシンプルなフィルター処理の場合は、件名の開始値または終了値を指定します。 件名でフィルター処理を行うための JSON 構文を次に示します。

```json
"filter": {
  "subjectBeginsWith": "/account/acct-123224/"
}
``` 

たとえば、上で構成されているフィルターでは、アカウント `acct-123224` に関連付けられているすべての注文がサブスクライバー エンドポイントに送信されます。 

イベントをトピックに発行する場合は、サブスクライバーがそのイベントに関心があるかどうかを簡単に知ることができるようにイベントの件名を作成してください。 サブスクライバーは、件名のプロパティを使用してイベントのフィルター処理またはルーティングを行います。 そのイベントが発生したパスを追加することにより、サブスクライバーがそのパスのセグメントでフィルター処理できるように考慮してください。 このパスにより、サブスクライバーはイベントを狭く、または幅広くフィルター処理できます。 /A/B/C のように件名に 3 つのセグメント パスを示した場合、サブスクライバーは最初のセグメント /A でフィルター処理して幅広い一連のイベントを取得できます。 これらのサブスクライバーは、/A/B/C や /A/D/E などの件名を持つイベントを取得します。 他のサブスクライバーは、/A/B でフィルター処理して、より絞り込んだ一連のイベントを取得できます。

## <a name="filter-by-values-in-event-data"></a>イベント データの値でフィルター処理する
高度なフィルター処理について詳しくは、[Azure での Event Grid に関する記事の高度なフィルター処理に関するセクション](../event-filtering.md)を参照してください。 Kubernetes 上の Event Grid では、次の機能と演算子はサポートされていません。 

- 受信イベントのキーで配列データをフィルター処理する
- [CloudEvents 拡張機能のコンテキスト属性](https://github.com/cloudevents/spec/blob/v1.0/documented-extensions.md)に対するフィルター処理を許可します。
- 次の演算子
    - StringNotContains
    - StringNotBeginsWith
    - StringNotEndsWith
    - NumberInRange
    - NumberNotInRange
    - IsNullOrUndefined
    - IsNotNull
    

## <a name="next-steps"></a>次の手順
Azure Arc for Kubernetes 上の Event Grid でサポートされる宛先とハンドラーについて詳しくは、[Kubernetes 上の Event Grid - イベント ハンドラー](event-handlers.md)に関する記事を参照してください。