---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: e8b274cd3d9398694661b06a5a1c9e13e42c6c89
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130137385"
---
実行時に "AzureWebJobsStorage" のストレージ アカウントにアクセスできるようにするロールの割り当てを作成する必要があります。 [所有者](../articles/role-based-access-control/built-in-roles.md#owner)のような管理ロールでは十分ではありません。 [ストレージ BLOB データ所有者]ロールでは、Functions ホスト ストレージの基本ニーズが対処されます。ランタイムでは、BLOB の読み取りと書き込みの両方が必要であり、また、コンテナーを作成できる必要があります。 BLOB トリガーの使用時のように、[ストレージ キュー データ共同作成者]も必要になることがあります。 他の目的で "AzureWebJobsStorage" を使用する場合は、追加のアクセス許可が必要な場合があります。

[ストレージ BLOB データ所有者]: ../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner
[ストレージ キュー データ共同作成者共同作成者]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor