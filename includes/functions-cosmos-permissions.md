---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2021
ms.author: mahender
ms.openlocfilehash: 15f68c6339135139f6f6fcbf335306f6edf2a3c6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257132"
---
実行時にデータベース アカウントへのアクセスを提供するロールの割り当てを作成する必要があります。 [所有者](../articles/role-based-access-control/built-in-roles.md#owner)のような管理ロールでは十分ではありません。 次の表は、通常の操作で Cosmos DB の拡張機能を使用するときに推奨される組み込みのロールを示しています。 アプリケーションでは、記述したコードに基づいて追加のアクセス許可が必要になる場合があります。

| [バインドの種類]   | 組み込みロールの例                |
|----------------|---------------------------------------|
| トリガー        | [Cosmos DB 組み込みデータ共同作成者] |
| 入力バインド  | [Cosmos DB 組み込みデータ リーダー]      |
| 出力バインド | [Cosmos DB 組み込みデータ共同作成者] |


[Cosmos DB 組み込みデータ リーダー]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions
[Cosmos DB 組み込みデータ共同作成者]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions