---
title: Privileged Identity Management で Azure リソース ロールを割り当てる - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールを割り当てる方法を説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34051a31c6ccf69356f330d7c5ecb009f760857a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79231803"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management で Azure リソース ロールを割り当てる

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) では、組み込みの Azure リソース ロールと、以下を含むカスタム ロール (ただしこれらに限定されません) を管理できます。

- 所有者
- User Access Administrator
- Contributor
- セキュリティ管理者
- Security Manager

> [!NOTE]
> Azure AD でサブスクリプション管理を有効にする、所有者またはユーザー アクセス管理者サブスクリプション ロールに割り当てられたグループのユーザーまたはメンバー、および Azure AD 全体管理者には、既定でリソース管理者のアクセス許可が与えられます。 これらの管理者は、ロールの割り当て、ロール設定の構成、Azure リソース用 Privileged Identity Management を使用したアクセスの確認ができます。 リソース管理者のアクセス許可がないユーザーは、リソース用 Privileged Identity Management を管理できません。 [Azure リソースの組み込みロール](../../role-based-access-control/built-in-roles.md)の一覧をご確認ください。

## <a name="assign-a-role"></a>ロールの割り当て

ユーザーを Azure リソース ロールの候補にするには、次の手順を実行します。

1. [特権ロール管理者](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)ロールのメンバー ユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

    Privileged Identity Management を管理するためのアクセス権を別の管理者に付与する方法については、「[Privileged Identity Management を管理する他の管理者にアクセス権を付与する](pim-how-to-give-access-to-pim.md)」を参照してください。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** を選択します。

1. **[リソース フィルター]** を使用して、管理対象リソースの一覧をフィルター処理します。

    ![管理する Azure リソースの一覧](./media/pim-resource-roles-assign-roles/resources-list.png)

1. サブスクリプションや管理グループなど、管理するリソースを選択します。

1. [管理] で **[ロール]** を選択して、Azure リソースのロール一覧を表示します。

    ![Azure リソース ロール](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. **[メンバーの追加]** を選択して [新しい割り当て] ペインを開きます。

1. **[ロールを選択]** を選択して [ロールを選択] ペインを開きます。

    ![[新しい割り当て] ウィンドウ](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. 割り当てるロールを選択し、 **[選択]** をクリックします。

    [メンバーまたはグループの選択] ウィンドウが開きます。

1. ロールに割り当てるメンバーまたはグループを選択し、 **[選択]** をクリックします。

    ![[メンバーまたはグループの選択] ウィンドウ](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    [メンバーシップ設定] ウィンドウが開きます。

1. **[割り当ての種類]** リストで **[対象]** または **[アクティブ]** を選択します。

    ![[メンバーシップ設定] ウィンドウ](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Azure リソース向けの Privileged Identity Management には、2 つの明確な割り当ての種類があります。

    - **[対象]** 割り当ての場合、このロールのメンバーは、ロールを使用するにはアクションを実行する必要があります。 要求されるアクションには、多要素認証 (MFA) チェックの実行、業務上の妥当性の指定、指定された承認者に対する承認要求などがあります。

    - **[アクティブ]** 割り当ての場合、ロールを使用するために何らかのアクションを実行することをメンバーに要求しません。 アクティブ割り当てされたメンバーは、ロールによって提供される特権を常に所有します。

1. 割り当てを永続的 (永続的に対象または永続的に割り当て済み) にする必要がある場合は、 **[Permanently]\(永続的\)** チェック ボックスをオンにします。

    ロールの設定によっては、チェック ボックスが表示されない場合や、変更できない場合があります。

1. 特定の割り当て期間を指定するには、チェック ボックスの選択を解除して、開始または終了日時フィールドを変更します。

    ![メンバー シップ設定 - 日付と時刻](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. 終わったら、 **[Done]\(完了\)** を選択します。

    ![新しい割り当て - 追加](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. 新しいロールの割り当てを作成するには、 **[追加]** を選択します。 状態の通知が表示されます。

    ![新しい割り当て - 通知](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>既存のロールの割り当てを更新または削除する

既存のロールの割り当てを更新または削除するには、次の手順を実行します。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** を選択します。

1. サブスクリプションや管理グループなど、管理するリソースを選択します。

1. [管理] で **[ロール]** を選択して、Azure リソースのロール一覧を表示します。

    ![Azure リソース ロール - ロールの選択](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. 更新または削除するロールを選択します。

1. **[資格のあるロール]** タブまたは **[アクティブなロール]** タブでロールの割り当てを見つけます。

    ![ロールの割り当ての更新または削除](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. **[更新]** または **[削除]** を選択して、ロールの割り当てを更新または削除します。

    ロールの割り当てを延長する方法については、[Privileged Identity Management で Azure リソース ロールを延長または更新する方法](pim-resource-roles-renew-extend.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure リソース ロールを延長または更新する](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management で Azure リソース ロールの設定を構成する](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management で Azure AD ロールを割り当てる](pim-how-to-add-role-to-user.md)
