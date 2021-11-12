---
title: Azure Communication Services のルーター ルール エンジンの概念
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services のジョブ ルーターのルール エンジンに関する概念について説明します。
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5d53f2492888323107fa539283f128ed6b6ebd7c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319227"
---
# <a name="job-router-rules-engine-concepts"></a>ジョブ ルーターのルール エンジンの概念

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Azure Communication Services のジョブ ルーターでは、拡張可能なルール エンジンを使用して、データの処理と、ジョブと worker に関する決定が行われます。 このドキュメントでは、ルール エンジンによって行われることと、それを実装に適用することが必要になる場合がある理由について説明します。

## <a name="rules-engine-overview"></a>ルール エンジンの概要

多くの場合、実装の動作を制御するには、複雑な意思決定が含まれます。 ジョブ ルーターにより、さまざまなルール エンジンを使用してプログラムで動作を呼び出す柔軟な方法が提供されます。 通常は、ジョブ ルーターのルール エンジンによって、オブジェクトで定義されている **ラベル** (ジョブ、キュー、worker など) のセットが入力として取得され、ルールが適用されて、出力が生成されます。

> [!NOTE]
> ルール エンジンでは、入力としてラベルが一般に使用されますが、ラベルの評価を使用せずにキュー ID などの値を設定することもできます。

ジョブ ルーターでルールを適用する場所によって、結果が異なる場合があります。 たとえば、分類ポリシーでは、ジョブの入力で定義されているラベルに基づいて、キュー ID を選択できます。 別の例として、分散ポリシーでは、`RouterRule` によって定義されているカスタム スコアリング規則を使用して、最適な worker を見つけることができます。

### <a name="example-use-a-static-rule-in-a-classification-policy-to-set-the-queue-id"></a>例: 分類ポリシーで静的ルールを使用してキュー ID を設定する

この例では、`RouterRule` の一種である `StaticRule` を使用して、分類ポリシー ID `XBOX_QUEUE_POLICY` を参照するすべてのジョブのキュー ID を設定できます。

```csharp
await client.SetClassificationPolicyAsync(
    id: "XBOX_QUEUE_POLICY",
    queueSelector: new QueueIdSelector(new StaticRule("XBOX"))
)
```
## <a name="routerrule-types"></a>RouterRule の種類

ジョブ ルーターの `RouterRule` には、ジョブを柔軟に処理できるよう、次の種類があります。

**静的ルール** - このルールを使用すると、特定のキュー ID の選択など、静的な値を指定できます。

**式ルール** - 式ルールでは、[PowerFx](https://powerapps.microsoft.com/en-us/blog/what-is-microsoft-power-fx/) 言語を使用して、ルールをインライン式として定義します。

**Azure 関数ルール** - このルールで URI と `AzureFunctionRuleCredential` を指定することにより、ジョブ ルーターでペイロードとして入力ラベルを渡し、出力値で応答できます。 このルールの種類は、要件が複雑な場合に使用できます。

> [!NOTE]
> **直接マップ ルール** は Job Router SDK の一部ですが、現時点では `NearestQueueLabelSelector` でのみサポートされています。
