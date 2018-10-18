---
title: 'クイック スタート: Custom Search SDK, C#'
titleSuffix: Azure Cognitive Services
description: Custom Search SDK C# コンソール アプリケーションの設定。
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 5abf1027059bed9c685e0eb44f17ab41dfabf655
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816771"
---
# <a name="quickstart-using-the-bing-custom-search-sdk-with-c"></a>クイック スタート: C# による Bing Custom Search SDK の使用

Bing Custom Search SDK は、Bing Custom Search REST API よりも簡単なプログラミング モデルを提供します。 このセクションでは、C# SDK を使用して、最初のカスタム検索呼び出しを行う方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

- すぐに使用できるカスタム検索インスタンス。 「[Create your first Bing Custom Search instance](quick-start.md)」 (最初の Bing Custom Search インスタンスを作成する) を参照してください。  
  
- サブスクリプション キー。 [無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)をアクティブにすると、サブスクリプション キーを取得できます。または Azure ダッシュボードから有料のサブスクリプション キーを使用することもできます ([Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を参照してください)。  
  
- Visual Studio 2017 がインストール済みであること。 お持ちでない場合は、**無料版の** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) をダウンロードできます。  
  
- [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) パッケージがインストール済みであること。 Visual Studio でソリューション エクスプ ローラーから、プロジェクトを右クリックして、メニューから `Manage NuGet Packages` を選択します。 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` パッケージをインストールします。

NuGet Custom Search パッケージをインストールすると、次のアセンブリもインストールされます。

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json



## <a name="run-the-code"></a>コードの実行

この例を実行するには、次の手順に従います。

1. Visual Studio 2017 を開きます。
  
2. **[ファイル]** メニューをクリックして、**[新規]**、**[プロジェクト]**、**Visual C# コンソール アプリケーション** テンプレートをクリックします。
  
3. ソリューションに CustomSearchSolution と名前を付け、それを配置するフォルダーを参照します。
  
4. [OK] をクリックしてソリューションを作成します。  
  
4. ソリューション エクスプローラーで、プロジェクト (ソリューションと同じ名前を持つ) を右クリックして、`Manage NuGet Packages` を選択します。 NuGet パッケージ マネージャーで **[参照]** をクリックします。 検索ボックスに Microsoft.Azure.CognitiveServices.Search.CustomSearch と入力し、Enter キーを押します。 パッケージを選択し、[インストール] をクリックします。  
  
4. Program.cs ファイルで次のコードをコピーします。 **YOUR-SUBSCRIPTION-KEY** と **YOUR-CUSTOM-CONFIG-ID** を、自分のサブスクリプション キーと構成 ID に置き換えます。  
  
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

    namespace CustomSrchSDK
    {
        class Program
        {
            static void Main(string[] args)
            {

                var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));

                try
                {
                    // This will look up a single query (Xbox).
                    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
                    Console.WriteLine("Searched for Query# \" Xbox \"");

                    //WebPages
                    if (webData?.WebPages?.Value?.Count > 0)
                    {
                        // find the first web page
                        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                        if (firstWebPagesResult != null)
                        {
                            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
                            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find web results!");
                        }
                    }
                    else
                    {
                        Console.WriteLine("Didn't see any Web data..");
                    }
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Encountered exception. " + ex.Message);
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

        }
    }
    ```  
  
5. ソリューションをビルドして実行 (デバッグ) します。 




## <a name="breaking-it-down"></a>分解

NuGet カスタム検索パッケージをインストールしたら、そのディレクティブを使用して追加する必要があります。

```csharp
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
```

次に、検索要求を行うために使用するカスタム検索クライアントをインスタンス化します。 必ず `YOUR-SUBSCRIPTION-KEY` を自分のキーで置き換えます。

```csharp
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));
```

検索要求を送信するのにクライアントを使用できるようになりました。 `YOUR-CUSTOM-CONFIG-ID` を自分のインスタンスの構成の ID で置き換えます (この ID は [Custom Search ポータル](https://www.customsearch.ai/)にあります)。 この例では、Xbox を検索します。 必要に応じて更新します。

```csharp
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
```

`SearchAsync` メソッドが `WebData` オブジェクトを返します。 `WebData` を使用して、検出された `WebPages` を反復処理します。 このコードは、最初の Web ページの結果を検索し、Web ページの `Name` と `URL` を印刷します。

```csharp
//WebPages
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Couldn't find web results!");
    }
}
else
{
    Console.WriteLine("Didn't see any Web data..");
}

```


## <a name="next-steps"></a>次の手順

SDK サンプルをチェックします。 各サンプルには、前提条件と、サンプルのインストール/実行の前提条件が記載された ReadMe ファイルが含まれています。

* [.NET サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)を使ってみる 
    * [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)
    * 定義および依存関係については、[.NET ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingCustomSearch)もご覧ください。
* [NodeJS サンプル](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)を使って作業を開始する 
    * 定義および依存関係については、[NodeJS ライブラリ](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/customSearch)もご覧ください。
* [Java サンプル](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)を使って作業を開始する 
    * 定義と依存関係については、[Java ライブラリ](https://github.com/Azure/azure-sdk-for-java/tree/master/cognitiveservices/azure-customsearch)も参照してください。
* [Python サンプル](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)を使ってみる 
    * 定義と依存関係については、[Python ライブラリ](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-customsearch)も参照してください。

