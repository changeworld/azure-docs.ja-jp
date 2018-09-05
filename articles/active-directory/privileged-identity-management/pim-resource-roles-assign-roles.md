---
title: PIM で Azure リソース ロールを割り当てる | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールを割り当てる方法を説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7019a6f97a9590d3b652584015f3077f4ed075af
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188922"
---
# <a name="assign-azure-resource-roles-in-pim"></a>PIM で Azure リソース ロールを割り当てる

Azure AD PIM は、組み込みの Azure リソース ロールと、以下を含むカスタム ロール (ただしこれらに限定されません) を管理できます。

- Owner
- User Access Administrator
- Contributor
- セキュリティ管理者
- セキュリティ マネージャーなど

>[!NOTE]
Azure AD でサブスクリプション管理を有効にする、所有者またはユーザー アクセス管理者ロールに割り当てられたグループのユーザーまたはメンバー、および全体管理者がリソース管理者です。 これらの管理者は、ロールの割り当て、ロール設定の構成、Azure リソースの PIM を使用したアクセスの確認ができます。 [Azure リソースの組み込みロール](../../role-based-access-control/built-in-roles.md)の一覧をご確認ください。

## <a name="assign-roles"></a>ロールを割り当てる

ロールにユーザーまたはグループを割り当てるには､**Roles** ウィンドウを表示している状態でロールを選択して､**Add user** を選択します｡ 

![[Roles] ウィンドウの [Add user] ボタン](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

**Add user** は､**Members** ウィンドウから選択することもできます｡

![[Members] ウィンドウの [Add user] ボタン](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


**Members** ウィンドウからユーザーまたはグループを追加する場合は､以下を行う必要があります｡ 

1. ユーザーまたはグループを選択する前に、**Select a role** ウィンドウからロールを選択します。

   ![[Select a role] ウィンドウ](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. ディレクトリからユーザーまたはグループを選択します。

3. ドロップダウン メニューから適切な割り当ての種類を選択します。 

   - **Just In Time 割り当て:** 指定した期間または無期限 (ロール設定で構成されている場合)､ 適格だが永続的ではないアクセス権を持つユーザーまたはグループ メンバーをロールに割り当てます｡ 
   - **直接割り当て:** ユーザーまたはグループ メンバーがロール割り当てをアクティブ化する必要はありません (永続的アクセスと呼ばれます)。 短期使用には､タスクが完了するとアクセス権が不要になる直接割り当てを使用することをお勧めします｡ 例えば待機制のアクティビティ､タイミングが重要なアクティビティがそうです｡

4. 割り当てを永続的にする (just-in-time 割り当ての場合ば永続的な資格にする､あるいは直接割り当てであれば永続的にアクティブ化する) 場合は､**Assignment type** にある該当するチェック ボックスを選択します｡

   ![[Membership settings] ウィンドウの [Assignment type] ボックスと関連するチェック ボックス](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

   >[!NOTE]
   >別の管理者がロール設定のそれぞれの割り当ての種類に対して最大の割り当て期間を指定していた場合､このチェック ボックスは変更できません。

   特定の割り当て期間を指定するには、チェック ボックスの選択を解除して、開始または終了日時フィールドを変更します。

   ![[Membership settings] ウィンドウの開始日時と終了日時フィールド](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>ロールの割り当てを管理する

管理者は、左側のウィンドウから **Roles** または **Members** のいずれを選択することでロールの割り当てを管理できます。 **Roles** を選択すると､特定のロールに管理タスクの範囲を設定できます｡ **Members** 選択すると､そのリソースに対するすべてのユーザーおよびグループのロール割り当てが表示されます｡

![[Roles] ウィンドウ](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![[Members] ウィンドウ](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
アクティブ化が保留中のロールがある場合、メンバーシップを表示すると､そのウィンドウの上部に通知バナーが表示されます。


## <a name="modify-existing-assignments"></a>既存の割り当てを変更する

ユーザー/グループの詳細ビューから既存の割り当てを変更するには、操作バーから **Change settings** を選択します。 割り当ての種類を **Just in time** または **Direct** に変更します

![｢User details｣ウィンドウの｢Change settings｣ボタン](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールの設定を構成する](pim-resource-roles-configure-role-settings.md)
- [PIM で Azure AD ディレクトリ ロールを割り当てる](pim-how-to-add-role-to-user.md)
