---
title: 'クイックスタート: cURL と REST を使用してナレッジ ベースを管理する - カスタム質問応答'
description: このクイックスタートでは、REST API を使用してナレッジ ベースを作成、公開し、クエリを実行する方法について説明します。
ms.date: 11/02/2021
ms.topic: include
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2768081da14be2c481093d54c833f1cde45e36a9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029791"
---
## <a name="prerequisites"></a>前提条件

* 最新バージョンの [cURL](https://curl.haxx.se/)。 クイックスタートでは、いくつかのコマンド ライン スイッチが使用されています。これらのスイッチについては、[cURL のドキュメント](https://curl.haxx.se/docs/manpage.html)を参照してください。
* カスタム質問応答には、API キーとエンドポイントを生成するために、カスタム質問応答機能を有効にした[言語リソース](../../../qnamaker/how-to/set-up-qnamaker-service-azure.md?tabs=v2#create-a-new-qna-maker-service)が必要です。 リソースの作成時に選択した **名前** が、エンドポイントのサブドメインとして使用されます。 キーとリソース名を取得するには、Azure portal で目的のリソースの **[クイックスタート]** を選択します。 リソース名は、エンドポイントの URL の最初のサブドメインです。 <!--TODO: Change link-->

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2`

> [!CAUTION]
> 以降に掲載した BASH の例では、行連結文字として `\` を使用しています。 ご利用のコンソールまたはターミナルで異なる行連結文字が使用されている場合は、この文字を使用してください。

## <a name="create-a-knowledge-base"></a>ナレッジ ベースの作成

REST API と cURL を使用してナレッジ ベースを作成するには、次の情報が必要です。

|Information|cURL の構成|目的|
|--|--|--|
|(カスタム質問応答機能を有効にした) 言語リソース名|URL|URL の構築用|
|言語リソース キー。|`Ocp-Apim-Subscription-Key` ヘッダーの `-h` パラメーター|Language サービスに対する認証|
|ナレッジ ベースを表す JSON|`-d` パラメーター|JSON の[例](/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples)|
|JSON のサイズ (バイト単位)|`Content-Size` ヘッダーの `-h` パラメーター||

cURL コマンドは、BASH シェルから実行します。 コマンドは、実際のリソース名、リソース キー、JSON の値、JSON のサイズに合わせて編集してください。

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

カスタム質問応答から返される cURL の応答には `operationId` が含まれており、[操作の状態を取得](#get-status-of-operation)するためには、この ID が必要となります。

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
|(カスタム質問応答機能を有効にした) 言語リソース名|URL|URL の構築用|
|操作 ID|URL ルート|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|言語リソース キー。|`Ocp-Apim-Subscription-Key` ヘッダーの `-h` パラメーター|Language サービスに対する認証|

cURL コマンドは、BASH シェルから実行します。 コマンドは、実際のリソース名、リソース キー、操作 ID に合わせて編集してください。

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/operations/REPLACE-WITH-YOUR-OPERATION-ID \
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

ナレッジ ベースに質問する前に、ナレッジ ベースを公開する必要があります。

|Information|cURL の構成|目的|
|--|--|--|
|(カスタム質問応答機能を有効にした) 言語リソース名|URL|URL の構築用|
|言語リソース キー。|`Ocp-Apim-Subscription-Key` ヘッダーの `-h` パラメーター|Language サービスに対する認証|
|ナレッジ ベース ID|URL ルート|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

cURL コマンドは、BASH シェルから実行します。 コマンドは、実際のリソース名、リソース キー、ナレッジ ベース ID に合わせて編集してください。

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

応答状態は、結果を伴わない 204 です。 cURL コマンドの詳細出力を表示するには、`-v` コマンド ライン パラメーターを使用してください。 これには HTTP の状態が含まれます。

## <a name="query-for-answer-from-published-knowledge-base"></a>公開されたナレッジ ベースから回答を得るためのクエリを実行する

|Information|cURL の構成|目的|
|--|--|--|
|(カスタム質問応答機能を有効にした) 言語リソース名|URL|URL の構築用|
|言語リソース キー。|`Ocp-Apim-Subscription-Key` ヘッダーの `-h` パラメーター|Language サービスに対する認証|
|ナレッジ ベース ID|URL ルート|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|クエリを表す JSON|`-d` パラメーター|JSON の[要求本文のパラメーター](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body)と[例](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples)|
|JSON のサイズ (バイト単位)|`Content-Size` ヘッダーの `-h` パラメーター||

cURL コマンドは、BASH シェルから実行します。 コマンドは、実際のリソース名、リソース キー、ナレッジ ベース ID に合わせて編集してください。

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

成功の応答には、上位の回答が含まれるほか、クライアント アプリケーション (チャット ボットなど) がユーザーに回答を表示する際に必要となる情報も含まれます。

## <a name="delete-knowledge-base"></a>ナレッジ ベースの削除

ナレッジ ベースの使用を終了したら、それを削除します。

|Information|cURL の構成|目的|
|--|--|--|
|(カスタム質問応答機能を有効にした) 言語リソース名|URL|URL の構築用|
|言語リソース キー。|`Ocp-Apim-Subscription-Key` ヘッダーの `-h` パラメーター|Language サービスに対する認証|
|ナレッジ ベース ID|URL ルート|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

cURL コマンドは、BASH シェルから実行します。 コマンドは、実際のリソース名、リソース キー、ナレッジ ベース ID に合わせて編集してください。

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

応答状態は、結果を伴わない 204 です。 cURL コマンドの詳細出力を表示するには、`-v` コマンド ライン パラメーターを使用してください。 これには HTTP の状態が含まれます。

## <a name="additional-resources"></a>その他のリソース

* [作成](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)のリファレンス ドキュメント
* [ランタイム](/rest/api/cognitiveservices/qnamaker4.0/runtime)のリファレンス ドキュメント
* [cURL を使用したサンプル BASH スクリプト](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)
