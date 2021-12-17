---
title: サポートされる Azure AD の機能
description: Azure AD B2C でまだサポートされている Azure AD 機能について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/08/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 99f6d58af1a8508b6e3582b4ec0139081a259046
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042999"
---
# <a name="supported-azure-ad-features"></a>サポートされる Azure AD の機能

Azure AD B2C テナントは、既にお持ちかもしれない Azure Active Directory テナントとは別のものですが、それに依存します。 Azure AD B2C テナントでは、次の Azure AD 機能を使用できます。

|機能  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [グループ](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | グループを使用して、管理アカウントとユーザー アカウントを管理できます。| グループを使用して、管理アカウントを管理できます。 [コンシューマー アカウント](user-overview.md#consumer-user)は、どのグループでもメンバーにすることはできません。 |
| [外部 ID のゲストの招待](../active-directory//external-identities/add-users-administrator.md)| ゲスト ユーザーを招待し、Facebook と Google のアカウントでのフェデレーションやサインインなどの外部 ID 機能を構成することができます。 | アプリケーションにアクセスしたり、テナントを管理したりするために Azure AD テナントにゲストとして招待できるのは、Microsoft アカウントまたは Azure AD ユーザーのみです。 [コンシューマー アカウント](user-overview.md#consumer-user)の場合は、Azure AD B2C のユーザー フローとカスタム ポリシーを使用して、ユーザーと、Google や Facebook などの外部 ID プロバイダーでのサインアップまたはサインインを管理します。 |
| [ロールと管理者](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| 管理アカウントとユーザー アカウントに対して完全にサポートされています。 | ロールは、[コンシューマー アカウント](user-overview.md#consumer-user)ではサポートされていません。 コンシューマー アカウントは、どの Azure リソースにもアクセスできません。|
| [カスタム ドメイン名](../active-directory/fundamentals/add-custom-domain.md) |  Azure AD カスタム ドメインは管理アカウントにのみ使用できます。 | [コンシューマー アカウント](user-overview.md#consumer-user)は、ユーザー名、電話番号、または任意の電子メール アドレスを使用してサインインできます。 [カスタム ドメイン](custom-domain.md)はリダイレクト URL で使用できます。|
| [条件付きアクセス](../active-directory/conditional-access/overview.md) | 管理アカウントとユーザー アカウントに対して完全にサポートされています。 | [コンシューマー アカウント](user-overview.md#consumer-user)では、Azure AD 条件付きアクセス機能のサブセットがサポートされています。Azure AD B2C の[条件付きアクセス](conditional-access-user-flow.md)を構成する方法を参照してください。|
| [Premium P1](https://azure.microsoft.com/pricing/details/active-directory) | Azure AD Premium P1 の機能で完全にサポートされています ([パスワード保護](../active-directory/authentication/concept-password-ban-bad.md)、[ハイブリッド ID](../active-directory/hybrid/whatis-hybrid-identity.md)、[条件付きアクセス](../active-directory/roles/permissions-reference.md#)、[動的グループ](../active-directory/enterprise-users/groups-create-rule.md)など)。 | [コンシューマー アカウント](user-overview.md#consumer-user)では、Azure AD 条件付きアクセス機能のサブセットがサポートされています。 Azure AD B2C の[条件付きアクセス](conditional-access-user-flow.md)を構成する方法を参照してください。|
| [Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) | Azure AD Premium P2 の機能で完全にサポートされています ([Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)、[Identity Governance](../active-directory/governance/identity-governance-overview.md) など)。  | コンシューマー アカウントでは、[Azure AD Identity Protection](user-overview.md#consumer-user) 機能のサブセットがサポートされています。 [Identity Protection を使用してリスクを調査](identity-protection-investigate-risk.md)し、Azure AD B2C の[条件付きアクセス](conditional-access-user-flow.md)を構成する方法を参照してください。 |

> [!NOTE]
> **テナント内のその他の Azure リソース:** <br>Azure AD B2C テナントでは、仮想マシン、Azure Web アプリ、Azure 関数などの他の Azure リソースをプロビジョニングすることはできません。 これらのリソースは、Azure AD テナントで作成する必要があります。