---
title: 従量課金制サブスクリプションの Azure 予約インスタンスの使用量について - Azure Billing | Microsoft Docs
description: 従量課金制サブスクリプションに Azure 予約 VM インスタンスがどのように適用されているかを把握するための使用状況の読み方について説明します。
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: manshuk
ms.openlocfilehash: 7bf4aea86d4d430c15d60a8d73365705ace18b5a
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301854"
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>従量課金制サブスクリプションの予約インスタンス使用量について

Azure の予約 VM インスタンスの使用状況は、[予約ページ](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)から ReservationId を使用するか、[Azure アカウント ポータル](https://account.azure.com)から使用状況ファイルを使用して把握することができます。


>[!NOTE]
>この記事の内容は EA ユーザーには当てはまりません。 EA ユーザーの方は、「[エンタープライズ加入契約の予約インスタンス使用量について](billing-understand-reserved-instance-usage-ea.md)」を参照してください。 またこの記事は、単一のサブスクリプションに予約インスタンスが適用されていることを前提として書かれています。 複数のサブスクリプションに予約インスタンスが適用されている場合、複数の使用状況ファイル (csv) に予約インスタンスの特典がまたがる場合があります。 

以降のセクションでは、Standard_DS1_v2 Windows VM が米国東部リージョンで実行され、予約インスタンス情報が次の表のようになっていることを想定して説明します。

| フィールド | 値 |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|数量 |1|
|SKU | Standard_DS1_v2|
|リージョン | eastus |

## <a name="reserved-instance-application"></a>予約インスタンスの適用

デプロイされている VM が予約インスタンスの属性と合致していることで特典が適用されるのは、VM のハードウェア部分です。 予約インスタンスによって充当されない Windows ソフトウェアについては、「[Azure 予約 VM インスタンスにおける Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)」を参照してください。

### <a name="statement-section-of-csv"></a>CSV の [明細書] セクション
CSV のこのセクションには、予約インスタンスの合計使用量が表示されます。 [測定サブカテゴリ] フィールドで、"予約-" を含んだフィルターを適用すると、次のスクリーンショットのようにデータが表示されます。![フィルターが適用された予約インスタンスの使用状況の詳細と料金](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

[占有 - ベース VM] 行には、予約インスタンスによって充当される合計時間が表示されます。 予約インスタンスの購入分が充当されるため、この行は $0.00 です。 [占有 - Windows Svr (1 コア)] 行は、Windows ソフトウェアのコストに充当されます。

### <a name="daily-usage-section-of-csv"></a>CSV の [日ごとの使用量] セクション
追加情報でフィルター処理し、**予約 ID** を入力します。 次のスクリーンショットは、予約インスタンスに関連したフィールドを示しています。 

![毎日の使用状況の詳細と料金のスクリーンショット](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. [追加情報] フィールドの **ReservationId** は、VM に特典を適用する際に使用された予約インスタンスです。
2. ConsumptionMeter は、VM のメーター ID です。
3. [測定サブカテゴリ] 行の [占有 - ベース VM] は、[明細書] セクションの $0 コストの行を表します。 この VM の実行コストは、予約インスタンスによる前払いが済んでいます。
4. これは予約インスタンスのメーター ID です。 このメーターのコストは $0 です。 予約インスタンスの適用資格を満たしたすべての VM は、そのコストを説明するこの MeterId が CSV に存在します。 
5. Standard_DS1_v2 は、vCPU を 1 個搭載した VM です。この VM は、Azure ハイブリッド特典なしでデプロイされます。 そのため、Windows ソフトウェアに対する追加料金は、このメーターでカバーされます。 「[Azure 予約 VM インスタンスにおける Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)」を参照してください。 D シリーズの 1 コア VM に対応するメーターが記載されています。 Azure ハイブリッド特典が使用されている場合、この追加料金は適用されません。 

## <a name="next-steps"></a>次の手順
予約インスタンスの詳細については、次の記事を参照してください。

- [Azure Reserved Instances で仮想マシンのコストを削減する](billing-save-compute-costs-reservations.md)
- [予約インスタンスによる仮想マシンの前払い](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [予約インスタンスの管理](billing-manage-reserved-vm-instance.md)
- [予約インスタンスの割引の適用方法について](billing-understand-vm-reservation-charges.md)
- [エンタープライズ加入契約の予約インスタンス使用量について](billing-understand-reserved-instance-usage-ea.md)
- [予約インスタンスに含まれない Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。
