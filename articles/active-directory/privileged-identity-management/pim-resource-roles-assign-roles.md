---
title: Azure リソース向けの Privileged Identity Management - ロールを割り当てる | Microsoft Docs
description: PIM でロールを割り当てる方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 9a9046afe2ee1e578333ff9d29f6fb21e95a0f22
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---assign"></a>Privileged Identity Management - リソース ロール - 割り当て

## <a name="assign-roles"></a>ロールを割り当てる

ロールにユーザーまたはグループを割り当てるには、ロールを選択するか (ロールを表示している場合)、 

![](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

アクション バーから [追加] をクリックします ([メンバー] ビューの場合)。

![](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


[メンバー] タブからユーザーまたはグループを追加する場合は、次の操作を行う必要があります。 

1. ユーザーまたはグループを選択する前に、[追加] メニューからロールを選択します。

![](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. ディレクトリからユーザーまたはグループを選択します。

3. ドロップダウン メニューから適切な割り当ての種類を選択します。 

    - **Just In Time 割り当て:** 指定した期間または無期限 (ロール設定で構成されている場合) に、ロールに対して適格だが永続的ではないアクセス権を持つユーザーまたはグループ メンバーが提供されます。 
    - **直接割り当て:** ロール割り当てをアクティブ化するためにユーザーまたはグループ メンバーは必要ありません (永続的アクセスと呼ばれます)。 タスクの完了時にアクセスが必要とされない、単発のシフトや時間が限られたアクティビティなどの短期使用には直接割り当てを使用することをお勧めします。

[割り当ての種類] ドロップダウンの下にあるチェック ボックスで、割り当てを永続的にする (Just In Time 割り当てを永続的にアクティブ化できる/直接割り当てが永続的にアクティブになる) かどうかを指定できます。

![](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

>[!NOTE]
>このチェック ボックスは、別の管理者がロール設定のそれぞれの割り当ての種類に対して最大の割り当て期間を指定している場合は変更できません。

 特定の割り当て期間を指定するには、チェック ボックスの選択を解除して、開始または終了の日時フィールドを変更します。

![](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>ロールの割り当てを管理する

管理者は、左側のナビゲーションからロールまたはメンバーのどちらかを選択してロールの割り当てを管理できます。 ロールの選択により、管理者が管理タスクを特定のロールに制限できるようになり、メンバーにはリソースに対するすべてのユーザーとグループのロール割り当てが表示されます。

![](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
アクティブ化が保留中のロールがある場合、メンバーシップを表示した際にページの上部に通知バナーが表示されます。


## <a name="modify-existing-assignments"></a>既存の割り当てを変更する

ユーザー/グループの詳細ビューから既存の割り当てを変更するには、ページの上部にある操作バーから [設定の変更] を選択します。 [割り当ての種類] を Just In Time 割り当てまたは直接割り当てに変更します。

![](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
