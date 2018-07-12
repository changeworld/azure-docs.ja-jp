---
title: Bing Search SDK | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Web を検索するアプリケーション用の Bing Web Search SDK。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.assetid: ''
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 1/11/2018
ms.author: v-gedod
ms.openlocfilehash: 9d2f363d772febfb9fffd2d9c2924398e438d6e0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377944"
---
# <a name="bing-search-sdk"></a>Bing Search SDK
Bing Web Search API のサンプルには、次のようなシナリオが含まれています。
1. 1 単語のクエリを使用して結果を取得し、Web、画像、ニュース、および動画の各結果の最初の名前と URL を出力します。
2. フレーズに対してクエリを実行し、結果の数を確認し、最初の結果の名前と URL を出力します。
3. 応答フィルターを `news` に設定して検索語句のクエリを実行し、ニュース結果の詳細を出力します。
4. `answerCount` および `promote` パラメーターを使用して検索語句のクエリを実行し、結果の詳細を出力します。

Bing Search SDK を使用すると、次のプログラミング言語で簡単に Web 検索機能にアクセスできるようになります。
* [.NET サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)を使ってみる 
    * [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
    * 定義と依存関係については、[.NET ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingWebSearch)も参照してください。
* [Node.js サンプル](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)を使ってみる 
    * 定義と依存関係については、[Node.js ライブラリ](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/webSearch)も参照してください。
* [Java サンプル](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)を使ってみる 
    * 定義と依存関係については、[Java ライブラリ](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch)も参照してください。
* [Python サンプル](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)を使ってみる 
    * 定義と依存関係については、[Python ライブラリ](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-websearch)も参照してください。

各言語の SDK サンプルには、前提条件と、サンプルのインストール/実行の詳細が記載された ReadMe ファイルが含まれています。
