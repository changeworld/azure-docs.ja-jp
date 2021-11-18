---
title: 'クイックスタート: サンプル ラベル付けツールを使用したフォームのラベル付け、モデルのトレーニング、フォームの分析 - Azure Form Recognizer'
titleSuffix: Azure Applied AI Services
description: このクイックスタートでは、Azure Form Recognizer のサンプル ラベル付けツールを使用して、フォーム ドキュメントに手動でラベルを付ける方法を学習します。 次に、ラベル付けされたドキュメントを使用してカスタム ドキュメント処理モデルをトレーニングし、そのモデルを使用してキーと値のペアを抽出します。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-may-2021, ignite-fall-2021
keywords: ドキュメントの処理
ms.openlocfilehash: 88bc19e245c3272c0ef7bdf958a29da36d668f7c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132710712"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
<!-- markdownlint-disable MD029 -->
# <a name="get-started-with-the-sample-labeling-tool"></a>サンプル ラベル付けツールを使ってみる

Azure Form Recognizer は、機械学習モデルを使用してドキュメントからフォーム フィールド、テキスト、テーブルを抽出して分析するクラウドベースの Azure Applied AI Service です。 Form Recognizer を使用して、アプリケーションとワークフローでのデータ処理を自動化し、データドリブン戦略を強化し、ドキュメント検索機能を強化できます。 

Azure Form Recognizer サンプル ラベル付けツールはオープン ソース ツールであり、Azure Form Recognizer と光学式文字認識 (OCR) サービスの最新の機能をテストすることができます。

* [レイアウト API を使用してドキュメントを分析します](#analyze-layout)。 レイアウト API を試して、ドキュメントからテキスト、テーブル、選択マーク、および構造を抽出します。

* [事前構築済みモデルモデルを使用してドキュメントを分析します](#analyze-using-a-prebuilt-model)。 事前構築済みモデルから開始して、請求書、領収書、ID ドキュメント、または名刺からデータを抽出します。

* [カスタム フォームのトレーニングと解析](#train-a-custom-form-model)。 カスタム モデルを使用して、個別のビジネス データおよびユース ケースに固有のドキュメントからデータを抽出します。

## <a name="prerequisites"></a>前提条件

作業を開始するには、次のものが必要です。

* Azure サブスクリプション — [無料で作成する](https://azure.microsoft.com/free/cognitive-services/)ことができます

* Cognitive Services または Form Recognizer リソース。 Azure サブスクリプションを用意できたら、Azure portal で[単一サービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)または[マルチサービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)の Form Recognizer リソースを作成し、キーとエンドポイントを取得します。 Free 価格レベル (<ph id="ph1">`F0`</ph>) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

    > [!TIP]
    > 1 つのエンドポイント/キーで複数の Cognitive Services にアクセスする予定の場合は、Cognitive Services リソースを作成します。 Form Recognizer アクセスのみの場合は、Form Recognizer リソースを作成します。 [Azure Active Directory 認証](../../../active-directory/authentication/overview-authentication.md)を使用する場合は、単一サービス リソースが必要になることに注意してください。

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer リソースを作成する

[!INCLUDE [create resource](../includes/create-resource.md)]

 :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

## <a name="analyze-using-a-prebuilt-model"></a>事前構築済みモデルを使用して分析する

Azure Form Recognizer では、いくつかの事前に構築されたモデルからモデルを選択できます。 各モデルには、サポートされているフィールドの独自のセットがあります。 分析操作に使用するモデルは、分析するドキュメントの種類によって異なります。 Azure Form Recognizer サービスで現在サポートされている事前構築済みモデルを次に示します。

* [**請求書**](../concept-invoice.md): 請求書からテキスト、選択マーク、テーブル、キーと値のペア、キー情報を抽出します。
* [**レシート**](../concept-receipt.md): レシートからテキストとキー情報を抽出します。
* [**身分証明書**](../concept-id-document.md): 運転免許証と国際パスポートからテキストとキー情報を抽出します。
* [**名刺**](../concept-business-card.md): 名刺からテキストとキー情報を抽出します。

1. [Azure Form Recognizer サンプル ツール](https://fott-2-1.azurewebsites.net/)に進みます。

1. サンプル ツールのホーム ページで、 **[Use prebuilt model to get data]\(事前構築済みモデルを使用してデータを取得する\)** を選択します。

    :::image type="content" source="../media/label-tool/prebuilt-1.jpg" alt-text="Form Recognizer レイアウトの結果を解析する":::

1. ドロップダウン ウィンドウから、分析する **[フォームの種類]** を選択します。

1. 次のオプションを使用して、分析するファイルの URL を選択します。

    * [**サンプル請求書ドキュメント**](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf)。
    * [**サンプル ID ドキュメント**](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png)。
    * [**サンプル レシートの画像**](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg)。
    * [**サンプル名刺の画像**](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg)。

1. **[ソース: URL]** フィールドに、選択した URL を貼り付け、 **[フェッチ]** ボタンを選択します。

1. **Azure Form Recognizer サービス エンドポイント** フィールドに、Azure Form Recognizer サブスクリプションで取得したエンドポイントを貼り付けます。

1. **API キー** フィールドに、Azure Form Recognizer リソースから取得したサブスクリプション キーを貼り付けます。

    :::image type="content" source="../media/fott-select-form-type.png" alt-text="スクリーンショット: [フォームの種類] ドロップダウン ウィンドウを選択します。":::

1. **[Run analysis]\(解析の実行\)** を選択します。 Azure Form Recognizer サンプル ラベル付けツールにより、Analyze Prebuilt API を呼び出してドキュメントを解析します。

1. 結果を表示する - 抽出されたキーと値のペア、行項目、抽出された強調表示テキスト、および検出されたテーブルを確認します。

    :::image type="content" source="../media/label-tool/prebuilt-2.jpg" alt-text="Form Recognizer 請求書モデルの分析結果":::

1. JSON 出力ファイルをダウンロードして、詳細なレイアウト結果を表示します。

    * "readResults" ノードには、あらゆるテキスト行が、ページ上の対応する境界ボックスの配置と共に表示されます。
    * "selectionMarks" ノードには、すべての選択マーク (チェック ボックス、ラジオ マーク) と、その状態が「選択済み」と「未選択」のどちらであるかが示されます。
    * 抽出された表は、"pageResults" セクションに含まれています。 それぞれの表について、テキスト、行インデックス、列インデックス、行スパン、列スパン、境界ボックスなどが抽出されます。
    * "documentResults" フィールドには、キーと値のペアの情報と、ドキュメントの最も関連性の高い部分の行項目の情報が含まれます。

## <a name="analyze-layout"></a>レイアウトを分析する

Azure Form Recognizer の Layout API を使用すると、ドキュメント (PDF、TIFF) と画像 (JPG、PNG、BMP) から、テキスト、テーブル、選択マーク、構造情報を抽出できます。

1. [Azure Form Recognizer サンプル ツール](https://fott-2-1.azurewebsites.net/)に進みます。

1. サンプル ツールのホーム ページで、 **[Use Layout to get text, tables and selection marks]\(レイアウトを使用してテキスト、テーブル、選択マークを取得する)** を選択します。

     :::image type="content" source="../media/label-tool/layout-1.jpg" alt-text="Layout Form Recognizer ツールの接続設定。":::

1. **Azure Form Recognizer サービス エンドポイント** フィールドに、Azure Form Recognizer サブスクリプションで取得したエンドポイントを貼り付けます。

1. **API キー** フィールドに、Azure Form Recognizer リソースから取得したサブスクリプション キーを貼り付けます。

1. **[ソース: URL]** フィールドに、次の URL `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/layout-page-001.jpg` を貼り付け、 **[フェッチ]** ボタンを選択します。

1. **[Run Layout]\(レイアウトの実行\)** を選択します。 Azure Form Recognizer サンプル ラベル付けツールにより、Analyze Layout API を呼び出してドキュメントを解析します。

    :::image type="content" source="../media/fott-layout.png" alt-text="スクリーンショット: レイアウトのドロップダウン ウィンドウ。":::

1. 結果を表示する - 抽出された強調表示テキスト、検出された選択マーク、検出されたテーブルを確認します。

    :::image type="content" source="../media/label-tool/layout-3.jpg" alt-text="Form Recognizer ツールの接続設定。":::

1. JSON 出力ファイルをダウンロードして、詳細なレイアウト結果を表示します。
     * `readResults` ノードには、あらゆるテキスト行が、ページ上の対応する境界ボックスの配置と共に表示されます。
     * `selectionMarks` ノードには、すべての選択マーク (チェック ボックス、ラジオ マーク) と、その状態が `selected` と `unselected` のどちらであるかが示されます。
     * 抽出された表は、`pageResults` セクションに含まれています。 それぞれの表について、テキスト、行インデックス、列インデックス、行スパン、列スパン、境界ボックスなどが抽出されます。

## <a name="train-a-custom-form-model"></a>カスタム フォーム モデルをトレーニングする

カスタム モデルをトレーニングし、ビジネスに固有のフォームやドキュメントからデータが分析されて抽出されるようにします。 この API は、特定のコンテンツ内のフォーム フィールドを認識し、キーと値のペアおよびテーブル データを抽出するようにトレーニングされた、機械学習プログラムです。 始めるには、同じフォームの少なくとも 5 種類の例が必要であり、それにより、カスタム モデルをラベル付けされたデータセットあり、またはなしでトレーニングできます。

### <a name="prerequisites-for-training-a-custom-form-model"></a>カスタム フォーム モデルをトレーニングするための前提条件

* トレーニング データのセットを含む Azure Storage Blob コンテナー。 すべてのトレーニング ドキュメントが同じ形式であることを確認します。 複数の形式のフォームがある場合は、共通する形式に基づいてサブフォルダーに分類します。 このプロジェクトでは、[サンプル データ セット](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample_data_without_labels.zip)を使用できます。

* CORS を構成する

    [CORS (クロス オリジン リソース共有)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) に Form Recognizer Studio から アクセスするには、Azure ストレージ アカウントに構成する必要があります。 Azure portal で CORS を構成するには、ストレージ アカウントの [CORS] ブレードにアクセスする必要があります。

    :::image type="content" source="../media/quickstarts/storage-cors-example.png" alt-text="ストレージ アカウントの CORS 構成を示すスクリーンショット。":::

    1. ストレージ アカウントの [CORS] ブレードを選択します。

    1. まず、Blob service に新しい CORS エントリを作成します。

    1. **[許可されたオリジン]** は、 **https://formrecognizer.appliedai.azure.com** に設定します。

    1. **[許可されたメソッド]** で使用可能な 8 つすべてのオプションを選択します。

    1. 各フィールドに * を入力して、 **[許可されたヘッダー]** と **[公開されるヘッダー]** をすべて承認します。

    1. **[Max Age]\(最長有効期間\)** 120 秒、または任意の許容値に設定します。

    1. ページ上部にある [保存] ボタンをクリックして、変更を保存します。

    これで、CORS は、Form Recognizer Studio からストレージ アカウントを使用するように構成されました。

### <a name="use-the-sample-labeling-tool"></a>サンプル ラベル付けツールの使用

1. [Azure Form Recognizer サンプル ツール](https://fott-2-1.azurewebsites.net/)に進みます。

1. サンプル ツールのホームページで、 **[Use custom form to train a model with labels and get key value pairs]\(カスタム フォームを使用してラベルを持つモデルをトレーニングし、キーと値のペアを取得する\)** を選択します。

    :::image type="content" source="../media/label-tool/custom-1.jpg" alt-text="カスタム モデルをトレーニングします。":::

1. **[新しいプロジェクト]** を選択する

    :::image type="content" source="../media/fott-new-project.png" alt-text="スクリーンショット: 新しいプロジェクト プロンプトを選択します。":::

#### <a name="create-a-new-project"></a>新しいプロジェクトを作成する

**[プロジェクト設定]** フィールドを次の値を使用して構成します。

1. **表示名**。 プロジェクトに名前を付けます。

1. **セキュリティ トークン**。 各プロジェクトでは、機密性の高いプロジェクト設定の暗号化または暗号化解除に使用できるセキュリティ トークンが自動生成されます。 セキュリティ トークンは、左側のナビゲーション バーの下部にある歯車アイコンを選択すると、[アプリケーション設定] に表示されます。

1. **ソースの接続**。 サンプル ラベル付けツールは、ソース (元のアップロードされたフォーム) とターゲット (作成されたラベルと出力データ) に接続されます。 接続は、複数のプロジェクトにまたがって設定および共有できます。 拡張可能なプロバイダー モデルが使用されるため、新しいソースまたはターゲット プロバイダーを簡単に追加できます。

    * 新しい接続を作成し、 **[接続の追加]** ボタンを選択します。 次の値を使用してフィールドに入力します。

    > [!div class="checklist"]
    >
    > * **表示名**。 接続に名前を指定します。
    > * **説明**。 簡単な説明を追加します。
    > * **SAS URL**。 Azure Blob Storage コンテナーの Shared Access Signature (SAS) URL を貼り付けます。

    * カスタム モデルのトレーニング データの SAS URL を取得するには、Azure portal のストレージ リソースに移動し、 **[Storage Explorer]** タブを選択します。コンテナーに移動して右クリックし、 **[Get shared access signature]\(Shared Access Signature の取得\)** を選択します。 ストレージ アカウント自体ではなく、コンテナー用の SAS を取得することが重要です。 **[読み取り]** 、 **[書き込み]** 、 **[削除]** 、および **[表示]** 権限がオンになっていることを確認し、 **[作成]** をクリックします。 次に、**URL** セクションの値を一時的な場所にコピーします。 それは次の書式になります`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。

       :::image type="content" source="../media/quickstarts/get-sas-url.png" alt-text="SAS の場所。":::

1. **フォルダー パス** (オプション)。  ソース フォームが BLOB コンテナー内のフォルダー内にある場合は、フォルダー名を指定します。

1. **[Form Recognizer Service Uri]\(Form Recognizer サービスの URI\)** - Form Recognizer のエンドポイント URL。

1. **API キー**。 Azure Form Recognizer のサブスクリプション キー。

1. **API バージョン**。 v2.1 (既定値) のままにします。

1. **[説明]** (省略可能): プロジェクトの説明を入力します。

    :::image type="content" source="../media/label-tool/connections.png"  alt-text="接続の設定":::

#### <a name="label-your-forms"></a>フォームにラベルを付ける

  :::image type="content" source="../media/label-tool/new-project.png"  alt-text="新規プロジェクト ページ":::

プロジェクトを作成するか開くと、メインのタグ エディター ウィンドウが開きます。 このタグ エディターは、次の 3 つの部分で構成されます。

* サイズ変更可能なプレビュー ペイン。基になる接続にあるフォームのスクロール可能な一覧が表示されます。
* メインのエディター ペイン。ここで、タグを適用できます。
* タグ エディター ペイン。ここで、タグの変更、ロック、並べ替え、削除を行うことができます。

##### <a name="identify-text-and-tables"></a>テキストとテーブルを特定する

左側のペインにある **[Run OCR on all files]\(すべてのファイルに対して OCR を実行\)** を選択して、各ドキュメントのテキストとテーブルのレイアウト情報を取得します。 ラベル付けツールによって、各テキスト要素の周囲に境界ボックスが描画されます。

ラベル付けツールでは、どのテーブルが自動的に抽出されたかも示されます。 ドキュメントの左側にあるテーブル (グリッド) アイコンを選択すると、抽出されたテーブルが表示されます。 テーブルの内容は自動的に抽出されるため、テーブルの内容に対するラベル付けは行わず、自動化された抽出を信頼することにします。

  :::image type="content" source="../media/label-tool/table-extraction.png" alt-text="サンプル ラベル付けツールでのテーブルの視覚化。":::

##### <a name="apply-labels-to-text"></a>ラベルをテキストに適用する

次に、タグ (ラベル) を作成し、モデルに解析させるテキスト要素に適用します。 サンプル ラベル データ セットには、既に別のフィールドを追加するラベル付きフィールドが含まれています。

タグ エディター ウィンドウを使用して、識別する新しいタグを作成します。

1. プラス記号 ( **+** ) を選択して、新しいタグを作成します。

1. "Total" というタグを入力します。

1. **Enter** キーを押して、タグを保存します。

1. メインのエディターで、強調表示されたテキスト要素から合計の値を選択します。

1. 値に適用する合計タグを選択するか、対応するキーボード キーを押します。 数字キーは、最初の 10 個のタグのホットキーとして割り当てられます。 タグの順序は、タグ エディター ペインの上矢印と下矢印のアイコンを使用して変更できます。

    > [!Tip]
    > フォームにラベルを付けるときは、次のヒントに留意してください。
    >
    > * 適用できるタグは、選択したテキスト要素ごとに 1 つのみです。
    >
    > * 各タグは、1 ページにつき 1 回のみ適用できます。 同じフォームに同じ値が複数回出現する場合は、インスタンスごとに異なるタグを作成します。 たとえば、"invoice # 1"、"invoice # 2" などとします。
    > * タグは複数のページにまたがることはできません。
    > * フォームに表示されるラベル値は、2 つの異なるタグを使用して 2 つの部分に分割しないでください。 たとえば、アドレス フィールドが複数の行にまたがる場合でも、1 つのタグを使用してラベルを付ける必要があります。
    > * タグが付けられたフィールドには値のみを含めます。キーは含めないでください。
    > * テーブル データは自動的に検出され、'pageResults' セクションの最終的な出力 JSON ファイルに表示されます。 ただし、モデルですべてのテーブル データの検出に失敗した場合は、モデルにラベルを付けテーブルを検出するトレーニングを行うことができます。「[カスタム モデルをトレーニングする | フォームにラベルを付ける](../label-tool.md#label-your-forms)」を参照してください。
    > * **+** の右にあるボタンを使用して、タグの検索、名前の変更、順序変更、削除を行います。
    > * タグそのものは削除せずに、適用されているタグを解除するには、タグ付けされた四角形をドキュメント ビューで選択し、Delete キーを押します。
    >

1. 上記の手順に従って、サンプル データセット内の 5 つのフォームすべてにラベルを付けます。

  :::image type="content" source="../media/label-tool/custom-1.jpg" alt-text="サンプルにラベルを付けます。":::

#### <a name="train-a-custom-model"></a>カスタム モデルをトレーニングする

左側のペインでトレーニング アイコンを選択して、[トレーニング] ページを開きます。 次に、 **[Train]\(トレーニング\)** ボタンを選択して、モデルのトレーニングを開始します。 トレーニング プロセスが完了すると、次の情報が表示されます。

* **[Model ID]** - 作成およびトレーニングされたモデルの ID。 トレーニングの呼び出しごとに、独自の ID を持つ新しいモデルが作成されます。 この文字列を安全な場所にコピーしてください。[REST API](./try-sdk-rest-api.md?pivots=programming-language-rest-api) または[クライアント ライブラリ](./try-sdk-rest-api.md)を使用して予測呼び出しを行う場合に必要になります。

* **[Average Accuracy]\(平均精度\)** - モデルの平均精度。 追加のフォームにラベルを付け、再度トレーニングを行って新しいモデルを作成することにより、モデルの精度を向上させることができます。 最初に、結果を解析してテストする 5 つのフォームにラベルを付け、必要に応じてフォームを追加することをお勧めします。
* タグの一覧と、タグごとの予測精度。

    :::image type="content" source="../media/label-tool/custom-3.jpg" alt-text="トレーニング ビュー ツール。":::

#### <a name="analyze-a-custom-form"></a>カスタムフォームを解析する

1. 左側の **解析** (電球) アイコンを選択して、モデルをテストします。 

1. ソース **[ローカル ファイル]** を選択し、テスト フォルダーに展開したサンプル データセットから選択するファイルを参照します。 

1. 次に、 **[分析を実行する]** ボタンを選択して、フォームのキーと値のペア、テキスト、テーブルの予測を取得します。 このツールでは、境界ボックスにタグが適用され、各タグの信頼度がレポートされます。

   :::image type="content" source="../media/analyze.png" alt-text="トレーニング ビュー。":::

これで完了です。 Azure Form Recognizer の事前構築済み、レイアウト、およびカスタム モデル用の Azure Form Recognizer サンプルツールを使用する方法について学習しました。 また、手動でラベル付けされたデータを使用してカスタム フォームを分析する方法についても学習しました。 これで、Azure Form Recognizer クライアント ライブラリ SDK または REST API を試せます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Form Recognizer のクライアント ライブラリ SDK と REST API クイックスタートを利用する](../quickstarts/get-started-sdk-rest-api.md)