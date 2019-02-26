---
title: 光学式文字認識 (OCR) でのテキストの抽出 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API を使用した光学式文字認識 (OCR) によるテキスト抽出に関連する概念です。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: deb73eb9fdd6879a5fbe1fed820bf92b2d627b65
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310442"
---
# <a name="extract-text-with-optical-character-recognition"></a>光学式文字認識でのテキストの抽出

Computer Vision の光学式文字認識 (OCR) 機能では、イメージのテキスト コンテンツが検出され、識別されたテキストはコンピューターが読み取り可能な文字ストリームに変換されます。 結果は、検索に、また、医療記録、セキュリティ、銀行取引などの多くの目的に使用できます。 

OCR では 25 言語がサポートされています。アラビア語、簡体字中国語、繁体字中国語、チェコ語、デンマーク語、オランダ語、英語、フィンランド語、フランス語、ドイツ語、ギリシャ語、ハンガリー語、イタリア語、日本語、韓国語、ノルウェー語、ポーランド語、ポルトガル語、ルーマニア語、ロシア語、セルビア語 (キリルおよびラテン)、スロバキア語、スペイン語、スウェーデン語、トルコ語。 OCR は、検出されたテキストの言語を自動的に検出します。

必要に応じて、OCR は、イメージの水平軸を中心とした回転のオフセットを度数で返すことによって、認識されたテキストの回転を修正します。 また、OCR では、以下の図に示すように、各単語のフレーム座標も提供されます。

![回転している画像と読み取られて描かれたテキストを表す図](./Images/vision-overview-ocr.png)

## <a name="image-requirements"></a>イメージの要件

Computer Vision では、OCR を使用して次の要件に合ったイメージからテキストを抽出できます。

* イメージが、JPEG、PNG、GIF、または BMP で提示されている
* 入力イメージのサイズが、50 x 50 から 4200 x 4200 ピクセルまでである
* イメージ内のテキストは、90 度の倍数に、40 度までの小角度を加えた度数で回転させることができます。

## <a name="improving-ocr-accuracy"></a>OCR 精度の向上

テキスト認識の精度は、イメージの品質によって異なります。 次のような状況では、読み取りが不正確になる可能性があります。

* ぼやけたイメージ。
* 手書きまたは筆記体のテキスト。
* アーティスティックなフォント スタイル。
* 小さなテキスト サイズ。
* 複雑な背景、影、テキスト上のグレア、射影ひずみ。
* 単語の先頭の大文字のサイズが大きすぎるか欠落している
* 下付き、上付き、または取り消し線テキスト。

### <a name="ocr-limitations"></a>OCR の制限事項

テキストが主なイメージでは、部分的に認識された単語により誤検知が発生する場合があります。 一部のイメージ、特にテキストのない写真では、イメージの種類によって、精度が大きく異なる場合があります。

## <a name="next-steps"></a>次の手順

[OCR のリファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)を参照して、詳細を学びます。
