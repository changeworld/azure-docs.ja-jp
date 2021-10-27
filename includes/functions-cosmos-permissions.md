---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: d95d50d6e5f69922856e6a3045dfccdf91ddbf1a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994333"
---
実行時にデータベース アカウントへのアクセスを提供するロールの割り当てを作成する必要があります。 [所有者](../articles/role-based-access-control/built-in-roles.md#owner)のような管理ロールでは十分ではありません。 次の表は、通常の操作で Cosmos DB の拡張機能を使用するときに推奨される組み込みのロールを示しています。 アプリケーションでは、記述したコードに基づいて追加のアクセス許可が必要になる場合があります。

| [バインドの種類]   | 組み込みロールの例                |
|----------------|---------------------------------------|
| トリガー        | [Cosmos DB 組み込みデータ リーダー]      |
| 出力バインド | [Cosmos DB 組み込みデータ共同作成者] |


[Cosmos DB 組み込みデータ リーダー]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions
[Cosmos DB 組み込みデータ共同作成者]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions