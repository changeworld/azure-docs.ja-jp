---
title: Form Recognizer の領収書モデル
titleSuffix: Azure Applied AI Services
description: 事前に構築済みの領収書モデルを使用したデータ抽出と分析を含む概念
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 111dcc2ab07c83e164e054395b0804f46c07d748
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027074"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-receipt-model"></a>Form Recognizer の領収書モデル

領収書モデルでは、強力な光学式文字認識 (OCR) 機能と、ディープ ラーニング モデルを組み合わせて、領収書を分析し、領収書から重要な情報を抽出します。 領収書には、印刷されたレシートや手書きの領収書など、さまざまな形式や品質のものが存在します。 API によって、業者名、業者の電話番号、取引日、税金、取引合計などの主要な情報が抽出されて、構造化された JSON データ表現が返されます。

***Form Recognizer サンプル ラベル付けツール [で処理された領収書のサンプル:](https://fott-2-1.azurewebsites.net/)***

:::image type="content" source="media/studio/overview-receipt.png" alt-text="サンプルのレシート" lightbox="media/overview-receipt.jpg":::

## <a name="development-options"></a>開発オプション

Form Recognizer v2.1 では、次のリソースがサポートされています。

| 機能 | リソース |
|----------|-------------------------|
|**レシート モデル**| <ul><li>[**Form Recognizer ラベル付けツール**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-receipts)</li><li>[**クライアント ライブラリ SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker コンテナー**](containers/form-recognizer-container-install-run.md?tabs=receipt#run-the-container-with-the-docker-compose-up-command)</li></ul>|

Form Recognizer v3.0 では、次のリソースがサポートされています。

| 機能 | リソース | モデル ID |
|----------|-------------|-----------|
|**レシート モデル**| <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|**prebuilt-receipt**|

### <a name="try-form-recognizer"></a>Form Recognizer を試す

Form Recognizer Studio またはサンプル ラベル付けツールを使用して、データ (トランザクションの日時、マーチャント情報、合計金額など) を領収書から抽出する方法について確認します。 次が必要です。

* Azure サブスクリプション: [無料で作成できます](https://azure.microsoft.com/free/cognitive-services/)。

* Azure portal の [Form Recognizer インスタンス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 Free 価格レベル (`F0`) を利用して、サービスを試用できます。 リソースがデプロイされたら、 **[Go to resource (リソースに移動)]** を選択して、API キーとエンドポイントを取得します。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio (プレビュー)

> [!NOTE]
> Form Recognizer スタジオは、プレビュー (v3.0) API で使用できます。

1. Form Recognizer Studio のホーム ページで、 **[領収書]** を選択します

1. サンプルの領収書を分析するか、 **[+ 追加]** ボタンを選択して独自のサンプルをアップロードできます。

1. **[分析]** ボタンを選択します。

    :::image type="content" source="media/studio/receipt-analyze.png" alt-text="スクリーンショット: 領収書の分析メニュー。":::

    > [!div class="nextstepaction"]
    > [Form Recognizer スタジオを試す](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt)

#### <a name="sample-labeling-tool"></a>サンプル ラベル付けツール

領収書のドキュメントが必要になります。 [サンプルの領収書](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-receipt.png)を使用できます。

1. サンプル ラベル付けツールのホーム ページで、 **[Use prebuilt model to get data]\(事前構築済みモデルを使用してデータを取得する\)** を選択します。

1. **[フォーム タイプ]** ドロップダウン メニューから **[領収書]** を選択します。

      :::image type="content" source="media/try-receipt.png" alt-text="スクリーンショット: サンプル ラベル付けツールのドロップダウン、事前構築済みモデルの選択メニュー。":::

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
  * キーは値の上または左に表示される必要があり、下または右に表示することはできません。

## <a name="supported-languages-and-locales-v21"></a>サポートされている言語とロケール (v2.1)

>[!NOTE]
 > ロケールを指定する必要はありません。 これは省略可能なパラメーターです。 Form Recognizer のディープラーニング技術によって、画像内のテキストの言語が自動的に検出されます。

| モデル | 言語: ロケール コード | Default |
|--------|:----------------------|:---------|
|Receipt| <ul><li>英語 (米国): en-US</li><li> 英語 (オーストラリア): en-AU</li><li>英語 (カナダ): en-CA</li><li>英語 (イギリス): en-GB</li><li>英語 (インド): en-IN</li></ul>  | 自動検出 |

## <a name="field-extraction"></a>フィールドの抽出

|名前| 型 | 説明 | 標準化された出力 |
|:-----|:----|:----|:----|
| ReceiptType | String | 販売レシートの種類 |  Itemized |
| MerchantName | String | レシートを発行しているマーチャントの名前 |  |
| MerchantPhoneNumber | phoneNumber | マーチャントの電話番号の一覧 | +1 xxx xxx xxxx |
| MerchantAddress | String | マーチャントの住所の一覧 |   |
| TransactionDate | Date | レシートが発行された日付 | yyyy-mm-dd |
| TransactionTime | Time | レシートが発行された時刻 | hh-mm-ss (24 時間)  |
| 合計 | 数値 (米国ドル)| レシートの取引合計額 | 小数点以下 2 桁の浮動小数点数|
| 小計 | 数値 (米国ドル) | レシートの小計 (多くの場合、税金が適用される前) | 小数点以下 2 桁の浮動小数点数|
| 税 | 数値 (米国ドル) | 領収書の税金 (多くの場合、消費税またはそれに相当する税金) | 小数点以下 2 桁の浮動小数点数 |
| ヒント | 数値 (米国ドル) | 購入者によって追加されたチップ | 小数点以下 2 桁の浮動小数点数|
| 項目 | オブジェクトの配列 | 抽出された品目 (名前、数量、単価、および合計価格) | |
| 名前 | String | 項目名 | |
| 数量 | Number | 各品目の数量 | 整数型 |
| 価格 | 数値 | 各品目単位の個別価格| 小数点以下 2 桁の浮動小数点数 |
| 合計価格 | 数値 | 品目の合計価格 | 小数点以下 2 桁の浮動小数点数 |

## <a name="form-recognizer-preview-v30"></a>Form Recognizer プレビュー v3.0

 Form Recognizer プレビューでは、いくつかの新機能が導入されています。 **領収書** モデルでは、単一ページのホテルの領収書の処理がサポートされます。

### <a name="hotel-receipt-field-extraction"></a>ホテルの領収書フィールドの抽出

|名前| 型 | 説明 | 標準化された出力 |
|:-----|:----|:----|:----|
| ArrivalDate | Date | 到着日 | yyyy-mm-dd |
| Currency | 通貨 | 受取金額の通貨単位。 たとえば、複数の値が見つかった場合は、USD、EUR、MIXED のようになります。 ||
| DepartureDate | Date | 出発日 | yyyy-mm-dd |
| 項目 | Array | | |
| Items.*.Category | String | 項目カテゴリ (例: 部屋、税金など) |  |
| Items.*.Date | Date | 項目の日付 | yyyy-mm-dd |
| Items.*.Description | String | 項目の説明 | |
| Items.*.TotalPrice |  数値 | 項目の合計価格 | 整数型 |
| Locale | String | 領収書のロケール (en-us など)。 | ISO 言語-市区郡コード   |
| MerchantAddress | String | マーチャントの住所の一覧 | |
| MerchantAliases | Array| | |
| MerchantAliases.* | String | マーチャントの代替名 |  |
| MerchantName | String | レシートを発行しているマーチャントの名前 | |
| MerchantPhoneNumber | phoneNumber | マーチャントの電話番号の一覧 | +1 xxx xxx xxxx|
| ReceiptType | String | 領収書の種類 (例: ホテル、明細書) | |
| 合計 | 数値 | レシートの取引合計額 | 小数点以下 2 桁の浮動小数点数 |

### <a name="hotel-receipt-supported-languages-and-locales"></a>ホテルの領収書がサポートされている言語とロケール

| モデル | 言語: ロケール コード | Default |
|--------|:----------------------|:---------|
|領収書 (ホテル) | <ul><li>英語 (米国): en-US</li></ul>| 英語 (米国) - en-US|

### <a name="migration-guide-and-rest-api-v30"></a>移行ガイドと REST API v3.0

* アプリケーションとワークフローでプレビュー バージョンを使用する方法については、[**Form Recognizer v3.0 への移行ガイド**](v3-migration-guide.md)の説明を参照してください。

* プレビュー バージョンと新機能の詳細については、[**REST API (プレビュー)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

* Form Recognizer のクイックスタートを完了する:

  > [!div class="nextstepaction"]
  > [Form Recognizer のクイックスタート](quickstarts/try-sdk-rest-api.md)

* REST API を調べる:

    > [!div class="nextstepaction"]
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)
