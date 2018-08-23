---
title: Bing Visual Search SDK の ImageInsightsToken のチュートリアル | Microsoft Docs
description: Bing Visual Search SDK を使用して ImageInsightsToken で指定された画像の URL を取得する方法。
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 8f6e7f7e88ae78fe7e8a9be4adefd689dd26d0f9
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2018
ms.locfileid: "41929978"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>チュートリアル: Bing Visual Search SDK の ImageInsightsToken と結果
Visual Search SDK には、前に実行された `ImageInsightsToken` を返す検索からオンラインで画像を検索するオプションがあります。  この例では `ImageInsightsToken` を取得し、このトークンをその後の検索で使用します。  コードでは、`ImageInsightsToken` を Bing に送信し、Bing Search URL とオンラインで見つかった類似する画像の URL を含む結果を返します。

## <a name="prerequisites"></a>前提条件
Visual Studio 2017。 必要に応じて、https://www.visualstudio.com/vs/community/ から無料のコミュニティ バージョンをダウンロードすることができます。
SDK の呼び出しを認証するには、Cognitive Services API キーが必要です。 無料試用版キーにサインアップしてください。 試用版キーは 1 秒あたり 1 回の呼び出しが可能で、有効期間は 7 日間です。 運用環境のシナリオでは、アクセス キーを購入してください。 価格情報も参照してください。
.NET Core SDK、.NET Core 1.1 アプリを実行する機能。 https://www.microsoft.com/net/download/ から、Core、Framework、およびランタイムを入手できます。

##<a name="application-dependencies"></a>アプリケーションの依存関係
Bing Web Search SDK を使用してコンソール アプリケーションを設定するには、Visual Studio のソリューション エクスプローラーで [NuGet パッケージの管理] オプションに移動します。 次の項目を追加します。
* Microsoft.Azure.CognitiveServices.Search.VisualSearch
* Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage パッケージ。

NuGet Web Search SDK パッケージをインストールすると、次の項目を含む依存関係もインストールされます。

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="get-the-imageinsightstoken-from-image-search"></a>Image Search から ImageInsightsToken を取得する
この例では、次のメソッドで取得した `ImageInsightsToken` を使用しています。  この呼び出しの詳細については、「[Image Search SDK C# quickstart (Image Search SDK C# のクイック スタート)](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart)」を参照してください。

このコードでは、"Canadian Rockies" のクエリ結果を検索し、ImageInsightsToken を取得します。 最初の画像の分析情報トークン、サムネイル URL、画像コンテンツ URL を印刷します。  このメソッドは、後続の Visual Search 要求で使用するための `ImageInsightsToken` を返します。

```
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }
```

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Visual Search 要求に ImageInsightsToken を指定する
この例では、前のメソッドから返される分析情報トークンを使用します。 次のコードは、`ImageInfo` オブジェクトを `ImageInsightsToken` から作成し、ImageInfo オブジェクトを `VisualSearchRequest` に読み込みます。 `ImageInsightsToken` を `VisualSearchRequest` の `ImageInfo` に指定します

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```
## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>Visual Search を使用して ImageInsightsToken から画像を検索する
`VisualSearchRequest` では、検索する画像に関する情報を `ImageInfo` オブジェクトに格納します。  `VisualSearchMethodAsync` メソッドを使用して結果を取得します。
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>ImageModuleAction から URL データを取得する
Visual Search の結果は `ImageTag` オブジェクトです。  各タグには、`ImageAction` オブジェクトのリストが含まれます。  各 `ImageAction` には、アクションのタイプに依存する値のリストである `Data` フィールドが含まれます。

次のコードを使用すると、さまざまなタイプを取得できます。
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
完全なアプリケーションでは、次の項目が返されます。

* ActionType: MoreSizes -> WebSearchUrl:
* ActionType: VisualSearch -> WebSearchUrl:
* ActionType: ImageById -> WebSearchUrl:
* ActionType: RelatedSearches -> WebSearchUrl:
* ActionType: DocumentLevelSuggestions -> WebSearchUrl:
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1

前の一覧からわかるように、`TopicResults` タイプと `ImageResults` タイプには、関連する画像のクエリが含まれています。 リスト内の URL は、Bing Search の結果にリンクしています。


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>Visual Search で見つかった画像の PagesIncluding ActionType URL

実際の画像 URL を取得するには、キャストにより、`ActionType` を、値のリストを持つ `Data` 要素が含まれる `ImageModuleAction` として読み取る必要があります。  各値は、画像の URL です。  次のコードでは、`PagesIncluding` アクション タイプを `ImageModuleAction` にキャストして、値を読み取ります。
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```
これらのデータ型の詳細については、「[Images - Visual Search (画像 - Visual Search)](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch)」を参照してください。
## <a name="complete-code"></a>完成したコード

前の例を実行するコードを次に示します。 次のコードは、投稿要求で `ImageInsightsToken` を送信します。 次に、各 ActionType の Bing Search URL を出力します。 ActionType が `PagesIncluding` の場合、`Data` 内の `ImageObject` 項目が取得されます。  `Data` には、Web ページ上の画像の URL である値のリストが含まれています。  結果の Visual Search URL をコピーしてブラウザーに貼り付け、結果を表示します。 ContentUrl 項目をコピーしてブラウザーに貼り付け、画像を表示します。

```
using System;
using System.IO;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;

namespace VisualSearchFeatures
{
    class Program
    {
        static void Main(string[] args)
        {
            String subscriptionKey = "YOUR-ACCESS-KEY";

            insightsToken = ImageResults(subscriptionKey);

            VisualSearchInsightsToken(subscriptionKey, insightsToken);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        // Searches for results on query (Canadian Rockies) and gets an ImageInsightsToken.
        // Also prints first image insights token, thumbnail url, and image content url.
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }


        // This method will get Visual Search results from an imageInsightsToken obtained from the return value of the ImageResults method.
        // The method includes imageInsightsToken the in a knowledgeRequest parameter, along with a cropArea object. 
        // It prints out the imageInsightsToken uploaded in the request.
        // Finally the example prints URLs of images found using the imageInsightsToken.

        public static void VisualSearchInsightsToken(string subscriptionKey, string insightsTok)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                // The image can be specified via an insights token, in the ImageInfo object.
                ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);

                // An image binary is not necessary here, as the image is specified by insights token.
                VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
                Console.WriteLine("\r\nVisual search request with imageInsightsToken and knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // Visual Search results
                    if (visualSearchResults.Image?.ImageInsightsToken != null)
                    {
                        Console.WriteLine($"Uploaded image insights token: {insightsTok}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image insights token!");
                    }

                    if (visualSearchResults.Tags.Count > 0)
                    {
                        // List of tags
                        foreach (ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            {
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                                    {
                                        Console.WriteLine("ContentURL: " + o.ContentUrl);
                                    }
                                }
                            }
                        }
                    }

                    else
                    {
                        Console.WriteLine("Couldn't find image tags!");
                    }

                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }
    }
}

```
## <a name="next-steps"></a>次の手順
[Visual Search 応答](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)
