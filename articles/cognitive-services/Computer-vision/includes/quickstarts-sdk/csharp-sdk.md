---
title: 'クイックスタート: .NET 用の光学式文字認識クライアント ライブラリ'
description: このクイックスタートでは、.NET 用の光学式文字認識クライアント ライブラリの基本的な使い方について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 538b3ce5a268464b9f014dd00b924875824cab3b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107326979"
---
<a name="HOLTop"></a>

OCR クライアント ライブラリを使用して、画像内の印刷されたテキストや手書きのテキストを読み取ります。

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

プロジェクト ディレクトリから、好みのエディターまたは IDE で *Program.cs* ファイルを開きます。

### <a name="find-the-subscription-key-and-endpoint"></a>サブスクリプション キーとエンドポイントを見つける

Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Computer Vision リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 サブスクリプション キーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 

アプリケーションの **Program** クラスで、Computer Vision のサブスクリプション キーとエンドポイントの変数を作成します。 サブスクリプション キーとエンドポイントを、次のコード内に示されている場所に貼り付けます。 Computer Vision エンドポイントの形式は `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` です。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using_and_vars)]

> [!IMPORTANT]
> 終わったらコードからサブスクリプション キーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 たとえば、[Azure Key Vault](../../../../key-vault/general/overview.md) が考えられます。

アプリケーションの `Main` メソッドで、このクイックスタートで使用するメソッドの呼び出しを追加します。 これらは後で作成します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

> [!div class="nextstepaction"]
> [クライアントを設定しました](?success=set-up-client#object-model) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>オブジェクト モデル

以下のクラスとインターフェイスにより、OCR .NET SDK の主要な機能の一部が処理されます。

|名前|説明|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | このクラスは、すべての Computer Vision の機能に必要です。 サブスクリプション情報を使用してインスタンス化し、そのインスタンスを使用して、画像に対するほとんどの操作を実行することができます。|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| このクラスには、**ComputerVisionClient** の追加的なメソッドが含まれています。|

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、.NET 用 OCR クライアント ライブラリを使用して以下のタスクを実行する方法が示されています。

* [クライアントを認証する](#authenticate-the-client)
* [印刷されたテキストと手書きのテキストを読み取る](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>クライアントを認証する

**Program** クラスの新しいメソッドで、実際のエンドポイントとサブスクリプション キーを使用してクライアントをインスタンス化します。 自分のサブスクリプション キーを指定して **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** オブジェクトを作成し、それを自分のエンドポイントと共に使用して **[ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)** オブジェクトを作成します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [クライアントを認証しました](?success=authenticate-client#read-printed-and-handwritten-text) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>印刷されたテキストと手書きのテキストを読み取る

OCR サービスは、画像に映っているテキストを読み取って、文字ストリームに変換することができます。 テキスト認識の詳細については、[光学式文字認識 (OCR)](../../overview-ocr.md) の概要を参照してください。 このセクションのコードでは、最新の [Read 3.0 用 Computer Vision SDK リリース](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)を使用し、クライアント オブジェクトを使用して画像内のテキストを検出して抽出する `BatchReadFileUrl` メソッドを定義しています。

> [!TIP]
> ローカルの画像からテキストを抽出することもできます。 [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) のメソッドを参照してください (**ReadInStreamAsync** など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) 上のサンプル コードを参照してください。

### <a name="set-up-test-image"></a>テスト画像の設定

**Program** クラスで、テキストの抽出元となる画像の URL への参照を保存します。 このスニペットには、印刷テキストと手書きテキストの両方のサンプル画像が含まれています。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>Read API を呼び出す

テキストを読み取るための新しいメソッドを定義します。 以下のコードは、指定された画像に対して **ReadAsync** メソッドを呼び出すものです。このコードを追加します。 これによって操作 ID が返され、画像の内容を読み取る非同期プロセスが開始されます。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_1)]

### <a name="get-read-results"></a>読み取りの結果を取得する

次に、**ReadAsync** の呼び出しから返された操作 ID を取得し、これを使用して操作の結果をサービスに照会します。 次のコードは、結果が返されるまで操作をチェックします。 次に、抽出されたテキスト データをコンソールに出力します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_2)]

### <a name="display-read-results"></a>読み取りの結果を表示する

取得したテキスト データを解析して表示する次のコードを追加すれば、メソッドの定義は完了です。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_3)]

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

このクイックスタートでは、OCR クライアント ライブラリをインストールし、Read API を使用する方法について学習しました。 次は、Read API の機能の詳細について学習しましょう。

> [!div class="nextstepaction"]
>[Read API を呼び出す](../../Vision-API-How-to-Topics/call-read-api.md)

* [OCR の概要](../../overview-ocr.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) にあります。
