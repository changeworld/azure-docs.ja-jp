---
title: クイックスタート - Azure Communication Services から電話番号を取得する
description: Azure portal を使用して Communication Services の電話番号を購入する方法について説明します。
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/09/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: f5cf8f8ef004dacc9fe2bbdd1b815f2ae5275311
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91298118"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>クイック スタート:Azure portal を使用して電話番号を取得する

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Azure portal を使用して電話番号を購入することで、Azure Communication Services の使用を開始します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [アクティブな Communication Services リソース。](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>電話番号を取得する

番号のプロビジョニングを開始するには、[Azure portal](https://portal.azure.com) で Communication Services リソースに移動します。

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Communication Services リソースのメイン ページを示すスクリーンショット。":::

### <a name="getting-new-phone-numbers"></a>新しい電話番号を取得する

リソース メニューで [Phone Numbers]\(電話番号\) ブレードに移動します。

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="Communication Services リソースのメイン ページを示すスクリーンショット。":::

[`Get`] ボタンをクリックしてウィザードを起動します。 [`Phone numbers`] ブレードのウィザードでは、自分のシナリオに最適な電話番号を選択するのに役立つ一連の質問が表示されます。 

まず、電話番号をプロビジョニングする [`Country/region`] を選択する必要があります。 国または地域を選択したら、自分のニーズに最も適した [`phone plan`] を選択する必要があります。 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Communication Services リソースのメイン ページを示すスクリーンショット。":::

### <a name="select-a-phone-plan"></a>電話プランを選択する

電話プランの選択は、2 つの手順に分けられます。 

1. [番号の種類](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)の選択
2. [プラン](../../concepts/telephony-sms/plan-solution.md#plans)の選択

現在、番号の種類として [`Geographic`] と [`Toll-free`] の 2 つが用意されています。 番号の種類を選択していると、選択できるプランがいくつか提供されます。

> [!NOTE]
> 現在、着信または発信通話対応の電話番号の選択のみがサポートされています。 ただし、着信通話が有効になっている電話番号を購入した後、着信通話対応の電話番号 (Communication Services アプリケーションから電話をかけたときに相手のユーザーに表示される内容) に一致するよう発信者番号を構成することができます。
> これは、双方向の通話のみに適用されます。 双方向の SMS はネイティブにサポートされています。

この例では、`Toll-free`という番号の種類と共に`Outbound calling`プランを選択しました。

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="Communication Services リソースのメイン ページを示すスクリーンショット。":::

### <a name="declare-purpose"></a>目的を宣言する

次に、このウィザードでは、番号の使用目的についてたずねられます。 この情報は、税と緊急通話の適切な規制を適用するために収集されます。

:::image type="content" source="../media/quickstart-search-and-acquire-bot-or-human.png" alt-text="Communication Services リソースのメイン ページを示すスクリーンショット。":::

ここで、ページの下部にある [`Next: Numbers`] ボタンをクリックして、プロビジョニングする電話番号をカスタマイズします。

### <a name="customizing-phone-numbers"></a>電話番号をカスタマイズする

[`Numbers`] ページで、プロビジョニングする電話番号をカスタマイズします。

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Communication Services リソースのメイン ページを示すスクリーンショット。":::

> [!NOTE]
> このクイックスタートでは、番号の種類が`Toll-free`のカスタマイズ フローを示しています。 番号の種類として [`Geographic`] を選択した場合は操作が多少異なる場合がありますが、最終的な結果は同じになります。

利用可能な市外局番の一覧から`Area code`を選択し、プロビジョニングする数量を入力した後、[`Search`] をクリックして、選択した要件を満たす番号を見つけます。 自分のニーズに合った電話番号が月額料金と共に表示されます。

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Communication Services リソースのメイン ページを示すスクリーンショット。":::

> [!NOTE]
> 使用できるかどうかは、選択した番号の種類、場所、プランによって異なります。
> トランザクションが期限切れになるまでの短い間、番号は予約されています。 トランザクションの期限が切れた場合は、番号を再選択する必要があります。

購入の概要を表示して注文を行うには、ページの下部にある [`Next: Summary`] ボタンをクリックします。

### <a name="place-order"></a>注文する

概要ページでは、番号の種類、機能、電話番号、および総月額料金を確認して、電話番号をプロビジョニングします。

> [!NOTE]
> 表示される料金は、**毎月の定期的な料金**で、選択した電話番号をリースするコストがカバーされています。 このビューに含まれていないのは**従量課金制のコスト**で、通話の発信または受信時に発生します。 価格表は、[こちらをご覧ください](../../concepts/pricing.md)。 これらのコストは、番号の種類と通話先によって異なります。 たとえば、シアトルの地域番号からニューヨークの地域番号への通話と同じ番号から英国の携帯電話番号への通話では、分単位の料金が異なる場合があります。

最後に、ページの下部にある [`Place order`] をクリックして確定します。

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Communication Services リソースのメイン ページを示すスクリーンショット。":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Azure portal で電話番号を検索する

[Azure portal](https://portal.azure.com) で、Azure Communication リソースに移動します。

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Communication Services リソースのメイン ページを示すスクリーンショット。":::

メニューの [Phone Numbers]\(電話番号\) タブを選択して、電話番号を管理します。

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="Communication Services リソースのメイン ページを示すスクリーンショット。":::

> [!NOTE]
> プロビジョニングされた番号がこのページに表示されるまでに数分かかる場合があります。

## <a name="troubleshooting"></a>トラブルシューティング

よくある質問と問題:

- 現在、電話番号の購入がサポートされているのは、米国だけです。 これは、リソースが関連付けられているサブスクリプションの請求先住所に基づきます。 現時点では、リソースを別のサブスクリプションに移動することはできません。

- 電話番号が削除されても、その電話番号はリリースされず、請求サイクルが終了するまで再購入することはできません。

- Communication Services リソースが削除されると同時に、そのリソースに関連付けられている電話番号が自動的にリリースされます。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、次の方法について学習しました。

> [!div class="checklist"]
> * 電話番号を購入する
> * 電話番号を管理する
> * 電話番号をリリースする

> [!div class="nextstepaction"]
> [SMS の送信](../telephony-sms/send.md)
> [通話の概要](../voice-video-calling/getting-started-with-calling.md)
