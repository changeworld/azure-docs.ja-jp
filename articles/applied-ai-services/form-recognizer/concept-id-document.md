---
title: Form Recognizer の身分証明書モデル
titleSuffix: Azure Applied AI Services
description: 事前に構築済みの身分証明書モデルを使用したデータ抽出と分析を含む概念
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: d598c3af52f8b62b23b49f9d661a79a2979574a0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027306"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-id-document-model"></a>Form Recognizer の身分証明書モデル

身分証明書モデルでは、強力な光学式文字認識 (OCR) 機能とディープ ラーニング モデルが組み合わされ、米国の運転免許証 (全 50 州とコロンビア特別区) と国際パスポートの個人情報が載せられたページ (査証や他の旅行文書を除く) を分析し、それらから重要な情報を抽出します。 この API は、身分証明書を分析します。つまり、名、姓、住所、生年月日などの重要な情報を抽出し、構造化された JSON データ表現を返します。

***Form Recognizer Studio で処理した米国の運転免許証のサンプル***

:::image type="content" source="media/studio/drivers-license.png" alt-text="ID カードのサンプル" lightbox="media/overview-id.jpg":::

## <a name="development-options"></a>開発オプション

Form Recognizer v2.1 では、次のリソースがサポートされています。

| 機能 | リソース |
|----------|-------------------------|
|**身分証明書モデル**| <ul><li>[**Form Recognizer ラベル付けツール**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-identity-id-documents)</li><li>[**クライアント ライブラリ SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker コンテナー**](containers/form-recognizer-container-install-run.md?tabs=id-document#run-the-container-with-the-docker-compose-up-command)</li></ul>|

Form Recognizer v3.0 では、次のリソースがサポートされています。

| 機能 | リソース | モデル ID |
|----------|-------------|-----------|
|**身分証明書モデル**|<ul><li> [**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md)</li><li>[**JavaScript SDK**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|**prebuilt-idDocument**|

### <a name="try-form-recognizer"></a>Form Recognizer を試す

Form Recognizer Studio またはサンプル ラベル付けツールを使用して、名前、生年月日、コンピューターで読み取り可能なゾーン、有効期限などのデータを身分証明書から抽出する方法について説明します。 次が必要です。

* Azure サブスクリプション — [無料で作成する](https://azure.microsoft.com/free/cognitive-services/)ことができます

* Azure portal の [Form Recognizer インスタンス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 Free 価格レベル (`F0`) を利用して、サービスを試用できます。 リソースがデプロイされたら、 **[Go to resource (リソースに移動)]** を選択して、API キーとエンドポイントを取得します。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio (プレビュー)

> [!NOTE]
> Form Recognizer スタジオは、プレビュー (v3.0) API で使用できます。

1. Form Recognizer Studio のホーム ページで、 **[請求書]** を選択します

1. サンプルの請求書を分析するか、 **[+ 追加]** ボタンを選択して独自のサンプルをアップロードできます。

1. **[分析]** ボタンを選択します。

    :::image type="content" source="media/studio/id-document-analyze.png" alt-text="スクリーンショット: 身分証明書の分析メニュー。":::

    > [!div class="nextstepaction"]
    > [Form Recognizer スタジオを試す](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=idDocument)

#### <a name="sample-labeling-tool"></a>サンプル ラベル付けツール

身分証明書が必要になります。 [サンプルの身分証明書](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png)を使用できます。

1. サンプル ラベル付けツールのホーム ページで、**事前構築済みモデルを使用してデータを取得する** を選択します。

1. **[フォームの種類]** ドロップダウン メニューから **[身分証明書]** を選択します。

    :::image type="content" source="media/try-id-document.png" alt-text="スクリーンショット: サンプル ラベル付けツールのドロップダウン、事前構築済みモデルの選択メニュー。":::

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

## <a name="supported-languages-and-locales-v21"></a>サポートされている言語とロケール (v2.1)

| モデル | 言語: ロケール コード | Default |
|--------|:----------------------|:---------|
|身分証明書| <ul><li>英語 (米国): en-US (運転免許証)</li><li>国際パスポートの個人情報が載せられたページ</br> (査証や他の旅行文書を除く)</li></ul></br>|英語 (米国): en-US|

## <a name="field-extraction"></a>フィールドの抽出

|名前| 型 | 説明 | 標準化された出力|
|:-----|:----|:----|:----|
|  CountryRegion | countryRegion | ISO 3166 標準に準拠した国または地域コード |  |
|  DateOfBirth | Date | DOB | yyyy-mm-dd |
|  DateOfExpiration | Date | 有効期限 DOB | yyyy-mm-dd |
|  DocumentNumber | String | 関連するパスポート番号、運転免許証番号など |  |
|  FirstName | String | 該当する場合は、抽出された名とミドルネームのイニシャル |  |
|  LastName | String | 抽出された姓 |  |
|  Nationality | countryRegion | ISO 3166 標準に準拠した国または地域コード (Passport のみ) |  |
|  Sex | String | "M"、"F"、"X" という値が抽出される可能性があります | |
|  MachineReadableZone | Object | それぞれ 44 文字の 2 行を含む抽出されたパスポート MRZ | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816" |
|  DocumentType | String | ドキュメントの種類 (例: パスポート、運転免許証) | "passport" |
|  Address | String | 抽出された住所 (運転免許証) ||
|  リージョン | String | 抽出された地域、州、都道府県など (運転免許証のみ) |  |

## <a name="form-recognizer-preview-v30"></a>Form Recognizer プレビュー v3.0

 Form Recognizer プレビューでは、いくつかの新機能が導入されています。

* **身分証明書 (v3.0)** モデルでは、米国の運転免許証からの署名、制限、車両分類の抽出がサポートされています。

### <a name="id-document-preview-field-extraction"></a>身分証明書プレビュー フィールドの抽出

|名前| 型 | 説明 | 標準化された出力|
|:-----|:----|:----|:----|
| 🆕 Endorsements | String | ドライバーに付与される追加の運転特権 (二輪車やスクール バスなど)。  | |
| 🆕 Restrictions | String | 停止または取り消されたライセンスに適用される制限付き運転特権。| |
| 🆕 VehicleClassification | String | ドライバーが運転できる車両の種類。 ||
|  CountryRegion | countryRegion | ISO 3166 標準に準拠した国または地域コード |  |
|  DateOfBirth | Date | DOB | yyyy-mm-dd |
|  DateOfExpiration | Date | 有効期限 DOB | yyyy-mm-dd |
|  DocumentNumber | String | 関連するパスポート番号、運転免許証番号など |  |
|  FirstName | String | 該当する場合は、抽出された名とミドルネームのイニシャル |  |
|  LastName | String | 抽出された姓 |  |
|  Nationality | countryRegion | ISO 3166 標準に準拠した国または地域コード (Passport のみ) |  |
|  Sex | String | "M"、"F"、"X" という値が抽出される可能性があります | |
|  MachineReadableZone | Object | それぞれ 44 文字の 2 行を含む抽出されたパスポート MRZ | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816" |
|  DocumentType | String | ドキュメントの種類 (例: パスポート、運転免許証) | "passport" |
|  Address | String | 抽出された住所 (運転免許証) ||
|  リージョン | String | 抽出された地域、州、都道府県など (運転免許証のみ) |  |

### <a name="migration-guide-and-rest-api-v30"></a>移行ガイドと REST API v3.0

* アプリケーションとワークフローでプレビュー バージョンを使用する方法については、[**Form Recognizer v3.0 への移行ガイド**](v3-migration-guide.md)の説明を参照してください。

* プレビュー バージョンと新機能の詳細については、[**REST API (プレビュー)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

* Form Recognizer のクイックスタートを完了する:

  > [!div class="nextstepaction"]
  > [Form Recognizer のクイックスタート](quickstarts/try-sdk-rest-api.md)

* REST API を調べる:

    > [!div class="nextstepaction"]
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707)
