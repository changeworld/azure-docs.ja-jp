---
title: 印刷されたテキストと手書きのテキストの認識 - Computer Vision
titleSuffix: Azure Cognitive Services
description: 画像に含まれる印刷されたテキストと手書きのテキストの Computer Vision API を使用した認識に関する概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 6ee2b3282a5f895c61b9f0b55aa3353e534b6e37
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55191043"
---
# <a name="recognizing-printed-and-handwritten-text"></a>印刷されたテキストと手書きのテキストの認識

Computer Vision では、レシート、ポスター、名刺、手紙、およびホワイトボードなど、異なるサーフェスや背景を持つさまざまなオブジェクトのイメージから、印刷または手書きのテキストを検出して、抽出できます。

テキスト認識は時間と労力の節約になります。 文字起こしではなくテキストの画像を取得することにより、生産性を高めることができます。 テキスト認識ではメモをデジタル化できます。 このデジタル化により、迅速かつ簡単な検索が可能になります。 また、書類が散乱する事態も防ぐことができます。

## <a name="text-recognition-requirements"></a>テキスト認識の要件

Computer Vision では、次の要件を満たす画像内の印刷および手書きのテキストを認識できます。

- 画像が、JPEG、PNG、または BMP 形式になっている
- イメージのファイル サイズが 4 メガバイト (MB) 未満である
- 画像の寸法が 50 x 50 から 4200 x 4200 ピクセルの間である

> [!NOTE]
> このテクノロジは現在、プレビュー段階であり、英語のテキストでのみ使用できます。

## <a name="next-steps"></a>次の手順

[OCR でのテキスト抽出](concept-extracting-text-ocr.md)に関する概念を学習する。
