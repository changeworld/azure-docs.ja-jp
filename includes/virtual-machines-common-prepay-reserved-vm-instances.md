---
ms.topic: include
ms.openlocfilehash: 8e710bebf979b60f61552593ae550e95a8340d2b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2018
ms.locfileid: "34307568"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Azure Reserved VM Instances による仮想マシンの前払い

Azure Reserved Virtual Machine (VM) Instances を使って、仮想マシンの料金を前払いして、コストを節約しましょう。 詳しくは、[Azure Reserved Instances のオファー](https://azure.microsoft.com/pricing/reserved-vm-instances/)に関するページをご覧ください。

Azure Reserved Instances は [Azure portal](https://portal.azure.com) で購入できます。 予約インスタンスを購入するには:
-   少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制サブスクリプションで所有者ロールである必要があります。
-   Enterprise サブスクリプションの場合、[EA ポータル](https://ea.azure.com)で予約インスタンスを有効にする必要があります。
-   クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理エージェントまたはセールス エーエージェントのみが予約インスタンスを購入できます。

## <a name="buy-a-reserved-instance"></a>予約インスタンスを購入する
1. [Azure Portal](https://portal.azure.com) にログインします。
2. **[すべてのサービス]** > **[予約]** を選択します。
3. **[追加]** を選択して、新しい予約インスタンスを購入します。
4. 必須フィールドに必要事項を入力します。 選択した属性と一致する VM インスタンスを実行することで、予約インスタンスの割引を受けることができます。 割引を受ける VM インスタンスの実際の数は、選択したスコープと数量によって変わります。

    | フィールド      | 説明|
    |:------------|:--------------|
    |N名前        |この予約インスタンスの名前。| 
    |サブスクリプション|予約インスタンスの支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、予約インスタンスの初期コストが課金されます。 サブスクリプションの種類は、Enterprise Agreement (プラン番号: MS-AZR-0017P) または従量課金制 (プラン番号: MS-AZR-0003P) である必要があります。 エンタープライズ サブスクリプションの場合、登録の年額コミットメント残高から料金が差し引かれるか、超過料金として課金されます。 従量課金制サブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。|    
    |スコープ       |1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を予約インスタンスのスコープにすることができます。 以下を選択した場合: <ul><li>1 つのサブスクリプション - 予約インスタンス割引はこのサブスクリプションの VM に適用されます。 </li><li>共有 - 予約インスタンス割引は、課金のコンテキスト内にある任意のサブスクリプションで実行されている VM に適用されます。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプション (開発/テスト サブスクリプションを除きます) が含まれます。 従量課金制のお客様の場合、共有スコープは、アカウント管理者が作成するすべての従量課金制サブスクリプションです。</li></ul>|
    |リージョン    |予約インスタンスの対象となる Azure リージョン。|    
    |VM サイズ     |VM インスタンスのサイズ|
    |契約期間        |1 年間または 3 年間。|
    |数量    |予約インスタンス内で購入しているインスタンス数。 数量は、課金の割引を受けられる実行中の VM インスタンス数です。 たとえば、米国東部で Standard_D2 VM を 10 個実行している場合、実行中のすべてのマシンのメリットを最大限に利用するには、数量を 10 と指定します。 |
5. **[コストの計算]** を選択すると、予約インスタンスのコストを表示できます。

    ![予約インスタンスの購入を送信する前のスクリーンショット](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. **[購入]** を選択します。
7. **[この予約を表示する]** を選択すると、購入の状態が表示されます。

    ![予約インスタンスの購入を送信した後のスクリーンショット](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>次の手順 
予約インスタンス割引は、予約インスタンス スコープと属性に一致する実行中の仮想マシン数に対して自動的に適用されます。 予約インスタンスのスコープは、[Azure portal](https://portal.azure.com)、PowerShell、CLI、または API で更新できます。 

予約インスタンスを管理する方法については、[Azure Reserved Instances の管理](../articles/billing/billing-manage-reserved-vm-instance.md)に関するページを参照してください。

Azure Reserved Instances について詳しくは、次の記事をご覧ください。

- [Reserved Instances で仮想マシンのコストを削減する](../articles/billing/billing-save-compute-costs-reservations.md)
- [Azure Reserved Instances を管理する](../articles/billing/billing-manage-reserved-vm-instance.md)
- [予約インスタンスの割引の適用方法について](../articles/billing/billing-understand-vm-reservation-charges.md)
- 
  [従量課金制サブスクリプションの予約インスタンス使用量について](../articles/billing/billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約インスタンス使用量について](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [予約インスタンスに含まれない Windows ソフトウェアのコスト](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムで予約済みのインスタンス](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。