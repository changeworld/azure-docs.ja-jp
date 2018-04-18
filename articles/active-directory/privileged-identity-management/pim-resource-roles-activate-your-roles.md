---
title: Azure リソース向けの Privileged Identity Management - ロールをアクティブ化する | Microsoft Docs
description: PIM でロールをアクティブ化する方法について説明します。
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
ms.openlocfilehash: 3e5456e7a632639cb82d7ba2b2e073938b1798ef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---activate"></a>Privileged Identity Management - リソース ロール - アクティブ化
Azure リソースのロールをアクティブ化すると、ロールの有資格メンバーは、将来の日付/時刻でのアクティブ化をスケジュールしたり、管理者が許可した最長期間の範囲内でアクティブ化期間を指定できるという新しいエクスペリエンスが導入されます。 Azure AD ロールのアクティブ化の詳細については、[こちら](../active-directory-privileged-identity-management-how-to-activate-role.md)を参照してください。

## <a name="activate-roles"></a>ロールをアクティブ化する
左側のナビゲーション バーの [自分のロール] セクションに移動します。 アクティブ化するロールに対して [アクティブ化] をクリックします。
![](media/azure-pim-resource-rbac/rbac-roles.png)

[アクティブ化] メニューから、希望する開始日時を入力してロールをアクティブ化します。 または、アクティブ化期間 (ロールがアクティブな期間) を短くし、必要に応じて理由を入力して、[アクティブ化] をクリックします。

開始日時が変更されない場合、数秒以内にロールがアクティブ化されます。 [自分のロール] ページに、アクティブ化のキューに登録されているロールのバナー メッセージが表示されます。 このメッセージを消去するには、[更新] ボタンをクリックします。

![](media/azure-pim-resource-rbac/rbac-activate-notification.png)

アクティブ化が将来の日時でスケジュールされている場合、保留中の要求が左側のナビゲーション メニューの [保留中の要求] タブに表示されます。 ロールのアクティブ化が必要でなくなった場合、ユーザーは、ページの右側にある [キャンセル] ボタンをクリックして、要求をキャンセルすることができます。

![](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="just-enough-administration"></a>Just Enough Administration

Just Enough Administration (JEA) をリソースのロール割り当てで使用するベスト プラクティスは Azure リソースの PIM では簡単です。 Azure サブスクリプションまたはリソース グループで割り当てが行われているユーザーおよびグループ メンバーは、より限られた範囲で既存のロール割り当てをアクティブ化できます。 

検索ページで、管理する必要がある下位のリソースを検索します。

![](media/azure-pim-resource-rbac/azure-resources-02.png)

左側のナビゲーション メニューから [自分のロール] を選択し、アクティブ化する適切なロールを選択します。 以下のとおり、ロールがリソース グループではなくサブスクリプションで割り当てられたため、割り当ての種類が継承されていることがわかります。

![](media/azure-pim-resource-rbac/my-roles-02.png)
