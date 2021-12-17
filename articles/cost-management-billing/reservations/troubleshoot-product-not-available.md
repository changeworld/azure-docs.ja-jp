---
title: 利用できない Azure 予約タイプのトラブルシューティング
description: この記事では、Azure portal 内で利用できないものとして表示される予約インスタンスについて、その詳細とトラブルシューティングの方法を説明します。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: primittal
ms.topic: troubleshooting
ms.date: 09/20/2021
ms.openlocfilehash: 51d30767c3e084ade041d22bac8a246aa5d1f502
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631136"
---
# <a name="troubleshoot-reservation-type-not-available"></a>利用できない予約タイプのトラブルシューティング

この記事では、Azure portal 内で利用できないものとして表示される予約インスタンスについて、その詳細とトラブルシューティングの方法を説明します。

## <a name="symptoms"></a>現象

1. [Azure portal](https://portal.azure.com/) にサインインし、 **[予約]** に移動します。
2. **[+ 追加]** を選択し、製品を選択します。
3. **[すべての製品]** タブを選択します。
4. 製品の一覧から、製品を選択します。 次のいずれかのメッセージが表示されることがあります。
    - `Product unavailable for the selected subscription or region. Contact support.`  
        :::image type="content" source="./media/troubleshoot-product-not-available/product-unavailable-message.png" alt-text="選択されたサブスクリプションまたはリージョンでは利用できない製品であることを示すメッセージの例" lightbox="./media/troubleshoot-product-not-available/product-unavailable-message.png" :::
    - `The selected subscription does not have enough core quota remaining to purchase this product. Request quota increase`  
        :::image type="content" source="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" alt-text="コア クォータが不十分であることを示すメッセージの例" lightbox="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" :::

## <a name="cause"></a>原因

一部の予約を購入できない理由は次のとおりです。

- 一部リージョンでは購入できない予約インスタンス製品である
- サブスクリプションにクォータの制限が設定されている

### <a name="cause-1"></a>原因 1

予約インスタンス製品を必ずしもすべて購入できるとは限りません。 Azure では、お客様に割引を提供するコストに応じて、一部の製品の提供の有無を判断しています。 このほか、特定のデータセンターのキャパシティ面を考慮して提供していない製品もあります。 さらに、一部 Azure 製品の開発グループが、比較的古い製品を廃止するために一定の製品の提供を開始することもあります。

ほかには、一部のリージョンにおいて、各種の製品に対してその需要に応じたキャパシティ制限が適用されることがあります。 このような制限が適用される局面の例としては、あるデータ センターで特定の VM サイズに対する需要が突出しているときが挙げられます。 このような場合、Azure は、そのリージョンでそのサイズの予約を購入したお客様に対してキャパシティを保証できません。 最後に、さまざまな理由により提供を制限している製品がいくつかあります。 このような製品は、少数かつ特定のお客様に限り提供しているものです。

では、購入できない予約が Azure portal に表示されているのはなぜでしょうか。 それは、ユーザーから探している製品を見つけられない旨のサポート リクエストが作成されることがあるためです。 Azure の予約開発チームにより、`Product unavailable` というメッセージが出るものも含めて製品をすべて表示する方が、そうでない場合よりもサポート リクエストが少なくなることが確認されています。

### <a name="cause-2"></a>原因 2

サブスクリプションには、クォータの制限が設定されています。 サブスクリプションで利用できる CPU コアの数には、上限があります。 この上限は、一部の予約インスタンス製品に適用されます (代表例は仮想マシンです)。 Azure では、予約インスタンスを購入したお客様がそのインスタンスを確実に使えることが重要であると考えており、 Azure portal において、お客様のサブスクリプションに VM をデプロイできるだけの未使用のコアが十分にあり、予約の購入によるメリットが得られるかどうかを簡単に確認しています。

お客様がカートに特定の製品を追加し、予約を購入できるかどうかを判定するしくみはシンプルです。 Azure では、お客様のサブスクリプションで利用可能な CPU コアの合計数を算出し、選択された項目に必要なコアの数よりも多いかどうかを確認します。

Azure では、 **[共有]** 範囲または **管理グループ** 範囲の予約インスタンスについては、クォータの確認を実施しません。 範囲が [共有] の予約インスタンスによるメリットは、加入契約内のあらゆるサブスクリプションに適用されます。 管理グループ範囲の予約インスタンス特典は、管理グループと課金スコープの両方の一部であるすべてのサブスクリプションに適用されます。 Azure では、リソースをデプロイできるだけの十分な未使用コアがあるかどうかについて、お客様がお持ちのサブスクリプションすべてを考慮して判断を下すことができません。 Azure では、クォータが何であっても、選択した範囲が共有または管理グループに設定された場合は、常に VM サイズを選択できます。

また、**おすすめ候補** として提示された購入については、Azure によるクォータ確認が実施されません。 おすすめ候補は、アクティブな使用量に基づいて提示されます。 Azure では、おすすめ候補の提示に必要な使用量を既に満たしていることを根拠に、特定のサイズの VM を稼働させることができるだけのコア数があると判断します。

## <a name="solution"></a>解決策

表示されたエラー メッセージに応じて、次のいずれかの解決策をお試しください。

### <a name="solution-1"></a>解決策 1

"_製品が利用できない_" 旨のメッセージが表示された場合には、そのエラー メッセージにある **[サポートにお問い合わせ]** のリンクを選択し、ご自分のサブスクリプションを対象とした例外を追加するよう依頼します。 例外は常に認められるわけではありません。

### <a name="solution-2"></a>解決策 2

"_コア クォータが十分でない_" 旨のメッセージが表示された場合には、範囲を **[共有]** に変更します。 予約を購入した後に、予約範囲を **[共有]** から **[シングル]** に変更できます。

このほか、エラー メッセージにある **[クォータの増加を要求]** というリンクを選択し、サブスクリプションの CPU コア クォータの追加を依頼することもできます。

## <a name="next-steps"></a>次のステップ

- 予約範囲のオプションの詳細については、「[予約の範囲設定](prepare-buy-reservation.md#scope-reservations)」を参照してください。