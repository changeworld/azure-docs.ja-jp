---
title: Azure のクレジット カードの変更
description: Azure サブスクリプションの支払いに使用するクレジット カードを変更する方法について説明します。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 07/27/2021
ms.author: banders
ms.openlocfilehash: 18d8a363a21e8086b3ebcd01c0b32b0ae119a483
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000425"
---
# <a name="add-or-update-a-credit-card-for-azure"></a>Azure のクレジット カードの追加または更新

このドキュメントは、クレジット カードを使用して Azure オンラインにサインアップされているお客様を対象としています。

Azure portal では、既定の支払い方法から新しいクレジット カードへの変更と、クレジット カードの詳細の更新を行うことができます。 これらの変更を行うには、[アカウント管理者](add-change-subscription-administrator.md#whoisaa)であるか、適切な [MCA アクセス許可](understand-mca-roles.md)を持っている必要があります。

クレジット カードを削除する場合は、「[Azure の課金の支払い方法を削除する](delete-azure-payment-method.md)」を参照してください。

Microsoft Azure でサポートされている支払い方法は、クレジット カードと小切手/電信送金です。 小切手/電信送金による支払いの承認を受けるには、[Azure サブスクリプションの請求書による支払い](pay-by-invoice.md)に関する記事を参照してください。

Microsoft 顧客契約では、支払い方法は課金プロファイルに関連付けられています。 [Microsoft 顧客契約にアクセスする](#check-the-type-of-your-account)方法をご覧ください。 MCA を結んでいる場合は、「[Microsoft 顧客契約のクレジット カードを管理する](#manage-credit-cards-for-a-microsoft-customer-agreement)」に進んでください。

>[!NOTE]
> 新しいサブスクリプションを作成するときに、新しいクレジット カードを指定できます。 これを行っても、他のサブスクリプションが新しいクレジット カードに関連付けられることはありません。 ただし、後で次のいずれかの変更を行った場合は、選択した支払い方法が "*すべてのサブスクリプション*" で使用されるようになります。
  >- **[アクティブに設定]** オプションを使用して支払い方法をアクティブにする
  >- 任意のサブスクリプションに対して **[置換]** の支払いオプションを使用する
  >- 既定の支払い方法を変更する

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>Azure サブスクリプションのクレジット カードを管理する

以降のセクションは、Microsoft Online Services Program 課金アカウントを持つお客様に適用されます。 [課金アカウントの種類を確認する](#check-the-type-of-your-account)方法をご覧ください。 課金アカウントの種類が Microsoft Online Services Program である場合、支払い方法は個別の Azure サブスクリプションに関連付けられています。 クレジット カードを追加したあとでエラーが発生する場合は、「[Azure へのサインアップ時にクレジット カード拒否される](./troubleshoot-declined-card.md)」を参照してください。

### <a name="change-credit-card-for-all-subscriptions-by-adding-a-new-credit-card"></a>新しいクレジット カードを追加してすべてのサブスクリプションのクレジット カードを変更する

Azure サブスクリプションの既定のクレジット カードを、新しいクレジット カードまたは Azure portal で以前に保存したクレジット カードに変更できます。 クレジット カードを変更するには、アカウント管理者である必要があります。 

複数のサブスクリプションに同じアクティブな支払い方法が指定されている場合は、これらのうちのいずれかのサブスクリプションの既定の支払い方法を変更すると、それ以外のサブスクリプションのアクティブな支払い方法も更新されます。

次の手順に従って、サブスクリプションの既定のクレジット カードを新しいものに変更できます。

1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてサインインします。
1. "**コスト管理 + 請求**" を検索します。  
    :::image type="content" source="./media/change-credit-card/search.png" alt-text="検索を示すスクリーンショット。" lightbox="./media/change-credit-card/search.png" :::
1. クレジット カードを追加するサブスクリプションを選択します。
1. **[支払い方法]** を選択します。  
    :::image type="content" source="./media/change-credit-card/payment-methods-blade-x.png" alt-text="[支払い方法の管理] オプションが選択されていることを示すスクリーンショット。" lightbox="./media/change-credit-card/payment-methods-blade-x.png" :::
1. 左上隅にある **[+ 追加]** を選択して、カードを追加します。 クレジット カードのフォームが右側に表示されます。
1. クレジット カードの詳細を入力します。  
    :::image type="content" source="./media/change-credit-card/sub-add-new-default.png" alt-text="新しいカードの追加を示すスクリーンショット。" lightbox="./media/change-credit-card/sub-add-new-default.png" :::
1. このカードを既定の支払い方法にするには、フォームの上にある **[Make this my default payment method]\(これを既定の支払方法にする\)** を選択します。 選択したサブスクリプションと同じカードを使用しているすべてのサブスクリプションで、このカードがアクティブな支払い方法になります。
1. **[次へ]** を選択します。

### <a name="replace-credit-card-for-a-subscription-to-a-previously-saved-credit-card"></a>サブスクリプションのクレジット カードを前に保存したクレジット カードに置き換えます

また、次の手順に従って、サブスクリプションの既定のクレジット カードをアカウントに保存済みのものに置き換えることもできます。 この手順では、他のすべてのサブスクリプションのクレジット カードを変更します。

1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてサインインします。
1. "**コスト管理 + 請求**" を検索します。  
    :::image type="content" source="./media/change-credit-card/search.png" alt-text="[コスト管理 + 請求] の検索を示すスクリーンショット。" lightbox="./media/change-credit-card/search.png" :::
1. クレジット カードを追加するサブスクリプションを選択します。
1. **[支払い方法]** を選択します。
    :::image type="content" source="./media/change-credit-card/payment-methods-blade-x.png" alt-text="[支払い方法の管理] オプションを示すスクリーンショット。" lightbox="./media/change-credit-card/payment-methods-blade-x.png" :::
1. **[置換]** を選択して、現在のクレジット カードを選択したものに変更します。
    :::image type="content" source="./media/change-credit-card/replace-credit-card.png" alt-text="[置換] オプションを示すスクリーンショット。" lightbox="./media/change-credit-card/replace-credit-card.png" :::
1. **[Replace default payment method]\(既定の支払方法を置き換える\)** で、既定のクレジット カードを置き換える別のクレジット カードを選択し、 **[次へ]** を選択します。
    :::image type="content" source="./media/change-credit-card/replace-default-payment-method.png" alt-text="[Replace default payment method]\(既定の支払方法を置き換える\) ボックスを示すスクリーンショット。" lightbox="./media/change-credit-card/replace-default-payment-method.png" :::
1. しばらくすると、支払い方法が変更されたこと示す確認メッセージが表示されます。

### <a name="edit-credit-card-details"></a>クレジット カードの詳細を編集する

クレジット カードを更新しても、番号が元のままである場合は、有効期限など、既存のクレジット カードの詳細情報を更新します。 カードの紛失、盗難、期限切れなどの理由でクレジット カード番号が変更された場合は、[支払い方法としてクレジット カードを追加する](#addcard)セクションの手順に従います。 CVV を更新する必要はありません。

1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてサインインします。
1. "**コスト管理 + 請求**" を検索します。
    :::image type="content" source="./media/change-credit-card/search.png" alt-text="検索のスクリーンショット。" lightbox="./media/change-credit-card/search.png" :::
1. **[支払い方法]** を選択します。
    :::image type="content" source="./media/change-credit-card/payment-methods-blade-x.png" alt-text="[支払い方法の管理] を示すスクリーンショット" lightbox="./media/change-credit-card/payment-methods-blade-x.png" :::
1. 編集するクレジット カードを選択します。 クレジット カードのフォームが右側に表示されます。
    :::image type="content" source="./media/change-credit-card/edit-card-x.png" alt-text="[支払方法の編集] を示すスクリーンショット。" lightbox="./media/change-credit-card/edit-card-x.png" :::
1. クレジット カードの詳細を更新します。
1. **[次へ]** を選択します。

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>Microsoft 顧客契約のクレジット カードを管理する

以降のセクションは、Microsoft 顧客契約を締結し、クレジット カードを使用して Azure にオンラインでサインアップしたお客様と、適切な [MCA アクセス許可](understand-mca-roles.md)を持つお客様に適用されます。 [Microsoft 顧客契約にアクセスできるかどうかを確認する方法をご覧ください](#check-the-type-of-your-account)。

### <a name="change-default-credit-card"></a>既定のクレジット カードを変更する

Microsoft 顧客契約を結んでいる場合、クレジット カードは課金プロファイルに関連付けられています。 課金プロファイルの支払い方法を変更するには、Azure にサインアップして課金アカウントを作成済みのユーザーであるか、適切な [MCA アクセス許可](understand-mca-roles.md)を持っている必要があります。

課金プロファイルの既定の支払い方法を小切手/電信送金に変更する場合は、[Azure サブスクリプションの請求書による支払い](pay-by-invoice.md)に関する記事を参照してください。

クレジット カードを変更するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[コストの管理と請求]** で検索します。
1. 左側のメニューで、 **[課金プロファイル]** を選択します。
1. 課金プロファイルを選択します。
1. 左側のメニューで **[支払い方法]** を選択します。  
    :::image type="content" source="./media/change-credit-card/payment-methods-tab-mca.png" alt-text="メニューの支払い方法を示すスクリーンショット。" lightbox="./media/change-credit-card/payment-methods-tab-mca.png" :::
1. **[既定の支払方法]** セクションで、 **[変更]** を選択します。  
    :::image type="content" source="./media/change-credit-card/change-payment-method-mca.png" alt-text="[置換] を示すスクリーンショット。" lightbox="./media/change-credit-card/change-payment-method-mca.png" :::
1. 右側の新しい領域で、ドロップダウンから既存のカードを選択するか、青色の **[新しい支払方法を追加]** リンクを選択して新しく追加します。

### <a name="edit-a-credit-card"></a>クレジット カードを編集する

Azure portal で、クレジット カードの詳細を編集できます (有効期限の更新など)。 

クレジット カードを編集するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[コストの管理と請求]** で検索します。
1. 左側のメニューで、 **[課金プロファイル]** を選択します。
1. 課金プロファイルを選択します。
1. 左側のメニューで **[支払い方法]** を選択します。  
    :::image type="content" source="./media/change-credit-card/payment-methods-tab-mca.png" alt-text="メニューの支払い方法を示すスクリーンショット。" lightbox="./media/change-credit-card/payment-methods-tab-mca.png" :::
1. **[クレジット カード]** セクションで、編集するクレジット カードを見つけます。
1. 行の末尾にある省略記号 (`...`) を選択します。  
    :::image type="content" source="./media/change-credit-card/edit-delete-credit-card-mca.png" alt-text="省略記号を示すスクリーンショット。" lightbox="./media/change-credit-card/edit-delete-credit-card-mca.png" :::
1. クレジット カードの詳細を編集するには、コンテキスト メニューから **[編集]** を選択します。

## <a name="troubleshooting"></a>トラブルシューティング

Azure では、バーチャル カードやプリペイド カードはサポートされていません。 有効なクレジット カードを追加または更新するときにエラーが発生する場合は、ブラウザーをプライベート モードで開いてみてください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

次のセクションでは、クレジット カードの情報の変更についてよく寄せられる質問に回答します。

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>"ログイン セッションの期限が切れています。 ここをクリックして、ログインし直してください。" という表示が消えないのはなぜですか。

既にログアウトして再ログインしている場合にも、このエラー メッセージが続けて表示される場合は、プライベート ブラウズ セッションを使用して再試行してください。

### <a name="how-do-i-use-a-different-card-for-each-subscription"></a>サブスクリプションごとに異なるカードを使用するには、どうしたらいいですか。

前に説明したように、新しいサブスクリプションを作成するときに、新しいクレジット カードを指定できます。 これを行っても、他のサブスクリプションが新しいクレジット カードに関連付けられることはありません。 それぞれに一意のクレジット カードが関連付けられた、複数の新しいサブスクリプションを追加することができます。 ただし、後で次のいずれかの変更を行った場合は、選択した支払い方法が "*すべてのサブスクリプション*" で使用されるようになります。

- **[アクティブに設定]** オプションを使用して支払い方法をアクティブにする
- 任意のサブスクリプションに対して **[置換]** の支払いオプションを使用する
- 既定の支払い方法を変更する

### <a name="how-do-i-make-payments"></a>支払いの手順を教えてください。

支払い方法としてクレジット カードを設定している場合、各請求期間の終了後にカードから自動的に支払われます。 何も行う必要はありません。

[請求書による支払](pay-by-invoice.md)を行っている場合、請求書の下部に記載されている場所に請求書を送付します。

### <a name="how-do-i-change-the-tax-id"></a>税 ID を変更する方法を教えてください。

税 ID を追加または更新するには、[Azure portal](https://portal.azure.com) のプロファイルを更新し、 **[納税記録]** を選択します。 この税 ID は、免税の計算に使用され、請求書に表示されます。

## <a name="check-the-type-of-your-account"></a>アカウントの種類を確認する

[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- [Azure の予約](../reservations/save-compute-costs-reservations.md)について学習し、コストを節約できるかどうか確認してください。
- クレジット カードを削除する場合は、「[Azure の課金の支払い方法を削除する](delete-azure-payment-method.md)」を参照してください。