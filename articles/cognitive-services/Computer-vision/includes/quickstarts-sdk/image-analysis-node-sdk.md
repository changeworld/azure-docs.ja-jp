---
title: 'クイックスタート: Node.js 用画像分析クライアント ライブラリ'
description: このクイックスタートでは、Node.js 用画像分析クライアント ライブラリの使用を開始します
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: d38b1f20547056c30f19dbbc77589643be1663a4
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073614"
---
<a name="HOLTop"></a>

画像分析クライアント ライブラリを使用して、画像のタグ、テキスト説明、顔、成人向けコンテンツなどを分析します。

[リファレンスのドキュメント](/javascript/api/@azure/cognitiveservices-computervision/) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [パッケージ (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [サンプル](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* 最新バージョンの [Node.js](https://nodejs.org/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision リソースを作成"  target="_blank">Computer Vision リソースを作成</a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを Computer Vision サービスに接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。

```console
mkdir myapp && cd myapp
```

`npm init` コマンドを実行し、`package.json` ファイルを使用して node アプリケーションを作成します。

```console
npm init
```

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

`ms-rest-azure` および `@azure/cognitiveservices-computervision` NPM パッケージをインストールします。

```console
npm install @azure/cognitiveservices-computervision
```

また、async モジュールもインストールします。

```console
npm install async
```

アプリの `package.json` ファイルが依存関係によって更新されます。

*index.js* という新しいファイルを作成し、テキスト エディターで開きます。 次の import ステートメントを追加します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) にあり、このクイックスタートのコード例が含まれています。

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

> [!IMPORTANT]
> Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Computer Vision リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 キーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 詳細については、Cognitive Services の[セキュリティ](../../../cognitive-services-security.md)に関するページを参照してください。

> [!div class="nextstepaction"]
> [クライアントを設定しました](?success=set-up-client#object-model) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client)

## <a name="object-model"></a>オブジェクト モデル

以下のクラスとインターフェイスにより、画像分析 Node.js SDK の主要な機能の一部が処理されます。

|名前|説明|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | このクラスは、すべての Computer Vision の機能に必要です。 サブスクリプション情報を使用してインスタンス化し、そのインスタンスを使用して、画像に対するほとんどの操作を実行することができます。|
|[VisualFeatureTypes](/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes)| この列挙型は、標準の分析操作で実行できるさまざまな種類の画像分析を定義します。 必要に応じて、**VisualFeatureTypes** の値のセットを指定します。 |

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Node.js 用画像分析クライアント ライブラリを使用して以下のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [イメージを分析する](#analyze-an-image)

## <a name="authenticate-the-client"></a>クライアントを認証する


ご利用のエンドポイントとキーを使用してクライアントをインスタンス化します。 キーとエンドポイントを使用して [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials) オブジェクトを作成し、それを使用して [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) オブジェクトを作成します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

次に、関数 `computerVision` を定義し、プライマリ関数とコールバック関数を使用して非同期の series を宣言します。 主要な関数にクイックスタート コードを追加し、スクリプトの一番下にある `computerVision` を呼び出します。 このクイックスタートの残りのコードは、`computerVision` 関数に属します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> [クライアントを認証しました](?success=authenticate-client#analyze-an-image) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client)

## <a name="analyze-an-image"></a>イメージを分析する

このセクションのコードでは、リモートの画像を分析して、さまざまなビジュアル機能を抽出します。 これらの操作は、クライアント オブジェクトの **analyzeImage** メソッドの一部として実行するか、個別のメソッドを使用して呼び出すことができます。 詳細については、[リファレンス ドキュメント](/javascript/api/@azure/cognitiveservices-computervision/)を参照してください。

> [!NOTE]
> ローカルの画像を分析することもできます。 [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) のメソッドを参照してください (**analyzeImageInStream** など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) 上のサンプル コードを参照してください。

### <a name="get-image-description"></a>画像の説明の取得

次のコードは、画像に対して生成されたキャプションの一覧を取得します。 詳細については、[画像の説明](../../concept-describing-images.md)に関するページを参照してください。

まず分析する画像の URL を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

次に画像の説明を取得してコンソールに出力する次のコードを追加します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>画像カテゴリの取得

次のコードは、画像の検出されたカテゴリを取得します。 詳細については、[画像の分類](../../concept-categorizing-images.md)に関するページを参照してください。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

ヘルパー関数 `formatCategories` を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>画像タグの取得

次のコードは、画像内の検出されたタグのセットを取得します。 詳細については、[コンテンツ タグ](../../concept-tagging-images.md)に関するページを参照してください。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

ヘルパー関数 `formatTags` を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>物体を検出する

次のコードは、画像から身の回りの物を検出してコンソールに出力します。 詳細については、[物体の検出](../../concept-object-detection.md)に関するページを参照してください。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

上、左、下、および右の座標と、幅および高さを返すように、ヘルパー関数 `formatRectObjects` を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>ブランドを検出する

次のコードは、企業のブランドとロゴを画像から検出してコンソールに出力します。 詳細については、[ブランドの検出](../../concept-brand-detection.md)に関するページを参照してください。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>顔を検出する

次のコードは、画像内で検出された顔を、その四角形の座標と顔選択属性と共に返します。 詳細については、[顔の検出](../../concept-detecting-faces.md)に関するページを参照してください。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

ヘルパー関数 `formatRectFaces` を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>成人向け、わいせつ、または不快なコンテンツを検出する

次のコードは、画像内で検出された成人向けのコンテンツの存在を出力します。 詳細については、「[成人向けコンテンツ、わいせつなコンテンツ、不快なコンテンツ](../../concept-detecting-adult-content.md)」を参照してください。

使用する画像の URL を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

次に、アダルト コンテンツを検出し、結果をコンソールに出力する次のコードを追加します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>画像の配色の取得

次のコードは、ドミナント カラー、アクセント カラーなど、画像内で検出された色の属性を出力します。 詳細については、[配色](../../concept-detecting-color-schemes.md)に関するページを参照してください。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

コンソールに配色の詳細を出力するヘルパー関数 `printColorScheme` を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>ドメイン固有のコンテンツの取得

画像分析では、特殊なモデルを使用して、画像をさらに分析できます。 詳細については、[ドメイン固有のコンテンツ](../../concept-detecting-domain-content.md)に関するページを参照してください。

まず分析する画像の URL を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

次のコードは、画像内の検出されたランドマークに関するデータを解析します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

検出されたランドマークに関する位置データを解析するヘルパー関数 `formatRectDomain` を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>画像の種類を取得する

次のコードは、画像の種類 (クリップ アートであるか線画であるか) に関する情報を出力します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

ヘルパー関数 `describeType` を定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

> [!div class="nextstepaction"]
> [画像を分析しました](?success=analyze-image#run-the-application) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=analyze-image)



## <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `node` コマンドを使用して、アプリケーションを実行します。

```console
node index.js
```

> [!div class="nextstepaction"]
> [アプリケーションを実行しました](?success=run-the-application#clean-up-resources) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=run-the-application)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [リソースをクリーンアップしました](?success=clean-up-resources#next-steps) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=clean-up-resources)

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、画像分析クライアント ライブラリをインストールし、基本的な画像分析呼び出しを行う方法について学習しました。 次は、Analyze API の機能の詳細について学習しましょう。

> [!div class="nextstepaction"]
>[Analyze API を呼び出す](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [画像分析の概要](../../overview-image-analysis.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) にあります。

