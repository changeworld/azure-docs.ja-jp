---
title: Azure 課金アカウントの連絡先情報を変更する
description: Azure 課金アカウントの連絡先情報を変更する方法について説明します
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 04/08/2021
ms.author: banders
ms.custom: contperf-fy21q4
ms.openlocfilehash: 72b1429ac93defd32510ab973697a0f2d5492999
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112289767"
---
# <a name="change-contact-information-for-an-azure-billing-account"></a>Azure 課金アカウントの連絡先情報を変更する

この記事は、Azure portal で "*課金アカウント*" の連絡先情報を更新するために役立ちます。 連絡先情報を更新する手順は、課金アカウントの種類によって異なります。 課金アカウントの詳細を確認し、ご自分の課金アカウントの種類を特定するには、[Azure portal での課金アカウントの表示](view-all-accounts.md)に関するページを参照してください。 Azure 課金アカウントは、Azure ユーザー アカウントおよび [Microsoft アカウント](https://account.microsoft.com/)とは別のものです。

Azure Active Directory のユーザー プロファイル情報を更新する場合は、ユーザー管理者のみが変更を行うことができます。 ユーザー管理者ロールが割り当てられていない場合は、ユーザー管理者に問い合わせてください。 ユーザーのプロファイルの変更の詳細については、「[Azure Active Directory を使用してユーザーのプロファイル情報を追加または更新する](../../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)」を参照してください。

*販売先住所* - 販売先住所は、課金アカウントの責任者である組織または個人の住所および連絡先情報です。 課金アカウントに対して生成されるすべての請求書に表示されます。

*請求先住所* - 請求先住所は、課金アカウントに対して生成された請求書の責任者である組織または個人の住所および連絡先情報です。 Microsoft Online Service Program (MOSP) の課金アカウントの場合、アカウントに対して生成されたすべての請求書に表示される 1 つの請求先住所があります。 Microsoft 顧客契約 (MCA) の課金アカウントの場合、課金プロファイルごとに請求先住所があり、課金プロファイルに対して生成された請求書に表示されます。

*サービスおよびマーケティング メール用の連絡先メール アドレス* - サインインに使用するメール アドレスとは異なるメール アドレスを指定して、ご利用の Azure アカウントに関しての請求、サービス、推奨量に関連した重要な通知を受け取ることができます。 アカウントで使用されているサービスに対する緊急のセキュリティ イシュー、価格の変更、破壊的変更などのサービス通知メールは、常にサインインアドレスに送信されます。

## <a name="update-an-mosp-billing-account-address"></a>MOSP 課金アカウントの住所を更新する

1. アカウントの管理者アクセス許可を持つメール アドレスを使用して、Azure portal にサインインします。
1. "**コスト管理 + 請求**" を検索します。  
    ![Azure portal での [コストの管理と請求] の検索を示すスクリーンショット](./media/change-azure-account-profile/search-cmb.png)
1. 左側で **[プロパティ]** を選択します。  
    ![MOSP 課金アカウントのプロパティを示すスクリーンショット](./media/change-azure-account-profile/update-contact-information-select-properties.png)
1. **[Update billing address]\(請求先住所の更新\)** を選択してから、販売先および請求先の住所を更新します。 新しい住所を入力して **[保存]** を選択します。  
    ![MOSP 課金アカウントの [住所の更新] を示すスクリーンショット](./media/change-azure-account-profile/update-contact-information-mosp.png)

## <a name="update-an-mca-billing-account-sold-to-address"></a>MCA 課金アカウントの販売先住所を更新する

1. Microsoft 顧客契約の課金アカウントの所有者または共同作成者のロールを持つメール アドレスを使用して、Azure portal にサインインします。
1. "**コスト管理 + 請求**" を検索します。  
    ![Azure portal 内で検索する場所を示すスクリーンショット](./media/change-azure-account-profile/search-cmb.png)
1. 左側の **[プロパティ]** を選択してから、 **[Update sold-to]\(販売先の更新\)** を選択します。  
    ![販売先住所を変更できる MCA 課金アカウントのプロパティを示すスクリーンショット](./media/change-azure-account-profile/update-sold-to-list-properties-mca.png)
1. 新しい住所を入力し、 **[保存]** を選択します。  
    ![MCA アカウントの販売先アドレスの更新を示すスクリーンショット](./media/change-azure-account-profile/update-sold-to-save-mca.png)

    > [!IMPORTANT]
    > 一部のアカウントでは、販売先を更新する前に追加認証が必要です。 アカウントで手動の承認が必要な場合、Azure サポートに連絡するよう求められます。

## <a name="update-an-mca-billing-account-address"></a>MCA 課金アカウントの住所を更新する

1. MCA の課金アカウントまたは課金プロファイルの所有者または共同作成者のロールを持つメール アドレスを使用して、Azure portal にサインインします。
1. "**コスト管理 + 請求**" を検索します。  
1. 左側の **[課金プロファイル]** を選択します。
1. 請求先住所を更新するには、課金プロファイルを選択します。  
    ![課金プロファイルを選択する [課金プロファイル] ページを示すスクリーンショット](./media/change-azure-account-profile/update-bill-to-list-profiles-mca.png)
1. 左側で **[プロパティ]** を選択します。
1. **[住所の更新]** を選択します。  
    ![住所を更新する場所を示すスクリーンショット](./media/change-azure-account-profile/update-bill-to-list-properties-mca.png)
1. 新しい住所を入力して **[保存]** を選択します。  
    ![住所の更新を示すスクリーンショット](./media/change-azure-account-profile/update-bill-to-save-mca.png)

## <a name="update-a-po-number"></a>発注番号を更新する

課金プロファイルの請求書には、既定では発注番号が関連付けられません。 課金プロファイルの請求書に発注番号が表示されるのは、課金プロファイルの発注番号を追加した後になります。

課金プロファイルの発注番号を追加または変更するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 「**コストの管理と請求**」を検索し、 **[課金スコープ]** を選択します。
1. ご使用の課金スコープを選択します。
1. 左側のメニューで、 **[課金]** の **[課金プロファイル]** を選択します。
1. 適切な課金プロファイルを選択します。
1. 左側のメニューの **[設定]** で、 **[プロパティ]** を選択します。
1. **[発注番号の更新]** を選択します。
1. 発注番号を入力し、 **[更新]** を選択します。

## <a name="update-your-tax-id"></a>税 ID を更新する

サブスクリプションの移動後は、必ず税 ID を更新してください。 この税 ID は、免税の計算に使用され、請求書に表示されます。

**課金アカウントの情報を更新するには**

1. [ビジネス向け Microsoft Store](https://businessstore.microsoft.com/) または[教育機関向け Microsoft Store](https://educationstore.microsoft.com/) にサインインします。
1. **[管理]** を選択し、 **[Billing accounts]\(課金アカウント\)** を選択します。
1. **[概要]** で **[Edit billing account information]\(課金アカウント情報の編集\)** を選択します。
1. 更新後、 **[保存]** を選択します。

[課金アカウントの設定を更新する方法の詳細](/microsoft-store/update-microsoft-store-for-business-account-settings)をご覧ください。


## <a name="service-and-marketing-emails"></a>サービスおよびマーケティングのメール

Azure portal では、90 日ごとにメール アドレスの確認または更新を求められます。 Microsoft では、次の Azure アカウント関連情報のメールを、このメール アドレスに送信します。

- サービスの通知
- セキュリティのアラート
- 請求の目的
- サポート
- マーケティングの通信
- Azure の使用状況に基づくベスト プラクティスのレコメンデーション

アカウントに関する通信を受け取るメール アドレスを入力します。 メール アドレスを入力することにより、Microsoft からの通信の受信にオプトインします。

![連絡先情報更新のプロンプトの例](./media/change-azure-account-profile/update-contact-information.png)

### <a name="change-your-contact-email-address"></a>連絡先のメール アドレスを変更する

次のいずれかの方法を使って、連絡先のメール アドレスを変更できます。 連絡先メール アドレスを更新しても、サインインに使用するメール アドレスは更新されません。

1. MOSP アカウントのアカウント管理者の場合は、「[MOSP 課金アカウントの住所を更新する](#update-an-mosp-billing-account-address)」の指示に従い、最後の手順で **[Update contact info]\(連絡先情報の更新\)** を選択します。 次に、新しいメール アドレスを入力します。
1. Azure portal の [[連絡先情報]](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade) 領域に移動して、新しいメール アドレスを入力します。 
1. Azure portal で、自分のイニシャルの付いたアイコンまたは画像を選択します。 次に、コンテキスト メニュー **[...]** を選択します。次に、メニューから **[My Contact Information]\(自分の連絡先情報\)** を選択し、新しいメール アドレスを入力します。

![Azure でのメール アドレスの更新の例](./media/change-azure-account-profile/azure-contact-information.png)

### <a name="opt-out-of-marketing-emails"></a>マーケティング メールをオプトアウトする

マーケティング メールの受信からオプトアウトするには:

1. [要求フォーム](https://account.microsoft.com/profile/permissions-link-request)に移動し、自分のプロファイルのメール アドレスを使って要求を送信します。 個人設定を更新するためのリンクをメールで受け取ります。
1. そのリンクを選択して、 **[Manage communication permissions]\(通信のアクセス許可の管理\)** ページを開きます。 このページには、メール アドレスがオプトインされているマーケティング通信の種類が表示されます。 オプトアウトする選択をオフにして、 **[保存]** を選択します。  
    ![通信のアクセス許可を管理するためのページの例](./media/change-azure-account-profile/manage-communication-permissions.png)

マーケティング通信をオプトアウトしても、アカウントに基づくサービスの通知はまだ受け取ります。

## <a name="update-the-email-address-that-you-sign-in-with"></a>サインインに使用するメール アドレスを更新する

アカウントへのアクセスに使用するメール アドレスは更新できません。 ただし、MOSP の課金アカウントがある場合は、新しいメール アドレスを使用して別のアカウントにサインアップし、サブスクリプションの所有権を次のアカウントに譲渡することができます。 MCA 課金アカウントの場合は、新しいメール アドレスのアクセス許可をアカウントに付与できます。

## <a name="update-your-credit-card"></a>クレジット カードを更新する

クレジット カードを更新する方法については、[Azure サブスクリプションの支払いに使用するクレジット カードの変更](change-credit-card.md)に関するページをご覧ください。

## <a name="update-your-country-or-region"></a>国または地域を更新する

既存のアカウントの国または地域の変更はサポートされていません。 ただし、別の国または地域で新しいアカウントを作成してから、Azure サポートに連絡してサブスクリプションを新しいアカウントに譲渡することができます。

## <a name="change-the-subscription-name"></a>サブスクリプション名を変更する

1. Azure portal にサインインし、左側のペインから **[サブスクリプション]** を選択して、名前を変更するサブスクリプションを選択します。
1. **[概要]** を選択し、コマンド バーから **[名前の変更]** を選択します。  
    ![Azure サブスクリプションの名前変更の例](./media/change-azure-account-profile/rename-sub.png)
1. 名前を変更したら、 **[保存]** を選択します。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- [請求先アカウントを表示する](view-all-accounts.md)
