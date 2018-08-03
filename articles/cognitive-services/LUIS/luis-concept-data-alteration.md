---
title: LUIS におけるデータ変更の概念について - Azure | Microsoft Docs
description: Language Understanding (LUIS) での予測前にデータを変更する方法について説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/26/2018
ms.author: diberry
ms.openlocfilehash: d8421114bb5a7416ad2523fe9b0353f03f672619
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223985"
---
# <a name="data-alterations"></a>データの変更
LUIS では、予測前または予測中に発話を操作する方法が用意されています。 

## <a name="correct-spelling-errors-in-utterance"></a>発話内のスペル ミスの修正
LUIS では、[Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) を使用して、発話内のスペル ミスを修正します。 LUIS には、このサービスに関連付けられているキーが必要です。 キーを作成し、[エンドポイント](https://aka.ms/luis-endpoint-apis)にクエリ文字列パラメーターとして追加します。 

スペル ミスは、**[Test]\(テスト\)** パネルで、[キーを入力する](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel)ことで修正することもできます。 キーは、[Test]\(テスト\) パネルのブラウザーで、セッション変数として保持されます。 スペルを修正する各ブラウザー セッションで、[Test]\(テスト\) パネルにキーを追加します。 

[Test]\(テスト\) パネルとエンドポイントでのキーの使用が、[キー使用法](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/)のクォータに対してカウントされます。 LUIS には、テキストの長さに関する Bing Spell Check の制限が実装されています。 

スペル修正が機能するためには、エンドポイントに 2 つのパラメーターが必要です。

|Param|値|
|--|--|
|`spellCheck`|ブール値|
|`bing-spell-check-subscription-key`|[Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) エンドポイント キー|

[Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) でエラーが検出されると、元の発話と修正された発話が、予測と共にエンドポイントから返されます。

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```
 
## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>事前構築済み datetimeV2 エンティティのタイム ゾーンの変更
LUIS アプリで事前構築済み datetimeV2 エンティティを使用している場合、予測の応答で datetime 値が返されることがあります。 要求のタイム ゾーンを使用して、返すべき正しい datetime が決定されます。 要求元がボットであったり、LUIS の前段階の集約化された他のアプリケーションであったりする場合は、LUIS で使用するタイム ゾーンを修正します。 

### <a name="endpoint-querystring-parameter"></a>エンドポイントのクエリ文字列パラメーター
タイム ゾーンは、`timezoneOffset` パラメーターを使用してユーザーのタイム ゾーンを[エンドポイント](https://aka.ms/luis-endpoint-apis)に追加することで修正します。 時刻を変更するためは、`timezoneOffset` の値は正または負の数値 (分単位) である必要があります。  

|Param|値|
|--|--|
|`timezoneOffset`|正または負の数値 (分単位)|

### <a name="daylight-savings-example"></a>夏時間の例
返される事前構築済み datetimeV2 を夏時間用に調整する必要がある場合は、[エンドポイント](https://aka.ms/luis-endpoint-apis) クエリで、正負の値 (分単位) を含む `timezoneOffset` クエリ文字列パラメーターを使用する必要があります。

60 分を追加する場合は、次の手順を実行します。 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

60 分を削除する場合は、次の手順を実行します。 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C# コードによって timezoneOffset の正しい値を決定する
次の C# コードでは、[TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo?view=netframework-4.7.1) クラスの [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid?view=netframework-4.7.1#examples) メソッドを使用して、システム時刻に基づいて正しい `timezoneOffset` を決定します。

```CSharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [このチュートリアルを使用してスペル ミスを修正します。](luis-tutorial-bing-spellcheck.md)