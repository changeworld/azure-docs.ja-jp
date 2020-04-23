---
title: データ アクセスを許可するようにセキュリティを構成する - Azure Time Series Insights プレビュー |Microsoft Docs
description: Azure Time Series Insights プレビュー環境でセキュリティとアクセス許可を構成し、データ アクセス ポリシーを管理する方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: 1735daf66483df496141a642ac6633973aa0abf0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407543"
---
# <a name="grant-data-access-to-an-environment"></a>環境へのデータ アクセスの許可

この記事では、Azure Time Series Insights プレビューの 2 つのタイプのアクセス ポリシーについて説明します。

> [!TIP]
> Azure Active Directory アプリの登録手順については、[認証と承認](time-series-insights-authentication-and-authorization.md)に関する記事を参照してください。

## <a name="sign-in-to-time-series-insights"></a>Time Series Insights へのサインイン

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. Time Series Insights 環境を検索します。 **[検索ボックス]** に「`Time Series`」と入力します。 検索結果の **[Time Series Environment]\(Time Series 環境\)** を選択します。
1. リストから Time Series Insights 環境を選択します。

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

`Guest` ロールは、管理ロールではありません。 あるテナントから別のテナントに招待されたアカウントを示す用語として使用されます。 ゲスト アカウントは、テナントのディレクトリに招待されると、他のアカウントと同様に、同じアクセス制御を付与できます。 [アクセス制御 (IAM)] ブレードを使用して、Time Series Insights 環境に管理アクセスを許可できます。 または、[データ アクセス ポリシー] ブレードで、環境内のデータへのアクセスを許可できます。 Azure Active Directory (Azure AD) テナントのゲスト アクセスの詳細については、「[Azure Portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)」を参照してください。

別のテナントから Azure AD ユーザーに Time Series Insights 環境へのゲスト アクセスを付与するには、以下の手順を実行します。

1. **[データ アクセス ポリシー]** を選択し、 **[+ 招待]** をクリックします。

    [![[データ アクセス ポリシー] を選択して [+ 招待] を選択する](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. 招待するユーザーの電子メール アドレスを入力します。 この電子メール アドレスは、Azure AD に関連付ける必要があります。 招待状に個人的なメッセージを含めることもできます。

    [![メール アドレスを入力して、選択したユーザーを検索する](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. 画面に表示される確認のバブルを探します。

    [![表示される確認のバブルを探す](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. **[ユーザーの選択]** を選択します。 招待したゲスト ユーザーの電子メール アドレスを検索し、追加するユーザーを見つけます。 次に、 **[選択]** を選択して選択内容を確定します。

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


    ゲスト ユーザーが管理者のテナントを選択すると、共有されている Time Series Insights 環境を選択できるようになります。 
    
    ユーザーは、**手順 5.** で提供されたロールに関連付けられているすべての能力を持つようになりました。

    [![ゲスト ユーザーがドロップダウンから Azure テナントを選択する](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>次のステップ

* [Time Series Insights 環境に Azure Event Hubs のイベント ソースを追加する方法](./time-series-insights-how-to-add-an-event-source-eventhub.md)について説明します。

* イベント ソースに[イベントを送信](./time-series-insights-send-events.md)します。

* [Time Series Insights プレビュー エクスプローラー](./time-series-insights-update-explorer.md)で環境を表示します。
