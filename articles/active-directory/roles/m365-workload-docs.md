---
title: Microsoft 365 サービス全体の管理者ロール ドキュメント - Azure AD | Microsoft Docs
description: Azure Active Directory で Microsoft 365 サービスの管理者ロールのコンテンツおよび API リファレンスを検索する
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5579e0d2094c3dc596e8e50992ae6bc31818ff87
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743068"
---
# <a name="roles-for-microsoft-365-services-in-azure-active-directory"></a>Azure Active Directory の Microsoft 365 サービスのロール

Microsoft 365 のすべての製品は、Azure Active Directory (Azure AD) の管理者ロールで管理できます。 一部の製品では、その製品に固有の追加のロールも提供されます。 各製品でサポートされるロールについては、次の表を参照してください。 委任の問題に関する一般的な説明については、[Azure Active Directory でのロールの委任計画](concept-delegation.md)に関するページを参照してください。

## <a name="where-to-find-content"></a>コンテンツの参照先

Microsoft 365 サービス | ロール コンテンツ | API コンテンツ
---------------------- | ------------------ | -----------------
Office 365 と Microsoft 365 のビジネス プランでの管理者ロール | [Microsoft 365 管理者ロール](/office365/admin/add-users/about-admin-roles?view=o365-worldwide&preserve-view=true) | 使用不可
Azure Active Directory (Azure AD) および Azure AD Identity Protection| [Azure AD 管理者ロール](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true&preserve-view=true)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Exchange Online| [Exchange のロール ベースのアクセス制御](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell for Exchange](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps&preserve-view=true)<br>[ロールの割り当てのフェッチ](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps&preserve-view=true)
SharePoint Online | [Azure AD 管理者ロール](permissions-reference.md)<br>[Microsoft 365 での SharePoint 管理者ロール](/sharepoint/sharepoint-admin-role)について | [Graph API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
チーム/Skype for Business | [Azure AD 管理者ロール](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
セキュリティ/コンプライアンス センター (Office 365 の Advanced Threat Protection、Exchange Online Protection、Information Protection) | [Office 365 の管理者ロール](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps&preserve-view=true)<br>[ロールの割り当てのフェッチ](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps&preserve-view=true)
セキュリティ スコア | [Azure AD 管理者ロール](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Compliance Manager | [Compliance Manager ロール](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | 使用不可
Azure Information Protection | [Azure AD 管理者ロール](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Microsoft Cloud App Security | [ロールベースのアクセス制御](/cloud-app-security/manage-admins) | [API リファレンス](/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Azure ATP ロール グループ](/azure-advanced-threat-protection/atp-role-groups) | 使用不可
Windows Defender Advanced Threat Protection | [Windows Defender ATP のロール ベースのアクセス制御](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | 使用不可
Privileged Identity Management | [Azure AD 管理者ロール](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)
Intune | [Intune のロール ベースのアクセス制御](/intune/role-based-access-control) | [Graph API](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta&preserve-view=true)<br>[ロールの割り当てのフェッチ](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta&preserve-view=true)
管理対象デスクトップ | [Azure AD 管理者ロール](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list?view=graph-rest-1.0&preserve-view=true)

## <a name="next-steps"></a>次のステップ

* [Azure AD 管理者ロールの割り当てと削除の方法](manage-roles-portal.md)
* [Azure AD 管理者ロールのリファレンス](permissions-reference.md)