---
title: Azure の予約とは | Microsoft Docs
description: 仮想マシン、SQL データベース、その他のリソースのコストを節約するための Azure の予約と価格について説明します。
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
ms.date: 08/08/2018
ms.author: yashar
ms.openlocfilehash: 93c11852a11e0bb57a0b92090368298fc14b8c2a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626306"
---
# <a name="what-are-azure-reservations"></a>Azure の予約とは

Azure の予約は、仮想マシンまたは SQL Database のコンピューティング能力を 1 年分または 3 年分前払いすることで、コストを削減するのに役立ちます。 前払いすると、使用するリソースの割り引きを受けることができます。 Azure の予約により、仮想マシンまたは SQL Database のコンピューティング コストが大幅に削減されます。割引率は、従量課金制の料金に対し、1 年間または 3 年間の前払い契約で最大 72% となります。 予約は課金割引を提供するもので、仮想マシンまたは SQL Database の実行時の状態には影響しません。

予約は [Azure portal](https://aka.ms/reservations) で購入できます。 詳細については、次のトピックを参照してください。

- [Azure Reserved VM Instances による仮想マシンの前払い](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database の容量が予約された SQL Database コンピューティング リソースの前払い](../sql-database/sql-database-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>予約を購入しなければならないのはなぜですか

長期にわたって実行される仮想マシンまたは SQL Database がある場合、予約を購入することで最も費用効果が高いオプションを選択できます。 たとえば予約なしでサービスの 4 つのインスタンスを常時実行した場合、従量課金制の料金が請求されます。 これらのリソースの予約を購入すると、予約割引がすぐに得られます。 リソースには従量課金制の料金が適用されなくなります。

## <a name="what-charges-does-a-reservation-cover"></a>予約には何の料金が含まれますか

- 予約仮想マシン インスタンス: 予約の対象は仮想マシンのコンピューティング コストのみです。 その他のソフトウェア、ネットワーク、ストレージの料金は含まれません。
- SQL Database の予約された仮想コア: 予約にはコンピューティング コストのみが含まれます。 ライセンスの料金は別途請求されます。

Windows 仮想マシンと SQL Database については、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)でライセンス コストを賄うことができます。

## <a name="whos-eligible-to-purchase-a-reservation"></a>予約を購入する資格があるのはだれですか

予約は、サブスクリプションの種類が次のいずれかに該当する Azure ユーザーが購入できます。

- エンタープライズ契約サブスクリプション オファー タイプ (MS-AZR-0017P)。
- 
  [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)サブスクリプション オファー タイプ (MS-AZR-003P)。 予約を購入するには、サブスクリプションの "所有者" ロールが必要です。 エンタープライズ登録で予約を購入するには、エンタープライズ管理者が EA ポータルで予約の購入を有効にする必要があります。 既定では、この設定が有効になっています。
- クラウド ソリューション プロバイダー (CSP) パートナーは、Azure portal または[パートナー センター](https://docs.microsoft.com/partner-center/azure-reservations)を使用して Azure の予約を購入できます。 

Azure の予約の割引が適用されるのは、エンタープライズ、従量課金制、または CSP のサブスクリプションの種類に関連付けられた仮想マシンまたは SQL Databases のみです。

## <a name="how-is-a-reservation-billed"></a>予約はどのように課金されますか

予約は、サブスクリプションに関連付けられている支払い方法に対して課金されます。 エンタープライズ サブスクリプションの場合、予約コストは年額コミットメントの残高から差し引かれます。 年額コミットメントの残高で予約のコストを賄えない場合は、超過分が課金されます。 従量課金制サブスクリプションをご利用の場合、アカウントに登録されているクレジット カードに直接課金されます。 請求書による課金の場合、次回の請求書に料金が表示されます。

## <a name="how-is-the-reservation-discount-applied"></a>予約割引の適用方法

予約割引は、予約購入時に選択した属性と一致するリソースの使用に適用されます。 代表的な属性の 1 つは、一致する VM、SQL Database、他のリソースが実行されるスコープです。 たとえば米国西部リージョンの Standard D2 仮想マシンの予約の割引を希望する場合、その VM が実行されているサブスクリプションを選択します。 その仮想マシンが、加入契約/アカウント内の複数の異なるサブスクリプションで実行される場合は、共有スコープを選択します。 共有スコープによって、サブスクリプションをまたぐ予約割引の適用が可能となります。 スコープは、予約の購入後に変更することができます。 詳しくは、「[Azure での予約インスタンスの管理](billing-manage-reserved-vm-instance.md)」をご覧ください。

予約の割引が適用されるのは、エンタープライズまたは従量課金制のサブスクリプションの種類に関連付けられた仮想マシンまたは SQL Databases のみです。 その他のオファー タイプのサブスクリプションで実行される仮想マシンまたは SQL Database については、予約割引の対象外となります。 エンタープライズ加入契約に関して、Enterprise Dev/Test サブスクリプションには予約の特典が適用されません。

予約が仮想マシンまたは SQL Database の課金にどのように影響するかについて理解を深めるため、[予約割引の適用方法について](billing-understand-vm-reservation-charges.md)のページをご覧ください。

## <a name="what-happens-when-the-reservation-term-expires"></a>予約期間が終了するとどうなりますか

予約期間が満了した時点で、課金割引の有効期限が切れ、従量課金料金が仮想マシン、SQL Database、他のリソースに適用されます。 Azure の予約は自動更新されません。 引き続き課金割引の適用を受けるためには、予約対象サービスの新しい予約を購入する必要があります。

## <a name="next-steps"></a>次の手順

実際に[予約 VM インスタンス](../virtual-machines/windows/prepay-reserved-vm-instances.md)または [SQL Database 予約容量](../sql-database/sql-database-reserved-capacity.md)を購入して仮想マシンのコストを節約してみましょう。

予約について詳しくは、次の記事を参照してください。

- [Azure の予約の管理](billing-manage-reserved-vm-instance.md)
- [予約の割引の適用方法について](billing-understand-vm-reservation-charges.md)
- [従量課金制サブスクリプションの予約使用量について](billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](billing-understand-reserved-instance-usage-ea.md)
- [予約に含まれない Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)
- [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。
