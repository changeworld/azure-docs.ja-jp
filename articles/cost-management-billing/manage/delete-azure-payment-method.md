---
title: Azure の課金の支払い方法を削除する
description: Azure サブスクリプションで使用される支払い方法を削除する方法について説明します。
author: bandersmsft
ms.reviewer: lishepar
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/10/2021
ms.author: banders
ms.openlocfilehash: 6050b98d54cf46bf83168d7d33b9ca44d2bd6eda
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132180018"
---
# <a name="delete-an-azure-billing-payment-method"></a>Azure の課金の支払い方法を削除する

このドキュメントでは、各種の Azure サブスクリプションから支払い方法 (クレジット カードなど) を削除する手順を説明します。 以下についての支払い方法を削除できます。

- Microsoft 顧客契約 (MCA)
- Microsoft Online Services プログラム (MOSP)。従量課金制とも呼ばれます

関連付けられている支払い方法を削除するためには、Azure サブスクリプションの種類に関係なく、それを取り消す必要があります。

Microsoft Partner Agreement や Enterprise Agreement など、他の種類の Azure サブスクリプションの支払い方法を削除することはできません。

## <a name="delete-an-mca-payment-method"></a>MCA の支払い方法を削除する

支払い方法を削除できるのは、Microsoft 顧客契約アカウントを作成したユーザーだけです。

Microsoft 顧客契約の支払い方法を削除するには、次の手順を実行します。

1. Azure Portal ( https://portal.azure.com/ ) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 必要に応じて、課金スコープを選択します。
1. 左側のメニューで、 **[課金]** の **[課金プロファイル]** を選択します。  
    :::image type="content" source="./media/delete-azure-payment-method/billing-profiles.png" alt-text="Azure portal での課金プロファイルの表示例のスクリーンショット" lightbox="./media/delete-azure-payment-method/billing-profiles.png" :::
1. 課金プロファイルの一覧から、目的の支払い方法が使用されているものを選択します。  
    :::image type="content" source="./media/delete-azure-payment-method/select-billing-profile.png" alt-text="課金プロファイルの一覧表示画面の例" :::
1. 左側のメニュー一覧の **[設定]** で **[支払い方法]** を選択します。
1. 自分の課金プロファイルの支払い方法ページの **[クレジット カード]** セクションに、支払い方法の表が表示されます。 削除したいクレジット カードを探して省略記号 ( **…** ) を選択し、 **[削除]** を選択します。  
    :::image type="content" source="./media/delete-azure-payment-method/delete-credit-card.png" alt-text="クレジット カードの削除画面の例" :::
1. [Delete a payment method]\(支払い方法の削除\) ページが表示されます。 その支払い方法が使用中であるかどうかが、Azure によってチェックされます。
    - 支払い方法が使用中でない場合、 **[削除]** オプションが有効になります。 それを選択すると、クレジット カード情報が削除されます。
    - 支払い方法が使用中である場合は、交換またはデタッチする必要があります。 引き続き以降のセクションをお読みください。 自分のサブスクリプションで使用中の支払い方法を **デタッチ** する方法が説明されています。

### <a name="detach-payment-method-used-by-an-mca-billing-profile"></a>MCA の課金プロファイルで使用されている支払い方法をデタッチする

自分の支払い方法が MCA の課金プロファイルで使用されている場合、次の例のようなメッセージが表示されます。

:::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-microsoft-customer-agreement.png" alt-text="支払い方法が Microsoft 顧客契約で使用されていることを示す画面の例" :::

支払い方法をデタッチするには、一連の条件を満たす必要があります。 1 つでも条件を満たしていない場合、その条件を満たす方法についての手順が表示されます。 また、その状況を解決することができる場所へのリンクも表示されます。

すべての条件が満たされると、課金プロファイルから支払い方法をデタッチすることができます。

> [!NOTE]
> 既定の支払い方法がデタッチされると、その課金プロファイルは "_非アクティブ_" 状態になります。 この手順で削除した内容は一切回復できなくなります。 課金プロファイルが非アクティブ状態になった後は、新しい Azure サブスクリプションにサインアップして新しいリソースを作成する必要があります。

#### <a name="to-detach-a-payment-method"></a>支払い方法をデタッチするには

1. [Delete a payment method]\(支払い方法の削除\) 領域で、 **[Detach the current payment method]\(現在の支払い方法をデタッチする\)** リンクを選択します。
1. すべての条件が満たされている場合は、 **[デタッチ]** を選択します。 使用する場合は、次の手順に進みます。
1. [デタッチ] が利用できない場合、条件の一覧が表示されます。 一覧に表示されているアクションを実行してください。 [Detach the default payment method]\(既定の支払い方法をデタッチする\) 領域に表示されているリンクを選択します。 次に示したのは、実行が必要なアクションについて説明する是正措置の例です。  
    :::image type="content" source="./media/delete-azure-payment-method/azure-subscriptions.png" alt-text="MCA の支払い方法をデタッチするために必要な是正措置を示す画面の例" :::
1. 是正措置のリンクを選択すると、アクションを実行するための Azure ページにリダイレクトされます。 必要な是正措置をすべて行ってください。
1. 必要に応じて、他の是正措置をすべて済ませます。
1. **[コストの管理と請求]**  >  **[課金プロファイル]**  >  **[支払い方法]** に戻ります。 **[デタッチ]** を選択します。 [Detach the default payment method]\(既定の支払い方法をデタッチする\) ページの一番下にある **[デタッチ]** を選択します。

> [!NOTE]
> - サブスクリプションの取り消し後、そのサブスクリプションが削除されるまでに最大 90 日かかる場合があります。
> - 支払い方法を削除できるのは、課金プロファイルに対する以前の請求金額がすべて決済された後となります。 現時点で請求期間がアクティブな場合は、その請求期間の終了を待ってから自分の支払い方法を削除する必要があります。 **請求期間の終了を待つ間、デタッチに必要なその他の条件がすべて満たされていることを確認してください**。

## <a name="delete-a-mosp-payment-method"></a>MOSP の支払い方法を削除する

支払い方法を削除するには、アカウント管理者である必要があります。

支払い方法が MOSP サブスクリプションで使用されている場合は、次の手順を実行します。

1. Azure Portal ( https://portal.azure.com/ ) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 必要に応じて、課金スコープを選択します。
1. 左側のメニュー一覧の **[課金]** で **[支払い方法]** を選択します。
1. [支払い方法] 領域で、支払い方法がオンになっている行の省略記号 **[...]** を選択してから、 **[削除]** を選択します。
    :::image type="content" source="./media/delete-azure-payment-method/delete-mosp-payment-method.png" alt-text="MOSP の支払い方法をデタッチするために必要な是正措置を示す画面の例" :::
1. すべての条件を満たしている場合、[Delete a payment method]\(支払い方法の削除\) 領域の **[削除]** を選択します。 [削除] が選択できない場合は、次の手順に進んでください。
1. 条件の一覧が表示されます。 一覧に表示されているアクションを実行してください。 [Delete a payment method]\(支払い方法の削除\) 領域に表示されているリンクを選択します。  
    :::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-mosp.png" alt-text="支払い方法が MOSP サブスクリプションで使用されていることを示す画面の例" :::
1. 是正措置のリンクを選択すると、アクションを実行するための Azure ページにリダイレクトされます。 必要な是正措置をすべて行ってください。
1. 必要に応じて、他の是正措置をすべて済ませます。
1. **[コストの管理と請求]**  >  **[課金プロファイル]**  >  **[支払い方法]** に戻り、支払い方法を削除します。

> [!NOTE]
> サブスクリプションの取り消し後、そのサブスクリプションが削除されるまでに最大 90 日かかる場合があります。

## <a name="next-steps"></a>次のステップ

- Azure サブスクリプションの取り消しについて詳しい情報が必要な場合は、「[Azure サブスクリプションの取り消し](cancel-azure-subscription.md)」を参照してください。
- クレジット カードの追加または更新について詳しくは、「[Azure のクレジット カードの追加または更新](change-credit-card.md)」を参照してください。
