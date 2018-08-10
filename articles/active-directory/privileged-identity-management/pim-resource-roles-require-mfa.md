---
title: Privileged Identity Management を使用して Azure リソースで Azure Multi-Factor Authentication を強制する | Microsoft Docs
description: このドキュメントでは、PIM リソースに対して多要素認証を有効にする方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 42c93a107c7ed58d6f7412b61627dccaf9076bb7
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617872"
---
# <a name="enforce-azure-multi-factor-authentication-in-azure-resources-by-using-privileged-identity-management"></a>Privileged Identity Management を使用して Azure リソースで Azure Multi-Factor Authentication を強制する

Azure リソース向け Privileged Identity Management (PIM) ロールにより、リソース管理者と ID 管理者が、期限付きメンバーシップと Just-In-Time アクセスで重要な Azure インフラストラクチャを保護できます。 さらに、PIM によって、2 つの異なるシナリオで、必要に応じて Azure Multi-factor Authentication を強制できます。

## <a name="require-multi-factor-authentication-to-activate"></a>アクティブ化に Multi-Factor Authentication を要求する

リソース管理者は、適格なロール メンバーをアクティブにする前に、そのメンバーが Azure Multi-Factor Authentication を実行するように要求できます。 このプロセスにより、アクティブ化を要求しているユーザーが、確かに本人が言うような人物であることが合理的かつ確実に保証されます。 このオプションを強制すると、ユーザー アカウントが侵害された可能性がある状況で、重要なリソースが保護されます。 

この要件を強制するには、管理対象リソースの一覧からリソースを選択します。 [概要ダッシュボード](pim-resource-roles-overview-dashboards.md)で、画面の右下にあるロールの一覧からロールを選択します。

さらに、左側のウィンドウの **[ロール]** タブまたは **[ロールの設定]** タブのいずれかから、ロールの設定に移動します。

>[!Note]
>左側のウィンドウのオプションが淡色表示され、ページ上部に "アクティブにできる適格なロールがある" ことを示すバナーが表示される場合は、アクティブな管理者ではありません。 これは、次に進むには[アクティブ化](pim-resource-roles-activate-your-roles.md)する必要があることを意味します。

![[ロール] タブと [ロールの設定] タブ ](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

ロールのメンバーシップを表示するには、画面の上部にあるバーから **[ロールの設定]** を選択して、**[Role setting detail]\(ロールの設定の詳細\)** を開きます。

ロールの設定を変更するには、上部の **[編集]** を選択します。

**[アクティブ化]** のセクションで、**[Require Multi-Factor Authentication to activate]\(アクティブ化に Multi-Factor Authentication を要求する\)** チェック ボックスをオンにします。 次に、**[保存]** を選択します。

![アクティブ化に Multi-Factor Authentication を要求する](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-multi-factor-authentication-on-assignment"></a>割り当てに Multi-Factor Authentication を要求する

場合によっては、リソース管理者がメンバーをロールに短期間 (たとえば 1 日) 割り当てる必要があります。 この場合、割り当てられたメンバーにアクティブ化を要求する必要はありません。 このシナリオでは、メンバーは、ロールが割り当てられた時点からそのロールで既にアクティブであるため、自分のロールの割り当てを使用しているとき、PIM では Multi-Factor Authentication を強制できません。

割り当てを実行するリソース管理者が、確かに本人が言うような人物であることを保証するために、割り当てに対して Multi-Factor Authentication を強制できます。

同じロールの設定の詳細画面で、**[Require Multi-Factor Authentication on assignment]\(割り当てに Multi-Factor Authentication を要求する\)** ボックスをオンにします。

![直接割り当てに Multi-Factor Authentication を要求する](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>次の手順

[アクティブ化に承認を要求する](pim-resource-roles-approval-workflow.md)

[監査ログの使用](pim-resource-roles-use-the-audit-log.md)



