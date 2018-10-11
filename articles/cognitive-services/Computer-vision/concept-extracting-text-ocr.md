---
title: OCR によるテキストの抽出 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API を使用した光学式文字認識 (OCR) によるテキスト抽出に関連する概念です。
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 8cfc654e881c7477e430515e62f8c78cfd0a2b84
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238590"
---
# <a name="extracting-text-with-ocr"></a>OCR によるテキストの抽出

Computer Vision の光学式文字認識 (OCR) テクノロジでは、イメージのテキスト コンテンツが検出され、識別されたテキストはコンピューターが読み取り可能な文字ストリームに抽出されます。 結果は、検索の際に、また、医療記録、セキュリティ、銀行取引などの他の多くの目的に使用できます。 言語は自動的に検出されます。 OCR を利用すれば、ユーザーはテキストを書き写すのではなく、撮影できるため、使い勝手がよく、時間を短縮できます。

OCR では 25 言語がサポートされています。 これらの言語は、アラビア語、簡体字中国語、繁体字中国語、チェコ語、デンマーク語、オランダ語、英語、フィンランド語、フランス語、ドイツ語、ギリシャ語、ハンガリー語、イタリア語、日本語、韓国語、ノルウェー語、ポーランド語、ポルトガル語、ルーマニア語、ロシア語、セルビア語 (キリルおよびラテン)、スロバキア語、スペイン語、スウェーデン語、トルコ語です。

必要に応じて、OCR では、認識されたテキストの回転が、イメージの水平軸を中心として度単位で修正されます。 OCR では、以下の図に示すように、各単語のフレーム座標が提供されます。

![OCR の概要](./Images/vision-overview-ocr.png)

## <a name="ocr-requirements"></a>OCR の要件

Computer Vision では、OCR を使用して次の要件に合ったイメージからテキストを抽出できます。

* イメージが、JPEG、PNG、GIF、または BMP で提示されている
* 入力イメージのサイズが、50 x 50 から 4200 x 4200 ピクセルまでである


入力イメージは、90 度の倍数に、40 度までの小角度を加えた度数で回転させることができます。

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

テキストが主な写真では、部分的に認識された単語により誤検知が発生する場合があります。 一部の写真、特にテキストのない写真では、イメージの種類によって、精度が大きく異なる場合があります。

## <a name="next-steps"></a>次の手順

[印刷されたテキストと手書きのテキストの認識](concept-recognizing-text.md)についての概念を学習します。
