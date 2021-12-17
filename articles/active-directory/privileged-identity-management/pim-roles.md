---
title: Privileged Identity Management で管理できないロール - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で管理できないロールについて説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89cd7e3cfac71e194d8f32ae8b15c26da7f0615d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132336470"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Privileged Identity Management で管理できないロール

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用すると、すべての [Azure AD ロール](../roles/permissions-reference.md)とすべての [Azure ロール](../../role-based-access-control/built-in-roles.md)を管理できます。 Azure ロールには、管理グループ、サブスクリプション、リソース グループ、およびリソースに関連付けられているカスタム ロールも含めることができます。 ただし、管理できないロールがいくつかあります。 この記事では、Privileged Identity Management で管理できないロールについて説明します。

## <a name="classic-subscription-administrator-roles"></a>従来のサブスクリプション管理者ロール

Privileged Identity Management では、次の従来のサブスクリプション管理者ロールを管理することはできません。

- アカウント管理者
- サービス管理者
- 共同管理者

従来のサブスクリプション管理者ロールの詳細については、[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD 管理者ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)に関する記事を参照してください。

## <a name="what-about-microsoft-365-admin-roles"></a>Microsoft 365 管理者ロールについて

Exchange 管理者や SharePoint 管理者など、Azure AD ロールと管理者ポータルのエクスペリエンスでは、すべての Microsoft 365 のロールがサポートされていますが、Exchange RBAC および SharePoint RBAC 内の特定のロールはサポートされていません。 これらの Microsoft 365 サービスの詳細については、[Microsoft 365 の管理者ロール](/office365/admin/add-users/about-admin-roles)に関するページをご覧ください。

> [!NOTE]
> - SharePoint 管理者ロール、デバイス管理者ロール、および Microsoft セキュリティ センターとコンプライアンス センターにアクセスしようとしているすべてのロールの対象ユーザーは、ロールをアクティブ化した後、最大で数時間の遅延に遭遇する可能性があります。 これらのチームと協力して問題の解決に取り組んでいます。
> - Azure AD 参加済みデバイスのローカル管理者ロールのアクティブ化遅延について詳しくは、「[Azure AD 参加済みデバイスのローカル管理者グループの管理方法](../devices/assign-local-admin.md#manage-the-device-administrator-role)」をご覧ください。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure AD ロールを割り当てる](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management で Azure リソース ロールを割り当てる](pim-resource-roles-assign-roles.md)