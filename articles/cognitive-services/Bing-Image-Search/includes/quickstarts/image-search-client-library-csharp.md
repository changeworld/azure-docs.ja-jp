---
title: Bing Image Search C# クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2020
ms.author: aahi
ms.openlocfilehash: e051eca990ae0aa0b5a79c208a594e1b2332bcb2
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2020
ms.locfileid: "97612657"
---
このクイックスタートでは、Bing Image Search クライアント ライブラリを使用して最初の画像検索を行います。 

このクライアント検索ライブラリは、REST API のラッパーであり、同じ機能を含んでいます。 

ここでは、画像検索クエリを送信し、JSON 応答を解析して、返された最初の画像の URL を表示する C# アプリケーションを作成します。

このサンプルのソース コードは、追加のエラー処理と注釈を含め、[GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) で入手できます。

## <a name="prerequisites"></a>前提条件

* (Windows を使用している場合) [Visual Studio 2017 以降](https://visualstudio.microsoft.com/vs/whatsnew/)のエディション
* (macOS または Linux を使用している場合) [VS Code](https://code.visualstudio.com) と [.NET Core がインストール](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)されていること
* [無料の Azure サブスクリプション](https://azure.microsoft.com/free/dotnet)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

「[Cognitive Services の価格 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)」もご覧ください。

## <a name="create-a-console-project"></a>コンソール プロジェクトを作成する

まず、新しい C# コンソール アプリケーションを作成します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visualstudio)

1. Visual Studio で、*BingImageSearch* という新しいコンソール ソリューションを作成します。
    
1. [Cognitive Image Search NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch)を追加します。
    1. **ソリューション エクスプローラー** でプロジェクトを右クリックします。
    1. **[NuGet パッケージの管理]** を選択します。
    1. *Microsoft.Azure.CognitiveServices.Search.ImageSearch* を検索して選択し、このパッケージをインストールします。
    
# <a name="vs-code"></a>[VS Code](#tab/vscode)

1. VS Code でターミナル ウィンドウを開きます。
1. ターミナル ウィンドウに次のコードを入力して、*BingImageSearch* という名前の新しいコンソール プロジェクトを作成します。
    
    ```bash
    dotnet new console -n BingImageSearch
    ```
1. VS Code で *BingImageSearch* フォルダーを開きます。
1. ターミナル ウィンドウに次のコードを入力して、[[Cognitive Image Search NuGet package]\(Cognitive Image Search NuGet パッケージ\)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch) という NuGetPackage を追加します。

    ```bash
    dotnet add package Microsoft.Azure.CognitiveServices.Search.ImageSearch
    ```

---

## <a name="initialize-the-application"></a>アプリケーションを初期化する


1. *Program.cs* 内のすべての `using` ステートメントを次のコードに置き換えます。

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
    ```

1. プロジェクトの `Main` メソッドでは、有効なサブスクリプション キー、Bing で返す必要のある画像の結果、および検索語句の変数を作成します。 その後、キーを使用して画像検索クライアントをインスタンス化します。

    ```csharp
    static async Task Main(string[] args)
    {
        //IMPORTANT: replace this variable with your Cognitive Services subscription key
        string subscriptionKey = "ENTER YOUR KEY HERE";
        //stores the image results returned by Bing
        Images imageResults = null;
        // the image search term to be used in the query
        string searchTerm = "canadian rockies";
        
        //initialize the client
        //NOTE: If you're using version 1.2.0 or below for the Bing Image Search client library, 
        // use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.
        
        var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
    }
    ```
    
## <a name="send-a-search-query-using-the-client"></a>クライアントを使用して検索クエリを送信する
    
引き続き `Main` メソッドで、クライアントを使用してクエリ テキストを検索します。
    
```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = await client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>最初の画像の結果を解析して表示する

応答で返された画像の結果を解析します。 

応答に検索結果が含まれている場合は、最初の結果を格納して、その詳細の一部を出力します。

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.WriteLine("Press any key to exit ...");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Bing Image Search の単一ページ アプリのチュートリアル](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>関連項目

* [Bing Image Search とは](../../overview.md)  
* [オンラインの対話型デモを試す](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Azure Cognitive Services SDK の .NET サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Azure Cognitive Services のドキュメント](../../../index.yml)
* [Bing Image Search API リファレンス](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)