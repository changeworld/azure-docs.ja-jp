---
title: 'クイックスタート: .NET 用の質問応答クライアント ライブラリ'
description: このクイックスタートでは、.NET 用の質問応答クライアント ライブラリの使用を開始する方法について説明します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 質問応答を使用すると、FAQ ドキュメント、URL、製品マニュアルなどの半構造化コンテンツの質問応答サービスをいっそう強力にできます。
author: mrbullwinkle
ms.author: mbullwin
ms.topic: include
ms.date: 11/11/2021
ms.openlocfilehash: e374ef91c414b9d352b3c0cddd8af59da3e699a0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354197"
---
このクイックスタートは、以下を行う .NET 用の質問応答クライアント ライブラリに使用します。

* ナレッジ ベースから回答を取得する。
* 質問と共に送信したテキストの本文から回答を取得する。
* 質問に対する回答の信頼度スコアを取得する。

 [API のリファレンスのドキュメント][questionanswering_refdocs]|[ソース コード][questionanswering_client_src] | [パッケージ (NuGet)][questionanswering_nuget_package]  | [サンプル][questionanswering_samples] |

[questionanswering_nuget_package]: https://nuget.org/packages/Azure.AI.Language.
[questionanswering_refdocs]: https://docs.microsoft.com/dotnet/api/Azure.AI.Language.QuestionAnswering/
[questionanswering_client_src]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/cognitivelanguage/Azure.AI.Language.QuestionAnswering/src/
[questionanswering_samples]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/cognitivelanguage/Azure.AI.Language.QuestionAnswering/samples/README.md

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) または現在のバージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* 質問応答の API キーとエンドポイントを生成するには、カスタム質問応答機能を有効にした[言語リソース](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics)が必要です。 <!--TODO: Change link-->
    * 言語リソースがデプロイされたら、 **[リソースに移動]** を選びます。 API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
* クエリを実行する既存のナレッジ ベース。 ナレッジ ベースをまだ設定していない場合は、[**Language Studio のクイックスタート**](../quickstart/sdk.md)に関するページに記載されている手順に従ってください。 または、この [Surface ユーザー ガイド URL](https://download.microsoft.com/download/7/B/1/7B10C82E-F520-4080-8516-5CF0D803EEE0/surface-book-user-guide-EN.pdf) をデータ ソース として使用するナレッジ ベースを追加します。

## <a name="setting-up"></a>設定

### <a name="cli"></a>CLI

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`question-answering-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイル (*program.cs*) を使用する単純な "Hello World" C# プロジェクトが作成されます。

```console
dotnet new console -n question-answering-quickstart
```

新しく作成されたアプリ フォルダーにディレクトリを変更します。 次を使用してアプリケーションをビルドできます。

```console
dotnet build
```

ビルドの出力に警告やエラーが含まれないようにする必要があります。

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用カスタム質問応答クライアント ライブラリをインストールします。

```console
dotnet add package Azure.AI.Language.QuestionAnswering
```

## <a name="query-a-knowledge-base"></a>ナレッジ ベースにクエリを実行する

#### <a name="generate-an-answer-from-a-knowledge-base"></a>ナレッジ ベースから回答を生成する

以下の例では、`GetAnswers` を使ってナレッジ ベースに対してクエリを実行し、質問に対する回答を得ることができます。

以下の変数を実際の値に変更して、以下のコードを更新する必要があります。

|変数名 | 値 |
|--------------------------|-------------|
| `endpoint`               | この値は、Azure portal からリソースを確認する際に、 **[Keys & Endpoint]\(キーとエンドポイント\)** セクションで確認することができます。 または、 **[Language Studio]**  >  **[質問応答]**  >  **[ナレッジ ベースをデプロイする]**  >  **[Get prediction URL]\(予測 URL の取得\)** で値を見つけることができます。 エンドポイントの例: `https://southcentralus.api.cognitive.microsoft.com/`|
| `credential` | この値は、Azure portal からリソースを確認する際に、 **[Keys & Endpoint]\(キーとエンドポイント\)** セクションで確認することができます。 Key1 または Key2 を使用できます。 常に 2 つの有効なキーを持つことで、ダウンタイムなしで安全にキーのローテーションを行うことができます。 または、 **[Language Studio]**  >  **[質問応答]**  >  **[ナレッジ ベースをデプロイする]**  >  **[Get prediction URL]\(予測 URL の取得\)** で値を見つけることができます。 キー値は、サンプル要求の一部です。|
| `projectName` | 質問応答プロジェクトの名前。|
| `deploymentName`             | 使用可能な値は `test` および `production` です。 `production` は、 **[Language Studio]**  >  **[質問応答]**  >  **[ナレッジ ベースをデプロイする]** からナレッジ ベースをデプロイしていることに依存します。|

プロジェクト ディレクトリから *program.cs* ファイルを開いて、以下のコードに置き換えます。

```csharp
using Azure;
using Azure.AI.Language.QuestionAnswering;
using System;

namespace question_answering
{
    class Program
    {
        static void Main(string[] args)
        {

            Uri endpoint = new Uri("https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/");
            AzureKeyCredential credential = new AzureKeyCredential("{YOUR-LANGUAGE-RESOURCE-KEY}");
            string projectName = "{YOUR-PROJECT-NAME}";
            string deploymentName = "production";

            string question = "How long should my Surface battery last?";

            QuestionAnsweringClient client = new QuestionAnsweringClient(endpoint, credential);
            QuestionAnsweringProject project = new QuestionAnsweringProject(projectName, deploymentName);

            Response<AnswersResult> response = client.GetAnswers(question, project);

            foreach (KnowledgeBaseAnswer answer in response.Value.Answers)
            {
                Console.WriteLine($"Q:{question}");
                Console.WriteLine($"A:{answer.Answer}");
            }
        }
    }
}
```

この例では変数をハード コーディングしています。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 たとえば、[Azure Key Vault](../../../../key-vault/general/overview.md) で安全なキー記憶域を確保できます。

上記のコードを使用して `Program.cs` を更新し、正しい変数値で置き換えます。 自分のアプリケーション ディレクトリで `dotnet run` コマンドを使用してアプリケーションを実行します。

```console
dotnet run
```

応答は次のようになります。

```console
Q: How much battery life do I have left?
A: If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
```

この回答の精度に関する質問応答による信頼度については、既存の print ステートメントの下に print ステートメントを追加します。

```csharp
Console.WriteLine($"Q:{question}");
Console.WriteLine($"A:{answer.Answer}");
Console.WriteLine($"({answer.Confidence})"); // add this line
```

`dotnet run` を再度実行すると、信頼度スコアを含む結果が返されます。

```console
Q:How much battery life do I have left?
A:If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
(0.9185)
```

信頼度スコアは、0 から 1 の値で返されます。 これはパーセンテージのように考えることができるため、100 を掛けます。つまり、信頼度スコアが 0.9185 の場合、これがナレッジ ベースに基づいた、質問に対する正しい答えであることを質問応答は 91.85% 確信していることを意味します。

信頼度スコアが特定のしきい値を下回る回答を除外したい場合は、`AnswerOptions` を使用して `ConfidenceScoreThreshold` プロパティを追加します。

```csharp
QuestionAnsweringClient client = new QuestionAnsweringClient(endpoint, credential);
QuestionAnsweringProject project = new QuestionAnsweringProject(projectName, deploymentName);
AnswersOptions options = new AnswersOptions(); //Add this line
options.ConfidenceThreshold = 0.95; //Add this line

Response<AnswersResult> response = client.GetAnswers(question, project, options); //Add the additional options parameter
```

前回のコードの実行から、信頼度スコアが `.9185` であることが分かっているため、しきい値を `.95` に設定すると、[既定の回答](../how-to/change-default-answer.md)が返されます。

```console
Q:How much battery life do I have left?
A:No good match found in KB
(0)
```

## <a name="query-text-without-a-knowledge-base"></a>ナレッジ ベースを使用せずにテキストに対してクエリを実行する

`GetAnswersFromText` を使用することで、ナレッジ ベースなしで質問応答を使用することもできます。 この場合、質問を送信する際に、質問と、回答の検索先となる関連テキスト レコードの両方を質問応答に提供します。

この例の場合は、`endpoint` と `credential` の変数だけを変更する必要があります。

```csharp
using Azure;
using Azure.AI.Language.QuestionAnswering;
using System;
using System.Collections.Generic;


namespace questionansweringcsharp
{
    class Program
    {
        static void Main(string[] args)
        {

            Uri endpoint = new Uri("https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/");
            AzureKeyCredential credential = new AzureKeyCredential("YOUR-LANGUAGE-RESOURCE-KEY");
            QuestionAnsweringClient client = new QuestionAnsweringClient(endpoint, credential);

            IEnumerable<TextDocument> records = new[]
            {
                new TextDocument("doc1", "Power and charging.It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. " +
                         "It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it"),
                new TextDocument("doc2", "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. " +
                         "The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface."),
            };

            AnswersFromTextOptions options = new AnswersFromTextOptions("How long does it takes to charge a surface?", records);
            Response<AnswersFromTextResult> response = client.GetAnswersFromText(options);

           foreach (TextAnswer answer in response.Value.Answers)
            {
                if (answer.Confidence > .9)
                {
                    string BestAnswer = response.Value.Answers[0].Answer;

                    Console.WriteLine($"Q:{options.Question}");
                    Console.WriteLine($"A:{BestAnswer}");
                    Console.WriteLine($"Confidence Score: ({response.Value.Answers[0].Confidence:P2})"); //:P2 converts the result to a percentage with 2 decimals of accuracy. 
                    break;
                }
                else
                {
                    Console.WriteLine($"Q:{options.Question}");
                    Console.WriteLine("No answers met the requested confidence score.");
                    break;
                }
            }

        }
    }
}
```

上記のコードを実行するには、`Program.cs` を上記のスクリプト ブロックの内容で置き換え、`endpoint` と `credential` の変数を、前提条件の一部として作成した言語リソースに対応するように変更します。

今回は、すべての回答を繰り返し処理し、最大の信頼性スコアが 0.9 より大きい回答のみを返します。 `GetAnswersFromText` で使用できるオプションの詳細について理解する。
