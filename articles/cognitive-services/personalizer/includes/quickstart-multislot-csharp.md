---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 03/23/2021
ms.openlocfilehash: e21dab310c41cf6aae0e201d7d1b8e78a8540a30
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2021
ms.locfileid: "112255640"
---
[リファレンス ドキュメント](/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer) | [マルチスロットの概念](..\concept-multi-slot-personalization.md) | [サンプル](https://aka.ms/personalizer/ms-dotnet)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* 最新バージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* Azure サブスクリプションを用意できたら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="Personalizer リソースを作成"  target="_blank">Personalizer リソースを作成</a>し、自分のキーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * アプリケーションを Personalizer API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

[!INCLUDE [Upgrade Personalizer instance to multi-slot](upgrade-personalizer-multi-slot.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

[!INCLUDE [Change reward wait time](change-reward-wait-time.md)]

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

好みのエディターまたは IDE で、新しい .NET Core アプリケーションを作成します。

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、dotnet `new` コマンドを使用し、`personalizer-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイル (`Program.cs`) を使用する単純な "Hello World" C# プロジェクトが作成されます。

```console
dotnet new console -n personalizer-quickstart
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

プロジェクト ディレクトリから、好みのエディターまたは IDE で `Program.cs` ファイルを開きます。 ディレクティブを使用して以下を追加します。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Text;
using System.Text.Json;
using System.Text.Json.Serialization;
using System.Threading.Tasks;
```

## <a name="object-model"></a>オブジェクト モデル

各スロットに対してコンテンツの 1 つの最適な項目を要求するには、**MultiSlotRankRequest** を作成し、[multislot/rank](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Rank) に POST 要求を送信します。 その後、応答は **MultiSlotRankResponse** に解析されます。

報酬スコアを Personalizer に送信するには、**MultiSlotReward** を作成してから、POST 要求を [multislot/events/{eventId}/reward](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Events_Reward) に送信します。

このクイックスタートでは、報酬スコアの決定は重要ではありません。 実稼働システムでは、何がどの程度まで[報酬スコア](../concept-rewards.md)に影響を及ぼすかを特定するのは複雑なプロセスとなる場合があり、そのプロセスはやがて変更することになる場合もあります。 実際の Personalizer アーキテクチャでは、この設計上の意思決定を主要な意思決定に含めるようにしてください。

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、.NET に HTTP 要求を送信することによって以下のタスクを実行する方法が示されています。

* [ベース URL を作成する](#create-base-urls)
* [マルチスロット ランク API](#request-the-best-action)
* [マルチスロット報酬 API](#send-a-reward)


## <a name="create-base-urls"></a>ベース URL を作成する

このセクションでは、次の 2 つのことを行います。
* キーとエンドポイントを指定する
* ランクと報酬の URL を作成する

まず、Program クラスに以下の行を追加します。 必ず Personalizer リソースから取得したキーとエンドポイントを追加してください。

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```csharp
//Replace 'PersonalizationBaseUrl' and 'ResourceKey' with your valid endpoint values.
private const string PersonalizationBaseUrl = "<REPLACE-WITH-YOUR-PERSONALIZER-ENDPOINT>";
private const string ResourceKey = "<REPLACE-WITH-YOUR-PERSONALIZER-KEY>";
```

次に、ランクと報酬の URL を作成します。

```csharp
private static string MultiSlotRankUrl = string.Concat(PersonalizationBaseUrl, "personalizer/v1.1-preview.1/multislot/rank");
private static string MultiSlotRewardUrlBase = string.Concat(PersonalizationBaseUrl, "personalizer/v1.1-preview.1/multislot/events/");
```

## <a name="get-content-choices-represented-as-actions"></a>アクションとして表されるコンテンツの選択肢を取得する

アクションはコンテンツの選択肢を表します。Personalizer を使用して、この中から最適なコンテンツ項目を選択します。 一連のアクションとそのフィーチャーを表す次のメソッドを Program クラスに追加します。 

```csharp
private static IList<Action> GetActions()
{
    IList<Action> actions = new List<Action>
    {
        new Action
        {
            Id = "Red-Polo-Shirt-432",
            Features =
            new List<object>() { new { onSale = "true", price = "20", category = "Clothing" } }
        },
        new Action
        {
            Id = "Tennis-Racket-133",
            Features =
            new List<object>() { new { onSale = "false", price = "70", category = "Sports" } }
        },
        new Action
        {
            Id = "31-Inch-Monitor-771",
            Features =
            new List<object>() { new { onSale = "true", price = "200", category = "Electronics" } }
        },
        new Action
        {
            Id = "XBox-Series X-117",
            Features =
            new List<object>() { new { onSale = "false", price = "499", category = "Electronics" } }
        }
    };
    return actions;
}
```

## <a name="get-slots"></a>スロットを取得する

スロットにより、ユーザーが対話するページが構成されます。 Personalizer により、定義されている各スロットに表示するアクションが決定されます。 アクションは、`ExcludeActions` として示されている特定のスロットから除外できます。 `BaselineAction` はスロットの既定のアクションであり、Personalizer を使用しない場合に表示されていたものです。


このクイックスタートには、単純なスロット機能があります。 運用システムでは、[フィーチャー](../concepts-features.md)を決定して[評価](../concept-feature-evaluation.md)することが、簡単ではない場合があります。

```csharp
private static IList<Slot> GetSlots()
{
    IList<Slot> slots = new List<Slot>
    {
        new Slot
        {
            Id = "BigHeroPosition",
            Features = new List<object>() { new { size = "large", position = "left" } },
            ExcludedActions = new List<string>() { "31-Inch-Monitor-771" },
            BaselineAction = "Red-Polo-Shirt-432"

        },
        new Slot
        {
            Id = "SmallSidebar",
            Features = new List<object>() { new { size = "small", position = "right" } },
            ExcludedActions = new List<string>() { "Tennis-Racket-133" },
            BaselineAction = "XBox-Series X-117"
        },
    };

    return slots;
}
```

## <a name="classes-for-constructing-rankreward-requests-and-responses"></a>ランクと報酬の要求と応答を構築するためのクラス

ランクと報酬の要求を構築して応答を解析するために使用される、次の入れ子になったクラスを追加します。

```csharp
private class Action
{
    [JsonPropertyName("id")]
    public string Id { get; set; }

    [JsonPropertyName("features")]
    public object Features { get; set; }
}
```

```csharp
private class Slot
{
    [JsonPropertyName("id")]
    public string Id { get; set; }

    [JsonPropertyName("features")]
    public List<object> Features { get; set; }

    [JsonPropertyName("excludedActions")]
    public List<string> ExcludedActions { get; set; }

    [JsonPropertyName("baselineAction")]
    public string BaselineAction { get; set; }
}
```

```csharp
private class Context
{
    [JsonPropertyName("features")]
    public object Features { get; set; }
}
```

```csharp
private class MultiSlotRankRequest
{
    [JsonPropertyName("contextFeatures")]
    public IList<Context> ContextFeatures { get; set; }

    [JsonPropertyName("actions")]
    public IList<Action> Actions { get; set; }

    [JsonPropertyName("slots")]
    public IList<Slot> Slots { get; set; }

    [JsonPropertyName("eventId")]
    public string EventId { get; set; }

    [JsonPropertyName("deferActivation")]
    public bool DeferActivation { get; set; }
}
```

```csharp
private class MultiSlotRankResponse
{
    [JsonPropertyName("slots")]
    public IList<SlotResponse> Slots { get; set; }

    [JsonPropertyName("eventId")]
    public string EventId { get; set; }
}
```

```csharp
private class SlotResponse
{
    [JsonPropertyName("id")]
    public string Id { get; set; }

    [JsonPropertyName("rewardActionId")]
    public string RewardActionId { get; set; }
}
```

```csharp
private class MultiSlotReward
{
    [JsonPropertyName("reward")]
    public List<SlotReward> Reward { get; set; }
}
```

```csharp
private class SlotReward
{
    [JsonPropertyName("slotId")]
    public string SlotId { get; set; }

    [JsonPropertyName("value")]
    public float Value { get; set; }
}
```

## <a name="get-user-preferences-for-context"></a>コンテキストに対するユーザーの好みを取得する

Program クラスに次のメソッドを追加し、時間帯とユーザーが使用しているデバイスの種類に関するユーザーの入力をコマンド ラインから取得します。 これらはコンテキストのフィーチャーとして使用されます。

```csharp
static string GetTimeOfDayForContext()
{
    string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

    Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
    if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
        timeIndex = 1;
    }

    return timeOfDayFeatures[timeIndex - 1];
}
```

```csharp
static string GetDeviceForContext()
{
    string[] deviceFeatures = new string[] { "mobile", "tablet", "desktop" };

    Console.WriteLine("\nWhat is the device type (enter number)? 1. Mobile 2. Tablet 3. Desktop");
    if (!int.TryParse(GetKey(), out int deviceIndex) || deviceIndex < 1 || deviceIndex > deviceFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + deviceFeatures[0] + ".");
        deviceIndex = 1;
    }

    return deviceFeatures[deviceIndex - 1];
}
```

どちらのメソッドも、`GetKey` メソッドを使用して、ユーザーの選択内容をコマンド ラインから読み取ります。

```csharp
private static string GetKey()
{
    return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
}
```

```csharp
private static IList<Context> GetContext(string time, string device)
{
    IList<Context> context = new List<Context>
    {
        new Context
        {
            Features = new {timeOfDay = time, device = device }
        }
    };

    return context;
}
```

## <a name="make-http-requests"></a>HTTP 要求を行う

マルチスロットの Rank 呼び出しと Reward 呼び出しの場合は、これらの関数を追加して POST 要求を Personalizer のエンドポイントに送信します。

```csharp
private static async Task<MultiSlotRankResponse> SendMultiSlotRank(HttpClient client, string rankRequestBody, string rankUrl)
{
    try
    {
    var rankBuilder = new UriBuilder(new Uri(rankUrl));
    HttpRequestMessage rankRequest = new HttpRequestMessage(HttpMethod.Post, rankBuilder.Uri);
    rankRequest.Content = new StringContent(rankRequestBody, Encoding.UTF8, "application/json");
    HttpResponseMessage response = await client.SendAsync(rankRequest);
    response.EnsureSuccessStatusCode();
    MultiSlotRankResponse rankResponse = JsonSerializer.Deserialize<MultiSlotRankResponse>(await response.Content.ReadAsByteArrayAsync());
    return rankResponse;
    }
    catch (Exception e)
    {
        Console.WriteLine("\n" + e.Message);
        Console.WriteLine("Please make sure multi-slot feature is enabled. To do so, follow multi-slot Personalizer documentation to update your loop settings to enable multi-slot functionality.");
        throw;
    }
}
```

```csharp
private static async Task SendMultiSlotReward(HttpClient client, string rewardRequestBody, string rewardUrlBase, string eventId)
{
    string rewardUrl = String.Concat(rewardUrlBase, eventId, "/reward");
    var rewardBuilder = new UriBuilder(new Uri(rewardUrl));
    HttpRequestMessage rewardRequest = new HttpRequestMessage(HttpMethod.Post, rewardBuilder.Uri);
    rewardRequest.Content = new StringContent(rewardRequestBody, Encoding.UTF8, "application/json");

    await client.SendAsync(rewardRequest);
}
```

## <a name="create-the-learning-loop"></a>学習ループを作成する

Personalizer の学習ループとは、[MultiSlotRank](#request-the-best-action) と [MultiSlotReward](#send-a-reward) の呼び出しのサイクルです。 このクイックスタートでは、コンテンツをパーソナライズするための各 Rank 呼び出しの後に Reward 呼び出しを行って、サービスがどの程度適切に実行されたかを Personalizer に伝えます。

次のコードでは、コマンド ラインでユーザーに好みをたずね、その情報を Personalizer に送信して各スロットに最適なアクションを選択し、その選択をリストから選択できるようユーザーに提示した後、その選択にサービスがどの程度寄与したかを示す報酬スコアを Personalizer に送る形でサイクルをループで処理しています。

```csharp
static async Task Main(string[] args)
{
    Console.WriteLine($"Welcome to this Personalizer Quickstart!\n" +
            $"This code will help you understand how to use the Personalizer APIs (multislot rank and multislot reward).\n" +
            $"Each iteration represents a user interaction and will demonstrate how context, actions, slots, and rewards work.\n" +
            $"Note: Personalizer AI models learn from a large number of user interactions:\n" +
            $"You won't be able to tell the difference in what Personalizer returns by simulating a few events by hand.\n" +
            $"If you want a sample that focuses on seeing how Personalizer learns, see the Python Notebook sample.");

    IList<Action> actions = GetActions();
    IList<Slot> slots = GetSlots();

    using (var client = new HttpClient())
    {
        client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", ResourceKey);
        int iteration = 1;
        bool runLoop = true;
        do
        {
            Console.WriteLine($"\nIteration: {iteration++}");
            string timeOfDayFeature = GetTimeOfDayForContext();
            string deviceFeature = GetDeviceForContext();

            IList<Context> context = GetContext(timeOfDayFeature, deviceFeature);

            string eventId = Guid.NewGuid().ToString();

            string rankRequestBody = JsonSerializer.Serialize(new MultiSlotRankRequest()
            {
                ContextFeatures = context,
                Actions = actions,
                Slots = slots,
                EventId = eventId,
                DeferActivation = false
            });

            //Ask Personalizer what action to show for each slot
            MultiSlotRankResponse multiSlotRankResponse = await SendMultiSlotRank(client, rankRequestBody, MultiSlotRankUrl);

            MultiSlotReward multiSlotRewards = new MultiSlotReward()
            {
                Reward = new List<SlotReward>()
            };

            for (int i = 0; i < multiSlotRankResponse.Slots.Count(); ++i)
            {
                Console.WriteLine($"\nPersonalizer service decided you should display: { multiSlotRankResponse.Slots[i].RewardActionId} in slot {multiSlotRankResponse.Slots[i].Id}. Is this correct? (y/n)");
                SlotReward reward = new SlotReward()
                {
                    SlotId = multiSlotRankResponse.Slots[i].Id
                };

                string answer = GetKey();

                if (answer == "Y")
                {
                    reward.Value = 1;
                    Console.WriteLine("\nGreat! The application will send Personalizer a reward of 1 so it learns from this choice of action for this slot.");
                }
                else if (answer == "N")
                {
                    reward.Value = 0;
                    Console.WriteLine("\nYou didn't like the recommended item. The application will send Personalizer a reward of 0 for this choice of action for this slot.");
                }
                else
                {
                    reward.Value = 0;
                    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended item.");
                }
                multiSlotRewards.Reward.Add(reward);
            }

            string rewardRequestBody = JsonSerializer.Serialize(multiSlotRewards);

            // Send the reward for the action based on user response for each slot.
            await SendMultiSlotReward(client, rewardRequestBody, MultiSlotRewardUrlBase, multiSlotRankResponse.EventId);

            Console.WriteLine("\nPress q to break, any other key to continue:");
            runLoop = !(GetKey() == "Q");
        } while (runLoop);
    }
}
```
rank 呼び出しと reward 呼び出しについて、以降の各セクションでさらに詳しく見ていきましょう。
コード ファイルを実行する前に[コンテンツの選択肢を取得](#get-content-choices-represented-as-actions)し、[スロットを取得](#get-slots)して、[マルチスロットのランクと報酬の要求を送信](#make-http-requests)する、次のメソッドを追加します。

* `GetActions`
* `GetSlots`
* `GetTimeOfDayForContext`
* `GetTimeOfDayForContext`
* `GetKey`
* `GetContext`
* `SendMultiSlotRank`
* `SendMultiSlotReward`

コード ファイルを実行する前に、[ランクと報酬の要求の本文を作成して応答を解析する](#classes-for-constructing-rankreward-requests-and-responses)、次のクラスを追加します。

* `Action`
* `Slot`
* `Context`
* `MultiSlotRankRequest`
* `MultiSlotRankResponse`
* `SlotResponse`
* `MultiSlotReward`
* `SlotReward`

## <a name="request-the-best-action"></a>最適なアクションを要求する

Rank 要求を実行するために、このプログラムは、ユーザーの好みをたずねてコンテンツの選択肢の `context` を作成します。 要求本文には、コンテキスト、アクション、スロットと、それぞれの機能が含まれています。 `SendMultiSlotRank` メソッドは、要求を送信するための HTTP クライアント、要求本文、URL を解釈します。

このクイックスタートのコンテキストのフィーチャーは、時間帯とユーザーのデバイスという単純なものです。 実稼働システムでは、[アクションとフィーチャー](../concepts-features.md)を決定し、[評価](../concept-feature-evaluation.md)することが、決して簡単ではない場合もあります。

```csharp
string timeOfDayFeature = GetTimeOfDayForContext();
string deviceFeature = GetDeviceForContext();

IList<Context> context = GetContext(timeOfDayFeature, deviceFeature);

string eventId = Guid.NewGuid().ToString();

string rankRequestBody = JsonSerializer.Serialize(new MultiSlotRankRequest()
{
    ContextFeatures = context,
    Actions = actions,
    Slots = slots,
    EventId = eventId,
    DeferActivation = false
});

//Ask Personalizer what action to show for each slot
MultiSlotRankResponse multiSlotRankResponse = await SendMultiSlotRank(client, rankRequestBody, MultiSlotRankUrl);
```

## <a name="send-a-reward"></a>報酬を送信する

Reward 要求の報酬スコアを取得するめに、このプログラムでは、各スロットのユーザーの選択をコマンド ラインから取得し、選択に数値を割り当てた後、一意のイベント ID、スロット ID、各スロットの数値 (報酬スコア) を Reward API に送信します。 スロットごとに報酬を定義する必要はありません。

このクイックスタートでは、0 または 1 という単純な数値を報酬スコアとして割り当てます。 実際のニーズにもよりますが、実稼働システムでは、いつ何を [Reward](../concept-rewards.md) 呼び出しに送信するかが決して簡単な決定事項ではない場合もあります。

```csharp
MultiSlotReward multiSlotRewards = new MultiSlotReward()
{
    Reward = new List<SlotReward>()
};

for (int i = 0; i < multiSlotRankResponse.Slots.Count(); ++i)
{
    Console.WriteLine($"\nPersonalizer service decided you should display: { multiSlotRankResponse.Slots[i].RewardActionId} in slot {multiSlotRankResponse.Slots[i].Id}. Is this correct? (y/n)");
    SlotReward reward = new SlotReward()
    {
        SlotId = multiSlotRankResponse.Slots[i].Id
    };

    string answer = GetKey();

    if (answer == "Y")
    {
        reward.Value = 1;
        Console.WriteLine("\nGreat! The application will send Personalizer a reward of 1 so it learns from this choice of action for this slot.");
    }
    else if (answer == "N")
    {
        reward.Value = 0;
        Console.WriteLine("\nYou didn't like the recommended item. The application will send Personalizer a reward of 0 for this choice of action for this slot.");
    }
    else
    {
        reward.Value = 0;
        Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended item.");
    }
    multiSlotRewards.Reward.Add(reward);
}

string rewardRequestBody = JsonSerializer.Serialize(multiSlotRewards);

// Send the reward for the action based on user response for each slot.
await SendMultiSlotReward(client, rewardRequestBody, MultiSlotRewardUrlBase, multiSlotRankResponse.EventId);
```

## <a name="run-the-program"></a>プログラムの実行

アプリケーション ディレクトリから、dotnet `run` コマンドを使用してアプリケーションを実行します。

```console
dotnet run
```

![クイック スタート プログラムは、フィーチャーと呼ばれるユーザー設定を収集するためにいくつかの質問をしてから、最上位のアクションを提供します。](../media/csharp-quickstart-commandline-feedback-loop/multislot-quickstart-program-feedback-loop-example-1.png)


[こちらでこのクイックスタート用のソース コード](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer/multislot-quickstart)を入手できます。
