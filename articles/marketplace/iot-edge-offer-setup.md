---
title: Azure Marketplace 上で IoT Edge モジュール オファーを作成する
description: Azure Marketplace 上で IoT Edge モジュール オファーを作成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 09/27/2021
ms.openlocfilehash: 4817502ac03074bd1521724fe1d5e76259f3add4
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084012"
---
# <a name="create-an-iot-edge-module-offer"></a>IoT Edge モジュール オファーを作成する

この記事では、IoT (モノのインターネット) Edge モジュール オファーを作成する方法について説明します。 すべてのオファーは認定手続きを受けます。そこで、ソリューションが標準要件と互換性を満たしているか、内容や使用方法が適切かどうかを確認します。

最初に、<bpt id="p1">[</bpt>パートナー センター<ept id="p1">](./create-account.md)</ept>でコマーシャル マーケットプレース アカウントを作成してコマーシャル マーケットプレース プログラムに登録します。

## <a name="before-you-begin"></a>開始する前に

<bpt id="p1">[</bpt>IoT Edge モジュール オファーの計画<ept id="p1">](marketplace-iot-edge.md)</ept>に関するページを確認します。 これには、このオファーの技術的な要件の説明と、その作成時に必要となる情報と資産が記載されています。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. <bpt id="p1">[</bpt>パートナー センター<ept id="p1">](https://go.microsoft.com/fwlink/?linkid=2166002)</ept>にサインインします。

1. ホーム ページで、 **[Marketplace offers]\(Marketplace のプラン\)** タイルを選択します。

    [ ![パートナー センターのホーム ページにある [Marketplace offers]\(Marketplace のプラン\) タイルの画像。](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. [Marketplace offers]\(Marketplace のプラン\) ページで、 **[+ 新しいプラン]**  >  **[IoT Edge モジュール]** を選択します。

    [ ![左ペイン メニューのオプションと [新しいプラン] ボタン。](./media/iot-edge/new-offer-iot-edge-workspaces.png) ](./media/iot-edge/new-offer-iot-edge-workspaces.png#lightbox)

> [!IMPORTANT]
> パートナー センターで発行済みのオファーを編集した場合、そのオファーを再発行するまで Azure Marketplce での表示には反映されません。 オファーを変更した後は必ずそれを再発行します。

#### <a name="current-view"></a><bpt id="p1">[</bpt>現在のビュー<ept id="p1">](#tab/current-view)</ept>

1. <bpt id="p1">[</bpt>パートナー センター<ept id="p1">](https://partner.microsoft.com/dashboard/home)</ept>にサインインします。
2. 左側のナビゲーション メニューで、 <bpt id="p1">**</bpt>[コマーシャル マーケットプレース]<ept id="p1">**</ept> <ph id="ph1"> > </ph> <bpt id="p2">**</bpt>[概要]<ept id="p2">**</ept> の順に選択します。
3. [概要] ページで、 <bpt id="p1">**</bpt>[+ 新しいオファー]<ept id="p1">**</ept> <ph id="ph1"> > </ph> <bpt id="p2">**</bpt>[IoT Edge モジュール]<ept id="p2">**</ept> の順に選択します。

    <bpt id="p1">:::image type="content" source="media/iot-edge/new-offer-iot-edge.png" alt-text="</bpt>左ペイン メニューと [新しいオファー] ボタン。<ept id=&quot;p1&quot;>":::</ept>

> [!IMPORTANT]
> パートナー センターで発行済みのオファーを編集した場合、そのオファーを再発行するまで Azure Marketplce での表示には反映されません。 オファーを変更した後は必ずそれを再発行します。

---

## <a name="new-offer"></a>新しいプラン

<bpt id="p1">**</bpt>[オファー ID]<ept id="p1">**</ept> を入力します。 これは、アカウントのオファーごとに一意の識別子です。

- この ID は、オファーの Web アドレス内と Azure Resource Manager テンプレート内 (該当する場合) の顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ID にはハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、パブリッシャー ID が <ph id="ph1">`testpublisherid`</ph> であれば、「<bpt id="p1">**</bpt>test-offer-1<ept id="p1">**</ept>」と入力すると、オファーの Web アドレスは <ph id="ph2">`https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`</ph> になります。
<!--- The Offer ID combined with the Publisher ID must be under 50 characters in length.-->
- <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択した後で、オファー ID を変更することはできません。

<bpt id="p1">**</bpt>[オファーのエイリアス]<ept id="p1">**</ept> を入力します。 これは、パートナー センター内でオファーに使用される名前です。

- この名前は、AppSource では使用されません。 これは、顧客に表示されるオファー名やその他の値とは異なります。
- <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択した後にこの名前を変更することはできません。

<bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択してオファーを生成します。 パートナー センターで <bpt id="p1">**</bpt>[オファーのセットアップ]<ept id="p1">**</ept> ページが開きます。

## <a name="alias"></a>エイリアス

パートナー センター内でのみこのオファーを参照するために使用する、わかりやすい名前を入力します。 このオファーのエイリアス (オファー作成時に入力した内容によって事前入力済み) は、マーケットプレースでは使用されず、顧客に表示されるオファー名とは異なります。 オファー名を後から変更する方法は、<bpt id="p1">[</bpt>オファーの掲載<ept id="p1">](iot-edge-offer-listing.md)</ept>に関するページをご覧ください。

## <a name="customer-leads"></a>潜在顧客

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

詳細については、「<bpt id="p1">[</bpt>コマーシャル マーケットプレース オファーからの顧客リード<ept id="p1">](partner-center-portal/commercial-marketplace-get-customer-leads.md)</ept>」を参照してください。

左側のナビゲーション メニューの次のタブである <bpt id="p2">**</bpt>[プロパティ]<ept id="p2">**</ept> に進む前に、 <bpt id="p1">**</bpt>[下書きの保存]<ept id="p1">**</ept> を選択します。

## <a name="next-steps"></a>次のステップ

- <bpt id="p1">[</bpt>オファーのプロパティを構成する<ept id="p1">](iot-edge-properties.md)</ept>
- <bpt id="p1">[</bpt>オファーの掲載のベスト プラクティス<ept id="p1">](gtm-offer-listing-best-practices.md)</ept>
