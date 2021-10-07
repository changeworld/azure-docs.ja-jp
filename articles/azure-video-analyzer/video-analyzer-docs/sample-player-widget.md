---
title: Azure Video Analyzer プレーヤー ウィジェットのサンプル
description: この参照記事では、Video Analyzer プレーヤー ウィジェットのサンプル アプリケーションの概要について簡単に説明します
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 08/13/2021
ms.openlocfilehash: abea3bdd1098dbb32be35d632c7a8b0ad51fc49e
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058493"
---
# <a name="azure-video-analyzer-player-widget-sample"></a>Azure Video Analyzer プレーヤー ウィジェットのサンプル

このサンプル アプリケーションは、Video Analyzer プレーヤー ウィジェットとビデオ再生、ゾーン描画、およびビデオ クリップ生成の諸機能との統合を示します。

* [AVA C# サンプル リポジトリ](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp)を複製する
* [**src/video-player** フォルダー内の README](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp/blob/main/src/video-player/README.md) の手順に従う

## <a name="get-started"></a>開始するには
README の手順に従った後、アプリが http://localhost:3000/ で実行します
1. [ビデオの取得] をクリックします
2. Video Analyzer アカウントのすべてのビデオのドロップダウン リストからビデオを選択します

## <a name="video-player"></a>ビデオ プレーヤー
ポータルに示されているように、[ビデオ プレーヤー] ページには、一般的なプレーヤーが表示されます。  
![ビデオ プレーヤーのスクリーンショット。](./media/sample-player-widget/widget-video-player.png)

## <a name="zone-drawer"></a>ゾーン ドロワー
ゾーン ドロワーを使用すると、ビデオに多角形を描画したり、線を描画したりしてゾーンを作成できます。 これらのゾーンと線を保存して、それぞれのゾーンと線の座標を取得することもできます。  
  
**例:**
```json
  {
    "@type": "#Microsoft.VideoAnalyzer.NamedLineString",
    "name": "Line 1",
    "line": [
      {
        "x": 0.6987951807228916,
        "y": 0.4430992736077482
      },
      {
        "x": 0.6987951807228916,
        "y": 0.7046004842615012
      }
    ]
  }{
    "@type": "#Microsoft.VideoAnalyzer.NamedPolygonString",
    "name": "Zone 2",
    "polygon": [
      {
        "x": 0.10575635876840696,
        "y": 0.7481840193704601
      },
      {
        "x": 0.16599732262382866,
        "y": 0.7796610169491526
      },
      {
        "x": 0.07764390896921017,
        "y": 0.9007263922518159
      },
      {
        "x": 0.024096385542168676,
        "y": 0.8547215496368039
      }
    ]
  }
```
これらの座標をコピーするには、 **[クリップボードにコピー]** ボタンを使用します。 ゾーンと行は、各ゾーンと行の横にある 3 つの点を使用して、名前を変更したり削除したりできます。  
![ゾーン ドロワーのスクリーンショット。](./media/sample-player-widget/widget-zone-drawer.png)

## <a name="video-clips"></a>ビデオ クリップ
ビデオ クリップを使用すると、開始日時と終了日時を選択できます。 *[追加]* ボタンを使用して、ビデオ クリップを生成できます。
生成されたすべてのクリップをドロップダウン リストから選択できます。ここでは、各クリップには、開始日時と終了日時としてタイトルが付けられています。
(例: 形式) この場合、ビデオ クリップは、一般的なビデオ プレーヤー形式で表示できます。  
![ビデオ クリップのスクリーンショット。](./media/sample-player-widget/widget-video-clips.png)

## <a name="next-steps"></a>次の手順
これらの[手順](./player-widget.md)を使用して、独自のカスタム Video Analyzer プレーヤーウィジェットを作成してみてください

