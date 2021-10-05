---
title: Microsoft AppSource (Azure Marketplace) 上で Power BI アプリ オファーを作成する
description: Microsoft AppSource (Azure Marketplace) 上で Power BI アプリ オファーを作成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 09/27/2021
ms.openlocfilehash: 6020ff6b6152fa559de5f6afc6f6560f1d7252ba
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129082530"
---
# <a name="create-a-power-bi-app-offer"></a>Power BI アプリ オファーを作成する

この記事では、Power BI アプリ オファーの作成方法について説明します。 すべてのオファーは認定手続きを受けます。そこで、ソリューションが標準要件と互換性を満たしているか、内容や使用方法が適切かどうかを確認します。

最初に、[パートナー センター](./create-account.md)でコマーシャル マーケットプレース アカウントを作成してコマーシャル マーケットプレース プログラムに登録します。

## <a name="before-you-begin"></a>開始する前に

[Power BI オファーの計画](marketplace-power-bi.md)に関するページを確認します。 これには、このオファーの技術的な要件の説明と、その作成時に必要となる情報と資産が記載されています。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. [パートナー センター](https://go.microsoft.com/fwlink/?linkid=2166002)にサインインします。

1. ホーム ページで、 **[Marketplace offers]\(Marketplace のオファー\)** タイルを選択します。

    [ ![パートナー センターのホーム ページにある [Marketplace offers]\(Marketplace のオファー\) タイルを示しています。](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. [Marketplace offers]\(Marketplace のオファー\) ページで、 **[新しいオファー]**  >  **[Power BI アプリ]** を選択します。

    [ ![[新しいオファー] ボタンから選択できるオファーの種類、Power BI アプリを示しています。](./media/power-bi/new-offer-power-bi-app-workspaces.png) ](./media/power-bi/new-offer-power-bi-app-workspaces.png#lightbox)

> [!IMPORTANT]
> オファーが公開された後にパートナー センターで行った編集は、そのオファーを再公開した後にのみ Microsoft AppSource に反映されます。 変更後は必ずオファーを再公開するようにしてください。

**[Power BI アプリ]** が表示されない場合、または有効にならない場合は、この種類のオファーを作成するためのアクセス許可がアカウントにありません。 開発者アカウントの登録など、この種類のオファーに対するすべての[要件](./marketplace-dynamics-365.md)を満たしていることを確認してください。

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
2. 左側のナビゲーション メニューで、 **[コマーシャル マーケットプレース]**  >  **[概要]** の順に選択します。
3. [概要] ページで、 **[+ 新しいオファー]**  >  **[Power BI アプリ]** を選択します。

    :::image type="content" source="media/power-bi/new-offer-power-bi-app.png" alt-text="左ペイン メニューと [新しいオファー] ボタン。":::

> [!IMPORTANT]
> オファーが公開された後にパートナー センターで行った編集は、そのオファーを再公開した後にのみ Microsoft AppSource に反映されます。 変更後は必ずオファーを再公開するようにしてください。

**[Power BI アプリ]** が表示されない場合、または有効にならない場合は、この種類のオファーを作成するためのアクセス許可がアカウントにありません。 開発者アカウントの登録など、この種類のオファーに対するすべての[要件](marketplace-dynamics-365.md)を満たしていることを確認してください。

---

## <a name="new-offer"></a>新しいプラン

**[オファー ID]** を入力します。 これは、アカウントのオファーごとに一意の識別子です。

- この ID は、オファーの Web アドレス内と Azure Resource Manager テンプレート内 (該当する場合) の顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ID にはハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、パブリッシャー ID が `testpublisherid` であれば、「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` になります。
- **[作成]** を選択した後で、オファー ID を変更することはできません。

**[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

- この名前は、AppSource では使用されません。 これは、顧客に表示されるオファー名やその他の値とは異なります。
- **[作成]** を選択した後にこの名前を変更することはできません。

**[作成]** を選択してオファーを生成します。 パートナー センターで **[オファーのセットアップ]** ページが開きます。

## <a name="alias"></a>エイリアス

パートナー センター内でのみこのオファーを参照するために使用する、わかりやすい名前を入力します。 このオファーのエイリアス (オファー作成時に入力した内容によって事前入力済み) は、マーケットプレースでは使用されず、顧客に表示されるオファー名とは異なります。 オファー名を後で更新する場合は、[オファー登録情報](power-bi-app-offer-listing.md)ページを参照してください。

## <a name="setup-details"></a>セットアップの詳細

このセクションは空です。Power BI アプリには適用されません。

## <a name="customer-leads"></a>潜在顧客

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

詳細については、「[コマーシャル マーケットプレース オファーからの顧客リード](partner-center-portal/commercial-marketplace-get-customer-leads.md)」を参照してください。

左側のナビゲーション メニューの次のタブである **[プロパティ]** に進む前に、 **[下書きの保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [オファーのプロパティを構成する](power-bi-app-properties.md)
