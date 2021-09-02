---
title: Metrics Advisor のメトリックのグラフ
titleSuffix: Azure Cognitive Services
description: メトリックのグラフを構成し、データ内の関連する異常を視覚化する方法です。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbullwin
ms.openlocfilehash: 7842cdd6d5336e90346d666d8c2d31d20c19caa0
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341514"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>方法: メトリックのグラフを作成して関連するメトリックを分析する

Metrics Advisor では、それぞれの時系列が、履歴データから学習して将来の傾向を予測するモデルによって個別に監視されます。 履歴パターンから外れたデータ ポイントがあると、異常が検出されます。 ただし、場合によっては、複数のメトリックが相互に関連しており、複数のメトリックを横断して異常を分析することが必要になります。 これを支援するツールが **メトリック グラフ** です。 

たとえば、複数のメトリックを使用してさまざまな観点からビジネスを監視している場合、異常検出が個々に適用されます。 しかし、実際のビジネス ケースでは、複数のメトリックで検出された異常が相互に関係している可能性があります。これらの関係を検出し、それに基づいて根本原因を分析することが実際の問題に対処する際に役立ちます。 メトリック グラフは、関連するメトリックで検出された異常を自動的に相互に関連付けて、トラブルシューティング プロセスを促進するのに役立ちます。 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>最初のノードをグラフに配置するためのメトリックを選択します。

ナビゲーション バーの **[Metrics Graph]\(メトリック グラフ\)** タブをクリックします。 メトリック グラフを作成するための最初の手順は、ノードをグラフに配置することです。 ページの上部でデータ フィードとメトリックを選択します。 ノードが下部のパネルに表示されます。 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="メトリックを選択する":::

## <a name="add-a-noderelation-on-existing-node"></a>既存のノードにノードと関係を追加する

次に、別のノードを追加して、既存のノードとの関係を指定する必要があります。 既存のノードを選択して、右クリックします。 コンテキスト メニューに、いくつかのオプションが表示されます。 

**[Add relation]\(関係の追加\)** を選択すると、別のメトリックを選択して、2 つのノード間の関係の種類を指定できます。 また、特定のディメンション フィルターを適用することもできます。 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="ノードと関係を追加します":::

上記の手順を繰り返すと、関連するすべてのメトリック間の関係を示すメトリックのグラフが作成されます。

グラフで実行できる他の操作は次のとおりです。 
1.  ノードを削除する
2.  メトリックに移動する
3.  Incident Hub (インシデント ハブ) に移動する
4.  Expand
5.  関係を削除する

## <a name="legend-of-metrics-graph"></a>メトリック グラフの凡例

グラフ上の各ノードはメトリックを表します。 メトリック グラフには、次の 4 種類のノードがあります。

-  **緑のノード**: 現在のメトリック インシデントの重大度が "低" であることを表すノード。
- **オレンジ色のノード**: 現在のメトリック インシデントの重大度が "中" であることを表すノード。
- **赤のノード**: 現在のメトリック インシデントの重大度が "高" であることを表すノード。
- **青のノード**: 異常な重大度のないノード。


## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>関連するメトリックの異常状態をインシデント ハブに表示する

メトリック グラフが作成され、そのグラフ内のメトリックに異常が検出された場合は、関連する異常ステータスを表示したり、インシデントの概要を把握したりすることができます。 

グラフ内のインシデントをクリックし、下にスクロールして、診断情報の下にある **[cross metrics analysis]\(クロス メトリック分析\)** に移動します。

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="関連するメトリックと異常を表示します":::

## <a name="next-steps"></a>次のステップ

- [フィードバックを使用して異常検出を調整する](anomaly-feedback.md)
- [インシデントを診断する](diagnose-an-incident.md)
- [メトリックを構成して検出構成を微調整する](configure-metrics.md)