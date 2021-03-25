---
title: 光学式文字認識 (OCR) - Computer Vision
titleSuffix: Azure Cognitive Services
description: 印刷や手書きのテキストが含まれる画像やドキュメントの、Computer Vision API を使用した光学式文字認識 (OCR) に関連する概念です。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: e0247560afa8229f4fa5c25ec7dfbbca4f7defb2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102486100"
---
# <a name="optical-character-recognition-ocr"></a>光学式文字認識 (OCR)

Azure の Computer Vision API には、画像から印刷または手書きのテキストを抽出する光学式文字認識 (OCR) 機能が含まれています。 ナンバー プレートの写真やシリアル番号の付いたコンテナーなどの画像からも、ドキュメント (請求書、明細書、財務報告書、記事など) からもテキストを抽出することができます。

## <a name="read-api"></a>Read API 

Computer Vision の [Read API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) は、印刷されたテキスト (複数の言語)、手書きのテキスト (英語のみ)、数字、通貨記号を、画像や複数ページの PDF ドキュメントから抽出する、Azure の最新の OCR テクノロジです ([新機能について学習する](./whats-new.md))。 これは、テキストの多い画像や、混合言語を含む複数ページの PDF ドキュメントからテキストを抽出するように最適化されています。 同じ画像またはドキュメントで、印刷と手書き両方のテキストの検出がサポートされています。

![OCR で画像やドキュメントからテキストを抽出して構造化された出力に変換するしくみ](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>入力の要件
**Read** 呼び出しにより、画像とドキュメントが入力として取得されます。 これには次の要件があります。

* サポートされているファイル形式: JPEG、PNG、BMP、PDF、TIFF
* PDF ファイルと TIFF ファイルの場合は、最大 2,000 ページ (Free レベルの場合は最初の 2 ページのみ) が処理されます。
* ファイル サイズは 50 MB 未満 (Free レベルの場合は 4 MB)、寸法は 50 x 50 ピクセル以上 10,000 x 10,000 ピクセル以下にする必要があります。 
* PDF の寸法は、17 x 17 インチ以下である必要があります (リーガル サイズまたは A3 サイズ以下の用紙に対応します)。

> [!NOTE]
> **言語の入力** 
>
> [Read 呼び出し](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005)には、言語に対する省略可能な要求パラメーターがあります。 読み取りでは、言語の自動識別と多言語ドキュメントがサポートされるため、言語コードの指定は、その特定の言語としてドキュメントを処理するように強制する場合にのみ行ってください。

## <a name="ocr-demo-examples"></a>OCR のデモ (例)

![OCR のデモ](./Images/ocr-demo.gif)

## <a name="step-1-the-read-operation"></a>手順 1: 読み取り操作

Read API の [Read 呼び出し](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005)は、画像または PDF ドキュメントを入力として受け取り、非同期でテキストを抽出します。 この呼び出しにより、`Operation-Location` という応答ヘッダー フィールドが返されます。 `Operation-Location` 値は、次の手順で使用する操作 ID を含む URL です。

|応答ヘッダー| 結果の URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> 「[Computer Vision の価格](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/)」ページには、Read の価格レベルが含まれています。 分析された画像またはページはそれぞれ 1 つのトランザクションです。 100 ページを含む PDF または TIFF ドキュメントを使用して操作を呼び出した場合、Read 操作ではそれは 100 トランザクションとしてカウントされ、100 トランザクションに対して課金されます。 操作に対して 50 回の呼び出しを行い、各呼び出しで 100 ページのドキュメントが送信された場合、50 X 100 = 5000 トランザクションに対して課金されます。

## <a name="step-2-the-get-read-results-operation"></a>手順 2:読み取り結果の取得操作

2 番目のステップでは、[読み取り結果の取得](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750)操作を呼び出します。 この操作は、読み取り操作によって作成された操作 ID を入力として受け取ります。 これにより、次の設定可能な値を持つ **status** フィールドが含まれた JSON 応答が返されます。 **succeeded** の値が返されるまで、この操作を対話形式で呼び出します。 1 秒あたりの要求 (RPS) レートを超えないようにするために、1 秒から 2 秒の間隔を使用してください。

|フィールド| 型 | 設定可能な値 |
|:-----|:----:|:----|
|status | string | notStarted: 操作は開始されていません。 |
| |  | running: 操作を処理しています。 |
| |  | failed: 操作は失敗しました。 |
| |  | succeeded: 操作は成功しました。 |

> [!NOTE]
> Free レベルでは、要求レートは 1 分あたり 20 回の呼び出しに制限されます。 有料レベルでは、10 RPS (1 秒あたりの要求) が許可されており、これはリクエストに応じて増加できます。 1 秒あたりの要求 (RPS) レートの引き上げをリクエストするには、Azure サポート チャネルまたはお客様のアカウント チームを使用してください。

**status** フィールドに **succeeded** 値が指定されている場合、JSON 応答には、画像またはドキュメントから抽出されたテキスト コンテンツが含まれます。 JSON の応答では、認識された単語の元の行グループが維持されます。 抽出されたテキスト行とその境界ボックスの座標が含まれます。 各テキスト行には、抽出されたすべての単語と、その座標および信頼度スコアが含まれています。

> [!NOTE]
> `Read` 操作に送信されたデータは一時的に暗号化され、しばらくの間保存されてから削除されます。 これにより、アプリケーションからは、サービス応答の一部として抽出されたテキストを取得できます。

## <a name="sample-json-output"></a>サンプル JSON 出力

成功した JSON 応答の例を次に示します。

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="natural-reading-order-output-latin-only"></a>自然な読み取り順序の出力 (ラテンのみ)
[Read 3.2 プレビュー API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) では、`readingOrder` クエリ パラメーターを使用して、テキスト行の出力順序を指定します。 次の例に示すように、`natural` を使用して、よりわかりやすい読み取り順序の出力を行います。 この機能は、ラテン語系の言語でのみサポートされています。

:::image border type="content" source="./Images/ocr-reading-order-example.png" alt-text="OCR の読み取り順序の例":::

## <a name="handwritten-classification-for-text-lines-latin-only"></a>テキスト行の手書き分類 (ラテンのみ)
[Read 3.2 プレビュー API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) 応答には、各テキスト行が手書きスタイルであるかどうかと、信頼度スコアが分類されます。 この機能は、ラテン語系の言語でのみサポートされています。 次の例は、画像内のテキストの手書き分類を示しています。

:::image border type="content" source="./Images/ocr-handwriting-classification.png" alt-text="OCR の手書き分類の例":::

## <a name="select-pages-or-page-ranges-for-text-extraction"></a>テキスト抽出のページまたはページ範囲の選択
[Read 3.2 プレビュー API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) では、大規模なマルチページ ドキュメントに対し、`pages` クエリ パラメーターを使用して、ページ番号またはページ範囲を指定し、それらのページからのみテキストを抽出します。 次の例は、10 ページを含むドキュメントを示しています。ここには、すべてのページ (1 から 10) と選択したページ (3 から 6) の両方に対して抽出されたテキストがあります。

:::image border type="content" source="./Images/ocr-select-pages.png" alt-text="選択されたページの出力":::

## <a name="supported-languages"></a>サポートされている言語
読み取り API では、印刷スタイルのテキストとして合計 73 言語がサポートされています。 [OCR でサポートされている言語](./language-support.md#optical-character-recognition-ocr)の完全な一覧を参照してください。 手書きスタイルの OCR は、英語でのみサポートされています。

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>クラウド API の使用またはオンプレミスでのデプロイ
Read 3.x クラウド API は、統合が簡単で、すぐに生産性を上げることができるため、ほとんどのお客様にとって推奨される選択肢です。 Azure と Computer Vision サービスがスケール、パフォーマンス、データ セキュリティ、コンプライアンスのニーズに対応する一方で、お客様は顧客のニーズを満たすことに集中できます。

オンプレミスでのデプロイの場合、[Read Docker コンテナー (プレビュー)](./computer-vision-how-to-install-containers.md) を使用すると、独自のローカル環境に新しい OCR 機能をデプロイできます。 コンテナーは、特定のセキュリティ要件とデータ ガバナンス要件に適しています。

## <a name="ocr-api"></a>OCR API

[OCR API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) では、古い認識モデルが使用されており、画像のみがサポートされ、同期的に実行されて、検出されたテキストは直ちに返されます。 [OCR でサポートされている言語](./language-support.md#optical-character-recognition-ocr)と Read API に関する記事をご覧ください。

> [!NOTE]
> Computer Vison 2.0 RecognizeText 操作は非推奨になる予定であり、この記事で取り上げている新しい Read API がその代わりになります。 既存顧客の皆様には、[読み取り操作をご利用いただくように](upgrade-api-versions.md)お願いします。

## <a name="next-steps"></a>次のステップ

- [Computer Vision REST API またはクライアント ライブラリ クイックスタート](./quickstarts-sdk/client-library.md)を始めます。
- [Read 3.1 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) について学習します。
- 合計 73 言語に対応した [Read 3.2 パブリック プレビュー REST API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) について説明します。
