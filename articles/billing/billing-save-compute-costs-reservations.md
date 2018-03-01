---
title: "前払いで Azure 仮想マシンのコスト削減 - Azure | Microsoft Docs"
description: "Azure 予約仮想マシン インスタンスによる仮想マシンのコスト削減について説明します。"
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikramdesai01
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: vikdesai
ms.openlocfilehash: 799abddc4894bc090d860e7fe100ee65d4d085ab
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="save-money-on-virtual-machines-with-reserved-virtual-machine-instances"></a>予約仮想マシン インスタンスで仮想マシンのコストを削減する 
仮想マシンは、予約仮想マシン インスタンスを通じて 1 年分または 3 年分の計算処理能力を前払いすることで割引価格で利用することができます。 これにより、仮想マシンのコストが大幅に削減されます。割引率は、従量課金制の料金に対し、1 年間または 3 年間の前払い契約で最大 72% となります。 予約仮想マシン インスタンスは課金割引であり、仮想マシンの実行時の状態には影響しません。

予約仮想マシン インスタンスは [Azure Portal](https://aka.ms/reservations) で購入できます。 詳細については、[予約仮想マシン インスタンスによる仮想マシンの使用料の前払いとコスト削減](https://go.microsoft.com/fwlink/?linkid=861721)に関するページを参照してください。

## <a name="why-should-i-buy-a-reserved-virtual-machine-instance"></a>予約仮想マシン インスタンスを購入しなければならないのはなぜですか
長期にわたって実行される仮想マシンがある場合、予約仮想マシン インスタンスを購入することで費用効果を最大限に高めることができます。 たとえば予約なしで、Standard D2 のインスタンスを米国西部リージョンで常時 4 つ実行する場合、従量課金制の料金が請求されます。 その 4 つの VM に関して予約仮想マシン インスタンスを購入した場合、それらの VM の課金に関して、直ちにメリットが発生します。 それ以降は、従量課金制の料金が適用されなくなります。 

## <a name="what-charges-does-a-reserved-virtual-machine-instance-cover"></a>予約仮想マシン インスタンスには何の料金が含まれますか
予約に含まれるのは、Windows 仮想マシンと Linux 仮想マシンの仮想マシン インフラストラクチャの料金だけです。 その他のソフトウェア、ネットワーク、ストレージの料金は含まれません。 Windows 仮想マシンについては、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)で Windows のライセンス コストを賄うことができます。

## <a name="whos-eligible-to-purchase-a-reserved-virtual-machine-instance"></a>予約仮想マシン インスタンスを購入する資格があるのはだれですか
予約仮想マシン インスタンスは、サブスクリプションの種類が次のいずれかに該当する Azure ユーザーが購入できます。
-   エンタープライズ契約サブスクリプション プラン タイプ (MS-AZR-0017P)。
-   [従量課金](https://azure.microsoft.com/offers/ms-azr-0003p/)サブスクリプション プラン タイプ (MS-AZR-003P)。
予約インスタンスを購入するには、サブスクリプションの "所有者" ロールに属している必要があります。 エンタープライズ登録で予約を購入する場合、エンタープライズ管理者が EA ポータルで予約の購入を有効にする必要があります。既定では、この設定が有効になっています。

## <a name="how-is-a-reserved-virtual-machine-instances-purchase-billed"></a>予約仮想マシン インスタンスを購入した場合はどのように課金されますか
予約の購入は、サブスクリプションに紐付けられている支払い方法に対して課金されます。 エンタープライズ サブスクリプションの場合、予約コストは年額コミットメントの残高から差し引かれます。 年額コミットメントの残高で予約のコストを賄えない場合は、超過分が課金されます。
従量課金制サブスクリプションをご利用の場合、アカウントに登録されているクレジット カードに直接課金されます。 請求書による課金の場合、次回の請求書に料金が表示されます。

## <a name="how-is-the-purchased-reserved-virtual-machine-instance-discount-applied"></a>購入した予約仮想マシン インスタンスの割引はどのように適用されますか
予約仮想マシン インスタンスの割引は、予約の購入時に選択した属性と合致した仮想マシンに適用されます。 代表的な属性の 1 つは、合致する VM が実行されるスコープです。 たとえば米国西部リージョンの Standard D2 仮想マシンの予約 VM インスタンス割引を希望する場合、その VM が実行されているサブスクリプションを選択します。 その仮想マシンが、加入契約/アカウント内の複数の異なるサブスクリプションで実行される場合は、共有スコープを選択します。 共有スコープによって、サブスクリプションをまたぐ予約割引の適用が可能となります。
スコープは、予約 VM インスタンスの購入後に変更することができます。 スコープの変更については、予約の管理方法に関するドキュメントを参照してください。

予約割引が適用されるのは、エンタープライズ プラン タイプまたは従量課金プラン タイプに該当するサブスクリプションの仮想マシンのみです。 その他のプラン タイプのサブスクリプションで実行される仮想マシンについては、予約割引の対象外となります。 エンタープライズ加入契約に関して、Enterprise Dev/Test サブスクリプションには予約インスタンスの特典が適用されません。

予約の特典が仮想マシンの課金にどのように適用されるかについては、[予約による割引の適用](https://go.microsoft.com/fwlink/?linkid=863405)に関するページを参照してください。

## <a name="what-happens-when-the-reservation-term-expires"></a>予約期間が終了するとどうなりますか
予約期間が満了した時点で、課金割引の有効期限が切れ、従量課金料金が仮想マシン インフラストラクチャに適用されます。 予約は自動更新されません。 引き続き課金割引の適用を受けるためには、新しい予約仮想マシン インスタンスをご購入ください。 

## <a name="sizes-and-regional-availability"></a>サイズと提供されるリージョン
予約はほとんどの VM サイズで提供されますが、一部例外があります。
- プレビュー VM サイズ - プレビューでは、そのサイズにかかわらず、予約仮想マシン インスタンスの購入対象外となります。
- クラウド - Azure 米国政府機関、ドイツ、中国の各リージョンでは、予約仮想マシン インスタンスが提供されません。 
- クォータの不足 - 単一サブスクリプションをスコープとする予約 VM インスタンスの場合、新しい予約インスタンスに割り当てることのできる vCPU クォータがそのサブスクリプションに存在していることが必要です。 たとえば対象のサブスクリプションに、D-Series ファミリーに関して 10 vCPU のクォータ制限がある場合、Standard_D1 インスタンス 11個分の予約 VM インスタンスを購入することはできません。 予約のクォータ チェックには、既にサブスクリプションにデプロイされている VM が含まれます。 たとえば、D-Series ファミリーに関して 10 vCPU のクォータがサブスクリプションにあるとします。 このサブスクリプションに 2 つの standard_D1 インスタンスがデプロイされている場合、このサブスクリプションでは、10 standard_D1 インスタンスの予約 VM インスタンスを購入することができます。 
- 容量制限 - まれなケースですが、一部の VM サイズに関して、リージョンのキャパシティが低下しているために、新しい予約を購入できないよう Azure によって制限されます。

## <a name="next-steps"></a>次の手順
実際に[予約仮想マシン インスタンス](https://go.microsoft.com/fwlink/?linkid=861721)を購入して仮想マシンのコストを節約してみましょう。 

予約仮想マシン インスタンスの詳細については、次の記事を参照してください。

- [予約 VM インスタンスによる仮想マシンの前払い](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [予約仮想マシン インスタンスの管理](billing-manage-reserved-vm-instance.md)
- [予約仮想マシン インスタンスの割引の適用方法について](billing-understand-vm-reservation-charges.md)
- [従量課金サブスクリプションの予約インスタンス使用量について](billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約インスタンス使用量について](billing-understand-reserved-instance-usage-ea.md)
- [予約インスタンスに含まれない Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
