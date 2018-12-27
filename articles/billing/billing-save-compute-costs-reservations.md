---
title: Azure の予約とは | Microsoft Docs
description: 仮想マシン、SQL データベース、Azure Cosmos DB、その他のリソースのコストを節約するための Azure の予約と価格について説明します。
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
ms.author: cwatson
ms.openlocfilehash: a0fb5eccf14aee07005ab345f1af293f341f9215
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47422951"
---
# <a name="what-are-azure-reservations"></a>Azure の予約とは

Azure の予約は、仮想マシン、SQL Database のコンピューティング能力、Azure Cosmos DB のスループット、または他の Azure リソースを 1 年分または 3 年分前払いすることで、コストを削減するのに役立ちます。 前払いすると、使用するリソースの割り引きを受けることができます。 予約すると、コストを大幅に削減でき、仮想マシン、SQL データベースのコンピューティング、Azure Cosmos DB、または他のリソースで、従量課金制の料金を最大 72% 削減できます。 予約は課金割引を提供するもので、リソースの実行時の状態には影響しません。

予約は [Azure portal](https://aka.ms/reservations) で購入できます。 詳細については、次のトピックを参照してください。

- [Azure Reserved VM Instances による仮想マシンの前払い](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database の容量が予約された SQL Database 計算リソースの前払い](../sql-database/sql-database-reserved-capacity.md)
- [Azure Cosmos DB の容量が予約された Azure Cosmos DB リソースの前払い](../cosmos-db/cosmos-db-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>予約を購入しなければならないのはなぜですか

長期にわたって実行される仮想マシン、Azure Cosmos DB、または SQL Database がある場合、予約を購入することで最も費用効果が高いオプションを選択できます。 たとえば予約なしでサービスの 4 つのインスタンスを常時実行した場合、従量課金制の料金が請求されます。 これらのリソースの予約を購入すると、予約割引がすぐに得られます。 リソースには従量課金制の料金が適用されなくなります。

## <a name="what-charges-does-a-reservation-cover"></a>予約には何の料金が含まれますか

- 予約仮想マシン インスタンス: 予約の対象は仮想マシンのコンピューティング コストのみです。 その他のソフトウェア、ネットワーク、ストレージの料金は含まれません。
- SQL Database の予約された仮想コア: 予約にはコンピューティング コストのみが含まれます。 ライセンスの料金は別途請求されます。
- Azure Cosmos DB の予約容量: 予約には、リソースに対してプロビジョニングされたスループットは含まれますが、ストレージとネットワークの料金は含まれません。 

Windows 仮想マシンと SQL Database については、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)でライセンス コストを賄うことができます。

## <a name="whos-eligible-to-purchase-a-reservation"></a>予約を購入する資格があるのはだれですか

予約は、サブスクリプションの種類が次のいずれかに該当する Azure ユーザーが購入できます。

- エンタープライズ契約サブスクリプション オファー タイプ (MS-AZR-0017P)。
- [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)サブスクリプション オファー タイプ (MS-AZR-003P)。 予約を購入するには、サブスクリプションの "所有者" ロールが必要です。
- クラウド ソリューション プロバイダー (CSP) パートナーは、Azure portal または[パートナー センター](https://docs.microsoft.com/partner-center/azure-reservations)を使用して Azure の予約を購入できます。

予約割引が適用されるのは、エンタープライズ、従量課金制、または CSP のサブスクリプションの種類に関連付けられたリソースのみです。

## <a name="how-is-a-reservation-billed"></a>予約はどのように課金されますか

予約は、サブスクリプションに関連付けられている支払い方法に対して課金されます。 エンタープライズ サブスクリプションの場合、予約コストは年額コミットメントの残高から差し引かれます。 年額コミットメントの残高で予約のコストを賄えない場合は、超過分が課金されます。 従量課金制サブスクリプションをご利用の場合、アカウントに登録されているクレジット カードに直接課金されます。 請求書による課金の場合、次回の請求書に料金が表示されます。

## <a name="how-is-the-reservation-discount-applied"></a>予約割引の適用方法

予約割引は、予約購入時に選択した属性と一致するリソースの使用に適用されます。 代表的な属性の 1 つは、一致する VM、SQL Database、Azure Cosmos DB、他のリソースが実行されるスコープです。 たとえば米国西部リージョンの Standard D2 仮想マシンの予約の割引を希望する場合、その VM が実行されているサブスクリプションを選択します。 その仮想マシンが、加入契約/アカウント内の複数の異なるサブスクリプションで実行される場合は、共有スコープを選択します。 共有スコープによって、サブスクリプションをまたぐ予約割引の適用が可能となります。 スコープは、予約の購入後に変更することができます。 詳しくは、[Azure の予約の管理](billing-manage-reserved-vm-instance.md)に関する記事をご覧ください。

予約割引が適用されるのは、エンタープライズ、従量課金制、または CSP のサブスクリプションの種類に関連付けられたリソースのみです。 他のプランの種類のサブスクリプションで実行されるリソースは、予約割引の対象外です。 エンタープライズ加入契約に関して、Enterprise Dev/Test サブスクリプションには予約の特典が適用されません。

予約が課金に与える影響をさらに理解するには、次のトピックを参照してください。

-  [Azure Reserved VM Instances の割引を理解する](billing-understand-vm-reservation-charges.md)
- [Azure の予約割引を理解する](billing-understand-vm-reservation-charges.md)
- [Azure Cosmos DB の予約割引を理解する](billing-understand-cosmosdb-reservation-charges.md)
- [Azure 予約割引と SUSE の使用を理解する](billing-understand-suse-reservation-charges.md)

## <a name="what-happens-when-the-reservation-term-expires"></a>予約期間が終了するとどうなりますか

予約期間が満了した時点で、課金割引の有効期限が切れ、従量課金料金が仮想マシン、SQL Database、Azure Cosmos DB、または他のリソースに適用されます。 Azure の予約は自動更新されません。 引き続き課金割引の適用を受けるためには、予約対象のサービスとソフトウェア向けの新しい予約を購入する必要があります。

## <a name="discount-applies-to-different-sizes-with-instance-size-flexibility"></a>さまざまなサイズに適用され、インスタンス サイズの柔軟性を提供する割引

予約を購入するときに、同じサイズのグループ内にある属性を持つ他のインスタンスに割引を適用できます。 割引範囲の柔軟性は、予約のタイプと、予約を購入するときに選択する属性によって異なります。

- 予約された VM インスタンス: 予約を購入するときに、**[最適化の対象**: **インスタンス サイズの柔軟性]** を選択した場合、割引範囲は選択する仮想マシンのサイズによって決まります。 予約は同じサイズの系列グループの仮想マシン (VM) のサイズに適用できます。 詳細については、「[Reserved VM Instances での仮想マシン サイズの柔軟性](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)」を参照してください。
- SUSE Linux Enterprise ソフトウェア プラン: 割引範囲は、SUSE ソフトウェアを実行する仮想マシンの vCPU によって異なります。 詳細については、[SUSE Linux Enterprise ソフトウェアのプラン割引が適用されるしくみ](billing-understand-suse-reservation-charges.md)に関するページを参照してください。
- SQL Database の予約容量: 割引範囲は、選択したパフォーマンス層によって異なります。 詳細については、「[Azure の予約割引の適用方法](billing-understand-reservation-charges.md)」を参照してください。
- Azure Cosmos DB の予約容量: 割引範囲は、プロビジョニングされたスループットによって異なります。 詳細については、「[Azure Cosmos DB の予約割引の適用方法](billing-understand-cosmosdb-reservation-charges.md)」を参照してください。

## <a name="next-steps"></a>次の手順

実際に、[予約 VM インスタンス](../virtual-machines/windows/prepay-reserved-vm-instances.md)、[SQL Database 予約容量](../sql-database/sql-database-reserved-capacity.md)、または [Azure Cosmos DB 予約容量](../cosmos-db/cosmos-db-reserved-capacity.md)を購入して、仮想マシンのコストを節約してみましょう。

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約の管理](billing-manage-reserved-vm-instance.md)
- [従量課金制サブスクリプションの予約使用量について](billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](billing-understand-reserved-instance-usage-ea.md)
- [予約に含まれない Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)
- [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。
