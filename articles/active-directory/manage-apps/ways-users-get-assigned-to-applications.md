---
title: Azure Active Directory でユーザーをアプリに割り当てる方法を理解する
description: ID 管理のために Azure Active Directory を使用するアプリにユーザーを割り当てる方法について説明します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 411e9a6e059075dfc2928ed09c0c604d600fd9be
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604157"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Azure Active Directory でユーザーをアプリに割り当てる方法を理解する
この記事では、ユーザーがテナントでアプリケーションに割り当てられる方法について説明します。

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>ユーザーが Azure AD でアプリケーションに割り当てられる方法
ユーザーがアプリケーションにアクセスするには、まず何らかの方法でそのユーザーを割り当てる必要があります。 割り当ては管理者またはビジネス デリゲートによって行われますが、場合によってはユーザー自身で実行できることもあります。 次に、ユーザーをアプリケーションに割り当てることができる方法について説明します。

*  管理者が、アプリケーションに直接[ユーザーを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)。
*  管理者が、アプリケーションにユーザーがメンバーとなっている[グループを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)。次のグループが含まれます。
    * オンプレミスから同期されたグループ
    * クラウドで作成された静的なセキュリティ グループ
    * クラウドで作成された[動的なセキュリティ グループ](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)
    * クラウドで作成された Microsoft 365 グループ
    * [すべてのユーザー](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups) グループ
*  管理者が [[アプリケーションのセルフ サービス アクセス]](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) を有効にして、**ビジネス承認なし**でユーザーが[マイ アプリ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)の **[アプリの追加]** 機能を使用してアプリケーションを追加することを許可します
*  管理者が [[アプリケーションのセルフ サービス アクセス]](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) を有効にして、ユーザーが[マイ アプリ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)の **[アプリの追加]** 機能を使用してアプリケーションを追加することを許可しますが、**選択された一連のビジネス承認者からの事前の承認があった**場合に限ります
*  管理者が [[セルフサービスによるグループ管理]](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) を有効にして、アプリケーションが**ビジネス承認なし**で割り当てられているグループにユーザーが参加することを許可します。
*  管理者が [[セルフサービスによるグループ管理]](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) を有効にして、アプリケーションが割り当てられているグループにユーザーが参加することを許可しますが、**選択された一連のビジネス承認者からの事前の承認があった**場合に限ります。
*  管理者は、[Microsoft 365](https://products.office.com/) などのファースト パーティ アプリケーションのライセンスを直接ユーザーに割り当てます。
*  管理者は、[Microsoft 365](https://products.office.com/) などのファースト パーティ アプリケーションのライセンスを、ユーザーがメンバーとなっているグループに割り当てます。
*  [管理者がすべてのユーザーによるアプリケーションの使用に同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)したら、ユーザーはアプリケーションにサインインします。
* ユーザーはアプリケーションにサインインすることで、[アプリケーションに同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)します。

## <a name="next-steps"></a>次のステップ
* [アプリケーション管理のクイックスタート シリーズ](view-applications-portal.md)
* [アプリケーション管理とは](what-is-application-management.md)
* [シングル サインオンとは](what-is-single-sign-on.md)
