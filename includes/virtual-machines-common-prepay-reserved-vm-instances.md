---
author: yashesvi
ms.author: yashar
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 08-07-2018
ms.openlocfilehash: 19a153a5cdc9d5f878494984313baebd12dbcbb5
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39631165"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Azure Reserved VM Instances による仮想マシンの前払い

Azure Reserved Virtual Machine (VM) Instances を使って、仮想マシンの料金を前払いして、コストを節約しましょう。 詳しくは、[Azure Reserved VM Instances のオファー](https://azure.microsoft.com/pricing/reserved-vm-instances/)に関するページをご覧ください。

予約 VM インスタンスは [Azure Portal](https://portal.azure.com) から購入できます。 インスタンスを購入する場合、次のことが必要です。

- 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制サブスクリプションで所有者ロールである必要があります。
- エンタープライズ サブスクリプションの場合、[EA ポータル](https://ea.azure.com)で予約購入を有効にする必要があります。
- クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理エージェントまたはセールス エーエージェントのみが予約購入できます。

## <a name="determine-the-right-vm-size-before-purchase"></a>購入する前に適切な VM サイズを決定する

使用状況データの測定サブカテゴリ フィールドと製品フィールドでは、Premium Storage を使用する VM サイズと Premium Storage を使用しない VM サイズが区別されません。これらのフィールドを使用して予約購入用の VM サイズを判断すると、誤った予約購入をしてしまい、予約の割引を得られない可能性があります。 次の方法のいずれかを使用して、予約購入用の適切な VM サイズを判断します。

- 使用状況ファイルの AdditionalInfo フィールドまたは使用状況 API を参照して、予約購入の適切な VM サイズを確認します。 測定サブカテゴリ フィールドと製品フィールドは S バージョンと S 以外のバージョンの VM を区別しないので、これらのフィールドの値は使用しないでください。
- Powershell または Azure Resource Manager を使用するか、Azure Portal の VM の詳細から、正確な VM サイズ情報を取得することもできます。

予約 VM インスタンスはほとんどの VM サイズで提供されますが、一部例外があります。

- プレビューの VM: プレビュー段階の VM シリーズまたはサイズは、すべて予約購入の対象外となります。
- クラウド: Azure 米国政府機関、ドイツ、中国の各リージョンでは、予約購入を利用できません。
- クォータの不足: 単一サブスクリプションをスコープとする予約の場合、新しい予約インスタンスに割り当てることのできる vCPU クォータがそのサブスクリプションに存在していることが必要です。 たとえば対象のサブスクリプションに、D-Series に対して 10 vCPU のクォータ制限がある場合、Standard_D1 インスタンス 11 個分の予約を購入することはできません。 予約のクォータ チェックには、既にサブスクリプションにデプロイされている VM が含まれます。 たとえば、サブスクリプションに D-Series に対する 10 vCPU のクォータがあり、2 つの standard_D1 インスタンスがデプロイされている場合、このサブスクリプションでは、10 standard_D1 インスタンスの予約を購入することができます。 
- 容量制限: まれなケースですが、一部の VM サイズに関して、リージョンのキャパシティが低下しているために、新しい予約を購入できないよう Azure によって制限されます。

## <a name="buy-a-reserved-vm-instance"></a>予約 VM インスタンスの購入

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]** > **[予約]** を選択します。
3. **[追加]** を選択して新しい予約を購入します。
4. 必須フィールドに必要事項を入力します。 選択した属性と一致する VM インスタンスを実行することで、予約割引を受けることができます。 割引を受ける VM インスタンスの実際の数は、選択したスコープと数量によって変わります。

    | フィールド      | 説明|
    |:------------|:--------------|
    |Name        |この予約の名前。| 
    |サブスクリプション|予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、予約の初期コストが課金されます。 サブスクリプションの種類は、Enterprise Agreement (オファー番号: MS-AZR-0017P) または従量課金制 (オファー番号: MS-AZR-0003P) である必要があります。 エンタープライズ サブスクリプションの場合、登録の年額コミットメント残高から料金が差し引かれるか、超過料金として課金されます。 従量課金制サブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。|    
    |Scope (スコープ)       |1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を予約のスコープにすることができます。 以下を選択した場合: <ul><li>1 つのサブスクリプション - 予約割引はこのサブスクリプションの VM に適用されます。 </li><li>共有 - 予約割引は、課金のコンテキスト内にある任意のサブスクリプションで実行されている VM に適用されます。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプション (開発/テスト サブスクリプションを除きます) が含まれます。 従量課金制のお客様の場合、共有スコープは、アカウント管理者が作成するすべての従量課金制サブスクリプションです。</li></ul>|
    |Location    |予約の対象となる Azure リージョン。|    
    |VM サイズ     |VM インスタンスのサイズ|
    |最適化の対象     |VM インスタンス サイズの柔軟性によって、予約割引が、同じ [VM サイズ グループ](https://aka.ms/RIVMGroups)内の他の VM に適用されます。 容量の優先度では、デプロイ用のデータセンターの容量が予約されます。 これにより、必要なときに VM インスタンスを起動する能力に対する信頼が高まります。 容量の優先順位は、予約のスコープが単一サブスクリプションに設定されている場合にのみ使用できます。 |
    |用語        |1 年間または 3 年間。|
    |数量    |予約内で購入しているインスタンス数。 数量は、課金の割引を受けられる実行中の VM インスタンス数です。 たとえば、米国東部で Standard_D2 VM を 10 個実行している場合、実行中のすべてのマシンのメリットを最大限に利用するには、数量を 10 と指定します。 |
5. **[コストの計算]** を選択すると、予約のコストを表示できます。

    ![予約の購入を送信する前のスクリーンショット](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. **[購入]** を選択します。
7. **[この予約を表示する]** を選択すると、購入の状態が表示されます。

    ![予約の購入を送信した後のスクリーンショット](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>次の手順

予約割引は、予約スコープと属性に一致する実行中の仮想マシン数に対して自動的に適用されます。 予約のスコープは、[Azure Portal](https://portal.azure.com)、PowerShell、CLI、または API で更新できます。

予約を管理する方法については、「[Azure の予約の管理](../articles/billing/billing-manage-reserved-vm-instance.md)」をご覧ください。

Azure の予約について詳しくは、次の記事をご覧ください。

- [Azure の予約とは](../articles/billing/billing-save-compute-costs-reservations.md)
- [Azure での予約の管理](../articles/billing/billing-manage-reserved-vm-instance.md)
- [予約の割引の適用方法について](../articles/billing/billing-understand-vm-reservation-charges.md)
- [従量課金制サブスクリプションの予約使用量について](../articles/billing/billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [予約に含まれない Windows ソフトウェアのコスト](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。
