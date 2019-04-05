---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9402c4b24c9d64b4b69d750fbd19de40cda396f3
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57218092"
---
Azure には、ストレージ データにアクセスするために、以下の組み込み RBAC ロールが用意されています。

- [ストレージ BLOB データ所有者 (プレビュー)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview):Azure Data Lake Storage Gen2 (プレビュー) で、所有権の設定と POSIX アクセス制御の管理に使用します。 詳細については、[Azure Data Lake Storage Gen2 でのアクセス制御](../articles/storage/blobs/data-lake-storage-access-control.md)に関するページを参照してください。
- [ストレージ BLOB データ共同作成者 (プレビュー)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview):BLOB ストレージ リソースの読み取り/書き込み/削除のアクセス許可を付与するために使用します。
- [ストレージ BLOB データ閲覧者 (プレビュー)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview):BLOB ストレージ リソースの読み取り専用アクセス許可を付与するために使用します。
- [ストレージ キュー データ共同作成者 (プレビュー)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview):Azure キューの読み取り/書き込み/削除のアクセス許可を付与するために使用します。
- [ストレージ キュー データ閲覧者 (プレビュー)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview):Azure キューの読み取り専用アクセス許可を付与するために使用します。

Azure Storage の組み込みロールの定義方法については、「[ロール定義について](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview)」を参照してください。 カスタム RBAC ロールの作成の詳細については、[Azure のロールベースのアクセス制御のためにカスタム ロールを作成する方法](../articles/role-based-access-control/custom-roles.md)に関するページを参照してください。 
