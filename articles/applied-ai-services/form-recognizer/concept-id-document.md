---
title: Form Recognizer の身分証明書モデル
titleSuffix: Azure Applied AI Services
description: 事前に構築済みの身分証明書モデルを使用したデータ抽出と分析を含む概念
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 98364df02238c36f7e15f00eaff6ec124631987c
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178241"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-id-document-model"></a>Form Recognizer の身分証明書モデル

身分証明書モデルでは、強力な光学式文字認識 (OCR) 機能とディープ ラーニング モデルが組み合わされ、米国の運転免許証 (全 50 州とコロンビア特別区) と国際パスポートの個人情報が載せられたページ (査証や他の旅行文書を除く) を分析し、それらから重要な情報を抽出します。 この API は、身分証明書を分析します。つまり、名、姓、住所、生年月日などの重要な情報を抽出し、構造化された JSON データ表現を返します。

***Form Recognizer サンプル ラベル付けツールで処理された [米国の運転免許証のサンプル](https://fott-2-1.azurewebsites.net/):***

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="ID カードのサンプル" lightbox="./media/overview-id.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>Form Recognizer Studio (プレビュー) を試す

* Form Recognizer Studio は、プレビュー (v3.0) API で使用できます。

* Form Recognizer Studio 身分証明書機能を使用して、名前、マシンが読み取り可能なゾーン、有効期限を抽出します。

> [!div class="nextstepaction"]
> [Form Recognizer Studio を試す](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=idDocument)

## <a name="try-it-sample-labeling-tool"></a>試してみる: サンプル ラベル付けツール

サンプル ラベル付けツールを試すことで、身分証明書データがどのように抽出されるかを確認できます。 次が必要です。

* Azure サブスクリプション: [無料で作成できます](https://azure.microsoft.com/free/cognitive-services/)。

* Azure portal の [Form Recognizer インスタンス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 Free 価格レベル (`F0`) を利用して、サービスを試用できます。 リソースがデプロイされたら、 **[リソースに移動]** をクリックして API キーとエンドポイントを取得します。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

* 身分証明書。 [サンプルの身分証明書](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/applied-ai-services/form-recognizer/media/id-license.jpg)を使用できます。

> [!div class="nextstepaction"]
  > [試してみる](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

Form Recognizer UI で以下の手順を実行します。

  1. **[事前構築済みモデルを使用してデータを取得する]** を選択します。
  1. **[フォーム タイプ]** ドロップダウン メニューから **[領収書]** を選択します。

  :::image type="content" source="media/try-id-document.png" alt-text="スクリーンショット: サンプル ラベル付けツールのドロップダウン、事前構築済みモデルの選択メニュー。":::

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
  * キーは値の上または左に配置されている必要があり、下または右に配置されていてはいけません。

> [!NOTE]
> [サンプル ラベル付けツール](https://fott-2-1.azurewebsites.net/)では、BMP ファイル形式はサポートされていません。 これは、Form Recognizer サービスではなく、ツールの制限です。

## <a name="supported-languages-and-locales-v21"></a>サポートされている言語とロケール (v2.1)

| モデル | 言語: ロケール コード | Default |
|--------|:----------------------|:---------|
|身分証明書| <ul><li>英語 (米国): en-US (運転免許証)</li><li>国際パスポートの個人情報が載せられたページ</br> (査証や他の旅行文書を除く)</li></ul></br>|英語 (米国): en-US|

## <a name="key-value-pair-extraction"></a>キーと値のペアの抽出

|名前| Type | 説明 | 標準化された出力|
|:-----|:----|:----|:----|
|  CountryRegion | countryRegion | ISO 3166 標準に準拠した国または地域コード |  |
|  DateOfBirth | date | DOB | yyyy-mm-dd |
|  DateOfExpiration | date | 有効期限 DOB | yyyy-mm-dd |
|  DocumentNumber | string | 関連するパスポート番号、運転免許証番号など |  |
|  FirstName | string | 該当する場合は、抽出された名とミドルネームのイニシャル |  |
|  LastName | string | 抽出された姓 |  |
|  Nationality | countryRegion | ISO 3166 標準に準拠した国または地域コード (Passport のみ) |  |
|  Sex | string | "M"、"F"、"X" という値が抽出される可能性があります | |
|  MachineReadableZone | object | それぞれ 44 文字の 2 行を含む抽出されたパスポート MRZ | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816" |
|  DocumentType | string | ドキュメントの種類 (例: パスポート、運転免許証) | "passport" |
|  Address | string | 抽出された住所 (運転免許証) ||
|  リージョン | string | 抽出された地域、州、都道府県など (運転免許証のみ) |  |

## <a name="form-recognizer-preview-v30"></a>Form Recognizer プレビュー v3.0

 Form Recognizer プレビューでは、いくつかの新機能が導入されています。

* **身分証明書 (v3.0)** モデルでは、米国の運転免許証からの署名、制限、車両分類の抽出がサポートされています。

    ### <a name="id-document-preview-key-value-pair-extraction"></a>身分証明書プレビューのキーと値のペアの抽出

    |名前| Type | 説明 | 標準化された出力|
    |:-----|:----|:----|:----|
    | 🆕 Endorsements | string | ドライバーに付与される追加の運転特権 (二輪車やスクール バスなど)。  | |
    | 🆕 Restrictions | string | 停止または取り消されたライセンスに適用される制限付き運転特権。| |
    | 🆕 VehicleClassification | string | ドライバーが運転できる車両の種類。 ||
    |  CountryRegion | countryRegion | ISO 3166 標準に準拠した国または地域コード |  |
    |  DateOfBirth | date | DOB | yyyy-mm-dd |
    |  DateOfExpiration | date | 有効期限 DOB | yyyy-mm-dd |
    |  DocumentNumber | string | 関連するパスポート番号、運転免許証番号など |  |
    |  FirstName | string | 該当する場合は、抽出された名とミドルネームのイニシャル |  |
    |  LastName | string | 抽出された姓 |  |
    |  Nationality | countryRegion | ISO 3166 標準に準拠した国または地域コード (Passport のみ) |  |
    |  Sex | string | "M"、"F"、"X" という値が抽出される可能性があります | |
    |  MachineReadableZone | object | それぞれ 44 文字の 2 行を含む抽出されたパスポート MRZ | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816" |
    |  DocumentType | string | ドキュメントの種類 (例: パスポート、運転免許証) | "passport" |
    |  Address | string | 抽出された住所 (運転免許証) ||
    |  リージョン | string | 抽出された地域、州、都道府県など (運転免許証のみ) |  |

* アプリケーションとワークフローでプレビュー バージョンを使用する方法については、[**Form Recognizer v3.0 移行ガイド**](v3-migration-guide.md)に関する記事を参照してください。

* プレビュー バージョンと新機能の詳細については、[**REST API (プレビュー)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

* Form Recognizer のクイックスタートを完了する:

  > [!div class="nextstepaction"]
  > [Form Recognizer のクイックスタート](quickstarts/try-sdk-rest-api.md)

* REST API を調べる:

    > [!div class="nextstepaction"]
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707)
