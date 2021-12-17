---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: b4ce7e128304aa1befd7029ed2a692b1d7f391f3
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137406"
---
実行時にトピックとキューへのアクセスを提供するロールの割り当てを作成する必要があります。 ロール割り当てのスコープは、特定のキューまたはトピックに制限されない、Service Bus 名前空間に対するものである必要があります。 [所有者](../articles/role-based-access-control/built-in-roles.md#owner)のような管理ロールでは十分ではありません。 次の表は、通常の操作で Service Bus の拡張機能を使用するときに推奨される組み込みロールを示しています。 アプリケーションでは、記述したコードに基づいて追加のアクセス許可が必要になる場合があります。

| [バインドの種類]   | 組み込みロールの例                                            |
|----------------|-------------------------------------------------------------------|
| トリガー        | [Azure Service Bus データ受信者]、[Azure Service Bus データ所有者] |
| 出力バインド | [Azure Service Bus データ送信者]                                   |

[Azure Service Bus データ受信者]: ../articles/role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver
[Azure Service Bus データ送信者]: ../articles/role-based-access-control/built-in-roles.md#azure-service-bus-data-sender
[Azure Service Bus データ所有者]: ../articles/role-based-access-control/built-in-roles.md#azure-service-bus-data-owner
