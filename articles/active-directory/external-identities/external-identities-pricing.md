---
title: Azure AD External Identities の MAU 課金モデル
description: Azure AD でのゲスト ユーザー コラボレーション (B2B) 向けの Azure AD External Identities の月間アクティブ ユーザー (MAU) 課金モデルについて説明します。 Azure AD テナントを Azure サブスクリプションにリンクする方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6e4926d9bad317027d1b290e0365c667b867df6
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113729652"
---
# <a name="billing-model-for-azure-ad-external-identities"></a>Azure AD External Identities の課金モデル

Azure Active Directory (Azure AD) External Identities の価格は、1 か月間に認証アクティビティを行った一意のユーザーの数である、月間アクティブ ユーザー数 (MAU) に基づいています。 この課金モデルは、Azure AD ゲスト ユーザー コラボレーション (B2B) と [Azure AD B2C テナント](../../active-directory-b2c/billing.md)の両方に適用されます。 MAU の課金は、無料レベルと柔軟で予測可能な価格を提供することによってコストを削減するのに役立ちます。 この記事では、MAU の課金と Azure AD テナントをサブスクリプションにリンクする方法について説明します。

> [!IMPORTANT]
> この記事には価格の詳細は含まれていません。 使用量の課金と価格の最新情報については、「[Azure Active Directory の価格](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)」を参照してください。

## <a name="what-do-i-need-to-do"></a>何をする必要がありますか?

MAU の課金を利用するには、Azure AD テナントを Azure サブスクリプションにリンクする必要があります。

|テナントが以下の場合:  |以下を実行する必要があります。  |
|---------|---------|
| Azure AD テナントは既にサブスクリプションにリンクされています     | 何もしない。 External Identities 機能を使用してゲスト ユーザーと共同作業する場合、MAU モデルを使用して自動的に課金されます。        |
| サブスクリプションにまだリンクされていない Azure AD テナント     | [Azure AD テナントをサブスクリプションにリンク](#link-your-azure-ad-tenant-to-a-subscription)して MAU の課金をアクティブ化します。        |
|  |  |

## <a name="about-monthly-active-users-mau-billing"></a>月間アクティブ ユーザー (MAU) の課金の概要

Azure AD テナントでは、ゲスト ユーザーの共同作業の使用量は、1 か月間の認証アクティビティを使用した一意のゲスト ユーザー数に基づいて課金されます。 このモデルは、テナントの Azure AD Premium ライセンスごとに最大 5 人のゲスト ユーザーが許可されていた 1:5 の比率の課金モデルに代わるものです。 テナントがサブスクリプションにリンクされており、External Identities 機能を使用してゲスト ユーザーと共同作業する場合、MAU ベースの課金モデルを使用して自動的に課金されます。

ゲスト ユーザーに適用される価格レベルは、Azure AD テナントに割り当てられている最も高い価格レベルに基づいて決まります。 詳細については、「[Azure Active Directory External Identities の価格](https://azure.microsoft.com/pricing/details/active-directory/external-identities/)」を参照してください。

## <a name="link-your-azure-ad-tenant-to-a-subscription"></a>Azure AD テナントをサブスクリプションにリンクする

適切な課金と機能へのアクセスのために、Azure AD テナントを Azure サブスクリプションにリンクする必要があります。 ディレクトリにリンクできるサブスクリプションがまだない場合は、このプロセス中に追加することができます。

1. サブスクリプション内、またはサブスクリプション内のリソース グループ内では、[共同作成者](../../role-based-access-control/built-in-roles.md)以上のロールが割り当てられている Azure アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。

2. リンクするディレクトリを選択します。Azure portal のツール バーで、 **[ディレクトリ + サブスクリプション]** アイコンを選択し、ディレクトリを選択します。

    ![[ディレクトリ + サブスクリプション] アイコンを選択します](media/external-identities-pricing/portal-mau-pick-directory.png)

3. **[Azure サービス]** で **[Azure Active Directory]** を選択します。

4. 左側のメニューで、 **[External Identities]** を選択します。

5. **[サブスクリプション]** で **[Linked subscriptions]\(リンク済みのサブスクリプション\)** を選択します。

6. テナント一覧で、テナントの横にあるチェックボックスをオンにして、 **[Link subscription]\(サブスクリプションのリンク\)** を選択します。

    ![テナントを選択してサブスクリプションをリンクする](media/external-identities-pricing/linked-subscriptions.png)

7. [Link subscription]\(サブスクリプションのリンク\) ペインで、 **[サブスクリプション]** と **[リソース グループ]** を選択します。 次に、**[適用]** を選択します。

   > [!NOTE]
   >
   > * Premium P1 機能と Premium P2 機能の両方で、1 か月あたり最初の 50,000 人の MAU については無料となります。 MAU の総数を確認するために、Microsoft では同じサブスクリプションにリンクされているすべてのテナント (Azure AD と Azure AD B2C の両方) の MAU を結合します。
    >* サブスクリプションが表示されていない場合は、[サブスクリプションをテナントに関連付ける](../fundamentals/active-directory-how-subscriptions-associated-directory.md)ことができます。 または、 **[サブスクリプションをまだお持ちでない場合は、こちらで作成できます]** のリンクを選択して、新しいサブスクリプションを追加することもできます。

    ![サブスクリプションとリソース グループを選択する](media/external-identities-pricing/link-subscription-resource.png)

これらの手順を完了すると、Azure Direct または Enterprise Agreement の条項 (該当する場合) に基づいて Azure サブスクリプションに課金されます。

## <a name="next-steps"></a>次のステップ

最新の価格情報については、「[Azure Active Directory の価格](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)」を参照してください。