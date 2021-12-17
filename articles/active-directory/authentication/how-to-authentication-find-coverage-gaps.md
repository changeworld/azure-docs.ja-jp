---
title: Azure Active Directory で管理者の強力な認証範囲にギャップがないか見つけて対処する
description: Azure Active Directory で管理者の強力な認証範囲にギャップがないか見つけて対処する方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/03/2021
ms.author: justinha
author: inbarckMS
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e52deae12ae44fbf0eb52b8377960530459fca7
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717660"
---
# <a name="find-and-address-gaps-in-strong-authentication-coverage-for-your-administrators"></a>管理者の強力な認証範囲にギャップがないか見つけて対処する

テナントの管理者に対して多要素認証 (MFA) を要求することは、テナントのセキュリティを強化するために実行できる最初の手順の 1 つです。 この記事では、すべての管理者を確実に多要素認証の対象とする方法について説明します。

## <a name="detect-current-usage-for-azure-ad-built-in-administrator-roles"></a>Azure AD の組み込み管理者ロールの現在の使用状況を検出する

[Azure AD のセキュリティ スコア](../fundamentals/identity-secure-score.md)を使用すると、テナントで **管理者ロールに対して MFA が必須になっているかどうか** についてのスコアが提供されます。 この改善アクションによって、グローバル管理者、セキュリティ管理者、Exchange サービス管理者、SharePoint サービス管理者の MFA 使用状況が追跡されます。 

管理者が MFA ポリシーの対象となっているかどうかは、さまざまな方法で確認できます。 

- 特定の管理者のサインインのトラブルシューティングを行う場合は、サインイン ログを使用できます。 サインイン ログでは、特定のユーザーの **[認証要件]** をフィルター処理できます。 **[認証要件]** が **[Single-factor authentication]\(単一要素認証\)** となっているサインインは、サインインに必要な多要素認証ポリシーが適用されていないことを意味します。

  ![サインイン ログのスクリーンショット。](./media/how-to-authentication-find-coverage-gaps/auth-requirement.png)

  **[認証の詳細]** をクリックして、[MFA 要件に関する詳細](../reports-monitoring/concept-sign-ins.md#authentication-details)を表示します。
  
  ![認証アクティビティの詳細のスクリーンショット。](./media/how-to-authentication-find-coverage-gaps/details.png)

- ユーザー ライセンスに基づいて有効にするポリシーを選択できるように、[MFA ポリシーを比較し](concept-mfa-licensing.md#compare-multi-factor-authentication-policies)、組織にとって最適な手順を確認するうえで役立つ新しい MFA 有効化ウィザードが用意されています。 ウィザードには、過去 30 日間に MFA によって保護された管理者が表示されます。

  ![多要素認証有効化ウィザードのスクリーンショット。](./media/how-to-authentication-find-coverage-gaps/wizard.png)

- テナント内で管理者ロールを持つすべてのユーザーとその強力な認証ステータスを一覧表示するレポートをプログラムで作成するには、[PowerShell スクリプト](https://github.com/microsoft/AzureADToolkit/blob/main/src/Find-UnprotectedUsersWithAdminRoles.ps1)を実行できます。 このスクリプトを使用すると、すべての永続的および有資格の組み込みロールとカスタム ロールの割り当て、およびロールが割り当てられているグループを列挙し、認証方法とサインイン アクティビティを評価することによって MFA に登録されていないユーザーまたは MFA を使用してサインインしていないユーザーを見つけることができます。

## <a name="enforce-multi-factor-authentication-on-your-administrators"></a>管理者に多要素認証を適用する

見つかったギャップに基づいて、次のいずれかの方法で管理者に多要素認証の使用を要求します。

- 管理者に Azure AD Premium のライセンスが付与されている場合は、[条件付きアクセス ポリシーを作成](tutorial-enable-azure-mfa.md)して管理者に MFA を適用することができます。 このポリシーを更新することで、カスタム ロールに含まれるユーザーに MFA を要求することもできます。  

- [MFA 有効化ウィザード](https://aka.ms/MFASetupGuide)を実行して MFA ポリシーを選択します。

- [Privileged Identity Management](../privileged-identity-management/pim-configure.md) でカスタムまたは組み込みの管理者ロールを割り当てると、ロールのアクティブ化時に多要素認証が必要になります。

## <a name="use-passwordless-and-phishing-resistant-authentication-methods-for-your-administrators"></a>管理者に対してパスワードレスおよびフィッシングに抵抗できる認証方法を使用する

管理者に多要素認証を適用してからしばらく経ったら、さらに強力な認証になるようにハードルを上げて、パスワードレスおよびフィッシングに抵抗できる認証方法を使用するようにします。 

- [電話によるサインイン (Microsoft Authenticator を使用)](concept-authentication-authenticator-app.md)
- [FIDO2](concept-authentication-passwordless.md#fido2-security-keys)
- [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-overview)

これらの認証方法とそのセキュリティに関する考慮事項の詳細については、[Azure AD の認証方法](concept-authentication-methods.md)に関するページ参照してください。