---
title: Azure Marketplace で Azure Container オファーを作成する
description: Azure Marketplace で Azure Container オファーを作成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 04/21/2021
ms.openlocfilehash: a8c10ea5bdfab20ed5a250fba656527cd7935527
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108129407"
---
# <a name="create-an-azure-container-offer"></a>Azure Container オファーを作成する

この記事では、Azure Container オファーの作成方法を説明します。 すべてのオファーは認定手続きを受けます。そこで、ソリューションが標準要件と互換性を満たしているか、内容や使用方法が適切かどうかを確認します。

最初に、[パートナー センター](./create-account.md)でコマーシャル マーケットプレース アカウントを作成してコマーシャル マーケットプレース プログラムに登録します。

## <a name="before-you-begin"></a>始める前に

[Azure Container オファーの計画](marketplace-containers.md)に関する記事をご確認ください。 オファーの技術的要件を説明し、オファー作成に必要な情報と資産を挙げています。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
2. 左側のナビゲーション メニューで、 **[商業マーケットプレース]**  >  **[概要]** の順に選択します。
3. [概要] ページで、 **[+ 新しいオファー]**  >  **[Azure Container]** の順に選択します。

    :::image type="content" source="media/azure-container/new-offer-azure-container.png" alt-text="左ペイン メニューと [新しいオファー] ボタン。":::

> [!IMPORTANT]
> パートナー センターで発行済みのオファーを編集した場合、そのオファーを再発行するまで Azure Marketplce での表示には反映されません。 オファーを変更した後は必ずそれを再発行します。

## <a name="new-offer"></a>新しいプラン

**[オファー ID]** を入力します。 これは、アカウントのオファーごとに一意の識別子です。

- (表示可能であれば) この ID は、オファーの Web アドレスと Azure Resource Manager テンプレートで顧客に対して表示されます。
- 使用できるのは小文字と数字だけです。 ID にはハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、パブリッシャー ID が `testpublisherid` であれば、「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` になります。
- **[作成]** を選択した後で、オファー ID を変更することはできません。

**[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

- この名前は、Azure Marketplace では使用されません。 顧客に対し表示されるオファー名などとは別のものです。

**[作成]** を選択してオファーを生成してください。 パートナー センターで **[オファーのセットアップ]** ページが開きます。

## <a name="alias"></a>エイリアス

パートナー センター内でのみこのオファーを参照するために使用する、わかりやすい名前を入力します。 オファーの別名 (オファー作成時の入力内容が初期値) は、顧客に対し表示されるオファー名とは別のものであり、マーケットプレースでは使用されません。 オファー名を後から変更する方法は、[オファーの掲載](azure-container-offer-listing.md)に関するページをご覧ください。

## <a name="customer-leads"></a>潜在顧客

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

詳しくは「[コマーシャル マーケットプレース オファーからの顧客リード](partner-center-portal/commercial-marketplace-get-customer-leads.md)」をご覧ください。

左側のナビゲーション メニューの次のタブである **[プロパティ]** に進む前に、 **[下書きの保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [オファーのプロパティを構成する](azure-container-properties.md)
- [オファーの掲載のベスト プラクティス](gtm-offer-listing-best-practices.md)