---
title: "課金内容の確認 | Microsoft Azure"
description: "Azure サブスクリプションの使用状況と課金内容を確認し、理解する方法について説明します。"
services: 
documentationcenter: 
author: genlin
manager: stevenpo
editor: 
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2016
ms.author: erihur;genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8768ccd5a1d04566c5f2e1f26ab328cea570cc61


---
# <a name="understand-your-bill-for-microsoft-azure"></a>Microsoft Azure の課金内容の確認
> [!NOTE]
> この記事についてさらにヘルプが必要な場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。
> 
> 

Microsoft Azure サブスクリプションの課金額は、料金プランによって異なります。 Visual Studio Enterprise (MPN) サブスクライバーなどの一部の料金プランには、お客様のニーズに合わせて Azure サービスで利用できる月々のクレジットが含まれています。

前の請求期間の潜在的使用時間のうち、最大 24 時間が現在の請求期間として報告される場合があることにご注意ください。

消費量と料金プランの詳細については、 [Microsoft Azure の購入オプション](https://azure.microsoft.com/pricing/purchase-options/)に関するページを参照してください。

<!-- The below links cover a complete list of all Microsoft Azure services.

<!-- - [Service Details list (csv1)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx)
<!-- - [Service Details list (csv2)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx)

<!-- *NOTE: The **csv1** link refers to the column header names for csv version 1 and **csv2** link refers to the new column header names for csv version 2.  These files are updated monthly.*-->

### <a name="view-or-download-a-bill-for-microsoft-azure"></a>Microsoft Azure の課金内容の表示またはダウンロード:
1. Microsoft アカウントまたは組織 ID を使用して [アカウント センター](https://account.windowsazure.com/subscriptions) にサインインします。
2. 詳細と使用状況を表示するサブスクリプションをクリックします。
3.  **[請求履歴]**
   
    ![概要 - 請求履歴 1](./media/billing-understand-your-bill/ContentViewaBillforMA1.png)
4. **[請求履歴]** セクションには、以前の請求期間と現行の未請求期間に関する明細が表示されます。 現在の期間の明細は、見積もりを作成した時点での料金の見積もりです。 この情報は毎日更新されますが、その日までのすべての累計使用量が含まれていない可能性があります。 その月に実際に支払う料金は、この見積もりとは異なる場合があります。  
   
    ![概要 - 請求履歴 2](./media/billing-understand-your-bill/ContentViewaBillforMA2.png)
5. 見積もりを作成した時点での料金の見積もりを表示するには、 **[現在の明細書の表示]** をクリックします。 この情報は毎日更新されますが、その日までのすべての累計使用量が含まれていない可能性があります。 その月に実際に支払う料金は、この見積もりとは異なる場合があります。
   
    ![概要 - 請求履歴 3](./media/billing-understand-your-bill/ContentViewaBillforMA3.png)
   
    ![概要 - 請求履歴 4](./media/billing-understand-your-bill/ContentViewaBillforMA4.png)
6. 以前の請求書のコピーを表示するには、 **[請求書のダウンロード]** をクリックします。
   
    ![概要 - 請求履歴 5](./media/billing-understand-your-bill/ContentViewaBillforMA5.png)

> [!NOTE]
> 銀行ではさまざまな費用に通貨換算率を適用しているため、米国以外のお客様向けの課金明細書に記載されている料金は、見積もりとしての使用を目的として提供されています。
> 
> 

Microsoft Azure で使用できる 2 つのプランのステートメント例を次に示します。

| プランの種類 | Description | ダウンロード |
|:--- |:--- |:--- |
| 従量課金制 |毎月、未払い分を支払います |[サンプル ファイル](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_ccinvoice_Sample.pdf) |
| コミットメント プラン |前払いのコミットメントから利用分が差し引かれます |[サンプル ファイル](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_invoice_Sample.pdf) |

## <a name="account-information"></a>アカウント情報
アカウント情報のセクションでは、使用状況とプロファイルの関連情報を確認できます。

![ヘッダー](./media/billing-understand-your-bill/Header.png)

| 用語 | Description |
| --- | --- |
| 請求書番号 |追跡を目的とする一意の請求書の識別子 |
| 請求サイクル |使用された期間 |
| 請求日 |請求書が作成された日付 |
| 支払い方法 |アカウントで使用される支払いの種類 (請求書またはクレジット カード) |
| 請求先 |Microsoft Azure の支払いをする住所 |
| サブスクリプション プラン |購入したサブスクリプション プランの種類 (従量課金制、BizSpark Plus、Azure Pass など) |
| アカウント所有者メール アドレス |Microsoft Azure アカウントが登録されたアカウントのメール アドレス |

## <a name="understand-the-invoice-summary"></a>請求書の要約について
請求書の **[請求書の要約]** セクションでは、前回の請求以降の明細と現在の使用料金が要約されています。

![[請求書の要約]](./media/billing-understand-your-bill/InvoiceSummary.png)

請求書の繰越残高、入金額、および未払い残高のセクションでは、前回の請求書以降の明細が要約されています。

| 用語 | Description |
| --- | --- |
| 繰越残高 |前回の請求書の合計請求額 |
| 入金額 |前回の請求に対する合計支払い金額 |
| (前の請求サイクルからの) 未払い残高 |前回の請求以降にアカウントに適用された請求書の調整 (クレジットまたは残高) |

## <a name="understand-the-current-charges"></a>現在の料金について
請求書の「現在の料金セクション」には、月々の料金の詳細が表示されます。 各リンクは、次のサブセクションに分かれています。

| 用語 | Description |
| --- | --- |
| 利用料金 |利用料金は、サブスクリプションの月額料金の合計です。 前月の利用量の未払い分が課金されます。 |
| 割引 |現在の請求書に適用されるサービス割引は、対象の明細項目に反映されます。 |
| 調整 |その他の調整とは、現在の請求書に適用されるその他のクレジット、または未処理料金です。 たとえば、Visual Studio Enterprise with MSDN のプランをご利用の場合、この明細項目には毎月のクレジット額が表示されます。 サブスクリプションを取り消した場合、その月の使用料金については、その月の課金開始日から取り消し日までに含まれるクレジット額を超過した分の金額が表示されます。 |

## <a name="footer-information"></a>フッター情報
![フッター](./media/billing-understand-your-bill/footerinformation.png)

## <a name="understand-the-additional-information"></a>追加情報について
追加情報のページでは、請求書について理解するためのその他のリソースへの参照、使用状況を表示するためのリンク、請求書に関するその他の関連情報を記載しています。

![追加情報](./media/billing-understand-your-bill/AdditionalInformation.png)

### <a name="detailed-usage"></a>詳細な使用状況
**[詳細な使用状況]** の説明のリンクから、アカウント センターに移動します。ここで、このサブスクリプションの詳細な使用状況を確認できます。  現在、ダウンロードできるバージョンは 2 種類です。**.csv version 1** には、古い命名規則と使用状況のフィールドが含まれ、**.csv version 2** には、各カテゴリのユーザーにわかりやすい名前と、Microsoft Azure で使用しているサービス内容を理解できるその他のフィールドが含まれています。 .csv version 1 には Azure Resource Manager の詳細がないことに注意してください。 Azure Resource Manager の情報は .csv version 2 に含まれています。

### <a name="additional-information-and-useful-resources"></a>追加情報と役立つリソース
このセクションには、コンピューティング インスタンスのサイズに関する簡単な質問、SQL DB 料金へのリンク、および詳細な質問に答えるための役立つリンクがあります。

| 用語 | Description |
| --- | --- |
| 販売先 |ここには、アカウントのプロファイル住所があらかじめ入力されています。 |
| 支払い指示 |このセクションは、小切手の送信先、電信送金、または支払い方法が請求書の場合のオーバーナイト チェックに関する支払いの指示のセクションです。 |

## <a name="understand-detailed-usage-charges"></a>詳細な使用料金について
ユーザーが Azure の使用状況を管理しやすくなるように、Azure サービスの使用状況とコストについて報告する使用状況ファイルのダウンロード機能を強化しています。  ダウンロード リンクには、2 つのバージョンの使用状況ファイルがあります。

* **Version 1** では既存の形式を使用します。
* **Version 2** には、[日ごとの使用量] セクションの追加情報と更新された列名が含まれています。  

使用料金は、サブスクリプションに対する合計 **月額** 料金にクレジットまたは割引を適用した金額になります。 前月の利用量の未払い分が課金されます。  ファイルの最上部セクションには、前月の請求サイクル中に課金されたサービスに関する詳細が表示されます。  次の表に、各バージョンの .csv ファイルの列名を示します。

| Version 1 | Version 2 | Description |
|:--- |:--- | --- |
| 請求期間 |請求期間 |リソースが消費された請求期間。 |
| 名前 |測定カテゴリ |この使用量の対象となる最上位レベルのサービスを示します。 |
| 型 |測定サブカテゴリ |この列で、Azure サービスをさらに種類で分類することがあります。種類が違うと料金も異なる場合があります。 |
| リソース |測定名 |消費しているリソースの測定単位を示します。 |
| リージョン |測定リージョン |データセンターの場所に基づいて価格が設定されるサービスについて、データセンターの場所を示します。 |
| SKU |SKU |各 Azure リソースの一意のシステム ID を示します。 |
| 単位 |単位 |サービスが課金される単位を特定します。 たとえば、GB、時間、10,000 単位などです。 |
| 消費量 |消費量 |請求期間中に使用されたリソースの量が表示されます。 |
| あり |含まれる量 |現在の請求期間内に無料で含まれるリソースの量が表示されます。 |
| 課金対象 |超過量 |消費量がベース量を超えた場合、この列にはその差異が表示されます。 この差異について課金されます。 プランに一定量が含まれない従量課金制では、この合計は消費量と同じになります。 |
| コミットメント以内 |コミットメント以内 |6 か月プランまたは 12 か月プランに関連付けられたコミットメント額から差し引かれるリソース料金が表示されます。 コミットメント額から差し引かれるリソース料金は、発生順に表示されます。 |
| 通貨 |通貨 |現在の請求期間で使用する通貨を示します。 |
| 超過料金 |超過料金 |6 か月プランまたは 12 か月プランに関連付けられたコミットメント額を超えたリソース料金が表示されます。 |
| コミットメント レート |コミットメント レート |6 か月プランまたは 12 か月プランに関連付けられたコミットメント額の合計に基づいてコミットメント レートが表示されます。 |
| 料金 |料金 |単価は、課金対象項目ごとに請求される価格を示します。 |
| 値 |値 |[課金対象] 列に [単価] 列を掛けた料金を示します。 消費量がベース量を超えない場合は、この列に料金は表示されません。 |

## <a name="analyze-daily-usage-data"></a>日ごとの使用量データの分析
使用量に応じて、日ごとの使用量データが数千行存在する場合があります。 このデータを分析するには、 **[使用量のダウンロード]** をクリックし、コンマ区切り値 (.csv) バージョンを選択して、該当する請求期間の日ごとの使用量データを確認します。  以下の各バージョンのサンプル .csv ファイルをダウンロードすることもできます。

| 名前 | ダウンロード |
|:---:|:---:|
|   Detailed Usage .csv Version 1 |[サンプル ファイル](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx) |
|   Detailed Usage .csv Version 2 |[サンプル ファイル](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx) |

![csv2screenshot](./media/billing-understand-your-bill/csv2screenshot.png)

この .csv ファイルには、現在の請求期間内の各リソースの消費量が詳細に記載されています。

![csv のスナップショット](./media/billing-understand-your-bill/csvsnapshotportal.png)

以下の列は、請求期間開始時の料金に影響する詳細を示します。

| Version 1 | Version 2 | Description |
|:--- |:--- | --- |
| 使用日 |使用日 |リソースが使用された日付。 |
| 名前 |測定カテゴリ |この使用量の対象となる最上位レベルのサービスを示します。 |
| リソース GUID |測定 ID |課金測定の識別子です。  課金データの料金計算に使用される識別子です。 |
| 型 |測定サブカテゴリ |この列で、Azure サービスをさらに種類で分類することがあります。種類が違うと料金も異なる場合があります。 |
| リソース |測定名 |消費しているリソースの測定単位を示します。 |
| リージョン |測定リージョン |データセンターの場所に基づいて価格が設定されるサービスについて、データセンターの場所を示します。 |
| 単位 |単位 |サービスが課金される単位を特定します。 たとえば、GB、時間、10,000 単位などです。 |
| 消費量 |消費量 |その日に消費されたリソースの量が表示されます。 |
| サブ リージョン |リソースの場所 |リソースが実行されているデータ センターを特定します。 |
| サービス |使用サービス |この列を使用して、[名前] 列だけでは詳しく識別できない、個々の Azure Platform サービスを追跡します。 この [サービス] 列には、使用量の対象となるサービスが表示されます。 |
| 該当なし |リソース グループ |***新しい列の追加。***  デプロイされたリソースが実行されるリソース グループ。 「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください |
| コンポーネント |インスタンス ID |実行中のリソースの識別子です。 識別子には、リソースの作成時に指定した名前が含まれています。 |
| 該当なし |タグ |***新しい列の追加。***  Azure の新しいリソースの種類では、リソースにタグを付けることができます。 「 [Organize your Azure resources with tags (タグを使用した Azure リソースの整理)](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) |
| 追加情報 |追加情報 |サービスに関連する追加のメタデータ。 |
| サービス情報 1 |サービス情報 1 |この列は、サブスクリプションでサービスが属しているプロジェクトの名前を特定します。 |
| サービス情報 2 |サービス情報 2 |これは、サービス固有の省略可能なメタデータをキャプチャする、以前から使用されているフィールドです。 |

いくつかの新しいフィールドと、csv Version 2 への名前の変更に加え、以下のフィールドのデータに対して、標準化された書式設定があります。

* **インスタンス ID**: [インスタンス ID] フィールドは、プロビジョニングされたサービスのユーザー指定の識別子を示します。 現在、インスタンス ID を表現する書式は 2 つあり、リソース名、または完全修飾リソース ID です。 Microsoft Azure サービスは、インスタンス ID を標準化された完全修飾リソース ID 形式 ***(/subscriptions/<subscription id>/resourcegroups/<resourcegroupname>/providers/<providername>/<resourcename>)*** で表すように移行しています。 サービスが新しい書式に移行すると、[インスタンス ID] データ フィールドはリソース名からリソース ID に変わります。 リソース ID は、 [Azure Resource Manager API](https://msdn.microsoft.com/library/azure/dn790567.aspx) がサブスクリプション内のリソースを識別するために使用する形式です。

![instanceid](./media/billing-understand-your-bill/instanceid.png)

* **追加情報**: 使用状況の .csv の [追加情報] 列は、サービス固有のメタデータを示します。 たとえば、VM のイメージの種類です。 現在、サービスからは、[追加情報]、[サービス情報 1]、[サービス情報 2] フィールドという複数の列にサービス固有のメタデータが出力されます。 Microsoft Azure サービスは、[追加情報] 列のみについて、サービス固有のメタデータの出力を標準化します。  標準化された形式のスナップショットを次に示します。

![additionalinfo_csv2](./media/billing-understand-your-bill/AdditionaInfo_csv2.png)

* **タグ**: この列には、ユーザーが指定したリソース タグが含まれます。 タグは、課金記録のグループ化に使用できます。 たとえば、タグを使用して、サービスを使用する部門ごとにコストを配分することができます。 タグの使用方法については、「[タグを使用した Azure リソースの整理](../resource-group-using-tags.md)」を参照してください。 タグの生成をサポートするサービスは、次のとおりです。  
  
  * Virtual Machines
  * 記憶域
  * [Azure リソース マネージャー API を使用してプロビジョニングされたネットワーク サービス。](https://msdn.microsoft.com/library/azure/dn790567.aspx)

![タグ](./media/billing-understand-your-bill/tags.png)

## <a name="next-steps"></a>次のステップ
* [課金アラートのセットアップ](../billing-set-up-alerts.md)
* [支払い方法の管理](../billing-how-to-change-credit-card.md)
* [Azure Marketplace の料金について](../billing-understand-your-azure-marketplace-charges.md)
* [Azure の課金とサブスクリプションに関する FAQ ](../billing-subscription-faq.md)

> [!NOTE]
> まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。
> 
> 

<!--
OLD MSDN Articles
- [What do I do if my Azure subscription become disabled?](https://msdn.microsoft.com/library/azure/dn736049.aspx)
- [Edit payment information for an existing credit card](https://msdn.microsoft.com/library/azure/dn736053.aspx)
- [Add a new credit card to use as a payment method](https://msdn.microsoft.com/library/azure/dn736057.aspx)
- [Change the credit card on your Microsoft Azure account](https://msdn.microsoft.com/library/azure/dn736050.aspx)
- [Manage your payment method](https://msdn.microsoft.com/library/azure/dn736054.aspx)
-->



<!--Image references-->



<!--HONumber=Nov16_HO3-->


