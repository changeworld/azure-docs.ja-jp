---
title: Azure の請求書を表示してダウンロードする
description: Azure の請求書の表示とダウンロードの方法を説明します。
keywords: 請求書,請求書のダウンロード,Azure の請求書,Azure の使用状況
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: banders
ms.openlocfilehash: 16534343a831f0802a60a9214f567742153360e0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478998"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Microsoft Azure の請求書の表示とダウンロード

請求書は、[Azure portal](https://portal.azure.com/) からダウンロードするか、メールで送信することができます。 マイクロソフト エンタープライズ契約 (EA) を締結している Azure ユーザー (EA のお客様) が、その組織の請求書をダウンロードすることはできません。 請求書は、登録のために請求書が届くよう設定されている担当者に送信されます。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="when-invoices-are-generated"></a>いつ請求書が生成されるか

請求書は、課金アカウントの種類に基づいて生成されます。 Microsoft Online Services Program (MOSP)、Microsoft 顧客契約 (MCA)、Microsoft Partner Agreement (MPA) の各課金アカウントの請求書が作成されます。 マイクロソフト エンタープライズ契約 (EA) 課金アカウントの請求書も生成されます。 ただし EA 課金アカウントの請求書は、Azure portal に表示されません。

課金アカウントの詳細を確認し、ご自分の課金アカウントの種類を特定するには、[Azure portal での課金アカウントの表示](../manage/view-all-accounts.md)に関するページを参照してください。

## <a name="invoices-for-mosp-billing-accounts"></a>MOSP 課金アカウントの請求書

MOSP 課金アカウントは、Azure Web サイトを通じて Azure にサインアップしたときに作成されます。 たとえば、[Azure 無料アカウント](https://azure.microsoft.com/offers/ms-azr-0044p/)または[従量課金制料金のアカウント](https://azure.microsoft.com/offers/ms-azr-0003p/)にサインアップした場合や、[Visual Studio サブスクライバー](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)としてサインアップした場合です。

一部のリージョンでは、Azure Web サイトから[従量課金制料金のアカウント](https://azure.microsoft.com/offers/ms-azr-0003p/)または [Azure 無料アカウント](https://azure.microsoft.com/offers/ms-azr-0044p/)にサインアップしたお客様にも、MCA の課金アカウントが作成される場合があります。

自分の課金アカウントの種類がわからない場合は、この記事の手順に進む前に、[自分の課金アカウントの種類の確認](../manage/view-all-accounts.md#check-the-type-of-your-account)に関するセクションを参照してください。 

MOSP 課金アカウントには、次の請求書が存在する可能性があります。

**Azure サービスの料金** - Azure サブスクリプションごとに、そのサブスクリプションで使用された Azure リソースを含む請求書が生成されます。 請求書には、請求期間の料金が含まれます。 請求期間は、サブスクリプションが作成された日付によって決まります。

たとえば、John が *Azure sub 01* を 3 月 5 日に、*Azure sub 02* を 3 月 10 日に作成したとします。 *Azure sub 01* の請求書には、月の 5 日目から翌月の 4 日目までの料金が記載されます。 *Azure sub 02* の請求書には、月の 10 日目から翌月の 9 日目までの料金が記載されます。 すべての Azure サブスクリプションの請求書は通常、アカウントが作成されたその日に生成されますが、最大で 2 日後となる場合があります。 この例で、John が 2 月 2 日にアカウントを作成した場合、*Azure sub 01* と *Azure sub 02* の請求書はどちらも通常は、各月の 2 日目に生成されますが、最大で 2 日後に生成される可能性もあります。

**Azure Marketplace、予約、スポット VM** - 請求書は、サブスクリプションを使用して購入した予約、マーケットプレース製品、スポット VM について生成されます。 請求書には、前月からの個別の料金が含まれます。 たとえば、John が 3 月 1 日に予約を購入し、さらに別の予約を 3 月 30 日に購入したとします。 請求書は、両方の予約について 1 枚だけ 4 月に生成されます。 Azure Marketplace、予約、スポット VM の請求書は常に、月の 9 日目頃に生成されます。

Azure の支払いにクレジット カードを使用して予約を購入した場合、Azure によって即座に請求書が生成されます。 ただし請求書による課金の場合、予約に対する課金は翌月の請求書で行われます。

**Azure サポート プラン** - サポート プラン サブスクリプションの請求書は毎月生成されます。 初回の請求書は、購入日 (または最大その 2 日後) に生成されます。 それ以降のサポート プランの請求書は通常、アカウントが作成された月の日付と同じ日に生成されますが、最大で 2 日後となる場合があります。

## <a name="download-your-mosp-azure-subscription-invoice"></a>MOSP Azure サブスクリプションの請求書をダウンロードする

請求書は、MOSP の課金アカウントに属しているサブスクリプションについてのみ生成されます。 [MOSP アカウントへのアクセスを確認してください](../manage/view-all-accounts.md#check-the-type-of-your-account)。 

サブスクリプションの請求書をダウンロードするには、そのサブスクリプションのアカウント管理者ロールが必要です。 所有者、共同作成者、閲覧者のいずれかのロールのユーザーでも、アカウント管理者からアクセス許可が与えられていればその請求書をダウンロードすることができます。 詳細については、「[ユーザーに請求書のダウンロードを許可する](../manage/manage-billing-access.md#opt-in)」を参照してください。

1. Azure portal の [[サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で、自分のサブスクリプションを選択します。
1. [課金] セクションから **[請求書]** を選択します。  
    ![ユーザーがサブスクリプションの請求書オプションを選択する操作を示すスクリーンショット](./media/download-azure-invoice/select-subscription-invoice.png)
1. **[ダウンロード]** を選択して PDF バージョンの請求書をダウンロードした後、請求書セクションの **[ダウンロード]** を選択します。  
    [![請求期間、ダウンロード オプション、各請求期間の料金の合計を示すスクリーンショット](./media/download-azure-invoice/download-invoice-subscription.png)](./media/download-azure-invoice/download-invoice-subscription-zoomed.png#lightbox)
1. [使用量の詳細] セクションの **[ダウンロード]** を選択して、使用量と料金の日単位の内訳をダウンロードすることもできます。 CSV ファイルの準備には数分かかる場合があります。  
    ![請求書と使用状況のダウンロード ページを示すスクリーンショット](./media/download-azure-invoice/usage-and-invoice-subscription.png)

請求書の詳細については、「[Microsoft Azure の課金内容の確認](../understand/review-individual-bill.md)」をご覧ください。 コスト管理に関するヘルプについては、[Azure の課金とコスト管理で想定外の料金を防ぐ](../manage/getting-started.md)方法に関するページを参照してください。

## <a name="download-your-mosp-support-plan-invoice"></a>MOSP サポート プランの請求書をダウンロードする

請求書は、MOSP 課金アカウントに属しているサポート プラン サブスクリプションについてのみ生成されます。 [MOSP アカウントへのアクセスを確認してください](../manage/view-all-accounts.md#check-the-type-of-your-account)。 

サポート プラン サブスクリプションの請求書をダウンロードするには、そのサブスクリプションのアカウント管理者ロールが必要です。

1.  [Azure portal](https://portal.azure.com) にサインインします。
1.  "**コスト管理 + 請求**" を検索します。  
    ![ポータルでコスト管理と請求を検索しているところを示すスクリーンショット](./media/download-azure-invoice/search-cmb.png)
1.  左側で **[請求書]** を選択します。
1.  サポート プラン サブスクリプションを選択し、 **[ダウンロード]** を選択します。  
    [![課金プロファイルの一覧を示すスクリーン ショット](./media/download-azure-invoice/cmb-invoices.png)](./media/download-azure-invoice/cmb-invoices-zoomed-in.png#lightbox)
1.  **[ダウンロード]** を選択して PDF バージョンの請求書をダウンロードします。  
    ![請求期間、ダウンロード オプション、各請求期間の料金の合計を示すスクリーンショット](./media/download-azure-invoice/download-invoice-support-plan.png)

## <a name="allow-others-to-download-an-mosp-subscription-invoice"></a>MOSP サブスクリプションの請求書を他のユーザーがダウンロードできるようにする

PDF バージョンの請求書には、アカウント管理者に関する個人情報が含まれているため、請求書をダウンロードするためのアクセス許可を、そのサブスクリプションのアカウント管理者が他のユーザーに付与する必要があります。 アクセス許可が付与されると、次に示す適切なロールを割り当てられたスタッフやユーザーがサブスクリプションの請求書をダウンロードできるようになります。

- ユーザー
- グループ
- 所有者を含むサービス プリンシパル
- Contributor
- Reader
- ユーザー アクセス管理者
- 請求閲覧者
- 共同管理者
- サービス管理者

請求書をダウンロードするには、次の手順に従います。

1.  サブスクリプションのアカウント管理者として [Azure portal](https://portal.azure.com) にサインインします。
1.  "**コスト管理 + 請求**" を検索します。  
1.  左側で **[請求書]** を選択します。
1.  自分の Azure サブスクリプションを選択し、 **[請求書へのアクセス]** を選択します。  
    [![[請求書へのアクセス] の選択操作を示すスクリーンショット](./media/download-azure-invoice/cmb-select-access-to-invoice.png)](./media/download-azure-invoice/cmb-select-access-to-invoice-zoomed-in.png#lightbox)
1.  **[オン]** を選択し、ページ上部の **[保存]** を選択します。  
    ![[請求書へのアクセス] で [オン] を選択する操作のスクリーンショット](./media/download-azure-invoice/cmb-access-to-invoice.png)

## <a name="get-mosp-subscription-invoice-in-email"></a>MOSP サブスクリプションの請求書をメールで受け取る

サブスクリプションまたはサポート プランの請求書をメールで受信するようオプトインするには、そのサブスクリプションまたはサポート プランのアカウント管理者ロールが必要です。 メールでの請求書を利用できるのは、サブスクリプションとサポート プランだけです。予約や Azure Marketplace の購入では利用できません。 オプトイン後、請求書をメールで受け取ることができる受信者をさらに追加可能です。

1.  [Azure portal](https://portal.azure.com) にサインインします。
1.  "**コスト管理 + 請求**" を検索します。  
1.  左側で **[請求書]** を選択します。
1.  Azure サブスクリプションまたはサポート プラン サブスクリプションを選択し、 **[請求書を電子メールで送信]** を選択します。  
    [![課金プロファイルの一覧を示すスクリーン ショット](./media/download-azure-invoice/cmb-email-invoice.png)](./media/download-azure-invoice/cmb-email-invoice-zoomed-in.png#lightbox)
1. **[オプトイン]** を選択して、条項に同意します。  
    ![手順 2 のオプトイン フローを示すスクリーンショット](./media/download-azure-invoice/invoice-article-step02.png)
1. 請求書は、ご希望の連絡先メール アドレスに送信されます。 メール アドレスは、[課金アカウントの連絡先情報](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/Properties)で更新することができます。  
    ![手順 3 のオプトイン フローを示すスクリーンショット](./media/download-azure-invoice/invoice-article-step03-verify-email.png)

## <a name="share-your-mosp-invoices-with-others-by-email"></a>MOSP の請求書を他のユーザーとメールで共有する

サブスクリプションやサポート プランの請求書を毎月会計チームと共有したい場合や、自分が使用している他のメール アドレス宛てに送信したい場合もあります。

1. [サブスクリプションとサポート プランの請求書をメールで受け取る](#get-mosp-subscription-invoice-in-email)ための手順に従い、 **[受信者の構成]** を選択します。  
    ![ユーザーが [受信者の構成] を選択する操作を示すスクリーンショット](./media/download-azure-invoice/invoice-article-step03.png)
1. メール アドレスを入力し、 **[受信者の追加]** を選択します。 複数のメール アドレスを追加することができます。  
    ![ユーザーが受信者を追加する操作を示すスクリーンショット](./media/download-azure-invoice/invoice-article-step04.png)
1. すべてのメール アドレスを追加したら、画面下部の **[完了]** を選択します。

## <a name="invoices-for-mca-and-mpa-billing-accounts"></a>MCA および MPA の課金アカウントの請求書

MCA の課金アカウントは、組織が Microsoft 担当者を通じて MCA を締結すると作成されます。 一部のリージョンでは、Azure Web サイトから[従量課金制料金のアカウント](https://azure.microsoft.com/offers/ms-azr-0003p/)または [Azure 無料アカウント](https://azure.microsoft.com/offers/ms-azr-0044p/)にサインアップしたお客様には、MCA の課金アカウントが作成される場合があります。 詳細については、[MCA の課金アカウントの概要](../understand/mca-overview.md)に関するページを参照してください。

MPA の課金アカウントは、クラウド ソリューション プロバイダー (CSP) パートナーが新しいコマース エクスペリエンスで顧客を管理する目的で作成します。 パートナーが Azure portal で課金アカウントを管理するためには、[Azure プラン](https://docs.microsoft.com/partner-center/purchase-azure-plan)に少なくとも 1 件の顧客が必要です。 詳細については、[MPA の課金アカウントの概要](../understand/mpa-overview.md)に関するページを参照してください。

月の初めに、アカウントの各課金プロファイルの月次請求書が生成されます。 請求書には、前月からのすべての Azure サブスクリプションとその他の購入に対するそれぞれの料金が含まれています。 たとえば、John が *Azure sub 01* を 3 月 5 日に、*Azure sub 02* を 3 月 10 日に作成したとします。 3 月 28 日には、*Billing profile 01* を使用して *Azure support 01* サブスクリプションを購入しました。 John は、それらの Azure サブスクリプションの料金とサポート プランの料金とを含んだ 1 枚の請求書を 4 月の初めに受け取ることになります。

##  <a name="download-an-mca-or-mpa-billing-profile-invoice"></a>MCA または MPA の課金プロファイルの請求書をダウンロードする

Azure portal から課金プロファイルの請求書をダウンロードするには、課金プロファイルの所有者、共同作成者、閲覧者、請求書管理者のいずれかのロールが必要となります。 課金アカウントの所有者、共同作成者、閲覧者のいずれかのロールを持つユーザーは、そのアカウントのすべての課金プロファイルの請求書をダウンロードすることができます。

1.  [Azure portal](https://portal.azure.com) にサインインします。
1.  "**コスト管理 + 請求**" を検索します。  
1. 左側で **[請求書]** を選択します。
    [![MCA 課金アカウントの請求書ページを示すスクリーンショット](./media/download-azure-invoice/mca-billing-profile-invoices.png)](./media/download-azure-invoice/mca-billing-profile-invoices-zoomed.png#lightbox)
1. 請求書のテーブルから、ダウンロードする請求書を選択します。
1. ページの上部にある **[請求書 PDF のダウンロード]** を選択します。  
    ![請求書の PDF のダウンロードを示すスクリーンショット](./media/download-azure-invoice/mca-billing-profile-download-invoice.png)
1. **[Azure の利用状況をダウンロードします]** を選択して、使用量と見積料金に関する日単位の内訳を含んだ CSV ファイルをダウンロードすることもできます。 CSV ファイルの準備には数分かかる場合があります。

## <a name="get-your-billing-profiles-invoice-in-email"></a>課金プロファイルの請求書をメールで受け取る

課金プロファイルのメール請求書の設定を更新するには、課金プロファイルまたはその課金アカウントに対する所有者ロールまたは投稿者ロールが必要です。 オプトイン後は、課金プロファイルの所有者、共同作成者、閲覧者、または請求書管理者のロールを持つすべてのユーザーは、メールでその請求書を受け取ることになります。 

1.  [Azure portal](https://portal.azure.com) にサインインします。
1.  "**コスト管理 + 請求**" を検索します。  
1.  左側から **[請求書]** を選択し、ページ上部から **[請求書を電子メールで送信]** を選択します。  
    [![MCA 課金アカウントの請求書ページを示すスクリーンショット](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  複数の課金プロファイルがある場合は、いずれかの課金プロファイルを選択し、 **[オプトイン]** を選択します。  
    ![MCA 課金アカウントの請求書ページを示すスクリーンショット](./media/download-azure-invoice/mca-billing-profile-email-invoice.png)
1.  **[Update]\(更新\)** を選択します。

## <a name="give-others-access-to-mca-or-mpa-invoices"></a>MCA または MPA の請求書へのアクセスを他のユーザーに許可する

MCA または MPA の課金プロファイルの請求書管理者ロールを割り当てることにより、請求書の表示、ダウンロード、支払いを行うためのアクセス権を他のユーザーに付与することができます。 請求書をメールで受け取るようにオプトインした場合は、メールでも請求書が送信されます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. "**コスト管理 + 請求**" を検索します。  
1. 左側の **[課金プロファイル]** を選択します。 課金プロファイルの一覧から、請求書管理者ロールを割り当てる課金プロファイルを選択します。  
   ![課金プロファイルの一覧を示すスクリーンショット](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)
1. 左側の **[アクセス制御 (IAM)]** を選択し、ページの上部にある **[追加]** を選択します。  
    ![アクセス制御ページを示すスクリーンショット](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)
1. [ロール] ドロップダウン リストで **[請求書管理者]** を選択します。 アクセス権を付与するユーザーのメール アドレスを入力します。 **[保存]** を選択してロールを割り当てます。  
   ![請求書管理者としてのユーザーの追加を示すスクリーンショット](./media/download-azure-invoice/mca-added-invoice-manager.png)

## <a name="why-you-might-not-see-an-invoice"></a><a name="noinvoice"></a> 請求書が表示されない理由

請求書が表示されない理由はいくつか考えられます。

- Azure のサブスクリプションを開始してから 30 日以上経過していない。 
- Azure による課金は、請求期間の終了から数日後に行われます。 そのため、請求書がまだ生成されていない可能性があります。
- 請求書を表示するアクセス許可がない。
    MCA または MPA の課金アカウントを所有している場合、課金プロファイルの所有者、共同作成者、閲覧者、請求書管理者のいずれかのロール、または請求書を表示する課金アカウントの所有者、共同作成者、閲覧者のいずれかのロールが必要となります。 その他のサブスクリプションでは、アカウント管理者でない場合は請求書が表示されないことがあります。 課金情報へのアクセス権の取得に関する詳細については、[ロールを使用した Azure の課金へのアクセス管理](../manage/manage-billing-access.md)に関するページをご覧ください。
- MOSP の課金アカウントを所有していて、なおかつ Azure 無料アカウントまたは月単位のクレジット額付きのサブスクリプションにサインアップした場合、請求書が表示されるのは、毎月のクレジット額を超えたときのみです。 MCA または MPA の課金アカウントを所有している場合は、常に請求書が表示されます。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

請求書および料金の詳細については、以下を参照してください。

- [Microsoft Azure の利用状況と料金の表示とダウンロード](download-azure-daily-usage.md)
- [Microsoft Azure の課金内容の確認](review-individual-bill.md)
- [Azure 請求書の用語について](understand-invoice.md)

MCA を締結している場合は、次のページを参照してください。

- [課金プロファイルの請求書の料金を理解する](review-customer-agreement-bill.md)
- [課金プロファイルの請求書の用語を理解する](mca-understand-your-invoice.md)
- [課金プロファイルの Azure の使用量と料金のファイルを理解する](mca-understand-your-usage.md)


