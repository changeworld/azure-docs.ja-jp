---
title: ナレッジ ベースの分析 - カスタム質問と回答
titleSuffix: Azure Cognitive Services
description: カスタム質問と回答では、Azure 診断ログによりテレメトリ データとチャットのログを保存します。
services: cognitive-services
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: fba1481ffe7aa45514e53813ccf7b14ea01bd6bf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092337"
---
# <a name="get-analytics-for-your-knowledge-base"></a>ナレッジ ベースに関する分析の取得

カスタム質問と回答では、Azure 診断ログによりテレメトリ データとチャットのログを保存します。 下の手順でサンプル クエリを実行し、カスタム質問と回答ナレッジ ベースの使用状況分析を取得します。

1. カスタム質問と回答が有効になっている言語リソースの [診断ログを有効にします](../../../diagnostic-logging.md)。

2. 前の手順で、ログに **[Audit]、[RequestResponse]、[AllMetrics]** に加えて **[Trace]** を選択しました。

    ![カスタム質問と回答でトレース ログを有効にする](../media/analytics/qnamaker-v2-enable-trace-logging.png)

## <a name="kusto-queries"></a>Kusto クエリ

### <a name="chat-log"></a>チャット ログ

```kusto
// All QnA Traffic
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer" // This OperationName is valid for custom question answering enabled resources
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| project question_, answer_, score_, kbId_
```

### <a name="traffic-count-per-knowledge-base-and-user-in-a-time-period"></a>一定期間内のナレッジ ベースおよびユーザーあたりのトラフィック カウント

```kusto
// Traffic count per KB and user in a time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer" // This OperationName is valid for custom question answering enabled resources
| where TimeGenerated <= endDate and TimeGenerated >=startDate
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| extend userId_ = tostring(parse_json(properties_s).userId)
| summarize ChatCount=count() by bin(TimeGenerated, 1d), kbId_, userId_
```

### <a name="latency-of-generateanswer-api"></a>GenerateAnswer API の待機時間

```kusto
// Latency of GenerateAnswer
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="Generate Answer"
| project TimeGenerated, DurationMs
| render timechart
```

### <a name="average-latency-of-all-operations"></a>すべての操作の平均待機時間

```kusto
// Average Latency of all operations
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| project DurationMs, OperationName
| summarize count(), avg(DurationMs) by OperationName
| render barchart
```

### <a name="unanswered-questions"></a>未回答の質問

```kusto
// All unanswered questions
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer" // This OperationName is valid for custom question answering enabled resources
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| where score_ == 0
| project question_, answer_, score_, kbId_
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [容量の選択](../../../qnamaker/how-to/improve-knowledge-base.md)
