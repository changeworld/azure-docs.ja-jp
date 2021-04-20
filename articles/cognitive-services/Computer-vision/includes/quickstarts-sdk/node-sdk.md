---
title: 'クイックスタート: Node.js 用の光学式文字認識クライアント ライブラリ'
description: このクイックスタートでは、Node.js 用の光学式文字認識クライアント ライブラリの基本的な使い方について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: cb4679152740b73d6bb9cf7288fcaa811b6d6141
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073500"
---
<a name="HOLTop"></a>

Read API を使用して、画像内の印刷されたテキストや手書きのテキストを読み取ります。

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

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) にあり、このクイックスタートのコード例が含まれています。

*index.js* という新しいファイルを作成し、テキスト エディターで開きます。

### <a name="find-the-subscription-key-and-endpoint"></a>サブスクリプション キーとエンドポイントを見つける

Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Computer Vision リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 サブスクリプション キーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 

Computer Vision のサブスクリプション キーとエンドポイントの変数を作成します。 サブスクリプション キーとエンドポイントを、次のコード内に示されている場所に貼り付けます。 Computer Vision エンドポイントの形式は `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` です。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> 終わったらコードからサブスクリプション キーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 たとえば、[Azure Key Vault](../../../../key-vault/general/overview.md) が考えられます。

> [!div class="nextstepaction"]
> [クライアントを設定しました](?success=set-up-client#object-model) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client)

## <a name="object-model"></a>オブジェクト モデル

以下のクラスとインターフェイスにより、OCR Node.js SDK の主要な機能の一部が処理されます。

|名前|説明|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | このクラスは、すべての Computer Vision の機能に必要です。 サブスクリプション情報を使用してインスタンス化し、そのインスタンスを使用して、画像に対するほとんどの操作を実行することができます。|

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Node.js 用 OCR クライアント ライブラリを使用して以下のタスクを実行する方法が示されています。

* [クライアントを認証する](#authenticate-the-client)
* [印刷されたテキストと手書きのテキストを読み取る](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>クライアントを認証する


ご利用のエンドポイントとキーを使用してクライアントをインスタンス化します。 キーとエンドポイントを使用して [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials) オブジェクトを作成し、それを使用して [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) オブジェクトを作成します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

次に、関数 `computerVision` を定義し、プライマリ関数とコールバック関数を使用して非同期の series を宣言します。 主要な関数にクイックスタート コードを追加し、スクリプトの一番下にある `computerVision` を呼び出します。 このクイックスタートの残りのコードは、`computerVision` 関数に属します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> [クライアントを認証しました](?success=authenticate-client#read-printed-and-handwritten-text) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>印刷されたテキストと手書きのテキストを読み取る

OCR サービスでは、画像に映っているテキストを抽出し、文字ストリームに変換することができます。 このサンプルでは、Read 操作を使用します。

### <a name="set-up-test-images"></a>テスト画像を設定する

テキストを抽出する画像の URL の参照を保存します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

> [!NOTE]
> また、ローカルの画像からテキストを読み取ることもできます。 [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) のメソッドを参照してください (**readInStream** など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) 上のサンプル コードを参照してください。

### <a name="call-the-read-api"></a>Read API を呼び出す

Read 呼び出しの状態値を表す次のフィールドを関数内に定義します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_statuses)]

次のコードを追加します。これによって、指定された画像の `readTextFromURL` 関数が呼び出されます。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

`readTextFromURL` 関数を定義します。 これにより、クライアント オブジェクトの **read** メソッドが呼び出され、操作 ID が返され、画像の内容を読み取る非同期プロセスが開始されます。 次に、結果が返されるまで、操作 ID を使用して操作の状態が確認されます。 その後、抽出された結果が返されます。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

次に、ヘルパー関数 `printRecText` を定義します。これにより、Read 操作の結果がコンソールに出力されます。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

> [!div class="nextstepaction"]
> [テキストを読み取りました](?success=read-printed-handwritten-text#run-the-application) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=read-printed-handwritten-text)

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

このクイックスタートでは、OCR クライアント ライブラリをインストールし、Read API を使用する方法について学習しました。 次は、Read API の機能の詳細について学習しましょう。

> [!div class="nextstepaction"]
>[Read API を呼び出す](../../Vision-API-How-to-Topics/call-read-api.md)

* [OCR の概要](../../overview-ocr.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) にあります。
