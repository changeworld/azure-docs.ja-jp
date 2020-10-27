---
title: 言語サポート - Computer Vision
titleSuffix: Azure Cognitive Services
description: この記事では、Computer Vision の機能 (OCR、画像分析) でサポートされる自然言語の一覧を示します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 9bd7e7dab5384ecd3e1e87d763d4b8f081b669be
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329834"
---
# <a name="language-support-for-computer-vision"></a>Computer Vision の言語サポート

Computer Vision の一部の機能では、複数の言語がサポートされています。ここに記載されていない機能では、英語だけがサポートされています。

## <a name="optical-character-recognition-ocr"></a>光学式文字認識 (OCR)

Computer Vision の OCR API では複数の言語をサポートしています。 言語コードを指定する必要はありません。 詳細については、「[光学式文字認識 (OCR)](concept-recognizing-text.md)」を参照してください。

|Language| 言語コード | OCR API | 3\.0 および 3.1 の読み取り | v3.2-preview.1 の読み取り |
|:-----|:----:|:-----:|:---:|:---:|
|アラビア語 | `ar`|✔ | | |
|簡体中国語 | `zh-Hans`|✔ | |✔ |
|繁体中国語 | `zh-Hant`|✔ | | |
|チェコ語 | `cs` |✔ | | |
|デンマーク語 | `da` |✔ | | |
|オランダ語 | `nl` |✔ |✔ |✔ |
|英語 | `en` |✔ |✔ |✔ |
|フィンランド語 | `fi` |✔ | | |
|フランス語 | `fr` |✔ |✔ |✔ |
|ドイツ語 | `de` |✔ |✔ |✔ |
|ギリシャ語 | `el` |✔ | | |
|ハンガリー語 | `hu` |✔ | | |
|イタリア語 | `it` |✔ |✔ |✔ |
|日本語 | `ja` |✔ | |✔ |
|韓国語 | `ko` |✔ | | |
|ノルウェー語 | `nb` |✔ | | |
|ポーランド語 | `pl` |✔ | | |
|Portuguese | `pt` |✔ |✔ |✔ |
|ルーマニア語 | `ro` |✔ | | |
|ロシア語 | `ru` |✔ | | |
|セルビア語 (キリル) | `sr-Cyrl` |✔ | | |
|セルビア語 (ラテン) | `sr-Latn` |✔ | | |
|スロバキア語 | `sk` |✔ | | |
|スペイン語 | `es` |✔ |✔ |✔ |
|スウェーデン語 | `sw` |✔ | | |
|トルコ語 | `tr` |✔ | | |

## <a name="image-analysis"></a>画像分析

[Analyze - Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API の一部のアクションは、`language` クエリ パラメーターで指定された他の言語で結果を返すことができます。 他のアクションは、どの言語が指定されていても英語で結果を返し、その他のアクションはサポートされていない言語に対して例外をスローします。 アクションは `visualFeatures` および `details` クエリ パラメーターで指定されます。画像分析で実行できるすべてのアクションの一覧については、[概要](overview.md)に関するページを参照してください。

|Language | 言語コード | Categories | Tags | 説明 | 成人向けコンテンツ | ブランド | Color | 顔 | ImageType | Objects | 有名人 | ランドマーク |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinese | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|英語 | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|日本語 | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portuguese | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|スペイン語 | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>次のステップ

このガイドで説明されている Computer Vision 機能を使ってみます。

* [ローカル画像の分析 (REST)](./quickstarts/csharp-analyze.md)
* [印刷されたテキストの抽出 (REST)](./quickstarts/csharp-print-text.md)
