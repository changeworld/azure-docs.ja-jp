---
title: Azure 予約インスタンスとは | Microsoft Docs
description: 仮想マシンのコストを節約し、費用効果を最大限に高める料金を得るため、Azure Reserved Instances と VM 価格について学習します。
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: 12fc88596b4283b6f809575328ab801704cc1c8d
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064522"
---
# <a name="what-are-azure-reserved-vm-instances"></a>Azure Reserved VM Instances とは
[Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances) は、1 年分または 3 年分の計算処理能力を前払いすることで、仮想マシンを割引価格で使用できるため、節約に役立ちます。 Azure 予約インスタンスにより、仮想マシンのコストが大幅に削減されます。割引率は、従量課金制の料金に対し、1 年間または 3 年間の前払い契約で最大 72% となります。 予約インスタンスは課金割引を提供するもので、仮想マシンの実行時の状態には影響しません。

予約インスタンス (RI) は [Azure Portal](https://aka.ms/reservations) で購入できます。 詳細については、[予約インスタンスによる仮想マシンの使用料の前払いとコスト削減](https://go.microsoft.com/fwlink/?linkid=861721)に関するページを参照してください。

## <a name="why-should-i-buy-a-reserved-instance"></a>予約インスタンスを購入しなければならないのはなぜですか
長期にわたって実行される仮想マシンがある場合、予約インスタンスを購入することで最も費用効果が高いオプションを選択できます。 たとえば予約インスタンスなしで、Standard D2 VM のインスタンスを米国西部リージョンで常時 4 つ実行する場合、従量課金制の料金が請求されます。 その 4 つの VM に関して予約インスタンスを購入した場合、それらの VM の課金に関して、直ちにメリットが発生します。 それ以降は、従量課金制の料金が適用されなくなります。 

## <a name="what-charges-does-a-reserved-instance-cover"></a>予約インスタンスには何の料金が含まれますか
予約インスタンスに含まれるのは、Windows 仮想マシンと Linux 仮想マシンの仮想マシン インフラストラクチャの料金だけです。 その他のソフトウェア、ネットワーク、ストレージの料金は含まれません。 Windows 仮想マシンについては、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)で Windows のライセンス コストを賄うことができます。

## <a name="whos-eligible-to-purchase-a-reserved-instance"></a>予約インスタンスを購入する資格があるのはだれですか
予約インスタンスは、サブスクリプションの種類が次のいずれかに該当する Azure ユーザーが購入できます。
-   エンタープライズ契約サブスクリプション プラン タイプ (MS-AZR-0017P)。
-   [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)サブスクリプション プラン タイプ (MS-AZR-003P)。 予約インスタンスを購入するには、サブスクリプションの "所有者" ロールに属している必要があります。 エンタープライズ登録で予約インスタンスを購入する場合、エンタープライズ管理者が EA ポータルで予約インスタンスの購入を有効にする必要があります。 既定では、この設定が有効になっています。
-   クラウド ソリューション プロバイダー (CSP) パートナーは、Azure Portal または[パートナー センター](https://docs.microsoft.com/partner-center/azure-reservations)を使用して予約インスタンスを購入できます。

## <a name="how-is-a-reserved-instance-purchase-billed"></a>予約インスタンスを購入した場合はどのように課金されますか
予約インスタンスの購入は、サブスクリプションに紐付けられている支払い方法に対して課金されます。 エンタープライズ サブスクリプションの場合、予約インスタンスのコストは年額コミットメントの残高から差し引かれます。 年額コミットメントの残高で予約インスタンスのコストを賄えない場合は、超過分が課金されます。
従量課金制サブスクリプションをご利用の場合、アカウントに登録されているクレジット カードに直接課金されます。 請求書による課金の場合、次回の請求書に料金が表示されます。

## <a name="how-is-the-purchased-reserved-instance-discount-applied"></a>購入した予約インスタンスの割引はどのように適用されますか
予約インスタンスの割引は、予約インスタンスの購入時に選択した属性と合致した仮想マシンに適用されます。 代表的な属性の 1 つは、合致する VM が実行されるスコープです。 たとえば米国西部リージョンの Standard D2 仮想マシンの予約インスタンスの割引を希望する場合、その VM が実行されているサブスクリプションを選択します。 その仮想マシンが、加入契約/アカウント内の複数の異なるサブスクリプションで実行される場合は、共有スコープを選択します。 共有スコープによって、サブスクリプションをまたぐ予約インスタンスの割引の適用が可能となります。 スコープは、予約インスタンスの購入後に変更することができます。 スコープの変更については、[Azure での予約インスタンスの管理](billing-manage-reserved-vm-instance.md)に関するページを参照してください。

予約インスタンスの割引が適用されるのは、エンタープライズまたは従量課金制のサブスクリプションの種類に関連付けられた仮想マシンのみです。 その他のプラン タイプのサブスクリプションで実行される仮想マシンについては、予約インスタンスの割引の対象外となります。 エンタープライズ加入契約に関して、Enterprise Dev/Test サブスクリプションには予約インスタンスの特典が適用されません。

予約インスタンスが仮想マシンの課金にどのように影響するかについて理解を深めるため、「[予約インスタンスの割引の適用方法について](billing-understand-vm-reservation-charges.md)」を参照してください。

## <a name="what-happens-when-the-reserved-instance-term-expires"></a>予約インスタンス期間が終了するとどうなりますか
予約インスタンス期間が満了した時点で、課金割引の有効期限が切れ、従量課金料金が仮想マシン インフラストラクチャに適用されます。 Azure 予約インスタンスは自動更新されません。 引き続き課金割引の適用を受けるためには、新しい予約インスタンスをご購入ください。 

## <a name="sizes-and-regional-availability"></a>サイズと提供されるリージョン
予約インスタンスはほとんどの VM サイズで提供されますが、一部例外があります。
- プレビューの VM: プレビューでは、VM シリーズまたはサイズにかかわらず、予約インスタンスの購入対象外となります。
- クラウド: Azure 米国政府機関、ドイツ、中国の各リージョンでは、予約インスタンスが提供されません。 
- クォータの不足: 単一サブスクリプションをスコープとする予約インスタンスの場合、新しい予約インスタンスに割り当てることのできる vCPU クォータがそのサブスクリプションに存在していることが必要です。 たとえば対象のサブスクリプションに、D-Series に対して 10 vCPU のクォータ制限がある場合、Standard_D1 インスタンス 11 個分の予約インスタンスを購入することはできません。 予約インスタンスのクォータ チェックには、既にサブスクリプションにデプロイされている VM が含まれます。 たとえば、サブスクリプションに D-Series に対する 10 vCPU のクォータがあり、2 つの standard_D1 インスタンスがデプロイされている場合、このサブスクリプションでは、10 standard_D1 インスタンスの予約インスタンスを購入することができます。 
- 容量制限: まれなケースですが、一部の VM サイズに関して、リージョンのキャパシティが低下しているために、新しい予約インスタンスを購入できないよう Azure によって制限されます。

## <a name="next-steps"></a>次の手順
実際に [Azure Reserved Instances](https://go.microsoft.com/fwlink/?linkid=861721) を購入して仮想マシンのコストを節約してみましょう。 

予約インスタンスの詳細については、次の記事を参照してください。

- [Azure Reserved VM Instances とは](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure での予約インスタンスの管理](billing-manage-reserved-vm-instance.md)
- [予約インスタンスの割引の適用方法について](billing-understand-vm-reservation-charges.md)
- [従量課金制サブスクリプションの予約インスタンス使用量について](billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約インスタンス使用量について](billing-understand-reserved-instance-usage-ea.md)
- [予約インスタンスに含まれない Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)
- [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムで予約済みのインスタンス](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。
