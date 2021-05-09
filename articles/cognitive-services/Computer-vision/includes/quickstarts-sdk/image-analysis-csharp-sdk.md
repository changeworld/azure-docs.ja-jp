---
title: 'クイックスタート: .NET 用画像分析クライアント ライブラリ'
description: このクイックスタートでは、.NET 用画像分析クライアント ライブラリの使用を開始します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 03/29/2021
ms.author: pafarley
ms.openlocfilehash: 407ef167ca05f08d349a017c60164e2fe67977a6
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327417"
---
<a name="HOLTop"></a>

画像分析クライアント ライブラリを使用して、画像のタグ、テキスト説明、顔、成人向けコンテンツなどを分析します。

[リファレンスのドキュメント](/dotnet/api/overview/azure/cognitiveservices/client/computervision) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [サンプル](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) または現在のバージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision リソースを作成"  target="_blank">Computer Vision リソースを作成</a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを Computer Vision サービスに接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Visual Studio を使用して、新しい .NET Core アプリケーションを作成します。 

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする 

新しいプロジェクトを作成したら、**ソリューション エクスプローラー** でプロジェクト ソリューションを右クリックし、 **[NuGet パッケージの管理]** を選択して、クライアント ライブラリをインストールします。 パッケージ マネージャーが開いたら、 **[参照]** を選択し、 **[プレリリースを含める]** をオンにして、`Microsoft.Azure.CognitiveServices.Vision.ComputerVision` を検索します。 バージョン `7.0.0` を選択し、 **[インストール]** を選択します。 

#### <a name="cli"></a>[CLI](#tab/cli)

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`computer-vision-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイルを使用する単純な "Hello World" C# プロジェクトが作成されます。*Program.cs*。

```console
dotnet new console -n computer-vision-quickstart
```

新しく作成されたアプリ フォルダーにディレクトリを変更します。 次を使用してアプリケーションをビルドできます。

```console
dotnet build
```

ビルドの出力に警告やエラーが含まれないようにする必要があります。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用 Computer Vision クライアント ライブラリをインストールします。

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 7.0.0
```

---

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) にあり、このクイックスタートのコード例が含まれています。

プロジェクト ディレクトリから、好みのエディターまたは IDE で *ComputerVisionQuickstart.cs* ファイルを開きます。 次の `using` ディレクティブを追加します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using)]

アプリケーションの **Program** クラスで、対象のリソースの Azure エンドポイントおよびキー用の変数を作成します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_vars)]

> [!IMPORTANT]
> Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Computer Vision リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 キーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 詳細については、Cognitive Services の[セキュリティ](../../../cognitive-services-security.md)に関するページを参照してください。

アプリケーションの `Main` メソッドで、このクイックスタートで使用するメソッドの呼び出しを追加します。 これらは後で作成します。


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyzeinmain)]

> [!div class="nextstepaction"]
> [クライアントを設定しました](?success=set-up-client#object-model) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>オブジェクト モデル

以下のクラスとインターフェイスにより、画像分析 .NET SDK の主要な機能の一部が処理されます。

|名前|説明|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | このクラスは、すべての Computer Vision の機能に必要です。 サブスクリプション情報を使用してインスタンス化し、そのインスタンスを使用して、画像に対するほとんどの操作を実行することができます。|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| このクラスには、**ComputerVisionClient** の追加的なメソッドが含まれています。|
|[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| この列挙型は、標準の分析操作で実行できるさまざまな種類の画像分析を定義します。 必要に応じて、VisualFeatureTypes の値のセットを指定します。 |

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、.NET 用画像分析クライアント ライブラリを使用して以下のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [イメージを分析する](#analyze-an-image)

## <a name="authenticate-the-client"></a>クライアントを認証する

> [!NOTE]
> このクイックスタートでは、`COMPUTER_VISION_SUBSCRIPTION_KEY` および `COMPUTER_VISION_ENDPOINT` という名前の、Computer Vision キーとエンドポイントのそれぞれの [環境変数を作成](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) してあることを前提としています。

**Program** クラスの新しいメソッドで、実際のエンドポイントとキーを使用してクライアントをインスタンス化します。 自分のキーを指定して **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** オブジェクトを作成し、それを自分のエンドポイントと共に使用して **[ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)** オブジェクトを作成します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [クライアントを認証しました](?success=authenticate-client#analyze-an-image) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="analyze-an-image"></a>イメージを分析する

次のコードでは、`AnalyzeImageUrl` メソッドを定義しています。このメソッドは、クライアント オブジェクトを使用して、リモート画像を分析し、結果を出力します。 このメソッドでは、テキストの説明、分類、タグの一覧、検出された顔、成人向けコンテンツ フラグ、メインの色、および画像の種類が返されます。

> [!TIP]
> ローカルの画像を分析することもできます。 [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) のメソッドを参照してください (**AnalyzeImageInStreamAsync** など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) 上のサンプル コードを参照してください。

### <a name="set-up-test-image"></a>テスト画像の設定

**Program** クラスで、分析する画像の URL への参照を保存します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_url)]

### <a name="specify-visual-features"></a>視覚的特徴を指定する

画像分析用の新しいメソッドを定義します。 以下のコードは、分析で抽出する視覚的特徴を指定するものです。このコードを追加します。 完全な一覧については、 **[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)** 列挙型を参照してください。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_visualfeatures)]

次のいずれかのコード ブロックを **AnalyzeImageUrl** メソッドに挿入して、その機能を実装します。 末尾に右角かっこを追加することを忘れないでください。

```csharp
}
```

### <a name="analyze"></a>分析

**AnalyzeImageAsync** メソッドは、抽出されたすべての情報を含む **ImageAnalysis** オブジェクトを返します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_call)]

以降のセクションでは、この情報の解析方法について詳しく説明します。

### <a name="get-image-description"></a>画像の説明の取得

次のコードは、画像に対して生成されたキャプションの一覧を取得します。 詳細については、[画像の説明](../../concept-describing-images.md)に関するページを参照してください。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_describe)]

### <a name="get-image-category"></a>画像カテゴリの取得

次のコードは、画像の検出されたカテゴリを取得します。 詳細については、[画像の分類](../../concept-categorizing-images.md)に関するページを参照してください。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>画像タグの取得

次のコードは、画像内の検出されたタグのセットを取得します。 詳細については、[コンテンツ タグ](../../concept-tagging-images.md)に関するページを参照してください。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_tags)]

### <a name="detect-objects"></a>物体を検出する

次のコードは、画像から身の回りの物を検出してコンソールに出力します。 詳細については、[物体の検出](../../concept-object-detection.md)に関するページを参照してください。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_objects)]

### <a name="detect-brands"></a>ブランドを検出する

次のコードは、企業のブランドとロゴを画像から検出してコンソールに出力します。 詳細については、[ブランドの検出](../../concept-brand-detection.md)に関するページを参照してください。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_brands)]

### <a name="detect-faces"></a>顔を検出する

次のコードは、画像内で検出された顔を、その四角形の座標と顔選択属性と共に返します。 詳細については、[顔の検出](../../concept-detecting-faces.md)に関するページを参照してください。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>成人向け、わいせつ、または不快なコンテンツを検出する

次のコードは、画像内で検出された成人向けのコンテンツの存在を出力します。 詳細については、「[成人向けコンテンツ、わいせつなコンテンツ、不快なコンテンツ](../../concept-detecting-adult-content.md)」を参照してください。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>画像の配色の取得

次のコードは、ドミナント カラー、アクセント カラーなど、画像内で検出された色の属性を出力します。 詳細については、[配色](../../concept-detecting-color-schemes.md)に関するページを参照してください。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>ドメイン固有のコンテンツの取得

画像分析では、特殊なモデルを使用して、画像をさらに分析できます。 詳細については、[ドメイン固有のコンテンツ](../../concept-detecting-domain-content.md)に関するページを参照してください。 

次のコードは、画像内の検出された有名人に関するデータを解析します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_celebs)]

次のコードは、画像内の検出されたランドマークに関するデータを解析します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>画像の種類を取得する

次のコードは、画像の種類 (クリップ アートであるか線画であるか) に関する情報を出力します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_type)]

> [!div class="nextstepaction"]
> [画像を分析しました](?success=analyze-image#run-the-application) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=analyze-image)



## <a name="run-the-application"></a>アプリケーションの実行

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

IDE ウィンドウの上部にある **[デバッグ]** ボタンをクリックして、アプリケーションを実行します。

#### <a name="cli"></a>[CLI](#tab/cli)

アプリケーション ディレクトリから `dotnet run` コマンドを使用してアプリケーションを実行します。

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、画像分析クライアント ライブラリをインストールし、基本的な画像分析呼び出しを行う方法について学習しました。 次は、Analyze API の機能の詳細について学習しましょう。


> [!div class="nextstepaction"]
>[Analyze API を呼び出す](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [画像分析の概要](../../overview-image-analysis.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) にあります。
