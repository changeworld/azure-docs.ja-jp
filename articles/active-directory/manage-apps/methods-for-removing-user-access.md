---
title: アプリケーションへのユーザー アクセスの削除方法
description: Azure Active Directory でアプリケーションへのユーザーのアクセスを削除する方法について説明します
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: davidmu
ms.reviewer: phsignor
ms.openlocfilehash: b0b3e1074df0a1067736900dbce7d7521414c1bc
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549757"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>アプリケーションへのユーザー アクセスの削除方法

この記事では、アプリケーションへのユーザーのアクセスを削除する方法について説明します。

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>アプリケーションへの特定のユーザーまたはグループの割り当てを削除する必要がある

アプリケーションへのユーザーまたはグループの割り当てを削除するには、「[Azure Active Directory プレビューでエンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](./assign-user-or-group-access-portal.md)」の記事に示されている手順に従います。

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>すべてのユーザーに対してアプリケーションへのアクセスをすべて無効にする必要がある

アプリケーションへのすべてのユーザーのサインインを無効にするには、「[Azure Active Directory プレビューでエンタープライズ アプリケーションのユーザー サインインを無効にする](./disable-user-sign-in-portal.md)」の記事に示されている手順に従います。

## <a name="i-want-to-delete-an-application-entirely"></a>アプリケーションを完全に削除する必要がある

[アプリケーション管理に関するクイックスタート シリーズ](delete-application-portal.md)には、Azure Active Directory テナントからのアプリケーションの削除に関するガイダンスが含まれています。

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>すべてのアプリケーションに対して今後のユーザーの同意操作をすべて無効にする必要がある

ディレクトリ全体でユーザーの同意を無効にすると、エンドユーザーはすべてのアプリケーションに同意できなくなります。 管理者は、依然としてユーザーに代わって同意できます。 アプリケーションの同意と、これを行う理由または行わない理由の詳細については、「[ユーザーおよび管理者の同意について](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)」をご覧ください。 「[アクセス許可と同意](../develop/v2-permissions-and-consent.md)」も参照してください。

**ディレクトリ全体で今後のユーザーの同意動作をすべて無効にする** には、次の手順に従います。

1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者** としてサインインします。

2. **[Azure Active Directory 拡張機能]** を開きます

3. ナビゲーション メニューで **[エンタープライズ アプリケーション]** をクリックします。

4. **[ユーザー設定]** をクリックします。

5. **[Users can allow apps to access company data on their behalf]**(ユーザーはアプリが代わりに企業データにアクセスすることを許可できる) トグルを **[いいえ]** に設定して、[保存] ボタンをクリックします。

## <a name="next-steps"></a>次のステップ

[アプリへのアクセスの管理](what-is-access-management.md)
