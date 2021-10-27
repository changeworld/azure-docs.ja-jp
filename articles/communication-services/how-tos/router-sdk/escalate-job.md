---
title: Job Router でジョブをエスカレートする
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK を使用してジョブをエスカレートします。
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 3ed75c4e418e5e7494502e4f70d3c5419a5b162c
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176037"
---
# <a name="escalate-a-job"></a>ジョブをエスカレートする

このガイドでは、例外ポリシーを使用してキュー内のジョブをエスカレートする方法について説明します。

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../quickstarts/create-communication-resource.md)。
- 省略可能: [Job Router を開始する](../../quickstarts/router/get-started-router.md)ためのクイックスタートを完了する
- 省略可能: [ジョブ分類の攻略ガイド](job-classification.md)を確認する

## <a name="escalation-overview"></a>エスカレーションの概要

エスカレーションは、ジョブを別のキューに移動させる、より高い優先度を指定するなど、さまざまな動作の形式を取る可能性があります。 優先度の高いジョブは、優先度の低いジョブよりも先に worker に配分されます。 この攻略ガイドでは、この目標を達成するためにエスカレーション ポリシーと分類ポリシーを使用します。

## <a name="exception-policy-configuration"></a>例外ポリシーの構成

例外ポリシーを作成し、通常のキューにアタッチします。これは時間によってトリガーされ、ジョブが再分類されるというアクションが実行されます。

```csharp
// create the exception policy
await client.SetExceptionPolicyAsync(
    id: "Escalate_XBOX_Policy",
    name: "Add escalated label and reclassify XBOX Job requests after 5 minutes",
    rules: new List<ExceptionRule>()
    {
        new (
            id: "Escalated_Rule",
            trigger: new WaitTimeExceptionTrigger(TimeSpan.FromMinutes(5)),
            actions: new List<ExceptionAction>
            {
                new ReclassifyExceptionAction("EscalateReclassifyExceptionAction")
                {
                    LabelsToUpsert = new LabelCollection(
                        new Dictionary<string, object>
                    {
                        ["Escalated"] = true,
                    })
                }
            }
        )
    });
```

## <a name="classification-policy-configuration"></a>分類ポリシーの構成

ジョブに追加された新しいラベルを処理するための分類ポリシーを作成します。 このポリシーを使用すると、`Escalated` ラベルが評価され、ジョブがいずれかのキューに割り当てられます。 また、ジョブの優先度を `1` から `10` に上げるために、このポリシーには [RulesEngine](../../concepts/router/router-rule-concepts.md) が使用されます。

```csharp
await client.SetClassificationPolicyAsync(
    id: "Classify_XBOX_Voice_Jobs",
    name: "Classify XBOX Voice Jobs",
    queueSelector: new QueueIdSelector(
        new ExpressionRule(
            "If(job.Escalated = true, \"XBOX_Queue\", \"XBOX_Escalation_Queue\")")),
    workerSelectors: null,
    prioritizationRule: new ExpressionRule("If(job.Escalated = true, 10, 1)"),
    fallbackQueueId: "Default");
```

## <a name="queue-configuration"></a>キューの構成

通常のジョブとエスカレートされたジョブに必要なキューを作成し、通常のキューに例外ポリシーを割り当てます。

> [!NOTE]
> この手順は、既に `Round_Robin_Policy` という名前の配分ポリシーが作成されていることを前提としています。

```csharp
// create a queue for regular Jobs and attach the exception policy
await client.SetQueueAsync(
    id: "XBOX_Queue",
    name: "XBOX Queue",
    distributionPolicyId: "Round_Robin_Policy",
    exceptionPolicyId: "XBOX_Escalate_Policy"
);

// create a queue for escalated Jobs
await client.SetQueueAsync(
    id: "XBOX_Escalation_Queue",
    name: "XBOX Escalation Queue",
    distributionPolicyId: "Round_Robin_Policy"
);
```

## <a name="job-lifecycle"></a>ジョブのライフサイクル

ジョブを送信するときには、次のように分類ポリシー ID を指定します。 この特定の例では、値が数値 `7` 以上である `XBOX_Hardware` というラベルの worker を見つけることが要件です。

```csharp
await client.CreateJobWithClassificationPolicyAsync(
    channelId: ManagedChannels.AcsVoiceChannel,
    classificationPolicyId: "Classify_XBOX_Voice_Jobs",
    workerSelectors: new List<LabelSelector>
    {
        new (
            key: "XBOX_Hardware",
            @operator: LabelOperator.GreaterThanEqual,
            value: 7)
    }
);
```

構成が完了し、ジョブを送信する準備ができると、次のライフサイクル手順が実行されます。

1. ジョブはジョブ ルーターに送信され、分類ポリシーがアタッチされているので、評価されて `RouterJobReceived` と `RouterJobClassified` の両方が生成されます。
2. 次に、5 分のタイマーが開始され、worker を割り当てることができない場合に最終的にトリガーされます。 worker が登録されず、`RouterJobExceptionTriggered` ともう 1 つの `RouterJobClassified` という結果になったとします。
3. この時点で、ジョブは `XBOX_Escalation_Queue` になり、優先度は `10` に設定されます。