---
title: Azure Active Directory 専用認証の Azure Policy
description: この記事では、Azure Active Directory (Azure AD) 専用認証を有効にして Azure SQL Database または Azure SQL Managed Instance を作成するために、Azure ポリシーを適用する方法について説明します
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/02/2021
ms.openlocfilehash: 7315b27fccf261dcf25ffe347a22fbb41e0d52cb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458800"
---
# <a name="azure-policy-for-azure-active-directory-only-authentication-with-azure-sql"></a>Azure SQL を使用した Azure Active Directory 専用認証の Azure Policy

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure Policy は、プロビジョニング中、[Azure AD 専用認証](authentication-azure-ad-only-authentication.md)を有効にして、Azure SQL Database または Azure SQL Managed Instance の作成を強制することができます。 このポリシーが適用されていると、[Azure 内で論理サーバー](logical-servers.md)を、またはマネージド インスタンスを作成しようとしても、Azure AD 専用認証を有効にして作成されていない場合は失敗します。

Azure Policy は、Azure サブスクリプション全体に適用することも、リソース グループ内だけに適用することも可能です。

Azure Policy には 2 つの新しい組み込みポリシーが導入されました。

- Azure SQL Database で、Azure Active Directory 専用認証が有効になっている必要があります
- Azure SQL Managed Instance で Azure Active Directory 専用認証を有効にする必要がある

Azure Policy の詳細については、「[Azure Policy とは](../../governance/policy/overview.md)」および「[Azure Policy の定義の構造](../../governance/policy/concepts/definition-structure.md)」を参照してください。

## <a name="permissions"></a>アクセス許可

Azure Policy の管理に必要なアクセス許可の概要については、「[Azure Policy における Azure RBAC アクセス許可](../../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)」を参照してください。

### <a name="actions"></a>アクション

カスタム役割を使用して Azure Policy を管理している場合は、次の[アクション](../../role-based-access-control/role-definitions.md#actions)が必要です。

- */read
- Microsoft.Authorization/policyassignments/*
- Microsoft.Authorization/policydefinitions/*
- Microsoft.Authorization/policyexemptions/*
- Microsoft.Authorization/policysetdefinitions/*
- Microsoft.PolicyInsights/*

カスタム ロールの詳細については、「[Azure カスタム ロール](../../role-based-access-control/custom-roles.md)」を参照してください。

## <a name="manage-azure-policy-for-azure-ad-only-authentication"></a>Azure AD 専用認証の Azure Policy を管理する

Azure AD 専用認証ポリシーを管理するには、[Azure portal](https://portal.azure.com) にアクセスし、**Policy** サービスを検索します。 **[定義]** で、"*Azure Active Directory 専用認証*" を検索します。

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/policy-azure-ad-only-authentication.png" alt-text="Azure AD 専用認証の Azure Policy のスクリーンショット":::

Azure AD 専用認証の Azure Policy を追加する方法のガイドについては、「[Azure Policy を使用して Azure SQL で Azure Active Directory 専用認証を適用する](authentication-azure-ad-only-authentication-policy-how-to.md)」を参照してください。

これらのポリシーには、次の 3 つの効果があります。

- **監査** - 既定の設定。Azure Policy アクティビティ ログ内で監査レポートをキャプチャするだけです
- **拒否** - [Azure AD 専用認証](authentication-azure-ad-only-authentication.md)を有効にせず、論理サーバーまたはマネージド インスタンスが作成されないようにします
- **無効** - ポリシーを無効にします。Azure AD 専用認証を有効にせず、ユーザーによる論理サーバーまたはマネージド インスタンスの作成は制限されません

Azure AD 認証専用の Azure Policy が **[拒否]** に設定されている場合、Azure SQL 論理サーバーまたはマネージド インスタンスの作成は失敗します。 このエラーの詳細は、リソース グループの **アクティビティ ログ** に記録されます。

## <a name="policy-compliance"></a>ポリシーへの準拠

**ポリシー** サービスの **[コンプライアンス]** 設定を表示して、コンプライアンスの状態を確認できます。 **コンプライアンスの状態** には、サーバーまたはマネージド インスタンスが現在、Azure AD 専用認証が有効である状態に準拠しているかどうかが示されます。 

Azure Policy は、Azure AD 専用認証を有効にせず、新しい論理サーバーやマネージド インスタンスが作成されないようにできますが、この機能は、サーバーやマネージド インスタンスの作成後に変更することができます。 サーバーまたはマネージド インスタンスの作成後、ユーザーが Azure AD 専用認証を無効にした場合、Azure Policy が **[拒否]** に設定されていると、コンプライアンス状態は `Non-compliant` になります。

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/check-compliance-policy-azure-ad-only-authentication.png" alt-text="Azure AD 専用認証の Azure Policy コンプライアンス メニューのスクリーンショット":::

## <a name="limitations"></a>制限事項

- Azure Policy は、論理サーバーまたはマネージド インスタンスの作成中に、Azure AD 専用認証を適用します。 サーバーが作成されると、特別なロールを持つ承認済み Azure AD ユーザー (SQL セキュリティ 管理者など) が、Azure AD 専用認証機能を無効にできます。 これは Azure Policy によって許可されていますが、この場合、サーバーまたはマネージド インスタンスはコンプライアンス レポートに `Non-compliant` と表示され、レポートには、サーバーまたはマネージド インスタンス名が示されます。  
- 注釈、既知の問題、必要なアクセス許可の詳細については、[Azure AD 専用認証](authentication-azure-ad-only-authentication.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Policy を使用して Azure SQL で Azure Active Directory 専用認証を適用する](authentication-azure-ad-only-authentication-policy-how-to.md)