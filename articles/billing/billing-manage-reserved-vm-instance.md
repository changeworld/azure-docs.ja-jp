---
title: Azure Reserved VM Instances の管理 | Microsoft Docs
description: サブスクリプション スコープを変更したり、Azure Reserved VM Instances のアクセス権を管理したりする方法について説明します。
services: billing
documentationcenter: ''
author: vikramdesai01
manager: vikramdesai01
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: vikdesai
ms.openlocfilehash: ddb9d46dc2689b0dbcd8734e276916f7cd9d2728
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064643"
---
# <a name="manage-reserved-instances-in-azure"></a>Azure での予約インスタンスの管理

Azure 予約 VM インスタンスの購入後、購入時に指定したものとは別のサブスクリプションに予約インスタンスを適用することができます。 また、条件に合致する仮想マシンが複数のサブスクリプションで実行されている場合は、その予約インスタンスのスコープを "共有" に変更することもできます。 予約インスタンスの割引効果を最大限に高めるために、実行する仮想マシンの属性と数に合ったインスタンス数を購入するようにしてください。 Azure 予約インスタンスの詳細については、[前払いで Azure 仮想マシンのコストを節約する方法](https://go.microsoft.com/fwlink/?linkid=862121)に関するページを参照してください。

## <a name="change-the-scope-for-a-reserved-instance"></a>予約インスタンスのスコープを変更する
 予約インスタンスの割引は、実際の予約インスタンスに該当し、かつ予約インスタンスのスコープ内で実行される仮想マシンに適用されます。 予約インスタンスのスコープは、単一のサブスクリプションになることもあれば、課金コンテキスト内のすべてのサブスクリプションになることもあります。 単一のサブスクリプションにスコープを設定した場合、選択したサブスクリプション内の実行中の仮想マシンに対して予約インスタンスが照合されます。 スコープを "共有" に設定した場合、課金コンテキスト内の全サブスクリプションで実行される仮想マシンに対して予約インスタンスが照合されます。 課金コンテキストは、予約インスタンスの購入に使用されたサブスクリプションによって決まります。 詳細については、[予約インスタンスで VM の使用料を前払いする方法](https://go.microsoft.com/fwlink/?linkid=861721)に関するページを参照してください。

予約インスタンスのスコープを更新するには、次の手順に従います。 
1. [Azure Portal](https://portal.azure.com) にログインします。
2. **[すべてのサービス]** > **[予約]** を選択します。
3. 予約インスタンスを選択します。
4. **[設定]** > **[構成]** を選択します。
5. スコープを変更します。 共有スコープから単一スコープに変更する場合、選択できるのは自分が所有者であるサブスクリプションだけです。 選択できるのは、予約インスタンスと同じ課金コンテキスト内のサブスクリプションに限られます。 課金コンテキストは、予約インスタンスの購入時に選択したサブスクリプションによって決まります。 スコープの対象となるのは、従量課金制プラン MS-AZR-0003P サブスクリプションと、Enterprise プラン MS-AZR-0017P サブスクリプションだけです。 エンタープライズ契約に関して、Dev/Test サブスクリプションは予約インスタンスの割引対象外となります。

## <a name="split-a-single-reserved-instance-into-two-reserved-instances"></a>予約インスタンスを 2 つの予約インスタンスに分割する
 複数のインスタンスを購入した後で、予約インスタンスに含まれるインスタンスを別のサブスクリプションに割り当てることができます。 既定では、すべてのインスタンス (その数は購入時に指定) に 1 つ (単一サブスクリプションと共有のどちらか) のスコープが割り当てられます。 たとえば Standard D2 VM を 10 個購入し、そのスコープをサブスクリプション A に指定したとします。その後、7 個の予約インスタンスについて、そのスコープをサブスクリプション A に変更し、残りの 3 つをサブスクリプション B に変更することができます。予約インスタンスを分割してインスタンスを配分することで細かなスコープ管理が可能となります。 共有スコープを選択することでサブスクリプションへの割り当てを単純化することができます。 ただし、コスト管理や予算編成の都合上、特定のサブスクリプションに数量を割り当てることもできます。

 PowerShell、CLI、または API を通じて、1 つの予約インスタンスを 2 つの予約インスタンスに分割することができます。

### <a name="split-a-reserved-instance-by-using-powershell"></a>PowerShell を使用して予約インスタンスを分割する
1. 次のコマンドを実行して予約インスタンスの発注 ID を取得します。

    ```powershell
    # Get the reserved instance orders you have access to
    Get-AzureRmReservationOrder
    ```
2. 予約インスタンスの詳細を取得します。

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. 予約インスタンスを 2 つに分割してインスタンスを配分します。

    ```powershell
    # Split the reserved instance. The sum of the reserved instances, the quantity, must equal the total number of instances in the reserved instance that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. 次のコマンドを実行してスコープを更新することができます。

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reserved-instance"></a>予約インスタンスを管理できるユーザーを追加または変更する
予約インスタンスのロールに人を追加することで予約インスタンスの管理を委任することができます。 既定では、予約インスタンスの購入者とアカウント管理者に予約インスタンスの所有者ロールが割り当てられています。 

予約インスタンスへのアクセス権は、その予約インスタンスの割引を取得したサブスクリプションとは別個に管理することができます。 予約インスタンスを管理するためのアクセス許可をだれかに与えても、サブスクリプションを管理する権限は与えられません。 また、予約インスタンスのスコープ内のサブスクリプションを管理するアクセス許可をだれかに与えても、予約インスタンスを管理する権限は与えられません。
 
予約インスタンスのアクセス管理を委任するには、次の手順に従います。 
1.  [Azure Portal](https://portal.azure.com) にログインします。
2.  **[すべてのサービス]** > **[予約]** の順に選択し、自分にアクセス権がある予約インスタンスを一覧表示します。
3.  どの予約インスタンスのアクセス権を他のユーザーに委任するかを選択します。
4.  メニューから **[アクセス制御 (IAM)]** を選択します。
5.  **[追加]** > **[ロール]** > **[所有者]** (制限されたアクセス権を割り当てる場合は他のロール) を選択します。 
6. 所有者として追加するユーザーのメール アドレスを入力します。 
7. ユーザーを選択し、**[保存]** を選択します。

## <a name="next-steps"></a>次の手順
Azure 予約インスタンスの詳細については、次の記事を参照してください。

- [Azure Reserved VM Instances とは](billing-save-compute-costs-reservations.md)
- [Azure Reserved VM Instances による仮想マシンの前払い](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [予約インスタンスの割引の適用方法について](billing-understand-vm-reservation-charges.md)
- [従量課金制サブスクリプションの予約インスタンス使用量について](billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約インスタンス使用量について](billing-understand-reserved-instance-usage-ea.md)
- [予約インスタンスに含まれない Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。
