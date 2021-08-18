---
title: Microsoft 顧客契約の課金アカウントでテナントを管理する - Azure
description: この記事は、Microsoft 顧客契約の課金アカウントに関連付けられているテナントを把握し、管理するうえで役に立ちます。
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: microsoft-customer-agreement
ms.topic: conceptual
ms.date: 05/05/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: 9fe8fc7660bf63e13f8286900bbfab5d8399d2c8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737020"
---
# <a name="manage-tenants-in-your-microsoft-customer-agreement-billing-account"></a>Microsoft 顧客契約の課金アカウントでテナントを管理する

この記事は、Microsoft 顧客契約の課金アカウントに関連付けられているテナントを把握し、管理するうえで役に立ちます。 この情報を使用することで、課金環境へのアクセスをセキュリティで保護しながら、テナントの管理、サブスクリプションの譲渡、課金所有権の管理を行うことができます。

## <a name="whats-a-tenant"></a>テナントとは

テナントは組織のデジタル表現であり、Microsoft.com のようなドメインに主に関連付けられています。 これは Azure Active Directory を介して管理される環境であり、Azure のリソースや課金を管理するためのアクセス許可をユーザーに割り当てることができます。

各テナントは他のテナントから明確に分離されていますが、お使いのテナントに他のテナントのゲスト ユーザーがアクセスして、コストの追跡や課金の管理を行うことができるように設定できます。

## <a name="how-tenants-and-subscriptions-relate-to-billing-account"></a>テナントおよびサブスクリプションと課金アカウントの関係

Microsoft 顧客契約 (課金アカウント) を使用して、コストの追跡と課金の管理を行います。 各課金アカウントには、少なくとも 1 つの課金プロファイルがあります。 課金プロファイルを使用すると、請求書と支払方法を管理できます。 既定では、課金プロファイルごとに 1 つの請求書セクションが含まれています。 必要に応じて、さらに多くの請求書セクションを作成し、より細かなレベルでコストをグループ化、追跡、管理することもできます。

- 課金アカウントは 1 つのテナントに関連付けられます。 これは、そのテナントに属するユーザーのみが課金アカウントにアクセスできることを意味します。
- 課金アカウントに新しい Azure サブスクリプションを作成する場合、それは常に課金アカウントのテナント内に作成されます。 ただし、サブスクリプションは他のテナントに移動できます。 また、既存のサブスクリプションを他のテナントから自分の課金アカウントにリンクすることもできます。 これにより、1 つのテナントを通じて課金を一元管理しながら、ニーズに基づいて他のテナントにリソースとサブスクリプションを維持することができます。

次の図は、課金アカウントとサブスクリプションがどのようにテナントにリンクされているかを示しています。 Contoso MCA 課金アカウントはテナント 1 に関連付けられており、Contoso PAYG アカウントはテナント 2 に関連付けられています。 Contoso が MCA 課金アカウントを通じて PAYG サブスクリプションの支払を行うことを希望している場合、課金所有権の譲渡を使用して、このサブスクリプションを MCA 課金アカウントにリンクすることができます。 サブスクリプションとそのリソースは引き続きテナント 2 に関連付けられていますが、支払は MCA 課金アカウントを使用して行われます。

:::image type="content" source="./media/manage-tenants/billing-hierarchy-example.png" alt-text="課金階層の例を示す図。" border="false" lightbox="./media/manage-tenants/billing-hierarchy-example.png":::

## <a name="manage-subscriptions-under-multiple-tenants-in-a-single-microsoft-customer-agreement"></a>1 つの Microsoft 顧客契約で複数のテナントのサブスクリプションを管理する

課金所有者は、課金アカウントに対する[適切なアクセス許可](../manage/understand-mca-roles.md#subscription-billing-roles-and-tasks)がある場合にサブスクリプションを作成できます。 既定では、Microsoft 顧客契約の下で作成された新しいサブスクリプションは、Microsoft 顧客契約テナントに含まれます。

- 他のテナントのサブスクリプションを Microsoft 顧客契約の課金アカウントにリンクできます。 サブスクリプションの課金所有権を取得しても、請求の処理が変更されるだけです。 サービス テナントや Azure RBAC ロールには影響しません。
- サービス テナントのサブスクリプション所有者を変更するには、[別の Azure Active Directory ディレクトリにサブスクリプション](../../role-based-access-control/transfer-subscription.md)を譲渡する必要があります。

MCA 課金アカウントは、1 つのテナントまたはディレクトリによって管理されます。 課金アカウントが管理するのは、そのテナント内のサブスクリプションの課金のみです。 ただし課金所有権の譲渡を使用すると、異なるテナントの課金アカウントにサブスクリプションをリンクすることができます。

### <a name="billing-ownership-transfer"></a>課金所有権の譲渡

課金所有権の譲渡で変更されるのは、1 つのサブスクリプションに対する請求書の段取りだけです。 サブスクリプションのユーザーとリソースの管理は変更されません。

課金所有権の譲渡では、次の 2 つの処理が実行されます。

- サブスクリプションの元の課金所有権が削除されます。
- サブスクリプションの課金所有権が、ターゲットの課金アカウントに "*リンク*" されます。ターゲットの課金アカウントは、異なるテナントまたはディレクトリに存在していてもかまいません。

課金所有権の譲渡は、次の要素には影響しません。

- ユーザー
- リソース
- Azure RBAC アクセス許可


## <a name="add-guest-users-to-your-microsoft-customer-agreement-tenant"></a>Microsoft 顧客契約テナントにゲスト ユーザーを追加する

Microsoft 顧客契約の課金テナントに追加されたユーザーが別のテナントの課金の責任を管理するためには、ゲストとして招待される必要があります。

だれかをゲストとして招待するには、自分の Azure Active Directory (AD) ドメインとは異なるドメインを含む既存のメール アドレスをそのユーザーが持っている必要があります。 Azure AD は、認証用のリンクを含むメールをゲスト ユーザーに送信します。

:::image type="content" source="./media/manage-tenants/guest-invitation-email.png" alt-text="招待メールの例を示すスクリーンショット。" lightbox="./media/manage-tenants/guest-invitation-email.png" :::

ユーザーを Microsoft 顧客契約テナントに追加する際に、そのユーザーが[招待を承諾](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)する必要があります。

**[招待の承諾]** リンクを選択すると、Azure での認証を求めるメッセージが表示されます。

:::image type="content" source="./media/manage-tenants/authenticate.png" alt-text="Azure での認証を求めるメッセージを示すスクリーンショット。" lightbox="./media/manage-tenants/authenticate.png" :::

次に、 **[承諾]** を選択します。

:::image type="content" source="./media/manage-tenants/accept-invitation.png" alt-text="招待の承諾を求めるメッセージを示すスクリーンショット。" lightbox="./media/manage-tenants/accept-invitation.png" :::

ユーザーが承諾すると、[[コストの管理と請求] で Microsoft 顧客契約の課金アカウントを表示](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement)できるようになります。

:::image type="content" source="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" alt-text="課金アカウントの一覧にある Microsoft 顧客契約を示すスクリーンショット。" lightbox="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" :::

ゲスト ユーザーを招待するための承認は、Azure AD の設定によって制御されます。 設定の値は、 **[組織の関係]** ページの **[設定]** に表示されます。 設定が選択されていることを確認します。選択されていない場合、招待は失敗します。詳細については、[ゲスト ユーザーのアクセス許可の制限](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)に関する記事をご覧ください。

:::image type="content" source="./media/manage-tenants/external-collaboration-settings.png" alt-text="外部コラボレーションの設定を示すスクリーンショット。" lightbox="./media/manage-tenants/external-collaboration-settings.png" :::

> [!IMPORTANT]
> ゲスト ユーザーは Microsoft 顧客契約テナントにアクセスできるため、セキュリティ上の問題が生じる可能性があります。 詳細については、[ゲスト ユーザーの既定のアクセス許可を制限する方法](../../active-directory/fundamentals/users-default-permissions.md#restrict-member-users-default-permissions)に関する記事をご覧ください。

## <a name="manage-multiple-microsoft-cloud-services-under-an-azure-ad-tenant"></a>Azure AD テナントで複数の Microsoft クラウド サービスを管理する

1 つの Azure AD テナントの下で、組織の複数のクラウド サービスを管理できます。 Microsoft のすべてのクラウド サービスのユーザー アカウントは、Azure AD テナントに格納されます。そこには、ユーザー アカウントとグループが含まれます。 次の図は、複数のサービスで、アカウントを含む共通の Azure AD テナントを使用している組織の例を示しています。 サービスごとに、ユーザーがサービスを管理するための独自のポータル (青字) があります。

:::image type="content" source="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png" alt-text="複数のサービスで、アカウントを含む共通の Azure AD テナントを使用している組織の例を示す図。" border="false" lightbox="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png":::

## <a name="next-steps"></a>次の手順

次の記事を読んで、課金所有権を柔軟に管理し、Microsoft 顧客契約への安全なアクセスを確保する方法を確認してください。

- [テナントを設定する方法](../../active-directory/develop/quickstart-create-new-tenant.md)
- [Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)
- [Azure サブスクリプションを別の Azure AD ディレクトリに移転する](../../role-based-access-control/transfer-subscription.md)
- [Azure Active Directory でゲストのアクセス許可を制限する (プレビュー)](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)
- [Azure portal でディレクトリにゲスト ユーザーを追加する](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Azure Active Directory の既定のユーザー アクセス許可とは](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Azure Active Directory とは](../../active-directory/fundamentals/active-directory-whatis.md)