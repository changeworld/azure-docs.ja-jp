---
title: Form Recognizer のカスタム モデルと作成済みモデル
titleSuffix: Azure Applied AI Services
description: Form Recognizer のカスタム モデルと作成済みモデルを作成、使用、管理する方法について説明します。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 825bd073759d27f789aac454eb2b384bbf065c4b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027658"
---
# <a name="form-recognizer-custom-and-composed-models"></a>Form Recognizer のカスタム モデルと作成済みモデル

Form Recognizer では、高度な機械学習テクノロジを使用して、ドキュメント イメージから情報を検出および抽出し、抽出したデータを構造化 JSON 出力で返します。 Form Recognizer を使用すると、スタンドアロンのカスタム モデルのトレーニングを行ったり、カスタム モデルを組み合わせて作成済みモデルを作成することができます。

* **カスタム モデル**。 Form Recognizer のカスタム モデルを使用すると、ビジネスに固有のフォームやドキュメントに含まれるデータの分析と抽出ができます。 カスタム モデルは、特定のデータとユース ケースに合わせてトレーニングされます。

* **作成済みモデル**。 作成済みモデルは、カスタム モデルのコレクションを取得し、目的のフォームの種類を含む 1 つのモデルに割り当てることによって作成されます。 作成済みモデルにドキュメントが送信されると、サービスによって分類ステップが実行され、分析のために提示されたフォームを正確に表すカスタム モデルが決定されます。

:::image type="content" source="media/studio/analyze-custom.png" alt-text="スクリーンショット: Form Recognizer ツールのカスタム フォーム分析ウィンドウ。":::

## <a name="what-is-a-custom-model"></a>カスタム モデルとは

カスタム モデルとは、特定のコンテンツ内のフォーム フィールドを認識し、キーと値のペア、およびテーブル データを抽出するためにトレーニングされる機械学習プログラムのことです。 始めるために必要なのは同じフォームの種類の 5 つの例だけであり、カスタム モデルのトレーニングは、ラベル付けされたデータセットがあってもなくても実行できます。

## <a name="what-is-a-composed-model"></a>作成済みモデルとは

作成済みモデルを使用すると、1 つのモデル ID で呼び出される作成済みモデルに複数のカスタム モデルを割り当てることができます。 これは、複数のモデルをトレーニングし、類似したフォームの種類を分析するためにそれらをグループ化する場合に役立ちます。 たとえば、作成済みモデルには、サプライ、備品、家具の発注書を分析するようにトレーニングされたカスタム モデルを含めることができます。 適切なモデルを手動で選択する代わりに、作成済みモデルを使用すれば、分析と抽出ごとに適切なカスタム モデルを決定できます。

## <a name="development-options"></a>開発オプション

Form Recognizer v2.1 では、次のリソースがサポートされています。

| 機能 | リソース |
|----------|-------------------------|
|**カスタム モデル**| <ul><li>[**Form Recognizer ラベル付けツール**](https://fott-2-1.azurewebsites.net)</li><li>[**REST API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-forms-with-a-custom-model)</li><li>[**クライアント ライブラリ SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Form Recognizer Docker コンテナー**](containers/form-recognizer-container-install-run.md?tabs=custom#run-the-container-with-the-docker-compose-up-command)</li></ul>|

Form Recognizer v3.0 では、次のリソースがサポートされています。

| 機能 | リソース |
|----------|-------------|
|**カスタム モデル**| <ul><li>[**Form Recognizer Studio**](https://fott-2-1.azurewebsites.net)</li><li>[**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>| 

### <a name="try-form-recognizer"></a>Form Recognizer を試す

カスタム モデルを使用して、特定のドキュメントまたは一意のドキュメントからデータを抽出する方法について説明します。 次が必要です。

* Azure サブスクリプション — [無料で作成する](https://azure.microsoft.com/free/cognitive-services/)ことができます

* Azure portal の [Form Recognizer インスタンス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 Free 価格レベル (`F0`) を利用して、サービスを試用できます。 リソースがデプロイされたら、 **[Go to resource (リソースに移動)]** を選択して、API キーとエンドポイントを取得します。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio (プレビュー)

> [!NOTE]
> Form Recognizer スタジオは、プレビュー (v3.0) API で使用できます。

1. Form Recognizer Studio のホーム ページで、 **[Custom form]\(カスタムフォーム\)** を選択します。

1. **[マイ プロジェクト]** で、 **[+ Create a project]\(+ プロジェクトの作成\)** を選択します。

1. **プロジェクトの詳細** を指定するフィールドに値を入力します。

1. **サービス リソースを構成** します。

1. **[Connect your training data source]\(トレーニング データ ソースを接続\)** に、**Storage アカウント** と  **BLOB コンテナー** を追加します。

1. プロジェクトを **確認して作成** します。

1. カスタム モデルをビルドしてテストするための一連のサンプル ドキュメントが用意されています。

    > [!div class="nextstepaction"]
    > [Form Recognizer スタジオを試す](https://formrecognizer.appliedai.azure.com/studio/customform/projects)

#### <a name="sample-labeling-tool"></a>サンプル ラベル付けツール

同じ種類の少なくとも 6 つのフォームのセットが必要になります。 このデータを使用して、モデルのトレーニングとフォームのテストを行います。 [サンプル データ セット](https://go.microsoft.com/fwlink/?linkid=2090451)を使用できます。 *sample_data.zip* をダウンロードして抽出し、コンテンツを Azure Blob Storage コンテナーにアップロードします。

Form Recognizer UI で以下の手順を実行します。

1. サンプル ラベル付けツールのホーム ページで、 **[Use Custom to train a model with labels and get key value pairs]\(キーと値のペアを取得するためにカスタムを使用してラベルでモデルのトレーニングを行う\)** を選択します。

      :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="スクリーンショット: カスタム オプションの FOTTtool の選択。":::

1. 次のウィンドウで **[New project]\(新規プロジェクト\)** を選択します。

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="スクリーンショット: FOTTtools で新しいプロジェクトが選択されます。":::

    詳細な手順については、[サンプル ラベル付けツール](quickstarts/try-sample-label-tool.md)のクイックスタートを *参照* してください。

    > [!div class="nextstepaction"]
    > [サンプル ラベル付けツールを試す](https://fott-2-1.azurewebsites.net/projects/create)

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

  > [!TIP]
  > **トレーニング データ**
  >
  >* 可能であれば、画像ベースのドキュメントではなく、テキストベースの PDF ドキュメントを使用します。 スキャンした PDF は画像として処理されます。
  > * 入力フォームの場合は、すべてのフィールドに入力されている例を使用します。
  > * 各フィールドに異なる値が含まれたフォームを使用します。
  >* フォームの画像の品質が低い場合は、より大きなデータ セット (たとえば 10 から 15 の画像) を使用します。

> [!NOTE]
> [サンプル ラベル付けツール](https://fott-2-1.azurewebsites.net/)では、BMP ファイル形式はサポートされていません。 これは、Form Recognizer サービスではなく、ツールの制限です。

## <a name="supported-languages-and-locales"></a>サポートされている言語とロケール

 Form Recognizer のプレビュー バージョンでは、カスタム モデルに追加の言語サポートが導入されています。 サポートされている手書き文字と印刷テキストの完全な一覧については、[言語サポート](language-support.md#layout-and-custom-model)に関する記事を "*参照してください*"。

## <a name="form-recognizer-preview-v30"></a>Form Recognizer プレビュー v3.0

 Form Recognizer v3.0 (プレビュー) では、いくつかの新機能が導入されています。

* **カスタム モデル API (v3.0)** では、カスタム フォームの署名の検出がサポートされています。 カスタム モデルをトレーニングするときに、特定のフィールドを署名として指定できます。  カスタム モデルでドキュメントが分析されるときに、署名が検出されたかどうかが示されます。

* アプリケーションとワークフローでプレビュー バージョンを使用する方法については、[**Form Recognizer v3.0 移行ガイド**](v3-migration-guide.md)の説明を参照してください。

* プレビュー バージョンと新機能の詳細については、[**REST API (プレビュー)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) に関する記事を参照してください。

### <a name="try-signature-detection"></a>署名の検出を試す

1. トレーニング データセットを作成します。

1. [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio) に移動し、[Custom models]\(カスタム モデル\) で **[Custom form]\(カスタム フォーム\)** を選択します。

    :::image type="content" source="media/label-tool/select-custom-form.png" alt-text="スクリーンショット: Form Recognizer Studio でカスタム フォーム ページを選択します。":::

1. ワークフローに従って、新しいプロジェクトを作成します。

1. カスタム モデルの入力要件に従います。

1. ドキュメントにラベルを付けます。 署名フィールドの場合、精度を向上させるため領域ラベル付けを使用することをお勧めします。

1. ドキュメントにラベルを付けます。 署名フィールドの場合、精度を向上させるため領域ラベル付けを使用することをお勧めします。

    :::image type="content" source="media/label-tool/signature-label-region-too.png" alt-text="スクリーンショット: 署名フィールドへのラベル付け。":::

トレーニング セットにラベルを付けた後は、カスタム モデルをトレーニングし、それを使用してドキュメントを分析できます。 署名フィールドで、署名が検出されたかどうかが指定されます。

## <a name="next-steps"></a>次の手順

* Form Recognizer のクイックスタートを完了する:

  > [!div class="nextstepaction"]
  > [Form Recognizer のクイックスタート](quickstarts/try-sdk-rest-api.md)

* REST API を調べる:

    > [!div class="nextstepaction"]
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
