---
title: Azure リソース向けの Privileged Identity Management - MFA | Microsoft Docs
description: このドキュメントでは、PIM リソースに対して多要素認証を有効にする方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 8d1c05e7f61ed76c47613bfab7bb8afd9b66cbe7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---mfa"></a>Privileged Identity Management - リソース ロール - MFA

Azure リソース向け PIM ロールにより、リソース管理者と ID 管理者が、期限付きメンバーシップと Just-In-Time アクセスで重要な Azure インフラストラクチャを保護できます。 さらに、PIM によって、2 つの異なるシナリオで、必要に応じて Azure Multi-factor Authentication (MFA) を強制できます。

## <a name="require-mfa-to-activate"></a>アクティブ化に MFA を要求する

リソース管理者は、適格なロール メンバーをアクティブにする前に、そのメンバーが Azure MFA に成功するように要求できます。 このプロセスにより、アクティブ化を要求しているユーザーが、確かに本人が言うような人物であることが合理的かつ確実に保証されます。 このオプションを強制すると、ユーザー アカウントが侵害された可能性がある状況で、重要なリソースが保護されます。 

この要件を強制するには、管理対象リソースの一覧からリソースを選択します。 [概要ダッシュボード](pim-resource-roles-overview-dashboards.md)で、画面の右下にあるロールの一覧からロールを選択します。

さらに、左側のナビゲーション メニューの [ロール] タブまたは [ロールの設定] タブのいずれかから、ロールの設定に移動します。

>[!Note]
>左側のナビゲーション メニューのオプションが淡色表示され、ページ上部に "アクティブにできる適格なロールがある" ことを示すバナーが表示される場合は、アクティブな管理者ではないため、次に進むには[アクティブ化](pim-resource-roles-activate-your-roles.md)する必要があります。

![](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

ロールのメンバーシップが表示されている場合は、画面の上部にあるバーから [ロールの設定] を選択して、"ロールの設定の詳細" を開きます。

上部の **[編集]** をクリックして、ロールの設定を変更します。

**[アクティブ化]** のセクションで、**[Require Multi-Factor Authentication to activate]\(アクティブ化に Multi-Factor Authentication を要求する\)** チェック ボックスをオンにして、[保存] をクリックします。

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-mfa-on-assignment"></a>割り当てに対して MFA を要求する

場合によっては、リソース管理者がメンバーをロールに割り当てる必要があるのが短期間 (たとえば 1 日) で、その割り当てられたメンバーについては、アクティブ化を要求する必要がないことがあります。 このシナリオでは、メンバーは、ロールが割り当てられた時点からそのロールで既にアクティブであるため、自分のロールの割り当てを使用しているとき、PIM では MFA を強制できません。

割り当てを実行するリソース管理者が、確かに本人が言うような人物であることを保証するために、割り当てに対して MFA を強制できます。

同じロールの設定の詳細画面で、[Require Multi-Factor Authentication on assignment]\(割り当てで Multi-Factor Authentication を要求する\) ボックスをオンにします。

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>次の手順

[アクティブ化に承認を要求する](pim-resource-roles-approval-workflow.md)

[監査ログの使用](pim-resource-roles-use-the-audit-log.md)



