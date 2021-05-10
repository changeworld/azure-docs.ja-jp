---
title: 'クイックスタート: Python 用の光学式文字認識クライアント ライブラリ'
description: このクイックスタートでは、Python 用の光学式文字認識クライアント ライブラリの基本的な使い方について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 230934ea3d4a8a970ca1fca46160b96e3f2234e8
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073364"
---
<a name="HOLTop"></a>

Read API を使用して、画像内の印刷されたテキストや手書きのテキストを読み取ります。

[リファレンス ドキュメント](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision) | [パッケージ (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | [サンプル](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * Python のインストールには、[pip](https://pip.pypa.io/en/stable/) が含まれている必要があります。 pip がインストールされているかどうかを確認するには、コマンド ラインで `pip --version` を実行します。 最新バージョンの Python をインストールして pip を入手してください。
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision リソースを作成"  target="_blank">Computer Vision リソースを作成</a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。

    * 対象のアプリケーションを Computer Vision サービスに接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

次のようにして、クライアント ライブラリをインストールできます。

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

また、Pillow ライブラリもインストールします。

```console
pip install pillow
```

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) にあり、このクイックスタートのコード例が含まれています。

新しい Python ファイル (たとえば *quickstart-file.py*) を作成します。 次に、お好きなエディターまたは IDE で開きます。

### <a name="find-the-subscription-key-and-endpoint"></a>サブスクリプション キーとエンドポイントを見つける

Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Computer Vision リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 サブスクリプション キーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 

Computer Vision のサブスクリプション キーとエンドポイントの変数を作成します。 サブスクリプション キーとエンドポイントを、次のコード内に示されている場所に貼り付けます。 Computer Vision エンドポイントの形式は `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` です。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> 終わったらコードからサブスクリプション キーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 たとえば、[Azure Key Vault](../../../../key-vault/general/overview.md) が考えられます。

> [!div class="nextstepaction"]
> [クライアントを設定しました](?success=set-up-client#object-model) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=set-up-client)

## <a name="object-model"></a>オブジェクト モデル

以下のクラスとインターフェイスにより、OCR Python SDK の主要な機能の一部が処理されます。

|名前|説明|
|---|---|
|[ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)| このクラスは、画像分析、テキスト検出、サムネイル生成など、すべての画像操作を直接処理します。|
| [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) | このクラスは、すべての Computer Vision の機能に必要です。 サブスクリプション情報を使用してこれをインスタンス化し、他のクラスのインスタンスを生成するために使用します。 **ComputerVisionClientOperationsMixin** が実装されています。|
|[VisualFeatureTypes](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| この列挙型は、標準の分析操作で実行できるさまざまな種類の画像分析を定義します。 必要に応じて、**VisualFeatureTypes** の値のセットを指定します。 |

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Python 用 OCR クライアント ライブラリを使用して以下のタスクを実行する方法が示されています。

* [クライアントを認証する](#authenticate-the-client)
* [印刷されたテキストと手書きのテキストを読み取る](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>クライアントを認証する

ご利用のエンドポイントとキーを使用してクライアントをインスタンス化します。 キーを使用して [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) オブジェクトを作成し、それをエンドポイントと共に使用して、[ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) オブジェクトを作成します。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

> [!div class="nextstepaction"]
> [クライアントを認証しました](?success=authenticate-client#read-printed-and-handwritten-text) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>印刷されたテキストと手書きのテキストを読み取る

OCR サービスは、画像に映っているテキストを読み取って、文字ストリームに変換することができます。 これは、次の 2 つの処理から成ります。

### <a name="call-the-read-api"></a>Read API を呼び出す

まず、次のコードを使用して、指定した画像の **read** メソッドを呼び出します。 これによって操作 ID が返され、画像の内容を読み取る非同期プロセスが開始されます。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

> [!TIP]
> また、ローカルの画像からテキストを読み取ることもできます。 [ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin) のメソッドを参照してください (**read_in_stream** など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) 上のサンプル コードを参照してください。

### <a name="get-read-results"></a>読み取りの結果を取得する

次に、**read** の呼び出しから返された操作 ID を取得し、操作の結果をサービスに照会します。 次のコードは、結果が返されるまで 1 秒間隔で操作をチェックします。 次に、抽出されたテキスト データをコンソールに出力します。

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

> [!div class="nextstepaction"]
> [テキストを読み取りました](?success=read-printed-handwritten-text#run-the-application) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `python` コマンドを使用して、アプリケーションを実行します。

```console
python quickstart-file.py
```

> [!div class="nextstepaction"]
> [アプリケーションを実行しました](?success=run-the-application#clean-up-resources) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=run-the-application)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [リソースをクリーンアップしました](?success=clean-up-resources#next-steps) [問題が発生しました](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=clean-up-resources)

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、OCR クライアント ライブラリをインストールし、Read API を使用する方法について学習しました。 次は、Read API の機能の詳細について学習しましょう。

> [!div class="nextstepaction"]
>[Read API を呼び出す](../../Vision-API-How-to-Topics/call-read-api.md)

* [OCR の概要](../../overview-ocr.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py) にあります。
