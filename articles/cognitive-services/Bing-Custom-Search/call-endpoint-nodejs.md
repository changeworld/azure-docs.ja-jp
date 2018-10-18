---
title: 'クイック スタート: Node.js を使用してエンドポイントを呼び出す - Bing Custom Search'
titlesuffix: Azure Cognitive Services
description: このクイックスタートでは、Node.js を利用して Bing Custom Search エンドポイントを呼び出すことで、カスタム検索インスタンスから検索結果を要求する方法について紹介します。
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: af77b4c06b61cda4fd18d19ac3578129004c4914
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167207"
---
# <a name="quickstart-call-bing-custom-search-endpoint-nodejs"></a>クイック スタート: Bing Custom Search エンドポイントを呼び出す (Node.js)

このクイック スタートでは、Node.js を利用して Bing Custom Search エンドポイントを呼び出すことで、カスタム検索インスタンスから検索結果を要求する方法を紹介します。 

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

- すぐに使用できるカスタム検索インスタンス。 「[Create your first Bing Custom Search instance](quick-start.md)」 (最初の Bing Custom Search インスタンスを作成する) を参照してください。
- [Node.js](https://www.nodejs.org/) がインストールされていること。
- サブスクリプション キー。 [無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)をアクティブにすると、サブスクリプション キーを取得できます。または Azure ダッシュボードから有料のサブスクリプション キーを使用することもできます ([Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を参照してください)。    

## <a name="run-the-code"></a>コードの実行

この例を実行するには、次の手順に従います。

1. コードのフォルダーを作成します。  
  
2. コマンド プロンプトまたはターミナルから、先ほど作成したフォルダーに移動します。  
  
3. **request** ノード モジュールをインストールします。
    <pre>
    npm install request
    </pre>  
    
4. 作成したフォルダー内に BingCustomSearch.js という名前のファイルを作成し、次のコードをコピーします。 **YOUR-SUBSCRIPTION-KEY** と **YOUR-CUSTOM-CONFIG-ID** を、自分のサブスクリプション キーと構成 ID に置き換えます。  
  
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
