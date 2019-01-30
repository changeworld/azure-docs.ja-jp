---
title: PIM で管理できないロール - Azure | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で管理できないロールについて説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 01/18/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: e4c60c6ecdc586ba97a01fa42cd80b8ed541fa61
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434940"
---
# <a name="roles-you-cannot-manage-in-pim"></a>PIM で管理できないロール

Azure AD Privileged Identity Management (PIM) を使用すると、すべての [Azure AD ディレクトリ ロール](../users-groups-roles/directory-assign-admin-roles.md)とすべての [Azure リソース ロール](../../role-based-access-control/built-in-roles.md)を管理できます。 これらのロールには、管理グループ、サブスクリプション、リソース グループ、およびリソースに関連付けられているカスタム ロールも含まれます。 ただし、管理できないロールがいくつかあります。 この記事では、PIM で管理できないロールについて説明します。

## <a name="classic-subscription-administrator-roles"></a>従来のサブスクリプション管理者ロール

PIM では、次の従来のサブスクリプション管理者ロールを管理することはできません。

- アカウント管理者
- サービス管理者
- 共同管理者

従来のサブスクリプション管理者ロールの詳細については、「[従来のサブスクリプション管理者ロール、Azure RBAC ロール、および Azure AD 管理者ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)」を参照してください。

## <a name="what-about-office-365-admin-roles"></a>Office 365 の管理者ロールの場合

Exchange 管理者と SharePoint 管理者を除く Exchange Online または SharePoint Online 内のロールは Azure AD で表示されないため、PIM で管理することはできません。 これらの Office 365 サービスの詳細については、[Office 365 の管理者ロール](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)に関するページを参照してください。

> [!NOTE]
> SharePoint 管理者は、SharePoint Online 管理センターを通じた SharePoint Online への管理アクセスが可能で、SharePoint Online でのほぼすべてのタスクを実行できます。 対象となるユーザーは、PIM でアクティブにした後に SharePoint 内でこのロールを使用すると、遅延が発生する場合があります。

## <a name="next-steps"></a>次の手順

- [PIM の使用を開始する](pim-getting-started.md)
- [PIM で Azure AD ディレクトリ ロールを割り当てる](pim-how-to-add-role-to-user.md)
- [PIM で Azure リソース ロールを割り当てる](pim-resource-roles-assign-roles.md)
