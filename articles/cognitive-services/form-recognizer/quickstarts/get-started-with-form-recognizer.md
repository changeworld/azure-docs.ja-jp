---
title: 'クイック スタート: サンプル ラベル付けツールを使用したフォームのラベル付け、モデルのトレーニング、フォームの分析 - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Form Recognizer のサンプル ラベル付けツールを使用して、フォーム ドキュメントに手動でラベルを付けます。 次に、ラベル付けされたドキュメントを使用してカスタム ドキュメント処理モデルをトレーニングし、そのモデルを使用してキーと値のペアを抽出します。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/14/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-may-2021
keywords: ドキュメントの処理
ms.openlocfilehash: c833e08915347682276cb53b45c507902d5ea43f
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382281"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
<!-- markdownlint-disable MD029 -->
# <a name="get-started-with-form-recognizer"></a>Form Recognizer の概要

Form Recognizer サンプル ツールを使用する Form Recognizer の概要 Azure Form Recognizer は、機械学習テクノロジを利用して自動データ処理ソフトウェアを構築ことを可能にするコグニティブ サービスです。 フォーム ドキュメントから、テキスト、キーと値のペア、選択マーク、テーブル データなどを特定して抽出します。このサービスによって、元のファイルにおけるリレーションシップを含む構造化データが出力されます。 Form Recognizer は、サンプル ツール、REST API または SDK を介して使用できます。 次の手順に従って、サンプル ツールで Form Recognizer を試してください。

Form Recognizer の用途:

* Analyze Layout
* 事前構築済みモデル (請求書、領収書、ID ドキュメント) を使用して分析する
* カスタムフォームのトレーニングと解析

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* Azure サブスクリプションを用意できたら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer リソースを作成"  target="_blank">Form Recognizer リソースを作成</a>し、自分のキーとエンドポイントを取得します。
  * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* (省略可能) 次のクイックスタート サンプル ドキュメントをダウンロードして解凍します

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer リソースを作成する

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-layout"></a>レイアウトを分析する

ドキュメントからテキスト、テーブル、選択マーク、および構造を抽出します。

1. [Form Recognizer サンプル ツール](https://fott-2-1.azurewebsites.net/)に進む
2. サンプル ツールのホーム ページで、[use layout to get text, tables and selection marks]\(レイアウトを使用してテキスト、テーブル、選択マークを取得する) を選択します

     :::image type="content" source="../media/label-tool/layout-1.jpg" alt-text="Layout Form Recognizer ツールの接続設定。":::

3. {need Endpoint} を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。

4. {need apiKey} を、Form Recognizer リソースから取得したサブスクリプション キーに置き換えます。

    :::image type="content" source="../media/label-tool/layout-2.jpg" alt-text="Layout Form Recognizer ツールの接続設定。":::

5. ソース URL を選択し、サンプル ドキュメント https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/layout-page-001.jpg の次の URL を貼り付け、[Fetch]\(フェッチ\) ボタンをクリックします。

1. [Run Layout]\(レイアウトの実行\) をクリックしてください。Form Recognizer サンプル ラベル付けツールにより、Analyze Layout API を呼び出してドキュメントを解析します。

1. 結果を表示する - 抽出された強調表示テキスト、検出された選択マーク、検出されたテーブルを確認します。

    :::image type="content" source="../media/label-tool/layout-3.jpg" alt-text="Form Recognizer ツールの接続設定。":::

1. JSON 出力ファイルをダウンロードして、詳細なレイアウト結果を表示します。
     * "readResults" ノードには、あらゆるテキスト行が、ページ上の対応する境界ボックスの配置と共に表示されます。
     * "selectionMarks" ノードには、すべての選択マーク (チェック ボックス、ラジオ マーク) と、その状態が「選択済み」と「未選択」のどちらであるかが示されます。
     * 抽出された表は、"pageResults" セクションに含まれています。 それぞれの表について、テキスト、行インデックス、列インデックス、行スパン、列スパン、境界ボックスなどが抽出されます。

## <a name="analyze-using-a-prebuilt-model-invoices-receipts-ids-"></a>事前構築済みモデル (請求書、領収書、ID ドキュメント) を使用して分析する

Form Recognizer 事前構築済みモデルを使用して、請求書、領収書、ID ドキュメント、または名刺からテキスト、テーブル、キー値のペアを抽出します。

1. [Form Recognizer サンプル ツール](https://fott-2-1.azurewebsites.net/)に進む
2. サンプル ツールのホーム ページで、[use prebuilt model to get data]\(事前構築済みモデルを使用してデータを取得する\) を選択します

    :::image type="content" source="../media/label-tool/prebuilt-1.jpg" alt-text="Form Recognizer レイアウトの結果を解析する":::

3. ソース URL を選択します。

4. 次のオプションを使用して、分析するファイルを選択します。

    * 請求書の画像の URL。 このクイックスタートでは、[サンプル請求書ドキュメント](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf)を使用できます。
    * レシートの画像の URL。 このクイックスタートでは、[サンプル ID ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/id-us-driver-license-wa.jpg)を使用できます。
    * レシートの画像の URL。 このクイックスタートでは、[サンプルの領収書画像](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg)を使用できます。
    * 名刺の画像の URL。 このクイックスタートでは、[サンプル名刺画像](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg)を使用できます。

5. {need Endpoint} を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。

6. {need apiKey} を、Form Recognizer リソースから取得したサブスクリプション キーに置き換えます。

    :::image type="content" source="../media/label-tool/prebuilt-3.jpg" alt-text="事前構築済み Form Recognizer ツールの接続設定。":::

7. 解析して選択するドキュメントの種類に基づいて、分析するフォームの種類 (請求書、領収書、名刺、または ID) を選択します。

8. [Run analysis]\(解析の実行\) をクリックします。 Form Recognizer サンプル ラベル付けツールにより、Analyze Prebuilt API を呼び出してドキュメントを解析します。
9. 結果を表示する - 抽出されたキーと値のペア、行項目、抽出された強調表示テキスト、および検出されたテーブルを確認します。

    :::image type="content" source="../media/label-tool/prebuilt-2.jpg" alt-text="Form Recognizer の事前構築済み請求書の結果を解析する":::

10. JSON 出力ファイルをダウンロードして、詳細なレイアウト結果を表示します。

    * "readResults" ノードには、あらゆるテキスト行が、ページ上の対応する境界ボックスの配置と共に表示されます。
    * "selectionMarks" ノードには、すべての選択マーク (チェック ボックス、ラジオ マーク) と、その状態が「選択済み」と「未選択」のどちらであるかが示されます。
    * 抽出された表は、"pageResults" セクションに含まれています。 それぞれの表について、テキスト、行インデックス、列インデックス、行スパン、列スパン、境界ボックスなどが抽出されます。
    * "documentResults" フィールドには、キーと値のペアの情報と、ドキュメントの最も関連性の高い部分の行項目の情報が含まれます。

## <a name="train--analyze-a-custom-form"></a>カスタムフォームのトレーニングと解析

ドキュメントに合わせたカスタム フォーム モデルをトレーニングします。 テキスト、テーブル、選択マーク、およびキーと値のペアを、Form Recognizer カスタムのドキュメントから抽出します。

### <a name="prerequisites-for-training-a-custom-form-model"></a>カスタム フォーム モデルをトレーニングするための前提条件

* トレーニング データのセットを含む Azure Storage Blob コンテナー。 まず、すべてのトレーニング ドキュメントが同じ形式であることを確認します。 複数の形式のフォームがある場合は、共通する形式に基づいてサブフォルダーに分類します。 このクイックスタートでは、[サンプル データ セット](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample_data_without_labels.zip)の Train フォルダーにあるファイルを使用できます (sample_data.zip をダウンロードして展開)。
* ストレージ アカウントでAzure Storage blob 対応 CORS でクロスドメインリソース共有 (CORS) を構成します。 Azure portal で自分のストレージ アカウントを選択し、左側のペインで **[CORS]** タブを選択します。 一番下の行に、次の値を入力します。 上部にある **[保存]** を選択します。 </br></br>

  * [許可されたドメイン] = *
  * [許可されたメソッド] = \[すべて選択\]
  * [許可されたヘッダー] = *
  * [公開されるヘッダー] = *
  * [最長有効期間] = 200

> [!div class="mx-imgBorder"]
> ![Azure portal での CORS の設定](../media/label-tool/cors-setup.png)

## <a name="train-a-custom-form-model"></a>カスタム フォーム モデルをトレーニングする

1. [Form Recognizer サンプル ツール](https://fott-2-1.azurewebsites.net/)に進む

1. サンプル ツールのホームページで、[use custom form to train a model with labels and get key value pairs]\(カスタムフォームを使用してラベルを持つモデルをトレーニングし、キーと値のペアを取得する\) を選択します。

    :::image type="content" source="../media/label-tool/custom-1.jpg" alt-text="カスタム モデルをトレーニングします。":::

2. [新しいプロジェクト] を選択する

#### <a name="create-a-new-project"></a>新しいプロジェクトを作成する

プロジェクト設定を構成し、フィールドに次の値を入力します。

* **[表示名]** - プロジェクトの表示名
* **[セキュリティ トークン]** - 一部のプロジェクト設定には、API キーや他の共有シークレットなどの機密性の高い値を含めることができます。 各プロジェクトでは、機密性の高いプロジェクト設定の暗号化または暗号化解除に使用できるセキュリティ トークンが生成されます。 セキュリティ トークンは、左側のナビゲーション バーの下部にある歯車アイコンを選択すると、[アプリケーション設定] に表示されます。

* **ソース接続** - サンプル ラベル付けツールは、ソース (元のアップロードされたフォーム) とターゲット (作成されたラベルと出力データ) に接続されます。 接続は、複数のプロジェクトにまたがって設定および共有できます。 拡張可能なプロバイダー モデルが使用されるため、新しいソースまたはターゲット プロバイダーを簡単に追加できます。 新しい接続を作成し、 **[Add Connection]** \(接続の追加\) ボタンをクリック します。 フィールドに次の値を入力します。
   * **[表示名]** - 接続の表示名。
   * **[説明]** - プロジェクトの説明。
   * **[SAS URL]** - Azure Blob Storage コンテナーの Shared Access Signature (SAS) URL。

   [!INCLUDE [get SAS URL](../includes/sas-instructions.md)]

   :::image type="content" source="../media/quickstarts/get-sas-url.png" alt-text="SAS の場所。":::

* **[フォルダー パス]** (省略可能) - ソース フォームが BLOB コンテナー上のフォルダーに配置されている場合は、ここにフォルダー名を指定します。
* **[Form Recognizer Service Uri]\(Form Recognizer サービスの URI\)** - Form Recognizer のエンドポイント URL。
* **[API キー]** - Form Recognizer のサブスクリプション キー。
* **[説明]** (省略可能) - プロジェクトの説明

    :::image type="content" source="../media/label-tool/connections.png"  alt-text="接続の設定":::

#### <a name="label-your-forms"></a>フォームにラベルを付ける

  :::image type="content" source="../media/label-tool/new-project.png"  alt-text="新規プロジェクト ページ":::

プロジェクトを作成するか開くと、メインのタグ エディター ウィンドウが開きます。 このタグ エディターは、次の 3 つの部分で構成されます。

* サイズ変更可能なプレビュー ペイン。基になる接続にあるフォームのスクロール可能な一覧が表示されます。
* メインのエディター ペイン。ここで、タグを適用できます。
* タグ エディター ペイン。ここで、タグの変更、ロック、並べ替え、削除を行うことができます。

##### <a name="identify-text-and-tables"></a>テキストとテーブルを特定する

左側のペインにある **[Run OCR on all files]\(すべてのファイルに対して OCR を実行\)** を選択して、各ドキュメントのテキストとテーブルのレイアウト情報を取得します。 ラベル付けツールによって、各テキスト要素の周囲に境界ボックスが描画されます。

ラベル付けツールでは、どのテーブルが自動的に抽出されたかも示されます。 ドキュメントの左側にあるテーブル (グリッド) アイコンを選択すると、抽出されたテーブルが表示されます。 テーブルの内容は自動的に抽出されるため、このクイックスタートではテーブルの内容に対するラベル付けは行わず、自動化された抽出を信頼することにします。

  :::image type="content" source="../media/label-tool/table-extraction.png" alt-text="サンプル ラベル付けツールでのテーブルの視覚化。":::

##### <a name="apply-labels-to-text"></a>ラベルをテキストに適用する

次に、タグ (ラベル) を作成し、モデルに解析させるテキスト要素に適用します。 サンプル ラベル データ セットには、既に別のフィールドを追加するラベル付きフィールドが含まれています。

1. まず、タグ エディター ウィンドウを使用して、識別する新しいタグを作成します。
   1. **+** を選択して、新しいタグを作成します。
   1. タグ名を入力します。 「合計」タグを追加する
   1. Enter キーを押して、タグを保存します。
1. メインのエディターで、強調表示されたテキスト要素から合計の値を選択します。
1. 値に適用する合計タグを選択するか、対応するキーボード キーを押します。 数字キーは、最初の 10 個のタグのホットキーとして割り当てられます。 タグの順序は、タグ エディター ペインの上矢印と下矢印のアイコンを使用して変更できます。

    > [!Tip]
    > フォームにラベルを付けるときは、次のヒントに留意してください。
    >
    > * 適用できるタグは、選択したテキスト要素ごとに 1 つのみです。
    > * 各タグは、1 ページにつき 1 回のみ適用できます。 同じフォームに同じ値が複数回出現する場合は、インスタンスごとに異なるタグを作成します。 たとえば、"invoice # 1"、"invoice # 2" などとします。
    > * タグは複数のページにまたがることはできません。
    > * フォームに表示されるラベル値は、2 つの異なるタグを使用して 2 つの部分に分割しないでください。 たとえば、アドレス フィールドが複数の行にまたがる場合でも、1 つのタグを使用してラベルを付ける必要があります。
    > * タグが付けられたフィールドには値のみを含めます。キーは含めないでください。
    > * テーブル データは自動的に検出され、'pageResults' セクションの最終的な出力 JSON ファイルに表示されます。 ただし、モデルですべてのテーブル データの検出に失敗した場合は、モデルにラベルを付けテーブルを検出するトレーニングを行うことができます。トレーニングとラベル付けの方法については、<< route to the how to >> を参照してください。
    > * **+** の右にあるボタンを使用して、タグの検索、名前の変更、順序変更、削除を行います。
    > * タグそのものは削除せずに、適用されているタグを解除するには、タグ付けされた四角形をドキュメント ビューで選択し、Delete キーを押します。
    >

上記の手順に従って、サンプル データセット内の 5 つのフォームにラベルを付けます。

  :::image type="content" source="../media/label-tool/custom-1.jpg" alt-text="サンプルにラベルを付けます。":::

#### <a name="train-a-custom-model"></a>カスタム モデルをトレーニングする

左側のペインでトレーニング アイコンを選択して、[トレーニング] ページを開きます。 次に、 **[Train]\(トレーニング\)** ボタンを選択して、モデルのトレーニングを開始します。 トレーニング プロセスが完了すると、次の情報が表示されます。

* **[Model ID]** - 作成およびトレーニングされたモデルの ID。 トレーニングの呼び出しごとに、独自の ID を持つ新しいモデルが作成されます。 この文字列を安全な場所にコピーしてください。[REST API](./client-library.md?pivots=programming-language-rest-api) または[クライアント ライブラリ](./client-library.md)を使用して予測呼び出しを行う場合に必要になります。
* **[Average Accuracy]\(平均精度\)** - モデルの平均精度。 追加のフォームにラベルを付け、再度トレーニングを行って新しいモデルを作成することにより、モデルの精度を向上させることができます。 最初に、結果を解析してテストする 5 つのフォームにラベルを付け、必要に応じてフォームを追加することをお勧めします。
* タグの一覧と、タグごとの予測精度。

    :::image type="content" source="../media/label-tool/custom-2.jpg" alt-text="トレーニング ビュー。":::

#### <a name="analyze-a-custom-form"></a>カスタムフォームを解析する

左側の解析 (電球) アイコンを選択して、モデルをテストします。 ソースの「ローカル ファイル」を選択します。 ファイルを参照し、テスト フォルダーに展開したサンプル データセットからファイルを選択します。 次に、 **[Run analysis]** \(解析の実行\) ボタンを選択して、フォームのキーと値のペア、テキスト、テーブルの予測を取得します。 このツールでは、境界ボックスにタグが適用され、各タグの信頼度がレポートされます。

:::image type="content" source="../media/label-tool/custom-3.jpg" alt-text="トレーニング ビュー ツール。":::

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Form Recognizer サンプル ツールを使用して、カスタム モデルのレイアウト、事前構築、トレーニングを試し、手動でラベル付けされたデータを使用してカスタム フォームを解析する方法について説明しました。 これで、クライアント ライブラリ SDK または REST API を試して Form Recognizer を使用します。

> [!div class="nextstepaction"]
> [Form Recognizer のクライアント ライブラリ SDK と REST API クイックスタートを利用する](client-library.md)
