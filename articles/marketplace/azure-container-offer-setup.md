---
title: Azure Marketplace で Azure Container オファーを作成する
description: Azure Marketplace で Azure Container オファーを作成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 09/27/2021
ms.openlocfilehash: b09500142878ee383ccc20e8b62ed22b0f9478c5
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129079878"
---
# <a name="create-an-azure-container-offer"></a>Azure Container オファーを作成する

この記事では、Azure Container オファーの作成方法を説明します。 すべてのオファーは認定手続きを受けます。そこで、ソリューションが標準要件と互換性を満たしているか、内容や使用方法が適切かどうかを確認します。

最初に、[パートナー センター](./create-account.md)でコマーシャル マーケットプレース アカウントを作成してコマーシャル マーケットプレース プログラムに登録します。

## <a name="before-you-begin"></a>始める前に

[Azure Container オファーの計画](marketplace-containers.md)に関する記事をご確認ください。 オファーの技術的要件を説明し、オファー作成に必要な情報と資産を挙げています。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。

1. ホーム ページで、 **[マーケットプレースのオファー]** タイルを選択します。

    [ ![パートナー センターのホーム ページにある [マーケットプレースのオファー] タイルを示す図。](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. [マーケットプレースのオファー] ページで、 **[+ 新しいオファー]**  >  **[Azure コンテナー]** の順に選択します。

    [ ![左ペイン メニューのオプションと [新しいオファー] ボタン。](./media/azure-container/new-offer-azure-container-workspaces.png) ](./media/azure-container/new-offer-azure-container-workspaces.png#lightbox)

> [!IMPORTANT]
> パートナー センターで発行済みのオファーを編集した場合、そのオファーを再発行するまで Azure Marketplce での表示には反映されません。 オファーを変更した後は必ずそれを再発行します。

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
2. 左側のナビゲーション メニューで、 **[商業マーケットプレース]**  >  **[概要]** の順に選択します。
3. [概要] ページで、 **[+ 新しいオファー]**  >  **[Azure Container]** の順に選択します。

    :::image type="content" source="media/azure-container/new-offer-azure-container.png" alt-text="左ペイン メニューと [新しいオファー] ボタン。":::

> [!IMPORTANT]
> パートナー センターで発行済みのオファーを編集した場合、そのオファーを再発行するまで Azure Marketplce での表示には反映されません。 オファーを変更した後は必ずそれを再発行します。

---

## <a name="new-offer"></a>新しいプラン

**[オファー ID]** を入力します。 これは、アカウントのオファーごとに一意の識別子です。

- この ID は、オファーの Web アドレス内と Azure Resource Manager テンプレート内 (該当する場合) の顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ID にはハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、パブリッシャー ID が `testpublisherid` であれば、「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` になります。
- **[作成]** を選択した後で、オファー ID を変更することはできません。

**[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

- この名前は、Azure Marketplace では使用されません。 顧客に対し表示されるオファー名などとは別のものです。

**[作成]** を選択してオファーを生成してください。 パートナー センターで **[オファーのセットアップ]** ページが開きます。

## <a name="alias"></a>エイリアス

パートナー センター内でのみこのオファーを参照するために使用する、わかりやすい名前を入力します。 このオファーのエイリアス (オファー作成時に入力した内容によって事前入力済み) は、マーケットプレースでは使用されず、顧客に表示されるオファー名とは異なります。 オファー名を後から変更する方法は、[オファーの掲載](azure-container-offer-listing.md)に関するページをご覧ください。

## <a name="customer-leads"></a>潜在顧客

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

詳細については、「[コマーシャル マーケットプレース オファーからの顧客リード](partner-center-portal/commercial-marketplace-get-customer-leads.md)」を参照してください。

左側のナビゲーション メニューの次のタブである **[プロパティ]** に進む前に、 **[下書きの保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [オファーのプロパティを構成する](azure-container-properties.md) 
- [オファーの掲載のベスト プラクティス](gtm-offer-listing-best-practices.md)
