---
title: Microsoft AppSource 用にパートナー センターで Power BI ビジュアル オファーを作成する
description: パートナー センターで Power BI ビジュアル オファーを作成する方法について説明します。
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 916013d7bde10e63985324d736a755183200bf3e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079360"
---
# <a name="create-a-power-bi-visual-offer"></a>Power BI ビジュアル オファーを作成する

この記事では、パートナー センターを使用して、他のユーザーが検索して使用できるように Power BI ビジュアル オファーを [Microsoft AppSource](https://appsource.microsoft.com) に送信する方法について説明します。

開始する前に、[パートナー センター](./create-account.md)でコマーシャル マーケットプレース アカウントを作成し、それがコマーシャル マーケットプレース プログラムに登録されているようにします。

## <a name="before-you-begin"></a>開始する前に

「[Power BI ビジュアル オファーを計画する](marketplace-power-bi-visual.md)」を確認してください。 これには、このオファーの技術的な要件の説明と、その作成時に必要となる情報と資産が記載されています。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. <bpt id="p1">[</bpt>パートナー センター<ept id="p1">](https://partner.microsoft.com/dashboard/home)</ept>にサインインします。

1. ホーム ページで、 **[Marketplace offers]\(Marketplace のオファー\)** タイルを選択します。

    [ ![パートナー センターのホーム ページにある [Marketplace offers]\(Marketplace のオファー\) タイルを示しています。](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. [Marketplace offers]\(Marketplace のオファー\) ページで、 **[新しいオファー]**  >  **[Power BI 視覚エフェクト]** を選択します。

    [ ![左ペイン メニューのオプションと [新しいオファー] ボタンを示しています。](media/power-bi-visual/new-offer-power-bi-visual-workspaces.png) ](media/power-bi-visual/new-offer-power-bi-visual-workspaces.png#lightbox)

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

1. <bpt id="p1">[</bpt>パートナー センター<ept id="p1">](https://partner.microsoft.com/dashboard/home)</ept>にサインインします。
1. 左側のナビゲーション メニューで、 **[コマーシャル マーケットプレース]**  >  **[概要]** の順に選択します。
1. [概要] ページで、 **[+ 新しいオファー]**  >  **[Power BI 視覚エフェクト]** を選択します。

    :::image type="content" source="media/power-bi-visual/new-offer-power-bi-visual.png" alt-text="左ペイン メニューのオプションと [新しいオファー] ボタンを示しています。":::

---

> [!IMPORTANT]
> オファーが公開された後にパートナー センターで行った編集は、そのオファーを再公開した後にのみ AppSource に反映されます。 変更後は必ずオファーを再公開するようにしてください。

## <a name="new-offer"></a>新しいプラン

**[オファー ID]** を入力します。 これは、アカウントのオファーごとに一意の識別子です。

- この ID は、オファーの Web アドレス内と Azure Resource Manager テンプレート内 (該当する場合) の顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ID にはハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、パブリッシャー ID が `testpublisherid` であれば、「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` になります。
- **[作成]** を選択した後で、オファー ID を変更することはできません。
- オファー ID は、パートナー センター内の他のすべての Power BI ビジュアル オファーのリスト内で一意である必要があります。

**[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

- この名前は、AppSource では使用されません。 顧客に対し表示されるオファー名などとは別のものです。

**[作成]** を選択してオファーを生成してください。 パートナー センターで **[オファーのセットアップ]** ページが開きます。

## <a name="setup-details"></a>セットアップの詳細

**追加購入** では、オファーでサービスの購入や追加のアプリ内課金が必要かどうかを選択します。

**Power BI 証明書** (任意) については、説明を注意深く読んだ上で、Power BI 証明書を要求する場合は、このチェック ボックスをオンにしてください。 [認定済み](/power-bi/developer/visuals/power-bi-custom-visuals-certified)の Power BI ビジュアルは、Microsoft Power BI チームによってテストおよび承認された特定のコード要件を満たしています。 認証プロセスには追加で時間がかかり、オファーの公開が遅れる可能性があるため、認証を申請する "*前に*"、Power BI ビジュアルの申請と公開することをお勧めします。

## <a name="customer-leads"></a>潜在顧客

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

CRM への接続は任意です。 詳細については、「[コマーシャル マーケットプレース オファーからの顧客リード](partner-center-portal/commercial-marketplace-get-customer-leads.md)」を参照してください。

左側のナビゲーション メニューの次のタブである **[プロパティ]** に進む前に、 **[下書きの保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [**オファーのプロパティ**](power-bi-visual-properties.md)