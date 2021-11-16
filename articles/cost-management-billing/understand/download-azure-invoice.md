---
title: Azure の請求書を表示してダウンロードする
description: Azure の請求書の表示とダウンロードの方法を説明します。 請求書は、Azure portal からダウンロードするか、メールで受信することができます。
keywords: 請求書,請求書のダウンロード,Azure の請求書,Azure の使用状況
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 05/17/2021
ms.author: banders
ms.openlocfilehash: c3e37fa2d4a24344c2d417bd54544bd786fc8e75
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054259"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Microsoft Azure の請求書の表示とダウンロード

請求書は、[Azure portal](https://portal.azure.com/) からダウンロードするか、メールで送信することができます。 マイクロソフト エンタープライズ契約 (EA) を締結している Azure ユーザー (EA のお客様) が、その組織の請求書をダウンロードすることはできません。 請求書は、登録のために請求書が届くよう設定されている担当者に送信されます。

## <a name="when-invoices-are-generated"></a>いつ請求書が生成されるか

請求書は、課金アカウントの種類に基づいて生成されます。 Microsoft Online Services Program (MOSP)、Microsoft 顧客契約 (MCA)、Microsoft Partner Agreement (MPA) の各課金アカウントの請求書が作成されます。 マイクロソフト エンタープライズ契約 (EA) 課金アカウントの請求書も生成されます。 ただし EA 課金アカウントの請求書は、Azure portal に表示されません。

課金アカウントの詳細を確認し、ご自分の課金アカウントの種類を特定するには、[Azure portal での課金アカウントの表示](../manage/view-all-accounts.md)に関するページを参照してください。

### <a name="invoice-status"></a>請求書の状態

Azure portal で請求書の状態を確認すると、次のいずれかの状態記号が各請求書に表示されます。

|  状態記号 | 説明  |
|---|---|
| ![未払いの状態記号](./media/download-azure-invoice/due.svg) | 請求書は生成されたものの、まだ支払いが完了していない場合、"*未払い*" が表示されます。 |
| ![期限切れの状態記号](./media/download-azure-invoice/past-due.svg)  | 指定された支払い方法に課金を試みたものの、支払いが拒否された場合、"*期限切れ*" が表示されます。 |
| ![支払い済みの状態記号](./media/download-azure-invoice/paid.svg)  | 指定された支払い方法に対する課金が正常に完了すると、"*支払い済み*" 状態が表示されます。 |

請求書は、作成された時点では "*未払い*" 状態で Azure portal に表示されます。 未払い状態は正常であり、想定されています。  

請求書の支払いが行われなかった場合、その状態は "*期限切れ*" として表示されます。 請求書の支払いが実行されなかった場合、期限切れのサブスクリプションは無効になります。

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
1. ダウンロードする請求書を選択し、 **[Download invoices]\(請求書のダウンロード\)** をクリックします。  
    ![MOSP 請求書のダウンロード オプションのスクリーンショット](./media/download-azure-invoice/downloadinvoice-subscription.png)
1. ダウンロード アイコンをクリックし、使用量の詳細セクションの下にある **[Prepare Azure usage file]\(Azure 使用量ファイルの準備\)** ボタンをクリックして、消費量と料金の日単位の内訳をダウンロードすることもできます。 CSV ファイルの準備には数分かかる場合があります。  
    ![請求書と使用状況のダウンロード ページを示すスクリーンショット](./media/download-azure-invoice/usage-and-invoice-subscription.png)

請求書の詳細については、「[Microsoft Azure の課金内容の確認](../understand/review-individual-bill.md)」をご覧ください。 通常とは異なるコストを特定するためのヘルプについては、[予期しない料金の分析](analyze-unexpected-charges.md)に関するページを参照してください。

## <a name="download-your-mosp-support-plan-invoice"></a>MOSP サポート プランの請求書をダウンロードする

請求書は、MOSP 課金アカウントに属しているサポート プラン サブスクリプションについてのみ生成されます。 [MOSP アカウントへのアクセスを確認してください](../manage/view-all-accounts.md#check-the-type-of-your-account)。

サポート プラン サブスクリプションの請求書をダウンロードするには、そのサブスクリプションのアカウント管理者ロールが必要です。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. "**コスト管理 + 請求**" を検索します。  
    ![ポータルでコスト管理と請求を検索しているところを示すスクリーンショット](./media/download-azure-invoice/search-cmb.png)
1. 左側で **[請求書]** を選択します。
1. サポート プランのサブスクリプションを選択します。  
    [![MOSP サポート プランの請求書の課金プロファイル一覧を示すスクリーンショット](./media/download-azure-invoice/cmb-invoices.png)](./media/download-azure-invoice/cmb-invoices-zoomed-in.png#lightbox)
1. ダウンロードする請求書を選択し、 **[Download invoices]\(請求書のダウンロード\)** をクリックします。  
    ![MOSP サポート プランの請求書のダウンロード オプションを示すスクリーンショット](./media/download-azure-invoice/download-invoice-support-plan.png)

## <a name="allow-others-to-download-your-subscription-invoice"></a>サブスクリプションの請求書を他のユーザーがダウンロードできるようにする

請求書をダウンロードするには、次の手順に従います。

1.  サブスクリプションのアカウント管理者として [Azure portal](https://portal.azure.com) にサインインします。

2.  "**コスト管理 + 請求**" を検索します。

    ![ポータルでのコストの管理と請求の検索を示すスクリーンショット](./media/download-azure-invoice/search-cmb.png)

3.  左側で **[請求書]** を選択します。

4.  自分の Azure サブスクリプションを選択し、 **[請求書のダウンロードを他のユーザーに許可する]** をクリックします。

    [![[請求書へのアクセス] の選択操作を示すスクリーンショット](./media/download-azure-invoice/cmb-select-access-to-invoice.png)](./media/download-azure-invoice/cmb-select-access-to-invoice-zoomed-in.png#lightbox)

5.  **[オン]** を選択し、ページ上部の **[保存]** を選択します。  
    ![[請求書へのアクセス] で [オン] を選択する操作のスクリーンショット](./media/download-azure-invoice/cmb-access-to-invoice.png)
    
> [!NOTE]
> Microsoft は、機密情報や個人情報を一切第三者と共有しないことをお勧めします。 この推奨事項は、コストを最適化する目的で Azure の請求書を第三者と共有することにも当てはまります。 詳細については、次のトピックを参照してください。 https://azure.microsoft.com/support/legal/ および https://www.microsoft.com/trust-center

## <a name="get-mosp-subscription-invoice-in-email"></a>MOSP サブスクリプションの請求書をメールで受け取る

サブスクリプションまたはサポート プランの請求書をメールで受信するようオプトインするには、そのサブスクリプションまたはサポート プランのアカウント管理者ロールが必要です。 オプトイン後、請求書をメールで受け取ることができる受信者をさらに追加可能です。

1.  [Azure portal](https://portal.azure.com) にサインインします。
2.  "**コスト管理 + 請求**" を検索します。  
3.  左側で **[請求書]** を選択します。
4.  Azure サブスクリプションまたはサポート プラン サブスクリプションを選択し、 **[請求書を電子メールで受信する]** を選択します。  
    [![[請求書を電子メールで受信する] オプションを示すスクリーンショット](./media/download-azure-invoice/cmb-email-invoice.png)](./media/download-azure-invoice/cmb-email-invoice-zoomed-in.png#lightbox)
5. **[請求書を電子メールで送信]** をクリックして、条項に同意します。  
    ![手順 2 のオプトイン フローを示すスクリーンショット](./media/download-azure-invoice/invoicearticlestep02.png)
6. 請求書は、優先する連絡先メールに送信されます。 メールを更新するには、 **[プロファイルの更新]** を選択します。  
    ![手順 3 のオプトイン フローを示すスクリーンショット](./media/download-azure-invoice/invoicearticlestep03-verifyemail.png)

## <a name="share-subscription-and-support-plan-invoice"></a>サブスクリプションとサポート プランの請求書を共有する

サブスクリプションやサポート プランの請求書を毎月会計チームと共有したい場合や、自分が使用している他のメール アドレス宛てに送信したい場合もあります。

1. [サブスクリプションとサポート プランの請求書をメールで受け取る](#get-mosp-subscription-invoice-in-email)ための手順に従い、 **[受信者の構成]** を選択します。  
    [![ユーザーが [受信者の構成] を選択する操作を示すスクリーンショット](./media/download-azure-invoice/invoice-article-step03.png)](./media/download-azure-invoice/invoice-article-step03-zoomed.png#lightbox)
1. メール アドレスを入力し、 **[受信者の追加]** を選択します。 複数のメール アドレスを追加することができます。  
    ![ユーザーが受信者を追加する操作を示すスクリーンショット](./media/download-azure-invoice/invoice-article-step04.png)
1. すべてのメール アドレスを追加したら、画面下部の **[完了]** を選択します。

## <a name="invoices-for-mca-and-mpa-billing-accounts"></a>MCA および MPA の課金アカウントの請求書

MCA の課金アカウントは、組織が Microsoft 担当者を通じて MCA を締結すると作成されます。 一部のリージョンでは、Azure Web サイトから[従量課金制料金のアカウント](https://azure.microsoft.com/offers/ms-azr-0003p/)または [Azure 無料アカウント](https://azure.microsoft.com/offers/ms-azr-0044p/)にサインアップしたお客様には、MCA の課金アカウントが作成される場合があります。 詳細については、[MCA の課金アカウントの概要](../understand/mca-overview.md)に関するページを参照してください。

MPA の課金アカウントは、クラウド ソリューション プロバイダー (CSP) パートナーが新しいコマース エクスペリエンスで顧客を管理する目的で作成します。 パートナーが Azure portal で課金アカウントを管理するためには、[Azure プラン](/partner-center/purchase-azure-plan)に少なくとも 1 件の顧客が必要です。 詳細については、[MPA の課金アカウントの概要](../understand/mpa-overview.md)に関するページを参照してください。

月の初めに、アカウントの各課金プロファイルの月次請求書が生成されます。 請求書には、前月からのすべての Azure サブスクリプションとその他の購入に対するそれぞれの料金が含まれています。 たとえば、John が *Azure sub 01* を 3 月 5 日に、*Azure sub 02* を 3 月 10 日に作成したとします。 3 月 28 日には、*Billing profile 01* を使用して *Azure support 01* サブスクリプションを購入しました。 John は、それらの Azure サブスクリプションの料金とサポート プランの料金とを含んだ 1 枚の請求書を 4 月の初めに受け取ることになります。

##  <a name="download-an-mca-or-mpa-billing-profile-invoice"></a>MCA または MPA の課金プロファイルの請求書をダウンロードする

Azure portal から課金プロファイルの請求書をダウンロードするには、課金プロファイルの所有者、共同作成者、閲覧者、請求書管理者のいずれかのロールが必要となります。 課金アカウントの所有者、共同作成者、閲覧者のいずれかのロールを持つユーザーは、そのアカウントのすべての課金プロファイルの請求書をダウンロードすることができます。

1.  [Azure portal](https://portal.azure.com) にサインインします。

2.  "**コスト管理 + 請求**" を検索します。

    ![ポータルでのコストの管理と請求の検索を示すスクリーンショット](./media/download-azure-invoice/search-cmb.png)

3. 左側で **[請求書]** を選択します。

    [![MCA 課金アカウントの請求書ページを示すスクリーンショット](./media/download-azure-invoice/mca-billingprofile-invoices.png)](./media/download-azure-invoice/mca-billingprofile-invoices-zoomed-in.png#lightbox)

4. 請求書のテーブルから、ダウンロードする請求書を選択します。

5. ページの上部にある **[請求書 PDF のダウンロード]** ボタンをクリックします。

    [![請求書の PDF のダウンロードを示すスクリーンショット](./media/download-azure-invoice/mca-billingprofile-download-invoice.png)](./media/download-azure-invoice/mca-billingprofile-download-invoice-zoomed-in.png#lightbox)

6. また、 **[Azure の利用状況をダウンロードします]** をクリックして、消費量と見積料金の日単位の内訳をダウンロードすることもできます。 csv ファイルの準備には数分かかる場合があります。

## <a name="get-your-billing-profiles-invoice-in-email"></a>課金プロファイルの請求書をメールで受け取る

課金プロファイルのメール請求書の設定を更新するには、課金プロファイルまたはその課金アカウントに対する所有者ロールまたは投稿者ロールが必要です。 オプトイン後は、課金プロファイルの所有者、共同作成者、閲覧者、または請求書管理者のロールを持つすべてのユーザーは、メールでその請求書を受け取ることになります。 

1.  [Azure portal](https://portal.azure.com) にサインインします。
1.  "**コスト管理 + 請求**" を検索します。  
1.  左側から **[請求書]** を選択し、ページ上部から **[請求書のメール送信設定]** を選択します。  
    [![請求書の [請求書を電子メールで送信] オプションを示すスクリーンショット](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  複数の課金プロファイルがある場合は、いずれかの課金プロファイルを選択し、 **[はい]** を選択します。  
    [![オプトイン オプションを示すスクリーンショット](./media/download-azure-invoice/mca-billing-profile-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  **[保存]** を選択します。

MCA または MPA の課金プロファイルの請求書管理者ロールを割り当てることにより、請求書の表示、ダウンロード、支払いを行うためのアクセス権を他のユーザーに付与することができます。 請求書をメールで受け取るようにオプトインした場合は、メールでも請求書が送信されます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. "**コスト管理 + 請求**" を検索します。  
1. 左側の **[課金プロファイル]** を選択します。 課金プロファイルの一覧から、請求書管理者ロールを割り当てる課金プロファイルを選択します。  
   ![課金プロファイルの選択画面である課金プロファイル一覧を示すスクリーンショット](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)
1. 左側の **[アクセス制御 (IAM)]** を選択し、ページの上部にある **[追加]** を選択します。  
    ![アクセス制御ページを示すスクリーンショット](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)
1. [ロール] ドロップダウン リストで **[請求書管理者]** を選択します。 アクセス権を付与するユーザーのメール アドレスを入力します。 **[保存]** を選択してロールを割り当てます。  
    [![請求書管理者としてのユーザーの追加を示すスクリーンショット](./media/download-azure-invoice/mca-added-invoice-manager.png)](./media/download-azure-invoice/mca-added-invoice-manager.png#lightbox)
   

## <a name="share-your-billing-profiles-invoice"></a>課金プロファイルの請求書を共有する

請求書を毎月会計チームと共有したり、自分が使用している他のメール アドレス宛てにそれらを送信したりしたい場合に、会計チームや他のメールに自分の課金プロファイルへのアクセス許可を与えずにそれを行う方法があります。

1.  [Azure portal](https://portal.azure.com) にサインインします。
1.  "**コスト管理 + 請求**" を検索します。  
1.  左側から **[請求書]** を選択し、ページ上部から **[請求書のメール送信設定]** を選択します。  
    [![請求書の [請求書を電子メールで送信] オプションを示すスクリーンショット](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  複数の課金プロファイルがある場合は、いずれかの課金プロファイルを選択します。
1.  [その他の受信者] セクションで、請求書の宛先となるメール アドレスを追加します。
    [![請求書メールの受信者を追加指定する画面のスクリーンショット](./media/download-azure-invoice/mca-billing-profile-add-invoice-recipients.png)](./media/download-azure-invoice/mca-billing-profile-add-invoice-recipients-zoomed.png#lightbox)
1.  **[保存]** を選択します。

##  <a name="why-you-might-not-see-an-invoice"></a>請求書が表示されない理由

<a name="noinvoice"></a>

請求書が表示されない理由はいくつか考えられます。

- 請求書の準備がまだできていない。
    
    - Azure のサブスクリプションを開始してから 30 日以上経過していない。 

    - Azure による課金は、請求期間の終了から数日後に行われます。 そのため、請求書がまだ生成されていない可能性があります。

- 請求書を表示するアクセス許可がない。 
    
    - MCA または MPA の課金アカウントを所有している場合、課金プロファイルの所有者、共同作成者、閲覧者、請求書管理者のいずれかのロール、または請求書を表示する課金アカウントの所有者、共同作成者、閲覧者のいずれかのロールが必要となります。 
    
    - その他の課金アカウントでは、アカウント管理者でない場合は請求書が表示されないことがあります。

- ご利用のアカウントが請求書をサポートしていない。

    - Microsoft Online Services Program (MOSP) の課金アカウントを所有していて、なおかつ Azure 無料アカウントまたは月単位のクレジット額付きのサブスクリプションにサインアップした場合、請求書が表示されるのは、毎月のクレジット額を超えたときのみです。

    - Microsoft 顧客契約 (MCA) または Microsoft Partner Agreement (MPA) の課金アカウントをご利用の場合は、常に請求書が表示されます。

- 他のいずれかのアカウントを使用して請求書にアクセスする必要がある。

    - 一般に、この状況は、請求書をポータルで確認するよう求めるメールのリンクをクリックしたときに発生します。 そのリンクをクリックすると、"`We can't display your invoices. Please try again`" というエラー メッセージが表示されます。 請求書を表示するためのアクセス許可があるメール アドレスでサインインしていることを確認してください。

- 別の ID を使用して請求書にアクセスする必要がある。 

    - お客様によっては、同じメール アドレスで 2 つの ID (職場アカウントと Microsoft アカウント) を所有している場合があります。 一般に、請求書を表示するアクセス許可があるのは、そのどちらか一方の ID のみです。 アクセス許可がない ID でサインインした場合、請求書は表示されません。 正しい ID を使用してサインインしていることを確認してください。

- 間違った Azure Active Directory (Azure AD) テナントにサインインしている。 

    - 課金アカウントは Azure AD テナントに関連付けられます。 間違ったテナントにサインインした場合、ご利用の課金アカウントのサブスクリプションの請求書は表示されません。 正しい Azure AD テナントにサインインしていることを確認してください。 正しいテナントにサインインしていない場合は、Azure portal で次の手順に従ってテナントを切り替えてください。

        1. ページの右上から自分のメールを選択します。

        2. **[ディレクトリの切り替え]** を選択します。

           ![ポータルで [ディレクトリの切り替え] を選択する操作を示すスクリーンショット](./media/download-azure-invoice/select-switch-directory.png)

        3. **[すべてのディレクトリ]** セクションからディレクトリの **[切り替え]** を選択します。

           ![ポータルでのディレクトリの選択操作を示すスクリーンショット](./media/download-azure-invoice/select-directory.png)

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