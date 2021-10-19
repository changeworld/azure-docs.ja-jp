---
title: Form Recognizer のカスタム モデルと作成済みモデル
titleSuffix: Azure Applied AI Services
description: Form Recognizer のカスタム モデルと作成済みモデルを作成、使用、管理する方法について説明します。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 181d96d2e722d7f9b79c47285bab417bf0337133
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754674"
---
# <a name="form-recognizer-custom-and-composed-models"></a>Form Recognizer のカスタム モデルと作成済みモデル

Form Recognizer では、高度な機械学習テクノロジを使用して、ドキュメント イメージから情報を検出および抽出し、抽出したデータを構造化 JSON 出力で返します。 Form Recognizer を使用すると、スタンドアロンのカスタム モデルのトレーニングを行ったり、カスタム モデルを組み合わせて作成済みモデルを作成することができます。

* **カスタム モデル**。 Form Recognizer のカスタム モデルを使用すると、ビジネスに固有のフォームやドキュメントに含まれるデータの分析と抽出ができます。 カスタム モデルは、特定のデータとユース ケースに合わせてトレーニングされます。

* **作成済みモデル**。 作成済みモデルは、カスタム モデルのコレクションを取得し、目的のフォームの種類を含む 1 つのモデルに割り当てることによって作成されます。 作成済みモデルにドキュメントが送信されると、サービスによって分類ステップが実行され、分析のために提示されたフォームを正確に表すカスタム モデルが決定されます。

:::image type="content" source="media/analyze.png" alt-text="スクリーンショット: Form Recognizer ツールのカスタム フォーム分析ウィンドウ。":::


## <a name="what-is-a-custom-model"></a>カスタム モデルとは

カスタム モデルとは、特定のコンテンツ内のフォーム フィールドを認識し、キーと値のペア、およびテーブル データを抽出するためにトレーニングされる機械学習プログラムのことです。 始めるために必要なのは同じフォームの種類の 5 つの例だけであり、カスタム モデルのトレーニングは、ラベル付けされたデータセットがあってもなくても実行できます。

## <a name="what-is-a-composed-model"></a>作成済みモデルとは

作成済みモデルを使用すると、1 つのモデル ID で呼び出される作成済みモデルに複数のカスタム モデルを割り当てることができます。 これは、複数のモデルをトレーニングし、類似したフォームの種類を分析するためにそれらをグループ化する場合に役立ちます。 たとえば、作成済みモデルには、サプライ、備品、家具の発注書を分析するようにトレーニングされたカスタム モデルを含めることができます。 適切なモデルを手動で選択する代わりに、作成済みモデルを使用すれば、分析と抽出ごとに適切なカスタム モデルを決定できます。

## <a name="try-form-recognizer-studio-preview"></a>Form Recognizer Studio (プレビュー) を試す

* Form Recognizer Studio は、プレビュー (v3.0) API で使用できます。

* Form Recognizer Studio のカスタム フォーム機能を使用して、特定または固有の種類のフォームを分析します。

> [!div class="nextstepaction"]
> [Form Recognizer Studio を試す](https://formrecognizer.appliedai.azure.com/studio/customform/projects)

## <a name="try-form-recognizer-sample-labeling-tool"></a>Form Recognizer サンプル ラベル付けツールを試す

カスタム フォームからデータを抽出する方法を確認するには、サンプル ラベル付けツールを試してください。 次が必要です。

* Azure サブスクリプション — [無料で作成できます](https://azure.microsoft.com/free/cognitive-services/)

* Azure portal の [Form Recognizer インスタンス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 Free 価格レベル (`F0`) を利用して、サービスを試用できます。 リソースがデプロイされたら、 **[リソースに移動]** をクリックして API キーとエンドポイントを取得します。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

> [!div class="nextstepaction"]
> [試してみる](https://fott-2-1.azurewebsites.net/projects/create)

Form Recognizer UI で以下の手順を実行します。

1. **[Use Custom to train a model with labels and get key value pairs]\(キーと値のペアを取得するためにカスタムを使用してラベルでモデルのトレーニングを行う\)** を選択します。

      :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="スクリーンショット: カスタム オプションの FOTTtool の選択。":::

1. 次のウィンドウで **[New project]\(新規プロジェクト\)** を選択します。

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="スクリーンショット: FOTTtools で新しいプロジェクトが選択されます。":::

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

* アプリケーションとワークフローでプレビュー バージョンを使用する方法については、[**Form Recognizer v3.0 移行ガイド**](v3-migration-guide.md)に関する記事を参照してください。

* プレビュー バージョンと新機能の詳細については、[**REST API (プレビュー)** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) に関する記事を参照してください。

### <a name="try-signature-detection"></a>署名の検出を試す

1. [**トレーニング データセットを作成します**](build-training-data-set.md#custom-model-input-requirements)。

1. [**Form Recognizer サンプル ラベル付ツール**](https://fott-preview-private.azurewebsites.net)に移動し、 **[Use Custom to train a models with labels and get key value pairs]\(カスタムを使用してラベルでモデルをトレーニングし、キーと値のペアを取得する\)** を選択します。

    :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="スクリーンショット: カスタム オプションの FOTTtools の選択。":::

1. 次のウィンドウで **[New project]\(新規プロジェクト\)** を選択します。

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="スクリーンショット: FOTTtools で新しいプロジェクトが選択されます。":::

1. 「[**カスタム モデルの入力要件**](build-training-data-set.md#custom-model-input-requirements)」に従います。

1. 種類を **署名** にしてラベルを作成します。

1. **ドキュメントにラベルを付けます**。  署名フィールドの場合、精度を向上させるため領域ラベル付けを使用することをお勧めします。

1. トレーニング セットにラベルを付けた後は、**カスタム モデルをトレーニング** し、それを使用してドキュメントを分析できます。 署名フィールドで、署名が検出されたかどうかが指定されます。

## <a name="next-steps"></a>次の手順

* Form Recognizer のクイックスタートを完了する:

  > [!div class="nextstepaction"]
  > [Form Recognizer のクイックスタート](quickstarts/try-sdk-rest-api.md)

* REST API を調べる:

    > [!div class="nextstepaction"]
    > [Form Recognizer API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
