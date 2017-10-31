---
title: "既存の Azure サブスクリプションを Azure AD ディレクトリに追加する方法 | Microsoft Docs"
description: "既存のサブスクリプションを Azure AD ディレクトリに追加する方法"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: abf207a3ceec708a828170936f7dc7948ccf34a9
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2017
---
# <a name="how-to-add-an-azure-subscription-to-azure-active-directory"></a>Azure サブスクリプションを Azure Active Directory に追加する方法
この記事では、Azure サブスクリプションと Azure Active Directory (Azure AD) との関係、および既存のサブスクリプションを Azure AD ディレクトリに追加する方法について取り上げます。 ご利用の Azure サブスクリプションには、Azure AD との間に信頼関係があります。つまり、Azure サブスクリプションは、ユーザー、サービス、デバイスの認証に関して Azure AD ディレクトリを信頼します。 複数のサブスクリプションが同じディレクトリを信頼することはできますが、各サブスクリプションが信頼するディレクトリは 1 つだけです。 

サブスクリプションとディレクトリの間の信頼関係は、サブスクリプションと Azure 内の他のリソース (Web サイト、データベースなど) の間の関係とは異なります。 サブスクリプションの有効期限が切れた場合、サブスクリプションに関連付けられた他のリソースへのアクセスも停止します。 一方、Azure AD ディレクトリは Azure 内に残っており、別のサブスクリプションをそのディレクトリに関連付けたうえで、新しいサブスクリプションを使ってディレクトリを管理することができます。

すべてのユーザーにはそのユーザーを認証する 1 つのホーム ディレクトリがありますが、ユーザーは他のディレクトリのゲストになることもできます。 Azure AD で見えるのは、自分のユーザー アカウントが従属している (または guest になっている) ディレクトリだけです。

## <a name="to-add-an-existing-subscription-to-your-azure-ad-directory"></a>既存のサブスクリプションを Azure AD ディレクトリに追加するには
対象サブスクリプションが関連付けられている現在のディレクトリとその追加先となるディレクトリとの両方に存在するアカウントでサインインする必要があります。 

1. 所有権を譲渡するサブスクリプションのアカウント管理者であるアカウントで [Azure アカウント センター](https://account.azure.com/Subscriptions)にサインインします。
2. サブスクリプションの所有者となるユーザーが、譲渡先のディレクトリに存在することを確認します。
3. **[サブスクリプションの譲渡]** をクリックします。
4. 譲渡先を指定します。 譲渡先には、承認用のリンクが記載された電子メールが自動的に送信されます。
5. 譲渡先のユーザーは、リンクをクリックして指示に従います (支払情報の入力など)。 受信者が継承すると、サブスクリプションが譲渡されます。 
6. サブスクリプションの既定のディレクトリが、譲渡先のユーザーが属しているディレクトリに変更されます。

詳細については、「[別のアカウントに Azure サブスクリプションの所有権を譲渡する](../billing/billing-subscription-transfer.md)」を参照してください

## <a name="next-steps"></a>次のステップ
* Azure サブスクリプションの管理者を変更する方法について詳しくは、「[Azure サブスクリプションの所有権を別のアカウントに譲渡する](../billing/billing-subscription-transfer.md)」を参照してください。
* Microsoft Azure でリソース アクセスを制御する方法の詳細については、「 [Azure でのリソース アクセスについて](active-directory-understanding-resource-access.md)
* Azure AD でロールを割り当てる方法の詳細については、「 [Azure Active Directory (Azure AD) の管理者ロールの割り当て](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
