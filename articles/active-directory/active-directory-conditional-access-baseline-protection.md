---
title: Azure Active Directory の条件付きアクセスにおけるベースラインの保護とは | Microsoft Docs
description: ベースラインの保護により、少なくともベースライン レベルのセキュリティを環境で有効にする方法について説明します。
services: active-directory
keywords: アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 25ae4db2cd4f2a2cea74c428a272c6868acaa5c5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248966"
---
# <a name="what-is-baseline-protection"></a>ベースラインの保護とは  

昨年は ID 攻撃が 300% 増加しました。 ますます増加する攻撃から環境を守るために、Azure AD (Azure AD) にはベースラインの保護と呼ばれる新しい機能が導入されています。 ベースラインの保護は、事前に定義された条件付きアクセス ポリシーのセットです。 これらのポリシーの目標は、少なくともベースライン レベルのセキュリティを環境で有効にすることです。 

プレビュー段階でベースライン ポリシーをアクティブにするには、機能を有効にする必要があります。 一般公開後は、これらのポリシーは既定で有効になります。 

最初のベースライン保護ポリシーには、特権アカウントの MFA が必要です。 攻撃者が特権アカウントを制御できる場合、多大な損害が発生する可能性があるので、まず特権アカウントを保護することが重要です。 このポリシーでは、次の特権ロールが範囲に含まれます。 

- 全体管理者  

- SharePoint 管理者  

- Exchange 管理者  

- 条件付きアクセス管理者  

- セキュリティ管理者  


![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/01.png)

## <a name="how-to-get-started"></a>ファースト ステップ 

ベースライン ポリシーを有効にするには:  

1. [Azure portal](https://portal.azure.com) にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。

2. **Azure Portal** の左側のナビゲーション バーで、**[Azure Active Directory]** をクリックします。

    ![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/02.png)

3. **[Azure Active Directory]** ページの **[管理]** セクションで、**[条件付きアクセス]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-baseline-protection/03.png)

4. ポリシーのリストで、**[Baseline policy: Require MFA for admins (Preview)]\(ベースライン ポリシー: 管理者に MFA を要求する (プレビュー)\)** をクリックします。 

5. ポリシーを有効にするには、**[ポリシーをすぐに使用する]** をクリックします。

6. **[Save]** をクリックします。 
 

ベースライン ポリシーを使用すると、ユーザーとグループを除外できるようになります。 テナントからロックアウトされないように、1 つの*[緊急アクセス管理アカウント](active-directory-admin-manage-emergency-access-accounts.md)* を除外することができます。
  
 

## <a name="what-you-should-know"></a>知っておくべきこと 

ベースライン ポリシーに含まれるディレクトリ ロールは、最も特権のある Azure AD ロールです。 フィードバックに基づいて、今後追加される可能性があります。 

スクリプトに管理者特権を持つアカウントがある場合は、代わりに[マネージド サービス ID (MSI)](managed-service-identity/overview.md) または[サービス プリンシパル (と証明書)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) を使用する必要があります。 一時的な回避策として、ベースライン ポリシーから特定のユーザー アカウントを除外することができます。 

このポリシーは、POP、IMAP、古い Office デスクトップ クライアントなどの従来の認証フローに適用されます。 

## <a name="next-steps"></a>次の手順

条件付きアクセスポリシーの構成方法については、[Azure Active Directory での条件付きアクセスの使用](active-directory-conditional-access-azure-portal-get-started.md)に関する記事を参照してください。

環境に適用する条件付きアクセス ポリシーを構成する準備ができたら、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](active-directory-conditional-access-best-practices.md)」を参照してください。 
