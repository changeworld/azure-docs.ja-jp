---
title: Form Recognizer の一般ドキュメント モデル | プレビュー
titleSuffix: Azure Applied AI Services
description: 事前に構築済みの一般ドキュメント プレビュー モデルを使用したデータ抽出と分析を含む概念
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 63d381f96a9781f2f3ab1abfd45d03c968d6dad8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027525"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-general-document-model-preview"></a>Form Recognizer の一般ドキュメント モデル (プレビュー)

一般ドキュメント プレビュー モデルを使用すると、強力な光学式文字認識 (OCR) 機能と、ディープ ラーニング モデルの組み合わせにより、キーと値のペアとエンティティがドキュメントから抽出されます。 一般ドキュメントは、プレビュー (v3.0) API でのみ使用できます。  プレビュー (v3.0) API の使用の詳細については、[移行ガイド](v3-migration-guide.md)に関する記事を参照してください。

一般ドキュメントの API では、ほとんどの種類のフォームがサポートされており、ドキュメントが分析されて、検出されたキーの値とテーブルのエントリが関連付けられます。 これは、ドキュメントから一般的なキーと値のペアを抽出するのに最適です。 一般ドキュメント モデルは、[ラベルなしのカスタム モデルのトレーニング](compose-custom-models.md#train-without-labels)の代わりに使用できます。

## <a name="general-document-features"></a>一般ドキュメント機能

* キーと値のペアを抽出するために、カスタム モデルをトレーニングする必要はありません。

* 1 つの API を使用して、ドキュメントからキーと値のペア、エンティティ、テキスト、テーブル、構造が抽出されます。

* これは事前トレーニング済みのモデルであり、カバレッジと精度を向上させるため、新しいデータで定期的にトレーニングされます。

* 一般ドキュメント モデルでは、構造化データ、半構造化データ、非構造化データがサポートされています。

***Form Recognizer Studio で処理されたサンプル ドキュメント***

:::image type="content" source="media/studio/general-document-analyze.png" alt-text="スクリーンショット: Form Recognizer Studio での一般ドキュメントの分析。":::

## <a name="development-options"></a>開発オプション

Form Recognizer v3.0 では、次の機能がサポートされています。

| 機能 | リソース |
|----------|-------------------------|
|🆕 **一般的なドキュメント モデル**|<ul ><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md)</li><li>[**JavaScript SDK**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|

### <a name="try-form-recognizer"></a>Form Recognizer を試す

Form Recognizer Studio またはサンプル ラベル付けツールを使用して、テーブル、値、エンティティなどのデータをフォームやドキュメントから抽出する方法について説明します。 次が必要です。

* Azure サブスクリプション — [無料で作成する](https://azure.microsoft.com/free/cognitive-services/)ことができます

* Azure portal の [Form Recognizer インスタンス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 Free 価格レベル (`F0`) を利用して、サービスを試用できます。 リソースがデプロイされたら、 **[Go to resource (リソースに移動)]** を選択して、API キーとエンドポイントを取得します。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio (プレビュー)

> [!NOTE]
> Form Recognizer Studio と一般ドキュメント モデルは、プレビュー (v3.0) API で使用できます。

1. Form Recognizer Studio のホーム ページで、 **[一般ドキュメント]** を選択します

1. サンプル ドキュメントを分析するか、 **[+ 追加]** ボタンを選択して、独自のサンプルをアップロードできます。

1. **[分析]** ボタンを選択します。

    :::image type="content" source="media/studio/general-document-analyze-1.png" alt-text="スクリーンショット: [一般ドキュメントの分析] メニュー。":::

    > [!div class="nextstepaction"]
    > [Form Recognizer スタジオを試す](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document)

## <a name="key-value-pairs"></a>キー/値ペア

キーと値のペアは、ラベルまたはキーとそれに関連する応答または値を識別する、ドキュメント内の特定の範囲です。 構造化フォームでは、これは、たとえば、ラベルと、ユーザーがそのフィールドに入力した値です。非構造化ドキュメントの場合は、たとえば、段落内のテキストに基づいて契約が実行された日付です。  さまざまなドキュメントの種類、形式、構造に基づいて、識別可能なキーと値を抽出するために、AI モデルがトレーニングされています。

モデルによってキーの存在が検出されても、関連する値がない場合や、省略可能なフィールドの処理では、キーが単独で存在する可能性もあります。 たとえば、一部のインスタンスのフォームで、ミドル ネームのフィールドが空白のままになっている場合があります。 キーと値のペアは、常に、ドキュメントに含まれるテキストの範囲です。同じ値が異なる方法で記述されるドキュメントの場合 (たとえば、"顧客" と "ユーザー")、関連付けられるキーは、ドキュメントに含まれているものに基づいて顧客またはユーザーになります。 

## <a name="entities"></a>エンティティ

自然言語処理モデルを使用すると、音声の一部を識別して、各トークンまたは単語を分類できます。 名前付きエンティティ認識モデルを使用すると、人、場所、日付のようなエンティティを識別して、より充実したエクスペリエンスを提供できます。 エンティティを識別すると、個人や組織など、顧客の種類を区別できます。
キー値ペア抽出モデルとエンティティ識別モデルは、抽出されたキーと値のペアの値だけではなく、ドキュメント全体に対して並列に実行されます。 これにより、キーを識別できない複雑な構造でも、参照されているエンティティを識別することでエンリッチされます。 さらに、識別された範囲のオフセットに基づいて、キーまたは値をエンティティに一致させることができます。

* 一般ドキュメントは事前トレーニング済みのモデルであり、REST API を使用して直接呼び出すことができます。 

* 一般ドキュメントでは、いくつかのエンティティ カテゴリに対する固有表現認識 (NER) がサポートされています。 NER とは、人、場所、イベント、製品や組織などの事前に定義されているさまざまなテキスト形式のエンティティを、クラスまたは種類に分類する機能です。 エンティティの抽出は、抽出された値を検証する必要があるシナリオで役に立ちます。 エンティティは、抽出された値だけでなく、コンテンツ全体から抽出されます。

## <a name="general-document-model-data-extraction"></a>一般ドキュメント モデルのデータ抽出

| **Model**   | **テキストの抽出** |**キーと値のペア** |**選択マーク**   | **テーブル**   |**エンティティ** |
| --- | :---: |:---:| :---: | :---: |:---: |
|一般ドキュメント  | ✓  |  ✓ | ✓  | ✓  | ✓  |

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
  * キーは、値の上または左に配置されている必要があり、下または右に配置されていてはいけません。

## <a name="supported-languages-and-locales"></a>サポートされている言語とロケール

| モデル | 言語: ロケール コード | Default |
|--------|:----------------------|:---------|
|一般ドキュメント| <ul><li>英語 (米国) - en-US</li></ul>| 英語 (米国) - en-US|

### <a name="named-entity-recognition-ner-categories"></a>固有表現認識 (NER) のカテゴリ

| カテゴリ | Type | 説明 |
|-----------|-------|--------------------|
| Person | String | 人物の名前の一部または全体。 |
| PersonType | String | 人の職種または役割。  |
| 場所 | String | 自然および人工のランドマーク、構造、地物、地政学的実体。 |
| Organization | String | 企業、政治団体、音楽バンド、スポーツ クラブ、政府機関、および公的機関。 |
| イベント | String | 歴史上の出来事、社会的事件、自然発生的な事象。 |
| Product | String |さまざまなカテゴリの物。 |
| スキル | String | 能力、スキル、または専門知識。 |
| Address | String | 完全な郵送先住所。 |
| 電話番号 | String| 電話番号です。 | 
| Email | String | 電子メール アドレス。 |
| URL | String | Web サイトの URL とリンク。 |
| IP アドレス | String | ネットワーク IP アドレス。 |
| DateTime | String | 日付と時刻。 |
| Quantity | String | 数値的測定値と単位。 |

## <a name="considerations"></a>考慮事項

* エンティティの抽出は、抽出された値を検証する必要があるシナリオで役に立ちます。 エンティティは、抽出された値だけでなく、ドキュメントのコンテンツ全体で抽出されます。

* キーはドキュメントから抽出されたテキストの範囲であり、半構造化ドキュメントの場合、キーの既存のディクショナリにキーをマップすることが必要な場合があります。

* キーだけがあって値のないキーと値のペアがあることを想定してください。 たとえば、ユーザーがフォームでメール アドレスを提供しないことを選択したような場合です。

## <a name="next-steps"></a>次の手順

* アプリケーションとワークフローでプレビュー バージョンを使用する方法については、[**Form Recognizer v3.0 移行ガイド**](v3-migration-guide.md)に関する記事を参照してください。

* プレビュー バージョンと新機能の詳細については、[**REST API (プレビュー)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) に関する記事を参照してください。

> [!div class="nextstepaction"]
> [Form Recognizer Studio を試す](https://formrecognizer.appliedai.azure.com/studio)
