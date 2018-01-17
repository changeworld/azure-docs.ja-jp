---
title: "既存の Azure サブスクリプションを Azure AD ディレクトリに追加する方法 | Microsoft Docs"
description: "既存のサブスクリプションを Azure AD ディレクトリに追加する方法"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: e063e6a46b6b99c4bbe749347e6887a930adb882
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2018
---
# <a name="how-to-associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Azure サブスクリプションを Azure Active Directory に関連付けるまたは追加する方法

この記事では、Azure サブスクリプションと Azure Active Directory (Azure AD) との関係、および既存のサブスクリプションを Azure AD ディレクトリに追加する方法について取り上げます。 ご利用の Azure サブスクリプションには、Azure AD との間に信頼関係があります。つまり、Azure サブスクリプションは、ユーザー、サービス、デバイスの認証に関して Azure AD ディレクトリを信頼します。 複数のサブスクリプションが同じディレクトリを信頼することはできますが、各サブスクリプションが信頼するディレクトリは 1 つだけです。 

サブスクリプションとディレクトリの間の信頼関係は、サブスクリプションと Azure 内の他のリソース (Web サイト、データベースなど) の間の関係とは異なります。 サブスクリプションの有効期限が切れた場合、サブスクリプションに関連付けられた他のリソースへのアクセスも停止します。 一方、Azure AD ディレクトリは Azure 内に残っており、別のサブスクリプションをそのディレクトリに関連付けたうえで、新しいサブスクリプションを使ってディレクトリを管理することができます。

すべてのユーザーにはそのユーザーを認証する 1 つのホーム ディレクトリがありますが、ユーザーは他のディレクトリのゲストになることもできます。 Azure AD で見えるのは、自分のユーザー アカウントが従属している (または guest になっている) ディレクトリだけです。

## <a name="before-you-begin"></a>開始する前に

* サブスクリプションに RBAC 所有者のアクセス権を持つアカウントでサインインする必要があります。
* 対象サブスクリプションが関連付けられている現在のディレクトリとその追加先となるディレクトリとの両方に存在するアカウントでサインインする必要があります。 別のディレクトリへのアクセスの取得の詳細については、「[Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)」を参照してください。

## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>既存のサブスクリプションを Azure AD ディレクトリに関連付けるには

1. Azure ポータルにサインインし、[[サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)でサブスクリプションを選択します。
2. **[ディレクトリの変更]** をクリックしします。

    ![[ディレクトリの変更] ボタンを示すスクリーンショット](./media/active-directory-how-subscriptions-associated-directory/edit-directory-button.PNG)
3. 警告を確認します。 サブスクリプションのディレクトリを変更すると、アクセス権が割り当てられているすべての[ロール ベースのアクセス制御 (RBAC)](role-based-access-control-configure.md) ユーザーとすべてのサブスクリプション管理者がアクセス権を失います。
4. ディレクトリを選択します。

    ![ディレクトリの変更 UI を示すスクリーンショット](./media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.PNG)
5. **[変更]** をクリックします。
6. 成功です。 ディレクトリ スイッチャーを使用して、新しいディレクトリに移動します。 すべてが正しく表示されるまで、最大で 10 分かかる場合があります。

    ![ディレクトリ変更の成功通知を示すスクリーンショット](./media/active-directory-how-subscriptions-associated-directory/edit-directory-success.PNG)

    ![スイッチャーを示すスクリーンショット](./media/active-directory-how-subscriptions-associated-directory/directory-switcher.PNG)


サブスクリプションのディレクトリの変更は、サービス レベルの操作です。 サブスクリプションの課金所有権には影響しないため、アカウント管理者は[アカウント センター](https://account.azure.com/subscriptions)を使用して引き続きサービス管理者を変更できます。 元のディレクトリを削除する場合は、新しいアカウント管理者にサブスクリプションの課金所有権を譲渡する必要があります。課金所有権を別のアカウントに譲渡するには、「[Azure サブスクリプションの所有権を別のアカウントに譲渡する](../billing/billing-subscription-transfer.md)」を参照してください。 

## <a name="next-steps"></a>次の手順

* 新しい Azure AD ディレクトリの無料作成の詳細については、「[Azure Active Directory テナントを取得する方法](develop/active-directory-howto-tenant.md)」を参照してください。
* Azure サブスクリプションの課金所有権の譲渡の詳細については、「[Azure サブスクリプションの所有権を別のアカウントに譲渡する](../billing/billing-subscription-transfer.md)」を参照してください。
* Microsoft Azure でリソース アクセスを制御する方法の詳細については、「 [Azure でのリソース アクセスについて](active-directory-understanding-resource-access.md)
* Azure AD でロールを割り当てる方法の詳細については、「 [Azure Active Directory (Azure AD) の管理者ロールの割り当て](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
