---
title: 'ハウツー ガイド: カスタム モデルと作成済みモデルを使用する'
titleSuffix: Azure Applied AI Services
description: Form Recognizer のカスタム モデルと作成済みモデルを作成、使用、管理する方法について説明します
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5389e30e2aca2d93ba0fb27c71a6b934d7bf10e0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027791"
---
# <a name="use-custom-and-composed-models"></a>カスタム モデルと作成済みモデルを使用する

Form Recognizer では、高度な機械学習テクノロジを使用して、ドキュメント イメージから情報を検出および抽出し、抽出したデータを構造化 JSON 出力で返します。 Form Recognizer を使用すると、スタンドアロンのカスタム モデルのトレーニングを行ったり、カスタム モデルを組み合わせて作成済みモデルを作成することができます。

* **カスタム モデル**。 Form Recognizer のカスタム モデルを使用すると、ビジネスに固有のフォームやドキュメントに含まれるデータの分析と抽出ができます。 カスタム モデルは、特定のデータとユース ケースに合わせてトレーニングされます。

* **作成済みモデル**。 作成済みモデルは、カスタム モデルのコレクションを取得し、目的のフォームの種類を含む 1 つのモデルに割り当てることによって作成されます。 作成済みモデルにドキュメントが送信されると、サービスによって分類ステップが実行され、分析のために提示されたフォームを正確に表すカスタム モデルが決定されます。

"***Form Recognizer Studio の [モデル構成] ウィンドウ***"

:::image type="content" source="media/studio/composed-model.png" alt-text="スクリーンショット: Form Recognizer Studio の [モデル構成] ウィンドウ。":::

この記事では、[Form Recognizer サンプル ラベル付けツール](label-tool.md)、[REST API](quickstarts/client-library.md?branch=main&pivots=programming-language-rest-api#train-a-custom-model)、または[クライアント ライブラリ SDK](quickstarts/client-library.md?branch=main&pivots=programming-language-csharp#train-a-custom-model) を使用して Form Recognizer のカスタム モデルと作成済みモデルを作成する方法について説明します。

## <a name="sample-labeling-tool"></a>サンプル ラベル付けツール

カスタム フォームからデータを抽出する方法を確認するには、サンプル ラベル付けツールを試してください。 次が必要です。

* Azure サブスクリプション: [無料で作成できます](https://azure.microsoft.com/free/cognitive-services/)。

* Azure portal の [Form Recognizer インスタンス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)。 Free 価格レベル (`F0`) を利用して、サービスを試用できます。 リソースがデプロイされたら、 **[Go to resource (リソースに移動)]** を選択して、API キーとエンドポイントを取得します。

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

> [!div class="nextstepaction"]
> [試してみる](https://fott-2-1.azurewebsites.net/projects/create)

Form Recognizer UI で以下の手順を実行します。

1. **[Use Custom to train a model with labels and get key value pairs]\(キーと値のペアを取得するためにカスタムを使用してラベルでモデルのトレーニングを行う\)** を選択してください。
  
      :::image type="content" source="media/label-tool/fott-use-custom.png" alt-text="スクリーンショット: カスタム オプションの fott ツールの選択。":::

1. 次のウィンドウで **[New project]\(新規プロジェクト\)** を選択します:

    :::image type="content" source="media/label-tool/fott-new-project.png" alt-text="スクリーンショット: 新しいプロジェクトの fott ツールの選択。"::: 

## <a name="create-your-models"></a>モデルを作成する

カスタム モデルと作成済みモデルを構築、トレーニング、使用するための手順は次のとおりです。

* [**トレーニング データセットを作成する**](#assemble-your-training-dataset)
* [**トレーニング セットを Azure BLOB ストレージにアップロードする**](#upload-your-training-dataset)
* [**カスタム モデルをトレーニングする**](#train-your-custom-model)
* [**カスタム モデルを作成する**](#create-a-composed-model)
* [**ドキュメントを分析する**](#analyze-documents-with-your-custom-or-composed-model)
* [**カスタム モデルを管理する**](#manage-your-custom-models)

## <a name="assemble-your-training-dataset"></a>トレーニング データセットを作成する

カスタム モデルの構築は、トレーニング データセットの確立から始まります。 サンプル データセットには、同じ種類の完成したフォームが少なくとも 5 つ必要です。 異なるファイルの種類 (jpg、png、pdf、tiff) にして、テキストと手書きの両方を含めることができます。 フォームは Form Recognizer の[入力要件](build-training-data-set.md#custom-model-input-requirements)に従う必要があります。

## <a name="upload-your-training-dataset"></a>トレーニング データセットをアップロードする

Azure BLOB ストレージ コンテナーに[トレーニング データをアップロード](build-training-data-set.md#upload-your-training-data)する必要があります。 コンテナーを含む Azure Storage アカウントを作成する方法がわからない場合は、[Azure portal の Azure Storage に関するクイックスタート](../../storage/blobs/storage-quickstart-blobs-portal.md)を *参照してください*。 Free 価格レベル (F0) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="train-your-custom-model"></a>カスタム モデルをトレーニングする

ラベル付きデータ セットの有無にかかわらず、[モデルをトレーニング](./quickstarts/try-sdk-rest-api.md#train-a-custom-model)できます。 ラベルのないデータセットの場合、[Layout API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync) のみに依存し、ユーザーの入力を追加することなく重要な情報を検出および特定できます。 ラベル付きデータセットの場合も、Layout API をに依存していますが、特定のラベルやフィールドの場所など、補助的なユーザーの入力が含まれています。 ラベル付きデータとラベルなしデータの両方を使用するには、ラベル付きトレーニング データに同じ種類の少なくとも 5 つの完成したフォームから始めて、ラベルなしデータを必要なデータ セットに追加します。

### <a name="train-without-labels"></a>ラベルを使用しないトレーニング

Form Recognizer では、教師なし学習を使用して、フォーム内でのレイアウトおよびフィールドやエントリ間の関係を把握します。 入力フォームを送信すると、アルゴリズムによって種類別にフォームが分類されて、存在するキーとテーブルが検出されます。また、値がキーに、エントリがテーブルに関連付けられます。 ラベルを使用しないトレーニングでは、手動によるデータのラベル付けや大量のコーディングとメンテナンスは必要ありません。この方法を最初に試してみることをお勧めします。

トレーニング ドキュメントを収集する方法のヒントについては、「[トレーニング データ セットの作成](./build-training-data-set.md)」を参照してください。

### <a name="train-with-labels"></a>ラベルを使用したトレーニング

ラベル付けされたデータによるモデルのトレーニングでは、ラベル付けされた指定のフォームを使用して、教師あり学習を使って、目的とする値が抽出されます。 ラベル付きデータにより、性能の高いモデルが得られ、複雑なフォームやキーのない値を含んだフォームでも機能するモデルを作成できます。

Form Recognizer では、[Layout API](concept-layout.md) を使用して、印刷または手書きのテキストの要素について予想されるサイズや位置を学習し、テーブルを抽出します。 その後、ユーザーによって指定されたラベルを使用して、ドキュメントに含まれるキーと値の関係およびテーブルを学習します。 新しいモデルをトレーニングする際はまず、手動でラベル付けされた同じタイプ (同じ構造) のフォーム 5 つを使用し、そのうえで、モデルの精度を改善するために必要であれば、ラベル付けされたデータを追加することをお勧めします。 Form Recognizer を使用すると、教師あり学習機能を使用してキーと値のペアおよびテーブルを抽出するようにモデルをトレーニングできます。

[ラベルを使用したトレーニングを開始する](label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="create-a-composed-model"></a>作成済みモデルを作成する

> [!NOTE]
> **[モデルの作成] は、ラベル _を使用して_ トレーニングするカスタム モデルでのみ使用できます。** ラベルのないモデルを作成しようとすると、エラーが発生します。

[モデルの作成] 操作では、100 個までのトレーニング済みカスタム モデルを 1 つのモデル ID に割り当てることができます。 作成済みモデルの ID で Analyze を呼び出すと、まず送信されたフォームが分類され、最も適合する割り当て済みモデルが選択され、そのモデルに対する結果が返されます。 この操作は、受信フォームが複数のテンプレートのいずれかに属している場合に役立ちます。

Form Recognizer サンプル ラベル付けツール、REST API、またはクライアント ライブラリ SDK を使用し、次の手順に従って、作成済みモデルをセットアップします。

1. [**カスタム モデル ID を収集する**](#gather-your-custom-model-ids)
1. [**カスタム モデルを作成する**](#compose-your-custom-models)

#### <a name="gather-your-custom-model-ids"></a>カスタム モデル ID を収集する

トレーニング プロセスが正常に完了すると、カスタム モデルにモデル ID が割り当てられます。 モデル ID は次のようにして取得できます。

### <a name="form-recognizer-sample-labeling-tool"></a>[**Form Recognizer サンプル ラベル付けツール**](#tab/fott)

[**Form Recognizer サンプル ラベル付けツール**](https://fott-2-1.azurewebsites.net/)を使用してモデルをトレーニングすると、モデル ID が [Train Result]\(トレーニング結果\) ウィンドウに表示されます。

:::image type="content" source="media/fott-training-results.png" alt-text="スクリーンショット: トレーニング結果ウィンドウ。":::

### <a name="rest-api"></a>[**REST API**](#tab/rest-api)

[**REST API**](./quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#train-a-custom-model) から、**Location** ヘッダーを含む `201 (Success)` 応答が返されます。 このヘッダーの最後のパラメーターの値は、新しくトレーニングしたモデルのモデル ID です。

:::image type="content" source="media/model-id.png" alt-text="スクリーンショット: 返された Location ヘッダーにモデル ID が含まれている。":::

### <a name="client-library-sdks"></a>[**クライアント ライブラリ SDK**](#tab/sdks)

 [**クライアント ライブラリ SDK**](./quickstarts/try-sdk-rest-api.md?pivots=programming-language-csharp#train-a-custom-model) から返されたモデル オブジェクトをクエリすることで、トレーニングしたモデルの ID を取得できます。

* \#  | [CustomFormModel クラス](/dotnet/api/azure.ai.formrecognizer.training.customformmodel?view=azure-dotnet&preserve-view=true#properties "Azure SDK for .NET")

* Java | [CustomFormModelInfo クラス](/java/api/com.azure.ai.formrecognizer.training.models.customformmodelinfo?view=azure-java-stable&preserve-view=true#methods "Azure SDK for Java")

* JavaScript | [CustomFormModelInfo インターフェイス](/javascript/api/@azure/ai-form-recognizer/customformmodelinfo?view=azure-node-latest&preserve-view=true&branch=main#properties "Azure SDK for JavaScript")

* Python | [CustomFormModelInfo クラス](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.customformmodelinfo?view=azure-python&preserve-view=true&branch=main#variables "Azure SDK for Python")

---

#### <a name="compose-your-custom-models"></a>カスタム モデルを作成する

1 つのフォームの種類に対応するカスタム モデルを収集したら、それらをまとめた 1 つのモデルを作成できます。

### <a name="form-recognizer-sample-labeling-tool"></a>[**Form Recognizer サンプル ラベル付けツール**](#tab/fott)

**サンプル ラベル付けツール** を使用すると、モデルをトレーニングし、それらを 1 つのモデル ID にまとめる作業をすばやく開始できます。

トレーニングが完了したら、次のようにしてモデルを作成します。

1. 左側のレール メニューで、 **[モデルの作成] アイコン** (マージ矢印) を選択します。

1. メイン ウィンドウで、1 つのモデル ID に割り当てるモデルを選択します。 矢印アイコンの付いたモデルは、既に作成済みのモデルです。

1. 左上隅にある **[作成] ボタン** を選択します。

1. ポップアップ ウィンドウで、新しく作成するモデルに名前を付け、 **[作成]** をクリックします。

操作が完了すると、新しく作成されたモデルが一覧に表示されます。

  :::image type="content" source="media/custom-model-compose.png" alt-text="スクリーンショット: モデル作成ウィンドウ。" lightbox="media/custom-model-compose-expanded.png":::

### <a name="rest-api"></a>[**REST API**](#tab/rest-api)

**REST API** を使用して [**Compose Custom Model**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/Compose) 要求を実行し、既存のモデルから 1 つの作成済みモデルを作成することができます。 要求の本文には、作成する `modelIds` の文字列配列が必要であり、必要に応じて `modelName` を定義できます。  [非同期でのモデルの作成](/rest/api/formrecognizer/2.1preview2/compose-custom-models-async/compose-custom-models-async)に関する記事を *参照* してください。

### <a name="client-library-sdks"></a>[**クライアント ライブラリ SDK**](#tab/sdks)

任意のプログラミング言語コードを使用して、1 つのモデル ID で呼び出される作成済みモデルを作成します。 既存のカスタム モデルから作成済みモデルを作成する方法を示したコード サンプルへのリンクを次に示します。

* [**C#/.NET**](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample_ModelCompose.md)

* [**Java**](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/administration/CreateComposedModel.java)

* [**JavaScript**](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/formrecognizer/ai-form-recognizer/samples/v3/javascript/createComposedModel.js)

* [**Python**](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/v3.2-beta/sample_create_composed_model.py)

---

## <a name="analyze-documents-with-your-custom-or-composed-model"></a>カスタムまたは作成済みモデルを使用してドキュメントを分析する

 カスタム フォームの **Analyze** 操作では、Form Recognizer への呼び出しで `modelID` を指定する必要があります。 `modelID` パラメーターには、1 つのカスタム モデル ID または作成済みモデル ID を指定できます。

### <a name="form-recognizer-sample-labeling-tool"></a>[**Form Recognizer サンプル ラベル付けツール**](#tab/fott)

1. ツールの左ペインのメニューで、 **[分析] アイコン** (電球) を選択します。

1. 分析するローカル ファイルまたは画像の URL を選択します。

1. **[Run Analysis]\(分析の実行\)** ボタンを選択します。

1. このツールでは、境界ボックスにタグが適用され、各タグの信頼度がパーセント単位でレポートされます。

:::image type="content" source="media/analyze.png" alt-text="スクリーンショット: Form Recognizer ツールのカスタム フォーム分析ウィンドウ。":::

### <a name="rest-api"></a>[**REST API**](#tab/rest-api)

REST API を使用して [Analyze Form](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) 要求を実行し、ドキュメントを分析したり、キーと値のペア、およびテーブル データを抽出したりできます。

### <a name="client-library-sdks"></a>[**クライアント ライブラリ SDK**](#tab/sdks)

任意のプログラミング言語と、カスタムまたは作成済みモデルを使用してフォームまたはドキュメントを分析します。 Form Recognizer エンドポイント、API キー、モデル ID が必要になります。

* [**C#/.NET**](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample_ModelCompose.md)

* [**Java**](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/java/com/azure/ai/formrecognizer/AnalyzeCustomDocumentFromUrl.java)

* [**JavaScript**](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/formrecognizer/ai-form-recognizer/samples/v3/javascript/recognizeCustomForm.js)

* [**Python**](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/formrecognizer/azure-ai-formrecognizer/samples/v3.1/sample_recognize_custom_forms.py)

---

トレーニング データセットの一部ではなかった[フォームを分析](./quickstarts/try-sdk-rest-api.md#analyze-forms-with-a-custom-model)して、新しくトレーニングしたモデルをテストします。 レポートされる精度によっては、モデルを改善するために、さらにトレーニングを行う必要が生じる場合があります。 さらにトレーニングを続けて[結果を改善する](label-tool.md#improve-results)ことができます。

## <a name="manage-your-custom-models"></a>カスタム モデルを管理する

ライフサイクル全体にわたって[カスタム モデルを管理](./quickstarts/try-sdk-rest-api.md#manage-custom-models)できます。サブスクリプションに属する[すべてのカスタム モデルの一覧](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModels)を表示したり、[特定のカスタム モデル](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetCustomModel)に関する情報を取得したり、アカウントから[カスタム モデルを削除](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/DeleteCustomModel)したりします。

お疲れ様です。 カスタム モデルと作成済みモデルを作成し、それらを Form Recognizer プロジェクトとアプリケーションで使用する手順について学習しました。

## <a name="next-steps"></a>次のステップ

API リファレンスのドキュメントを参照して、Form Recognizer クライアント ライブラリの詳細について理解します。

> [!div class="nextstepaction"]
> [Form Recognizer API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
>
