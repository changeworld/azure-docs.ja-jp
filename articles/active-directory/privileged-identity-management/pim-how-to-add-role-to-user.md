---
title: PIM で Azure AD ディレクトリ ロールを割り当てる | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ディレクトリ ロールを割り当てる方法を説明します。
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
ms.openlocfilehash: 33bfe28bf612c47c9f42345dabccc017337c3d45
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190158"
---
# <a name="assign-azure-ad-directory-roles-in-pim"></a>PIM で Azure AD ディレクトリ ロールを割り当てる

全体管理者は、Azure Active Directory (Azure AD) を使用して、**永続的**なディレクトリ ロールの割り当てを行うことができます。 こうしたロールの割り当ては、[Azure portal](../users-groups-roles/directory-assign-admin-roles.md) または [PowerShell のコマンド](/powershell/module/azuread#directory_roles) を使用して作成できます。

永続的なディレクトリ ロールの割り当ては、特権ロール管理者が Azure AD Privileged Identity Management (PIM) サービスを使用して行うこともできます。 さらに、特権ロール管理者は、ユーザーをディレクトリ ロールの**候補**にすることもできます。 管理者候補は必要なときにロールをアクティブ化できます。作業が完了すると、そのアクセス許可は期限切れになります。 PIM を使って管理できるロールについては、「[PIM で管理できる Azure AD のディレクトリ ロール](pim-roles.md)」をご覧ください。

## <a name="make-a-user-eligible-for-a-role"></a>ユーザーをロールの候補にする

ユーザーを Azure AD ディレクトリ ロールの候補にするには、次の手順に従います。

1. [特権ロール管理者](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)ロールのメンバー ユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

    PIM を管理するためのアクセス権を別の管理者に付与する方法については、「[PIM を管理する他の管理者にアクセス権を付与する](pim-how-to-give-access-to-pim.md)」をご覧ください。

1. **[Azure AD Privileged Identity Management]** を開きます。

    Azure portal で PIM をまだ開始していない場合は、「[PIM の使用を開始する](pim-getting-started.md)」をご覧ください。

1. **[Azure AD ディレクトリ ロール]** をクリックします。

1. **[ロール]** または **[メンバー]** をクリックします。

    ![Azure AD ディレクトリ ロール](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. **[メンバーの追加]** をクリックして [管理されるメンバーの追加] を開きます。

1. **[Select a role]\(ロールを選択する\)** をクリックして、管理するロールをクリックし、**[選択]** をクリックします。

    ![ロールを選択する](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. **[メンバーの選択]** をクリックしてロールに割り当てるユーザーを選択し、**[選択]** をクリックします。

    ![ロールを選択する](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. [管理されるメンバーの追加] で **[OK]** をクリックし、ロールにユーザーを追加します。

1. ロールの一覧で、割り当てたロールをクリックしてメンバーの一覧を表示します。

     ロールが割り当てられると、選択したユーザーが、ロールの**候補**としてメンバー リストに表示されます。

    ![ロールの候補となるユーザー](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. これで、ユーザーがロールの候補者になりました。「[PIM で自分の Azure AD ディレクトリ ロールをアクティブにする](pim-how-to-activate-role.md)」の指示に従って、ロールをアクティブ化できることをユーザーに通知します。

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

- [PIM で Azure AD ディレクトリ ロールの設定を構成する](pim-how-to-change-default-settings.md)
- [PIM で Azure リソース ロールを割り当てる](pim-resource-roles-assign-roles.md)
