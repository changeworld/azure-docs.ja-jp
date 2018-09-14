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
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: c661f2662f48c5aaece142cb4a2223ab8a6d0853
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666593"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>PIM で Azure リソース ロールに対する要求を承認または拒否する

Azure AD Privileged Identity Management (PIM) を使用して、アクティブ化の承認を必要とするようにロールを構成できます。また、代理承認者として 1 名以上のユーザーまたはグループを選択できます。 Azure リソース ロールの要求を承認または拒否するには、この記事の手順に従ってください。

## <a name="view-pending-requests"></a>保留中の要求を表示する

代理承認者は、Azure リソース ロール要求が代理承認者による承認を待っている状態になると、電子メール通知を受け取ります。 これらの保留中の要求は、PIM で表示できます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[申請の承認]** をクリックします。

    ![Azure リソース - [申請の承認]](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    **[ロールのアクティブ化に関する要求]** セクションに、承認が保留されている要求の一覧が表示されます。

## <a name="approve-requests"></a>要求の承認

1. 承認する要求を見つけてクリックします。 承認ウィンドウが表示されます。

    ![[申請の承認] ウィンドウ](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. **[根拠]** ボックスに、理由を入力します。

1. **[Approve]\(承認\)** をクリックします。

    承認すると同時に通知が表示されます。

    ![承認通知](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>要求の拒否

1. 拒否する要求を見つけてクリックします。 承認ウィンドウが表示されます。

    ![[申請の承認] ウィンドウ](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. **[根拠]** ボックスに、理由を入力します。

1. **[拒否]** をクリックします。

    拒否すると同時に通知が表示されます。

## <a name="workflow-notifications"></a>ワークフロー通知

ワークフロー通知に関するいくつかの情報を次に示します。

- ロールの要求のレビューが保留中の場合、承認者一覧のすべてのメンバーに電子メールで通知されます。 電子メール通知には、承認者が承認または拒否できる、要求への直接リンクが含まれています。
- 要求は、一覧の承認または拒否した最初のメンバーによって解決されます。
- 承認者が要求に応答すると、承認者一覧のすべてのメンバーにその動作が通知されます。
- リソース管理者には、承認されたメンバーがロール内でアクティブになったときに通知されます。

>[!Note]
>承認されたメンバーをアクティブにしないほうがよいと見なしたリソース管理者は、PIM でアクティブなロールの割り当てを削除できます。 承認者一覧のメンバーでない限り、保留中の要求はリソース管理者には通知されませんが、リソース管理者は PIM で保留中の要求を表示することで、すべてのユーザーの保留中の要求を確認およびキャンセルできます。 

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールを延長または更新する](pim-resource-roles-renew-extend.md)
- [PIM での電子メール通知](pim-email-notifications.md)
- [PIM で Azure AD ディレクトリ ロールに対する要求を承認または拒否する](azure-ad-pim-approval-workflow.md)
