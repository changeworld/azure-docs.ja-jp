---
title: Metrics Advisor サービスに異常なフィードバックを提供する
titleSuffix: Azure Cognitive Services
description: Metrics Advisor インスタンスによって検出された異常に関するフィードバックを送信し、結果を調整する方法について説明します。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 11864cb26b76d414aa2efe2643797a2f66fa30e4
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047609"
---
# <a name="adjust-anomaly-detection-using-feedback"></a>フィードバックを使用して異常検出を調整する

メトリックの監視によって提供される異常検出の結果に満足できない場合は、フィードバックを手動で追加することができます。これにより、データに適用されるモデルが影響を受けます。 

フィードバック注釈モードをアクティブにするには、ページの右上隅にあるボタンを使用します。

:::image type="content" source="../media/feedback/annotation-mode.png" alt-text="フィードバック注釈モード。":::

フィードバック注釈モードがアクティブになると、1 つのポイント、または複数の連続したポイントに関するフィードバックを送信することができます。

## <a name="give-feedback-for-one-point"></a>1 つのポイントに関するフィードバックを送信する 

フィードバック注釈モードがアクティブの状態で、ポイントをクリックして、 **[フィードバックの追加]** パネルを開きます。 適用するフィードバックの種類を設定できます。 このフィードバックは、今後発生するポイントの検出に組み込まれる予定です。  

* メトリックの監視によるポイントのラベル付けが誤りであると考えられる場合は、 **[異常]** を選択します。 ポイントが異常であるかどうかを指定できます。 
* ポイントが傾向の変化の始まりを示していると考えられる場合は、 **[ChangePoint]\(変化点\)** を選択します。
* 季節性を示すには、 **[期間]** を選択します。 メトリックの監視では、季節性の間隔を自動的に検出したり、手動でこれを指定したりすることができます。 

同時に、コメントを **[コメント]** テキスト ボックスに残し、 **[保存]** をクリックして、フィードバックを保存することを検討してください。

:::image type="content" source="../media/feedback/feedback-menu.png" alt-text="フィードバック注釈モード。":::

## <a name="give-feedback-for-multiple-continuous-points"></a>複数の連続したポイントに関するフィードバックを提供する

[下へ] をクリックして、注釈を設定するポイントにマウスをドラッグすることで、複数の連続したポイントに関するフィードバックを一度に提供することができます。 上記と同じフィードバック メニューが表示されます。 **[保存]** をクリックすると、選択したすべてのポイントに同じフィードバックが適用されます。

:::image type="content" source="../media/feedback/continuous-points.png" alt-text="フィードバック注釈モード。":::

## <a name="how-to-view-my-feedback"></a>フィードバックを表示する方法

ポイントの異常検出が変更されたかどうかを確認するには、ポイントの上にマウス ポインターを置きます。 検出が変更された場合、ヒントには、 **[Affected by feedback: true]\(フィードバックの影響あり: true\)** と表示されます。 **false** と表示される場合は、その時点でフィードバックの計算が行われたものの、異常検出の結果が変更されなかった場合です。

:::image type="content" source="../media/feedback/affected-point.png" alt-text="フィードバック注釈モード。":::

## <a name="when-should-i-annotate-an-anomaly-as-normal"></a>異常に "正常" として注釈を設定する必要がある場合

異常が誤ったアラームであると考える場合の理由は、数多くあります。 次のいずれかのシナリオに該当する場合は、次の Metrics Advisor 機能を使用することを検討してください。


|シナリオ  |推奨 |
|---------|---------|
|この異常は、既知のデータ ソースの変更 (システムの変更など) が原因で発生します。     | このシナリオが定期的に再発することはないと予想される場合は、この異常に注釈を設定しないでください。        |
|この異常は、休日が原因で発生します。     | [[Preset events]\(イベントの事前設定\)](configure-metrics.md#preset-events) を使用して、指定した時間で異常検出にフラグを設定します。       |
|異常が検出される通常のパターン (週末など) があり、その場合は異常ではありません。      |フィードバック機能を使用するか、イベントを事前設定します。        |

## <a name="next-steps"></a>次のステップ
- [インシデントを診断する](diagnose-incident.md)
- [メトリックを構成して検出構成を微調整する](configure-metrics.md)
- [フックを使用してアラートを構成し、通知を取得する](../how-tos/alerts.md)