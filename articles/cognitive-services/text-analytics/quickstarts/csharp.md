---
title: Azure Cognitive Services、Text Analytics API の C# のクイック スタート | Microsoft Docs
description: Azure 上の Microsoft Cognitive Services の Text Analytics API の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 09/20/2017
ms.author: ashmaka
ms.openlocfilehash: 59e2254054f51a8d5f30e1b38dc5e6c23899c054
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284331"
---
# <a name="quickstart-for-text-analytics-api-with-c"></a>C# での Text Analytics API のクイック スタート 
<a name="HOLTop"></a>

このチュートリアルでは、[Text Analytics API](//go.microsoft.com/fwlink/?LinkID=759711) を C# で使用して、言語の検出、センチメントの分析、およびキー フレーズの抽出を行う方法について説明します。 このコードは、.Net Core アプリケーションで動作するように作成されており、外部ライブラリへの参照は最小限なので、Linux または MacOS で実行することもできます。

API の技術ドキュメントについては、[API の定義](//go.microsoft.com/fwlink/?LinkID=759346)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

[Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と **Text Analytics API** を取得している必要があります。 このクイック スタートは、**月に 5,000 トランザクションを利用できる Free レベル**を使用して完了できます。

また、サインアップ時に生成される[エンドポイントとアクセス キー](../How-tos/text-analytics-how-to-access-key.md)が必要です。 


## <a name="install-the-nuget-sdk-package"></a>Nuget SDK パッケージのインストール
1. Visual Studio で新しいコンソール ソリューションを作成します。
1. ソリューションを右クリックし、**[ソリューションの NuGet パッケージの管理]** をクリックします
1. **[プレリリースを含める]** チェック ボックスをオンにします。
1. **[参照]** タブを選択し、**Microsoft.Azure.CognitiveServices.Language** を検索します
1. Nuget パッケージを選択して、インストールします。

> [!Tip]
>  [HTTP エンドポイント](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)は C# から直接呼び出すことができますが、Microsoft.Azure.CognitiveServices.Language SDK を使用すると、JSON のシリアル化とシリアル化解除について心配する必要がなく、サービスがかなり呼び出しやすくなります。
>
> 便利なリンク:
> - [SDK Nuget ページ](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [SDK コード](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-using-the-sdk"></a>SDK を使用した Text Analytics API の呼び出し
1. Program.cs を以下のコードで置き換えます。 このプログラムは、3 つのセクション (言語の抽出、キー フレーズの抽出、感情分析) で Text Analytics API の機能を示しています。
1. `Ocp-Apim-Subscription-Key` ヘッダー値を、お使いのサブスクリプションで有効なアクセス キーで置き換えます。
1. `Endpoint` 内の場所をサインアップしたエンドポイントに置き換えます。 このエンドポイントは、Azure Portal リソースで見つけることができます。 通常、エンドポイントは "https://[region].api.cognitive.microsoft.com" で始まります。ここでは、プロトコルとホスト名のみを含めるようにしてください。
1. プログラムを実行します。

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        /// <summary>
        /// Container for subscription credentials. Make sure to enter your valid key.
        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", "ENTER KEY HERE");
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            };

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result =  client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine("Document ID: {0} , Language: {1}", document.Id, document.DetectedLanguages[0].Name);
            }

            // Getting key-phrases
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing keyphrases
            foreach (var document in result2.Documents)
            {
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine("\t\t" + keyphrase);
                }
            }

            // Extracting sentiment
            Console.WriteLine("\n\n===== SENTIMENT ANALYSIS ======");

            SentimentBatchResult result3 = client.SentimentAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "I had the best day of my life."),
                          new MultiLanguageInput("en", "1", "This was a waste of my time. The speaker put me to sleep."),
                          new MultiLanguageInput("es", "2", "No tengo dinero ni nada que dar..."),
                          new MultiLanguageInput("it", "3", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                        })).Result;


            // Printing sentiment results
            foreach (var document in result3.Documents)
            {
                Console.WriteLine("Document ID: {0} , Sentiment Score: {1:0.00}", document.Id, document.Score);
            }
        }
    }
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Text Analytics と Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>関連項目 

 [Text Analytics の概要](../overview.md)  
 [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)

