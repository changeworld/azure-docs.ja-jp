---
title: クイック スタート:Node.js 用 QnA Maker クライアント ライブラリ
description: このクイックスタートでは、Node.js 用 QnA Maker クライアント ライブラリの使用を開始する方法について説明します。
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 05e6d2c77a351c22c73f0bdb54daeaf0e03f4a9d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020934"
---
Node.js 用 QnA Maker クライアント ライブラリは、次の目的で使用することができます。

* ナレッジ ベースの作成
* ナレッジ ベースの更新
* ナレッジ ベースの公開
* 発行したエンドポイント キーの取得
* 実行時間の長いタスクの待機
* ナレッジ ベースの削除

[リファレンスのドキュメント](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [パッケージ (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js のサンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* 最新バージョンの [Node.js](https://nodejs.org)。

## <a name="setting-up"></a>設定

### <a name="create-a-qna-maker-azure-resource"></a>QnA Maker Azure リソースを作成する

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) または [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) を使用して、ローカル コンピューター上に QnA Maker のリソースを作成します。

リソースからキーとエンドポイントを取得した後、Azure portal の [クイックスタート] ページで、新しいリソースのための値を取得します。

`QNAMAKER_AUTHORING_KEY` および `QNAMAKER_ENDPOINT` という名前の[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。 [.env.sample](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/.env.sample) ファイルを `.env` にコピーして、そのファイル内の環境変数を使用することができます。

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

`npm init -y` コマンドを実行し、`package.json` ファイルを使用して node アプリケーションを作成します。

```console
npm init -y
```

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

必須およびオプションの NPM パッケージをインストールします。

```console
npm install @azure/cognitiveservices-qnamaker @azure/ms-rest-js dotenv
```

アプリの `package.json` ファイルが依存関係によって更新されます。 `dotenv` はオプションであり、テキスト ファイルで環境変数を設定できるようにするために使用されます。 ソース管理に `.env` をチェックインしないでください。


## <a name="object-model"></a>オブジェクト モデル

QnA Maker クライアントは、自分のキーが含まれている ServiceClientCredentials を使用して Azure に対する認証を行う [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) オブジェクトです。

クライアントが作成されたら、[ナレッジ ベース](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) プロパティを使用して、ナレッジ ベースを作成、管理、および公開します。

JSON オブジェクトを送信して、ナレッジ ベースを管理します。 即時操作の場合は、通常、状態を示す JSON オブジェクトがメソッドから返されます。 実行時間の長い操作の場合、応答は操作 ID になります。 [client.Operations.getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) メソッドを操作 ID と共に呼び出して、[要求の状態](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest)を確認します。


## <a name="code-examples"></a>コード例

以下のコード スニペットは、Node.js 用 QnA Maker クライアント ライブラリを使用して次のことを実行する方法を示します。

* [ナレッジ ベースの作成](#create-a-knowledge-base)
* [ナレッジ ベースの更新](#update-a-knowledge-base)
* [ナレッジ ベースの公開](#publish-a-knowledge-base)
* [ナレッジ ベースの削除](#delete-a-knowledge-base)
* [発行したエンドポイントの取得](#get-published-endpoint)
* [操作の状態の取得](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>依存関係を追加する

`index.js` という名前でファイルを作成します。 ファイルに QnA Maker ライブラリと依存関係を追加します。

[!code-javascript[Require statements](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=dependencies)]

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、もう一度開く必要があります。

|環境変数|Node.js 変数|例|
|--|--|--|
|`QNAMAKER_AUTHORING_KEY`|`authoring_key`|このキーは 32 文字の文字列で、Azure portal の [クイックスタート] ページの QnA Maker リソースで入手できます。 これは、予測エンドポイント キーと同じではありません。|
|`QNAMAKER_ENDPOINT`|`endpoint`| `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` 形式の作成エンドポイントには、実際の**リソース名**が含まれます。 これは、予測エンドポイントを照会するときに使用した URL と同じではありません。|
||||

[!code-javascript[Azure resource variables](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=resourceSettings)]

## <a name="authenticate-the-client"></a>クライアントを認証する

次に、自分のキーを使用して ApiKeyCredentials オブジェクトを作成し、それを自分のエンドポイントと共に使用して、[QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) オブジェクトを作成します。 クライアント オブジェクトを使用して、[ナレッジ ベース クライアント](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest) オブジェクトを取得します。


[!code-javascript[Authorization to resource key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>ナレッジ ベースの作成

ナレッジ ベースには、次の 3 つのソースの [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) オブジェクトに対する質問と回答のペアが格納されます。

* **本文**の場合は、[QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) オブジェクトを使用します。
* **ファイル**の場合は、[FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) オブジェクトを使用します。
* **URL** の場合は、文字列のリストを使用します。

ナレッジ ベース情報を使用して、[create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) メソッドを呼び出します。 ナレッジ ベース情報は、基本的に JSON オブジェクトです。

create メソッドから戻ったら、返された操作 ID を [wait_for_operation](#get-status-of-an-operation) メソッドに渡して、状態をポーリングします。 操作が完了すると、wait_for_operation メソッドから戻ってきます。 返された操作の `resourceLocation` ヘッダー値を解析して、新しいナレッジ ベース ID を取得します。

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=createKnowledgeBase&highlight=15,30)]

ナレッジ ベースを適切に作成するには、上記のコードで参照されている [`wait_for_operation`](#get-status-of-an-operation) 関数を必ず含めます。

## <a name="update-a-knowledge-base"></a>ナレッジ ベースの更新

ナレッジ ベースを更新するには、ナレッジ ベース ID と、[add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add)、[update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)、[delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO オブジェクトを含む [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) を、[update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) メソッドに渡します。 DTO も、基本的に JSON オブジェクトです。 更新が成功したかどうかを確認するには、[wait_for_operation](#get-status-of-an-operation) メソッドを使用します。

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=updateKnowledgeBase&highlight=28)]

ナレッジ ベースを適切に更新するには、上記のコードで参照されている [`wait_for_operation`](#get-status-of-an-operation) 関数を必ず含めます。

## <a name="publish-a-knowledge-base"></a>ナレッジ ベースの公開

[publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) メソッドを使用して、ナレッジ ベースを公開します。 これにより、ナレッジ ベース ID によって参照される、最新の保存済みおよびトレーニング済みのモデルが取得され、エンドポイントで公開されます。 HTTP 応答コードを調べて、発行が成功したことを検証します。

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=publishKnowledgeBase&highlight=3)]


## <a name="get-published-endpoint"></a>発行したエンドポイントの取得

ナレッジ ベースが発行されたら、クエリ予測ランタイムの generateAnswer API を通じて、発行されたナレッジ ベースにアクセスします。 そうするには、ランタイムのエンドポイント キーが必要です。 これは、オーサリング キーとは異なります。

[!code-javascript[Get endpoint keys](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=getEndpointKeys&highlight=3)]

呼び出しからは、2 つのエンドポイント キーが返されます。 ランタイム エンドポイントへのアクセスに必要なのは、1 つだけです。

## <a name="delete-a-knowledge-base"></a>ナレッジ ベースを削除する

[delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) メソッドをナレッジ ベース ID のパラメーターと共に使用して、ナレッジ ベースを削除します。

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=deleteKnowledgeBase&highlight=3)]

## <a name="get-status-of-an-operation"></a>操作の状態の取得

create や update などのメソッドの中には、プロセスが終了するのを待つ代わりに、[操作](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest)が返されるのに十分な時間がかかるものがあります。 操作からの[操作 ID](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) を使用して、(再試行ロジックを使用して) ポーリングし、元のメソッドの状態を判別します。

次のコード ブロック内の _delayTimer_ 呼び出しは、再試行ロジックをシミュレートするために使用されています。 これを独自の再試行ロジックに置き換えます。

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=monitorOperation&highlight=8,13)]

## <a name="run-the-application"></a>アプリケーションの実行

自分のアプリケーション ディレクトリで `node index.js` コマンドを使用してアプリケーションを実行します。

この記事にあるすべてのコード スニペットは、[入手可能](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)であり、1 つのファイルとして実行できます。

```console
node index.js
```

プログラムによって、状態がコンソールに出力されます。

```console
qnamaker_quickstart@1.0.0 start C:\samples\cognitive-services-quickstart-code\javascript\QnAMaker\sdk> node index.js

Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Create operation 200, KB ID 99df758d-f23f-4931-ab83-e738fe978e69
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Update operation state 200 - HTTP status 200
Publish request succeeded - HTTP status 204
GetEndpointKeys request succeeded - HTTP status 200 - primary key 8482830b-681e-400e-b8a3-4016278aba64
QnA Maker FAQ stored in English language with 1 sources, last updated 2020-01-12T16:54:40Z
New KB name stored in English language with 1 sources, last updated 2020-01-12T17:32:16Z
New KB name stored in English language with 1 sources, last updated 2020-01-13T00:27:46Z
Delete operation state succeeded - HTTP status 204
done
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)