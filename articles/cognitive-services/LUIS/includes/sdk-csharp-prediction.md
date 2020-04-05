---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 3bb0b9fb8b268c3e036ffb365eb8d5b80a241269
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77372128"
---
.NET 用 Language Understanding (LUIS) 予測クライアント ライブラリの用途は次のとおりです。

* スロット別の予測を取得する

[リファレンス ドキュメント](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [予測ランタイム パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [C# サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>前提条件

* Language Understanding (LUIS) ポータル アカウント - [無料で作成できます](https://www.luis.ai)。
* 最新バージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

その他のドキュメントをお探しですか?

 * [SDK のリファレンス ドキュメント](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)

## <a name="setting-up"></a>設定

### <a name="create-an-environment-variable"></a>環境変数を作成する

キーとリソースの名前を使用して、認証用に 2 つの環境変数を作成します。

* `LUIS_PREDICTION_KEY` - 要求を認証するためのリソース キー。
* `LUIS_ENDPOINT_NAME` - キーに関連付けられているリソース名。

ご利用のオペレーティング システムの手順に従ってください。

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_PREDICTION_KEY <replace-with-your-resource-key>
setx LUIS_ENDPOINT_NAME <replace-with-your-resource-name>
```

環境変数を追加したら、コンソール ウィンドウを再起動します。

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source ~/.bashrc` を実行します。

#### <a name="macos"></a>[macOS](#tab/unix)

次のように `.bash_profile` を編集し、環境変数を追加します。

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source .bash_profile` を実行します。

---

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

好みのエディターまたは IDE で、新しい .NET Core アプリケーションを作成します。

1. コンソール ウィンドウ (cmd、PowerShell、Bash など) で、dotnet `new` コマンドを使用し、`language-understanding-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイル (`Program.cs`) を使用する単純な "Hello World" C# プロジェクトが作成されます。

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. 新しく作成されたアプリ フォルダーにディレクトリを変更します。

1. 次を使用してアプリケーションをビルドできます。

    ```dotnetcli
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

### <a name="install-the-sdk"></a>SDK のインストール

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用 Language Understanding (LUIS) 予測ランタイム クライアント ライブラリをインストールします。

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Visual Studio IDE を使用している場合、クライアント ライブラリは、ダウンロード可能な NuGet パッケージとして入手できます。

## <a name="object-model"></a>オブジェクト モデル

Language Understanding (LUIS) 予測ランタイム クライアントは、認証を経てリソース キーの保管場所である Azure にアクセスする [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) オブジェクトです。

クライアントを作成したら、このクライアントを使用して次のような機能にアクセスします。

* [ステージングまたは製品スロット](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)別の予測
* [バージョン](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)別の予測


## <a name="code-examples"></a>コード例

以下のコード スニペットは、.NET 用 Language Understanding (LUIS) 予測ランタイム クライアント ライブラリを使用して次のことを実行する方法を示したものです。

* [スロット別の予測](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>依存関係を追加する

プロジェクト ディレクトリから、好みのエディターまたは IDE で *Program.cs* ファイルを開きます。 既存の `using` コードを次の `using` ディレクティブに置き換えます。

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>クライアントを認証する

1. キー、名前、およびアプリ ID の変数を作成します。

    `LUIS_PREDICTION_KEY` という名前の環境変数から取得した予測キーを管理するための変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、再読み込みしなければならない場合があります。 メソッドは後で作成します。

    リソース名 `LUIS_ENDPOINT_NAME` を保持する変数を作成します。

    `LUIS_APP_ID` という名前の環境変数として、アプリ ID の変数を作成します。 この環境変数をパブリック IoT アプリに設定します。

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. 自分のキーを指定して [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) オブジェクトを作成し、それを自分のエンドポイントと共に使用して [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) オブジェクトを作成します。

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>ランタイムから予測を取得する

次のメソッドを追加して、予測ランタイムへの要求を作成します。

ユーザーの発話は、[PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet) オブジェクトの一部です。

**GetSlotPredictionAsync** メソッドには、アプリ ID、スロット名、要求を満たす予測要求オブジェクトなど、いくつかのパラメーターが必要です。 詳細、すべての意図の表示、ログなど、その他のオプションは省略可能です。

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>予測の Main コード

次の Main メソッドを使用して変数とメソッドを結び付け、予測を取得します。

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>アプリケーションの実行

自分のアプリケーション ディレクトリで `dotnet run` コマンドを使用してアプリケーションを実行します。

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

予測が終了したら、program.cs ファイルとそのサブディレクトリを削除して、このクイックスタートの作業をクリーンアップします。
