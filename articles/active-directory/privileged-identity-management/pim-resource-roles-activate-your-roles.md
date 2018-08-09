---
title: Privileged Identity Management を使用した Azure リソースのロールのアクティブ化 | Microsoft Docs
description: PIM でロールをアクティブ化する方法について説明します。
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
ms.openlocfilehash: 45a2747a60df4d91c2fe1c5247e1d4ac82ff819f
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617151"
---
# <a name="activate-roles-for-azure-resources-by-using-privileged-identity-management"></a>Privileged Identity Management を使用した Azure リソースのロールのアクティブ化
Privileged Identity Management (PIM) では､Azure リソースのロールのアクティブ化で新しい体験をすることができます｡ 有資格ロール メンバーは､アクティブ化する日時を指定することができます｡ 最大範囲 (管理者が設定) 内で特定のアクティブ化期間を選択することもできます｡ 詳細は､[Azure AD Privileged Identity Management でロールをアクティブ化または非アクティブ化する方法](pim-how-to-activate-role.md)を参照してください｡

## <a name="activate-roles"></a>ロールをアクティブ化する
左側のウィンドウの **My roles** 部分を見ます｡ アクティブ化するロールの **Activate** を選択します｡

![[My roles] ウィンドウの [Eligible roles] タブ](media/azure-pim-resource-rbac/rbac-roles.png)

**[アクティブ化]** メニューから、ロールをアクティブ化する開始日時を入力します。 アクティブ化期間 (ロールがアクティブな期間) を短くし、必要に応じて理由を入力することもできます｡ **[アクティブ化]** を選択します。

開始日時が変更されなかった場合､ロールはすぐにアクティブ化されます｡ **My roles** ウィンドウに､ロールがアクティブ化待ちであることを示すバナー メッセージが表示されます｡ このメッセージを消去するには、[更新] ボタンをクリックします。

![[My roles] ウィンドウのバナー メッセージと承認待ちの通知](media/azure-pim-resource-rbac/rbac-activate-notification.png)

アクティブ化する日時が指定された場合は、左側のウィンドウの **[保留中の要求]** タブにその保留中の要求が表示されます。 ロールのアクティブ化が必要なくなった場合は､**Cancel** ボタンを選択して､その要求を取り消すことができます｡

![保留中の要求一覧と [キャンセル] ボタン](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="apply-just-enough-administration-practices"></a>Just Enough Administration のプラクティスを適用する

Azure リソースに対して PIM を使用した場合､Just Enough Administration (JEA) のベスト プラクティスをリソース ロール 割り当てで利用するのは簡単です｡ Azure サブスクリプションまたはリソース グループで割り当てが行われているユーザーおよびグループ メンバーは、より限られた範囲で既存のロール割り当てをアクティブ化できます。 

検索ページで、管理する必要がある下位のリソースを検索します。

![リソースの選択](media/azure-pim-resource-rbac/azure-resources-02.png)

左側のウィンドウから **[自分のロール]** を選択し、アクティブ化するロールを選択します。 ロールがリソース グループではなくサブスクリプションで割り当てられているため、割り当ての種類は**継承**されます｡

![有資格ロール 割り当ての一覧と割り当ての種類 (強調部分)](media/azure-pim-resource-rbac/my-roles-02.png)
