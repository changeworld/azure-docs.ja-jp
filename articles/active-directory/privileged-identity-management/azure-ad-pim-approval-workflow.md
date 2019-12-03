---
title: PIM で Azure AD ロールに対する要求を承認または拒否する - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールに対する要求を承認または拒否する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f663f7daad19e77dcc1cc95a6a324e881d92b28
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847124"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management で Azure AD ロールに対する要求を承認または拒否する

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用して、アクティブ化の承認を必要とするようにロールを構成できます。また、代理承認者として 1 名以上のユーザーまたはグループを選択できます。 代理承認者は、要求を承認するまでに 24 時間あります。 要求が 24 時間以内に承認されない場合、有資格ユーザーは新しい要求を再送信する必要があります。 24 時間の承認時間枠は構成できません。

Azure AD ロールに対する要求を承認または拒否するには、この記事の手順に従ってください。

## <a name="view-pending-requests"></a>保留中の要求を表示する

代理承認者は、Azure AD ロール要求が代理承認者による承認を待っている状態になると、メール通知を受け取ります。 これらの保留中の要求は、Privileged Identity Management で表示できます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

1. **[申請の承認]** をクリックします。

    ![Azure AD ロール - 申請の承認](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    承認が保留されている要求の一覧を確認できます。

## <a name="approve-requests"></a>要求の承認

1. 承認する要求を選択し、 **[承認]** をクリックして、[選択した要求の承認] ウィンドウを開きます。

    ![[承認] オプションが強調表示された [申請の承認] の一覧](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. **[承認の理由]** ボックスに理由を入力します。

    ![[承認の理由] を含む [選択した要求の承認] ウィンドウ](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. **[Approve]\(承認\)** をクリックします。

    承認すると、状態シンボルが更新されます。

    ![[承認] をクリックした後の [選択した要求の承認] ウィンドウ](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>要求を拒否する

1. 拒否する要求を選択し、 **[拒否]** をクリックして、[選択した要求の拒否] ウィンドウを開きます。

    ![[拒否] オプションが強調表示された [申請の承認] の一覧](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. **[拒否の理由]** ボックスに理由を入力します。

    ![[拒否の理由] を含む [選択した要求の拒否] ウィンドウ](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. **[拒否]** をクリックします。

    拒否すると、状態シンボルが更新されます。

## <a name="next-steps"></a>次の手順

- [Privileged Identity Management での電子メール通知](pim-email-notifications.md)
- [Privileged Identity Management で Azure リソース ロールに対する要求を承認または拒否する](pim-resource-roles-approval-workflow.md)
