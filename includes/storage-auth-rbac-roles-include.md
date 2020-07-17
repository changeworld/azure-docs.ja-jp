---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d73bab76860abf3e9fa442dad44e1ddb66d147a3
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519567"
---
Azure では、BLOB とキューのデータへの Azure AD と OAuth を使ったアクセスを承認するために、次の組み込み RBAC ロールが提供されています。

- [ストレージ BLOB データ所有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner):Azure Data Lake Storage Gen2 で、所有権の設定と POSIX アクセス制御の管理に使用します。 詳細については、[Azure Data Lake Storage Gen2 でのアクセス制御](../articles/storage/blobs/data-lake-storage-access-control.md)に関するページを参照してください。
- [ストレージ BLOB データ共同作成者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor):BLOB ストレージ リソースの読み取り/書き込み/削除のアクセス許可を付与するために使用します。
- [ストレージ BLOB データ閲覧者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader):BLOB ストレージ リソースの読み取り専用アクセス許可を付与するために使用します。
- [ストレージ キュー データ共同作成者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor):Azure キューの読み取り/書き込み/削除のアクセス許可を付与するために使用します。
- [ストレージ キュー データ閲覧者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader):Azure キューの読み取り専用アクセス許可を付与するために使用します。
- [ストレージ キュー データのメッセージ プロセッサ](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor):Azure Storage キューのピーク、メッセージの取得と削除のアクセス許可を付与するために使用します。
- [ストレージ キュー データ メッセージ送信者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender):Azure Storage キューへのメッセージの追加アクセス許可を付与するために使用します。

データ サービスと管理サービスの両方に対する Azure Storage 用の組み込み RBAC ロールの詳細については、「[Azure RBAC の Azure 組み込みロール](../articles/role-based-access-control/built-in-roles.md#storage)」の「**ストレージ**」セクションを参照してください。 さらに、Azure でアクセス許可を提供するさまざまな種類のロールの詳細については、「[従来のサブスクリプション管理者ロール、Azure RBAC ロール、および Azure AD ロール](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)」を参照してください。

> [!NOTE]
> RBAC ロールの割り当ての反映には最大で 5 分かかる可能性があります。
>
> データ アクセスに対して明示的に定義されたロールによってのみ、セキュリティ プリンシパルによる BLOB データまたはキュー データへのアクセスが許可されます。 **所有者**、**共同作成者**、**ストレージ アカウント共同作成者**などのロールでは、セキュリティ プリンシパルによるストレージ アカウントの管理は許可されますが、そのアカウント内の BLOB データまたはキュー データへのアクセスは提供されません。
>
> Azure portal での BLOB または キュー データへのアクセスは、Azure AD アカウントまたはストレージ アカウント アクセス キーのいずれかを使用して承認できます。 詳細については、「[Azure portal を使用した BLOB またはキュー データへのアクセス](../articles/storage/common/storage-access-blobs-queues-portal.md)」を参照してください。
