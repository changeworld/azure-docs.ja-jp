---
title: ジョブを分類する
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK を使用して、ジョブのプロパティを変更します
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 0648334ef4baef6c753afa222a5532356ad67590
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074926"
---
# <a name="classifying-a-job"></a>ジョブの分類

ジョブ ルーターで分類ポリシーを使用して、キューと優先順位を動的に解決し、ジョブへの worker セレクターのアタッチも行う方法について説明します。

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../quickstarts/create-communication-resource.md)。
- 省略可能: [ジョブ ルーターを開始する](../../quickstarts/router/get-started-router.md)ためのクイックスタートを完了する

## <a name="static-classification"></a>静的分類

SDK を使用してジョブを作成するときは、キュー、優先順位、worker セレクターだけを指定します。この方法は **静的分類** と呼ばれています。 次の例では、ジョブが `XBOX_DEFAULT_QUEUE` に優先順位 `1` で配置され、worker には `5` 以上の `XBOX_Hardware` スキルを持っていることが求められます。

> [!NOTE]
> ジョブは、最初に分類ポリシーなしで作成された場合でも、[送信後に再分類](#reclassify-a-job-after-submission)できます。 この場合、ジョブ ルーターによって、**ラベル** に対してポリシーの動作が評価され、キュー、優先順位、worker セレクターに必要な調整が行われます。

```csharp
var job = await client.CreateJobAsync(
    channelId: ManagedChannels.AcsVoiceChannel,
    channelReference: "12345",
    queueId: queue.Value.Id,
    priority: 1,
    workerSelectors: new List<LabelSelector>
    {
        new (
            key: "Location",
            @operator: LabelOperator.Equal,
            value: "Edmonton")
    });

// returns a new GUID such as: 4ad7f4b9-a0ff-458d-b3ec-9f84be26012b
```

## <a name="dynamic-classification"></a>動的分類

前に説明したように、ジョブを送信する簡単な方法は、送信時に優先順位、キュー、worker セレクターを指定することです。 これを行う場合、送信者は、これらの特性に関する知識を持っている必要があります。 送信者は、**分類ポリシー** と動的な動作を呼び出すための汎用 **ラベル** コレクションを指定することで、ジョブ ルーターの内部動作に関する明示的な知識を持つ必要がなくなります。

### <a name="create-a-classification-policy"></a>分類ポリシーを作成する

次の分類ポリシーでは、ローコードの [PowerFx](https://powerapps.microsoft.com/en-us/blog/what-is-microsoft-power-fx/) 言語を使用して、キューと優先順位の両方が選択されます。 この式では、ジョブ ラベル `Region` が `NA` と等しいかどうかが照合されて、検出されたジョブは `XBOX_NA_QUEUE` に入れられ、それ以外の場合は `XBOX_DEFAULT_QUEUE` に入れられます。  `XBOX_DEFAULT_QUEUE` も見つからなかった場合は、ジョブは `fallbackQueueId` で定義されているフォールバック キュー `DEFAULT_QUEUE` に自動的に送信されます。  さらに、ラベル `Hardware_VIP` が一致した場合は優先順位が `10` に設定され、それ以外の場合は `1` に設定されます。

```csharp
var policy = await client.SetClassificationPolicyAsync(
    id: "XBOX_NA_QUEUE_Priority_1_10",
    name: "Select XBOX Queue and set priority to 1 or 10",
    queueSelector: new QueueIdSelector(
        new ExpressionRule("If(job.Region = \"NA\", \"XBOX_NA_QUEUE\", \"XBOX_DEFAULT_QUEUE\")")
    ),
    workerSelectors: new List<LabelSelectorAttachment>
    {
        new StaticLabelSelector(
            new LabelSelector(
                key: "Language",
                @operator: LabelOperator.Equal,
                value: "English")
        )
    },
    prioritizationRule: new ExpressionRule("If(job.Hardware_VIP = true, 10, 1)"),
    fallbackQueueId: "DEFAULT_QUEUE"
);
```

### <a name="submit-the-job"></a>ジョブを送信する

次の例では、分類ポリシーによってジョブ ラベルが評価されます。 結果として、Job は `XBOX_NA_QUEUE` というキューに配置され、優先順位が `1` に設定されます。

```csharp
var dynamicJob = await client.CreateJobAsync(
    channelId: ManagedChannels.AcsVoiceChannel,
    channelReference: "my_custom_reference_number",
    classificationPolicyId: "XBOX_NA_QUEUE_Priority_1_10",
    labels: new LabelCollection()
    {
        { "Region", "NA" },
        { "Caller_Id", "tel:7805551212" },
        { "Caller_NPA_NXX", "780555" },
        { "XBOX_Hardware", 7 }
    }
);

// returns a new GUID such as: 4ad7f4b9-a0ff-458d-b3ec-9f84be26012b
```

## <a name="reclassify-a-job-after-submission"></a>送信後にジョブを再分類する

ジョブ ルーターによってジョブが受信され、ポリシーを使用して分類された後、ユーザーは SDK を使用してそれを再分類できます。 次の例では、**ジョブ ID** を指定して `ReclassifyJobAsync` メソッドを呼び出し、`Hardware_VIP` ラベルを含めるだけで、ジョブの優先順位を `10` に上げる 1 つの方法が示されています。

```csharp
var reclassifiedJob = await client.ReclassifyJobAsync(
    jobId: "4ad7f4b9-a0ff-458d-b3ec-9f84be26012b",
    classificationPolicyId: null,
    labelsToUpdate: new LabelCollection()
    {
        { "Hardware_VIP", true }
    }
);
```
