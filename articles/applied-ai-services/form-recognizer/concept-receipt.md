---
title: Form Recognizer の領収書モデル
titleSuffix: Azure Applied AI Services
description: 事前に構築済みの領収書モデルを使用したデータ抽出と分析を含む概念
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/05/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 48ebbb4975e57db34fe080db2a61328cef65ca80
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716341"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-receipt-model"></a>Form Recognizer の領収書モデル

領収書モデルでは、強力な光学式文字認識 (OCR) 機能と、ディープ ラーニング モデルを組み合わせて、領収書を分析し、領収書から重要な情報を抽出します。 領収書には、印刷されたレシートや手書きの領収書など、さまざまな形式や品質のものが存在します。 API によって、業者名、業者の電話番号、取引日、税金、取引合計などの主要な情報が抽出されて、構造化された JSON データ表現が返されます。

##### <a name="sample-receipt-processed-with-form-recognizer-sample-labeling-tool"></a>[Form Recognizer サンプル ラベル付けツール](https://fott-2-1.azurewebsites.net/)で処理された領収書のサンプル:

:::image type="content" source="./media/overview-receipt.jpg" alt-text="サンプルのレシート" lightbox="./media/overview-receipt.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>Form Recognizer Studio (プレビュー) を試す

* Form Recognizer Studio は、プレビュー (v3.0) API で使用できます。

* Form Recognizer Studio 領収書機能を使用して、トランザクションの日時、マーチャント情報、金額の合計を抽出します。

> [!div class="nextstepaction"]
> [Form Recognizer Studio を試す](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt)

## <a name="try-it-sample-labeling-tool"></a>試してみる: サンプル ラベル付けツール

サンプル ラベル付けツールを試すことで、領収書データがどのように抽出されるかを確認できます。 次が必要です。

* Azure サブスクリプション: [無料で作成できます](https://azure.microsoft.com/free/cognitive-services/)。

* Azure portal の [Form Recognizer インスタンス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 Free 価格レベル (`F0`) を利用して、サービスを試用できます。 リソースがデプロイされたら、 **[リソースに移動]** をクリックして API キーとエンドポイントを取得します。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

* 領収書。 [サンプルの領収書](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-receipt.png)を使用できます。

> [!div class="nextstepaction"]
  > [試してみる](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

Form Recognizer UI で以下の手順を実行します。

  1. **[事前構築済みモデルを使用してデータを取得する]** を選択します。
  1. **[フォーム タイプ]** ドロップダウン メニューから **[領収書]** を選択します。

  :::image type="content" source="media/try-receipt.png" alt-text="スクリーンショット: サンプル ラベル付けツールのドロップダウン、事前構築済みモデルの選択メニュー。":::

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

## <a name="supported-languages-and-locales-v21"></a>サポートされている言語とロケール (v2.1)

>[!NOTE]
 > ロケールを指定する必要はありません。 これは省略可能なパラメーターです。 Form Recognizer のディープラーニング技術によって、画像内のテキストの言語が自動的に検出されます。

| モデル | 言語: ロケール コード | Default |
|--------|:----------------------|:---------|
|Receipt| <ul><li>英語 (米国): en-US</li><li> 英語 (オーストラリア): en-AU</li><li>英語 (カナダ): en-CA</li><li>英語 (イギリス): en-GB</li><li>英語 (インド): en-IN</li></ul>  | 自動検出 |

## <a name="key-value-pair-extraction"></a>キーと値のペアの抽出

|名前| 型 | 説明 | 標準化された出力 |
|:-----|:----|:----|:----|
| ReceiptType | string | 販売レシートの種類 |  Itemized |
| MerchantName | string | レシートを発行しているマーチャントの名前 |  |
| MerchantPhoneNumber | phoneNumber | マーチャントの電話番号の一覧 | +1 xxx xxx xxxx |
| MerchantAddress | string | マーチャントの住所の一覧 |   |
| TransactionDate | date | レシートが発行された日付 | yyyy-mm-dd |
| TransactionTime | time | レシートが発行された時刻 | hh-mm-ss (24 時間)  |
| 合計 | 数値 (米国ドル)| レシートの取引合計額 | 小数点以下 2 桁の浮動小数点数|
| 小計 | 数値 (米国ドル) | レシートの小計 (多くの場合、税金が適用される前) | 小数点以下 2 桁の浮動小数点数|
| 税 | 数値 (米国ドル) | 領収書の税金 (多くの場合、消費税またはそれに相当する税金) | 小数点以下 2 桁の浮動小数点数 |
| ヒント | 数値 (米国ドル) | 購入者によって追加されたチップ | 小数点以下 2 桁の浮動小数点数|
| 項目 | オブジェクトの配列 | 抽出された品目 (名前、数量、単価、および合計価格) | |
| 名前 | string | 項目名 | |
| 数量 | 数値 | 各品目の数量 | 整数 (integer) |
| 価格 | 数値 | 各品目単位の個別価格| 小数点以下 2 桁の浮動小数点数 |
| 合計価格 | 数値 | 品目の合計価格 | 小数点以下 2 桁の浮動小数点数 |

## <a name="form-recognizer-preview-v30"></a>Form Recognizer プレビュー v3.0

 Form Recognizer プレビューでは、いくつかの新機能が導入されています。

* **領収書** モデルでは、単一ページのホテルの領収書の処理がサポートされます。

    ### <a name="hotel-receipt-key-value-pair-extraction"></a>ホテルの確認キーと値のペアの抽出

    |名前| 型 | 説明 | 標準化された出力 |
    |:-----|:----|:----|:----|
    | ArrivalDate | date | 到着日 | yyyy-mm-dd |
    | Currency | currency | 受取金額の通貨単位。 たとえば、複数の値が見つかった場合は、USD、EUR、MIXED のようになります。 ||
    | DepartureDate | date | 出発日 | yyyy-mm-dd |
    | 項目 | array | | |
    | Items.*.Category | string | 項目カテゴリ (部屋、税金など) |  |
    | Items.*.Date | date | 項目の日付 | yyyy-mm-dd |
    | Items.*.Description | string | 項目の説明 | |
    | Items.*.TotalPrice |  number | 項目の合計価格 | 整数 (integer) |
    | Locale | string | 領収書のロケール (en-us など)。 | ISO 言語-市区郡コード   |
    | MerchantAddress | string | マーチャントの住所の一覧 | |
    | MerchantAliases | array| | |
    | MerchantAliases.* | string | マーチャントの代替名 |  |
    | MerchantName | string | レシートを発行しているマーチャントの名前 | |
    | MerchantPhoneNumber | phoneNumber | マーチャントの電話番号の一覧 | +1 xxx xxx xxxx|
    | ReceiptType | string | 領収書の種類 (例: ホテル、明細書) | |
    | 合計 | 数値 | レシートの取引合計額 | 小数点以下 2 桁の浮動小数点数 |

    ### <a name="hotel-receipt-supported-languages-and-locales"></a>ホテルの領収書がサポートされている言語とロケール

    | モデル | 言語: ロケール コード | Default |
    |--------|:----------------------|:---------|
    |領収書 (ホテル) | <ul><li>英語 (米国): en-US</li></ul>| 英語 (米国): en-US|

* アプリケーションとワークフローでプレビュー バージョンを使用する方法については、[**Form Recognizer v3.0 移行ガイド**](v3-migration-guide.md)に関する記事を参照してください。

* プレビュー バージョンと新機能の詳細については、[**REST API (プレビュー)** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

* Form Recognizer のクイックスタートを完了する:

  > [!div class="nextstepaction"]
  > [Form Recognizer のクイックスタート](quickstarts/try-sdk-rest-api.md)

* REST API を調べる:

    > [!div class="nextstepaction"]
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)
