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
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7bf1e437e97fdb4d929af23bd7b2a9abb49268df
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189160"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>PIM で Azure AD ディレクトリ ロールに対する要求を承認または拒否する

Privileged Identity Management を使用して、アクティブ化の承認を必要とするようにロールを構成できます。また、代理承認者として 1 名以上のユーザーまたはグループを選択できます。

## <a name="view-pending-approvals-requests"></a>保留中の承認 (要求) を表示する

代理承認者として、承認の要求が保留されると電子メール通知を受け取ります。 PIM ポータルでこれらの要求を表示するには、ダッシュボード (新ナビゲーション) の左側のナビゲーション バーで [保留中の承認要求] タブを選択します。

![](media/azure-ad-pim-approval-workflow/image023.png)

ここで、承認が保留されている要求の一覧を確認できます。

![](media/azure-ad-pim-approval-workflow/image024.png)

## <a name="approve-or-deny-requests-for-role-elevation-single-andor-bulk"></a>ロールの昇格の要求を承認または拒否する (単独および一括)

承認または拒否する要求を選択し、処理に対応するアクション バーのボタンをクリックします。

![](media/azure-ad-pim-approval-workflow/image025.png)

## <a name="provide-justification-for-my-approvaldenial"></a>自分の承認/拒否の理由を説明する

新しいブレードが開き、ここで一度に複数の要求を承認または拒否できます。 決定の理由を入力し、ブレードの下部で [承認] \(または [拒否]) をクリックします。

![](media/azure-ad-pim-approval-workflow/image029.png)

要求のプロセスが完了すると、行った決定が状態のシンボルに反映されます (この例の決定は承です)。

![](media/azure-ad-pim-approval-workflow/image031.png)

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールに対する要求を承認または拒否する](pim-resource-roles-approval-workflow.md)
- [PIM での電子メール通知](pim-email-notifications.md)
