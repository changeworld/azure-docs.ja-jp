---
title: C# を利用してエンドポイントを呼び出す - Bing Custom Search - Microsoft Cognitive Services
description: このクイックスタートでは、C# を利用して Bing Custom Search エンドポイントを呼び出すことで、カスタム検索インスタンスから検索結果を要求する方法について紹介します。
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 87970e1c5e8487f9afca2acc680bdfeb610dc89f
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "41929965"
---
# <a name="call-bing-custom-search-endpoint-c"></a>Bing Custom Search エンドポイントを呼び出す (C#)

このクイックスタートでは、C# を利用して Bing Custom Search エンドポイントを呼び出すことで、カスタム検索インスタンスから検索結果を要求する方法について紹介します。 

## <a name="prerequisites"></a>前提条件

-  すぐに使用できるカスタム検索インスタンス。 「[Create your first Bing Custom Search instance](quick-start.md)」 (最初の Bing Custom Search インスタンスを作成する) を参照してください。
-  [.Net Core](https://www.microsoft.com/net/download/core) がインストールされている。
- [Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と **Bing Search API**。 このクイックスタートには[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)で十分です。 無料試用版を起動するとき、アクセス キーを入力する必要があります。あるいは、Azure ダッシュボードの有料サブスクリプション キーを使用できます。  

  >[!NOTE]  
  >2017 年 10 月 15 日以前にプロビジョニングされたプレビュー キーを保有する既存の Bing Custom Search ユーザーは、2017 年 11 月 30 日まで、または許可されているクエリの最大数を使い切るまで、そのキーをご利用いただけます。 その後は、Azure での一般公開バージョンへの移行が必要になります。 
 
## <a name="run-the-code"></a>コードの実行

この例を実行するには、次の手順に従います。

1. コードのフォルダーを作成します。
2. コマンド プロンプトまたはターミナルから、先ほど作成したフォルダーに移動します。
3. 次のコマンドを実行します。
    ```
    dotnet new console -o BingCustomSearch
    cd BingCustomSearch
    dotnet add package Newtonsoft.Json
    dotnet restore
   ```

4. Program.cs に次のコードをコピーします。
5. **YOUR-SUBSCRIPTION-KEY** と **YOUR-CUSTOM-CONFIG-ID** を、自分のキーと構成 ID に置き換えます。

    ``` CSharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    
    namespace bing_custom_search_example_dotnet
    {
        class Program
        {
            static void Main(string[] args)
            {
                var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
                var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
                var searchTerm = args.Length > 0 ? args[0]: "microsoft";            
    
                var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                    "q=" + searchTerm +
                    "&customconfig=" + customConfigId;
    
                var client = new HttpClient();
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                var httpResponseMessage = client.GetAsync(url).Result;
                var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
                BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
                
                for(int i = 0; i < response.webPages.value.Length; i++)
                {                
                    var webPage = response.webPages.value[i];
                    
                    Console.WriteLine("name: " + webPage.name);
                    Console.WriteLine("url: " + webPage.url);                
                    Console.WriteLine("displayUrl: " + webPage.displayUrl);
                    Console.WriteLine("snippet: " + webPage.snippet);
                    Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
                    Console.WriteLine();
                }            
            }
        }
    
        public class BingCustomSearchResponse
        {        
            public string _type{ get; set; }            
            public WebPages webPages { get; set; }
        }
    
        public class WebPages
        {
            public string webSearchUrl { get; set; }
            public int totalEstimatedMatches { get; set; }
            public WebPage[] value { get; set; }        
        }
    
        public class WebPage
        {
            public string name { get; set; }
            public string url { get; set; }
            public string displayUrl { get; set; }
            public string snippet { get; set; }
            public DateTime dateLastCrawled { get; set; }
            public string cachedPageUrl { get; set; }
            public OpenGraphImage openGraphImage { get; set; }        
        }
        
        public class OpenGraphImage
        {
            public string contentUrl { get; set; }
            public int width { get; set; }
            public int height { get; set; }
        }
    }
    ```
6. 次のコマンドを使用してアプリケーションをビルドします。 コマンド出力で参照されている dll パスを書き留めます。

    <pre>
    dotnet build 
    </pre>
7. **PATH TO OUTPUT** はビルド ステップで参照されていたパスに置き換えて、次のコマンドを使用してアプリケーションを実行します。

    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>次の手順
- [ホストされている UI エクスペリエンスの構成](./hosted-ui.md)
- [装飾マーカーを使用してテキストを強調表示する](./hit-highlighting.md)
- [Web ページのページング](./page-webpages.md)
