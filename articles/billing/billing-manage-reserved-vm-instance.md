---
title: Azure の予約の管理 | Microsoft Docs
description: サブスクリプション スコープを変更し、Azure の予約に対するアクセス権を管理する方法について説明します。
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
ms.author: yashesvi
ms.openlocfilehash: d47c85d4197f45db50f1974b6faea270e6761237
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628574"
---
# <a name="manage-reservations-for-resources-in-azure"></a>Azure のリソースに対する予約を管理する

Azure の予約を購入した後、購入時に指定したものとは別のサブスクリプションに予約を適用することができます。 また、条件に合致する仮想マシン、SQL データベースなどのリソースが複数のサブスクリプションで実行されている場合は、その予約のスコープを共有に変更することもできます。 予約の割引効果を最大限に高めるために、実行するリソースの属性と数に合ったインスタンス数を購入するようにしてください。 詳細については、[Azure の予約](https://go.microsoft.com/fwlink/?linkid=862121)に関するページを参照してください。

## <a name="change-the-scope-for-a-reservation"></a>予約のスコープを変更する

 予約割引は、実際の予約に該当し、かつ予約のスコープ内で実行される仮想マシン、SQL データベースなどのリソースに適用されます。 予約のスコープは、単一のサブスクリプションになることもあれば、課金コンテキスト内のすべてのサブスクリプションになることもあります。 単一のサブスクリプションにスコープを設定した場合、選択したサブスクリプション内の実行中のリソースに対して予約が照合されます。 スコープを "共有" に設定した場合、課金コンテキスト内の全サブスクリプションで実行されるリソースに対して予約が照合されます。 課金コンテキストは、予約の購入に使用されたサブスクリプションによって決まります。

予約のスコープを更新するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]** > **[予約]** を選択します。
3. 予約を選択します。
4. **[設定]** > **[構成]** を選択します。
5. スコープを変更します。 共有スコープから単一スコープに変更する場合、選択できるのは自分が所有者であるサブスクリプションだけです。 選択できるのは、予約と同じ課金コンテキスト内のサブスクリプションに限られます。 課金コンテキストは、予約の購入時に選択したサブスクリプションによって決まります。 スコープの対象となるのは、従量課金制オファー MS-AZR-0003P サブスクリプションと、Enterprise オファー MS-AZR-0017P サブスクリプションだけです。 エンタープライズ契約に関して、Dev/Test サブスクリプションは予約割引の対象外となります。

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>予約を管理できるユーザーを追加または変更する

予約のロールに人を追加することで予約の管理を委任することができます。 既定では、予約の購入者とアカウント管理者に予約の所有者ロールが割り当てられています。

予約へのアクセス権は、その予約割引を取得したサブスクリプションとは別個に管理することができます。 予約を管理するためのアクセス許可をだれかに与えても、サブスクリプションを管理する権限は与えられません。 また、予約のスコープ内のサブスクリプションを管理するアクセス許可をだれかに与えても、予約を管理する権限は与えられません。

予約のアクセス管理を委任するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]** > **[予約]** の順に選択し、自分にアクセス権がある予約を一覧表示します。
3. どの予約のアクセス権を他のユーザーに委任するかを選択します。
4. メニューから **[アクセス制御 (IAM)]** を選択します。
5. **[追加]** > **[ロール]** > **[所有者]** (制限されたアクセス権を割り当てる場合は他のロール) を選択します。
6. 所有者として追加するユーザーのメール アドレスを入力します。 
7. ユーザーを選択し、**[保存]** を選択します。

## <a name="optimize-reserved-vm-instance-for-vm-size-flexibility-or-capacity-priority"></a>VM サイズの柔軟性や容量の優先度に合わせて予約 VM インスタンスを最適化する

 VM インスタンスの柔軟性によって、予約割引が、同じ [VM サイズ グループ](https://aka.ms/RIVMGroups)内の他の VM に適用されます。 既定では、予約のスコープが共有されている場合、インスタンス サイズの柔軟性は有効であり、データセンターの容量は VM のデプロイに対して優先されません。 スコープが単一の予約の場合は、VM インスタンス サイズの柔軟性ではなく、容量の優先度の予約を最適化できます。 容量の優先度の場合、デプロイ用にデータ センターの容量を予約し、さらに必要なときに VM インスタンスを確実に起動できます。

予約のスコープを更新するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]** > **[予約]** を選択します。
3. 予約を選択します。
4. **[設定]** > **[構成]** を選択します。
5. 設定のために [最適化] を変更します。

## <a name="split-a-single-reservation-into-two-reservations"></a>1 つの予約を 2 つの予約に分割する

 複数のインスタンスを購入した後で、予約に含まれるインスタンスを別のサブスクリプションに割り当てることができます。 既定では、すべてのインスタンス (その数は購入時に指定) に 1 つ (単一サブスクリプションと共有のどちらか) のスコープが割り当てられます。 たとえば Standard D2 VM を 10 個購入し、そのスコープをサブスクリプション A に指定したとします。その後、7 個の予約について、そのスコープをサブスクリプション A に変更し、残りの 3 つをサブスクリプション B に変更することができます。予約を分割してインスタンスを配分することで細かなスコープ管理が可能となります。 共有スコープを選択することでサブスクリプションへの割り当てを単純化することができます。 ただし、コスト管理や予算編成の都合上、特定のサブスクリプションに数量を割り当てることもできます。

 PowerShell、CLI、または API を通じて、1 つの予約を 2 つの予約に分割することができます。

### <a name="split-a-reservation-by-using-powershell"></a>PowerShell を使用して予約を分割する

1. 次のコマンドを実行して予約の発注 ID を取得します。

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```

2. 予約の詳細を取得します。

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. 予約を 2 つに分割してインスタンスを配分します。

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. 次のコマンドを実行してスコープを更新することができます。
    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="next-steps"></a>次の手順

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](billing-save-compute-costs-reservations.md)
- [Azure Reserved VM Instances による仮想マシンの前払い](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database の容量が予約された SQL Database 計算リソースの前払い](../sql-database/sql-database-reserved-capacity.md)
- [予約割引の適用方法について](billing-understand-vm-reservation-charges.md)
- [従量課金制サブスクリプションの予約使用量について](billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](billing-understand-reserved-instance-usage-ea.md)
- [予約に含まれない Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。
