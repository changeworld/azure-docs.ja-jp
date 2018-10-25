---
title: Bing News Search SDK
titleSuffix: Azure Cognitive Services
description: Web を検索するアプリケーション用の Bing News Search SDK。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: b1d9eaa35416adfa11647f2116171256f82fe095
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801235"
---
# <a name="bing-search-sdk"></a>Bing Search SDK
Bing News Search API のサンプルには、次のようなシナリオが含まれています。
1. `market` および `count` パラメーターを使用して、検索語句についてニュースのクエリを実行し、結果の数を確認し、最初のニュース結果の `totalEstimatedMatches`、名前、URL、説明、公開時刻、および提供元の名前を出力します。
2. `freshness` および `sortBy` パラメーターを使用して、検索語句について最近のニュースのクエリを実行し、結果の数を確認し、最初のニュース結果の `totalEstimatedMatches`、URL、説明、公開時刻、および提供元の名前を出力します。
3. セーフ サーチを使用して、`movie` および `TV entertainment` に関するカテゴリのニュースのクエリを実行し、結果の数を確認し、最初のニュース結果のカテゴリ、名前、URL、説明、公開時刻、および提供元の名前を出力します。
4. Bing の新しいトレンドのトピックのクエリを実行し、結果の数を確認し、最初のニュース結果の名前、クエリのテキスト、`webSearchUrl`、`newsSearchUrl`、および画像の URL を出力します。

Bing Search SDK を使用すると、次のプログラミング言語で簡単に Web 検索機能にアクセスできるようになります。
* [.NET サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)を使ってみる
    * [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0)
    * 定義と依存関係については、[.NET ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingNewsSearch)も参照してください。
* [Node.js サンプル](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)を使ってみる 
    * 定義と依存関係については、[Node.js ライブラリ](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/newsSearch)も参照してください。
* [Java サンプル](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)を使ってみる 
    * 定義と依存関係については、[Java ライブラリ](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch)も参照してください。
* [Python サンプル](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)を使ってみる 
    * 定義と依存関係については、[Python ライブラリ](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-newssearch)も参照してください。

各言語の SDK サンプルには、前提条件と、サンプルのインストール/実行の詳細が記載された ReadMe ファイルが含まれています。