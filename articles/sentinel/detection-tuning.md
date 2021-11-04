---
title: Azure Sentinel の分析ルールの微調整に関する推奨事項を取得する
description: 自動的に生成された推奨事項を使用して Azure Sentinel の脅威検出ルールを微調整することにより、脅威検出の対象範囲を維持しながら擬陽性を削減する方法について説明します。
author: yelevin
ms.author: yelevin
ms.service: azure-sentinel
ms.topic: how-to
ms.date: 10/13/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a302a325ff3bd510298f0d4b70b62f27af060a4e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092689"
---
# <a name="get-fine-tuning-recommendations-for-your-analytics-rules-in-azure-sentinel"></a>Azure Sentinel の分析ルールの微調整に関する推奨事項を取得する

> [!IMPORTANT]
>
> 検出のチューニングは現在、"**プレビュー**" 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

SIEM での脅威検出ルールの微調整は、脅威検出の対象範囲の最大化と擬陽性の割合の最小化の間のバランスをとるという、困難で、繊細、かつ継続的なプロセスである場合があります。 Azure Sentinel では、機械学習を使用して、一定期間にわたるデータ ソースからの数十億のシグナルとインシデントへの応答を分析してパターンを推測した後、チューニングのオーバーヘッドを大幅に削減して、ユーザーが実際の脅威の検出と対応に集中できるようにするすぐに使用可能な推奨事項と分析情報を提供することによって、このプロセスを簡略化および効率化します。

チューニングの推奨事項と分析情報は現在、分析ルールに組み込まれています。 この記事では、これらの分析情報によって示されることと、これらの推奨事項を実装する方法について説明します。

## <a name="view-rule-insights-and-tuning-recommendations"></a>ルールの分析情報とチューニングの推奨事項を表示する

Azure Sentinel に、いずれかの分析ルールに関するチューニングの推奨事項があるかどうかを確認するには、Azure Sentinel ナビゲーション メニューから **[分析]** を選択します。

次の図に示すように、推奨事項があるルールには電球アイコンが表示されます。

:::image type="content" source="media/detection-tuning/rule-list-with-insight.png" alt-text="推奨事項のインジケーターが付いた分析ルールの一覧のスクリーンショット。" lightbox="media/detection-tuning/rule-list-with-insight.png":::

このルールを編集して、これらの推奨事項を他の分析情報と共に表示します。 これらは、分析ルール ウィザードの **[ルールのロジックを設定]** タブの **[結果のシミュレーション]** の表示の下にまとめて表示されます。

:::image type="content" source="media/detection-tuning/tuning-insights.png" alt-text="分析ルールでのチューニングの分析情報のスクリーンショット。" lightbox="media/detection-tuning/tuning-insights.png":::

### <a name="types-of-insights"></a>分析情報の種類

**[チューニングの分析情報]** の表示は、それぞれが異なる情報を示す、スクロールしたりスワイプしたりできるいくつかのペインで構成されています。 それらの分析情報が表示される期間 (14 日) がそのフレームの上部に表示されます。

1. 最初の分析情報ペインには、インシデントあたりのアラートの平均数、開かれたインシデントの数、閉じられたインシデントの数といういくつかの統計情報が分類 (真陽性/擬陽性) によってグループ化されて表示されます。 この分析情報は、このルールへの負荷を見つけたり、何らかのチューニングが必要かどうか (たとえば、グループ化の設定を調整する必要があるかどうか) を理解したりするのに役立ちます。

    :::image type="content" source="media/detection-tuning/rule-efficiency.png" alt-text="ルールの効率の分析情報のスクリーンショット。":::

    この分析情報は、Log Analytics クエリの結果です。 **[インシデントあたりの平均アラート数]** を選択すると、その分析情報を生成した Log Analytics のクエリが表示されます。 **[開かれたインシデント]** を選択すると、 **[インシデント]** ブレードが表示されます。

1. 2 番目の分析情報ペインでは、除外すべき[エンティティ](entities-in-azure-sentinel.md)の一覧が推奨されます。 これらのエンティティは、閉じたインシデントと密接に関連付けられており、**擬陽性** として分類されます。 一覧表示されている各エンティティの横にあるプラス記号を選択すると、それは、このルールの今後の実行のクエリから除外されます。 

    :::image type="content" source="media/detection-tuning/entity-exclusion.png" alt-text="エンティティの除外の推奨事項のスクリーンショット。":::

    この推奨事項は、Microsoft の高度なデータ サイエンスおよび機械学習モデルによって生成されます。 このペインが **[チューニングの分析情報]** の表示に含まれるかどうかは、表示すべき推奨事項の存在に依存します。

1. 3 番目の分析情報ペインには、このルールによって生成されたすべてのアラートで最も頻繁に表示される 4 つのマップ済みエンティティが表示されます。 この分析情報で結果を生成するには、このルールに対してエンティティ マッピングが構成されている必要があります。 この分析情報は、"特に注目を集めており"、他のエンティティから注意をそらしているエンティティを認識するために役立つ可能性があります。 これらのエンティティを別のルールで個別に処理したり、それらを擬陽性またはその他のノイズであると判定してルールから除外したりすることもできます。

    :::image type="content" source="media/detection-tuning/top-entities.png" alt-text="上位のエンティティの分析情報のスクリーンショット。":::

    この分析情報は、Log Analytics クエリの結果です。 いずれかのエンティティを選択すると、その分析情報を生成した Log Analytics のクエリが表示されます。

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。
- [Azure Sentinel での偽陽性の処理](false-positives.md)
- [UEBA データを使用して擬陽性を分析する](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives)
- [脅威を検出するためのカスタム分析規則を作成する](detect-threats-custom.md)
