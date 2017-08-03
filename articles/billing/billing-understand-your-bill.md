---
title: "Azure の課金内容の確認 | Microsoft Docs"
description: "Azure サブスクリプションの使用状況と課金内容を確認し、理解する方法について説明します。"
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: tonguyen
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 264078587b81d7840fe5976498a91ae4f535b6aa
ms.contentlocale: ja-jp
ms.lasthandoff: 07/08/2017


---
# <a name="understand-your-bill-for-microsoft-azure"></a>Microsoft Azure の課金内容の確認
Azure の課金内容を確認するには、詳細な毎日の使用状況ファイルおよびAzure Portal のコスト管理レポートと、請求書を比較します。

請求書の PDF および詳細な毎日の使用状況ファイルの CSV ダウンロードについては、[Azure 課金の請求書および毎日の使用状況データ](billing-download-azure-invoice-daily-usage-date.md)に関する記事をご覧ください。 

請求書および詳細な毎日の使用状況ファイルの詳しい用語と説明については、[Microsoft Azure 請求書の用語の確認](billing-understand-your-invoice.md)に関する記事と「[Microsoft Azure の詳細な利用料金の用語を理解する](billing-understand-your-usage.md)」をご覧ください。

コスト管理レポートの詳細については、[Azure Portal のコスト管理](https://docs.microsoft.com/en-us/azure/billing/billing-getting-started)に関する記事をご覧ください。

## <a name="reconcile"></a>請求書の料金が正しいことを確認する方法
請求書上に詳細を知りたい料金がある場合は、次の 2 つの選択肢があります。

### <a name="review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>請求書を確認し、使用状況の詳細の CSV ファイルと、使用状況およびコストを比較する

使用状況の詳細の CSV ファイルでは、請求期間と毎日の使用状況ごとの料金を提示しています。 使用状況の詳細の CSV ファイルを取得するには、「[Azure の請求書と毎日の使用状況データを取得する方法](https://docs.microsoft.com/en-us/azure/billing/billing-download-azure-invoice-daily-usage-date)」をご覧ください。

使用料金は、メーター レベルで表示されます。 以下の用語一覧は、請求書と使用状況の詳細ファイル間で同じ内容を意味します。 たとえば、請求書の請求サイクルは、使用状況の詳細ファイルに示された請求期間と同じです。

 | 請求書 (PDF) | 使用状況の詳細 (CSV)|
 | --- | --- |
 |請求サイクル | 請求期間 |
 |名前 |測定カテゴリ |
 |種類 |測定サブカテゴリ |
 |リソース |測定名 |
 |リージョン |測定リージョン |
 |消費量 |消費量 |
 |あり |含まれる量 |
 |課金対象 |超過量 |

請求書の使用料金のセクションには、請求期間中に使用された各メーターの合計値が含まれています。 たとえば、次のスクリーン ショットでは、Azure Scheduler サービスの使用料金が表示されます。

![請求書の使用料金](./media/billing-understand-your-bill/1.png)

同じ料金が、使用状況の詳細の CSV の明細書セクションに表示されます。

![CSV の使用料金](./media/billing-understand-your-bill/2.png)

さらに、使用状況の詳細の CSV ファイルには、毎日の使用状況のセクションがあります。 このセクションには、メーターが使用された各日の明細項目があります。 これらの明細項目は、請求期間中のメーターの使用量に加算されます。

請求書の詳しいセクション、用語、および説明については、 [こちら](billing-understand-your-invoice.md)を参照してください。

毎日の使用状況の詳細ファイルの詳しいセクション、用語、および説明については、[こちら](billing-understand-your-usage.md)を参照してください。

### <a name="review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>請求書を確認し、Azure Portal の使用状況およびコストと比較する

Azure Portal で料金を確認することもできます。 Azure Portal では、請求書の使用状況と料金の概要を示したコスト管理グラフを提供しています。

Azure Portal で利用可能なコスト管理ツールのいくつかの例を次に示します。

![Azure Portal でのリソースごとのコスト](./media/billing-understand-your-bill/3.png)

このグラフは、リソースごとの現在のコストを示します。

![Azure Portal での使用料金と予測](./media/billing-understand-your-bill/4.png)

このグラフは、このサブスクリプションの現在のコストと、残りの請求期間の予測コストを示します。

![Azure Portal でのコスト分析ビュー](./media/billing-understand-your-bill/5.png)

コスト分析は、さまざまな請求期間のリソースとメーター レベルの使用状況を示します。

詳細については、「[Azure の課金とコスト管理で予想外のコストを防ぐ](billing-getting-started.md#costs)」をご覧ください。

## <a name="external"></a>外部サービスの料金について
外部サービス (Marketplace 注文とも呼ばれます) は、独立系サービス ベンダーによって提供され、個別に課金されます。 料金は、Azure の請求書には表示されません。 詳細については、「[Azure 外部サービスの課金について](billing-understand-your-azure-marketplace-charges.md)」を参照してください。

## <a name="payment"></a>支払い方法
支払い方法としてクレジット カードやデビット カードを設定している場合、支払いは自動的に行われます。 クレジット カードの明細には、**MSFT Azure** と表記されます。

[支払い方法として請求書](https://azure.microsoft.com/pricing/invoicing/)を利用している場合、請求書の下部に記載されている場所に支払いを送付します。 詳しくは、 [サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>クレジット カードによる支払い状況を確認するにはどうすればよいですか。
[サポート チケットを作成](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)して、支払い状況の確認に関するサポートを要求してください。 

## <a name="tips-for-cost-management"></a>コスト管理に関するヒント
- [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)や[総保有コスト計算ツール](https://aka.ms/azure-tco-calculator)を使用してコストを見積もり、サービスを追加するときにもコストを見積もる
- [課金アラートのセットアップ](billing-set-up-alerts.md)
- [Azure Portal で使用状況とコストを定期的に確認する](billing-getting-started.md#costs)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。 
お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

