---
title: マッピング データ フロー グラフの管理
titleSuffix: Azure Data Factory & Azure Synapse
description: マッピング データ フロー グラフを効率的に管理および編集する方法
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: da9b3148506e437f7bf5d90fc0fbe44e0a2d27f0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812112"
---
# <a name="managing-the-mapping-data-flow-graph"></a>マッピング データ フロー グラフの管理

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

マッピング データ フローは、データ フロー グラフと呼ばれるデザイン サーフェスを使用して作成されます。 グラフでは、変換ロジックが左から右に構築され、追加のデータ ストリームが上から下に追加されます。 新しい変換を追加するには、既存の変換の右下にあるプラス記号を選択します。

:::image type="content" source="media/data-flow/canvas-2.png" alt-text="キャンバス":::

データ フローの複雑性に伴い、次のメカニズムを使用して、データ フロー グラフを効率的に移動および管理できます。 

## <a name="moving-transformations"></a>変換の移動

マッピング データ フローでは、一連の接続された変換ロジックを **ストリーム** と呼びます。 **[Incoming stream]\(着信ストリーム\)** フィールドにより、現在の変換をフィードするデータ ストリームが決まります。 各変換は、その関数に応じて 1 つまたは 2 つの着信ストリームを持ち、出力ストリームを表します。 着信ストリームの出力スキーマによって、現在の変換で参照できる列のメタデータが決まります。

:::image type="content" source="media/data-flow/move-nodes.png" alt-text="ノードの移動":::

パイプライン キャンバスとは異なり、データ フロー変換はドラッグ アンド ドロップ モデルを使用して編集されません。 変換の着信ストリームを変更したり、変換を "移動" したりするには、**着信ストリーム** ドロップダウンから別の値を選択します。 これを行うと、すべての下流変換が編集された変換と共に移動します。 グラフが自動的に更新され、新しい論理フローが表示されます。 着信ストリームを既に下流変換がある変換に変更すると、新しい分岐または並列データ ストリームが作成されます。 詳細については、[マッピング データ フローでの新しい分岐](data-flow-new-branch.md)に関するページを参照してください。

## <a name="hide-graph-and-show-graph"></a>グラフの非表示とグラフの表示

変換を編集するときに、構成パネルを展開してキャンバス全体を表示し、グラフを非表示にすることができます。 キャンバスの右側にある上向きのシェブロンをクリックします。

:::image type="content" source="media/data-flow/hide-graph.png" alt-text="グラフの非表示":::

グラフが非表示になっている場合は、 **[次へ]** または **[前へ]** をクリックして、ストリーム内の変換間を移動できます。 下向きのシェブロンをクリックすると、グラフが表示されます。

:::image type="content" source="media/data-flow/show-graph.png" alt-text="グラフの表示":::

## <a name="searching-for-transformations"></a>変換の検索

グラフ内の変換をすばやく見つけるには、ズーム設定の上にある **[検索]** アイコンをクリックします。

:::image type="content" source="media/data-flow/search-1.png" alt-text="スクリーンショットには、[検索] ボタンが示されています。":::

変換名または説明で検索して、変換を見つけることができます。

:::image type="content" source="media/data-flow/search-2.png" alt-text="スクリーンショットには、[検索] テキスト ボックスが示されています。":::

## <a name="hide-reference-nodes"></a>参照ノードの非表示

データ フローに結合、参照、既存、または和集合の変換がある場合、データ フローにより、すべての着信ストリームへの参照ノードが表示されます。 使用する垂直スペースを最小化するために、参照ノードを最小化できます。 これを行うには、キャンバスを右クリックし、 **[Hide reference nodes]\(参照ノードの非表示\)** を選択します。

:::image type="content" source="media/data-flow/hide-reference-nodes.png" alt-text="参照ノードの非表示":::

## <a name="next-steps"></a>次の手順

データ フロー ロジックが完成したら、[デバッグ モード](concepts-data-flow-debug-mode.md)を有効にして、データ プレビューでテストします。
