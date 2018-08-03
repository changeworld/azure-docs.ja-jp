---
title: Node.js を利用してエンドポイントを呼び出す - Bing Custom Search - Microsoft Cognitive Services
description: このクイックスタートでは、Node.js を利用して Bing Custom Search エンドポイントを呼び出すことで、カスタム検索インスタンスから検索結果を要求する方法について紹介します。
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 5d9391cc486dc868a1a291ccc7095291cddd3e4c
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858461"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Bing Custom Search エンドポイントを呼び出す (Node.js)

このクイックスタートでは、Node.js を利用して Bing Custom Search エンドポイントを呼び出すことで、カスタム検索インスタンスから検索結果を要求する方法について紹介します。 

## <a name="prerequisites"></a>前提条件
このクイック スタートを完了するには、次のものが必要です。

- カスタム検索インスタンス。 「[Create your first Bing Custom Search instance](quick-start.md)」 (最初の Bing Custom Search インスタンスを作成する) を参照してください。

- [Node.js](https://www.nodejs.org/) がインストールされていること。

-  [Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と **Bing Search API**。 このクイックスタートには[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)で十分です。 無料試用版を起動するとき、アクセス キーを入力する必要があります。あるいは、Azure ダッシュボードの有料サブスクリプション キーを使用できます。

## <a name="run-the-code"></a>コードの実行

Bing Custom Search エンドポイントは次の手順で呼び出します。

1. コードのフォルダーを作成します。

2. コマンド プロンプトまたはターミナルから、先ほど作成したフォルダーに移動します。

3. **request** ノード モジュールをインストールします。
    <pre>
    npm install request
    </pre>
    
4. ファイル BingCustomSearch.js を作成し、それに次のコードをコピーします。

5. **YOUR-SUBSCRIPTION-KEY** と **YOUR-CUSTOM-CONFIG-ID** をそれぞれ、自分のキーと構成 ID に変更します (手順 1 参照)。

    ``` javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    
    var options = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
          'q=' + searchTerm + 
          '&customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    
    request(options, function(error, response, body){
        var searchResponse = JSON.parse(body);
        for(var i = 0; i < searchResponse.webPages.value.length; ++i){
            var webPage = searchResponse.webPages.value[i];
            console.log('name: ' + webPage.name);
            console.log('url: ' + webPage.url);
            console.log('displayUrl: ' + webPage.displayUrl);
            console.log('snippet: ' + webPage.snippet);
            console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
            console.log();
        }
    })
    ```
6. 次のコマンドでコードを実行します。
    ```    
    node BingCustomSearch.js
   ``` 

## <a name="next-steps"></a>次の手順
- [ホストされている UI エクスペリエンスの構成](./hosted-ui.md)
- [装飾マーカーを使用してテキストを強調表示する](./hit-highlighting.md)
- [Web ページのページング](./page-webpages.md)
