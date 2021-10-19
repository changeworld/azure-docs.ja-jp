---
title: Form Recognizer の名刺モデル
titleSuffix: Azure Applied AI Services
description: 事前に構築された名刺モデルを使用するデータ抽出と分析を含む概念
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/05/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: b9517dcab3f748283c7dace99c0e49796dae7f7f
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716276"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-business-card-model"></a>Form Recognizer の名刺モデル

## <a name="overview"></a>概要

名刺モデルでは、強力な光学式文字認識 (OCR) 機能と、ディープ ラーニング モデルの組み合わせにより、名刺の画像が分析されて、重要な情報が抽出されます。 API により、印刷された名刺が分析され、名、姓、会社名、メール アドレス、電話番号などの主要な情報が抽出されて、構造化された JSON データ表現が返されます。

***[Form Recognizer サンプル ラベル付けツール](https://fott-2-1.azurewebsites.net/)で処理された名刺のサンプル:***

:::image type="content" source="./media/overview-business-card.jpg" alt-text="サンプルの名刺" lightbox="./media/overview-business-card.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>Form Recognizer Studio (プレビュー) を試す

* Form Recognizer Studio は、プレビュー (v3.0) API で使用できます。

* Form Recognizer Studio の名刺機能を使用して、名前、役職、住所、メール、会社名などを抽出します。

> [!div class="nextstepaction"]
> [Form Recognizer Studio を試す](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=businessCard)

## <a name="try-it-sample-labeling-tool"></a>試してみる: サンプル ラベル付けツール

サンプル ラベル付けツールを試すことで、名刺のデータがどのように抽出されるかを確認できます。 次が必要です。

* Azure サブスクリプション - [無料で作成](https://azure.microsoft.com/free/cognitive-services/)できます

* Azure portal の [Form Recognizer インスタンス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 Free 価格レベル (`F0`) を利用して、サービスを試用できます。 リソースがデプロイされたら、 **[リソースに移動]** をクリックして API キーとエンドポイントを取得します。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

* 名刺ドキュメント。 [サンプルの名刺ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/businessCard.png)を使用できます。

> [!div class="nextstepaction"]
  > [試してみる](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

  Form Recognizer UI で以下の手順を実行します。

  1. **[Use prebuilt model to get data]\(事前構築済みモデルを使用してデータを取得する\)** を選択します。
  1. **[フォームの種類]** ドロップダウン メニューから **[名刺]** を選択します。

  :::image type="content" source="media/try-business-card.png" alt-text="スクリーンショット: サンプル ラベル付けツールのドロップダウン、事前構築済みモデルの選択メニュー。":::

## <a name="input-requirements"></a>入力の要件

* 最適な結果を得るには、ドキュメントごとに 1 つの鮮明な写真または高品質のスキャンを用意してください。
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

## <a name="supported-languages-and-locales"></a>サポートされている言語とロケール

>[!NOTE]
 > ロケールを指定する必要はありません。 これは省略可能なパラメーターです。 Form Recognizer のディープラーニング技術によって、画像内のテキストの言語が自動的に検出されます。

| モデル | 言語 - ロケール コード | Default |
|--------|:----------------------|:---------|
|名刺| <ul><li>英語 (米国) - en-US</li><li> 英語 (オーストラリア): en-AU</li><li>英語 (カナダ): en-CA</li><li>英語 (イギリス): en-GB</li><li>英語 (インド): en-IN</li></ul>  | 自動検出 |

## <a name="key-value-pair-extraction"></a>キーと値のペアの抽出

|名前| 型 | 説明 |標準化された出力 |
|:-----|:----|:----|:----:|
| ContactNames | オブジェクトの配列 | 連絡先の名前 |  |
| FirstName | string | 連絡先の名 |  |
| LastName | string | 連絡先の姓 |  |
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

* アプリケーションとワークフローでプレビュー バージョンを使用する方法については、[**Form Recognizer v3.0 移行ガイド**](v3-migration-guide.md)に関する記事を参照してください。

* プレビュー バージョンと新機能の詳細については、[**REST API (プレビュー)** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

* Form Recognizer のクイックスタートを完了する:

  > [!div class="nextstepaction"]
  > [Form Recognizer のクイックスタート](quickstarts/try-sdk-rest-api.md)

* REST API を調べる:

    > [!div class="nextstepaction"]
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync)
