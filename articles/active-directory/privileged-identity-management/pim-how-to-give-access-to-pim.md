---
title: Privileged Identity Management へのアクセス権を付与する方法 - Azure | Microsoft Docs
description: Azure Active Directory Privileged Identity Management 拡張機能でユーザーにロールを追加して、ユーザーが PIM を管理できるようにする方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: ade3939057669e7f9979001db4376fe61c2f4ad9
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39623070"
---
# <a name="giving-access-to-manage-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management を管理するためのアクセス権の付与
組織の Azure AD Privileged Identity Management (PIM) を有効にするグローバル管理者は、自動的にロールの割り当てを取得し、PIM にアクセスします。 ただし、他のグローバル管理者も含めて、既定で書き込みアクセス権を得るユーザーは他にはいません。 その他のグローバル管理者、セキュリティ管理者、およびセキュリティ リーダーは、Azure AD PIM に対する読み取り専用アクセス権を持ちます。 PIM へのアクセス権を付与するには、最初のユーザーが他のユーザーに **特権ロール管理者** ロールを割り当てます。

> [!NOTE]
> Azure AD PIM を管理するには、Azure MFA が必要です。 Microsoft アカウントは Azure MFA に登録できないため、Microsoft アカウントでサインインしたユーザーは Azure AD PIM にアクセスできません。
> 
> 

1 人のユーザーがロックアウトされたり、アカウントが削除されたりしたときのために、特権ロール管理者ロールを割り当てられたユーザーが常に少なくとも 2 人いるようにしてください。

## <a name="give-another-user-access-to-manage-pim"></a>PIM を管理するためのアクセス権を別のユーザーに付与する
1. [Azure Portal](https://portal.azure.com/) にサインインし、ダッシュボードで **Azure AD Privileged Identity Management** アプリを選択します。
2. **[特権ロールの管理]**  >  **[特権ロール管理者]**  >  **[追加]** の順に選択します。
   
    ![特権ロール管理者の追加 - スクリーンショット](./media/pim-how-to-give-access-to-pim/PIM_add_PRA.png)
3. [管理対象ユーザーの追加] ブレードで、手順 1 は既に完了しています。 手順 2、 **[ユーザーを選択する]** を選択し、追加するユーザーを検索します。
   
    ![ユーザーの選択 - スクリーンショット](./media/pim-how-to-give-access-to-pim/PIM_select_users.png)
4. 検索結果からユーザーを選択し、 **[実行]** をクリックします。
5. **[OK]** をクリックして選択を保存します。 選択したユーザーが特権ロール管理者の一覧に表示されます。
   
   * 新しいロールを他のユーザーに割り当てるたびに、ユーザーは自動的に対象として設定され、ロールをアクティブ化します。 ロールでユーザーを永続的にする場合は、一覧のユーザーをクリックします。 ユーザー情報メニューで **[永続的にする]** を選択します。
6. [Azure AD Privileged Identity Management の使用](pim-getting-started.md)へのリンクをユーザーに送信します。

## <a name="remove-another-users-access-rights-for-managing-pim"></a>別のユーザーに付与された、PIM を管理するためのアクセス権を削除する
特権ロール管理者ロールからユーザーを削除する際は、割り当てられているユーザーの人数が削除後も 2 人以上になるように、必ず事前に確認してください。

1. PIM ダッシュボードで、 **[特権ロール管理者]** ロールをクリックします。  そのロールの現在のユーザー一覧が表示されます。
2. ユーザーの一覧でユーザーをクリックします。
3. **[削除]** をクリックします。  確認メッセージが表示されます。
4. **[はい]** をクリックすると、ロールからユーザーが削除されます。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>次の手順
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
