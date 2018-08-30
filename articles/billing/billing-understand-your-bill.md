---
title: Azure の課金内容を確認する | Microsoft Docs
description: Azure サブスクリプションの使用状況と課金内容を確認して理解する方法について説明します
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2018
ms.author: tonguyen
ms.openlocfilehash: c782cadadb0250e6c3ca4912dbf8f81e19cb88c5
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "42919056"
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Microsoft Azure の課金内容の確認
Azure の課金内容を確認するには、詳細な毎日の使用状況ファイルおよび Azure ポータルのコスト管理レポートと、請求書を比較します。

>[!NOTE]
>この記事は、Enterprise Agreement (EA) のお客様には適用されません。 EA 顧客の場合は、[エンタープライズ ポータルで請求書ドキュメントを見つけることができます](https://ea.azure.com/helpdocs/understandingYourInvoice)。  

請求書の PDF および詳細な毎日の使用状況ファイルの CSV ダウンロードのコピーを取得するには、[Azure 課金の請求書および毎日の使用状況データの取得](billing-download-azure-invoice-daily-usage-date.md)に関する記事をご覧ください。 

請求書および詳細な毎日の使用状況ファイルの詳しい用語と説明については、[Microsoft Azure 請求書の用語の確認](billing-understand-your-invoice.md)に関する記事と「[Microsoft Azure の詳細な利用料金の用語を理解する](billing-understand-your-usage.md)」をご覧ください。 

コスト管理レポートの詳細については、[Azure ポータルのコスト管理](https://docs.microsoft.com/azure/billing/billing-getting-started)に関する記事をご覧ください。

> [!div class="nextstepaction"]
> [Azure の課金に関するドキュメントの改善にご協力ください](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="charges"></a>請求書の料金が正しいことを確認する方法

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

請求書上に詳細を知りたい料金がある場合は、次の 2 つの選択肢があります。

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>オプション 1: 請求書を確認し、使用状況およびコストを使用状況の詳細の CSV ファイルと比較する

使用状況の詳細の CSV ファイルは、請求期間と毎日の使用状況ごとの料金を示します。 使用状況の詳細の CSV ファイルを取得するには、[Azure 課金の請求書および毎日の使用状況データの取得](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date)に関する記事をご覧ください。

使用料金は、メーター レベルで表示されます。 以下の用語は、請求書と使用状況の詳細ファイルの両方で同じ内容を意味します。 たとえば、請求書の請求サイクルは、使用状況の詳細ファイルに示された請求期間と同じです。

 | 請求書 (PDF) | 使用状況の詳細 (CSV)|
 | --- | --- |
|請求サイクル | 請求期間 |
 |Name |測定カテゴリ |
 |type |測定サブカテゴリ |
 |リソース |測定名 |
 |リージョン |測定リージョン |
 |消費量 |消費量 |
 |あり |含まれる量 |
 |課金対象 |超過量 |

請求書の **[使用料金]** セクションには、請求期間中に使用された各メーターの合計値が含まれています。 たとえば、次のスクリーン ショットでは、Azure Scheduler サービスの使用料金が表示されます。

![請求書の使用料金](./media/billing-understand-your-bill/1.png)

使用状況の詳細の CSV の **[明細書]** セクションに同じ料金が表示されます。 "*[消費量]*" と "*[料金]*" は請求書と一致します。

![CSV の使用料金](./media/billing-understand-your-bill/2.png)

この料金の日々の明細を確認するには、CSV の **[毎日の使用状況]** セクションに移動します。 "*[メーター カテゴリ]*" の [Scheduler] をフィルター処理することで、メーターが使用された日とその日の消費量を確認できます。 "*[リソース]*" と "*[リソース グループ]*" の情報も、比較用に一覧表示されます。 "*[消費量]*" の合計値が請求書に表示される値になります。

![CSV の [毎日の使用状況] セクション](./media/billing-understand-your-bill/3.png)

1 日あたりのコストを取得するには、**[明細書]** セクションの "*[消費量]*" を "*[レート]*" で乗算します。

請求書の詳細については、[Azure 請求書の確認](billing-understand-your-invoice.md)に関する記事を参照してください。

CSV の各列については、[Azure の詳細な利用状況の確認](billing-understand-your-invoice.md)に関する記事を参照してください。

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>オプション 2: 請求書を確認し、Azure ポータルの使用状況およびコストと比較する

Azure Portal で料金を確認することもできます。Azure Portal は、請求書の使用状況と料金の概要を示すコスト管理グラフを提供します。

上記の例から続行するには、[[サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)に移動してサブスクリプションを選択し、**[コスト分析]** を選択します。 そこから、期間を指定して、Azure Scheduler サービスの使用料金を確認できます。

![Azure ポータルでのコスト分析ビュー](./media/billing-understand-your-bill/4.png)

**[コスト履歴]** の毎日のコストの明細を表示するには、行をクリックします。

![Azure ポータルでのコスト履歴ビュー](./media/billing-understand-your-bill/5.png)

詳細については、「[Azure の課金とコスト管理で予想外のコストを防ぐ](billing-getting-started.md#costs)」をご覧ください。

## <a name="external"></a>外部サービスの料金について
外部サービス (Azure Marketplace 注文とも呼ばれます) は、独立系サービス ベンダーによって提供され、別途課金されます。 これらの料金は、Azure の請求書には表示されません。 詳細については、「[Azure 外部サービスの課金について](billing-understand-your-azure-marketplace-charges.md)」を参照してください。

## <a name="payment"></a>支払い方法

支払い方法としてクレジット カードまたはデビット カードを設定している場合、支払いは、請求期間の終了後、10 日以内に自動的に行われます。 クレジット カードの明細には、**MSFT Azure** と表記されます。

[請求書による支払い](billing-how-to-pay-by-invoice.md)を利用している場合は、請求書の下部に記載されている場所に送金します。 詳しくは、 [サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>クレジット カードによる支払い状況を確認するにはどうすればよいですか。

[サポート チケットを作成](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)して、支払い状況の確認に関するサポートを要求してください。 

## <a name="are-there-different-azure-customer-types-how-do-i-know-what-customer-type-i-am"></a>Azure の顧客にはさまざまなタイプがありますか。 自分の顧客タイプはどうすればわかりますか。
Azure の顧客にはさまざまなタイプがあります。 価格設定や請求書をよく理解するために、次の顧客タイプの説明をお読みください。

- **Enterprise**: Enterprise 顧客は、Azure と Enterprise Agreement を締結し、交渉によって年額コミットメントを決定しています。Azure リソースのカスタム価格が提示されます。
- **Web Direct**: Web Direct 顧客は、Azure とカスタム契約を締結していません。 このような顧客は azure.com から Azure に新規登録し、すべての Azure リソースについて一般向け価格が提示されます。
- **クラウド サービス プロバイダー**: クラウド サービス プロバイダーとは、通常、Azure 上にソリューションを構築するようにエンド ユーザーから依頼される企業です。

## <a name="why-dont-i-see-the-cost-the-resource-i-have-created-in-my-bill"></a>作成したリソースのコストが請求書に表示されないのはなぜですか。
Azure は、リソースのコストに直接基づいて課金するわけではありません。 課金は、リソースの有効期間における使用状況を追跡するために使用する 1 つ以上の測定に基づいています。 これらの測定が課金の計算に使用されます。 Azure の測定については以下をご覧ください。

## <a name="how-does-azure-charge-metering-work"></a>Azure の料金測定はどのようなしくみですか。
仮想マシンなど 1 つの Azure リソースを使用すると、1 つ以上の測定インスタンスが作成されます。 これらの測定は、時間経過に伴ってリソースの使用状況を追跡するために使用されます。 各測定によって出力された使用状況レコードが、コスト測定システムで Azure によって課金の計算に使用されます。 

たとえば、Azure に作成された 1 つの仮想マシンに対して、使用状況を追跡するために次の測定が作成される場合があります。

- コンピューティング時間
- IP アドレス時間
- データ転送 (受信)
- データ転送 (送信)
- Standard マネージド ディスク
- Standard マネージド ディスク操作
- Standard IO - ディスク
- Standard IO - ブロック BLOB (読み取り)
- Standard IO - ブロック BLOB (書き込み)
- Standard IO - ブロック BLOB (削除)

VM が作成されると、上記の測定それぞれが使用状況レコードを出力し始めます。 後から Azure の測定システムで、この使用状況と測定の価格を組み合わせて、顧客に対する請求額が決定されます。

> [!Note]
> 上記の測定の例は、作成される VM に対して作成される測定のごく一部です。

## <a name="what-is-the-difference-between-azure-1st-party-charges-and-azure-marketplace-charges"></a>Azure ファースト パーティ料金と Azure Marketplace 料金の違いは何ですか。
Azure ファースト パーティ料金は、Azure が直接開発して提供しているリソースのものです。 Azure Marketplace 料金は、サード パーティ ソフトウェア ベンダーによって作成され、Azure Marketplace で入手できるリソースのものです。 たとえば、Barracuda Firewall はサード パーティによって提供される Azure Marketplace リソースです。 このファイアウォールと対応する測定に関するすべての料金は、マーケットプレース料金として表示されます。 

## <a name="tips-for-cost-management"></a>コスト管理に関するヒント
- [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)や[総保有コスト計算ツール](https://aka.ms/azure-tco-calculator)を使用してコストを推定し、[各サービスの詳細な料金情報](https://azure.microsoft.com/pricing/)を取得します。
- [課金アラートを設定](billing-set-up-alerts.md)します。
- [Azure ポータルで使用状況とコストを定期的に確認します](billing-getting-started.md#costs)。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
