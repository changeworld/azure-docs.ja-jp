---
title: Read API を呼び出す方法
titleSuffix: Azure Cognitive Services
description: Read API を呼び出して、その動作を詳細に構成する方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: pafarley
ms.openlocfilehash: ed19ddae799a743d3bc8e51c550e4a054ad42cf7
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2021
ms.locfileid: "129426753"
---
# <a name="call-the-read-api"></a>Read API を呼び出す

このガイドでは、Read API を呼び出して画像からテキストを抽出する方法について説明します。 この API の動作をニーズに合わせて構成できるさまざまな方法を学習します。

このガイドは、既に <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision リソースを作成"  target="_blank">Computer Vision リソースを作成</a>し、サブスクリプション キーとエンドポイント URL を取得していることを前提としています。 まだの場合は、[クイックスタート](../quickstarts-sdk/client-library.md)に従って作業を開始してください。

## <a name="determine-how-to-process-the-data-optional"></a>データの処理方法を決定する (省略可能)

### <a name="specify-the-ocr-model"></a>OCR モデルの指定

既定では、サービスは最新の GA モデルを使用してテキストを抽出します。 Read 3.2 以降では、`model-version` パラメーターによって、所与の API バージョンの GA とプレビュー モデルのどちらかを選択できます。 指定したモデルは、読み取り操作でテキストを抽出するために使用されます。

読み取り操作を使用する場合は、省略可能な `model-version` パラメーターに次の値を使用します。

|値| 使用モデル |
|:-----|:----|
| Not provided | 最新の GA モデルと言語 |
| latest | 最新の GA モデルと言語|
| 2021-09-30-preview | 追加のプレビュー言語と機能を使用したプレビューモデル。 以前の GA モデルに対する機能強化が含まれています。
| 2021-04-12 | 日付固有の GA (現在は最新のものと同じ) |

### <a name="input-language"></a>入力言語

既定で、サービスでは、イメージまたは混合言語を含むドキュメントからすべてのテキストを抽出します。 [読み取り操作](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)には、言語に対する省略可能な要求パラメーターがあります。 その特定の言語としてドキュメントを処理するように強制する場合にのみ言語コードを指定してください。 そうしないと、サービスが不完全で不正確なテキストを返すことがあります。

### <a name="natural-reading-order-output-latin-languages-only"></a>自然な読み取り順序の出力 (ラテン言語のみ)

既定では、サービスによって、テキスト行が左から右の順に出力されます。 次の例に示すように、`readingOrder` 要求パラメーターに `natural` を使用してよりわかりやすい読み取り順序の出力を行います。 この機能は、ラテン語系の言語でのみサポートされています。

:::image type="content" source="../Images/ocr-reading-order-example.png" alt-text="OCR の読み取り順序の例" border="true" :::

### <a name="select-pages-or-page-ranges-for-text-extraction"></a>テキスト抽出のページまたはページ範囲の選択

既定では、サービスはドキュメント内のすべてのページからテキストを抽出します。 必要に応じて、`pages` 要求パラメーターを使用して、ページ番号またはページ範囲を指定し、それらのページからのみテキストを抽出します。 次の例は、10 ページを含むドキュメントを示しています。ここには、すべてのページ (1 から 10) と選択したページ (3 から 6) の両方に対して抽出されたテキストがあります。

:::image type="content" source="../Images/ocr-select-pages.png" alt-text="選択されたページの出力" border="true" :::

## <a name="submit-data-to-the-service"></a>サービスにデータを送信する

ローカルの画像またはリモートの画像を Read API に送信します。 ローカルの場合、バイナリ画像データを HTTP 要求本文に配置します。 リモートの場合、要求本文を `{"url":"http://example.com/images/test.jpg"}` のような形式にして、画像の URL を指定します。

Read API の [Read 呼び出し](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)は、画像または PDF ドキュメントを入力として受け取り、非同期でテキストを抽出します。

`https://{endpoint}/vision/v3.2/read/analyze[?language][&pages][&readingOrder]`

この呼び出しにより、`Operation-Location` という応答ヘッダー フィールドが返されます。 `Operation-Location` 値は、次の手順で使用する操作 ID を含む URL です。

|応答ヘッダー| 値の例 |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.2/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> 「[Computer Vision の価格](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/)」ページには、Read の価格レベルが含まれています。 分析された画像またはページはそれぞれ 1 つのトランザクションです。 100 ページを含む PDF または TIFF ドキュメントを使用して操作を呼び出した場合、Read 操作ではそれは 100 トランザクションとしてカウントされ、100 トランザクションに対して課金されます。 操作に対して 50 回の呼び出しを行い、各呼び出しで 100 ページのドキュメントが送信された場合、50 X 100 = 5000 トランザクションに対して課金されます。


## <a name="get-results-from-the-service"></a>サービスから結果を取得する

2 番目のステップでは、[読み取り結果の取得](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d9869604be85dee480c8750)操作を呼び出します。 この操作は、読み取り操作によって作成された操作 ID を入力として受け取ります。

`https://{endpoint}/vision/v3.2/read/analyzeResults/{operationId}`

これにより、次の設定可能な値を持つ **status** フィールドが含まれた JSON 応答が返されます。

|値 | 説明 |
|:-----|:----|
| `notStarted`| 操作は開始されていません。 |
| `running`| 操作を処理しています。 |
| `failed`| 操作は失敗しました。 |
| `succeeded`| 操作は成功しました。 |

**succeeded** の値が返されるまで、この操作を対話形式で呼び出します。 1 秒あたりの要求 (RPS) レートを超えないようにするために、1 秒から 2 秒の間隔を使用してください。

> [!NOTE]
> Free レベルでは、要求レートは 1 分あたり 20 回の呼び出しに制限されます。 有料レベルでは、10 RPS (1 秒あたりの要求) が許可されており、これはリクエストに応じて増加できます。 Azure リソース ID とリージョンをメモし、Azure サポート チケットを開くか、アカウント チームに連絡して、1 秒あたりの要求 (RPS) レートの引き上げをリクエストします。

**status** フィールドに `succeeded` 値が指定されている場合、JSON 応答には、画像またはドキュメントから抽出されたテキスト コンテンツが含まれます。 JSON の応答では、認識された単語の元の行グループが維持されます。 抽出されたテキスト行とその境界ボックスの座標が含まれます。 各テキスト行には、抽出されたすべての単語と、その座標および信頼度スコアが含まれています。

> [!NOTE]
> `Read` 操作に送信されたデータは一時的に暗号化され、しばらくの間保存されてから削除されます。 これにより、アプリケーションからは、サービス応答の一部として抽出されたテキストを取得できます。

### <a name="sample-json-output"></a>サンプル JSON 出力

成功した JSON 応答の例を次に示します。

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          }
        ]
      }
    ]
  }
}
```

### <a name="handwritten-classification-for-text-lines-latin-languages-only"></a>テキスト行の手書き分類 (ラテン言語のみ)

応答では、各テキスト行が手書きスタイルであるかどうかと、信頼度スコアが分類されます。 この機能は、ラテン語系の言語でのみサポートされています。 次の例は、画像内のテキストの手書き分類を示しています。

:::image type="content" source="../Images/ocr-handwriting-classification.png" alt-text="OCR の手書き分類の例" border="true" :::

## <a name="next-steps"></a>次のステップ

- [OCR (Read) REST API またはクライアント ライブラリのクイックスタート](../quickstarts-sdk/client-library.md)を参照して使用を開始します。
- [Read 3.2 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) について学習します。
