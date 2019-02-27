---
title: 印刷されたテキストと手書きのテキストの認識 - Computer Vision
titleSuffix: Azure Cognitive Services
description: 画像に含まれる印刷されたテキストと手書きのテキストの Computer Vision API を使用した認識に関する概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 48ce15a11c3e3282535420f3e1bb1915276d70f5
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313179"
---
# <a name="recognizing-printed-and-handwritten-text"></a>印刷されたテキストと手書きのテキストの認識

Computer Vision では、レシート、ポスター、名刺、手紙、およびホワイトボードなど、異なるサーフェスや背景を持つさまざまなオブジェクトのイメージから、印刷または手書きのテキストを検出して、抽出できます。

テキスト認識機能は、[光学式文字認識 (OCR)](concept-extracting-text-ocr.md) によく似ていますが、OCR とは異なり、非同期で実行され、最新の認識モデルが使用されています。

> [!NOTE]
> このテクノロジは現在、プレビュー段階であり、英語のテキストでのみ使用できます。

## <a name="text-recognition-requirements"></a>テキスト認識の要件

Computer Vision では、次の要件を満たす画像内の印刷および手書きのテキストを認識できます。

- 画像が、JPEG、PNG、または BMP 形式になっている
- イメージのファイル サイズが 4 メガバイト (MB) 未満である
- 画像の寸法が 50 x 50 から 4200 x 4200 ピクセルの間である

## <a name="next-steps"></a>次の手順

[テキストのリファレンス ドキュメントの認識](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)に関するページを参照して、詳細を学びます。