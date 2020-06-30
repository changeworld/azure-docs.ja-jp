---
title: クイック スタート:.NET 用 QnA Maker クライアント ライブラリ
description: このクイックスタートでは、.NET 用 QnA Maker クライアント ライブラリの使用を開始する方法について説明します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。  QnA Maker を使用すると、FAQ ドキュメント、URL、製品マニュアルなどの半構造化コンテンツからの質問と回答サービスをいっそう強力にできます。
ms.topic: quickstart
ms.date: 06/11/2020
ms.openlocfilehash: e487783e506d16006231b07b9a86f93864f51903
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2020
ms.locfileid: "84765143"
---
.NET 用 QnA Maker クライアント ライブラリは、次の目的で使用することができます。

 * ナレッジベースを作成する
 * ナレッジベースを更新する
 * ナレッジベースを発行し、その完了を待つ
 * 予測ランタイム エンドポイント キーを取得する
 * ナレッジベースをダウンロードする
 * ナレッジベースを削除する

[リファレンス ドキュメント](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [C# サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) または現在のバージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* Azure サブスクリプションを入手したら、Azure portal で [QnA Maker リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)を作成し、オーサリング キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** を選択します。
    * アプリケーションを QnA Maker API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定


#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Visual Studio を使用して .NET Core アプリケーションを作成します。次に、**ソリューション エクスプローラー**でソリューションを右クリックし、 **[NuGet パッケージの管理]** を選択して、クライアント ライブラリをインストールしてください。 パッケージ マネージャーが開いたら、 **[参照]** を選択し、 **[プレリリースを含める]** をオンにして、`(package-name)` を検索します。 バージョン `(version)` を選択し、 **[インストール]** を選択します。

#### <a name="cli"></a>[CLI](#tab/cli)

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`qna-maker-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイル (*program.cs*) を使用する単純な "Hello World" C# プロジェクトが作成されます。

```console
dotnet new console -n qna-maker-quickstart
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

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用 QnA Maker クライアント ライブラリをインストールします。

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.1.0
```


---

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs) にあり、このクイックスタートのコード例が含まれています。

プロジェクト ディレクトリから *program.cs* ファイルを開いて、次の `using` ディレクティブを追加します。

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies&highlight=1-2)]

このクイックスタートの共通のタスクを使用するために、以下のセクションに示す変数とコードをアプリケーションの `Main` メソッドに追加します。

## <a name="object-models"></a>オブジェクト モデル

QnA Maker には、次の 2 種類のオブジェクト モデルが使用されています。
* **[QnAMakerClient](#qnamakerclient-object-model)** は、ナレッジベースを作成、管理、発行、ダウンロードするためのオブジェクトです。
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** は、GenerateAnswer API を使用してナレッジ ベースを照会したり、Train API を使用して ([アクティブ ラーニング](../concepts/active-learning-suggestions.md)の一環として) 提案された新しい質問を送信したりするためのオブジェクトです。


### <a name="qnamakerclient-object-model"></a>QnAMakerClient オブジェクト モデル

作成の QnA Maker クライアントは、自分のキーが含まれている Microsoft.Rest.ServiceClientCredentials を使用して Azure に対する認証を行う [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) オブジェクトです。

クライアントが作成されたら、[ナレッジ ベース](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) プロパティを使用して、ナレッジ ベースを作成、管理、および公開します。

JSON オブジェクトを送信して、ナレッジ ベースを管理します。 即時操作の場合は、通常、状態を示す JSON オブジェクトがメソッドから返されます。 実行時間の長い操作の場合、応答は操作 ID になります。 [client.Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) メソッドを操作 ID と共に呼び出して、[要求の状態](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet)を確認します。

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient オブジェクト モデル

予測の QnA Maker クライアントは、[QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) オブジェクトです。ナレッジベースの発行後に作成クライアントの呼び出し (`client.EndpointKeys.GetKeys`) から返される Microsoft.Rest.ServiceClientCredentials (予測ランタイム キーを含む) を使用して Azure に対する認証を行います。

## <a name="code-examples"></a>コード例

以下のコード スニペットは、.NET 用 QnA Maker クライアント ライブラリを使用して次のことを実行する方法を示します。

* [作成クライアントを認証する](#authenticate-the-client-for-authoring-the-knowledge-base)
* [ナレッジ ベースの作成](#create-a-knowledge-base)
* [ナレッジ ベースの更新](#update-a-knowledge-base)
* [ナレッジ ベースのダウンロード](#download-a-knowledge-base)
* [ナレッジ ベースの公開](#publish-a-knowledge-base)
* [ナレッジ ベースの削除](#delete-a-knowledge-base)
* [クエリ ランタイム キーを取得する](#get-query-runtime-key)
* [操作の状態の取得](#get-status-of-an-operation)
* [クエリ ランタイム クライアントを認証する](#authenticate-the-runtime-for-generating-an-answer)
* [ナレッジ ベースから回答を生成する](#generate-an-answer-from-the-knowledge-base)

## <a name="using-this-example-knowledge-base"></a>このサンプル ナレッジ ベースを使用する

このクイックスタートのナレッジ ベースは、2 つの会話型 QnA ペアから始めます。これは、例を簡略化し、Update メソッドで使用するための高度に予測可能な ID を持つようにするために行われます。これにより、質問を含むフォローアップ プロンプトが新しいペアに関連付けられます。 このクイックスタートでは、特定の順序でこれを計画、実装しています。

いずれ、既存の QnA ペアに依存するフォローアップ プロンプトを使用してナレッジ ベースを開発する予定である場合、次の方法を選択できます。
* 大規模なナレッジベースの場合、自動化をサポートする TSV ツールまたはテキスト エディターでナレッジ ベースを管理し、その後一度に、ナレッジ ベース全体を更新版に置き換えます。
* 比較的小規模なナレッジベースの場合、QnA Maker ポータルのみでフォローアップ プロンプトを管理します。

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>ナレッジ ベースを作成するためのクライアントを認証する

**main** メソッドに、リソース名とリソースの Azure キーに対応する変数を作成します。 作成 URL と予測 URL では、どちらもリソース名がサブドメインとして使用されます。

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]


> [!IMPORTANT]
> Azure portal に移動し、前提条件で作成した QnA Maker リソースのキーとエンドポイントを探します。 それらは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。
> ナレッジベースを作成するには、キー全体が必要です。 エンドポイントから必要となるのはリソース名のみです。 形式は `` のようになります。
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 たとえば、[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) で安全なキー記憶域を確保できます。

次に、自分のキーを使用して [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) オブジェクトを作成し、それを自分のエンドポイントと共に使用して、[QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) オブジェクトを作成します。

|可変|例|
|--|--|
|`authoringKey`|このキーは 32 文字の文字列で、Azure portal の QnA Maker リソースの **[Keys and endpoints]\(キーとエンドポイント\)** ページで入手できます。 これは、予測ランタイム キーとは異なります。|
|`resourceName`| リソース名は、`https://{resourceName}.cognitiveservices.azure.com` という形式で使用されます。 これは、予測ランタイムを照会するときに使用した URL とは異なります。|
||||

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>ナレッジ ベースの作成

ナレッジ ベースには、次の 3 つのソースの [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) オブジェクトに対する質問と回答のペアが格納されます。

* **本文**の場合は、[QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) オブジェクトを使用します。
    * メタデータとフォローアップ プロンプトを使用するには、編集コンテキストを使用します (このデータは個々の QnA ペア レベルで追加されるため)。
* **ファイル**の場合は、[FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) オブジェクトを使用します。
* **URL** の場合は、文字列のリストを使用します。

[CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) メソッドを呼び出した後、返された操作 ID を [MonitorOperation](#get-status-of-an-operation) メソッドに渡して、状態をポーリングします。

次のコードの最後の行では、MonitorOperation の応答からナレッジ ベース ID を返しています。

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod&highlight=37-38)]

ナレッジ ベースを適切に作成するには、上記のコードで参照されている [`MonitorOperation`](#get-status-of-an-operation) 関数を必ず含めます。

## <a name="update-a-knowledge-base"></a>ナレッジ ベースの更新

ナレッジ ベースを更新するには、ナレッジ ベース ID と、[add](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet)、[update](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)、および [delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) DTO オブジェクトを含む [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) を、[UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) メソッドに渡します。 更新が成功したかどうかを確認するには、[MonitorOperation](#get-status-of-an-operation) メソッドを使用します。

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod&highlight=8)]

ナレッジ ベースを適切に更新するには、上記のコードで参照されている [`MonitorOperation`](#get-status-of-an-operation) 関数を必ず含めます。

## <a name="download-a-knowledge-base"></a>ナレッジ ベースのダウンロード

データベースを [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet) のリストとしてダウンロードするには、[DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) メソッドを使用します。 このメソッドの結果は TSV ファイルではないため、これは、QnA Maker ポータルの **[設定]** ページからのエクスポートと同等 "_ではありません_"。

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB&highlight=3,4)]

## <a name="publish-a-knowledge-base"></a>ナレッジ ベースの公開

[PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) メソッドを使用して、ナレッジ ベースを公開します。 これにより、ナレッジ ベース ID によって参照される、最新の保存済みおよびトレーニング済みのモデルが取得され、エンドポイントで公開されます。

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB&highlight=3)]

## <a name="get-query-runtime-key"></a>クエリ ランタイム キーを取得する

ナレッジベースの発行後、ランタイムに対してクエリを実行するには、クエリ ランタイム キーが必要です。 これは、元のクライアント オブジェクトを作成するときに使用したキーとは異なります。

[EndpointKeys](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) メソッドを使用して、[EndpointKeysDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-dotnet) クラスを取得します。

そのオブジェクトに返されたいずれかのキー プロパティを使用して、ナレッジベースにクエリを実行します。

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey&highlight=3)]

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>回答を生成するためのランタイムを認証する

ナレッジ ベースにクエリを実行して回答を生成したり、アクティブ ラーニングからトレーニングしたりするための [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) を作成します。

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

QnAMakerRuntimeClient を使用してナレッジから回答を取得するか、または、[アクティブ ラーニング](../concepts/active-learning-suggestions.md)を目的として提案された新しい質問をナレッジ ベースに送信します。

## <a name="generate-an-answer-from-the-knowledge-base"></a>ナレッジ ベースから回答を生成する

[RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) メソッドを使用して公開済みのナレッジ ベースから回答を生成します。 このメソッドは、ナレッジ ベース ID と [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet) を受け取ります。 さらに、[Top](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) や [Context](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) など、QueryDTO のプロパティにアクセスしてチャット ボットで使用することができます。

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer&highlight=3)]

これは、ナレッジ ベースに対してクエリを実行する単純な例です。 高度なクエリ シナリオについては、[他のクエリ サンプル](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)をご覧ください。

## <a name="delete-a-knowledge-base"></a>ナレッジ ベースを削除する

[DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) メソッドをナレッジ ベース ID のパラメーターと共に使用して、ナレッジ ベースを削除します。

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>操作の状態の取得

create や update などのメソッドの中には、プロセスが終了するのを待つ代わりに、[操作](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet)が返されるのに十分な時間がかかるものがあります。 操作からの[操作 ID](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) を使用して、(再試行ロジックを使用して) ポーリングし、元のメソッドの状態を判別します。

次のコード ブロックの _loop_ および _Task.Delay_ は、再試行ロジックをシミュレートするために使用されています。 これらは自分の再試行ロジックに置き換える必要があります。

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>アプリケーションの実行

自分のアプリケーション ディレクトリで `dotnet run` コマンドを使用してアプリケーションを実行します。

この記事にあるすべてのコード スニペットは、[入手可能](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)であり、1 つのファイルとして実行できます。

```dotnetcli
dotnet run
```

* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart) にあります。