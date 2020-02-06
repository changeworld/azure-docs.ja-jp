---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 89586f932ee358664a2869c87ced72594336b404
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76850522"
---
予約容量によって Premium ソリッド ステート ドライブ (SSD) の使用量を節約し、予約仮想マシン インスタンスと組み合わせることで、VM の総コストを削減できます。 予約割引は、選択した予約範囲内の一致するディスクに自動的に適用されるため、割引を受けるためにマネージド ディスクに予約を割り当てる必要はありません。 割引はディスク使用量に対して 1 時間ごとに適用され、使用されなかった予約容量は引き継がれません。 マネージド ディスク予約割引は、アンマネージド ディスク、Ultra Disk、またはページ BLOB の使用には適用されません。

## <a name="determine-your-storage-needs"></a>必要なストレージを決定する

予約を購入する前に、必要なストレージを決定する必要があります。 現在、ディスク予約は一部の Premium SSD の SKU についてのみ利用できます。 Premium SSD の SKU によって、ディスクのサイズとパフォーマンスが決まります。 必要なストレージを決定する際に、ディスクを合計容量と見なすことはお勧めしません。大容量のディスク (P40 など) の予約を使用し、それを 2 台の小容量のディスク (P30) の支払いに使用することはできません。 予約を購入するときは、SKU ごとの合計ディスク数のみを購入します。

ディスクの予約はディスク SKU ごとに行われるため、予約使用量は、指定されたサイズではなくディスク SKU の単位に基づきます。 たとえば、プロビジョニング容量が 2 TiB の P40 を 1 台予約したが、2 台の P30 ディスクのみを割り当てた場合、2 つの P30 の使用量は P40 の予約に対して計上されず、従量課金制の料金が発生します。



[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>購入に関する考慮事項

ディスク予約の購入を検討する際には、次のベスト プラクティスに従うことをお勧めします。

- 購入する必要がある予約の決定に役立てるために、利用状況の情報を分析します。 プロビジョニング済みまたは使用済みのディスク容量ではなく、ディスク SKU の使用量を追跡するようにしてください。 
- VM 予約と併せてディスク予約を検討します。 最大限の節約のために、VM とディスクの両方の使用量について予約することを強くお勧めします。 適切な VM 予約の決定から始めて、それに従ってディスク予約を評価できます。 一般的に、それぞれのワークロードに標準的な構成があります。たとえば、SQL Server に 2 台の P40 データ ディスクと 1 台の P30 OS ディスクが存在する場合があります。 このようなパターンは、購入する予約の量を決定するのに役立つことがあります。 このアプローチによって、評価プロセスが簡素化され、サブスクリプション、リージョン、その他の観点から、VM とディスクの両方について整合性のとれたプランが得られるようになります。 

## <a name="purchase-restrictions"></a>購入の制限

予約割引は、現在、次のディスクでは利用できません。
- アンマネージド ディスク/ページ BLOB
- Standard SSD または Standard HDD
- P30 より小さい Premium SSD SKU: P1/P2/P3/P4/P6/P10/P15/P20 の Premium SSD SKU の場合、予約を利用できません。
- クラウド: Azure Gov、ドイツ、または中国の各リージョンでは、予約を利用できません。
- 容量制限: まれなケースですが、リージョンのキャパシティが低下しているために、ディスク SKU のサブセットに関する新しい予約の購入が Azure によって制限されます。

## <a name="buy-a-disk-reservation"></a>ディスク予約を購入する

Azure ディスク予約は [Azure portal](https://portal.azure.com/) を使用して購入できます。 予約の支払いは、前払いまたは月払いで行うことができます。 月払いでの購入の詳細については、「[月払いで予約を購入する](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)」を参照してください。

予約容量を購入するには、次の手順に従います。

1. Azure portal の [[購入予約]](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) ブレードに移動します。
1. **[Azure Managed Disks]** を選択して予約を購入します。

    ![disks-reserved-purchase-reservation.png](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png)    

1. 次の表で説明するように、必須フィールドに入力します。

   |フィールド  |[説明]  |
   |---------|---------|
   |**スコープ**   |  予約に関連づけられた課金の特典を使用できるサブスクリプションの数を指定します。 また、特定のサブスクリプションに予約を適用する方法も制御します。 <br/><br/> **[共有]** を選択すると、予約割引は、課金のコンテキスト内にある任意のサブスクリプションの Azure Storage 容量に適用されます。 課金のコンテキストは、Azure に対するサインアップ方法に基づきます。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプションが含まれます。 従量課金制のお客様の場合、共有スコープはアカウント管理者が作成するすべての個別の従量課金制サブスクリプションを含みます。  <br/><br/>  **[単一サブスクリプション]** を選択すると、予約割引は選択したサブスクリプションの Azure Storage 容量に適用されます。 <br/><br/> **[1 つのリソース グループ]** を選択した場合、予約割引は、選択したサブスクリプションおよびそのサブスクリプション内の選択したリソース グループ内の Azure Storage 容量に適用されます。 <br/><br/> 予約を購入した後で、予約のスコープを変更できます。  |
   |**サブスクリプション**  | Azure Storage の予約の支払いに使用するサブスクリプションです。 選択したサブスクリプションの支払方法が、コストの課金で使用されます。 サブスクリプションは、次のいずれかの種類である必要があります。 <br/><br/>  マイクロソフト エンタープライズ契約 (オファー番号:MS-AZR-0017P または MS-AZR-0148P):エンタープライズ サブスクリプションの場合、登録の年額コミットメント残高から料金が差し引かれるか、超過料金として課金されます。 <br/><br/> 従量課金制料金の個別サブスクリプション (オファー番号:MS-AZR-0003P または MS-AZR-0023P):従量課金制料金の個々のサブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。    |
   | **ディスク** | 作成しようとしている SKU。 |
   | **[リージョン]** | 予約が有効になっているリージョン。 |
   | **請求頻度** | アカウントが予約に対して課金される頻度を示します。 オプションには *[月 1 回]* または *[前払い]* があります。 |

    ![premium-ssd-reserved-purchase-selection.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. 予約のパラメーターを選択すると、Azure portal にコストが表示されます。 ポータルには、従量課金制の割引率も表示されます。 **[次へ]** を選択して、 **[購入予約]** ブレードに進みます。

1. **[購入予約]** ペインで、予約の名前を指定し、予約の合計数量を選択できます。 予約の数は、ディスクの数にマップされます。 たとえば、100 台のディスクを予約する場合は **[Quantity]\(数量\)** を 100 に変更します。
1. 予約の総コストを確認します。

    ![premium-ssd-reserved-selecting-sku-total-purchase.png](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

予約を購入すると、予約の条件に一致する既存の Azure Disk ストレージ リソースに自動的に適用されます。 Azure Disk ストレージ リソースをまだ作成していない場合は、予約の条件に一致するリソースを作成するたびに予約が適用されます。 どちらの場合にも、予約の条件は正常な購入の直後に開始されます。

## <a name="exchange-or-refund-a-reservation"></a>予約の交換または返金

一定の制限付きで、予約の交換または返金を行うことができます。

予約を交換または返金するには、Azure portal の予約の詳細に移動します。 **[Exchange or Refund]\(交換または払戻\)** を選択し、指示に従ってサポート リクエストを送信します。 要求が処理されると、Microsoft は要求の完了を確認する電子メールを送信します。

Azure の予約ポリシーの詳細については、「[Azure の予約のセルフサービスによる交換と払戻](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)」を参照してください。

### <a name="exchange-a-reservation"></a>予約を交換する

予約を交換すると、予約の未使用部分に基づいて日割り返金を受けることができます。 その後、新しい Azure ディスク予約の購入価格に返金を適用できます。
交換回数に制限はありません。 さらに、交換に伴う手数料はありません。 購入する新しい予約の値は、元の予約の日割りで計算されたクレジットと同じかそれ以上である必要があります。 Azure ディスク予約の交換は、他の Azure サービスの予約ではなく別の Azure ディスク予約に対してのみ行うことができます。

### <a name="refund-a-reservation"></a>予約を返金する

Azure ディスク予約はいつでも取り消すことができます。 取り消しを行うと、予約の残りの期間に基づいて日割り計算された返金金額から 12% の早期終了料金を差し引いた額が返金されます。 1 年あたりの最大の返金は 5 万ドルです。
予約を取り消すと予約はすぐに終了し、残りの月が Microsoft に返されます。 なお、残りは日割りの払い戻し額から手数料を引いた額が当初の購入形式に戻されます。

## <a name="expiration-of-a-reservation"></a>予約の有効期限

予約の有効期限が切れると、その予約の下で使用している Azure ディスクの容量は、従量課金制の料金で課金されます。 予約は自動更新されません。
予約の有効期限の 30 日前と有効期限当日に通知メールが送信されます。 予約によって提供されるコスト削減を引き続き利用するには、有効期限日より後に更新してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- [Azure の予約とは](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure Disk Storage に予約割引が適用されるしくみについて](../articles/cost-management-billing/reservations/understand-disk-reservations.md)