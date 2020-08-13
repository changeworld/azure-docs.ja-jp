---
title: 光学式文字認識 (OCR) - Computer Vision
titleSuffix: Azure Cognitive Services
description: 印刷されたテキストと手書きのテキストが含まれる画像やドキュメントからの、Computer Vision API を使用した光学式文字認識 (OCR) に関連する概念です。
services: cognitive-services
author: msbbonsu
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: t-bebon
ms.custom: seodec18
ms.openlocfilehash: 2b3f9b0a4bec76f1f5f9b1f42ec33fdf5e2678bf
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760160"
---
# <a name="optical-character-recognition-ocr"></a>光学式文字認識 (OCR)

Microsoft の Computer Vision API には、画像や PDF ドキュメントから印刷されたテキストまたは手書きのテキストを抽出する、光学式文字認識 (OCR) 機能が含まれます。 OCR API は、アナログ ドキュメント (画像、スキャンされたドキュメント) とデジタル化されたドキュメントの両方からテキストを抽出します。 ライセンス プレートの写真やシリアル番号の付いたコンテナーなど、整っていない画像からでも、ドキュメント (請求書、請求書、財務報告、記事など) からでも、テキストを抽出することができます。 新しい Read OCR API は、クラウドまたはオンプレミス (コンテナー) の管理サービスの一部として利用できます。 また、エンタープライズ グレードのコンプライアンスとプライバシーのニーズに対応するため、仮想ネットワークとプライベート エンドポイントもサポートされています。

## <a name="read-api"></a>Read API 

Computer Vision の [Read API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) は、印刷されたテキスト (複数の言語)、手書きのテキスト (英語のみ)、数字、通貨記号を、画像や複数ページの PDF ドキュメントから抽出する、Microsoft の最新の OCR テクノロジです。 整然としていないテキストの多い画像や、言語が混ざっている複数ページの PDF ドキュメントからテキストを抽出するように最適化されています。 同じ画像またはドキュメントからの、印刷されたテキストと手書きのテキスト (英語のみ) の検出がサポートされています。 [OCR でサポートされている言語](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)ページの完全な一覧を参照してください。

### <a name="how-ocr-works"></a>OCR のしくみ

[Read API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) では、最大 2,000 ページまでのテキストの多いドキュメントがサポートされているため、非同期的に実行されます。 最初のステップは、読み取り操作を呼び出すことです。 読み取り操作では、入力として画像または PDF ドキュメントが取得されて、操作 ID が返されます。 

2 番目のステップは、[結果の取得](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750)操作を呼び出すことです。 この操作では、読み取り操作によって作成された操作 ID が取得されます。 その後、画像またはドキュメントから抽出されたテキスト コンテンツが JSON 形式で返されます。 JSON の応答では、認識された単語の元の行グループが維持されます。 抽出されたテキスト行とその境界ボックスの座標が含まれます。 各テキスト行には、抽出されたすべての単語と、その座標および信頼度スコアが含まれています。

次の図に示されているように、Read では、必要に応じて、画像の水平軸を中心とした回転のオフセットを度数で返すことによって、認識されたページの回転が修正されます。

![OCR で画像やドキュメントからテキストを抽出して構造化された出力に変換するしくみ](./Images/how-ocr-works.svg)

### <a name="sample-ocr-output"></a>OCR 出力のサンプル

成功した応答は、次の例に示すように JSON 形式で返されます。

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.0.0",
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

C# と REST API を使用した OCR の実装については、[印刷されたテキストと手書きテキストの抽出](./QuickStarts/CSharp-hand-text.md)に関するクイックスタートに従ってください。

### <a name="input-requirements"></a>入力の要件

Read API は、次の入力を受け取ります。
* サポートされているファイル形式: JPEG、PNG、BMP、PDF、TIFF
* PDF と TIFF については、最大 2,000 ページまで処理されます。 Free レベルのサブスクライバーの場合は、最初の 2 ページだけが処理されます。
* ファイル サイズは 50 MB 未満でなければならず、寸法は 50 x 50 ピクセル以上 10,000 x 10,000 ピクセル以下です。
* PDF の寸法は、17 x 17 インチ以下である必要があります (リーガル サイズまたは A3 サイズ以下の用紙に対応します)。

### <a name="text-from-images"></a>画像からのテキスト

次の Read API の出力では、さまざまな角度、色、フォントのテキストが含まれる画像から抽出されたテキスト行と単語が示されています

![回転されている画像と、読み取られて輪郭が描かれたテキスト](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>ドキュメントからのテキスト

画像に加えて、Read API は PDF ドキュメントを入力として受け取ります。

![回転されている画像と、読み取られて輪郭が描かれたテキスト](./Images/text-from-documents-example.png)


### <a name="handwritten-text-in-english"></a>英語の手書きテキスト

現在、読み取り操作の手書きテキストの抽出でサポートされてるのは、英語だけです。

![回転されている画像と、読み取られて輪郭が描かれたテキスト](./Images/handwritten-example.png)

### <a name="printed-text-in-supported-languages"></a>サポートされている言語での印刷テキスト

Read 3.0 API の印刷テキストの抽出でサポートされている言語は、英語、スペイン語、ドイツ語、フランス語、イタリア語、ポルトガル語、オランダ語です。 [Read 3.1 API パブリック プレビュー](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)では、簡体字中国語のサポートが追加されます。 さらに多くの言語をサポートする必要があるシナリオの場合は、このドキュメントの「OCR API」で概要を参照してください。 すべての [OCR でサポートされている言語](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)の一覧を参照してください

![回転されている画像と、読み取られて輪郭が描かれたテキスト](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>混合言語のサポート

Read API では、複数の言語が含まれる画像とドキュメントがサポートされており、一般に混合言語ドキュメントと呼ばれます。 これは、テキスト コンテンツを抽出する前に、ドキュメントの各テキスト行を検出された言語に分類することによって行われます。

![回転されている画像と、読み取られて輪郭が描かれたテキスト](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Read サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、[Microsoft セキュリティ センター](https://www.microsoft.com/en-us/trust-center/product-overview)の Cognitive Services のページを参照してください。

### <a name="containers-for-on-premise-deployment"></a>オンプレミス デプロイのコンテナー

Read は Docker コンテナー (プレビュー) としても利用でき、独自の環境に新しい OCR 機能を展開できます。 コンテナーは、特定のセキュリティ要件とデータ ガバナンス要件に適しています。 [Read コンテナーをインストールして実行する方法](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)に関するページを参照してください。


## <a name="ocr-api"></a>OCR API

[OCR API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) では、古い認識モデルが使用されており、画像のみがサポートされ、同期的に実行されて、検出されたテキストは直ちに返されます。 [OCR でサポートされている言語](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)と Read API を参照してください。

## <a name="next-steps"></a>次のステップ

- [Read 3.0 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) について学習します。
- 簡体字中国語のサポートが追加された [Read 3.1 パブリック プレビュー REST API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) について学習します。
- [テキストの抽出](./QuickStarts/CSharp-hand-text.md)に関するクイックスタートに従い、C#、Java、JavaScript、または Python と REST API を使用して、OCR を実装します。
