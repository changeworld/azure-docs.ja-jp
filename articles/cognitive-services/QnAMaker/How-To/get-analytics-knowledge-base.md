---
title: ナレッジ ベースに関する分析 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker サービスの作成中に App Insights を有効にした場合、QnA Maker はすべてのチャット ログとその他のテレメトリを格納します。 サンプル クエリを実行して、App Insights からチャット ログを取得してみましょう。
services: cognitive-services
author: diberry
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: e769bde39bc796b5b598109328b468b15385f38a
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650403"
---
# <a name="get-analytics-on-your-knowledge-base"></a>ナレッジ ベースに関する分析の取得

[QnA Maker サービスの作成](./set-up-qnamaker-service-azure.md)中に App Insights を有効にした場合、QnA Maker はすべてのチャット ログとその他のテレメトリを格納します。 サンプル クエリを実行して、App Insights からチャット ログを取得してみましょう。

1. App Insights リソースに移動します。

    ![Application Insights リソースを選択します](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. **[Log (Analytics)]** を選択します。 QnA Maker テレメトリのクエリを実行できる新しいウィンドウが開きます。

3. 次のクエリを貼り付けて、実行します。

    ```kusto
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, url, resultCode, duration, performanceBucket
    | parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId
    ```

    **[実行]** を選択して、クエリを実行します。

    [![ユーザーからの質問、回答、スコアを確認するクエリを実行する](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>QnA Maker ナレッジ ベースに関する他の分析についてのクエリを実行します

### <a name="total-90-day-traffic"></a>90 日間のトラフィックの合計

```kusto
//Total Traffic
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>指定の期間における質問トラフィックの合計

```kusto
//Total Question Traffic in a given time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
requests
| where timestamp <= endDate and timestamp >=startDate
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>ユーザー トラフィック

```kusto
//User Traffic
requests
| where url endswith "generateAnswer"
| project timestamp, id, url, resultCode, duration
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend UserId = tostring(customDimensions['UserId'])
| summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>質問の配布の待ち時間

```kusto
//Latency distribution of questions
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| project timestamp, id, name, resultCode, performanceBucket, KbId
| summarize count() by performanceBucket, KbId
```

### <a name="unanswered-questions"></a>未回答の質問

```kusto
// Unanswered questions
requests
| where url endswith "generateAnswer"
| project timestamp, id, url
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend question = tostring(customDimensions['Question'])
| extend answer = tostring(customDimensions['Answer'])
| extend score = tostring(customDimensions['Score'])
| where  score  == "0"
| project timestamp, KbId, question, answer, score
| order  by timestamp  desc
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [容量の選択](./improve-knowledge-base.md)
