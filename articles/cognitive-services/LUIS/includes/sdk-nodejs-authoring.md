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
ms.openlocfilehash: 070de0f65e890c38acd5075286b349e95cd19f3b
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2020
ms.locfileid: "77371962"
---
Node.js 用 Language Understanding (LUIS) 作成クライアント ライブラリの用途は次のとおりです。

* アプリを作成する。
* 意図、エンティティ、発話の例を追加する。
* 語句リストなどの機能を追加する。
* アプリをトレーニングして公開する。
* アプリの削除

[リファレンス ドキュメント](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) | [作成パッケージ (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)、[ランタイム パッケージ (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_authoring_quickstart.js)

## <a name="prerequisites"></a>前提条件

* Language Understanding 作成リソース: [Azure portal で作成する](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>設定

### <a name="get-your-language-understanding-luis-starter-key"></a>Language Understanding (LUIS) スターター キーを取得する

LUIS 作成リソースを作成して、[スターター キー](../luis-how-to-azure-subscription.md#starter-key)を取得します。 次の手順のために、キーとキーのエンドポイントを保持しておきます。

### <a name="create-an-environment-variable"></a>環境変数を作成する

キーとキーのリージョンを使用して、認証用に 2 つの環境変数を作成します。

* `LUIS_AUTHORING_KEY` - 要求を認証するためのリソース キー。
* `LUIS_AUTHORING_ENDPOINT` - キーに関連付けられたエンドポイント。

ご利用のオペレーティング システムの手順に従ってください。

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_AUTHORING_ENDPOINT <replace-with-your-luis-authoring-endpoint>
```

環境変数を追加したら、コンソール ウィンドウを再起動します。

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source ~/.bashrc` を実行します。

#### <a name="macos"></a>[macOS](#tab/unix)

次のように `.bash_profile` を編集し、環境変数を追加します。

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source .bash_profile` を実行します。
***

### <a name="install-the-npm-library-for-luis-authoring"></a>LUIS 作成用 NPM ライブラリをインストールする

次のコマンドを使用して、アプリケーション ディレクトリ内に依存関係をインストールします。

```console
npm install @azure/cognitiveservices-luis-authoring @azure/ms-rest-js
```

## <a name="object-model"></a>オブジェクト モデル

Language Understanding (LUIS) 作成クライアントは、認証を経て作成キーの保管場所である Azure にアクセスする [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) オブジェクトです。

クライアントを作成したら、このクライアントを使用して次のような機能にアクセスします。

* アプリ - [追加](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-)、[削除](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-)、[公開](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* 発話の例 - [バッチによる追加](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-)、[ID による削除](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* 機能 - [語句リスト](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)の管理
* モデル - [意図](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-)とエンティティの管理
* パターン - [パターン](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)の管理
* トレーニング - アプリの[トレーニング](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-)と[トレーニングの状態](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)のポーリング
* [バージョン](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) - クローンによる管理、エクスポート、削除


## <a name="code-examples"></a>コード例

以下のコード スニペットは、Node.js 用 Language Understanding (LUIS) 作成クライアント ライブラリを使用して次のことを実行する方法を示したものです。

* [アプリの作成](#create-a-luis-app)
* [エンティティの追加](#create-entities-for-the-app)
* [意図の追加](#create-intent-for-the-app)
* [発話の例を追加する](#add-example-utterance-to-intent)
* [アプリのトレーニング](#train-the-app)
* [アプリの公開](#publish-a-language-understanding-app)
* [アプリの削除](#delete-a-language-understanding-app)
* [アプリの一覧表示](#list-language-understanding-apps)

## <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

好みのエディターまたは IDE で、`luis_authoring_quickstart.js` という名前の新しいテキスト ファイルを作成します。 その後、次の依存関係を追加します。

[!code-javascript[Create a new application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Dependencies)]

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、もう一度開く必要があります。

[!code-javascript[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Variables)]

## <a name="authenticate-the-client"></a>クライアントを認証する

自分のキーを指定して [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) オブジェクトを作成し、それを自分のエンドポイントと共に使用して [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) オブジェクトを作成します。

[!code-javascript[Create LUIS client object](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>LUIS アプリの作成

1. 意図、エンティティ、発話の例を保持した自然言語処理 (NLP) モデルが含まれる LUIS アプリを作成します。

1. [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) オブジェクトの [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) メソッドを作成してアプリを作成します。 名前と言語のカルチャは必須のプロパティです。

    [!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateApplication&highlight=9-11)]


## <a name="create-intent-for-the-app"></a>アプリの意図の作成
LUIS アプリのモデルの中で最も重要なオブジェクトが、意図です。 意図は、ユーザーが発話において "_意図したこと_" をグループ分けしたものに対応しています。 ユーザーが質問をしたり、言葉を発したりするのは、ボット (またはその他のクライアント アプリケーション) から "_自らが意図した_" 回答を得るためです。 意図の例は、フライトの予約、目的地の天気の確認、カスタマー サービスの連絡先情報の取得などが挙げられます。

意図の一意の名前と共に [model.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) メソッドを使用し、アプリ ID とバージョン ID、新しい意図の名前を渡します。

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddIntents&highlight=2-6)]

## <a name="create-entities-for-the-app"></a>アプリのエンティティの作成

必須ではないものの、ほとんどのアプリで見られるのがエンティティです。 エンティティは、ユーザーの発話からユーザーが意図した成果を達成するために必要な情報を抽出します。 [事前構築済み](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-)エンティティとカスタム エンティティにはいくつかの種類があり、それぞれが専用のデータ変換オブジェクト (DTO) モデルを備えています。  事前構築済みエンティティのうち、アプリに追加することが多いものとしては、[number](../luis-reference-prebuilt-number.md)、[datetimeV2](../luis-reference-prebuilt-datetimev2.md)、[geographyV2](../luis-reference-prebuilt-geographyv2.md)、[ordinal](../luis-reference-prebuilt-ordinal.md) があります。

この **add_entities** メソッドにより、2 つのロールが設定された `Location` シンプル エンティティ、`Class` シンプル エンティティ、`Flight` 複合エンティティが作成されるほか、いくつかの事前構築済みエンティティが追加されます。

重要なのは、エンティティには 1 つの意図だけがマークされるわけではないという点です。 これらは多くの意図に適用することができますし、多くの意図に適用されるのが普通です。 特定の意図が 1 つだけマークされるのは、ユーザーの発話の例のみです。

エンティティ作成のためのメソッドは、[Model](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest) クラスに含まれています。 エンティティは種類ごとに固有のデータ変換オブジェクト (DTO) モデルを備えています。

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddEntities&highlight=2-6)]

## <a name="add-example-utterance-to-intent"></a>意図に対する発話の例の追加

アプリでは、発話の意図を特定し、エンティティを抽出するために、発話の例が必要になります。 例では、特定の 1 つの意図をターゲットとし、すべてのカスタム エンティティをマークする必要があります。 事前構築済みエンティティをマークする必要はありません。

[ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) オブジェクトの一覧 (発話の例 1 件につき 1 つのオブジェクト) を作成し、発話の例を追加します。 それぞれの例では、エンティティの名前とエンティティの値から成る名前と値のペアのディクショナリを使用し、エンティティをすべてマークする必要があります。 エンティティの値は、発話の例のテキストに表示されているものと正確に一致している必要があります。

アプリ ID、バージョン ID、例の一覧を指定して [examples.batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-) を呼び出します。 この呼び出しにより、結果のリストが返されます。 モデルに対して例が正常に追加されたかどうかを確かめるには、それぞれの例の結果を確認する必要があります。

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringBatchAddUtterancesForIntent&highlight=52-56)]

## <a name="train-the-app"></a>アプリをトレーニングする

モデルの作成を終えたら、このバージョンのモデルに合わせて LUIS アプリをトレーニングする必要があります。 トレーニングを済ませたモデルは、[コンテナー](../luis-container-howto.md)で使用したり、ステージング スロットまたは製品スロットに[公開](../luis-how-to-publish-app.md)したりできます。

[train.trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) メソッドでは、アプリ ID とバージョン ID が必要です。

このクイックスタートで示したような非常に小さなモデルであれば、トレーニングが短時間で完了します。 運用レベルのアプリケーションの場合には、アプリのトレーニングに、トレーニングが成功したかどうかと、そのタイミングを確認するための [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) メソッドに対するポーリング呼び出しが含まれます。 応答は、オブジェクトごとの状態を格納した [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) オブジェクトの一覧です。 トレーニングが完了したと判定されるには、オブジェクトがすべて成功している必要があります。

[!code-javascript[Train LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringTrainVersion&highlight=2-5)]

すべてのモデルをトレーニングするには時間がかかります。 トレーニングの状態をチェックするには、operationResult を使用します。

[!code-javascript[Get training status](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringWaitForOperation&highlight=11-14)]

## <a name="publish-a-language-understanding-app"></a>Language Understanding アプリの公開

[app.publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) メソッドを使用して、LUIS アプリを公開します。 これにより、トレーニング済みの最新バージョンがエンドポイントの指定されたスロットに公開されます。 クライアント アプリケーションではこのエンドポイントを使用して、意図の予測とエンティティの抽出の対象となるユーザー発話を送信します。

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringPublishVersion&highlight=2-5)]

## <a name="delete-a-language-understanding-app"></a>Language Understanding アプリの削除

[app.deleteMethod](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-) メソッドを使用して、LUIS アプリを削除します。 これにより現在のアプリが削除されます。

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringDeleteApp&highlight=2)]

## <a name="list-language-understanding-apps"></a>Language Understanding アプリの一覧表示

Language Understanding キーに関連付けられているアプリの一覧を取得します。

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringListApps)]

## <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `node luis_authoring_quickstart.js` コマンドを使用して、アプリケーションを実行します。

```console
node luis_authoring_quickstart.js
```

このアプリケーションのコマンド ライン出力は次のとおりです。

```console
Created LUIS app with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7
Entity Destination created.
Entity Class created.
Entity Flight created.
Intent FindFlights added.
Created 3 entity labels.
Created 3 entity labels.
Created 3 entity labels.
Example utterances added.
Waiting for train operation to finish...
Current model status: ["Queued"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["Success"]
Application published. Endpoint URL: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/e137a439-b3e0-4e16-a7a8-a9746e0715f7
Application with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7 deleted. Operation result: Operation Successful
```
