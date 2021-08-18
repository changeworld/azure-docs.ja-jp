---
title: Kubernetes 上の Azure Event Grid　-　イベント スキーマ
description: この記事では、Azure Arc for Kubernetes 上の Event Grid でサポートされているイベント スキーマについて説明します。
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 4ec482a0cf8c0b418d2cd6ec11d1afd56273b681
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2021
ms.locfileid: "112415626"
---
# <a name="event-schemas-in-event-grid-on-kubernetes"></a>Kubernetes 上の Event Grid のイベント スキーマ
Kubernetes 上の Event Grid は、JSON 形式でイベントを受け入れて配信します。 これは [Cloud Events 1.0 スキーマ仕様](https://github.com/cloudevents/spec/blob/v1.0/spec.md)をサポートし、これは Event Grid にイベントを発行するときに使用するスキーマです。 

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]



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