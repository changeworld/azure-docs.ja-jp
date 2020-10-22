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
ms.openlocfilehash: 32759ed59e280980abdced46c29390e00ee7229c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973276"
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

### <a name="read-32-preview-allows-selecting-pages"></a>Read 3.2 プレビューではページの選択が可能
[Read 3.2 プレビュー API](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005) では、大規模な複数ページのドキュメントに対して、特定のページ番号またはページ範囲を入力パラメーターとして指定して、それらのページからのみテキストを抽出することができます。 これは、省略可能な言語パラメーターに加えて、新しい入力パラメーターです。

> [!NOTE]
> **言語の入力** 
>
> [Read 呼び出し](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005)には、言語に対する省略可能な要求パラメーターがあります。 これは、ドキュメント内のテキストの BCP-47 言語コードです。 読み取りでは、言語の自動識別と多言語ドキュメントがサポートされるため、言語コードの指定は、その特定の言語としてドキュメントを処理するように強制する場合にのみ行ってください。

## <a name="the-read-call"></a>Read 呼び出し

Read API の [Read 呼び出し](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005)は、画像または PDF ドキュメントを入力として受け取り、非同期でテキストを抽出します。 この呼び出しにより、`Operation-Location` という応答ヘッダー フィールドが返されます。 `Operation-Location` 値は、次の手順で使用する操作 ID を含む URL です。

|応答ヘッダー| 結果の URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.2/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> 「[Computer Vision の価格](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/)」ページには、Read の価格レベルが含まれています。 分析された画像またはページはそれぞれ 1 つのトランザクションです。 100 ページを含む PDF または TIFF ドキュメントを使用して操作を呼び出した場合、Read 操作ではそれは 100 トランザクションとしてカウントされ、100 トランザクションに対して課金されます。 操作に対して 50 回の呼び出しを行い、各呼び出しで 100 ページのドキュメントが送信された場合、50 X 100 = 5000 トランザクションに対して課金されます。

## <a name="the-get-read-results-call"></a>読み取り結果の取得呼び出し

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
        "language": "en",
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
### <a name="read-32-preview-adds-text-line-style-latin-languages-only"></a>Read 3.2 プレビューでのテキスト行スタイルの追加 (ラテン語系の言語のみ)
[Read 3.2 プレビュー API](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005) では、各テキスト行のスタイルが印刷または手書きかどうかを分類する**外観**オブジェクトが、信頼スコアとともに出力されます。 この機能は、ラテン語系の言語でのみサポートされています。

```json
  "appearance": {
              "style": "handwriting",
              "styleConfidence": 0.836
            }
```
[Computer Vision OCR SDK クイックスタート](./quickstarts-sdk/client-library.md)と [Read REST API クイックスタート](./QuickStarts/CSharp-hand-text.md)を使用して、アプリケーションへの OCR 機能の統合を開始します。

## <a name="supported-languages-for-print-text"></a>印刷テキストでサポートされている言語
[Read 3.2 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) の印刷テキストの抽出でサポートされている言語は、英語、スペイン語、ドイツ語、フランス語、イタリア語、ポルトガル語、オランダ語です。

OCR でサポートされている言語の完全な一覧については、[サポートされている言語](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)に関する記事をご覧ください。

### <a name="read-32-preview-adds-simplified-chinese-and-japanese"></a>Read 3.2 プレビューで追加された簡体字中国語と日本語
[Read 3.2 API パブリック プレビュー](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)では、簡体字中国語と日本語のサポートが追加されています。 お客様のシナリオでさらに多くの言語をサポートする必要がある場合は、「[OCR API](#ocr-api)」セクションを参照してください。 

## <a name="supported-languages-for-handwritten-text"></a>手書きテキストに対してサポートされている言語
現在、読み取り操作の手書きテキストの抽出でサポートされているのは、英語だけです。

## <a name="use-the-rest-api-and-sdk"></a>REST API と SDK を使用する
[Read 3.x REST API](./QuickStarts/CSharp-hand-text.md) は、統合が簡単で、すぐに生産性を上げることができるため、ほとんどのお客様にとって推奨される選択肢です。 Azure と Computer Vision サービスがスケール、パフォーマンス、データ セキュリティ、コンプライアンスのニーズに対応する一方で、お客様は顧客のニーズを満たすことに集中できます。

## <a name="deploy-on-premise-with-docker-containers"></a>Docker コンテナーを使用したオンプレミスのデプロイ
[Read 2.0 Docker コンテナー (プレビュー)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers) を使用すると、独自のローカル環境に新しい OCR 機能をデプロイできます。 コンテナーは、特定のセキュリティ要件とデータ ガバナンス要件に適しています。

## <a name="example-outputs"></a>出力例

### <a name="text-from-images"></a>画像からのテキスト

次の Read API の出力は、さまざまなテキスト角度、色、フォントが含まれる、画像から抽出されたテキストを示しています。

![抽出されたテキストが一覧表示された、さまざまな色と角度を持つ複数の単語の画像](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>ドキュメントからのテキスト

Read API は、PDF ドキュメントを入力として取ることもできます。

![抽出されたテキストが一覧表示された請求書ドキュメント](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>手書きのテキスト

読み取り操作では、画像から手書きのテキストが抽出されます (現在は英語のみ)。

![抽出されたテキストが一覧表示された手書きのメモの画像](./Images/handwritten-example.png)

### <a name="printed-text"></a>印刷されたテキスト

読み取り操作では、複数の異なる言語で印刷されたテキストを抽出できます。

![抽出されたテキストが一覧表示されたスペイン語の教科書の画像](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>混合言語ドキュメント

Read API は、一般に混合言語ドキュメントと呼ばれる、複数の異なる言語を含む画像とドキュメントをサポートしています。 これは、テキスト コンテンツを抽出する前に、ドキュメント内の各テキスト行を検出された言語に分類することによって機能します。

![抽出されたテキストが一覧表示された、複数言語の語句の画像](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>OCR API

[OCR API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) では、古い認識モデルが使用されており、画像のみがサポートされ、同期的に実行されて、検出されたテキストは直ちに返されます。 [OCR でサポートされている言語](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)と Read API に関する記事をご覧ください。

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、読み取り/OCR サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに注意する必要があります。 詳細については、[Microsoft セキュリティ センター](https://www.microsoft.com/trust-center/product-overview)の Cognitive Services のページを参照してください。

> [!NOTE]
> Computer Vison 2.0 RecognizeText 操作は非推奨になる予定であり、この記事で取り上げている新しい Read API がその代わりになります。 既存顧客の皆様には、[読み取り操作をご利用いただくように](upgrade-api-versions.md)お願いします。

## <a name="next-steps"></a>次のステップ

- C#、Java、JavaScript、または Python で [Computer Vision 3.0 SDK のクイックスタート](./quickstarts-sdk/client-library.md)を開始します。
- C#、Java、JavaScript、または Python で [Read 3.1 REST API クイックスタート](./QuickStarts/CSharp-hand-text.md) を使用して、REST API の使用方法を学習します。
- [Read 3.1 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) について学習します。
- 簡体字中国語と日本語のサポートが追加された [Read 3.2 パブリック プレビュー REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005) について学習します。
