---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 026717dff2b6883eb643497dec91226e4afe8133
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66115575"
---
Azure では、BLOB とキューのデータへの Azure AD と OAuth を使ったアクセスを承認するために、次の組み込み RBAC ロールが提供されています。

- [ストレージ BLOB データ所有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner):Azure Data Lake Storage Gen2 (プレビュー) で、所有権の設定と POSIX アクセス制御の管理に使用します。 詳細については、[Azure Data Lake Storage Gen2 でのアクセス制御](../articles/storage/blobs/data-lake-storage-access-control.md)に関するページを参照してください。
- [ストレージ BLOB データ共同作成者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor):BLOB ストレージ リソースの読み取り/書き込み/削除のアクセス許可を付与するために使用します。
- [ストレージ BLOB データ閲覧者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader):BLOB ストレージ リソースの読み取り専用アクセス許可を付与するために使用します。
- [ストレージ キュー データ共同作成者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor):Azure キューの読み取り/書き込み/削除のアクセス許可を付与するために使用します。
- [ストレージ キュー データ閲覧者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader):Azure キューの読み取り専用アクセス許可を付与するために使用します。
- [ストレージ キュー データのメッセージ プロセッサ](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor):Azure Storage キューのピーク、メッセージの取得と削除のアクセス許可を付与するために使用します。
- [ストレージ キュー データ メッセージ送信者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender):Azure Storage キューへのメッセージの追加アクセス許可を付与するために使用します。

> [!NOTE]
> RBAC ロールの割り当ての反映には最大で 5 分かかる場合があることに留意してください。
