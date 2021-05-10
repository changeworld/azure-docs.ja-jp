---
title: 'クイックスタート: Java 用の光学式文字認識クライアント ライブラリ'
description: このクイックスタートでは、Java 用の光学式文字認識クライアント ライブラリの基本的な使い方について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: c1322eac7fd5ec1ead09a7b0197db931f96f8d48
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073541"
---
<a name="HOLTop"></a>

光学式文字認識クライアント ライブラリを使用して、画像内の印刷されたテキストや手書きのテキストを読み取ります。

[リファレンス ドキュメント](/java/api/overview/azure/cognitiveservices/client/computervision) | [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-computervision) |[成果物 (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision) | [サンプル](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* 最新バージョンの [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK)
* [Gradle ビルド ツール](https://gradle.org/install/)、または別の依存関係マネージャー。
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision リソースを作成"  target="_blank">Computer Vision リソースを作成</a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを Computer Vision サービスに接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-gradle-project"></a>新しい Gradle プロジェクトを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```

作業ディレクトリから `gradle init` コマンドを実行します。 次のコマンドを実行すると、*build.gradle.kts* を含む、Gradle 用の重要なビルド ファイルが作成されます。これは、アプリケーションを作成して構成するために、実行時に使用されます。

```console
gradle init --type basic
```

**DSL** を選択するよう求められたら、**Kotlin** を選択します。

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

このクイックスタートでは、Gradle 依存関係マネージャーを使用します。 クライアント ライブラリとその他の依存関係マネージャーの情報については、[Maven Central Repository](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision) を参照してください。

*build.gradle.kts* を検索し、任意の IDE またはテキスト エディターで開きます。 その後、次のビルド構成をコピーします。 この構成では、エントリ ポイントが **ComputerVisionQuickstarts** クラスである Java アプリケーションとしてプロジェクトを定義します。 これによって、Computer Vision ライブラリがインポートされます。

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.4-beta")
}
```

### <a name="create-a-java-file"></a>Java ファイルを作成する

作業ディレクトリから次のコマンドを実行し、プロジェクト ソース フォルダーを作成します。

```console
mkdir -p src/main/java
```

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) にあり、このクイックスタートのコード例が含まれています。

新しいフォルダーに移動し、*ComputerVisionQuickstarts.java* という名前のファイルを作成します。 好みのエディターまたは IDE で開きます。

### <a name="find-the-subscription-key-and-endpoint"></a>サブスクリプション キーとエンドポイントを見つける

Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Computer Vision リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 サブスクリプション キーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 

**ComputerVisionQuickstarts** クラスを定義します。 Computer Vision のサブスクリプション キーとエンドポイントの変数を作成します。 サブスクリプション キーとエンドポイントを、次のコード内に示されている場所に貼り付けます。 Computer Vision エンドポイントの形式は `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` です。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> 終わったらコードからサブスクリプション キーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 たとえば、[Azure Key Vault](../../../../key-vault/general/overview.md) が考えられます。

アプリケーションの **main** メソッドで、このクイックスタートで使用するメソッドの呼び出しを追加します。 その定義は後から行います。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_beginmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_authinmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_readinmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_endmain)]
> [!div class="nextstepaction"]
> [クライアントを設定しました](?success=set-up-client#object-model) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=set-up-client)

## <a name="object-model"></a>オブジェクト モデル

以下のクラスとインターフェイスにより、OCR Java SDK の主要な機能の一部が処理されます。

|名前|説明|
|---|---|
| [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | このクラスは、すべての Computer Vision の機能に必要です。 サブスクリプション情報を使用してこれをインスタンス化し、他のクラスのインスタンスを生成するために使用します。|

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Java 用 OCR クライアント ライブラリを使用して以下のタスクを実行する方法が示されています。

* [クライアントを認証する](#authenticate-the-client)
* [印刷されたテキストと手書きのテキストを読み取る](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>クライアントを認証する

新しいメソッドで、実際のエンドポイントとキーを使用して [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) オブジェクトをインスタンス化します。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_auth)]

> [!div class="nextstepaction"]
> [クライアントを認証しました](?success=authenticate-client#read-printed-and-handwritten-text) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>印刷されたテキストと手書きのテキストを読み取る

OCR サービスは、画像に映っているテキストを読み取って、文字ストリームに変換することができます。 このセクションでは、ローカル ファイル パスを受け取って画像のテキストをコンソールに出力するメソッド `ReadFromFile` を定義します。

> [!TIP]
> また、URL によって参照されたリモート画像内のテキストを読み取ることもできます。 [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision) のメソッドを参照してください (**read** など)。 また、リモート画像を含むシナリオについては、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) 上のサンプル コードを参照してください。

### <a name="set-up-test-image"></a>テスト画像の設定

プロジェクトの **src/main/** フォルダーに **resources/** フォルダーを作成し、テキストを読み取る画像を追加します。 使用する[サンプル画像](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg)は、ここからダウンロードできます。

次に、**ComputerVisionQuickstarts** クラスに次のメソッド定義を追加します。 `localFilePath` の値を画像ファイルと一致するように変更します。 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_setup)]

### <a name="call-the-read-api"></a>Read API を呼び出す

次に、指定された画像に対して **readInStreamWithServiceResponseAsync** メソッドを呼び出すために、次のコードを追加します。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

次のコード ブロックでは、Read 呼び出しの応答から操作 ID を抽出します。 この ID をヘルパー メソッドと共に使用して、テキストの読み取り結果をコンソールに出力します。 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_response)]

try/catch ブロックとメソッドの定義を閉じます。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

### <a name="get-read-results"></a>読み取りの結果を取得する

次に、ヘルパー メソッドの定義を追加します。 このメソッドでは、前の手順の操作 ID を使用して読み取り操作を照会し、OCR の結果を取得できる場合はそれを取得します。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_call)]

メソッドの残りの部分では、OCR 結果を解析してコンソールに出力します。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_print)]

最後に、上記で使用したヘルパー メソッドをもう 1 つ追加して、初期応答から操作 ID を抽出します。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_opid_extract)]

> [!div class="nextstepaction"]
> [テキストを読み取りました](?success=read-printed-handwritten-text#run-the-application) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>アプリケーションの実行

次を使用してアプリをビルドできます。

```console
gradle build
```

`gradle run` コマンドを使用してアプリケーションを実行します。

```console
gradle run
```

> [!div class="nextstepaction"]
> [アプリケーションを実行しました](?success=run-the-application#clean-up-resources) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=run-the-application)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [リソースをクリーンアップしました](?success=clean-up-resources#next-steps) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=clean-up-resources)

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、OCR クライアント ライブラリをインストールし、Read API を使用する方法について学習しました。 次は、Read API の機能の詳細について学習しましょう。

> [!div class="nextstepaction"]
>[Read API を呼び出す](../../Vision-API-How-to-Topics/call-read-api.md)

* [OCR の概要](../../overview-ocr.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) にあります。
