---
title: クイック スタート:Java 用 QnA Maker クライアント ライブラリ
description: このクイックスタートでは、Java 用 QnA Maker クライアント ライブラリの使用を開始する方法について説明します。
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: 755e4fcb9d5b9f1711bc615a29489037ea377d43
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609505"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

Java 用 QnA Maker クライアント ライブラリは、次の目的で使用することができます。

* ナレッジベースを作成する
* ナレッジ ベースの更新
* ナレッジ ベースの公開
* 予測ランタイム エンドポイント キーの取得
* 実行時間の長いタスクの待機
* ナレッジ ベースのダウンロード
* ナレッジ ベースから回答を取得する
* ナレッジ ベースの削除

[ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker) | [パッケージ](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker/1.0.0-beta.1) | [サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker/sdk/quickstart.java)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

Java 用 QnA Maker クライアント ライブラリは、次の目的で使用することができます。

* ナレッジベースを作成する
* ナレッジ ベースの更新
* ナレッジ ベースの公開
* 実行時間の長いタスクの待機
* ナレッジ ベースのダウンロード
* ナレッジ ベースから回答を取得する
* ナレッジ ベースの削除

[ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker) | [パッケージ](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker/1.0.0-beta.2) | [サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker/sdk/preview-sdk/quickstart.java)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>前提条件

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* [JDK](https://www.oracle.com/java/technologies/javase-downloads.html)
* Azure サブスクリプションを入手したら、Azure portal で [QnA Maker リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)を作成し、オーサリング キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** を選択します。
    * アプリケーションを QnA Maker API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* [JDK](https://www.oracle.com/java/technologies/javase-downloads.html)
* Azure サブスクリプションを入手したら、Azure portal で [QnA Maker リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)を作成し、オーサリング キーとエンドポイントを取得します。
    * 注: 必ず **[マネージド]** チェックボックスをオンにしてください。
    * QnA Maker リソースがデプロイされたら、 **[リソースに移動]** を選択します。 アプリケーションを QnA Maker API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

---

## <a name="setting-up"></a>設定

### <a name="install-the-client-libraries"></a>クライアント ライブラリをインストールする

Java をインストールしたら、[MVN リポジトリ](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker)の [Maven](https://maven.apache.org/) を使用して、クライアント ライブラリをインストールできます。

### <a name="create-a-new-java-application"></a>新しい Java アプリケーションを作成する

`quickstart.java` という名前の新しいファイルを作成して、以下のライブラリをインポートします。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-java[Dependencies](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-java[Dependencies](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=dependencies)]

---

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

- サブスクリプション キーとオーサリング キーは同じ意味で使用しています。 オーサリング キーの詳細については、「[QnA Maker のキー](../concepts/azure-resources.md?tabs=v1#keys-in-qna-maker)」を参照してください。

- QNA_MAKER_ENDPOINT の値の形式は `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` です。 Azure portal に移動し、前提条件で作成した QnA Maker リソースを探します。 **[リソース管理]** の **[Keys and Endpoint]\(キーとエンドポイント\)** ページをクリックし、オーサリング (サブスクリプション) キーと QnA Maker のエンドポイントを見つけます。

 ![QnA Maker 作成エンドポイント](../media/keys-endpoint.png)

- QNA_MAKER_RUNTIME_ENDPOINT の値の形式は `https://YOUR-RESOURCE-NAME.azurewebsites.net` です。 Azure portal に移動し、前提条件で作成した QnA Maker リソースを探します。 **[オートメーション]** の **[テンプレートのエクスポート]** ページをクリックして、ランタイム エンドポイントを見つけます。

 ![QnA Maker のランタイム エンドポイント](../media/runtime-endpoint.png)
   
- 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 たとえば、[Azure Key Vault](../../../key-vault/general/overview.md) で安全なキー記憶域を確保できます。

[!code-java[Resource variables](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=resourceKeys)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

- サブスクリプション キーとオーサリング キーは同じ意味で使用しています。 オーサリング キーの詳細については、「[QnA Maker のキー](../concepts/azure-resources.md?tabs=v2#keys-in-qna-maker)」を参照してください。

- QNA_MAKER_ENDPOINT の値の形式は `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` です。 Azure portal に移動し、前提条件で作成した QnA Maker リソースを探します。 **[リソース管理]** の **[Keys and Endpoint]\(キーとエンドポイント\)** ページをクリックし、オーサリング (サブスクリプション) キーと QnA Maker のエンドポイントを見つけます。

 ![QnA Maker 作成エンドポイント](../media/keys-endpoint.png)
 
- 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 たとえば、[Azure Key Vault](../../../key-vault/general/overview.md) で安全なキー記憶域を確保できます。

[!code-java[Resource variables](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=resourceKeys)]

---

## <a name="object-models"></a>オブジェクト モデル

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

QnA Maker には、次の 2 種類のオブジェクト モデルが使用されています。
* **[QnAMakerClient](#qnamakerclient-object-model)** は、ナレッジ ベースを作成、管理、公開、ダウンロードするためのオブジェクトです。
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** は、GenerateAnswer API を使用してナレッジ ベースを照会したり、Train API を使用して ([アクティブ ラーニング](../how-to/use-active-learning.md)の一環として) 提案された新しい質問を送信したりするためのオブジェクトです。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

QnA Maker では、次のオブジェクト モデルが使用されます。
* **[QnAMakerClient](#qnamakerclient-object-model)** は、ナレッジ ベースの作成、管理、公開、ダウンロード、およびクエリを行うためのオブジェクトです。

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient オブジェクト モデル

作成の QnA Maker クライアントは、自分のキーが含まれている MsRest::ServiceClientCredentials を使用して Azure に対する認証を行う [QnAMakerClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java) オブジェクトです。

クライアントが作成されたら、クライアントの [Knowledgebases](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java) プロパティのメソッドを使用して、ナレッジ ベースを作成、管理、および公開します。

即時操作の場合、メソッドは通常、結果があれば、それを返します。 長時間にわたって実行される操作の場合、応答は [Operation](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java) オブジェクトになります。 [getDetails](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Operations.java#L32) メソッドを、`operation.operationId` 値を指定して呼び出して、[要求の状態](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/OperationStateType.java)を確認します。

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient オブジェクト モデル

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

ランタイム QnA Maker クライアントは、[QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java) オブジェクトです。

作成クライアントを使用してナレッジ ベースを公開した後で、ランタイム クライアントの [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36) メソッドを使用して、ナレッジ ベースからの回答を取得します。

ランタイム クライアントを作成するには、[QnAMakerRuntimeManager.authenticate](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) を呼び出し、ランタイム エンドポイント キーを渡します。 ランタイム エンドポイント キーを取得するには、作成クライアントを使用して、[getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30) を呼び出します。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

QnA Maker マネージド リソースでは、QnAMakerRuntimeClient オブジェクトを使用する必要はありません。 代わりに、[QnAMakerClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java) オブジェクトで [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/657e9a47e4b4c7e7e7eee4100273c09468a30c63/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L308) を直接呼び出します。

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>ナレッジ ベースを作成するためのクライアントを認証する

作成エンドポイントとサブスクリプション キーを使用して、クライアントをインスタンス化します。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-java[Authenticate](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=authenticate)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-java[Authenticate](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=authenticate)]

---

## <a name="create-a-knowledge-base"></a>ナレッジ ベースの作成

ナレッジ ベースには、次の 3 つのソースの [CreateKbDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/CreateKbDTO.java) オブジェクトに対する質問と回答のペアが格納されます。

* **本文** の場合は、[QnADTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADTO.java) オブジェクトを使用します。
    * メタデータとフォローアップ プロンプトを使用するには、編集コンテキストを使用します (このデータは個々の QnA ペア レベルで追加されるため)。
* **ファイル** の場合は、[FileDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/FileDTO.java) オブジェクトを使用します。 FileDTO には、ファイル名と、ファイルに到達するためのパブリック URL が含まれます。
* **URL** の場合は、公開されている URL を表す文字列のリストを使用します。

[create](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L173) メソッドを呼び出した後、返された操作の `operationId` プロパティを [getDetails](#get-status-of-an-operation) メソッドに渡し、状態をポーリングします。

次のコードの最後の行では、ナレッジ ベース ID が返されます。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-java[Create knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=createKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-java[Create knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=createKb)]

---

## <a name="update-a-knowledge-base"></a>ナレッジ ベースの更新

ナレッジ ベースを更新するには、[update](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L150) を呼び出し、ナレッジ ベース ID と [UpdateKbOperationDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTO.java) オブジェクトを渡します。 そのオブジェクトには、次のものを含めることができます。
- [add](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOAdd.java)
- [update](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOUpdate.java)
- [delete](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTODelete.java)

状態をポーリングするには、返された操作の `operationId` プロパティを [getDetails](#get-status-of-an-operation) メソッドに渡します。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-java[Update knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=updateKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-java[Update knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=updateKb)]

---

## <a name="download-a-knowledge-base"></a>ナレッジ ベースのダウンロード

データベースを [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADocumentsDTO.java) のリストとしてダウンロードするには、[download](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196) メソッドを使用します。 このメソッドの結果は TSV ファイルではないため、これは、QnA Maker ポータルの **[設定]** ページからのエクスポートと同等 "_ではありません_"。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-java[Download knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=downloadKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-java[Download knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=downloadKb)]

---

## <a name="publish-a-knowledge-base"></a>ナレッジ ベースの公開

[publish](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196) メソッドを使用して、ナレッジ ベースを公開します。 これにより、ナレッジ ベース ID によって参照される、最新の保存済みおよびトレーニング済みのモデルが取得され、エンドポイントで公開されます。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-java[Publish knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=publishKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-java[Publish knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=publishKb)]

---

## <a name="generate-an-answer-from-the-knowledge-base"></a>ナレッジ ベースから回答を生成する

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

ナレッジ ベースの公開後、ナレッジ ベースに対してクエリを実行するには、ランタイム エンドポイント キーが必要です。 これは、作成クライアントの作成に使用したサブスクリプション キーとは異なります。

[EndpointKeysDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/EndpointKeysDTO.java) オブジェクトを取得するには、[getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b637366e32edefb0fe63962983715a02c1ad2631/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30) メソッドを使用します。

ランタイム クライアントを作成するには、[QnAMakerRuntimeManager.authenticate](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) を呼び出し、EndpointKeysDTO オブジェクトのランタイム エンドポイント キーを渡します。

[generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36) メソッドを使用して、公開済みのナレッジ ベースから回答を生成します。 このメソッドは、ナレッジ ベース ID と [QueryDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java) オブジェクトを受け取ります。

[!code-java[Query knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=queryKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/657e9a47e4b4c7e7e7eee4100273c09468a30c63/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L308) メソッドを使用して、公開済みのナレッジ ベースから回答を生成します。 このメソッドは、ナレッジ ベース ID と [QueryDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java) オブジェクトを受け取ります。

[!code-java[Query knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=queryKb)]

---

これは、ナレッジ ベースに対してクエリを実行する単純な例です。 高度なクエリ シナリオについては、[他のクエリ サンプル](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)をご覧ください。

## <a name="delete-a-knowledge-base"></a>ナレッジ ベースを削除する

[delete](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L81) メソッドをナレッジ ベース ID のパラメーターと共に使用して、ナレッジ ベースを削除します。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-java[Delete knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=deleteKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-java[Delete knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=deleteKb)]

---

## <a name="get-status-of-an-operation"></a>操作の状態の取得

create や update などのメソッドの中には、プロセスが終了するのを待つ代わりに、[操作](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java)が返されるのに十分な時間がかかるものがあります。 操作からの操作 ID を使用して、(再試行ロジックを使用して) ポーリングし、元のメソッドの状態を判別します。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-java[Wait for operation](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=waitForOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-java[Wait for operation](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=waitForOperation)]

---

## <a name="run-the-application"></a>アプリケーションの実行

これが、アプリケーションの main メソッドです。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

[!code-java[Main method](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=main)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

[!code-java[Main method](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=main)]

---

次のようにアプリケーションを実行します。 ここでは、クラス名が `Quickstart` であり、依存関係が現在のフォルダーの下の `lib` という名前のサブフォルダーにあることを前提としています。

```console
javac Quickstart.java -cp .;lib\*
java -cp .;lib\* Quickstart
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/version-1)

このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/quickstart.java) にあります。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/version-2)

このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/preview-sdk/quickstart.java) にあります。

---
