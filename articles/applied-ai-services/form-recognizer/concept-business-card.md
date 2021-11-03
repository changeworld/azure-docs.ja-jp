---
title: Form Recognizer の名刺モデル
titleSuffix: Azure Applied AI Services
description: 事前に構築された名刺モデルを使用するデータ抽出と分析を含む概念
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 30b83d0edea0c3b54b0d15300e043c7f86fa6573
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027743"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-business-card-model"></a>Form Recognizer の名刺モデル

名刺モデルでは、強力な光学式文字認識 (OCR) 機能と、ディープ ラーニング モデルの組み合わせにより、名刺の画像が分析されて、重要な情報が抽出されます。 API により、印刷された名刺が分析され、名、姓、会社名、メール アドレス、電話番号などの主要な情報が抽出されて、構造化された JSON データ表現が返されます。

***Form Recognizer Studio で処理した名刺のサンプル***

:::image type="content" source="./media/studio/overview-business-card-studio.png" alt-text="サンプルの名刺" lightbox="./media/overview-business-card.jpg":::

## <a name="development-options"></a>開発オプション

Form Recognizer v2.1 では、次のリソースがサポートされています。

| 機能 | リソース |
|----------|-------------------------|
|**名刺モデル**|  <ul><li>[**Form Recognizer ラベル付けツール**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-business-cards)</li><li>[**クライアント ライブラリ SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker コンテナー**](containers/form-recognizer-container-install-run.md?tabs=business-card#run-the-container-with-the-docker-compose-up-command)</li></ul>|

Form Recognizer v3.0 では、次のリソースがサポートされています。

| 機能 | リソース | モデル ID |
|----------|-------------|-----------|
|**名刺モデル**| <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md)</li><li>[**JavaScript SDK**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|**事前構築された名刺**|

### <a name="try-form-recognizer"></a>Form Recognizer を試す

Form Recognizer Studio またはサンプル ラベル付けツールを使用して、名刺から名前、役職、住所、電子メール、会社名などのデータを抽出する方法について説明します。 次が必要です。

* Azure サブスクリプション — [無料で作成する](https://azure.microsoft.com/free/cognitive-services/)ことができます

* Azure portal の [Form Recognizer インスタンス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 Free 価格レベル (`F0`) を利用して、サービスを試用できます。 リソースがデプロイされたら、 **[Go to resource (リソースに移動)]** を選択して、API キーとエンドポイントを取得します。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio (プレビュー)

> [!NOTE]
> Form Recognizer スタジオは、プレビュー (v3.0) API で使用できます。

1. Form Recognizer Studio のホーム ページで、**名刺** を選択します

1. サンプルの名刺を分析するか、 **[+ Add (+ 追加)]** ボタンを選択して、独自のサンプルをアップロードできます。

1. **[Analyze (分析)]** ボタンを選択します。

    :::image type="content" source="media/studio/business-card-analyze.png" alt-text="スクリーンショット: 名刺の分析メニュー。":::

    > [!div class="nextstepaction"]
    > [Form Recognizer スタジオを試す](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=businessCard)

#### <a name="sample-labeling-tool"></a>サンプル ラベル付けツール

名刺ドキュメントが必要です。 [サンプルの名刺ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/businessCard.png)を使用できます。

  1. サンプル ラベル付けツールのホーム ページで、 **[Use prebuilt model to get data (事前構築済みモデルを使用してデータを取得する)]** を選択します。

  1. **[フォームの種類]** ドロップダウン メニューから **[名刺]** を選択します。

      :::image type="content" source="media/try-business-card.png" alt-text="スクリーンショット: サンプル ラベル付けツールのドロップダウン、事前構築済みモデルの選択メニュー。":::

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

>[!NOTE]
 > ロケールを指定する必要はありません。 これは省略可能なパラメーターです。 Form Recognizer のディープラーニング技術によって、画像内のテキストの言語が自動的に検出されます。

| モデル | 言語: ロケール コード | Default |
|--------|:----------------------|:---------|
|名刺| <ul><li>英語 (米国) - en-US</li><li> 英語 (オーストラリア): en-AU</li><li>英語 (カナダ): en-CA</li><li>英語 (イギリス): en-GB</li><li>英語 (インド): en-IN</li></ul>  | 自動検出 |

## <a name="field-extraction"></a>フィールドの抽出

|名前| 型 | 説明 |標準化された出力 |
|:-----|:----|:----|:----:|
| ContactNames | オブジェクトの配列 | 連絡先の名前 |  |
| FirstName | String | 連絡先の名 |  |
| LastName | String | 連絡先の姓 |  |
| CompanyNames | 文字列の配列 | 会社名|  |
| Departments | 文字列の配列 | 連絡先の部署または組織 |  |
| JobTitles | 文字列の配列 | リストされている連絡先の役職 |  |
| メール | 文字列の配列 | 連絡先のメール アドレス |  |
| Websites | 文字列の配列 | 会社の Web サイト |  |
| アドレス | 文字列の配列 | 名刺から抽出された住所 | |
| MobilePhones | 電話番号の配列 | 名刺の携帯電話番号 |+1 xxx xxx xxxx |
| Fax | 電話番号の配列 | 名刺の FAX 電話番号 | +1 xxx xxx xxxx |
| WorkPhones | 電話番号の配列 | 名刺の勤務先電話番号 | +1 xxx xxx xxxx |
| OtherPhones     | 電話番号の配列 | 名刺のその他の電話番号 | +1 xxx xxx xxxx |

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
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync)
