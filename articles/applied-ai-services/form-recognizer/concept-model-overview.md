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
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0ac003f812078f2bb3b27710068b7350468ad8fb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027195"
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

:::image type="content" source="media/studio/general-document.png" alt-text="スクリーンショット: Studio の一般ドキュメントアイコン。":::

* 一般ドキュメントの API では、ほとんどの種類のフォームがサポートされており、ドキュメントが分析されて、検出されたキーの値とテーブルのエントリが関連付けられます。 これは、ドキュメントから一般的なキーと値のペアを抽出するのに最適です。 一般ドキュメント モデルは、[ラベルなしのカスタム モデルのトレーニング](compose-custom-models.md#train-without-labels)の代わりに使用できます。

* 一般ドキュメントは事前トレーニング済みのモデルであり、REST API を使用して直接呼び出すことができます。

* 一般ドキュメントでは、いくつかのエンティティ カテゴリに対する固有表現認識 (NER) がサポートされています。 NER とは、人、場所、イベント、製品や組織などの事前に定義されているさまざまなテキスト形式のエンティティを、クラスまたは種類に分類する機能です。 エンティティの抽出は、抽出された値を検証する必要があるシナリオで役に立ちます。 エンティティは、コンテンツ全体から抽出されます。

***[フォームレコグナイザー Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document)で処理されるサンプルドキュメント***:

:::image type="content" source="media/studio/general-document-analyze.png" alt-text="スクリーンショット: Form Recognizer Studio での一般ドキュメントの分析。":::

> [!div class="nextstepaction"]
> [詳細情報: 一般ドキュメント モデル](concept-general-document.md)

### <a name="layout"></a>Layout

:::image type="content" source="media/studio/layout.png" alt-text="スクリーンショット: Studio レイアウトアイコン。":::

レイアウト API は、フォームとドキュメントからテキスト、テーブル、ヘッダー、選択マーク、および構造情報を分析して抽出します。

***[フォームレコグナイザーサンプルラベルツール](https://fott-2-1.azurewebsites.net/)のレイアウト機能を使用して処理されるサンプルフォーム***:

:::image type="content" source="media/overview-layout.png" alt-text="スクリーンショット: フォームレコグナイザー studio で処理されたサンプルドキュメントを分析する":::

> [!div class="nextstepaction"]
> [詳細情報: レイアウト モデル](concept-layout.md)

### <a name="invoice"></a>請求書

:::image type="content" source="media/studio/invoice.png" alt-text="スクリーンショット: Studio 請求書アイコン。":::

請求書モデルは、販売請求書から主要な情報が分析されて抽出されます。 この API は、さまざまな形式で請求書を分析し、顧客名、請求先住所、期日、および支払金額などの重要な情報を抽出します。

***[フォームレコグナイザーサンプルラベルツール](https://fott-2-1.azurewebsites.net/)を使用して処理されるサンプル請求書*** は次のとおりです：

:::image type="content" source="./media/overview-invoices.jpg" alt-text="サンプル請求書" lightbox="./media/overview-invoices.jpg":::

> [!div class="nextstepaction"]
> [詳細情報: 請求書モデル](concept-invoice.md)

### <a name="receipt"></a>Receipt

:::image type="content" source="media/studio/receipt.png" alt-text="スクリーンショット: Studio の受信確認アイコン。":::

領収書モデルは、販売領収書から主要な情報が分析されて抽出されます。 この API は、印刷された領収書を分析し、マーチャント名、マーチャント電話番号、取引日、税金、取引合計などの重要な情報を抽出します。 

***Form Recognizer サンプル ラベル付けツール [で処理された領収書のサンプル:](https://fott-2-1.azurewebsites.net/)***

:::image type="content" source="./media/overview-receipt.jpg" alt-text="サンプルのレシート" lightbox="./media/overview-receipt.jpg":::

> [!div class="nextstepaction"]
> [詳細情報: 領収書モデル](concept-receipt.md)

### <a name="id-document"></a>身分証明書

:::image type="content" source="media/studio/id-document.png" alt-text="スクリーンショット: Studio id ドキュメントアイコン。":::

身分証明書モデルでは、米国の運転免許証 (50 州すべてとコロンビア特別区) および国際パスポートの個人情報ページ (ビザや他の旅行文書を除く) から主要な情報が分析されて抽出されます。 API は、id ドキュメントを分析し、名、姓、住所、生年月日などの重要な情報を抽出します。

***[フォームレコグナイザーサンプルラベルツール](https://fott-2-1.azurewebsites.net/)で処理された U.S. ドライバーのライセンスの*** サンプル:

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="ID カードのサンプル" lightbox="./media/overview-id.jpg":::

> [!div class="nextstepaction"]
> [詳細情報: 身分証明書モデル](concept-id-document.md)

### <a name="business-card"></a>名刺

:::image type="content" source="media/studio/business-card.png" alt-text="スクリーンショット: Studio のビジネスカードアイコン。":::

名刺モデルでは、名刺の画像から主要な情報が分析されて抽出されます。 API は、印刷された名刺画像を分析し、名、姓、会社名、電子メールアドレス、電話番号などの重要な情報を抽出します。

***[フォームレコグナイザーサンプルラベルツール](https://fott-2-1.azurewebsites.net/)で処理されるサンプルのビジネスカード***:

:::image type="content" source="./media/overview-business-card.jpg" alt-text="サンプルの名刺" lightbox="./media/overview-business-card.jpg":::

> [!div class="nextstepaction"]
> [詳細情報: 名刺モデル](concept-business-card.md)

### <a name="custom"></a>Custom

 :::image type="content" source="media/studio/custom.png" alt-text="スクリーンショット: Studiocustom アイコン。":::

カスタム モデルでは、ビジネスに固有のフォームやドキュメントからデータが分析されて抽出されます。 この API は、特定のコンテンツ内のフォーム フィールドを認識し、キーと値のペアおよびテーブル データを抽出するようにトレーニングされた、機械学習プログラムです。 始めるために必要なのは同じフォームの種類の 5 つの例だけであり、カスタム モデルのトレーニングは、ラベル付けされたデータセットがあってもなくても実行できます。

***[フォームレコグナイザーサンプルラベルツール](https://fott-2-1.azurewebsites.net/)で処理されるサンプルカスタムフォーム***:

:::image type="content" source="media/analyze.png" alt-text="スクリーンショット: Form Recognizer ツールのカスタム フォーム分析ウィンドウ。":::

> [!div class="nextstepaction"]
> [詳細情報: カスタム モデル](concept-custom.md)

## <a name="data-extraction"></a>データの抽出

 | **Model**   | **テキストの抽出** |**キーと値のペア** |**Fields**|**選択マーク**   | **テーブル**   |**エンティティ** |
  | --- | :---: |:---:| :---: | :---: |:---: |:---: |
  |🆕一般的なドキュメント  | ✓  |  ✓ || ✓  | ✓  | ✓  |
  | Layout  | ✓  |   || ✓  | ✓  |   |
  | 請求書  | ✓ | ✓  |✓| ✓  | ✓ ||
  |Receipt  | ✓  |   ✓ |✓|   |  ||
  | 身分証明書 | ✓  |   ✓  |✓|   |   ||
  | 名刺    | ✓  |   ✓ | ✓|  |   ||
  | Custom             |✓  |  ✓ || ✓  | ✓  | ✓  |

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

## <a name="form-recognizer-preview-v30"></a>Form Recognizer プレビュー v3.0

  Form Recognizer v3.0 (プレビュー) では、いくつかの新機能が導入されています。

* [**一般ドキュメント (プレビュー)**](concept-general-document.md) モデルは新しい API であり、事前トレーニング済みのモデルを使用して、テキスト、テーブル、構造、キーと値のペア、名前付きエンティティがフォームとドキュメントから抽出されます。
* [**領収書 (プレビュー)**](concept-receipt.md) モデルでは、単一ページのホテルの領収書の処理がサポートされます。
* [**身分証明書 (プレビュー)**](concept-id-document.md) モデルでは、米国の運転免許証からの署名、制限、車両分類の抽出がサポートされています。
* [**カスタム モデル API (プレビュー)**](concept-custom.md) では、カスタム フォームの署名の検出がサポートされています。

### <a name="version-migration"></a>バージョンの移行

アプリケーションで Form Recognizer v3.0 を使用する方法については、[**Form Recognizer v3.0 移行ガイド**](v3-migration-guide.md)に関する記事を参照してください

## <a name="next-steps"></a>次の手順

* [Form Recognizer サンプル ツール](https://fott-2-1.azurewebsites.net/)を使用して[独自のフォームとドキュメントを処理する方法を学習します](quickstarts/try-sample-label-tool.md)

* [Form Recognizer のクイックスタート](quickstarts/try-sdk-rest-api.md)を行い、選択した開発言語でのフォーム処理アプリの作成を始めます。
