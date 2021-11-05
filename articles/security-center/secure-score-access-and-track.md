---
title: Microsoft Defender for Cloudでのセキュリティスコアの追跡
description: Microsoft Defender for Cloudでセキュリティスコアにアクセスして追跡するための複数の方法について説明します。
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 61cd7dd97f0b563d5bcb1030830edfa073ab0571
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452834"
---
# <a name="access-and-track-your-secure-score"></a>セキュリティで保護されたスコアにアクセスして追跡する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

次のセクションで説明するように、Azure portal またはプログラムによって、全体的なセキュリティ スコアとサブスクリプションごとのスコアを確認できます。

> [!TIP]
> スコア計算方法の詳しい説明については、「[計算 - スコアを理解する](secure-score-security-controls.md#calculations---understanding-your-score)」を参照してください。

## <a name="get-your-secure-score-from-the-portal"></a>ポータルからセキュリティ スコアを取得する

Defender for Cloudでは、ポータルでスコアが目立つように表示されます。これは、最初のメインタイルで、クラウドの概要 ページです。 このタイルを選択すると、スコアがサブスクリプション別に分類されている専用のセキュリティ スコア ページに移動ます。 サブスクリプションを 1 つ選択すると、優先度が設定された推奨事項と、修復によるスコアへの影響を示す詳細な一覧が表示されます。 

要約すると、セキュリティスコアは、クラウドのポータルページの Defender の次の場所に表示されます。

- クラウドの **概要** に関する Defender のタイル (メインダッシュボード):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="クラウドのダッシュボードの Defenderでのセキュリティで保護されたスコア":::

- 専用の **[セキュリティ スコア]** ページで、サブスクリプションと管理グループのセキュリティ スコアを確認できます。

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Security Centerのセキュリティ スコアページのサブスクリプションのセキュリティ スコア":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Defender on Cloudの セキュリティスコア ページの管理グループのセキュリティスコア":::

    > [!NOTE]
    > 十分なアクセス許可を持っていない管理グループについては、スコアが "制限付き" として表示されます。 

- **[推奨事項]** ページの上部:

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="クラウドの推奨事項 ページでの Defender のセキュリティスコア":::

## <a name="get-your-secure-score-from-the-rest-api"></a>REST API からセキュリティ スコアを取得する

スコアには、Secure Scores API を使用してアクセスできます。 この API メソッドを使用すると、データに対してクエリを実行したり、一定期間のセキュリティ スコアをレポートする独自のメカニズムを構築したりするための柔軟性が得られます。 たとえば、[Secure Scores API](/rest/api/securitycenter/securescores) を使用して、特定のサブスクリプションのスコアを取得できます。 また、[Secure Score Controls API](/rest/api/securitycenter/securescorecontrols) を使用して、サブスクリプションのセキュリティ コントロールと現在のスコアを一覧表示できます。

![API を使用して 1 つのセキュア スコアを取得する。](media/secure-score-security-controls/single-secure-score-via-api.png)

Secure Scores API を使用して構築されたツールの例については、[GitHub コミュニティのセキュリティ スコアの領域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)を参照してください。 

## <a name="get-your-secure-score-from-azure-resource-graph"></a>Azure Resource Graph からセキュア スコアを取得する

Azure Resource Graph を使用すると、堅牢なフィルター処理、グループ化、および並べ替え機能を使用して、クラウド環境全体のリソース情報にすばやくアクセスできます。 これは、Azure サブスクリプション全体の情報を、プログラムから、または Azure portal 内ですばやく効率的に照会する方法です。 [Azure Resource Graph の詳細についてさらに学習します](../governance/resource-graph/index.yml)。

Azure Resource Graph を使用し、複数のサブスクリプションのセキュア スコアにアクセスするには:

1. Azure portal から **Azure Resource Graph Explorer** を開きます。

    :::image type="content" source="./media/multi-factor-authentication-enforcement/opening-resource-graph-explorer.png" alt-text="Azure Resource Graph エクスプローラーの起動** 推奨ページ" :::

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

### <a name="secure-score-over-time-report-in-workbooks-page"></a>ブック ページのセキュア スコア推移レポート

Defender for Cloud のブックページには、サブスクリプション、セキュリティ制御などのスコアを視覚的に追跡するための既製のレポートが含まれています。 詳細につい [ては、「クラウドデータに対して Defender の豊富で対話型のレポートを作成する](custom-dashboards-azure-workbooks.md)」を参照してください。

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Microsoft Defender for Cloudのブックギャラリーからの一定期間内のセキュリティスコアレポートのセクション":::

### <a name="power-bi-pro-dashboards"></a>Power BI Pro ダッシュボード

Pro アカウントを使用している Power BI ユーザーの場合は、 **[Secure Score Over Time]\(経時的なセキュリティ スコア\)** Power BI ダッシュボードを使用して、セキュリティ スコアを経時的に追跡し、変更がないか調査できます。

> [!TIP]
> このダッシュボードと、プログラムを使用してセキュリティで保護されたスコアで作業するその他のツールについては、GitHub の Microsoft Defender for Cloudコミュニティの専用領域にあります。 https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

ダッシュボードには、セキュリティの状態を分析するために役立つ次の 2 つのレポートが含まれています。

- **[リソースの概要]** - リソースの正常性に関する概要データが提供されます。
- **[Secure Score Summary]\(セキュリティ スコアの概要\)** - スコアの進捗状況に関する集計データが提供されます。 スコアの変化を確認するには、[Secure score over time per subscription]\(サブスクリプションごとの経時的なセキュリティ スコア\) グラフを使用します。 スコアが大幅に変化した場合は、[detected changes that may affect your secure score]\(検出された変更の中でセキュリティ スコアに影響する可能性のあるもの\) の表を調べ、変更の原因となった可能性のある変更を確認します。 この表は、削除されたリソース、新しくデプロイされたリソース、または推奨事項のいずれかについてセキュリティの状態が変化したリソースを示しています。

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="経時的なセキュア スコアを追跡し、変更を調査するためのオプションの [Secure Score Over Time]\(経時的なセキュア スコア\) Power BI ダッシュボード。":::


## <a name="next-steps"></a>次のステップ

この記事では、セキュア スコアにアクセスし、追跡する方法について説明しました。 関連資料については、次の記事をご覧ください。

- [推奨事項のさまざまな要素に関する詳細](review-security-recommendations.md)
- [推奨事項を修復する方法を確認する](implement-security-recommendations.md)
- [セキュリティ スコアを使用してプログラムで作業するための GitHub ベースのツールを表示する](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)
