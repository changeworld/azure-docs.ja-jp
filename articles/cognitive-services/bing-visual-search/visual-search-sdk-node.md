---
title: 'クイック スタート: Bing Visual Search SDK (Node)'
titleSuffix: Azure Cognitive Services
description: Visual Search SDK の Node コンソール アプリケーションの設定。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: v-gedod
ms.openlocfilehash: cca39fb16c47eb13887f6198833dafc64974d301
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444462"
---
# <a name="quickstart-bing-visual-search-sdk-node"></a>クイック スタート: Bing Visual Search SDK Node 

Bing Visual Search SDK では、Web 要求と結果解析に REST API の機能を使用します。
[Node 向けの Visual Search SDK のサンプル ソース コード](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js)は、Git Hub で公開しています。

コード シナリオは次の見出しのセクションに記載しています。
* [Visual Search クライアント](#client)
* [コンソール アプリケーションの全体像](#complete)

## <a name="prerequisites"></a>前提条件

* このクイック スタートでは、「[Cognitive Services の価格 - Bing Search API](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/)」に記載されている S9 価格帯のサブスクリプションを開始する必要があります。 

Azure portal でサブスクリプションを開始するには、次の手順に従います。
1. Azure portal の一番上の "`Search resources, services, and docs`" と表示されているテキスト ボックスに「BingSearchV7」と入力します。  
2. ドロップダウン リストの [Marketplace] から `Bing Search v7` を選択します。
3. 新しいリソースの名前を [`Name`] に入力します。
4. `Pay-As-You-Go` サブスクリプションを選択します。
5. `S9` 価格レベルを選択します。
6. [`Enable`] をクリックしてサブスクリプションを開始します。

## <a name="application-dependencies"></a>アプリケーションの依存関係

Bing Visual Search SDK を使用してコンソール アプリケーションを設定するには:
* `npm install ms-rest-azure` を実行します。
* `npm install azure-cognitiveservices-search-visualSearch` を実行します。

<a name="client"></a>
## <a name="visual-search-client"></a>Visual Search クライアント
`VisualSearchAPI` クライアントのインスタンスを作成するには、次のディレクティブを追加します。
```
const Search = require('azure-cognitiveservices-visualsearch');
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;

```
次に、クライアントをインスタンス化します。
```
let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
let credentials = new CognitiveServicesCredentials(keyVar);
let visualSearchApiClient = new Search.VisualSearchAPIClient(credentials);

```
クライアントを使用して画像を検索します。
```
let fileStream = fs.createReadStream('../Data/image.jpg');
let visualSearchRequest = JSON.stringify({});
let visualSearchResults;
try {
    visualSearchResults = await visualSearchApiClient.images.visualSearch({
        image: fileStream,
        knowledgeRequest: visualSearchRequest
    });
    console.log("Search visual search request with binary of image");
} catch (err) {
    console.log("Encountered exception. " + err.message);
}

```
上記のクエリの結果を解析します。
```
// Visual Search results
if (visualSearchResults.image.imageInsightsToken) {
    console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
}
else {
    console.log("Couldn't find image insights token!");
}

// List of tags
if (visualSearchResults.tags.length > 0) {
    let firstTagResult = visualSearchResults.tags[0];
    console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

    // List of actions in first tag
    if (firstTagResult.actions.length > 0) {
        let firstActionResult = firstTagResult.actions[0];
        console.log(`First tag action count: ${firstTagResult.actions.length}`);
        console.log(`First tag action type: ${firstActionResult.actionType}`);
    }
    else {
        console.log("Couldn't find tag actions!");
    }

}
else {
    console.log("Couldn't find image tags!");
}

```
<a name="complete"></a>
## <a name="complete-console-application"></a>コンソール アプリケーションの全体像

次のコンソール アプリケーションは、上記のコードと他のシナリオを実行し、結果を解析します。
```
/*
 * Copyright (c) Microsoft Corporation. All rights reserved.
 * Licensed under the MIT License. See License.txt in the project root for
 * license information.
 */
'use strict';

const os = require("os");
const async = require('async');
const fs = require('fs');
const Search = require('azure-cognitiveservices-visualsearch');
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;


let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';

let credentials = new CognitiveServicesCredentials(keyVar);
let visualSearchApiClient = new Search.VisualSearchAPIClient(credentials);
let visualModels = visualSearchApiClient.models;
sample();

function sample() {
    async.series([
        async function () {
            let fileStream = fs.createReadStream('../Data/image.jpg');
            let visualSearchRequest = JSON.stringify({});
            let visualSearchResults;
            try {
                visualSearchResults = await visualSearchApiClient.images.visualSearch({
                    image: fileStream,
                    knowledgeRequest: visualSearchRequest
                });
                console.log("Visual search request with binary of dog image");
            } catch (err) {
                console.log("Encountered exception. " + err.message);
            }
            if (!visualSearchResults) {
                console.log("No visual search result data. ");
            } else {
                    // Visual Search results
                    if (visualSearchResults.image.imageInsightsToken) {
                        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
                    }
                else {
                    console.log("Couldn't find image insights token!");
                }

                // List of tags
                if (visualSearchResults.tags.length > 0) {
                    let firstTagResult = visualSearchResults.tags[0];
                    console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

                    // List of actions in first tag
                    if (firstTagResult.actions.length > 0) {
                        let firstActionResult = firstTagResult.actions[0];
                        console.log(`First tag action count: ${firstTagResult.actions.length}`);
                        console.log(`First tag action type: ${firstActionResult.actionType}`);
                    }
                    else {
                        console.log("Couldn't find tag actions!");
                    }

                }
                else {
                    console.log("Couldn't find image tags!");
                }
            }
        },
        async function () {
            let fileStream = fs.createReadStream('../Data/image.jpg');
            let cropArea = { top: 0.1, bottom: 0.5, left: 0.1, right: 0.9 };
            let imageInfo = { cropArea: cropArea };
            let visualSearchRequest = JSON.stringify({ imageInfo: imageInfo });
            let visualSearchResults;
            try {
                visualSearchResults = await visualSearchApiClient.images.visualSearch({
                    image: fileStream,
                    knowledgeRequest: visualSearchRequest
                });
                console.log("\r\nVisual search request with binary of image with JSON info of cropArea");
                if (!visualSearchResults) {
                    console.log("No visual search result data.");
                }
                else {
                    // Visual Search results
                    if (visualSearchResults.image.imageInsightsToken) {
                        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
                    }
                    else {
                        console.log("Couldn't find image insights token!");
                    }

                    // List of tags
                    if (visualSearchResults.tags.length > 0) {
                        var firstTagResult = visualSearchResults.tags[0];
                        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

                        // List of actions in first tag
                        if (firstTagResult.actions.length > 0) {
                            var firstActionResult = firstTagResult.actions[0];
                            console.log(`First tag action count: ${firstTagResult.actions.length}`);
                            console.log(`First tag action type: ${firstActionResult.actionType}`);
                        }
                        else {
                            console.log("Couldn't find tag actions!");
                        }

                    }
                    else {
                        console.log("Couldn't find image tags!");
                    }
                }
            } catch (err) {
                console.log("Encountered exception. " + err.message);
            }
        },
        async function () {
            let imageInfo = { url: "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80" };
            let filters = { site: "pinterest.com" };
            let knowledgeRequest = { filters: filters };
            let visualSearchRequest = JSON.stringify({ imageInfo: imageInfo, knowledgeRequest: knowledgeRequest });
            let visualSearchResults;
            try {
                visualSearchResults = await visualSearchApiClient.images.visualSearch({
                    knowledgeRequest: visualSearchRequest
                });
                console.log("\r\nSearch visual search request with imageInfo and filters in JSON text");
                if (!visualSearchResults) {
                    console.log("No visual search result data.");
                }
                else {
                    // Visual Search results
                    if (visualSearchResults.image.imageInsightsToken) {
                        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
                    }
                    else {
                        console.log("Couldn't find image insights token!");
                    }

                    // List of tags
                    if (visualSearchResults.tags.length > 0) {
                        var firstTagResult = visualSearchResults.tags[0];
                        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

                        // List of actions in first tag
                        if (firstTagResult.actions.length > 0) {
                            var firstActionResult = firstTagResult.actions[0];
                            console.log(`First tag action count: ${firstTagResult.actions.length}`);
                            console.log(`First tag action type: ${firstActionResult.actionType}`);
                        }
                        else {
                            console.log("Couldn't find tag actions!");
                        }

                    }
                    else {
                        console.log("Couldn't find image tags!");
                    }
                }
            } catch (err) {
                console.log("Encountered exception. " + err.message);
            }
        },
        async function () {
            var imageInsightsToken = "bcid_CA6BDBEA28D57D52E0B9D4B254F1DF0D*ccid_6J+8V1zi*thid_R.CA6BDBEA28D57D52E0B9D4B254F1DF0D";
            let cropArea = { top: 0.1, bottom: 0.5, left: 0.1, right: 0.9 };
            let imageInfo = { imageInsightsToken: imageInsightsToken, cropArea: cropArea };
            let visualSearchRequest = JSON.stringify({ imageInfo: imageInfo });
            let visualSearchResults;
            try {
                visualSearchResults = await visualSearchApiClient.images.visualSearch({
                    knowledgeRequest: visualSearchRequest
                });
                console.log("\r\nVisual search request with ImageInsightsToken and crop area of image");
                if (!visualSearchResults) {
                    console.log("No visual search result data.");
                }
                else {
                    // Visual Search results
                    if (visualSearchResults.image.imageInsightsToken) {
                        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
                    }
                    else {
                        console.log("Couldn't find image insights token!");
                    }

                    // List of tags
                    if (visualSearchResults.tags.length > 0) {
                        var firstTagResult = visualSearchResults.tags[0];
                        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

                        // List of actions in first tag
                        if (firstTagResult.actions.length > 0) {
                            var firstActionResult = firstTagResult.actions[0];
                            console.log(`First tag action count: ${firstTagResult.actions.length}`);
                            console.log(`First tag action type: ${firstActionResult.actionType}`);
                        }
                        else {
                            console.log("Couldn't find tag actions!");
                        }

                    }
                    else {
                        console.log("Couldn't find image tags!");
                    }
                }
            } catch (err) {
                console.log("Encountered exception. " + err.message);
            }
        },
        function () {
            return new Promise((resolve) => {
                console.log(os.EOL);
                console.log("Finished running Visual-Search sample.");
                resolve();
            })
        }
    ], (err) => {
        throw (err);
        });
}

exports.sample = sample;
```

## <a name="next-steps"></a>次の手順

[Cognitive Services の .NET 向け SDK のサンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)。
