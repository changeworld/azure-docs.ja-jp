---
title: Azure Marketplace 上で IoT Edge モジュール オファーを作成する
description: Azure Marketplace 上で IoT Edge モジュール オファーを作成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 05/21/2021
ms.openlocfilehash: 13557124999faeb68cbe009e4bbc3531023c12f9
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111971314"
---
# <a name="create-an-iot-edge-module-offer"></a>IoT Edge モジュール オファーを作成する

この記事では、IoT (モノのインターネット) Edge モジュール オファーを作成する方法について説明します。 すべてのオファーは認定手続きを受けます。そこで、ソリューションが標準要件と互換性を満たしているか、内容や使用方法が適切かどうかを確認します。

最初に、[パートナー センター](./create-account.md)でコマーシャル マーケットプレース アカウントを作成してコマーシャル マーケットプレース プログラムに登録します。

## <a name="before-you-begin"></a>開始する前に

[IoT Edge モジュール オファーの計画](marketplace-iot-edge.md)に関するページを確認します。 これには、このオファーの技術的な要件の説明と、その作成時に必要となる情報と資産が記載されています。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
2. 左側のナビゲーション メニューで、 **[コマーシャル マーケットプレース]**  >  **[概要]** の順に選択します。
3. [概要] ページで、 **[+ 新しいオファー]**  >  **[IoT Edge モジュール]** の順に選択します。

    :::image type="content" source="media/iot-edge/new-offer-iot-edge.png" alt-text="左ペイン メニューと [新しいオファー] ボタン。":::

> [!IMPORTANT]
> パートナー センターで発行済みのオファーを編集した場合、そのオファーを再発行するまで Azure Marketplce での表示には反映されません。 オファーを変更した後は必ずそれを再発行します。

## <a name="new-offer"></a>新しいプラン

**[オファー ID]** を入力します。 これは、アカウントのオファーごとに一意の識別子です。

- この ID は、オファーの Web アドレス内と Azure Resource Manager テンプレート内 (該当する場合) の顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ID にはハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、パブリッシャー ID が `testpublisherid` であれば、「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` になります。
<!--- The Offer ID combined with the Publisher ID must be under 50 characters in length.-->
- **[作成]** を選択した後で、オファー ID を変更することはできません。

**[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

- この名前は、AppSource では使用されません。 これは、顧客に表示されるオファー名やその他の値とは異なります。
- **[作成]** を選択した後にこの名前を変更することはできません。

**[作成]** を選択してオファーを生成します。 パートナー センターで **[オファーのセットアップ]** ページが開きます。

## <a name="alias"></a>エイリアス

パートナー センター内でのみこのオファーを参照するために使用する、わかりやすい名前を入力します。 このオファーのエイリアス (オファー作成時に入力した内容によって事前入力済み) は、マーケットプレースでは使用されず、顧客に表示されるオファー名とは異なります。 オファー名を後から変更する方法は、[オファーの掲載](iot-edge-offer-listing.md)に関するページをご覧ください。

## <a name="customer-leads"></a>潜在顧客

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

詳細については、「[コマーシャル マーケットプレース オファーからの顧客リード](partner-center-portal/commercial-marketplace-get-customer-leads.md)」を参照してください。

左側のナビゲーション メニューの次のタブである **[プロパティ]** に進む前に、 **[下書きの保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [オファーのプロパティを構成する](iot-edge-properties.md)
- [オファーの掲載のベスト プラクティス](gtm-offer-listing-best-practices.md)