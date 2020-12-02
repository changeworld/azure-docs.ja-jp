---
title: レイアウト - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Form Recognizer API を使用したレイアウト分析に関連する概念 (使用法と制限) について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: 60ef490bbba4dca4f01b82967ad6ea8636e7bd4e
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353699"
---
# <a name="form-recognizer-layout-service"></a>Form Recognizer レイアウト サービス

Azure Form Recognizer を使用すると、レイアウト サービスを使用して、ドキュメントからテキスト、テーブル、選択マーク、構造情報を抽出できます。 Layout API を使用すると、お客様は、さまざまな形式のドキュメントを渡して、構造化されたデータとドキュメントの表現を取得できます。 強力な[光学式文字認識 (OCR)](../computer-vision/concept-recognizing-text.md) 機能と、ドキュメントを解釈するディープ ラーニング モデルを組み合わせて、ドキュメントのテキスト、テーブル、選択マーク、構造を抽出します。 

## <a name="what-does-the-layout-service-do"></a>レイアウト サービスの機能

Layout API を使用すると、非常に優れた精度でドキュメントからテキスト、テーブル、選択マーク、構造の情報が抽出され、それが構造化された JSON 応答に整理されて返されます。 ドキュメントは、携帯電話でキャプチャされた画像、スキャンされたドキュメント、デジタル PDF など、形式や品質が異なっていてもかまいません。 Layout API により、このようなすべてのドキュメントから、構造化された出力が抽出されます。

![レイアウトの例](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>試してみる

Form Recognizer のレイアウト サービスを試してみるには、オンラインのサンプル UI ツールにアクセスしてください。

> [!div class="nextstepaction"]
> [サンプル UI](https://fott-preview.azurewebsites.net/)

Form Recognizer Layout API を試すには、Azure サブスクリプション ([無料で作成](https://azure.microsoft.com/free/cognitive-services)) と [Form Recognizer リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) エンドポイントおよびキーが必要です。 

![サンプル UI のスクリーンショット。ドキュメントのテキスト、テーブル、選択マークが分析されている](./media/analyze-layout.png)

### <a name="input-requirements"></a>入力の要件 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>レイアウト分析操作

[レイアウト分析](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)操作を使用すると、ドキュメント (画像、TIFF、または PDF ファイル) が入力として取得されて、ドキュメントのテキスト、テーブル、選択マーク、構造が抽出されます。 この呼び出しにより、`Operation-Location` という応答ヘッダー フィールドが返されます。 `Operation-Location` 値は、次の手順で使用される結果 ID を含む URL です。

|応答ヘッダー| 結果の URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-layout-result-operation"></a>レイアウト分析結果取得操作

2 番目の手順では、[レイアウト分析結果取得](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult)操作を呼び出します。 この操作には、レイアウト分析操作によって作成された結果 ID を入力として渡します。 これにより、次の設定可能な値を持つ **status** フィールドが含まれた JSON 応答が返されます。 

|フィールド| 型 | 設定可能な値 |
|:-----|:----:|:----|
|status | string | `notStarted`:分析操作は開始されていません。<br /><br />`running`:分析操作が進行中です。<br /><br />`failed`:分析操作は失敗しました。<br /><br />`succeeded`:分析操作は成功しました。|

`succeeded` の値が返されるまで、この操作を対話形式で呼び出します。 1 秒あたりの要求数 (RPS) を超えないようにするために、間隔は 3 - 5 秒あけてください。

**status** フィールドの値が `succeeded` である場合、JSON の応答には、レイアウト抽出の結果である抽出されたテキスト、テーブル、選択マークが含まれます。 抽出されるデータには、抽出されたテキスト行と単語、境界ボックス、テキスト外観の手書き指示、テーブル、選択マークと選択または非選択の指示が含まれています。 

### <a name="sample-json-output"></a>サンプル JSON 出力

レイアウト分析結果取得操作への応答は、抽出されたすべての情報が含まれるドキュメントの構造化表現になります。 [サンプルのドキュメント ファイル](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf)と、その構造化された出力である[サンプルのレイアウト出力](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)を参照してください。

JSON 出力には次の 2 つの部分があります。 
* `"readResults"` ノードには、認識されたすべてのテキストと選択マークが格納されます。 テキストは、まずページごとに整理され、そのうえで行ごと、さらに個々の単語ごとに整理されます。 
* `"pageResults"` ノードには、境界ボックスで抽出されたテーブルとセル、信頼度、および "readResults" 内の行と単語への参照が格納されます。

## <a name="example-output"></a>出力例

### <a name="text"></a>テキスト

レイアウトにより、ドキュメント (PDF、TIFF) と画像 (jpg、png、bmp) から、さまざまなテキストの角度、色、角度、ドキュメントの写真、FAX、印刷、手書き (英語のみ)、および混合モードのテキストが抽出されます。 テキストは、行、単語、境界ボックス、信頼度スコア、スタイル (手書きなど) に関する情報と共に抽出されます。 すべてのテキスト情報は、JSON 出力の `"readResults"` セクションに含まれています。 

### <a name="tables"></a>テーブル

レイアウトにより、ドキュメント (PDF、TIFF) と画像 (jpg、png、bmp) からテーブルが抽出されます。 ドキュメントは、スキャンされたもの、写真、またはデジタル化されたもののいずれでもかまいません。 セルや列が結合されている複雑なテーブル、境界線があるもの、またはないもの、不規則な角度のテーブルなどにも対応できます。 抽出されるテーブルには、列と行の数、行の範囲、列の範囲が含まれます。 各セルと共に、その境界ボックスと、`"readResults"` セクションに抽出されたテキストへの参照が抽出されます。 テーブルの情報は、JSON 出力の `"pageResults"` セクションにあります。 

![テーブルの例](./media/tables-example.jpg)

### <a name="selection-marks"></a>選択マーク

レイアウトにより、選択マークもドキュメントから抽出されます。 抽出される選択マークには、境界ボックス、信頼度、状態 (選択/非選択) が含まれます。 選択マークの情報は、JSON 出力の `"readResults"` セクションに抽出されます。 

## <a name="next-steps"></a>次のステップ

- [Form Recognizer のサンプル UI](https://fott-preview.azurewebsites.net/) を使用して、独自のレイアウト抽出を試してみてください
- 選択した言語でのレイアウト抽出を始めるには、[Form Recognizer クライアント ライブラリのクイックスタート](quickstarts/client-library.md)を行います。
- または、[レイアウト データ抽出](./QuickStarts/python-layout.md)のクイックスタートに従い、Python と REST API を使用してレイアウト データ抽出を実装します。

## <a name="see-also"></a>関連項目

* [Form Recognizer とは](./overview.md)
* [REST API リファレンス ドキュメント](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)