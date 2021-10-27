---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: e9897a9fb28f3ea9489e80b0dd4bb814c836b23b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992738"
---
実行時にキューへのアクセスを提供するロールの割り当てを作成する必要があります。 [所有者](../articles/role-based-access-control/built-in-roles.md#owner)のような管理ロールでは十分ではありません。 次の表は、通常の操作で Queue Storage の拡張機能を使用するときに推奨される組み込みロールを示しています。 アプリケーションでは、記述したコードに基づいて追加のアクセス許可が必要になる場合があります。

| [バインドの種類]   | 組み込みロールの例                                                |
|----------------|-----------------------------------------------------------------------|
| トリガー        | [ストレージ キュー データ閲覧者]、[ストレージ キュー データのメッセージ プロセッサ]   |
| 出力バインド | [ストレージ キュー データ共同作成者]、[ストレージ キュー データのメッセージ送信者] |

[ストレージ キュー データ閲覧者]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader
[ストレージ キュー データのメッセージ プロセッサ]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor
[ストレージ キュー データ メッセージ送信者]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender
[ストレージ キュー データ共同作成者共同作成者]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor
