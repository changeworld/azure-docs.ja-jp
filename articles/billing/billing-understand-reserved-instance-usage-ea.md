---
title: "エンタープライズ向けの Azure 予約インスタンス使用量 | Microsoft Docs"
description: "エンタープライズ加入契約に予約インスタンスがどのように適用されているかを把握するための使用状況の読み方について説明します。"
services: billing
documentationcenter: 
author: manish-shukla01
manager: manshuk
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: 515eae3c9a84a171bebc5213f5824e1b50336e34
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="understand--reserved-instance-usage-for-your-enterprise-enrollment"></a>エンタープライズ加入契約の予約インスタンス使用量について
予約インスタンスの使用状況は、[予約ページ](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade )から ReservationId を使用するか、[EA ポータル](https://ea.azure.com)から使用状況ファイルを使用して把握することができます。 予約の使用状況は、[EA ポータル](https://ea.azure.com)の使用状況の概要セクションで確認することもできます。

>[!NOTE]
>従量課金の課金コンテキストで予約を購入した場合、「[従量課金サブスクリプションの予約インスタンス使用量について](billing-understand-reserved-instance-usage.md)」を参照してください。

以降のセクションでは、Standard_D1_v2 Windows VM が米国東部リージョンで実行され、予約情報が次の表のようになっていることを想定して説明します。

| フィールド | 値 |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|数量 |1|
|SKU | Standard_D1|
|リージョン | eastus |

## <a name="reservation-application"></a>予約の適用

デプロイされている VM が予約の属性と合致していることで特典が適用されるのは、VM のハードウェア部分です。 予約インスタンスによって充当されない Windows ソフトウェアについては、「[Azure Reserved VM Instances における Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)」を参照してください。


### <a name="reservation-usage-in-csv"></a>予約の使用状況 (CSV)
EA の使用状況の CSV は EA ポータルからダウンロードできます。 ダウンロードした CSV ファイルのデータを追加情報でフィルター処理し、予約 ID を入力します。 次のスクリーンショットは、予約に関連したフィールドを示しています。

![EA の予約インスタンスの CSV](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. [追加情報] フィールドの ReservationId は、VM に特典を適用する際に使用された予約を表します。
2. ConsumptionMeter は、VM のメーター ID です。
3. これは ReservationMeter で、コストは $0 です。実行中の VM のコストは予約によって前払いが済んでいます。 
4. Standard_D1 は、vCPU を 1 個搭載した VM です。この VM は、Azure ハイブリッド特典なしでデプロイされます。 そのため、Windows ソフトウェアに対する追加料金は、このメーターでカバーされます。 「[Azure 予約 VM インスタンスにおける Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)」を参照してください。 D シリーズの 1 コア VM に対応するメーターが記載されています。 Azure ハイブリッド特典が使用されている場合、この追加料金は適用されません。

### <a name="reservation-usage-in-usage-summary-page-in-ea-portal"></a>EA ポータルの使用状況の概要ページに表示される予約の使用状況

予約インスタンス使用量は、EA ポータルの使用状況の概要セクションにも表示されます。![EA の使用状況の概要](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. VM のハードウェア コンポーネントは、予約インスタンスによって充当されるため、料金は発生しません。 
2. Windows ソフトウェアは、Azure ハイブリッド特典が使用されていないため、料金が発生します。 

## <a name="next-steps"></a>次の手順
予約仮想マシン インスタンスの詳細については、次の記事を参照してください。

- [予約 VM インスタンスによる仮想マシンの前払い](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [予約仮想マシン インスタンスの管理](billing-manage-reserved-vm-instance.md)
- [予約仮想マシン インスタンスで仮想マシンのコストを削減する](billing-save-compute-costs-reservations.md)
- [予約仮想マシン インスタンスの割引の適用方法について](billing-understand-vm-reservation-charges.md)
- [従量課金サブスクリプションの予約インスタンス使用量について](billing-understand-reserved-instance-usage.md)
- [予約インスタンスに含まれない Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。