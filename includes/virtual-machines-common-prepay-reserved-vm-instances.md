---
author: yashesvi
ms.author: cwatson
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/30/2018
ms.openlocfilehash: a35c3a9a6d914cf7e6620819b24e34d954f55c2d
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742448"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Azure Reserved VM Instances による仮想マシンの前払い

Azure Reserved Virtual Machine (VM) Instances を使って、仮想マシンの料金を前払いして、コストを節約しましょう。 詳しくは、[Azure Reserved VM Instances のオファー](https://azure.microsoft.com/pricing/reserved-vm-instances/)に関するページをご覧ください。

予約 VM インスタンスは [Azure Portal](https://portal.azure.com) から購入できます。 インスタンスを購入する場合、次のことが必要です。

- 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制サブスクリプションで所有者ロールである必要があります。
- エンタープライズ サブスクリプションの場合、[EA ポータル](https://ea.azure.com)で予約購入を有効にする必要があります。
- クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理エージェントまたはセールス エーエージェントのみが予約購入できます。

## <a name="determine-the-right-vm-size-before-you-buy"></a>購入する前に適切な VM サイズを決定する

使用状況データの Meter Sub-category フィールドと Product フィールドでは、VM の Premium Storage を使用している VM サイズと使用していない VM サイズは区別されません。 これらのフィールドを使用して予約する VM サイズを決定すると、不適切なサイズを購入する場合があり、期待する予約割引を得られない可能性があります。 予約購入する際に適切な VM サイズを決定するには、次の方法のいずれかを使用してください。

- 使用状況ファイルの AdditionalInfo フィールドまたは使用状況 API を参照して、適切な VM サイズを決定します。 Meter Sub-category フィールドまたは Product フィールドの値は使用しないでください。 これらのフィールドでは、VM の S バージョンと非 S バージョンは区別されません。
- Powershell または Azure Resource Manager を使用するか、Azure Portal の VM の詳細から、正確な VM サイズ情報を取得します。

予約 VM インスタンスはほとんどの VM サイズで提供されますが、一部例外があります。

- 次の VM には予約割引は適用されません。
  - クラシック VM とクラウド サービス
  - VM シリーズ: A シリーズ、Av2 シリーズ、または G シリーズ
  - プレビュー段階にある VM: プレビュー段階にあるすべての VM シリーズまたはサイズ
- クラウド: ドイツ、中国の各リージョンでは、予約購入を利用できません。
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
    |サブスクリプション|予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、予約の初期コストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号: MS-AZR-0017P) または従量課金制 (プラン番号: MS-AZR-0003P) である必要があります。 エンタープライズ サブスクリプションの場合、登録の年額コミットメント残高から料金が差し引かれるか、超過料金として課金されます。 従量課金制サブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。|    
    |Scope (スコープ)       |1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を予約のスコープにすることができます。 以下を選択した場合: <ul><li>1 つのサブスクリプション - 予約割引はこのサブスクリプションの VM に適用されます。 </li><li>共有 - 予約割引は、課金のコンテキスト内にある任意のサブスクリプションで実行されている VM に適用されます。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプション (開発/テスト サブスクリプションを除きます) が含まれます。 従量課金制のお客様の場合、共有スコープは、アカウント管理者が作成するすべての従量課金制サブスクリプションです。</li></ul>|
    |リージョン    |予約の対象となる Azure リージョン。|    
    |VM サイズ     |VM インスタンスのサイズ|
    |最適化の対象     |VM インスタンス サイズの柔軟性によって、予約割引が、同じ [VM サイズ グループ](https://aka.ms/RIVMGroups)内の他の VM に適用されます。 容量の優先度では、デプロイ用のデータ センターの容量が優先されます。 これにより、必要なときに VM インスタンスを起動する能力に対する信頼が高まります。 容量の優先順位は、予約のスコープが単一サブスクリプションに設定されている場合にのみ使用できます。 |
    |用語        |1 年間または 3 年間。|
    |数量    |予約内で購入しているインスタンス数。 数量は、課金の割引を受けられる実行中の VM インスタンス数です。 たとえば、米国東部で Standard_D2 VM を 10 個実行している場合、実行中のすべてのマシンのメリットを最大限に利用するには、数量を 10 と指定します。 |
5. **[コストの計算]** を選択すると、予約のコストを表示できます。

    ![予約の購入を送信する前のスクリーンショット](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. **[購入]** を選択します。
7. **[この予約を表示する]** を選択すると、購入の状態が表示されます。

    ![予約の購入を送信した後のスクリーンショット](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="cancellations-and-exchanges"></a>キャンセルと交換

ご自身の予約をキャンセルする必要がある場合は、12% の中途解約料が発生する可能性があります。 払い戻し額は、購入価格または予約の現在の価格のいずれかの最低価格に基づいています。 払い戻し額の上限は年額 50,000 ドルです。 日割り計算によって算出された金額から 12% の中途解約料を差し引いた金額が払い戻されます。 キャンセルを要求するには、Azure portal で予約に移動し、**[返金]** を選択して、サポート要求を作成します。

ご自身の予約 VM インスタンスの予約を別のリージョン、VM サイズ グループ、または期間に変更する必要がある場合は、同等以上の価値がある別の予約に交換できます。 新しい予約期間の開始日は、元の予約からは引き継がれません。 1 年または 3 年の予約期間は、新しい予約を作成した時点から始まります。 交換を要求するには、Azure portal で予約に移動し、**[交換]** を選択して、サポート要求を作成します。

## <a name="next-steps"></a>次の手順

予約割引は、予約スコープと属性に一致する実行中の仮想マシン数に対して自動的に適用されます。 予約のスコープは、[Azure Portal](https://portal.azure.com)、PowerShell、CLI、または API で更新できます。

予約を管理する方法については、「[Azure の予約の管理](../articles/billing/billing-manage-reserved-vm-instance.md)」をご覧ください。

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](../articles/billing/billing-save-compute-costs-reservations.md)
- [Azure での予約の管理](../articles/billing/billing-manage-reserved-vm-instance.md)
- [予約割引の適用方法について](../articles/billing/billing-understand-vm-reservation-charges.md)
- [従量課金制サブスクリプションの予約使用量について](../articles/billing/billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [予約に含まれない Windows ソフトウェアのコスト](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。
