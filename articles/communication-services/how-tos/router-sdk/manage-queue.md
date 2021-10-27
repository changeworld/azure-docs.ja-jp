---
title: ジョブ ルーターでキューを管理する
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK を使用してキューの動作を管理する
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 80f7aaf45cea9ced8721d9b13da4d8ab34b249f2
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074925"
---
# <a name="manage-a-queue"></a>キューを管理する

このガイドでは、ジョブ ルーター キューを作成し、管理する手順について説明します。

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../quickstarts/create-communication-resource.md)。
- 省略可能: [ジョブ ルーターを開始する](../../quickstarts/router/get-started-router.md)ためのクイックスタートを完了する

## <a name="create-a-queue"></a>キューを作成する

ジョブ ルーターで簡単なキューを作成するには、SDK を使用して **キュー ID**、**名前**、**分散ポリシー ID** を指定します。 分散ポリシーは、その存在がキューの作成時にジョブ ルーターによって確認されるため、前もって作成する必要があります。 次の例では、worker にジョブ オファーを生成する方法を制御する目的で分散ポリシーが作成されます。

```csharp
var distributionPolicy = await client.SetDistributionPolicyAsync(
    id: "Longest_Idle_45s_Min1Max10",
    name: "Longest Idle matching with a 45s offer expiration; min 1, max 10 offers",
    offerTTL: TimeSpan.FromSeconds(45),
    mode: new LongestIdleMode(
        minConcurrentOffers: 1,
        maxConcurrentOffers: 10)
);

var queue = await client.SetQueueAsync(
    id: "XBOX_DEFAULT_QUEUE",
    name: "XBOX Default Queue",
    distributionPolicy: "Longest_Idle_45s_Min1Max10"
);
```
## <a name="update-a-queue"></a>キューを更新する

`SetQueue` または `SetQueueAsync` のメソッドが呼び出されたとき、ジョブ ルーター SDK によって新しいキューが作成されるか、既存のキューが更新されます。

```csharp
var queue = await client.SetQueueAsync(
    id: "XBOX_DEFAULT_QUEUE",
    name: "XBOX Default Queue",
    distributionPolicy: "Longest_Idle_45s_Min1Max10"
);
```

## <a name="delete-a-queue"></a>キューを削除する

ジョブ ルーター SDK を使用してキューを削除するには、**キュー ID** を渡して `DeleteQueue` または `DeleteQueueAsync` のメソッドを呼び出します。

```csharp
var result = await client.DeleteQueueAsync("XBOX_DEFAULT_QUEUE");
```

> [!NOTE]
> キューを削除するには、アクティブなジョブがそのキューに割り当てられていないことを確認する必要があります。 また、文字列値に基づいて ID 別にキューを選択する式を利用する分類ポリシーまたはルールでそのキューが参照されていないことを確認します。