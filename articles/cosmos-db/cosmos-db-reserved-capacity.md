---
title: 前払いで Azure Cosmos DB リソースのコストを削減する | Microsoft Docs
description: Azure Cosmos DB の予約容量を購入して計算コストを節約する方法について説明します。
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 2291b2429e6c5c25e051c8f3eca30e1cc3f64611
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247331"
---
# <a name="prepay-for-azure-cosmos-db-resources-with-reserved-capacity"></a>予約容量で Azure Cosmos DB リソースを前払いする

Azure Cosmos DB の予約容量は、リソースの料金 1 年分または 3 年分を前払いすることで経費を節減できるサービスです。 Azure Cosmos DB の予約容量では、Cosmos DB リソース用にプロビジョニングされたデータベースやコンテナー (テーブル/コレクション/グラフ) などのスループットが割引価格で提供されます。 Azure Cosmos DB では、Cosmos DB にかかる費用を大幅に削減できます。1 年分または 3 年分の前払いにより、最大で 65 パーセントの割引が可能になります。 割引が適用されても、Cosmos DB リソースのランタイム状態は維持されます。

Azure Cosmos DB の予約容量には、リソースに対してプロビジョニングされたスループットは含まれますが、ストレージとネットワークの料金は含まれません。 予約を購入するとすぐに、予約の属性に一致するスループット料金は従量課金制で課金されなくなります。 予約について詳しくは、「[Azure の予約とは](../billing/billing-save-compute-costs-reservations.md)」をご覧ください。 

Azure Cosmos DB の予約容量は、[Azure portal](https://portal.azure.com) から購入できます。 予約容量を購入するには:

* 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制サブスクリプションで所有者ロールである必要があります。  
* エンタープライズ サブスクリプションの場合、[EA ポータル](https://ea.azure.com/)で Azure の予約購入を有効にする必要があります。  
* クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理者エージェントまたはセールス エージェントのみが Azure Cosmos DB の予約容量を購入できます。

## <a name="determine-the-required-throughput-before-purchase"></a>購入する前に必要なスループットを決定する

予約のサイズは、既存の、または間もなくデプロイする Azure Cosmos DB リソース (たとえば、データベースまたはコンテナー - コレクション、テーブル、グラフ) で使用されるスループットの合計量に基づく必要があります。 必要なスループットは次の方法で決定できます。

* すべてのリージョンの Azure Cosmos DB アカウント、データベース、コレクション全体でプロビジョニングされている合計スループットの履歴データを取得します。 たとえば、`https://account.azure.com` から毎日の使用量明細をダウンロードすることにより、毎日の平均のプロビジョニング済みスループットを評価できます。

* また、Enterprise Agreement (EA) の場合は、使用状況ファイルをダウンロードし、使用状況ファイルの **[追加情報]** セクションで **[サービスの種類]** の値を参照すると、Azure Cosmos DB のスループットの詳細を取得できます。

* 今後 1 年間または 3 年間実行することが予想される Azure Cosmos DB アカウントでの全ワークロードに対する平均スループットを合計して、その数量を予約に使用することもできます。

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB の予約容量を購入する

1. [Azure Portal](https://portal.azure.com) にサインインします。  

2. **[すべてのサービス]** > **[予約]** > **[追加]** を選択します。  

3. **[製品の種類を選択してください]** ウィンドウで **[Azure Cosmos DB]** を選択し、**[選択]** をクリックして新しい予約を購入します。  

4. 次の表で説明するように、必須フィールドに入力します。

   ![予約容量フォームに入力する](./media/cosmos-db-reserved-capacity/fill_reserved_capacity_form.png) 

   |フィールド  |説明  |
   |---------|---------|
   |Name   |    予約の名前です。 このフィールドには、`CosmosDB_Reservation_<timeStamp>` が自動的に設定されます。 予約の作成中に別の名前を指定したり、予約の作成後に名前を変更したりできます。      |
   |サブスクリプション  |   Azure Cosmos DB の予約容量の支払いに使用するサブスクリプションです。 選択したサブスクリプションの支払方法が、初期コストの課金で使用されます。 サブスクリプションの種類は、次のいずれかである必要があります。 <br/><br/>  [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (オファー番号: MS-AZR-0017P) - エンタープライズ サブスクリプションの場合、登録の年額コミットメント残高から料金が差し引かれるか、超過料金として課金されます。 <br/><br/> [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) (オファー番号: MS-AZR-0003P) - 従量課金制サブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。    |
   |Scope (スコープ)   |   予約のスコープにより、予約に伴う料金の特典を利用できるサブスクリプションの数と、特定のサブスクリプションに対する予約の適用方法が制限されます。 予約のスコープは、単一サブスクリプションまたは共有サブスクリプションです。 以下を選択した場合:   <br/><br/>  **単一サブスクリプション** - 予約割引は選択したサブスクリプションの Azure Cosmos DB インスタンスに適用されます。 <br/><br/>  **共有** – 予約割引は、課金のコンテキスト内にある任意のサブスクリプションで実行されている Azure Cosmos DB インスタンスに適用されます。 課金のコンテキストは、Azure に対するサインアップ方法に基づいて決まります。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプション (開発/テスト サブスクリプションを除きます) が含まれます。 従量課金制のお客様の場合、共有スコープは、アカウント管理者が作成するすべての従量課金制サブスクリプションです。  <br/><br/> 予約容量を購入した後で、予約のスコープを変更できます。  |
   |予約容量の種類   |  予約容量の種類は、要求ユニットに関してプロビジョニングされているスループットです。|
   |予約容量ユニット  |      予約するスループットの量です。 この値は、リージョンごとのすべての Cosmos DB リソース (たとえば、データベースやコンテナー) に必要なスループットを特定した後、Cosmos DB データベースに関連付けるリージョンの数をそれに掛けることによって、計算できます。  <br/> たとえば、5 つのリージョンがあり、すべてのリージョンが 100万 RU/秒である場合は、予約容量の購入に 500万 RU/秒を選択します。    |
   |用語  |   1 年間または 3 年間。   |

5. **[コスト]** セクションで割引率と予約の価格を確認します。 この予約価格は、すべてのリージョンでスループットがプロビジョニングされている Azure Cosmos DB リソースに適用されます。  

6. **[購入]** を選択します。 購入が成功すると、次のような画面が表示されます。 

   ![予約容量フォームに入力する](./media/cosmos-db-reserved-capacity/reserved_capacity_successful.png) 

予約を購入すると、予約の条件に一致する既存の Azure Cosmos DB リソースにすぐに適用されます。 既存の Azure Cosmos DB リソースを持っていない場合は、予約の条件に一致する新しい Cosmos DB インスタンスをデプロイすると、予約が適用されます。 どちらの場合にも、予約の期間は正常な購入の直後に開始されます。 

予約の期限が切れると、Azure Cosmos DB インスタンスは引き続き実行し、正規の従量課金制の料金で課金されます。

## <a name="next-steps"></a>次の手順

予約割引は、予約スコープと属性に一致する Azure Cosmos DB リソースに対して自動的に適用されます。 予約のスコープは、Azure portal、PowerShell、CLI、または API で更新できます。

*  Azure Cosmos DB に予約容量割引が適用される方法については、「[Azure の予約割引の概要](../billing/billing-understand-cosmosdb-reservation-charges.md)」をご覧ください

* Azure の予約の詳細については、次の記事を参照してください。

   * [Azure の予約とは](../billing/billing-save-compute-costs-reservations.md)  
   * [Azure の予約を管理する](../billing/billing-manage-reserved-vm-instance.md)  
   * [エンタープライズ加入契約の予約使用量について](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [従量課金制サブスクリプションの予約使用量について](../billing/billing-understand-reserved-instance-usage.md)
   * [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。

