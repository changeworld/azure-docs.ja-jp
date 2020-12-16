---
title: Azure portal で発生する "有効なサブスクリプションがない" というエラーのトラブルシューティング
description: この記事では、Azure portal で予約を購入しようとしたときに発生する "有効なサブスクリプションがない" というエラー メッセージのトラブルシューティング方法について説明します。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 12/15/2020
ms.openlocfilehash: ad85bd278b5dff1532f218acc0b8e88515d96070
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561207"
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

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[すべてのサービス]**  >  **[予約]** の順に選択し、自分にアクセス権がある予約を一覧表示します。
1. どの予約のアクセス権を他のユーザーに委任するかを選択します。
1. **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当ての追加]**  >  **[ロール]**  >  **[所有者]** を選択します。 また、制限付きアクセス権を付与する場合は、別のロールを選択します。
1. 所有者として追加するユーザーの電子メール アドレスを入力します。
1. ユーザーを選択し、 **[保存]** を選択します。

詳細については、「[予約を管理できるユーザーを追加または変更する](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default)」を参照してください。

## <a name="next-steps"></a>次の手順

- 予約所有者からアクセス権を付与してもらう必要がある場合は、「[予約を管理できるユーザーを追加または変更する](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default)」を参照してください。
