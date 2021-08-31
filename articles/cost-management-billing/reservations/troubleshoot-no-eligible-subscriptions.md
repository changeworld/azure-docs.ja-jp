---
title: Azure portal で発生する "有効なサブスクリプションがない" というエラーのトラブルシューティング
description: この記事では、Azure portal で予約を購入しようとしたときに発生する "有効なサブスクリプションがない" というエラー メッセージのトラブルシューティング方法について説明します。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 06/27/2021
ms.openlocfilehash: 40002ab69c39481393654629a44a038dfc9d01af
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988933"
---
# <a name="troubleshoot-no-eligible-subscriptions"></a>トラブルシューティング: "有効なサブスクリプションがない"

この記事では、Azure portal で予約を購入しようとしたときに発生する "*有効なサブスクリプションがない*" というエラー メッセージのトラブルシューティング方法について説明します。

## <a name="symptoms"></a>現象

1. [Azure portal](https://portal.azure.com) にサインインし、 **[予約]** に移動します。
1. **[追加]** を選択し、サービスを選択します。
1. 次のようなエラー メッセージが表示されます。
   ```
    No eligible subscriptions
    
    You do not have any eligible subscriptions to purchase reservations. To purchase a reservation, you should have owner or reservation purchaser permission on at least one subscription of the following type: Pay-as-you-go, CSP, Microsoft Enterprise or Microsoft Customer Agreement.
    ```
1. **[購入する製品の選択]** 領域の **[課金サブスクリプション]** の一覧を展開すると、特定のサブスクリプションが予約インスタンスを購入する資格を有していない理由が表示されます。 次の画像は、予約を購入できない理由の例を示しています。  
    :::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" alt-text="予約を購入できない理由の例" lightbox="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" :::

## <a name="cause"></a>原因

Azure 予約インスタンスを購入するには、次の要件を満たしたサブスクリプションを少なくとも 1 つ所有している必要があります。

- サブスクリプションのプランの種類がサポート対象である必要があります。 サポートされているプランの種類は、従量課金制、クラウド ソリューション プロバイダー (CSP)、Microsoft Azure エンタープライズ、Microsoft 顧客契約です。
- サブスクリプションの所有者または予約購入者である必要があります。

要件を満たすサブスクリプションがない場合は、"`No eligible subscriptions`" というエラーが表示されます。

### <a name="cause-1"></a>原因 1

サブスクリプションのプランの種類がサポート対象である必要があります。 サポートされているプランの種類は、従量課金制、CSP、Microsoft Azure エンタープライズ、Microsoft 顧客契約です。 ご利用のサブスクリプションの種類がサポート対象ではありません。 選択したサブスクリプションのプランの種類で予約がサポートされていない場合は、次のエラーが表示されます。

```
Subscription not eligible for purchase

This subscription is not eligible for reservation benefit an cannot be used to purchase a reservation.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/subscription-not-eligible.png" alt-text="エラー メッセージの例: &quot;購入対象外のサブスクリプション&quot;" :::

### <a name="cause-2"></a>原因 2

サブスクリプションの所有者または予約購入者である必要があります。 十分なアクセス許可がない場合は、次のエラーが表示されます。

```
You do not have owner or reservation purchaser access on the subscription

You can only purchase reservations using subscriptions on which you have owner or reservation purchaser access.
```

## <a name="solution"></a>解決策

- 現在のプランで予約がサポートされていない場合、新しい Azure サブスクリプションを作成する必要があります。
- 既存の予約にアクセスできない場合、現在の所有者からアクセス権を入手することができます。

### <a name="solution-1"></a>解決策 1

予約を購入するには、予約がサポートされる新しい Azure サブスクリプションを作成する必要があります。

- Azure 無料試用版をご利用の場合は、[サブスクリプションをアップグレード](../manage/upgrade-azure-subscription.md)してください。
- [従量課金制料金](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)の新しい Azure サブスクリプションを作成できます。
- [Microsoft 顧客契約](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)にサインアップして新しいサブスクリプションを作成します。
- [CSP](https://www.microsoft.com/solution-providers/home) の新しいサブスクリプションを購入し、新しいサブスクリプションを作成します。

### <a name="solution-2"></a>解決策 2

予約への所有者アクセス権を入手するには、次のいずれかへのアクセス権を入手する必要があります。

- 予約購入時の予約注文
- 予約自体

現在の予約注文所有者または予約所有者は、次の手順に従ってアクセス権を委任できます。

他のユーザーが予約を管理できるようにするには、次の 2 つのオプションがあります。

- 予約注文のリソース スコープのユーザーに Owner ロールを割り当てることにより、個別の予約注文に対するアクセス管理を委任する。 制限付きアクセス権を付与する場合は、別のロールを選択します。  
     詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

- ユーザーを課金管理者としてマイクロソフト エンタープライズ契約または Microsoft 顧客契約に追加するには、次の手順を実行します。
    - マイクロソフト エンタープライズ契約の場合は、"_エンタープライズ管理者_" ロールを持つユーザーを追加することで、マイクロソフト エンタープライズ契約に適用されるすべての予約注文を表示および管理できます。 "_エンタープライズ管理者 (読み取り専用)_ " ロールを持つユーザーは、予約の表示のみを実行できます。 部門管理者およびアカウント所有者は、アクセス制御 (IAM) を使用して明示的にこれらに追加されて "_いない限り_"、予約を表示することはできません。 詳細については、「[Azure エンタープライズ ロールの管理](../manage/understand-ea-roles.md)」を参照してください。

        "_エンタープライズ管理者は、予約注文の所有権を取得し、アクセス制御 (IAM) を使用して他のユーザーを予約に追加することができます。_ "
    - Microsoft 顧客契約の場合は、課金プロファイル所有者ロールまたは課金プロファイル共同作成者ロールを持つユーザーが、課金プロファイルを使用して行われたすべての予約購入を管理できます。 課金プロファイル閲覧者と請求書管理者は、請求プロファイルを使用して支払われるすべての予約を表示できます。 ただし、予約に変更を加えることはできません。
    詳細については、「[課金プロファイルのロールとタスク](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)」を参照してください。

詳細については、「[予約を管理できるユーザーを追加または変更する](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default)」を参照してください。

## <a name="next-steps"></a>次の手順

- 予約所有者からアクセス権を付与してもらう必要がある場合は、「[予約を管理できるユーザーを追加または変更する](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default)」を参照してください。
