---
title: Azure Security Center 内のセキュリティ スコア
description: Azure Security Center のセキュリティ スコアとそのセキュリティ コントロールの説明
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 05/05/2021
ms.author: memildin
ms.openlocfilehash: 9879b5154866436e3216243b123b3575e6cd3df4
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108754915"
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

## <a name="how-your-secure-score-is-calculated"></a>セキュリティ スコアの計算方法 

総合的なセキュリティ スコアに対する各セキュリティ コントロールの影響は、[推奨事項] ページで明確に示されています。

:::image type="content" source="./media/secure-score-security-controls/security-controls.png" alt-text="Azure Security Center のセキュリティ制御とセキュリティ スコアへの影響" lightbox="./media/secure-score-security-controls/security-controls.png":::

セキュリティ コントロールで取得可能なポイントをすべて取得するには、すべてのリソースがセキュリティ コントロール内のすべてのセキュリティの推奨事項に準拠している必要があります。 たとえば、Security Center には、管理ポートをセキュリティで保護する方法に関する推奨事項が複数あります。 今後は、セキュリティ スコアを改善するには、これらをすべて修復する必要があります。

### <a name="example-scores-for-a-control"></a>コントロールのスコアの例

:::image type="content" source="./media/secure-score-security-controls/remediate-vulnerabilities-control.png" alt-text="セキュリティ コントロール システム更新プログラムの適用" lightbox="./media/secure-score-security-controls/remediate-vulnerabilities-control.png":::


次の点に注意してください。

| #  | Name                                           | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                |
|:-:|------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1 | **脆弱性セキュリティ制御の修復** | このコントロールは、既知の脆弱性の検出と解決に関連する複数のレコメンデーションをグループ化します。                                                                                                                                                                                                                                                                                                                                   |
| 2 | **最大スコア**                                  | コントロール内のすべてのレコメンデーションを完了することによって得られるポイントの最大数です。 コントロールの最大スコアは、そのコントロールの相対的な有意性を示し、各環境で固定されます。 最大スコア値を使用して問題をトリアージし、最初に対処すべき問題を決定します。<br>すべてのコントロールとその最大スコアの一覧については、[「セキュリティ コントロールとそのレコメンデーション」](#security-controls-and-their-recommendations)を参照してください。 |
| 3 | **リソースの数**                        | このコントロールの影響を受けるリソースは 35 個あります。<br>すべてのリソースのコントリビューションの可能性について理解するには、最大スコアをリソースの数で割ります。<br>この例では 6/35=0.1714 です<br>**すべてのリソースが 0.1714 ポイントになります。**                                                                                                                                                                                          |
| 4 | **現在のスコア**                              | このコントロールの現在のスコアです。<br>現在のスコア = [リソースあたりのスコア] * [正常なリソースの数]<br> 0.1714 x 5 正常なリソース = 0.86<br>合計スコアは、各コントロールを基にしています。 この例のコントロールは、現在の合計セキュリティ スコアに 0.86 ポイント貢献しています。                                                                                                                                               |
| 5 | **潜在的なスコアの増加**                   | コントロール内で取得できる残りのポイント。 このコントロールのすべてのレコメンデーションを修復すると、スコアが 9% 増加します。<br>潜在スコア増加 = [リソースあたりのスコア] * [異常なリソースの数]<br> 0.1714 x 30 異常なリソース = 5.14<br>                                                                                                                                                        |
|   |                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                            |



### <a name="calculations---understanding-your-score"></a>計算 - スコアを理解する

|メトリック|式と例|
|-|-|
|**セキュリティ コントロールの現在のスコア**|<br>![セキュリティ コントロールのスコアを計算するための式](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>セキュリティ スコアは、個々のセキュリティ コントロールを基にしています。 コントロールの現在のスコアは、コントロール内の推奨事項によって影響を受ける各リソースを基にしています。 各コントロールの現在のスコアは、コントロール *内* でリソースの状態を測定したものです。<br>![セキュリティ コントロールの現在のスコアを計算するときに使用される値を示すヒント](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>この例では、最大スコア 6 は、正常なリソースと異常なリソースの合計値である 78 で割ることになります。<br>6 / 78 = 0.0769<br>これを正常なリソースの数 (4) で乗算すると、次に示す現在のスコアになります。<br>0.0769 * 4 = **0.31**<br><br>|
|**セキュリティ スコア**<br>1 つのサブスクリプション|<br>![サブスクリプションのセキュリティ スコアを計算するための式](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![すべてのコントロールを有効にした単一サブスクリプションのセキュア スコア](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>この例では、すべてのセキュリティ コントロールを適用可能な 1 つのサブスクリプションがあります (潜在的な最大スコアは 60 ポイントです)。 スコアには取得可能な 60 ポイントのうち 28 ポイントが示され、残りの 32 ポイントは、セキュリティ コントロールの "潜在的な増加スコア" の数値に反映されます。<br>![コントロールの一覧と潜在的な増加スコア](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**セキュリティ スコア**<br>複数のサブスクリプション|<br>![複数のサブスクリプションのセキュリティ スコアを計算するための式](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>複数のサブスクリプションに対して結合されたスコアを計算する場合、Security Center には各サブスクリプションの "*重み*" が含まれます。 サブスクリプションの相対的な重みは、リソースの数などの要素に基づいて Security Center によって決定されます。<br>各サブスクリプションの現在のスコアは、1 つのサブスクリプションの場合と同じ方法で計算されますが、式に示されているように重みが適用されます。<br>複数のサブスクリプションを表示する場合、セキュリティ スコアは、すべての有効なポリシー内のすべてのリソースを評価し、各セキュリティ コントロールの最大スコアに対するその組み合わせの影響をグループ化します。<br>![すべてのコントロールが有効な複数のサブスクリプションのセキュリティ スコア](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>結合されたスコアは平均では **ありません**。これは、すべてのサブスクリプションのすべてのリソースの状態の体制を評価したものです。<br>ここでも、[推奨事項] ページにアクセスして、取得可能なポイントを追加すると、現在のスコア (24) と利用可能な最大スコア (60) の差を確認できます。|


### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>セキュリティ スコアの計算に含まれる推奨事項

セキュリティ スコアに影響するのは、組み込みの推奨事項のみです。

**プレビュー** のフラグが設定されている推奨事項は、セキュリティ スコアの計算からは除外されます。 それでも、その推奨事項はプレビュー期間が終了した時点でスコアに反映されるため、可能な限り修復しておく必要があります。

プレビューの推奨事項の例を次に示します。

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="プレビュー フラグが設定された推奨事項":::

## <a name="improve-your-secure-score"></a>セキュリティ スコアを向上させる

セキュリティ スコアを向上させるには、推奨事項リストのセキュリティの推奨事項を修復してください。 各推奨事項は、リソースごとに手動で修復することも、 **[修正]** オプション (利用可能な場合) を使用して、複数のリソースの問題を迅速に解決することもできます。 詳細については、「[推奨事項の修復](security-center-remediate-recommendations.md)」を参照してください。

関連する推奨事項に対して強制と拒否のオプションを構成することで、スコアを向上させ、ユーザーがスコアに悪影響を与えるリソースを作成しないようにすることもできます。 詳細については、「[適用/拒否の推奨事項を使用した構成ミスの防止](prevent-misconfigurations.md)」を参照してください。

## <a name="security-controls-and-their-recommendations"></a>セキュリティ コントロールとその推奨事項

次の表に、Azure Security Center のセキュリティ コントロールを示します。 コントロールごとに、"*すべて*" のリソースにおいて、コントロールに示されている "*すべて*" の推奨事項を修復した場合に取得できるセキュリティ スコアのポイントの最大数が表示されます。 

Security Center で用意されている一連のセキュリティ推奨事項は、各組織の環境で使用可能なリソースに合わせて調整されています。 推奨事項は、[ポリシーを無効にしたり](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)、[推奨事項から特定のリソースを除外したり](exempt-resource.md)して、さらにカスタマイズできます。 
 
どの組織でも、割り当てられている Azure Policy イニシアティブを慎重に確認することをお勧めします。 

> [!TIP]
> イニシアティブのレビューと編集の詳細については、「[セキュリティ ポリシーの操作](tutorial-security-policy.md)」を参照してください。 

Security Center の既定のセキュリティ イニシアティブは業界のベスト プラクティスと標準に基づいていますが、下に示す組み込みの推奨事項が組織に完全には適合しない場合があります。 したがって、場合によっては、既定のイニシアティブを組織独自のポリシーに合わせて (セキュリティを損なわずに) 調整する必要があります。 満たす必要がある業界標準、規制標準、ベンチマークがこれに当たります。<br><br>
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


> [!div class="nextstepaction"]
> [セキュア スコアにアクセスして追跡する](secure-score-access-and-track.md)