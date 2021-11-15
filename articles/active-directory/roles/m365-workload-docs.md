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
ms.openlocfilehash: b3fa2fbceba39ee6044c252703236c85545e46db
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349169"
---
# <a name="roles-for-microsoft-365-services-in-azure-active-directory"></a>Azure Active Directory の Microsoft 365 サービスのロール

Microsoft 365 のすべての製品は、Azure Active Directory (Azure AD) の管理者ロールで管理できます。 一部の製品では、その製品に固有の追加のロールも提供されます。 各製品でサポートされるロールについては、次の表を参照してください。 ロール セキュリティの計画のガイドラインについては、「[Azure AD でのハイブリッドおよびクラウド デプロイ用の特権アクセスをセキュリティで保護する](security-planning.md)」を参照してください。

## <a name="where-to-find-content"></a>コンテンツの参照先

> [!div class="mx-tableFixed"]
> | Microsoft 365 サービス | ロール コンテンツ | API コンテンツ |
> | ---------------------- | ------------------ | ----------------- |
> | Office 365 と Microsoft 365 のビジネス プランでの管理者ロール | [Microsoft 365 管理者ロール](/office365/admin/add-users/about-admin-roles) | 使用不可 |
> | Azure Active Directory (Azure AD) および Azure AD Identity Protection| [Azure AD の組み込みロール](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list) |
> | Exchange Online| [Exchange のロール ベースのアクセス制御](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell for Exchange](/powershell/module/exchange/role-based-access-control/add-managementroleentry)<br>[ロールの割り当てのフェッチ](/powershell/module/exchange/role-based-access-control/get-rolegroup) |
> | SharePoint Online | [Azure AD の組み込みロール](permissions-reference.md)<br>[Microsoft 365 での SharePoint 管理者ロール](/sharepoint/sharepoint-admin-role)について | [Graph API](/graph/api/overview)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list) |
> | チーム/Skype for Business | [Azure AD の組み込みロール](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list) |
> | セキュリティ/コンプライアンス センター (Office 365 の Advanced Threat Protection、Exchange Online Protection、Information Protection) | [Office 365 の管理者ロール](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry)<br>[ロールの割り当てのフェッチ](/powershell/module/exchange/role-based-access-control/get-rolegroup) |
> | セキュリティ スコア | [Azure AD の組み込みロール](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list) |
> | Compliance Manager | [Compliance Manager ロール](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | 使用不可 |
> | Azure Information Protection | [Azure AD の組み込みロール](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list) |
> | Microsoft Defender for Cloud Apps | [ロールベースのアクセス制御](/cloud-app-security/manage-admins) | [API リファレンス](/cloud-app-security/api-tokens)  |
> | Azure Advanced Threat Protection | [Azure ATP ロール グループ](/azure-advanced-threat-protection/atp-role-groups) | 使用不可 |
> | Windows Defender Advanced Threat Protection | [Windows Defender ATP のロール ベースのアクセス制御](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | 使用不可 |
> | Privileged Identity Management | [Azure AD の組み込みロール](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list) |
> | Intune | [Intune のロール ベースのアクセス制御](/intune/role-based-access-control) | [Graph API](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta&preserve-view=true)<br>[ロールの割り当てのフェッチ](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta&preserve-view=true) |
> | 管理対象デスクトップ | [Azure AD の組み込みロール](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list) |

## <a name="next-steps"></a>次のステップ

* [Azure AD 管理者ロールの割り当てと削除の方法](manage-roles-portal.md)
* [Azure AD の組み込みロール](permissions-reference.md)
