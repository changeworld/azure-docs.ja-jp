---
title: Azure Active Directory で最近削除されたユーザーを復元するか完全に削除する | Microsoft Docs
description: Azure Active Directory で、削除されたユーザーを復元する方法、復元可能なユーザーを表示する方法、およびユーザーを完全に削除する方法
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 05/09/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 8dbb546954f8eee0bf997b3d2f4f92d67c0b7869
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930876"
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>Azure Active Directory で削除されたユーザーを復元する

この記事には、以前削除されたユーザーを復元する方法および完全に削除する方法が含まれます。 Azure Active Directory (Azure AD) でユーザーを削除すると、削除されたユーザー削除日から 30 日間は保持されます。 この期間中にはユーザーとそのプロパティを復元できます。 

> [!wARNING]
> 完全に削除されたユーザーは復元できません。


## <a name="how-to-restore-a-recently-deleted-user"></a>最近削除されたユーザーを復元する方法
ユーザーが最近削除された場合、すべてのディレクトリ情報は保持されています。 ユーザーが復元されると、その情報も復元されます。

1. [Azure AD 管理センター](https://aad.portal.azure.com)で、**[ユーザー]** &gt; **[削除済みのユーザー]** を選びます。 
2. 最近削除されたユーザーを 1 つまたは複数選択します。
3. **[ユーザーの復元]** を選びます。

## <a name="how-to-permanently-delete-a-recently-deleted-user"></a>最近削除されたユーザーを完全に削除する方法

1. [Azure AD 管理センター](https://aad.portal.azure.com)で、**[ユーザー]** &gt; **[削除済みのユーザー]** を選びます。 
2. 最近削除されたユーザーを 1 つまたは複数選択します。
3. **[完全に削除]** を選びます。

## <a name="required-permissions"></a>必要なアクセス許可
ユーザーを復元するための十分なアクセス許可は次のとおりです。

役割  | アクセス許可 
--------- | ---------
会社の管理者<p>パートナー レベル 1 のサポート<p>パートナー レベル 2 のサポート<p>ユーザー アカウント管理者 | 削除されたユーザーを復元できます。 
会社の管理者<p>パートナー レベル 1 のサポート<p>パートナー レベル 2 のサポート<p>ユーザー アカウント管理者 | ユーザーを完全に削除できます。

## <a name="next-steps"></a>次の手順
次の記事は、Azure Active Directory ユーザー管理に関する追加情報を提供します。

* [クイックスタート: Azure Active Directory でのユーザーの追加または削除](add-users-azure-active-directory.md)
* [ユーザー プロファイルの管理](active-directory-users-profile-azure-portal.md)
* [別のディレクトリからのゲスト ユーザーの追加](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Azure AD でのロールへのユーザーの割り当て](active-directory-users-assign-role-azure-portal.md)
