---
title: Video Search SDK C# のクイック スタート | Microsoft Docs
description: Video Search SDK コンソール アプリケーションの設定。
titleSuffix: Azure cognitive services setup News search SDK C# console application
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 01/29/2018
ms.author: v-gedod
ms.openlocfilehash: f53e2d0f0052ccfabb6d750556cb532f069c9121
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377952"
---
# <a name="video-search-sdk-c-quickstart"></a>Video Search SDK C# のクイック スタート

Bing Video Search SDK には、Web 要求と結果解析のための REST API 機能が含まれています。

[C# Bing Video Search SDK サンプルのソース コード](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch)は、Git Hub で入手できます。

## <a name="application-dependencies"></a>アプリケーションの依存関係

Bing Video Search SDK を使用してコンソール アプリケーションを設定するには、Visual Studio のソリューション エクスプローラーで `Manage NuGet Packages` オプションを参照します。  `Microsoft.Azure.CognitiveServices.Search.VideoSearch` パッケージを追加します。

[NuGet Video Search SDK パッケージ](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0)をインストールすると、次の項目を含む依存関係もインストールされます。
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json


## <a name="video-search-client"></a>Video Search クライアント
`VideoSearchAPI` クライアントのインスタンスを作成するには、次のディレクティブを追加します。
```
using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;

```
次に、クライアントをインスタンス化します。
```
var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));


```
クライアントを使用し、クエリ テキスト "SwiftKey" を指定して動画を検索します。
```
var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
Console.WriteLine("Search videos for query \"SwiftKey\"");

```

結果を解析し、結果の数を確認し、最初の動画結果の ID、名前、および URL を出力します。
```
if (videoResults == null)
{
    Console.WriteLine("Didn't see any video result data..");
}
else
{
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value.First();

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
}

```
## <a name="complete-console-application"></a>コンソール アプリケーションの全体像

次のコンソール アプリケーションは、以前に定義されたクエリを実行し、結果を解析します。

```
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;

namespace VideoSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("19aa718a79d6444daaa415981d9f54ad"));

            try
            {
                var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
                Console.WriteLine("Search videos for query \"SwiftKey\"");

                if (videoResults == null)
                {
                    Console.WriteLine("Didn't see any video result data..");
                }
                else
                {
                    if (videoResults.Value.Count > 0)
                    {
                        var firstVideoResult = videoResults.Value.First();

                        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
                        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
                        Console.WriteLine($"First video name: {firstVideoResult.Name}");
                        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find video results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            // Include these calls to use queries defined under the following headings.
            //VideoSearchWithFilters(client);
            //VideoDetail(client);
            //VideoTrending(client);


            Console.WriteLine("Any key to exit...");
            Console.ReadKey();

        }

```
## <a name="url-parameters"></a>URL パラメーター

変更されておらず、短く、解像度が 1080p の動画について、"Bellevue Trailer" というクエリ テキストを検索します。  動画の結果の数を確認し、最初の結果の ID、名前、および URL を出力します。

```
        public static void VideoSearchWithFilters(VideoSearchAPI client)
        {
            try
            {
                var videoResults = client.Videos.SearchAsync(query: "Bellevue Trailer", pricing: VideoPricing.Free, length: VideoLength.Short, resolution: VideoResolution.HD1080p).Result;
                Console.WriteLine("Search videos for query \"Bellevue Trailer\" that is free, short and 1080p resolution");

                if (videoResults == null)
                {
                    Console.WriteLine("Didn't see any video result data..");
                }
                else
                {
                    if (videoResults.Value.Count > 0)
                    {
                        var firstVideoResult = videoResults.Value.First();

                        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
                        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
                        Console.WriteLine($"First video name: {firstVideoResult.Name}");
                        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find video results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

        }


```
## <a name="trending-videos"></a>急上昇中の動画
急上昇中の動画を検索し、バナー タイルとカテゴリを確認します。
```
        public static void VideoTrending(VideoSearchAPI client)
        {
            try
            {
                var trendingResults = client.Videos.TrendingAsync().Result;
                Console.WriteLine("Search trending videos");

                if (trendingResults == null)
                {
                    Console.WriteLine("Didn't see any trending video data..");
                }
                else
                {
                    // Banner Tiles
                    if (trendingResults.BannerTiles?.Count > 0)
                    {
                        var firstBannerTile = trendingResults.BannerTiles[0];
                        Console.WriteLine($"\r\nBanner tile count: {trendingResults.BannerTiles.Count}");
                        Console.WriteLine($"First banner tile text: {firstBannerTile.Query.Text}");
                        Console.WriteLine($"First banner tile url: {firstBannerTile.Query.WebSearchUrl}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find banner tiles!");
                    }

                    // Categories
                    if (trendingResults.Categories?.Count > 0)
                    {
                        var firstCategory = trendingResults.Categories[0];
                        Console.WriteLine($"Category count: {trendingResults.Categories.Count}");
                        Console.WriteLine($"First category title: {firstCategory.Title}");

                        if (firstCategory.Subcategories?.Count > 0)
                        {
                            var firstSubCategory = firstCategory.Subcategories[0];
                            Console.WriteLine($"SubCategory count: {firstCategory.Subcategories.Count}");
                            Console.WriteLine($"First sub category title: {firstSubCategory.Title}");

                            if (firstSubCategory.Tiles?.Count > 0)
                            {
                                var firstTile = firstSubCategory.Tiles[0];
                                Console.WriteLine($"Tile count: {firstSubCategory.Tiles.Count}");
                                Console.WriteLine($"First tile text: {firstTile.Query.Text}");
                                Console.WriteLine($"First tile url: {firstTile.Query.WebSearchUrl}");
                            }
                            else
                            {
                                Console.WriteLine("Couldn't find tiles!");
                            }
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find subcategories!");
                        }
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find categories!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

        }


```
## <a name="details"></a>詳細
"Bellevue Trailer" の動画を検索し、最初の動画の詳細情報を検索します。
```
        public static void VideoDetail(VideoSearchAPI client)
        {
            try
            {
                var videoResults = client.Videos.SearchAsync(query: "Bellevue Trailer").Result;

                var firstVideo = videoResults?.Value?.FirstOrDefault();

                if (firstVideo != null)
                {
                    var modules = new List<VideoInsightModule?>() { VideoInsightModule.All };
                    var videoDetail = client.Videos.DetailsAsync(query: "Bellevue Trailer", id: firstVideo.VideoId, modules: modules).Result;
                    Console.WriteLine($"Search detail for video id={firstVideo.VideoId}, name={firstVideo.Name}");

                    if (videoDetail != null)
                    {
                        if (videoDetail.VideoResult != null)
                        {
                            Console.WriteLine($"\r\nExpected video id: {videoDetail.VideoResult.VideoId}");
                            Console.WriteLine($"Expected video name: {videoDetail.VideoResult.Name}");
                            Console.WriteLine($"Expected video url: {videoDetail.VideoResult.ContentUrl}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find expected video!");
                        }

                        if (videoDetail?.RelatedVideos?.Value?.Count > 0)
                        {
                            var firstRelatedVideo = videoDetail.RelatedVideos.Value[0];
                            Console.WriteLine($"Related video count: {videoDetail.RelatedVideos.Value.Count}");
                            Console.WriteLine($"First related video id: {firstRelatedVideo.VideoId}");
                            Console.WriteLine($"First related video name: {firstRelatedVideo.Name}");
                            Console.WriteLine($"First related video url: {firstRelatedVideo.ContentUrl}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find any related video!");
                        }
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find detail about the video!");
                    }
                }
                else
                {
                    Console.WriteLine("Couldn't find video results!");
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }
    }

```

## <a name="next-steps"></a>次の手順

[Cognitive Services の .NET 向け SDK のサンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)