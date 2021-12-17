---
title: データ アクセスを許可するようにセキュリティを構成する - Azure Time Series Insights |Microsoft Docs
description: Azure Time Series Insights 環境でセキュリティとアクセス許可を構成し、データ アクセス ポリシーを管理する方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: tedvilutis
ms.author: tvilutis
manager: dviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: ebbb3c02263d860822482e2e19293d9a032274ef
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2021
ms.locfileid: "113135363"
---
# <a name="grant-data-access-to-an-environment"></a>環境へのデータ アクセスの許可

この記事では、Azure Time Series Insights の 2 種類のアクセス ポリシーについて説明します。

> [!Warning]
> アクセス ポリシーは、Azure AD のユーザーまたはグループに、Time Series Insights 環境へのデータ プレーン アクセスを許可します。
> Azure Active Directory はテナントに関連付けられます。 そのため、テナント間でサブスクリプションを移動することになった場合は、必ず[以下のセクション](#procedure-for-when-the-subscription-is-moved-across-tenants)の手順に従ってください。

## <a name="sign-in-to-azure-time-series-insights"></a>Azure Time Series Insights にサインインする

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. **[検索]** ボックスに「`Time Series Insights environments`」と入力して、Azure Time Series Insights 環境を見つけます。 検索結果にある `Time Series Insights environments` を選択します。
1. 一覧から Azure Time Series Insights 環境を選択します。

## <a name="grant-data-access"></a>データ アクセスの許可

次の手順に従って、ユーザー プリンシパルにデータ アクセスを許可します。

1. **[データ アクセス ポリシー]** を選択し、 **[+ 追加]** をクリックします。

    [![データ アクセス ポリシーの選択と追加](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. **[ユーザーの選択]** を選択します。 ユーザー名または電子メール アドレスを検索して追加するユーザーを探します。 **[選択]** を選択して選択内容を確定します。

    [![追加するユーザーの選択](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. **[ロールの選択]** を選択します。 ユーザーに割り当てる適切なアクセス ロールを選択します。

    * ユーザーに対して、参照データの変更に加え、保存クエリとパースペクティブを環境の他のユーザーと共有することを許可する場合は **[共同作成者]** を選択します。

    * それ以外の場合は **[閲覧者]** を選択し、環境内のデータへのクエリ実行と環境内の個人用 (共有されない) クエリの保存をユーザーに許可します。

   **[OK]** を選び、ロールの選択を確定します。

    [![選択したロールの確定](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. **[ユーザー ロールの選択]** ページで **[OK]** を選択します。

    [![[ユーザー ロールの選択] ページで [OK] を選択する](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. **[データ アクセス ポリシー]** ページに、ユーザーと各ユーザーのロールが一覧表示されていることを確認します。

    [![正しいユーザーおよびロールの確認](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>別の Azure AD テナントからのゲスト アクセスの提供

`Guest` ロールは管理ロールではありません。 あるテナントから別のテナントに招待されたアカウントを示す用語として使用されます。 テナントのディレクトリに招待されると、ゲスト アカウントには、他のアカウントと同様に、同じアクセス制御を適用できます。 [アクセス制御 (IAM)] ブレードを使用して、Azure Time Series Insights 環境に管理アクセスを許可できます。 または、[データ アクセス ポリシー] ブレードで、環境内のデータへのアクセスを許可できます。 Azure Active Directory (Azure AD) テナントのゲスト アクセスの詳細については、「[Azure Portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../active-directory/external-identities/add-users-administrator.md)」を参照してください。

別のテナントから Azure AD ユーザーに Azure Time Series Insights 環境へのゲスト アクセスを付与するには、以下の手順を実行します。

1. Azure portal に移動し、 **[Azure Active Directory]** をクリックし、 **[概要]** タブを下にスクロールして、 **[ゲスト ユーザー]** を選択します。

    [![[データ アクセス ポリシー] を選択して [+ 招待] を選択する](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. 招待するユーザーの電子メール アドレスを入力します。 この電子メール アドレスは、Azure AD に関連付ける必要があります。 招待状に個人的なメッセージを含めることもできます。

    [![メール アドレスを入力して、選択したユーザーを検索する](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. 画面に表示される確認のバブルを探します。 また、 **[通知]** をクリックして、ゲスト ユーザーが追加されたことを確認することもできます。

    [![表示される確認のバブルを探す](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Time Series Insights 環境に戻り、新しく作成されたゲスト ユーザーを追加します。 **[データ アクセスの許可]** の説明に従って、 **[データ アクセス ポリシー]** をクリックします。 **ユーザーを選択します**。 招待したゲスト ユーザーの電子メール アドレスを検索し、追加するユーザーを見つけます。 次に、 **[選択]** を選択して選択内容を確定します。

    [![ユーザーを選択して選択内容を確定する](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. **[ロールの選択]** を選択します。 ゲスト ユーザーに割り当てる適切なアクセス ロールを選択します。

    * ユーザーに対して、参照データの変更に加え、保存クエリとパースペクティブを環境の他のユーザーと共有することを許可する場合は **[共同作成者]** を選択します。

    * それ以外の場合は **[閲覧者]** を選択し、環境内のデータへのクエリ実行と環境内の個人用 (共有されない) クエリの保存をユーザーに許可します。

   **[OK]** を選び、ロールの選択を確定します。

    [![ロールの選択の確定](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. **[ユーザー ロールの選択]** ページで **[OK]** を選択します。

1. **[データ アクセス ポリシー]** ページに、ゲスト ユーザーと各ゲスト ユーザーのロールが一覧表示されていることを確認します。

    [![ユーザーとロールが正しく割り当てられていることを確認する](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. これで、ゲスト ユーザーは、上で指定したメール アドレスで招待メールを受信します。 ゲスト ユーザーは **[使ってみる]** を選択して承諾を確認し、Azure Cloud に接続します。

    [![ゲストが [使ってみる] を選択して承諾する](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. **[使ってみる]** を選択すると、管理者の組織に関連付けられているアクセス許可ボックスがゲスト ユーザーに表示されます。 **[承諾]** を選択してアクセス許可を付与すると、そのユーザーはサインインされます。

    [![ゲストがアクセス許可を確認して承諾する](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. 管理者は、ゲストと[環境 URL を共有します](time-series-insights-parameterized-urls.md)。

1. 招待に使用されたメール アドレスに対してゲスト ユーザーがサインインして、招待を承諾すると、そのユーザーは Azure portal に誘導されます。

1. ゲストは、管理者が提供する環境 URL を使用して共有環境にアクセスできるようになります。 この URL を Web ブラウザーに入力すると、すぐにアクセスできます。

1. ゲスト ユーザーが管理者のテナントを表示するには、Time Series エクスプローラーの右上隅にある自分のプロファイル アイコンを選択します。

    [![insights.azure.com でのアバターの選択](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)

    ゲスト ユーザーが管理者のテナントを選択すると、共有されている Azure Time Series Insights 環境を選択できるようになります。

    ユーザーは、**手順 5.** で提供されたロールに関連付けられているすべての能力を持つようになりました。

    [![ゲスト ユーザーがドロップダウンから Azure テナントを選択する](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="procedure-for-when-the-subscription-is-moved-across-tenants"></a>テナント間でサブスクリプションを移動する場合の手順

Time Series Insights のデータ アクセス ポリシーは Azure Active Directory を拠り所としており、また Azure Active Directory は、サブスクリプションがある Azure テナントに関連付けられます。

データ アクセス ポリシーを許可する Azure AD オブジェクトと、Time Series Insights 環境そのものが、同じテナントに存在していなければなりません。 それ以外の場合、それらのオブジェクトが環境にアクセスすることはできません。

その環境があるサブスクリプションを別のテナントに移動する予定がある場合、新しいテナントの Azure AD オブジェクトを反映するようにデータ アクセス ポリシーが更新されるようにする必要があります。

このプロセスを円滑化するには、以下の手順に従ってください。

### <a name="before-moving-a-subscription-to-another-tenant"></a>サブスクリプションを別のテナントに移動する前に

- 環境の最新のデータ アクセス ポリシー割り当てリストが移動元のテナントにある間に、それを保存します。
- 移動先のテナントの Active Directory にサブスクリプションが移行された後も、ユーザー、グループ、アプリに環境へのアクセス権が維持されるようにします。
- サブスクリプションの移動後、移動先テナントの環境にデータ アクセス ポリシーを再適用できるよう、サブスクリプションに対する共同作成者以上のアクセス権を自分に確保 (または、そのようなアクセス権を持つユーザーと連携) してください。

### <a name="after-moving-a-subscription-to-another-tenant"></a>サブスクリプションを別のテナントに移動した後

移動先テナントのサブスクリプションに対する共同作成者のアクセス権があるユーザーは、次のことを実行できます。

- 移行済みのデータ アクセス ポリシーは移動元のテナントに属しているため、環境と共にすべて削除できます。
- 上記の手順を使用して環境にアクセス ポリシーを再付与します。以後、それらのアクセス ポリシーは、移動先テナント内の Azure AD オブジェクトを参照するようになります。

## <a name="next-steps"></a>次のステップ

* Azure Active Directory アプリの登録手順については、[認証と承認](time-series-insights-authentication-and-authorization.md)に関する記事を参照してください。

* [Azure Time Series Insights Explorer で自分の環境](./concepts-ux-panels.md)を表示します。
