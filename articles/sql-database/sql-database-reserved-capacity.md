---
title: 前払いで Azure SQL Database 仮想コアのコストを削減する | Microsoft Docs
description: Azure SQL Database の予約容量を購入して計算コストを節約する方法について説明します。
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.devlang: na
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: carlrab
ms.openlocfilehash: 1d1cad4b614eb35be9235a721368db8048be050a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39632794"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Azure SQL Database の予約容量を使用した SQL Database 計算リソースの前払い

Azure SQL Database の計算リソースを前払いすることで、従量課金制よりも Azure SQL Database に関するコストを節約できます。 Azure SQL Database の予約容量を使用すると、SQL Database を 1 年間または 3 年間分を前払いすることで計算コストを大幅に引き下げることができます。 SQL Database の予約容量を購入するには、Azure リージョン、デプロイの種類、サービス、および期間を指定する必要があります。 

SQL Database インスタンスに予約を割り当てる必要はありません。 既に実行している SQL Database インスタンスまたは新しくデプロイされた SQL Database インスタンスには、この特典が自動的に適用されます。 予約を購入すると、SQL Database インスタンスの計算コストを 1 年間または 3 年間分前払いすることになります。 予約を購入するとすぐに、予約の属性に一致する SQL Database のコンピューティング料金は従量課金制で課金されなくなります。 予約には、SQL Database インスタンスに関連するソフトウェア、ネットワーク、またはストレージの料金は含まれません。 予約期間が満了した時点で、課金特典の有効期限は切れ、従量課金料金が SQL データベースに適用されます。 予約は自動更新されません。 価格の詳細については、[SQL Database の予約容量オファー](https://azure.microsoft.com/pricing/details/sql-database/managed/)に関するページを参照してください。

Azure SQL Database の予約容量は、[Azure portal](https://portal.azure.com) で購入できます。 SQL Database の予約容量を購入するには:
- 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制サブスクリプションで所有者ロールである必要があります。
- エンタープライズ サブスクリプションの場合、[EA ポータル](https://ea.azure.com)で Azure の予約購入を有効にする必要があります。
-  クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理者エージェントまたはセールス エージェントのみが SQL Database の予約容量を購入できます。

エンタープライズ ユーザーと従量課金制ユーザーが予約購入に対してどのように課金されるかについては、[よくある質問](#frequently-asked-questions)のセクションを参照してください。

## <a name="determine-the-right-sql-size-before-purchase"></a>購入する前に適切な SQL サイズを決定する

特定のリージョン内で、同じパフォーマンス階層とハードウェア世代を使用する、既存またはすぐにデプロイされる予定の 単一の SQL データベースやエラスティック プールで使用される計算の合計に基づいて予約のサイズを決める必要があります。 

たとえば、1 つの汎用の Gen5 - 16 仮想コア エラスティック プールと、2 つのビジネス クリティカルな Gen5 - 4 仮想コア単一データベースを実行しているとします。 さらに、来月には汎用の Gen5 - 16 仮想コア エラスティック プールを 1 つとビジネス クリティカルな Gen5 - 32 仮想コア エラスティック プールを 1 つ追加する予定だとします。 また、少なくとも 1 年間はこれらのリソースが必要になることがわかっているとします。 この場合、SQL Database Single/Elastic Pool General Purpose - Compute Gen5 用に 32 (2x16) 仮想コアを 1 年間分と、SQL Database Single/Elastic Pool Business Critical - Compute Gen5 用に 40 (2x4 + 32) 仮想コアを 1 年間分を購入する必要があります。

## <a name="buy-sql-database-reserved-capacity"></a>SQL Database の予約容量の購入

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]** > **[予約]** を選択します。
3. **[追加]** を選択し、[製品の種類を選択してください] ウィンドウで **[SQL Database]** を選択して、SQL Database の新しい予約を購入します。
4. 必須フィールドに必要事項を入力します。 選択した属性と一致する既存または新規の単一データベースまたはエラスティック プールが、予約容量の割引を受けられます。 割引を受ける SQL Database インスタンスの実際の数は、選択したスコープと数量によって変わります。

   ![SQL Database の予約容量の購入を送信する前のスクリーンショット](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | フィールド      | 説明|
    |:------------|:--------------|
    |Name        |この予約の名前。| 
    |サブスクリプション|SQL Database の予約容量の予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、SQL Database の予約容量の予約の前払いコストが課金されます。 サブスクリプションの種類は、Enterprise Agreement (オファー番号: MS-AZR-0017P) または従量課金制 (オファー番号: MS-AZR-0003P) である必要があります。 エンタープライズ サブスクリプションの場合、登録の年額コミットメント残高から料金が差し引かれるか、超過料金として課金されます。 従量課金制サブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。|    
    |Scope (スコープ)       |1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を仮想コアの予約のスコープにすることができます。 以下を選択した場合: <ul><li>1 つのサブスクリプション - 仮想コアの予約割引はこのサブスクリプションの SQL Database インスタンスに適用されます。 </li><li>共有 - 仮想コアの予約割引は、課金のコンテキスト内にある任意のサブスクリプションで実行されている SQL Database インスタンスに適用されます。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプション (開発/テスト サブスクリプションを除きます) が含まれます。 従量課金制のお客様の場合、共有スコープは、アカウント管理者が作成するすべての従量課金制サブスクリプションです。</li></ul>|
    |リージョン      |SQL Database 予約容量の予約で充当されない Azure リージョン。|    
    |デプロイの種類|予約を購入する SQL デプロイの種類。|
    |サービス レベル|SQL Database インスタンスのサービス レベル
    |用語        |1 年間または 3 年間。|
    |数量    |SQL Database の予約容量の予約内で購入されるインスタンス数。 数量は、課金の割引を受けられる実行中の SQL Database インスタンス数です。 たとえば、米国東部で SQL Database インスタンスを 10 個実行している場合、実行中のすべてのマシンのメリットを最大限に利用するには、数量を 10 と指定します。 |

5. **[コスト]** セクションで、SQL Database の予約容量の予約コストを確認します。
6. **[購入]** を選択します。
7. **[この予約を表示する]** を選択すると、購入の状態が表示されます。

## <a name="next-steps"></a>次の手順 
仮想コアの予約割引は、SQL Database の予約容量の予約スコープと属性に一致する複数の SQL Database インスタンスに自動的に適用されます。 SQL Database の予約容量の予約スコープは、[Azure portal](https://portal.azure.com)、PowerShell、CLI、または API で更新できます。 

SQL Database の予約容量の予約を管理する方法については、[SQL Database の予約容量の管理](../billing/billing-manage-reserved-vm-instance.md)に関するページを参照してください。

Azure の予約の詳細については、以下の記事を参照してください。

- [Azure の予約とは](../billing/billing-save-compute-costs-reservations.md)
- [Azure の予約の管理](../billing/billing-manage-reserved-vm-instance.md)
- [従量課金制サブスクリプションの予約使用量について](../billing/billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](../billing/billing-understand-reserved-instance-usage-ea.md)
- [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。

