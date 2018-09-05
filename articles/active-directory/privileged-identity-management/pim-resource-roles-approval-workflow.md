---
title: PIM で Azure リソース ロールに対する要求を承認または拒否する | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールに対する要求を承認または拒否する方法について説明します。
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
ms.openlocfilehash: e5cda31af5ac95e7ebe2b858b1d7355ea3f2a6bb
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189806"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>PIM で Azure リソース ロールに対する要求を承認または拒否する

要求を承認または拒否するには、承認者一覧のメンバーである必要があります。 

1. PIM で、左側のメニューのタブから **[申請の承認]** を選択し、要求を見つけます。

   ![[申請の承認] ウィンドウ](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. 要求を選択して、決定の理由を入力し、**[承認]** または **[拒否]** を選択します。 これで、要求が解決されます。

   ![選択された要求と詳細情報](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>ワークフロー通知

ワークフロー通知に関するファクトをいくつか次に示します。

- ロールの要求のレビューが保留中の場合、承認者一覧のすべてのメンバーに電子メールで通知されます。 電子メール通知には、承認者が承認または拒否できる、要求への直接リンクが含まれています。
- 要求は、一覧の承認または拒否した最初のメンバーによって解決されます。 
- 承認者が要求に応答すると、承認者一覧のすべてのメンバーにその動作が通知されます。 
- リソース管理者には、承認されたメンバーがロール内でアクティブになったときに通知されます。 

>[!Note]
>承認されたメンバーをアクティブにしないほうがよいと見なしたリソース管理者は、PIM でアクティブなロールの割り当てを削除できます。 承認者一覧のメンバーでない限り、保留中の要求はリソース管理者には通知されませんが、リソース管理者は PIM で保留中の要求を表示することで、すべてのユーザーの保留中の要求を確認およびキャンセルできます。 

## <a name="next-steps"></a>次の手順

- [PIM で Azure AD ディレクトリ ロールに対する要求を承認または拒否する](azure-ad-pim-approval-workflow.md)
- [PIM での電子メール通知](pim-email-notifications.md)
