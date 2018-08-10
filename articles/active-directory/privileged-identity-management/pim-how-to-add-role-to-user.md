---
title: Azure AD PIM を使用したユーザーへのディレクトリ ロールの割り当て | Microsoft Docs
description: Azure Active Directory Privileged Identity Management と Azure portal を使用して、ユーザーにディレクトリ ロールを割り当てる方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 07/23/2018
ms.author: rolyon
ms.openlocfilehash: 1aede38cabba7f9811f2b9320bc1e9a9da857f08
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621815"
---
# <a name="assign-directory-roles-to-users-using-azure-ad-pim"></a>Azure AD PIM を使用したユーザーへのディレクトリ ロールの割り当て

全体管理者は、Azure Active Directory (Azure AD) を使用して、**永続的**なディレクトリ ロールの割り当てを行うことができます。 こうしたロールの割り当ては、[Azure portal](../users-groups-roles/directory-assign-admin-roles.md) または [PowerShell のコマンド](/powershell/module/azuread#directory_roles) を使用して作成できます。

永続的なディレクトリ ロールの割り当ては、特権ロール管理者が Azure AD Privileged Identity Management (PIM) サービスを使用して行うこともできます。 さらに、特権ロール管理者は、ユーザーをディレクトリ ロールの**候補**にすることもできます。 管理者候補は必要なときにロールをアクティブ化できます。作業が完了すると、そのアクセス許可は期限切れになります。 PIM を使って管理できるロールについては、[Azure AD PIM を使って管理できるディレクトリ ロール](pim-roles.md)に関するページを参照してください。

## <a name="make-a-user-eligible-for-a-role"></a>ユーザーをロールの候補にする

ユーザーを Azure AD ディレクトリ ロールの候補にするには、次の手順に従います。

1. [特権ロール管理者](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)ロールのメンバー ユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

    PIM を管理するためのアクセス権を別のユーザーに与える方法については、[PIM のアクセス権を付与する方法](pim-how-to-give-access-to-pim.md)に関するページを参照してください。

1. **[Azure AD Privileged Identity Management]** を開きます。

    まだ Azure portal で PIM を有効にしていない場合は、「[Azure AD Privileged Identity Management の使用](pim-getting-started.md)」を参照してください。

1. **[Azure AD ディレクトリ ロール]** をクリックします。

1. **[ロール (新規)]** または **[メンバー]** をクリックします。

    ![Azure AD ディレクトリ ロール](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. **[メンバーの追加]** をクリックして [管理されるメンバーの追加] を開きます。

1. **[Select a role]\(ロールを選択する\)** をクリックして、管理するロールをクリックし、**[選択]** をクリックします。

    ![ロールを選択する](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. **[メンバーの選択]** をクリックしてロールに割り当てるユーザーを選択し、**[選択]** をクリックします。

    ![ロールを選択する](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. [管理されるメンバーの追加] で **[OK]** をクリックし、ロールにユーザーを追加します。

     ロールが割り当てられると、選択したユーザーが、ロールの**候補**としてメンバー リストに表示されます。

    ![ロールの候補となるユーザー](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. これで、ユーザーがロールの候補者になりました。[ロールをアクティブ化または非アクティブ化する方法](pim-how-to-activate-role.md)に関するページの指示に従ってロールをアクティブ化できることを、ユーザーに通知してください。

    管理者候補は、アクティブ化時に Azure Multi-Factor Authentication (MFA) への登録が求められます。 ユーザーが MFA に登録できない場合、または Microsoft アカウント (通常は @outlook.com) を使用している場合は、すべてのロールでこのユーザーを永続化する必要があります。

## <a name="make-a-role-assignment-permanent"></a>ロールの割り当てを永続化する

既定では、新しいユーザーは、あくまでディレクトリ ロールの候補でしかありません。 ロールの割り当てを永続化する場合は、次の手順に従ってください。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ディレクトリ ロール]** をクリックします。

1. **[メンバー]** をクリックします。

    ![メンバーの一覧](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 永続化する**候補**ロールをクリックします。

1. **[More]\(その他\)** をクリックして **[永続化]** をクリックします。

    ![ロールの割り当てを永続化する](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    ロールが**永続**として表示されています。

    ![永続化されたメンバーの一覧が変化](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>ユーザーをロールから削除する

ロールの割り当てからユーザーを削除することはできますが、必ず、永続的な全体管理者が少なくとも 1 人は常に存在するようにしてください。 どのユーザーに引き続きロールの割り当てが必要であるかわからない場合は、[ロールのアクセス レビューを開始](pim-how-to-start-security-review.md)してください。

特定のユーザーをディレクトリ ロールから削除するには、次の手順に従います。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ディレクトリ ロール]** をクリックします。

1. **[メンバー]** をクリックします。

    ![メンバーの一覧](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 削除するロールの割り当てをクリックします。

1. **[More]\(その他\)** をクリックして **[削除]** をクリックします。

    ![ロールの削除](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. 確認を求めるメッセージで **[はい]** をクリックします。

    ![ロールの削除](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    ロールの割り当てが削除されました。

## <a name="next-steps"></a>次の手順
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
