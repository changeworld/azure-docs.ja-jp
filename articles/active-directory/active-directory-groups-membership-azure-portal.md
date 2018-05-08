---
title: Azure Active Directory で自分のグループが属するグループを管理する | Microsoft Docs
description: Azure Active Directory でグループに他のグループを含めることができます。 ここでは、これらのメンバーシップを管理する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: c3d5b4382ae107bc9992b11a8ed2975e4ef9caca
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>Azure Active Directory テナントでグループが属するグループを管理する
Azure Active Directory でグループに他のグループを含めることができます。 ここでは、これらのメンバーシップを管理する方法について説明します。

## <a name="how-do-i-find-the-groups-of-which-my-group-is-a-member"></a>自分のグループが属するグループを見つける方法
1. ディレクトリのグローバル管理者のアカウントで [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
2. **[ユーザーとグループ]** を選択します。

   ![ユーザーとグループを開く画像](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
1. **[すべてのグループ]** を選択します。

   ![グループの選択の画像](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
1. グループを選びます。
2. **[グループ メンバーシップ]** を選びます。

   ![グループ メンバーシップを開く画像](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
1. グループを別のグループのメンバーとして追加するには、**[グループ - グループ メンバーシップ]** ブレードで、**[追加]** をクリックします。
2. **[グループの選択]** ブレードでグループを選択し、ブレードの下部にある **[選択]** をクリックします。 グループは、一度に 1 つのグループにのみ追加できます。 **[ユーザー]** ボックスでは、入力内容とユーザー名またはデバイス名の一部との一致に基づいて表示がフィルター処理されます。 このボックスではワイルドカード文字は使用できません。

   ![グループ メンバーシップを追加する](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. 別のグループのメンバーであるグループを削除するには、 **[グループ - グループ メンバーシップ]** ブレードでグループを選択します。
9. **[削除]** コマンドを選び、表示されたメッセージで削除を確定します。

   ![メンバーシップの [削除] コマンド](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. グループのグループ メンバーシップの変更が完了したら、 **[保存]** をクリックします。

## <a name="additional-information"></a>追加情報
次の記事は、Azure Active Directory に関する追加情報を示します。

* [既存のグループの表示](active-directory-groups-view-azure-portal.md)
* [新しいグループの作成とメンバーの追加](active-directory-groups-create-azure-portal.md)
* [グループの設定の管理](active-directory-groups-settings-azure-portal.md)
* [グループのメンバーの管理](active-directory-groups-members-azure-portal.md)
* [グループ内のユーザーの動的ルールの管理](active-directory-groups-dynamic-membership-azure-portal.md)
