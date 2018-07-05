---
title: Bing Visual Search SDK のトリミング範囲と結果のチュートリアル | Microsoft Docs
description: Bing Visual Search SDK を使用してアップロードされた画像のトリミング範囲に類似する画像の URL を取得する方法。
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: 9bc3c180f108025f442343d8c5356982a83826a6
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958405"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>チュートリアル: Bing Visual Search SDK の画像トリミング範囲と結果
Visual Search SDK には、画像の領域を選択し、大きな画像のトリミング範囲に類似するオンラインの画像を見つけるオプションがあります。  この例では、複数の人物が含まれた画像から 1 人だけが含まれるトリミング範囲を指定します。  コードでは、トリミング範囲と大きな画像の URL を送信し、Bing Search URL とオンラインで見つかった類似する画像の URL を含む結果を返します。

## <a name="prerequisites"></a>前提条件

このコードを Windows 上で実行するには、[Visual Studio 2017](https://www.visualstudio.com/downloads/) が必要です  (無料の Community Edition でかまいません)。

[Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と Bing Search API を取得している必要があります。 このクイック スタートには[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)で十分です。 無料試用版を起動するとき、アクセス キーを入力する必要があります。あるいは、Azure ダッシュボードの有料サブスクリプション キーを使用できます。

## <a name="application-dependencies"></a>アプリケーションの依存関係
Bing Web Search SDK を使用してコンソール アプリケーションを設定するには、Visual Studio のソリューション エクスプローラーで [NuGet パッケージの管理] オプションに移動します。 Microsoft.Azure.CognitiveServices.Search.VisualSearch パッケージを追加します。

NuGet Web Search SDK パッケージをインストールすると、次の項目を含む依存関係もインストールされます。

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="image-and-crop-area"></a>画像とトリミング範囲
次の画像は、Microsoft のシニア リーダーシップ チームを示しています。  Visual Search SDK を使用して、画像のトリミング範囲をアップロードし、大きな画像の選択領域内のエンティティを含んでいる他の画像と Web ページを検索します。  この場合のエンティティは人物です。

![Microsoft のシニア リーダーシップ チーム](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>トリミング範囲を VisualSearchRequest の ImageInfo として指定する
この例では、画像全体の割合で左上および右下の座標を指定する、前の画像のトリミング範囲を使用します。  次のコードは、`ImageInfo` オブジェクトをトリミング範囲から作成し、`ImageInfo` オブジェクトを `VisualSearchRequest` に読み込みます。  `ImageInfo` オブジェクトには、オンラインの画像の URL も含まれます。

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>トリミング範囲に類似する画像を検索する
`VisualSearchRequest` には、画像に関するトリミング範囲情報とその URL が含まれています。  `VisualSearchMethodAsync` メソッドを使用して結果を取得します。
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>ImageModuleAction から URL データを取得する
Visual Search の結果は `ImageTag` オブジェクトです。  各タグには、`ImageAction` オブジェクトのリストが含まれます。  各 `ImageAction` には、アクションのタイプに依存する値のリストである `Data` フィールドが含まれます。

次のコードを使用すると、さまざまなタイプを取得できます。
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
完全なアプリケーションでは、次の項目が返されます。

* ActionType: PagesIncluding WebSearchURL:
* ActionType: MoreSizes WebSearchURL:
* ActionType: VisualSearch WebSearchURL:
* ActionType: ImageById WebSearchURL: 
* ActionType: RelatedSearches  WebSearchURL:
* ActionType: Entity -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1

前の一覧からわかるように、`Entity` `ActionType` には、認識可能な人物、場所、または物に関する情報を返す Bing Search クエリが含まれています。  `TopicResults` タイプと `ImageResults` タイプには、関連する画像のクエリが含まれています。 リスト内の URL は、Bing Search の結果にリンクしています。


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

## <a name="complete-code"></a>完成したコード

前の例を実行するコードを次に示します。 このコードは、投稿要求の本文で画像バイナリを cropArea オブジェクトと共に送信し、各 ActionType の Bing Search URL を出力します。 ActionType が PagesIncluding の場合、コードは ImageObject Data 内の ImageObject 項目を取得します。  Data には、Web ページ上の画像の URL である値のリストが含まれています。  結果の Visual Search URL をコピーしてブラウザーに貼り付け、結果を表示します。 ContentUrl 項目をコピーしてブラウザーに貼り付け、画像を表示します。

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

            VisualSearchImageBinaryWithCropArea(subscriptionKey);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        public static void VisualSearchImageBinaryWithCropArea(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
                
                // The ImageInfo struct specifies the crop area in the image and the URL of the larger image. 
                string imageURL = "https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
                ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);
                
                VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

                Console.WriteLine("\r\nVisual search request with image from URL and crop area combined in knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // List of tags
                    if (visualSearchResults.Tags.Count > 0)
                    {

                        foreach(ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            { 
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
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
[Visual Search 応答](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/overview#the-response)