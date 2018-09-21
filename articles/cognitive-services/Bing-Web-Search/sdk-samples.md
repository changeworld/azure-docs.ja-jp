---
title: Bing Web Search SDK のサンプル
titleSuffix: Azure Cognitive Services
description: Bing Web Search SDK を使用して、Python、Node.js、C#、または Java アプリケーションに検索機能を追加します。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: sample
ms.date: 08/16/2018
ms.author: erhopf
ms.openlocfilehash: b15f4000d30b7b6c6f8055d596b9ab5336d173c5
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126022"
---
# <a name="bing-web-search-sdk-samples"></a>Bing Web Search SDK のサンプル

Bing Web Search SDK は、Python、Node.js、C#、および Java で使用することができます。 GitHub では、コード サンプル、前提条件、およびビルド手順が提供されています。 次のシナリオが扱われています。

* 1 つの単語のクエリを実行し、Web ページ、画像、ニュース記事、および動画に対する最初の結果の名前と URL を出力します。
* フレーズに対してクエリを実行し、結果の数を確認し、最初の結果の名前と URL を出力します。
* 応答フィルターを `news` に設定して検索語句のクエリを実行し、ニュース結果の詳細を出力します。
* `answerCount` および `promote` パラメーターを使用して検索語句のクエリを実行し、結果の詳細を出力します。

## <a name="sdks-and-libraries"></a>SDK とライブラリ

目的の言語の SDK にアクセスするには、次のリンクを使用してください。

* [Python サンプル](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)を使ってみる
  * 定義と依存関係については、[Python ライブラリ](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-websearch)も参照してください。
* [Node.js サンプル](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)を使ってみる
  * 定義と依存関係については、[Node.js ライブラリ](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/webSearch)も参照してください。
* [.NET サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)を使ってみる
  * [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
  * 定義と依存関係については、[.NET ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingWebSearch)も参照してください。
* [Java サンプル](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)を使ってみる
  * 定義と依存関係については、[Java ライブラリ](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch)も参照してください。
