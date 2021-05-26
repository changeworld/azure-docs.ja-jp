---
title: Kubernetes 上の Azure Event Grid　-　イベント スキーマ
description: この記事では、Azure Arc for Kubernetes 上の Event Grid でサポートされているイベント スキーマについて説明します。
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 78576362ff3258344b6a340762799dfa1a52f623
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110388232"
---
# <a name="event-schemas-in-event-grid-on-kubernetes"></a>Kubernetes 上の Event Grid のイベント スキーマ
Kubernetes 上の Event Grid は、JSON 形式でイベントを受け入れて配信します。 これは [Cloud Events 1.0 スキーマ仕様](https://github.com/cloudevents/spec/blob/v1.0/spec.md)をサポートし、これは Event Grid にイベントを発行するときに使用するスキーマです。 

[!INCLUDE [event-grid-preview-feature-note.md](../../../includes/event-grid-preview-feature-note.md)]



## <a name="cloudevent-schema"></a>CloudEvent スキーマ
[CloudEvents](https://cloudevents.io/) は、イベント データを記述するためのオープンな仕様です。 これを使用すると、イベントを発行したり使用したりするための共通のイベント スキーマを提供して、相互運用性を簡略化することができます。 必須のコンテキスト属性の詳細については、[CloudEvents の仕様](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope)を参照してください。

## <a name="example--event-using-cloudevents-schema"></a>例 — CloudEvents スキーマを使用したイベント

```json
[{
      "specversion": "1.0",
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

## <a name="next-steps"></a>次の手順
Azure Arc for Kubernetes 上の Event Grid でサポートされる宛先とハンドラーについて詳しくは、[Kubernetes 上の Event Grid - イベント ハンドラー](event-handlers.md)に関する記事を参照してください。