---
title: Form Recognizer の請求書モデル
titleSuffix: Azure Applied AI Services
description: 事前に構築された請求書モデルを使用するデータ抽出と分析を含む概念
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0f9c2f1603a87e30b0db32041f7d7aeff259600e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027363"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-invoice-model"></a>Form Recognizer の請求書モデル

 請求書モデルを使用すると、強力な光学式文字認識 (OCR) 機能と、ディープ ラーニング モデルを組み合わせて、売上の請求書の主要なフィールドと品目を分析し、抽出することができます。  電話でキャプチャされた画像、スキャンされたドキュメント、デジタル PDF など、さまざまな形式や品質の請求書を使用できます。 API によって請求書のテキストが分析され、顧客名、請求先住所、期限、支払金額などの主要な情報が抽出されて、構造化された JSON データ表現が返されます。

**[Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice) で処理された請求書のサンプル**:

:::image type="content" source="media/studio/overview-invoices.png" alt-text="サンプル請求書" lightbox="media/overview-invoices-big.jpg":::

## <a name="development-options"></a>開発オプション

Form Recognizer v2.1 では、次のリソースがサポートされています。

| 機能 | リソース |
|----------|-------------------------|
|**請求書モデル**| <ul><li>[**Form Recognizer ラベル付けツール**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-invoices)</li><li>[**クライアント ライブラリ SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker コンテナー**](containers/form-recognizer-container-install-run.md?tabs=invoice#run-the-container-with-the-docker-compose-up-command)</li></ul>|

Form Recognizer v3.0 では、次のリソースがサポートされています。

| 機能 | リソース | モデル ID |
|----------|-------------|-----------|
|**請求書モデル** | <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md)</li><li>[**JavaScript SDK**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|**prebuilt-invoice**|

### <a name="try-form-recognizer"></a>Form Recognizer を試す

Form Recognizer Studio またはサンプル ラベル付けツールを使用して、顧客情報、ベンダーの詳細、品目などのデータを請求書から抽出する方法を確認します。 次が必要です。

* Azure サブスクリプション — [無料で作成する](https://azure.microsoft.com/free/cognitive-services/)ことができます

* Azure portal の [Form Recognizer インスタンス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 Free 価格レベル (`F0`) を利用して、サービスを試用できます。 リソースがデプロイされたら、 **[Go to resource (リソースに移動)]** を選択して、API キーとエンドポイントを取得します。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio (プレビュー)

1. Form Recognizer Studio のホーム ページで、 **[請求書]** を選択します。

1. サンプルの請求書を分析するか、 **[+ 追加]** ボタンを選択して独自のサンプルをアップロードできます。

1. **[Analyze]\(分析\)** ボタンを選択します。

    :::image type="content" source="media/studio/invoice-analyze.png" alt-text="スクリーンショット: 請求書の分析メニュー。":::

> [!div class="nextstepaction"]
> [Form Recognizer スタジオを試す](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice)

#### <a name="sample-labeling-tool"></a>サンプル ラベル付けツール

請求書ドキュメントが必要です。 [サンプル請求書ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)を使用できます。

1. サンプル ラベル付けツールのホーム ページで、 **[Use prebuilt model to get data]\(事前構築済みモデルを使用してデータを取得する\)** を選択します。

1. **[フォームの種類]** ドロップダウン メニューから **[請求書]** を選択します。

    :::image type="content" source="media/try-invoice.png" alt-text="スクリーンショット: サンプル ラベル付けツールのドロップダウン、事前構築済みモデルの選択メニュー。":::

    > [!div class="nextstepaction"]
    > [サンプル ラベル付けツールを試す](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

## <a name="input-requirements"></a>入力の要件

* 最適な結果を得るには、ドキュメントごとに 1 つの鮮明な写真または高品質のスキャンを提供してください。
* サポートされているファイル形式: JPEG、PNG、BMP、TIFF、および PDF (テキスト埋め込みまたはスキャン済み)。 文字の抽出と位置に関するエラーが発生する可能性を排除するには、テキストが埋め込まれている PDF が最適です。
* PDF および TIFF の場合、最大 2000 ページを処理できます (Free レベルのサブスクリプションでは、最初の 2 ページのみが処理されます)。
* ファイル サイズは 50 MB 未満である必要があります。
* 画像の寸法は、50 x 50 ピクセルから 10,000 x 10,000 ピクセルの間である必要があります。
* PDF の寸法は、17 x 17 インチまでで、Legal または A3 サイズ以下の用紙に対応します。
* トレーニング データの合計サイズは、500 ページ以下です。
* PDF がパスワードでロックされている場合は、送信前にロックを解除する必要があります。
* 教師なし学習の場合 (ラベルの付いたデータなし):
  * データには、キーと値を含める必要があります。
  * キーは値の上または左に表示され、下または右に表示することはできません。

> [!NOTE]
> [サンプル ラベル付けツール](https://fott-2-1.azurewebsites.net/)では、BMP ファイル形式はサポートされていません。 これは、Form Recognizer サービスではなく、ツールの制限です。

## <a name="supported-languages-and-locales"></a>サポートされている言語とロケール

| モデル | 言語: ロケール コード | Default |
|--------|:----------------------|:---------|
|請求書| <ul><li>英語 (米国) - en-US</li></ul>| 英語 (米国): en-US|

## <a name="field-extraction"></a>フィールドの抽出

|名前| 型 | 説明 | 標準化された出力 |
|:-----|:----|:----|:---:|
| CustomerName | String | 請求された顧客| |
| CustomerId | String | 顧客の参照 ID | |
| PurchaseOrder | String | 注文書の参照番号 | |
| InvoiceId | String | この特定の請求書の ID (多くの場合、"請求書番号") | |
| InvoiceDate | Date | 請求書の発行日 | yyyy-mm-dd|
| DueDate | Date | この請求書の支払期日 | yyyy-mm-dd|
| VendorName | String | ベンダー名 |  |
| VendorAddress | String |  ベンダーの郵送先住所|  |
| VendorAddressRecipient | String | VendorAddress に関連付けられている名前 |  |
| CustomerAddress | String | 顧客の住所 | |
| CustomerAddressRecipient | String | CustomerAddress に関連付けられている名前 | |
| BillingAddress | String | 顧客の明示的な請求先住所 |  |
| BillingAddressRecipient | String | BillingAddress に関連付けられている名前 | |
| ShippingAddress | String | 顧客の明示的な送付先住所 | |
| ShippingAddressRecipient | String | ShippingAddress に関連付けられている名前 |  |
| SubTotal | 数値 | この請求書で識別された小計フィールド | 整数型 |
| TotalTax | 数値 | この請求書で識別された合計税額フィールド | 整数型 |
| InvoiceTotal | 数値 (米国ドル) | この請求書に関連付けられている合計新規料金 | 整数型 |
| AmountDue |  数値 (米国ドル) | ベンダーに対する合計金額 | 整数型 |
| ServiceAddress | String | 顧客の明示的なサービス住所または物件所在地 | |
| ServiceAddressRecipient | String | ServiceAddress に関連付けられている名前 |  |
| RemittanceAddress | String | 顧客の明示的な送金住所または支払住所 |   |
| RemittanceAddressRecipient | String | RemittanceAddress に関連付けられている名前 |  |
| ServiceStartDate | Date | サービス期間の最初の日 (たとえば、公共料金サービス期間) | yyyy-mm-dd |
| ServiceEndDate | Date | サービス期間の最後の日 (たとえば、公共料金サービス期間) | yyyy-mm-dd|
| PreviousUnpaidBalance | 数値 | 明示的な以前の未払い残高 | 整数型 |

### <a name="line-items"></a>品目

次に示すのは、JSON の出力応答で請求書から抽出される品目です (以下の出力では、こちらの[サンプル請求書](media/sample-invoice.jpg)が使用されています)

|名前| 型 | 説明 | テキスト (品目 #1) | 値 (標準化された出力) |
|:-----|:----|:----|:----| :----|
| アイテム | String | 品目の完全な文字列テキスト行 | 3/4/2021 A123 Consulting Services 2 hours $30.00 10% $60.00 | |
| Amount | 数値 | 品目の金額 | $60.00 | 100 |
| 説明 | String | 請求書の明細項目の説明テキスト | コンサルティング サービス | コンサルティング サービス |
| 数量 | Number | この請求書明細項目の数量 | 2 | 2 |
| UnitPrice | 数値 | この項目の 1 つの単位の正味価格または総価格 (請求書の総請求書の設定によって異なります) | $30.00 | 30 |
| ProductCode | String| 特定の品目に関連付けられている製品コード、製品番号、または SKU | A123 | |
| ユニット | String| 品目の単位 (kg、lb など) | 時間 | |
| Date | Date| 各品目に対応する日付。 多くの場合、これは品目が発送された日付です | 3/4/2021| 2021-03-04 |
| 税 | 数値 | 各品目に関連付けられている税。 使用可能な値には、課税額、税金 (%)、税金 (Y/N) などがあります | 10% | |

抽出された請求書のキーと値のペアと品目は、JSON 出力の `documentResults` セクションにあります。 

## <a name="form-recognizer-preview-v30"></a>Form Recognizer プレビュー v3.0

 Form Recognizer プレビューでは、いくつかの新機能が導入されています。

* アプリケーションとワークフローでプレビュー バージョンを使用する方法については、[**Form Recognizer v3.0 移行ガイド**](v3-migration-guide.md)の説明を参照してください。

* プレビュー バージョンと新機能の詳細については、[**REST API (プレビュー)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

* Form Recognizer のクイックスタートを完了する:

  > [!div class="nextstepaction"]
  > [Form Recognizer のクイックスタート](quickstarts/try-sdk-rest-api.md)

* REST API を調べる:

    > [!div class="nextstepaction"]
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)
