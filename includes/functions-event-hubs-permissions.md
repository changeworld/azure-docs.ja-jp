---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: b0451d7a3f14002ec53cdb410144c6e69f1337a9
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137390"
---
実行時にイベント ハブへのアクセスを提供するロールの割り当てを作成する必要があります。 ロールの割り当てのスコープは、イベント ハブ自体ではなく、Event Hubs 名前空間に対するものである必要があります。 [所有者](../articles/role-based-access-control/built-in-roles.md#owner)のような管理ロールでは十分ではありません。 次の表は、通常の操作で Event Hubs の拡張機能を使用するときに推奨される組み込みのロールを示しています。 アプリケーションでは、記述したコードに基づいて追加のアクセス許可が必要になる場合があります。

| [バインドの種類]   | 組み込みロールの例                                          |
|----------------|-----------------------------------------------------------------|
| トリガー        | [Azure Event Hubs データ受信者]、[Azure Event Hubs データ所有者] |
| 出力バインド | [Azure Event Hubs データ送信者]                                  |

[Azure Event Hubs データ受信者]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver
[Azure Event Hubs データ送信者]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender
[Azure Event Hubs データ所有者]: ../articles/role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner