---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 32184e2b836aa881dc3175bb0790fc9cbbca1df7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996442"
---
実行時に BLOB コンテナーへのアクセスを提供するロールの割り当てを作成する必要があります。 [所有者](../articles/role-based-access-control/built-in-roles.md#owner)のような管理ロールでは十分ではありません。 次の表は、通常の操作で Blob Storage の拡張機能を使用するときに推奨される組み込みロールを示しています。 アプリケーションでは、記述したコードに基づいて追加のアクセス許可が必要になる場合があります。

| [バインドの種類]   | 組み込みロールの例                |
|----------------|---------------------------------------|
| トリガー        | [ストレージ BLOB データ所有者]<sup>1</sup> |
| 入力バインド  | [ストレージ BLOB データ閲覧者]            |
| 出力バインド | [ストレージ BLOB データ所有者]             |

<sup>1</sup> 一部の構成では、BLOB トリガーに[ストレージ キュー データ共同作成者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)が追加で必要になる場合があります。

[ストレージ BLOB データ閲覧者]: ../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader
[ストレージ BLOB データ所有者]: ../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner
