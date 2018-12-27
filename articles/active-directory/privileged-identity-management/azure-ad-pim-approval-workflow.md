---
title: PIM で Azure AD ディレクトリ ロールに対する要求を承認または拒否する | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ディレクトリ ロールに対する要求を承認または拒否する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 08/29/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 9402824540f965cb89aa00791d093bd87712a89a
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665844"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>PIM で Azure AD ディレクトリ ロールに対する要求を承認または拒否する

Azure AD Privileged Identity Management (PIM) を使用して、アクティブ化の承認を必要とするようにロールを構成できます。また、代理承認者として 1 名以上のユーザーまたはグループを選択できます。 Azure AD ディレクトリ ロールの要求を承認または拒否するには、この記事の手順に従ってください。

## <a name="view-pending-requests"></a>保留中の要求を表示する

代理承認者は、Azure AD ディレクトリ ロール要求が代理承認者による承認を待っている状態になると、電子メール通知を受け取ります。 これらの保留中の要求は、PIM で表示できます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ディレクトリ ロール]** をクリックします。

1. **[申請の承認]** をクリックします。

    ![PIM Azure AD ディレクトリ ロール - ロール](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    承認が保留されている要求の一覧を確認できます。

## <a name="approve-requests"></a>要求の承認

1. 承認する要求を選択し、**[承認]** をクリックして、[選択した要求の承認] ウィンドウを開きます。

    ![PIM の [申請の承認] リスト](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. **[承認の理由]** ボックスに理由を入力します。

    ![PIM の [選択した要求の承認]](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. **[Approve]\(承認\)** をクリックします。

    承認すると、状態シンボルが更新されます。

    ![PIM の [選択した要求の承認]](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>要求を拒否する

1. 拒否する要求を選択し、**[拒否]** をクリックして、[選択した要求の拒否] ウィンドウを開きます。

    ![PIM の [申請の承認] リスト](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. **[拒否の理由]** ボックスに理由を入力します。

    ![PIN の[選択した要求の拒否]](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. **[拒否]** をクリックします。

    拒否すると、状態シンボルが更新されます。

## <a name="next-steps"></a>次の手順

- [PIM での電子メール通知](pim-email-notifications.md)
- [PIM で Azure リソース ロールに対する要求を承認または拒否する](pim-resource-roles-approval-workflow.md)
