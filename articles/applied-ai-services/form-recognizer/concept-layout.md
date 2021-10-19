---
title: レイアウト - Form Recognizer
titleSuffix: Azure Applied AI Services
description: Form Recognizer API を使用したレイアウト分析に関連する概念 (使用法と制限) について説明します。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 425a5cb9ab332076ddd0d745bba7b914e5601917
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129707149"
---
# <a name="form-recognizer-layout-model"></a>Form Recognizer レイアウト モデル

Azure Form Recognizer の Layout API を使用すると、ドキュメント (PDF、TIFF) と画像 (JPG、PNG、BMP) から、テキスト、テーブル、選択マーク、および構造情報を抽出できます。 これを使用すると、お客様はさまざまな形式のドキュメントを受け取り、ドキュメントの構造化されたデータ表現を返すことができます。 これは、Microsoft の強力な[光学式文字認識 (OCR)](../../cognitive-services/computer-vision/overview-ocr.md) 機能の強化バージョンと、ディープ ラーニング モデルを組み合わせ、テキスト、テーブル、選択マーク、ドキュメント構造を抽出します。

##### <a name="sample-form-processed-with-form-recognizer-sample-labeling-tool--layout-feature"></a>[Form Recognizer サンプル ラベル付けツール](https://fott-2-1.azurewebsites.net/)のレイアウト機能を使用して処理されたサンプル フォーム:

:::image type="content" source="media/layout-demo.gif" alt-text="{alt-text}":::

**データ抽出機能**

| **レイアウト モデル**   | **テキスト抽出**   | **選択マーク**   | **テーブル**  |
| --- | --- | --- | --- |
| Layout  | ✓  | ✓  | ✓  |

## <a name="try-form-recognizer-studio-preview"></a>Form Recognizer スタジオ (プレビュー) を試す

* Form Recognizer スタジオは、プレビュー (v3.0) API で使用できます。

* Form Recognizer スタジオのレイアウト機能を使用して、フォームとドキュメントからテーブル、チェック ボックス、テキストを抽出します。

> [!div class="nextstepaction"]
> [Form Recognizer スタジオを試す](https://formrecognizer.appliedai.azure.com/studio/layout)

## <a name="try-form-recognizer-sample-labeling-tool"></a>Form Recognizer サンプル ラベル付けツールを試す

サンプル ラベル付けツールを試すことで、レイアウト データがどのように抽出されるかを確認できます。 次が必要です。

* Azure サブスクリプション — [無料で作成する](https://azure.microsoft.com/free/cognitive-services/)ことができます

* Azure portal の [Form Recognizer インスタンス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 Free 価格レベル (`F0`) を利用して、サービスを試用できます。 リソースがデプロイされたら、 **[リソースに移動]** をクリックして API キーとエンドポイントを取得します。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

* フォーム ドキュメント。 [サンプル フォーム ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)を使用できます。

> [!div class="nextstepaction"]
  > [試してみる](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

  Form Recognizer UI で以下の手順を実行します。

  1. **[Use Layout to get text, tables, and selection marks]\(レイアウトを使用してテキスト、テーブル、および選択マークを取得する\)** を選択します。
  1. ドロップダウン メニューから、 **[ローカル ファイル]** を選択します。
  1. ファイルをアップロードし、 **[Run Layout]\(レイアウトの実行\)** を選択します

  :::image type="content" source="media/try-layout.png" alt-text="スクリーンショット: スクリーンショット: サンプル ラベル付けツールのドロップダウンのレイアウト ファイル ソース選択メニュー。":::

## <a name="input-requirements"></a>入力の要件

* 最適な結果を得るには、ドキュメントごとに 1 つの鮮明な写真または高品質のスキャンを提供してください。
* サポートされているファイル形式: JPEG、PNG、BMP、TIFF、および PDF (テキスト埋め込みまたはスキャン済み)。 文字の抽出と位置に関するエラーが発生する可能性を排除するには、テキストが埋め込まれている PDF が最適です。
* PDF および TIFF の場合、最大 2000 ページが処理されます (Free レベルのサブスクリプションでは、最初の 2 ページのみが処理されます)。
* ファイル サイズは 50 MB 未満である必要があります。
* 画像の寸法は、50 x 50 ピクセルから 10,000 x 10,000 ピクセルの間である必要があります。
* PDF の寸法は、17 x 17 インチまでで、Legal または A3 サイズ以下の用紙に対応します。
* トレーニング データ セットの合計サイズは、500 ページ以下です。
* PDF がパスワードでロックされている場合は、送信前にロックを解除する必要があります。
* 教師なし学習の場合 (ラベルの付いたデータなし):
  * データには、キーと値を含める必要があります。
  * キーは値の上または左に配置されている必要があり、下または右に配置されていてはいけません。

> [!NOTE]
> [サンプル ラベル付けツール](https://fott-2-1.azurewebsites.net/)では、BMP ファイル形式はサポートされていません。 これは、Form Recognizer サービスではなく、ツールの制限です。

## <a name="supported-languages-and-locales"></a>サポートされている言語とロケール

 Form Recognizer のプレビュー バージョンでは、レイアウト モデルに追加の言語サポートが導入されています。 サポートされている手書き文字と印刷テキストの完全な一覧については、[言語サポート](language-support.md#layout-and-custom-model)に関する記事を "*参照してください*"。

## <a name="features"></a>特徴

### <a name="tables-and-table-headers"></a>テーブルとテーブル ヘッダー

Layout API では、JSON 出力の `pageResults` セクションにテーブルが抽出されます。 ドキュメントは、スキャンされたもの、写真、またはデジタル化されたもののいずれでもかまいません。 テーブルは、セルまたは列が結合されている複雑なものや、罫線があるもの、またはないもの、不規則な角度のものも含まれます。 抽出されるテーブル情報には、列と行の数、行の範囲、列の範囲が含まれます。 境界ボックスのある各セルは、ヘッダーの一部として認識されているかどうかにかかわらず、情報と共に出力されます。 モデル予測ヘッダー セルは、複数の行にまたがることがあるうえ、必ずしもテーブルの最初の行であるとは限りません。 これらは、回転されたテーブルに対しても機能します。 各テーブル セルには、`readResults` セクションの個々の単語への参照を含むフル テキストも含まれます。

:::image type="content" source="./media/layout-table-headers-example.png" alt-text="レイアウトのテーブル ヘッダーの出力":::

### <a name="selection-marks"></a>選択マーク

Layout API により、ドキュメントから選択マークも抽出されます。 抽出される選択マークには、境界ボックス、信頼度、状態 (選択または非選択) が含まれます。 選択マークの情報は、JSON 出力の `readResults` セクションに抽出されます。

:::image type="content" source="./media/layout-selection-marks.png" alt-text="レイアウト選択マークの出力":::

### <a name="text-lines-and-words"></a>テキスト行と単語

Layout API により、ドキュメントと画像から、複数のテキストの角度や色でテキストが抽出されます。 この対象になるのは、ドキュメント、FAX、印刷および手書きのテキスト (英語のみ)、混合モードの写真です。 テキストは、行、単語、境界ボックス、信頼度スコア、スタイル (手書きなど) に関して提供される情報と共に抽出されます。 すべてのテキスト情報は、JSON 出力の `readResults` セクションに含まれています。

:::image type="content" source="./media/layout-text-extraction.png" alt-text="レイアウトのテキスト抽出の出力":::

### <a name="natural-reading-order-for-text-lines-latin-only"></a>テキスト行に自然言語の読み取り順序を使用 (ラテン語系のみ)

`readingOrder` クエリ パラメーターを使用し、テキスト行の出力順序を指定できます。 次の例に示すように、`natural` を使用して、よりわかりやすい読み取り順序の出力を行います。 この機能は、ラテン語系の言語でのみサポートされています。

:::image type="content" source="./media/layout-reading-order-example.png" alt-text="レイアウトの読み取り順序の例" lightbox="../../cognitive-services/Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="handwritten-classification-for-text-lines-latin-only"></a>テキスト行の手書き分類 (ラテンのみ)

応答では、各テキスト行が手書きスタイルであるかどうかと、信頼度スコアが分類されます。 この機能は、ラテン語系の言語でのみサポートされています。 次の例は、画像内のテキストの手書き分類を示しています。

:::image type="content" source="./media/layout-handwriting-classification.png" alt-text="手書きの分類の例":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>テキスト抽出のページ番号または範囲の選択

ページが複数にわたる大きなドキュメントの場合、`pages` クエリ パラメーターを使用し、テキスト抽出対象として特定のページ番号またはページ範囲を示します。 次の例は、10 ページを含むドキュメントを示しています。ここには、すべてのページ (1 から 10) と選択したページ (3 から 6) の両方に対して抽出されたテキストがあります。

:::image type="content" source="./media/layout-select-pages-for-text.png" alt-text="レイアウトが選択されたページの出力":::

## <a name="form-recognizer-preview-v30"></a>Form Recognizer プレビュー v3.0

 Form Recognizer プレビューでは、いくつかの新機能が導入されています。

* アプリケーションとワークフローでプレビュー バージョンを使用する方法については、[**Form Recognizer v3.0 移行ガイド**](v3-migration-guide.md)に関する記事を参照してください。

* プレビュー バージョンと新機能の詳細については、[**REST API (プレビュー)** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

* Form Recognizer のクイックスタートを完了する:

  > [!div class="nextstepaction"]
  > [Form Recognizer のクイックスタート](quickstarts/try-sdk-rest-api.md)

* REST API を調べる:

    > [!div class="nextstepaction"]
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)