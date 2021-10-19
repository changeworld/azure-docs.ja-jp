---
title: Form Recognizer のモデル
titleSuffix: Azure Applied AI Services
description: 事前に構築されたモデルを使用するデータ抽出と分析を含む概念
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: f2e2ef59d4c3608065edab4ffd1d1ec55122f2ad
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754617"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-models"></a>Form Recognizer のモデル

 Azure Form Recognizer の事前に構築されたモデルを使用すると、独自のモデルをトレーニングして構築しなくても、インテリジェントなフォーム処理をアプリやフローに追加できます。 事前に構築されたモデルでは、光学式文字認識 (OCR) とディープ ラーニングモデルを組み合わせて、特定のフォームとドキュメントの種類に共通の定義済みのテキストおよびデータ フィールドが識別されて抽出されます。 Form Recognizer によってフォームとドキュメントのデータが抽出されて分析され、整理されて構造化された JSON 応答が返されます。 Form Recognizer v2.1 では、請求書、領収書、身分証明書、名刺のモデルがサポートされています。

## <a name="model-overview"></a>モデルの概要

| **Model**   | **説明**   |
| --- | --- |
| 🆕[一般ドキュメント (プレビュー)](#general-document-preview) | テキスト、テーブル、構造、キーと値のペア、および名前付きエンティティを抽出します。  |
| [レイアウト](#layout)  | ドキュメントからテキストとレイアウトの情報を抽出します。  |
| [請求書](#invoice)  | 英語の請求書から主要な情報を抽出します。  |
| [Receipt](#receipt)  | 英語の領収書から主要な情報を抽出します。  |
| [身分証明書](#id-document)  | 米国の運転免許証と国際パスポートから主要な情報を抽出します。  |
| [名刺](#business-card)  | 英語の名刺から主要な情報を抽出します。  |
| [カスタム](#custom) |  ビジネスに固有のフォームとドキュメントからデータを抽出します。 カスタム モデルは、特定のデータとユース ケースに合わせてトレーニングされます。 |

### <a name="general-document-preview"></a>一般ドキュメント (プレビュー)

* 一般ドキュメントの API では、ほとんどの種類のフォームがサポートされており、ドキュメントが分析されて、検出されたキーの値とテーブルのエントリが関連付けられます。 これは、ドキュメントから一般的なキーと値のペアを抽出するのに最適です。 一般ドキュメント モデルは、[ラベルなしのカスタム モデルのトレーニング](compose-custom-models.md#train-without-labels)の代わりに使用できます。

* 一般ドキュメントは事前トレーニング済みのモデルであり、REST API を使用して直接呼び出すことができます。

* 一般ドキュメントでは、いくつかのエンティティ カテゴリに対する固有表現認識 (NER) がサポートされています。 NER とは、人、場所、イベント、製品や組織などの事前に定義されているさまざまなテキスト形式のエンティティを、クラスまたは種類に分類する機能です。 エンティティの抽出は、抽出された値を検証する必要があるシナリオで役に立ちます。 エンティティは、コンテンツ全体から抽出されます。

##### <a name="sample-document-processed-in-the-form-recognizer-studio"></a>[Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document) で処理されたサンプル ドキュメント:

:::image type="content" source="media/general-document-analyze.png" alt-text="スクリーンショット: Form Recognizer Studio での一般ドキュメントの分析。":::

> [!div class="nextstepaction"]
> [詳細情報: 一般ドキュメント モデル](concept-general-document.md)

### <a name="layout"></a>Layout

Layout API では、ドキュメントからテキスト、テーブルとヘッダー、選択マーク、構造情報が分析されて抽出され、構造化された JSON データ表現が返されます。

##### <a name="sample-form-processed-with-form-recognizer-sample-labeling-tool--layout-feature"></a>[Form Recognizer サンプル ラベル付けツール](https://fott-2-1.azurewebsites.net/)のレイアウト機能を使用して処理されたサンプル フォーム:

:::image type="content" source="media/overview-layout.png" alt-text="{alt-text}":::

> [!div class="nextstepaction"]
> [詳細情報: レイアウト モデル](concept-layout.md)

### <a name="invoice"></a>請求書

請求書モデルは、販売請求書から主要な情報が分析されて抽出されます。 API によって、さまざまな形式の請求書が分析され、顧客名、請求先住所、期限、支払金額などの主要な情報が抽出されて、構造化された JSON データ表現が返されます。

##### <a name="sample-invoice-processed-with-form-recognizer-sample-labeling-tool"></a>[Form Recognizer サンプル ラベル付けツール](https://fott-2-1.azurewebsites.net/)で処理された請求書のサンプル:

:::image type="content" source="./media/overview-invoices.jpg" alt-text="サンプル請求書" lightbox="./media/overview-invoices.jpg":::

> [!div class="nextstepaction"]
> [詳細情報: 請求書モデル](concept-invoice.md)

### <a name="receipt"></a>Receipt

領収書モデルは、販売領収書から主要な情報が分析されて抽出されます。 API によって、印刷された領収書や手書きの領収書が分析され、業者名、業者の電話番号、取引日、税金、取引合計などの主要な情報が抽出されて、構造化された JSON データ表現が返されます。

##### <a name="sample-receipt-processed-with-form-recognizer-sample-labeling-tool"></a>[Form Recognizer サンプル ラベル付けツール](https://fott-2-1.azurewebsites.net/)で処理された領収書のサンプル:

:::image type="content" source="./media/overview-receipt.jpg" alt-text="サンプルのレシート" lightbox="./media/overview-receipt.jpg":::

> [!div class="nextstepaction"]
> [詳細情報: 領収書モデル](concept-receipt.md)

### <a name="id-document"></a>身分証明書

身分証明書モデルでは、米国の運転免許証 (50 州すべてとコロンビア特別区) および国際パスポートの個人情報ページ (ビザや他の旅行文書を除く) から主要な情報が分析されて抽出されます。 API により、身分証明書が分析され、名、姓、住所、生年月日などの主要な情報が抽出されて、構造化された JSON データ表現が返されます。

##### <a name="sample-us-drivers-license-processed-with-form-recognizer-sample-labeling-tool"></a>[Form Recognizer サンプル ラベル付けツール](https://fott-2-1.azurewebsites.net/)で処理された米国の運転免許証のサンプル:

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="ID カードのサンプル" lightbox="./media/overview-id.jpg":::

> [!div class="nextstepaction"]
> [詳細情報: 身分証明書モデル](concept-id-document.md)

### <a name="business-card"></a>名刺

名刺モデルでは、名刺の画像から主要な情報が分析されて抽出されます。 API により、印刷された名刺が分析され、名、姓、会社名、メール アドレス、電話番号などの主要な情報が抽出されて、構造化された JSON データ表現が返されます。

##### <a name="sample-business-card-processed-with-form-recognizer-sample-labeling-tool"></a>[Form Recognizer サンプル ラベル付けツール](https://fott-2-1.azurewebsites.net/)で処理された名刺のサンプル:

:::image type="content" source="./media/overview-business-card.jpg" alt-text="サンプルの名刺" lightbox="./media/overview-business-card.jpg":::

> [!div class="nextstepaction"]
> [詳細情報: 名刺モデル](concept-business-card.md)

### <a name="custom"></a>Custom

カスタム モデルでは、ビジネスに固有のフォームやドキュメントからデータが分析されて抽出されます。 この API は、特定のコンテンツ内のフォーム フィールドを認識し、キーと値のペアおよびテーブル データを抽出するようにトレーニングされた、機械学習プログラムです。 始めるために必要なのは同じフォームの種類の 5 つの例だけであり、カスタム モデルのトレーニングは、ラベル付けされたデータセットがあってもなくても実行できます。

##### <a name="sample-custom-form-processed-with-form-recognizer-sample-labeling-tool"></a>[Form Recognizer サンプル ラベル付けツール](https://fott-2-1.azurewebsites.net/)で処理されたカスタム フォームのサンプル:

:::image type="content" source="media/analyze.png" alt-text="スクリーンショット: Form Recognizer ツールのカスタム フォーム分析ウィンドウ。":::

> [!div class="nextstepaction"]
> [詳細情報: カスタム モデル](concept-custom.md)

## <a name="data-extraction"></a>データの抽出

 | **Model**   | **テキストの抽出** |**キーと値のペア** |**選択マーク**   | **テーブル**   |**エンティティ** |
  | --- | :---: |:---:| :---: | :---: |:---: |
  |🆕一般的なドキュメント  | ✓  |  ✓ | ✓  | ✓  | ✓  |
  | Layout  | ✓  |   | ✓  | ✓  |   |
  | 請求書  | ✓ | ✓  | ✓  | ✓ ||
  |Receipt  | ✓  |   ✓ |   |  ||
  | 身分証明書 | ✓  |   ✓  |   |   ||
  | 名刺    | ✓  |   ✓ |   |   ||
  | Custom             |✓  |  ✓ | ✓  | ✓  | ✓  |

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

## <a name="form-recognizer-preview-v30"></a>Form Recognizer プレビュー v3.0

  Form Recognizer v3.0 (プレビュー) では、いくつかの新機能が導入されています。

* [**一般ドキュメント (プレビュー)** ](concept-general-document.md) モデルは新しい API であり、事前トレーニング済みのモデルを使用して、テキスト、テーブル、構造、キーと値のペア、名前付きエンティティがフォームとドキュメントから抽出されます。
* [**領収書 (プレビュー)** ](concept-receipt.md) モデルでは、単一ページのホテルの領収書の処理がサポートされます。
* [**身分証明書 (プレビュー)** ](concept-id-document.md) モデルでは、米国の運転免許証からの署名、制限、車両分類の抽出がサポートされています。
* [**カスタム モデル API (プレビュー)** ](concept-custom.md) では、カスタム フォームの署名の検出がサポートされています。

### <a name="version-migration"></a>バージョンの移行

アプリケーションで Form Recognizer v3.0 を使用する方法については、[**Form Recognizer v3.0 移行ガイド**](v3-migration-guide.md)に関する記事を参照してください

## <a name="next-steps"></a>次の手順

* [Form Recognizer サンプル ツール](https://fott-2-1.azurewebsites.net/)を使用して[独自のフォームとドキュメントを処理する方法を学習します](quickstarts/try-sample-label-tool.md)

* [Form Recognizer のクイックスタート](quickstarts/try-sdk-rest-api.md)を行い、選択した開発言語でのフォーム処理アプリの作成を始めます。
