---
ms.openlocfilehash: 82e475c97aa2c68a33a48b04fe3e45fb13728b88
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629361"
---

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [アクティブな Communication Services リソース。](../../create-communication-resource.md)

## <a name="get-a-phone-number"></a>電話番号を取得する

番号のプロビジョニングを開始するには、[Azure portal](https://portal.azure.com) で Communication Services リソースに移動します。

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Communication Services リソースのメイン ページを示すスクリーンショット。":::

### <a name="search-for-available-phone-numbers"></a>利用可能な電話番号を検索する

リソース メニューで **[Phone Numbers]\(電話番号\)** ブレードに移動します。

:::image type="content" source="../../media/manage-phone-azure-portal-phone-page.png" alt-text="Communication Services リソースの電話のページを示すスクリーンショット。":::

**[Get]\(取得\)** ボタンをクリックしてウィザードを起動します。 **[Phone numbers]\(電話番号\)** ブレードのウィザードでは、自分のシナリオに最適な電話番号を選択するのに役立つ一連の質問が表示されます。 

まず、電話番号をプロビジョニングする **[Country/region]\(国/地域\)** を選択する必要があります。 国または地域を選択したら、自分のニーズに最も適した **[Use case]\(ユース ケース\)** を選択する必要があります。 

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers.png" alt-text="[Get phone numbers]\(電話番号の取得\) ビューを示すスクリーンショット。":::

### <a name="select-your-phone-number-features"></a>電話番号の機能を選択する

電話番号を構成する手順は、2 つに分けられます。 

1. [番号の種類](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)の選択
2. [番号機能の選択](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)

電話番号は、**局番** と **無料電話番号** の 2 種類から選択できます。 番号の種類を選択したら、機能を選択することができます。

この例では、番号の種類として **[Toll-free]\(無料電話番号\)** を、機能として **[Outbound calling]\(発信通話\)** と **[Inbound and Outbound SMS]\(受信および送信 SMS\)** を選択しました。

:::image type="content" source="../../media/manage-phone-azure-portal-select-plans.png" alt-text="機能の選択ビューのスクリーンショット。":::

ここで、ページの下部にある **[Next: Numbers]\(次へ: 番号\)** ボタンをクリックして、プロビジョニングする電話番号をカスタマイズします。

### <a name="customizing-phone-numbers"></a>電話番号をカスタマイズする

**[Numbers]\(番号\)** ページで、プロビジョニングする電話番号をカスタマイズします。

:::image type="content" source="../../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="[Numbers]\(番号\) ページを示すスクリーンショット。":::

> [!NOTE]
> このクイックスタートでは、番号の種類が **[Toll-free]\(無料電話番号\)** のカスタマイズ フローを示しています。 番号の種類として **[Geographic]\(局番\)** を選択した場合は操作が多少異なる場合がありますが、最終的な結果は同じになります。

利用可能な市外局番の一覧から **市外局番** を選択し、プロビジョニングする数量を入力した後、 **[Search]\(検索\)** をクリックして、選択した要件を満たす番号を見つけます。 自分のニーズに合った電話番号が月額料金と共に表示されます。

:::image type="content" source="../../media/manage-phone-azure-portal-found-numbers.png" alt-text="予約済み番号が表示された番号の選択ページを示すスクリーンショット。":::

> [!NOTE]
> 使用できるかどうかは、選択した番号の種類、場所、機能によって異なります。
> トランザクションが期限切れになるまでの短い間、番号は予約されています。 トランザクションの期限が切れた場合は、番号を再選択する必要があります。

購入の概要を表示して注文を行うには、ページの下部にある **[Next: Summary]\(次へ: 概要\)** ボタンをクリックします。

### <a name="purchase-phone-numbers"></a>電話番号を購入する

概要ページでは、番号の種類、機能、電話番号、および総月額料金を確認して、電話番号をプロビジョニングします。

> [!NOTE]
> 表示される料金は、**毎月の定期的な料金** で、選択した電話番号をリースするコストがカバーされています。 このビューに含まれていないのは **従量課金制のコスト** で、通話の発信または受信時に発生します。 価格表は、[こちらをご覧ください](../../../concepts/pricing.md)。 これらのコストは、番号の種類と通話先によって異なります。 たとえば、シアトルの地域番号からニューヨークの地域番号への通話と同じ番号から英国の携帯電話番号への通話では、分単位の料金が異なる場合があります。

最後に、ページの下部にある **[Place order]\(注文\)** をクリックして確定します。

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="番号の種類、機能、電話番号、総月額料金が表示された概要ページを示すスクリーンショット。":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Azure portal で電話番号を検索する

[Azure portal](https://portal.azure.com) で、Azure Communication リソースに移動します。

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Communication Services リソースのメイン ページを示すスクリーンショット。":::

メニューの [Phone Numbers]\(電話番号\) ブレードを選択して、電話番号を管理します。

:::image type="content" source="../../media/manage-phone-azure-portal-phones.png" alt-text="Communication Services リソースの電話番号のページを示すスクリーンショット。":::

> [!NOTE]
> プロビジョニングされた番号がこのページに表示されるまでに数分かかる場合があります。


### <a name="update-phone-number-capabilities"></a>電話番号機能を更新する

**[Numbers]\(番号\)** ページで、電話番号を選択してその構成を行うことができます。

:::image type="content" source="../../media/manage-phone-azure-portal-capability-update.png" alt-text="機能の更新ページのスクリーンショット。":::

目的の機能をオプションから選択し、 **[Confirm]\(確定\)** をクリックして選択内容を適用します。

### <a name="release-phone-number"></a>電話番号を解放する

電話番号は、 **[Numbers]\(番号\)** ページで解放できます。

:::image type="content" source="../../media/manage-phone-azure-portal-release-number.png" alt-text="電話番号の解放ページを示すスクリーンショット。":::

解放する電話番号を選択し、 **[Release]\(解放\)** ボタンをクリックします。