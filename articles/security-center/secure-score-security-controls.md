---
title: Azure Security Center 内のセキュリティ スコア
description: Azure Security Center のセキュリティ スコアとそのセキュリティ コントロールの説明
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: memildin
ms.openlocfilehash: 24822777b06fadf87ca446d9b7ff8ba4df34adc5
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007680"
---
# <a name="secure-score-in-azure-security-center"></a>Azure Security Center 内のセキュリティ スコア

## <a name="introduction-to-secure-score"></a>セキュリティ スコアの概要

Azure Security Center には主に次の 2 つの目標があります。 

- 現在のセキュリティ状況を把握すること
- セキュリティを効率的かつ効果的に向上させること

これらの目標を達成できるようにする Security Center の中心となる機能が、**セキュリティ スコア** です。

Security Center は、セキュリティの問題について、リソース、サブスクリプション、および組織を継続的に評価します。 その後、すべての結果を 1 つのスコアに集約して、現在のセキュリティの状況を一目で確認できるようにします。スコアが高くなるほど、識別されたリスク レベルが低下します。

セキュリティ スコアは、Azure portal のページにパーセント値として表示されますが、基になる値も明確に示されます。

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="ポータルに表示された全体的なセキュリティ スコア":::

セキュリティを強化するには、スコアを上げるために必要な未処理のアクションについて Security Center の推奨事項に関するページを参照してください。 各推奨事項には、特定の問題を修復するための手順が含まれています。

推奨事項は、**セキュリティ コントロール** にグループ化されています。 各コントロールは、関連するセキュリティの推奨事項の論理グループであり、脆弱な攻撃対象領域を反映しています。 コントロール内の 1 つのリソースに関する推奨事項を "*すべて*" 修復した場合にのみ、スコアが向上します。 組織が個々の攻撃面をどの程度セキュリティで保護できているかを確認するには、各セキュリティ コントロールのスコアを確認します。

詳細については、「[セキュリティ スコアの計算方法](secure-score-security-controls.md#how-your-secure-score-is-calculated)」を参照してください。 


## <a name="access-your-secure-score"></a>セキュリティ スコアにアクセスする

次のセクションで説明するように、Azure portal またはプログラムによって、全体的なセキュリティ スコアとサブスクリプションごとのスコアを確認できます。

- [ポータルからセキュリティ スコアを取得する](#get-your-secure-score-from-the-portal)
- [REST API からセキュリティ スコアを取得する](#get-your-secure-score-from-the-rest-api)
- [Azure Resource Graph (ARG) からセキュリティ スコアを取得する](#get-your-secure-score-from-azure-resource-graph-arg)

### <a name="get-your-secure-score-from-the-portal"></a>ポータルからセキュリティ スコアを取得する

ポータルで、Security Center にはスコアが目立つように表示されます。これは、Security Center の [概要] ページの最初の主なタイルです。 このタイルを選択すると、スコアがサブスクリプション別に分類されている専用のセキュリティ スコア ページに移動ます。 サブスクリプションを 1 つ選択すると、優先度が設定された推奨事項と、修復によるスコアへの影響を示す詳細な一覧が表示されます。 

要約すると、Security Center のポータル ページの次の場所に、セキュリティ スコアが表示されます。

- Security Center の **[概要]** にあるタイル (メイン ダッシュボード):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="Security Center のダッシュボード上のセキュリティ スコア":::

- 専用の **[セキュリティ スコア]** ページで、サブスクリプションと管理グループのセキュリティ スコアを確認できます。

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Security Center の [セキュリティ スコア] ページのサブスクリプションのセキュリティ スコア":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Security Center の [セキュリティ スコア] ページの管理グループのセキュリティ スコア":::

    > [!NOTE]
    > 十分なアクセス許可を持っていない管理グループについては、スコアが "制限付き" として表示されます。 

- **[推奨事項]** ページの上部:

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="Security Center の [推奨事項] ページのセキュリティ スコア":::

### <a name="get-your-secure-score-from-the-rest-api"></a>REST API からセキュリティ スコアを取得する

スコアには、Secure Scores API を使用してアクセスできます。 この API メソッドを使用すると、データに対してクエリを実行したり、一定期間のセキュリティ スコアをレポートする独自のメカニズムを構築したりするための柔軟性が得られます。 たとえば、[Secure Scores API](/rest/api/securitycenter/securescores) を使用して、特定のサブスクリプションのスコアを取得できます。 また、[Secure Score Controls API](/rest/api/securitycenter/securescorecontrols) を使用して、サブスクリプションのセキュリティ コントロールと現在のスコアを一覧表示できます。

![API を使用して 1 つのセキュリティ スコアを取得する](media/secure-score-security-controls/single-secure-score-via-api.png)

Secure Scores API を使用して構築されたツールの例については、[GitHub コミュニティのセキュリティ スコアの領域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)を参照してください。 

### <a name="get-your-secure-score-from-azure-resource-graph-arg"></a>Azure Resource Graph (ARG) からセキュリティ スコアを取得する

Azure Resource Graph を使用すると、堅牢なフィルター処理、グループ化、および並べ替え機能を使用して、クラウド環境全体のリソース情報にすばやくアクセスできます。 これは、Azure サブスクリプション全体の情報を、プログラムから、または Azure portal 内ですばやく効率的に照会する方法です。 [Azure Resource Graph の詳細についてさらに学習します](../governance/resource-graph/index.yml)。

ARG を使用して複数のサブスクリプションのセキュリティ スコアにアクセスするには:

1. Azure portal から **Azure Resource Graph Explorer** を開きます。

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Azure Resource Graph エクスプローラーの起動** 推奨ページ" :::

1. Kusto クエリを入力します (詳細については、次の例を参照してください)。

    - このクエリによって、サブスクリプション ID、現在のスコア (ポイントおよびパーセント表記)、およびサブスクリプションの最大スコアが返されます。 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - このクエリによって、すべてのセキュリティ コントロールの状態が返されます。 各コントロールについて、異常なリソースの数、現在のスコア、および最大スコアを取得します。 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. **[クエリの実行]** を選択します。




## <a name="tracking-your-secure-score-over-time"></a>セキュリティ スコアの経時的な追跡

Pro アカウントを使用している Power BI ユーザーの場合は、 **[Secure Score Over Time]\(経時的なセキュリティ スコア\)** Power BI ダッシュボードを使用して、セキュリティ スコアを経時的に追跡し、変更がないか調査できます。

> [!TIP]
> このダッシュボードや、セキュリティ スコアをプログラムで操作する他のツールは、GitHub の Azure Security Center コミュニティの専用領域を参照してください: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

ダッシュボードには、セキュリティの状態を分析するために役立つ次の 2 つのレポートが含まれています。

- **[リソースの概要]** - リソースの正常性に関する概要データが提供されます。
- **[Secure Score Summary]\(セキュリティ スコアの概要\)** - スコアの進捗状況に関する集計データが提供されます。 スコアの変化を確認するには、[Secure score over time per subscription]\(サブスクリプションごとの経時的なセキュリティ スコア\) グラフを使用します。 スコアが大幅に変化した場合は、[detected changes that may affect your secure score]\(検出された変更の中でセキュリティ スコアに影響する可能性のあるもの\) の表を調べ、変更の原因となった可能性のある変更を確認します。 この表は、削除されたリソース、新しくデプロイされたリソース、または推奨事項のいずれかについてセキュリティの状態が変化したリソースを示しています。

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="経時的なセキュリティ スコアを追跡し、変更を調査するためのオプションの [Secure Score Over Time]\(経時的なセキュリティ スコア\) Power BI ダッシュボード":::





## <a name="how-your-secure-score-is-calculated"></a>セキュリティ スコアの計算方法 

総合的なセキュリティ スコアに対する各セキュリティ コントロールの影響は、[推奨事項] ページで明確に示されています。

[![強化されたセキュリティ スコアによるセキュリティ コントロールの導入](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

セキュリティ コントロールで取得可能なポイントをすべて取得するには、すべてのリソースがセキュリティ コントロール内のすべてのセキュリティの推奨事項に準拠している必要があります。 たとえば、Security Center には、管理ポートをセキュリティで保護する方法に関する推奨事項が複数あります。 今後は、セキュリティ スコアを改善するには、これらをすべて修復する必要があります。

たとえば、"システム更新プログラムの適用" というセキュリティ コントロールのスコアは最大 6 ポイントです。これは、コントロールの潜在的な増加値に関するヒントに表示されます。

[![セキュリティ コントロール "システム更新プログラムの適用"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

このコントロール (システム更新プログラムの適用) の最大スコアは、常に 6 です。 この例では、50 のリソースがあります。 このため、最大スコアを 50 で割ると、各リソースが 0.12 ポイントずつ貢献しているという結果になります。 

* **上昇の可能性** (0.12 x 8 (正常な状態ではないリソース数) = 0.96) - コントロール内で取得できる残りのポイント数。 このコントロールですべての推奨事項を修復した場合、スコアは 2% 上昇します (この場合、0.96 ポイントは 1 に切り上げられます)。 
* **現在のスコア** (0.12 x 42 (正常な状態のリソース数) = 5.04) - このコントロールの現在のスコア。 合計スコアは、各コントロールを基にしています。 この例のコントロールは、現在の合計セキュリティ スコアに 5.04 ポイント貢献しています。
* **最大スコア** - コントロール内のすべての推奨事項を完了することによって得られるポイントの最大数です。 コントロールの最大スコアは、そのコントロールの相対的な有意性を示します。 最大スコア値を使用して問題をトリアージし、最初に対処すべき問題を決定します。 


### <a name="calculations---understanding-your-score"></a>計算 - スコアを理解する

|メトリック|式と例|
|-|-|
|**セキュリティ コントロールの現在のスコア**|<br>![セキュリティ コントロールのスコアを計算するための式](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>セキュリティ スコアは、個々のセキュリティ コントロールを基にしています。 コントロールの現在のスコアは、コントロール内の推奨事項によって影響を受ける各リソースを基にしています。 各コントロールの現在のスコアは、コントロール *内* でリソースの状態を測定したものです。<br>![セキュリティ コントロールの現在のスコアを計算するときに使用される値を示すヒント](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>この例では、最大スコア 6 は、正常なリソースと異常なリソースの合計値である 78 で割ることになります。<br>6 / 78 = 0.0769<br>これを正常なリソースの数 (4) で乗算すると、次に示す現在のスコアになります。<br>0.0769 * 4 = **0.31**<br><br>|
|**セキュリティ スコア**<br>1 つのサブスクリプション|<br>![サブスクリプションのセキュリティ スコアを計算するための式](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![すべてのコントロールを有効にした単一サブスクリプションのセキュア スコア](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>この例では、すべてのセキュリティ コントロールを適用可能な 1 つのサブスクリプションがあります (潜在的な最大スコアは 60 ポイントです)。 スコアには取得可能な 60 ポイントのうち 28 ポイントが示され、残りの 32 ポイントは、セキュリティ コントロールの "潜在的な増加スコア" の数値に反映されます。<br>![コントロールの一覧と潜在的な増加スコア](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**セキュリティ スコア**<br>複数のサブスクリプション|<br>![複数のサブスクリプションのセキュリティ スコアを計算するための式](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>複数のサブスクリプションに対して結合されたスコアを計算する場合、Security Center には各サブスクリプションの "*重み*" が含まれます。 サブスクリプションの相対的な重みは、リソースの数などの要素に基づいて Security Center によって決定されます。<br>各サブスクリプションの現在のスコアは、1 つのサブスクリプションの場合と同じ方法で計算されますが、式に示されているように重みが適用されます。<br>複数のサブスクリプションを表示する場合、セキュリティ スコアは、すべての有効なポリシー内のすべてのリソースを評価し、各セキュリティ コントロールの最大スコアに対するその組み合わせの影響をグループ化します。<br>![すべてのコントロールが有効な複数のサブスクリプションのセキュリティ スコア](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>結合されたスコアは平均では **ありません**。これは、すべてのサブスクリプションのすべてのリソースの状態の体制を評価したものです。<br>ここでも、[推奨事項] ページにアクセスして、取得可能なポイントを追加すると、現在のスコア (24) と利用可能な最大スコア (60) の差を確認できます。|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>セキュリティ スコアの計算に含まれる推奨事項

セキュリティ スコアに影響するのは、組み込みの推奨事項のみです。

**プレビュー** のフラグが設定されている推奨事項は、セキュリティ スコアの計算からは除外されます。 それでも、その推奨事項はプレビュー期間が終了した時点でスコアに反映されるため、可能な限り修復しておく必要があります。

プレビューの推奨事項の例を次に示します。

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="プレビュー フラグが設定された推奨事項":::

## <a name="improve-your-secure-score"></a>セキュリティ スコアを向上させる

セキュリティ スコアを向上させるには、推奨事項リストのセキュリティの推奨事項を修復してください。 各推奨事項は、リソースごとに手動で修復するか、リソースのグループに推奨設定の修復を迅速に適用するために **[クイック修正]** オプション (使用可能な場合) を使用して修復できます。 詳細については、「[推奨事項の修復](security-center-remediate-recommendations.md)」を参照してください。

関連する推奨事項に対して強制と拒否のオプションを構成することで、スコアを向上させ、ユーザーがスコアに悪影響を与えるリソースを作成しないようにすることもできます。 詳細については、「[適用/拒否の推奨事項を使用した構成ミスの防止](prevent-misconfigurations.md)」を参照してください。

## <a name="security-controls-and-their-recommendations"></a>セキュリティ コントロールとその推奨事項

次の表に、Azure Security Center のセキュリティ コントロールを示します。 コントロールごとに、"*すべて*" のリソースにおいて、コントロールに示されている "*すべて*" の推奨事項を修復した場合に取得できるセキュリティ スコアのポイントの最大数が表示されます。 

Security Center で用意されている一連のセキュリティ推奨事項は、各組織の環境で使用可能なリソースに合わせて調整されています。 推奨事項は、[ポリシーを無効にしたり](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)、[推奨事項から特定のリソースを除外したり](exempt-resource.md)して、さらにカスタマイズできます。 
 
どの組織でも、割り当てられている Azure Policy イニシアティブを慎重に確認することをお勧めします。 

> [!TIP]
> イニシアティブのレビューと編集の詳細については、「[セキュリティ ポリシーの操作](tutorial-security-policy.md)」を参照してください。 

Security Center の既定のセキュリティ イニシアティブは業界のベストプラクティスと基準に基づいていますが、以下に示す組み込みの推奨事項が組織に完全には適合しない場合があります。 したがって、既定のイニシアティブを組織独自のポリシーに合わせて (セキュリティを損なわずに) 調整する必要が生じることがあります。 満たす必要がある業界標準、規制標準、およびベンチマークがこれに当たります。<br><br>
<div class="foo">

<style type="text/css"> .tg  {border-collapse:collapse;border-spacing:0;} .tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px; overflow:hidden;padding:10px 5px;word-break:normal;} .tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:18px; font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;} .tg .tg-cly1{text-align:left;vertical-align:middle} .tg .tg-lboi{border-color:inherit;text-align:left;vertical-align:middle} </style>

[!INCLUDE [security-center-controls-and-recommendations](../../includes/asc/security-control-recommendations.md)]

</div>




## <a name="secure-score-faq"></a>セキュリティ スコアに関する FAQ

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>セキュリティ コントロールの 4 つの推奨事項のうち 3 つだけに対処した場合、セキュリティ スコアは変わりますか。
いいえ。 1 つのリソースのすべての推奨事項を修復するまでスコアは変更されません。 コントロールの最大スコアを取得するには、すべてのリソースのすべての推奨事項を修復する必要があります。

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>推奨事項が自分に該当しないためポリシーで無効にした場合、セキュリティ コントロールは達成され、セキュリティ スコアが更新されますか。
はい。 お使いの環境該当しない推奨設定は、無効にすることをお勧めします。 特定の推奨事項を無効にする方法については、「[セキュリティ ポリシーの無効化](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)」を参照してください。

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>特定のセキュリティ コントロールによるセキュリティ スコアのポイントが 0 の場合、無視すべきですか。
場合によっては、コントロールの最大スコアが 0 を超えているにもかかわらず、影響がないことがあります。 リソース修正によるスコアの増分がごくわずかである場合は、0 に丸められます。 これらの推薦事項は修正するとセキュリティが強化されるため、これらの推奨事項は無視しないでください。 唯一の例外は "追加のベスト プラクティス" コントロールです。 これらの推奨事項を修復してもスコアは増加しませんが、全体的なセキュリティが強化されます。

## <a name="next-steps"></a>次のステップ

この記事では、セキュリティ スコアと新しいセキュリティ コントロールについて説明しました。 関連資料については、次の記事をご覧ください。

- [推奨事項のさまざまな要素に関する詳細](security-center-recommendations.md)
- [推奨事項を修復する方法を確認する](security-center-remediate-recommendations.md)
- [セキュリティ スコアを使用してプログラムで作業するための GitHub ベースのツールを表示する](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)