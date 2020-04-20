---
title: クイック スタート:cURL と REST を使用してナレッジ ベースを管理する - QnA Maker
description: このクイックスタートでは、REST API を使用してナレッジ ベースを作成、公開し、クエリを実行する方法について説明します。
ms.date: 02/27/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: quickstart
ms.openlocfilehash: 00ec52fe20fb0e6a976f3e7142386e835713c98c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "78851206"
---
# <a name="quickstart-use-curl-and-rest-to-manage-knowledge-base"></a>クイック スタート:cURL と REST を使用してナレッジ ベースを管理する

このクイックスタートでは、ナレッジ ベースを作成、公開し、クエリを実行する手順を紹介しています。 QnA Maker は、[データ ソース](../Concepts/knowledge-base.md)の FAQ などの半構造化コンテンツから質問とその回答を自動的に抽出します。 ナレッジ ベースのモデルは、API 要求の本文で送信される JSON で定義されます。

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>前提条件

* 最新バージョンの [cURL](https://curl.haxx.se/)。 クイックスタートでは、いくつかのコマンド ライン スイッチが使用されています。これらのスイッチについては、[cURL のドキュメント](https://curl.haxx.se/docs/manpage.html)を参照してください。
* [QnA Maker リソース](../How-To/set-up-qnamaker-service-azure.md)が必要です。 キーとリソース名を取得するには、Azure portal で目的のリソースの **[クイックスタート]** を選択します。 リソース名は、エンドポイントの URL の最初の構成要素です。

    `https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> 以降に掲載した BASH の例では、行連結文字として `\` を使用しています。 ご利用のコンソールまたはターミナルで異なる行連結文字が使用されている場合は、この文字を使用してください。

## <a name="create-a-knowledge-base"></a>ナレッジ ベースの作成

REST API と cURL を使用してナレッジ ベースを作成するには、次の情報が必要です。

|Information|cURL の構成|目的|
|--|--|--|
|QnA Maker リソースの名前|URL|URL の構築用|
|QnA Maker リソースのキー|`Ocp-Apim-Subscription-Key` ヘッダーの `-h` パラメーター|QnA Maker サービスに対する認証|
|ナレッジ ベースを表す JSON|`-d` パラメーター|JSON の[例](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples)|
|JSON のサイズ (バイト単位)|`Content-Size` ヘッダーの `-h` パラメーター||

cURL コマンドは、BASH シェルから実行します。 コマンドは、実際のリソース名、リソース キー、JSON の値、JSON のサイズに合わせて編集してください。

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

QnA Maker から返される cURL の応答には `operationId` が含まれており、[操作の状態を取得](#get-status-of-operation)するためには、この ID が必要となります。

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

## <a name="get-status-of-operation"></a>操作の状態を取得する

ナレッジ ベースを作成するとき、操作は非同期で実行されるため、その状態を判別する情報が応答には含まれています。

|Information|cURL の構成|目的|
|--|--|--|
|QnA Maker リソースの名前|URL|URL の構築用|
|Operation Id|URL ルート|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|QnA Maker リソースのキー|`Ocp-Apim-Subscription-Key` ヘッダーの `-h` パラメーター|QnA Maker サービスに対する認証|

cURL コマンドは、BASH シェルから実行します。 コマンドは、実際のリソース名、リソース キー、操作 ID に合わせて編集してください。

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

cURL の応答には状態が含まれています。 操作の状態が成功である場合は、`resourceLocation` にナレッジ ベース ID が含まれています。

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

## <a name="publish-knowledge-base"></a>ナレッジ ベースを公開する

ナレッジ ベースにクエリを実行する前に、次のことを行う必要があります。
* ナレッジ ベースを公開する
* ランタイム エンドポイント キーを取得する

このタスクでは、ナレッジ ベースを公開します。 ランタイム エンドポイント キーの取得は、[別のタスク](#get-published-knowledge-bases-runtime-endpoint-key)です。

|Information|cURL の構成|目的|
|--|--|--|
|QnA Maker リソースの名前|URL|URL の構築用|
|QnA Maker リソースのキー|`Ocp-Apim-Subscription-Key` ヘッダーの `-h` パラメーター|QnA Maker サービスに対する認証|
|ナレッジ ベース ID|URL ルート|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

cURL コマンドは、BASH シェルから実行します。 コマンドは、実際のリソース名、リソース キー、ナレッジ ベース ID に合わせて編集してください。

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

応答状態は、結果を伴わない 204 です。 cURL コマンドの詳細出力を表示するには、`-v` コマンド ライン パラメーターを使用してください。 これには HTTP の状態が含まれます。

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>公開されたナレッジ ベースのランタイム エンドポイント キーを取得する

ナレッジ ベースにクエリを実行する前に、次のことを行う必要があります。
* ナレッジ ベースを公開する
* ランタイム エンドポイント キーを取得する

このタスクでは、ランタイム エンドポイント キーを取得します。 ナレッジ ベースの公開は、[別のタスク](#publish-knowledge-base)です。

ランタイム エンドポイント キーは、QnA Maker リソースを使用するすべてのナレッジ ベースに共通するキーです。

|Information|cURL の構成|目的|
|--|--|--|
|QnA Maker リソースの名前|URL|URL の構築用|
|QnA Maker リソースのキー|`Ocp-Apim-Subscription-Key` ヘッダーの `-h` パラメーター|QnA Maker サービスに対する認証|

cURL コマンドは、BASH シェルから実行します。 コマンドは、実際のリソース名とリソース キーに合わせて編集してください。

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


cURL の応答には、ランタイム エンドポイント キーが含まれています。 ナレッジ ベースから回答を取得するためのクエリを実行する際は、これらのキーのうち 1 つだけを使用します。

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

## <a name="query-for-answer-from-published-knowledge-base"></a>公開されたナレッジ ベースから回答を得るためのクエリを実行する

ナレッジ ベースからの回答の取得は、ナレッジ ベースの管理とは別のランタイムから行います。 これは独立したランタイムであるため、ランタイム キーを使用して認証する必要があります。

|Information|cURL の構成|目的|
|--|--|--|
|QnA Maker リソースの名前|URL|URL の構築用|
|QnA Maker のランタイム キー|`Authorization` ヘッダーの `-h` パラメーター|キーは、`Endpointkey ` という単語を含む文字列の一部です。 QnA Maker サービスに対する認証|
|ナレッジ ベース ID|URL ルート|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|クエリを表す JSON|`-d` パラメーター|JSON の[要求本文のパラメーター](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body)と[例](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples)|
|JSON のサイズ (バイト単位)|`Content-Size` ヘッダーの `-h` パラメーター||

cURL コマンドは、BASH シェルから実行します。 コマンドは、実際のリソース名、リソース キー、ナレッジ ベース ID に合わせて編集してください。

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

成功の応答には、上位の回答が含まれるほか、クライアント アプリケーション (チャット ボットなど) がユーザーに回答を表示する際に必要となる情報も含まれます。

## <a name="delete-knowledge-base"></a>ナレッジ ベースの削除

ナレッジ ベースの使用を終了したら、それを削除します。

|Information|cURL の構成|目的|
|--|--|--|
|QnA Maker リソースの名前|URL|URL の構築用|
|QnA Maker リソースのキー|`Ocp-Apim-Subscription-Key` ヘッダーの `-h` パラメーター|QnA Maker サービスに対する認証|
|ナレッジ ベース ID|URL ルート|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

cURL コマンドは、BASH シェルから実行します。 コマンドは、実際のリソース名、リソース キー、ナレッジ ベース ID に合わせて編集してください。

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

応答状態は、結果を伴わない 204 です。 cURL コマンドの詳細出力を表示するには、`-v` コマンド ライン パラメーターを使用してください。 これには HTTP の状態が含まれます。

## <a name="additional-resources"></a>その他のリソース

* [作成](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)のリファレンス ドキュメント
* [ランタイム](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/)のリファレンス ドキュメント
* [cURL を使用したサンプル BASH スクリプト](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2092179)
