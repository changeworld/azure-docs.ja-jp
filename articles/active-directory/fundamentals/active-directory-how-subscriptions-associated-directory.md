---
title: 既存の Azure サブスクリプションをテナントに追加する - Azure Active Directory | Microsoft Docs
description: 既存の Azure サブスクリプションを Azure Active Directory テナントに追加する方法について説明します。
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: 4e4d4d9f34c8220ba70c64f073557f0293881c3e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53083871"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する
Azure サブスクリプションには、Azure Active Directory (Azure AD) との間に信頼関係があります。つまり、サブスクリプションは Azure AD を信頼することでユーザー、サービス、デバイスを認証します。 複数のサブスクリプションが同じ Azure AD ディレクトリを信頼することはできますが、1 つのサブスクリプションが信頼できるディレクトリは 1 つに限られます。

サブスクリプションの有効期限が切れた場合、サブスクリプションに関連付けられた他のすべてのリソースへのアクセスもできなくなります。 ただし、Azure AD ディレクトリは Azure に残るため、別の Azure サブスクリプションを使用してディレクトリの関連付けおよび管理を行うことができます。

すべてのユーザーに、認証のための "ホーム" ディレクトリが 1 つあります。 ただし、ユーザーは、他のディレクトリのゲストになることもできます。 Azure AD では、各ユーザーのホーム ディレクトリとゲスト ディレクトリの両方を確認できます。

>[!Important]
>サブスクリプションのディレクトリを変更すると、アクセス権が割り当てられているすべての[ロール ベースのアクセス制御 (RBAC)](../../role-based-access-control/role-assignments-portal.md) ユーザーとすべてのサブスクリプション管理者がアクセス権を失います。 また、キー コンテナーを所有する場合は、それらもサブスクリプションの移動による影響を受けます。 これを解決するには、操作を再開する前に[キー コンテナーのテナント ID を変更](../../key-vault/key-vault-subscription-move-fix.md)する必要があります。


## <a name="before-you-begin"></a>開始する前に
サブスクリプションの関連付けまたは追加を行う前に、次のタスクを実行する必要があります。

- 次のようなアカウントを使用してサインインします。
    - サブスクリプションにアクセスできる **RBAC 所有者**である。

    - サブスクリプションに関連付けられた現在のディレクトリと、今後サブスクリプションを関連付ける新しいディレクトリの両方に存在する。 別のディレクトリへのアクセスの取得の詳細については、[Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](../b2b/add-users-administrator.md)に関する記事を参照してください。

- Azure クラウド サービス プロバイダー (CSP) サブスクリプション (MS-AZR-0145P、MS-AZR-0146P、MS-AZR-159P)、Microsoft 内部サブスクリプション (MS-AZR-0015P)、Microsoft Imagine サブスクリプション (MS-AZR-0144P) を使用していないことを確認します。
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>既存のサブスクリプションを Azure AD ディレクトリに関連付けるには
1. Azure portal にサインインし、[[サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で使用するサブスクリプションを選択します。

2. **[ディレクトリの変更]** を選択します。

    ![[ディレクトリの変更] オプションが強調表示された [サブスクリプション] ページ](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. 表示されたすべての警告を確認してから、**[変更]** を選択します。

    ![ディレクトリの変更先が表示された [ディレクトリの変更] ページ](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    サブスクリプションのディレクトリが変更され、成功メッセージが表示されます。

    ![成功メッセージ](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)    

4. ディレクトリ スイッチャーを使用して、新しいディレクトリに移動します。 すべてが正しく表示されるまで、最大で 10 分かかる場合があります。

    ![ディレクトリ スイッチャーのページ](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

サブスクリプションのディレクトリの変更はサービス レベルの操作であるため、サブスクリプションの課金所有権には影響しません。 アカウント管理者は、引き続き[アカウント センター](https://account.azure.com/subscriptions)からサービス管理者を変更することができます。 元のディレクトリを削除するには、新しいアカウント管理者にサブスクリプションの課金所有権を譲渡する必要があります。課金所有権を別のアカウントに譲渡するには、「[Azure サブスクリプションの所有権を別のアカウントに譲渡する](../../billing/billing-subscription-transfer.md)」を参照してください。 

## <a name="next-steps"></a>次の手順

- 新しい Azure AD のテナントを作成するには、[Azure Active Directory へのアクセスによる新しいテナントの作成](active-directory-access-create-new-tenant.md)に関する記事を参照してください

- Microsoft Azure でリソース アクセスを制御する方法の詳細については、「 [Azure でのリソース アクセスについて](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Azure AD でのロールの割り当て方法の詳細については、[Azure Active Directory を使用したユーザーへのディレクトリ ロールの割り当て方法](active-directory-users-assign-role-azure-portal.md)に関する記事を参照してください
