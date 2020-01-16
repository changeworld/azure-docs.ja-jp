---
title: Azure の予約とは
description: 仮想マシン、SQL データベース、Azure Cosmos DB、その他のリソースのコストを節約するための Azure の予約と価格について説明します。
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: banders
ms.openlocfilehash: 2e8c477aea7a11eefa606ab9cd084ca3ed9f400f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75986783"
---
# <a name="what-are-azure-reservations"></a>Azure の予約とは

Azure の予約は、仮想マシン、Azure BLOB ストレージまたは Azure Data Lake Storage Gen2、SQL Database のコンピューティング容量、Azure Cosmos DB のスループット、または他の Azure リソースの 1 年プランまたは 3 年プランにコミットすることで、コストを削減するのに役立ちます。 コミットすることで、使用するリソースの割り引きを受けることができます。 予約すると、コストを大幅に削減でき、従量課金制の料金を最大 72% 削減できます。 予約は課金割引を提供するもので、リソースの実行時の状態には影響しません。

予約の支払いは、前払いとすることも、月払いとすることもできます。 前払いも月払いも予約の総コストは同じであり、月払いを選んだとしても追加料金を支払う必要はありません。 毎月の支払いは、サードパーティ製品ではなく、Azure の予約で利用できます。

予約は [Azure portal](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) で購入できます。

## <a name="why-buy-a-reservation"></a>予約を購入する理由

仮想マシン、BLOB ストレージ データ、Azure Cosmos DB、または SQL Database が大量の容量またはスループットを使用する場合、または長期にわたって実行される場合、予約を購入することで最も費用効果の高いオプションを選択できます。 たとえば予約なしでサービスの 4 つのインスタンスを常時実行した場合、従量課金制の料金が請求されます。 これらのリソースの予約を購入すると、予約割引がすぐに得られます。 リソースには従量課金制の料金が適用されなくなります。

## <a name="charges-covered-by-reservation"></a>予約の対象となる料金

サービス プラン:

- **予約仮想マシン インスタンス** - 予約の対象は仮想マシンのコンピューティング コストのみです。 その他のソフトウェア、ネットワーク、ストレージの料金は含まれません。
- **Azure Storage の予約容量** - 予約の対象となるのは、BLOB ストレージまたは Azure Data Lake Gen2 ストレージの標準ストレージ アカウントのストレージ容量です。 帯域幅またはトランザクション レートは予約の対象外です。
- **Azure Cosmos DB の予約容量** - 予約は、リソース用にプロビジョニングされたスループットが対象になります。 ストレージとネットワーク料金は対象外です。
- **SQL Database の予約された仮想コア** - 予約にはコンピューティング コストのみが含まれます。 ライセンスの料金は別途請求されます。
- **SQL Data Warehouse** - 予約には cDWU の使用が含まれます。 SQL Data Warehouse の使用に関連するストレージまたはネットワークの料金は含まれません。
- **App Service スタンプ料金** - 予約にはスタンプの使用が含まれます。 worker には適用されないので、スタンプに関連する他のすべてのリソースには別途課金されます。
- Azure Database for MySQL
- Azure Database for PostgreSQL
- Azure Database for MariaDB
- Azure Data Explorer
- Premium SSD マネージド ディスク


Windows 仮想マシンと SQL Database については、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)でライセンス コストを賄うことができます。

## <a name="whos-eligible-to-purchase-a-reservation"></a>予約を購入する資格があるのはだれですか

プランを購入するには、Enterprise のサブスクリプション所有者ロール (MS-AZR-0017P または MS-AZR-0148P)、従量課金制サブスクリプション (MS-AZR-0003P または MS-AZR-0023P)、Microsoft 顧客契約サブスクリプションのいずれかが必要です。 クラウド ソリューション プロバイダーは、Azure portal または [パートナー センター](/partner-center/azure-reservations) を使用して Azure の予約を購入できます。

Enterprise Agreement (EA) のお客様は、EA Portal 内で、 **[予約インスタンスを追加します]** オプションを無効にして、購入を EA 管理者に限定することができます。 EA 管理者は、予約を購入するために、少なくとも 1 つの EA サブスクリプションのサブスクリプション所有者である必要があります。 このオプションは、さまざまなコスト センター用に予約を購入する集中管理チームを必要とする企業に役立ちます。 購入後、集中管理チームは、予約にコスト センター所有者を追加できます。 これにより、所有者は、そのサブスクリプションに予約のスコープを設定できます。 集中管理チームは、予約が購入されたサブスクリプション所有者のアクセス権を持つ必要はありません。

予約割引が適用されるのは、エンタープライズ、クラウド ソリューション プロバイダー (CSP)、Microsoft 顧客契約、および従量課金制の個人プランを通じて購入したサブスクリプションに関連付けられたリソースのみです。

## <a name="scope-reservations"></a>予約のスコープ設定

予約のスコープをサブスクリプションまたはリソース グループに設定することができます。 予約のスコープを設定すると、予約の割引が適用される場所が選択されます。 予約のスコープをリソース グループに設定すると、予約割引はサブスクリプション全体ではなく、リソース グループにのみ適用されます。

### <a name="reservation-scoping-options"></a>予約スコープのオプション

リソース グループのスコープ設定には、ニーズに応じて予約をスコープ設定する次の 3 つのオプションがあります。

- **単一のリソース グループのスコープ** - 選択されたリソース グループ内の一致するリソースにのみ、予約割引を適用します。
- **単一サブスクリプション** - 選択されたサブスクリプションの一致するリソースに予約割引を適用します。
- **共有スコープ** - 課金コンテキスト内にある有効なサブスクリプションの一致するリソースに予約割引を適用します。 マイクロソフト エンタープライズ契約のお客様の場合、課金コンテキストは登録です。 Microsoft 顧客契約のお客様の場合、課金スコープは課金プロファイルです。 従量課金制料金の個々のサブスクリプションの場合、課金スコープはアカウント管理者によって作成されるすべての有効なサブスクリプションです。

使用量に予約割引を適用しつつ、Azure では次の順序で予約が処理されます。

1. リソース グループにスコープが設定されている予約
2. 単一スコープの予約
3. 共有スコープの予約

予約にスコープを設定した方法に応じて、1 つのリソース グループが複数の予約から予約割引を受けることができます。

### <a name="scope-a-reservation-to-a-resource-group"></a>予約のスコープをリソース グループに設定する

リソース グループへの予約のスコープの設定は、予約の購入時に設定することも、購入後に設定することもできます。 予約のスコープをリソース グループに設定するには、サブスクリプションの所有者である必要があります。

スコープを設定するには、Azure portal の [[購入予約]](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) ページにアクセスします。 購入する予約の種類を選択します。 **[Select the product that you want to purchase]\(購入する製品を選択してください\)** 選択フォームで、スコープの値を単一リソース グループに変更します。 次に、リソース グループを選択します。

![VM 予約購入の選択を示す例](./media/save-compute-costs-reservations/select-product-to-purchase.png)

仮想マシン予約のリソース グループの購入に関するレコメンデーションが表示されます。 過去 30 日間の利用状況分析に基づいたレコメンデーションが計算されます。 予約インスタンスを使用してリソースを実行するコストが、従量課金制料金でリソースを実行する場合のコストよりも安い場合は、購入のレコメンデーションが作成されます。 予約購入のレコメンデーションの詳細については、「[Get Reserved Instance purchase recommendations based on usage pattern](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations)」 (使用パターンに基づく予約インスタンス購入のレコメンデーションを取得する) を参照してください。

スコープは、予約の購入後にいつでも更新できます。 これを行うには、予約にアクセスし、 **[構成]** をクリックして予約のスコープを再設定します。 予約のスコープの再設定は、商用トランザクションではありません。 予約期間は変更されません。 スコープの更新の詳細については、[予約購入後にスコープを更新する](manage-reserved-vm-instance.md#change-the-reservation-scope)方法に関するセクションを参照してください。

![予約のスコープの変更を示す例](./media/save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>予約の使用状況を監視および最適化する

予約の使用状況は、Azure portal、API、または使用状況データを使用するなど、複数の方法で監視できます。 アクセスできるすべての予約を表示するには、Azure portal の **[予約]** にアクセスします。 予約グリッドには、予約の最後に記録された使用率が表示されます。 予約をクリックすると、予約の長期的な使用率が表示されます。

マイクロソフト エンタープライズ契約または Microsoft 顧客契約のお客様の場合は、[API](reservation-apis.md#see-reservation-usage) を使用して、[使用状況データ](understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks)から予約使用率を取得することもできます。

リソース グループにスコープ設定された予約の使用率が低いことがわかった場合は、予約のスコープを単一サブスクリプションに更新するか、課金コンテキストで共有することができます。 予約を分割し、分割した予約を別々のリソース グループに適用することもできます。

### <a name="other-considerations"></a>その他の考慮事項

リソース グループに一致するリソースがない場合、予約の使用率が低下します。 予約が、使用率の低い別のリソース グループやサブスクリプションに自動的に適用されることはありません。

あるサブスクリプションから別のサブスクリプションにリソース グループを移動しても、予約スコープは自動的に更新されません。 リソース グループを削除した場合、スコープは更新されません。 [予約のスコープを再設定する](manage-reserved-vm-instance.md#change-the-reservation-scope)必要があります。 そうしないと、予約の使用率が低下します。

## <a name="discounted-subscription-and-offer-types"></a>割引サブスクリプションとオファーの種類

予約割引は、次の有効なサブスクリプションとオファーの種類に適用されます。

- マイクロソフト エンタープライズ契約 (オファー番号:MS-AZR-0017P または MS-AZR-0148P)
- Microsoft 顧客契約のサブスクリプション
- 従量課金制の個人プラン (プラン番号:MS-AZR-0003P または MS-AZR-0023P)
- CSP サブスクリプション

他のプランの種類のサブスクリプションで実行されるリソースは、予約割引の対象外です。

## <a name="how-is-a-reservation-billed"></a>予約はどのように課金されますか

予約は、サブスクリプションに関連付けられている支払い方法に対して課金されます。 予約コストは年額コミットメントの残高から差し引かれます (可能な場合)。 年額コミットメントの残高で予約のコストを賄えない場合は、超過分が課金されます。 従量課金制の個人プランのサブスクリプションをご利用の場合、前払い購入では、アカウントに登録されているクレジット カードに直接課金されます。 月支払いの場合は請求書に表示され、クレジット カードに毎月請求されます。 請求書による課金の場合、次回の請求書に料金が表示されます。

## <a name="how-reservation-discount-is-applied"></a>予約割引の適用方法

予約割引は、予約購入時に選択した属性と一致するリソースの使用に適用されます。 代表的な属性の 1 つは、一致する VM、SQL Database、Azure Cosmos DB、他のリソースが実行されるスコープです。 たとえば米国西部リージョンの Standard D2 仮想マシンの予約割引を希望する場合、その VM が実行されているサブスクリプションを選択します。

予約割引は、"*使用しないと失われます*"。 ある時間、一致するリソースがない場合は、その時間に対する予約量は失われます。 未使用の予約済み時間を繰り越すことはできません。

リソースをシャットダウンすると、予約割引は、指定されたスコープ内の別の一致するリソースに自動的に適用されます。 指定したスコープ内に一致するリソースが見つからない場合、予約済み時間は "*失われます*"。

たとえば、後でリソースを作成して、使用率が低い一致する予約がある場合があります。 予約割引は、新しい一致するリソースに自動的に適用されます。

その仮想マシンが、加入契約/アカウント内の複数の異なるサブスクリプションで実行される場合は、共有スコープを選択します。 共有スコープによって、サブスクリプションをまたぐ予約割引の適用が可能となります。 スコープは、予約の購入後に変更することができます。 詳しくは、[Azure の予約の管理](manage-reserved-vm-instance.md)に関する記事をご覧ください。

予約割引が適用されるのは、エンタープライズ、Microsoft 顧客契約、CSP、または従量課金制のサブスクリプションに関連付けられたリソースのみです。 他のプランの種類のサブスクリプションで実行されるリソースは、予約割引の対象外です。

## <a name="when-the-reservation-term-expires"></a>予約期間が終了したとき

予約期間が満了した時点で、割引の有効期限は切れ、従量課金料金がリソースに適用されます。 既定では、予約の自動更新は設定されていません。 予約の自動更新を有効にするには、更新設定でオプションを選択します。 自動更新を有効にすると、既存の予約の有効期限が切れたときに置換予約が購入されます。 既定では、置換予約の属性は期限切れになる予約の属性と同じです。更新設定では、必要に応じて請求頻度、期間、または数量を変更することもできます。 予約に対する所有者アクセス権を持つユーザーと、課金に使用されるサブスクリプションは、更新を設定できます。  

## <a name="discount-applies-to-different-sizes"></a>さまざまなサイズに割引が適用される

予約を購入するときに、同じサイズのグループ内にある属性を持つ他のインスタンスに割引を適用できます。 この機能は、インスタンス サイズの柔軟性と呼ばれます。 割引範囲の柔軟性は、予約のタイプと、予約を購入するときに選択する属性によって異なります。

サービス プラン:

- 予約 VM インスタンス:予約を購入し、 **[インスタンス サイズの柔軟性に対する最適化]** を選択すると、割引範囲は選択する仮想マシンのサイズによって決まります。 予約は同じサイズの系列グループの仮想マシン (VM) のサイズに適用できます。 詳細については、「[Reserved VM Instances での仮想マシン サイズの柔軟性](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)」を参照してください。
- Azure Storage の予約容量: Azure Storage の標準アカウントの予約容量を、1 か月あたり 100 TiB または 1 PiB 単位で購入できます。 Azure Storage の予約容量は、任意のアクセス層 (ホット、クール、アーカイブ) および任意のレプリケーション オプション (LRS、GRS、ZRS) のすべてのリージョンで利用できます。
- SQL Database 予約容量:割引範囲は、選択したパフォーマンス層によって異なります。 詳細については、「[Azure の予約割引の適用方法](understand-reservation-charges.md)」を参照してください。
- Azure Cosmos DB 予約容量:割引範囲は、プロビジョニングされたスループットによって異なります。 詳細については、「[Azure Cosmos DB の予約割引の適用方法](understand-cosmosdb-reservation-charges.md)」を参照してください。

## <a name="reservation-notifications"></a>予約通知

Azure サブスクリプションの支払い方法によっては、組織内の次のユーザーに予約通知がメールで送られます。 次のようなさまざまなイベントに対して通知が送信されます。

- Purchase
- 今後の予約の有効期限
- Expiry
- 更新
- キャンセル
- スコープの変更

EA サブスクリプションをお持ちのお客様の場合:
- 購入通知は、購入者と EA 通知の連絡先に送信されます。
- その他の予約ライフサイクル通知は、EA 通知の連絡先にのみ送信されます。
- RBAC (IAM) アクセス許可を使用して予約に追加されたユーザーは、メール通知を受け取りません。

個々のサブスクリプションをお持ちのお客様の場合:
- 購入者は、購入通知を受け取ります。
- 購入時に、サブスクリプション課金アカウントの所有者が購入通知を受け取ります。
- アカウント所有者は、他のすべての通知を受け取ります。


## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- Azure の予約の詳細については、次の記事を参照してください。
    - [Azure の予約の管理](manage-reserved-vm-instance.md)
    - [従量課金制のサブスクリプションの予約使用量について](understand-reserved-instance-usage.md)
    - [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)
    - [予約に含まれない Windows ソフトウェアのコスト](reserved-instance-windows-software-costs.md)
    - [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](/partner-center/azure-reservations)

- サービス プランの予約の詳細については、次の記事を参照してください。
    - [Azure Reserved VM Instances による仮想マシン](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB の容量が予約された Azure Cosmos DB リソース](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Azure SQL Database の予約容量を使用した SQL Database 計算リソースの前払い](../../sql-database/sql-database-reserved-capacity.md) ソフトウェア プランの予約の詳細については、次の記事を参照してください。
    - [Azure の予約からの Red Hat ソフトウェア プラン](../../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Azure の予約からの SUSE ソフトウェア プラン](../../virtual-machines/linux/prepay-suse-software-charges.md)
