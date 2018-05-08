---
ms.topic: include
ms.openlocfilehash: b41b93db63038645257bc348d5a027fbc4f7fdc9
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/03/2018
---
# <a name="prepay-for-virtual-machines-with-reserved-vm-instances"></a>予約 VM インスタンスによる仮想マシンの前払い

予約仮想マシン インスタンスを使用して、仮想マシンの料金を前払いして、コストを節約しましょう。 詳細については、[予約仮想マシン インスタンス プラン](https://azure.microsoft.com/pricing/reserved-vm-instances/)に関するページを参照してください。

予約仮想マシン インスタンスは、[Azure Portal](https://portal.azure.com) で購入できます。 予約仮想マシン インスタンスを購入するには:
-   少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制サブスクリプションで所有者ロールである必要があります。
-   エンタープライズ サブスクリプションの場合、[EA ポータル](https://ea.azure.com)で予約購入を有効にする必要があります。

## <a name="buy-a-reserved-virtual-machine-instance"></a>予約仮想マシン インスタンスの購入
1. [Azure Portal](https://portal.azure.com) にログインします。
2. **[すべてのサービス]** > **[予約]** を選択します。
3. **[追加]** を選択して新しい予約を購入します。
4. 必須フィールドに必要事項を入力します。 選択した属性と一致する VM インスタンスを実行することで、予約割引を受けることができます。 割引を受ける VM インスタンスの実際の数は、選択したスコープと数量によって変わります。

    | フィールド      | [説明]|
    |:------------|:--------------|
    |Name        |この予約の名前。| 
    |[サブスクリプション]|予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、予約の初期コストが課金されます。 サブスクリプションの種類は、Enterprise Agreement (プラン番号: MS-AZR-0017P) または従量課金制 (プラン番号: MS-AZR-0003P) である必要があります。 エンタープライズ サブスクリプションの場合、登録の年額コミットメント残高から料金が差し引かれるか、超過料金として課金されます。 従量課金制サブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。|    
    |Scope (スコープ)       |1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を予約のスコープにすることができます。 以下を選択した場合: <ul><li>1 つのサブスクリプション - 予約割引はこのサブスクリプションの VM に適用されます。 </li><li>共有 - 予約割引は、課金のコンテキスト内にある任意のサブスクリプションで実行されている VM に適用されます。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプション (開発/テスト サブスクリプションを除きます) が含まれます。 従量課金制のお客様の場合、共有スコープは、アカウント管理者が作成するすべての従量課金制サブスクリプションです。</li></ul>|
    |場所    |予約の対象となる Azure リージョン。|    
    |VM サイズ     |VM インスタンスのサイズ|
    |用語        |1 年間または 3 年間。|
    |数量    |予約内で購入しているインスタンス数。 数量は、課金の割引を受けられる実行中の VM インスタンス数です。 たとえば、米国東部で Standard_D2 VM を 10 個実行している場合、実行中のすべてのマシンのメリットを最大限に利用するには、数量を 10 と指定します。 |
5. **[コストの計算]** を選択すると、予約のコストを表示できます。

    ![予約の購入を送信する前のスクリーンショット](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. **[購入]** を選択します。
7. **[この予約を表示する]** を選択すると、購入の状態が表示されます。

    ![予約の購入を送信する前のスクリーンショット](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>次の手順 
予約割引は、予約スコープと属性に一致する実行中の仮想マシン数に対して自動的に適用されます。 予約のスコープは、[Azure Portal](https://portal.azure.com)、PowerShell、CLI、または API で更新できます。 

予約を管理する方法については、[Azure Reserved Virtual Machine Instances の管理](../articles/billing/billing-manage-reserved-vm-instance.md)に関するページを参照してください。

予約仮想マシン インスタンスの詳細については、次の記事を参照してください。

- [予約仮想マシン インスタンスで仮想マシンのコストを削減する](../articles/billing/billing-save-compute-costs-reservations.md)
- [予約仮想マシン インスタンスの割引の適用方法について](../articles/billing/billing-understand-vm-reservation-charges.md)
- 
  [従量課金制サブスクリプションの予約インスタンス使用量について](../articles/billing/billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約インスタンス使用量について](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [予約インスタンスに含まれない Windows ソフトウェアのコスト](../articles/billing/billing-reserved-instance-windows-software-costs.md)