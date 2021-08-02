---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/07/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a93e85114c46f6e0ce252bb4059836d62fadf20f
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904505"
---
Azure RBAC には、Azure AD と OAuth を使用してキュー データへのアクセスを承認するために、多数の組み込みロールが用意されています。 Azure Storage のデータ リソースへのアクセス許可を付与するロールの例を次に示します。

- [ストレージ キュー データ共同作成者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor):Azure キューの読み取り/書き込み/削除のアクセス許可を付与するために使用します。
- [ストレージ キュー データ閲覧者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader):Azure キューの読み取り専用アクセス許可を付与するために使用します。
- [ストレージ キュー データのメッセージ プロセッサ](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor):Azure Storage キューのピーク、メッセージの取得と削除のアクセス許可を付与するために使用します。
- [ストレージ キュー データ メッセージ送信者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender):Azure Storage キューへのメッセージの追加アクセス許可を付与するために使用します。

Azure RBAC ロールとそのアクセス許可を一覧表示する方法については、「[Azure ロールの定義を一覧表示する](../articles/role-based-access-control/role-definitions-list.md)」を参照してください。

データ アクセスに対して明示的に定義されたロールによってのみ、セキュリティ プリンシパルによるキュー データへのアクセスが許可されます。 **所有者**、**共同作成者**、**ストレージ アカウント共同作成者** などの組み込みロールでは、セキュリティ プリンシパルによるストレージ アカウントの管理は許可されますが、Azure AD を介したそのアカウント内のキュー データへのアクセスは提供されません。 ただし、ロールに **Microsoft.Storage/storageAccounts/listKeys/action** が含まれている場合、そのロールが割り当てられているユーザーは、アカウント アクセス キーを使った共有キーによる承認を介してストレージ アカウントのデータにアクセスできます。 詳細については、「[Azure portal で BLOB データへのアクセスの承認方法を選択する](../articles/storage/queues/authorize-data-operations-portal.md)」を参照してください。

データ サービスと管理サービスの両方に対する Azure Storage 用の Azure 組み込みロールの詳細については、「[Azure RBAC の Azure 組み込みロール](../articles/role-based-access-control/built-in-roles.md#storage)」の「**ストレージ**」セクションを参照してください。 さらに、Azure でアクセス許可を提供するさまざまな種類のロールの詳細については、「[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロール](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)」を参照してください。

> [!IMPORTANT]
> Azure ロールの割り当ての反映には最大で 30 分かかる可能性があります。
