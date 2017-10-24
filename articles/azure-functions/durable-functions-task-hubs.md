---
title: "Durable Functions におけるタスク ハブ - Azure"
description: "Azure Functions の Durable Functions 拡張機能におけるタスク ハブについて説明します。 タスク ハブを構成する方法について説明します。"
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 313daf1c105caa8569ed43e59d9e18f184599214
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Durable Functions におけるタスク ハブ (Azure Functions)

[Durable Functions](durable-functions-overview.md) の "*タスク ハブ*" は、1 つの Azure ストレージ アカウントのコンテキスト内でオーケストレーションとアクティビティに使用される論理上のコンテナーです。 同じタスク ハブに複数の関数が存在できるほか、複数の関数アプリが存在することもできます。また、タスク ハブにはアプリケーション コンテナーとしての働きがあります。

タスク ハブを明示的に作成する必要はありません。 これらは *host.json* ファイルに宣言されている名前で、ランタイムによって自動的に初期化されます。 それぞれのタスク ハブは、1 つのストレージ アカウントに一連のストレージ キュー、テーブル、BLOB を独自に保有します。 特定のタスク ハブで実行されるすべての関数アプリは、同じストレージ リソースを共有します。 オーケストレーター関数とアクティビティ関数は、同じタスク ハブに属しているときに限り、情報をやり取りすることができます。

## <a name="configuring-a-task-hub-in-hostjson"></a>host.json でのタスク ハブの構成

タスク ハブの名前は、関数アプリの *host.json* ファイルで構成することができます。

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

タスク ハブの名前は、先頭文字をアルファベットとする必要があります。また使用できるのはアルファベットと数値だけです。 指定しない場合、関数アプリの既定のタスク ハブ名は **DurableFunctionsHub** です。

> [!NOTE]
> 1 つのストレージ アカウントを共有する複数の関数アプリがある場合は、関数アプリごとに異なるタスク ハブ名を構成するようお勧めします。 そのようにすることで関数アプリが互いに適切に分離されます。

## <a name="azure-storage-resources"></a>Azure Storage のリソース

タスク ハブは、いくつかの Azure Storage リソースから成ります。

* 1 つまたは複数のコントロールキュー。
* 1 つの作業項目キュー。
* 1 つの履歴テーブル。
* Lease Blob を少なくとも 1 つ含んだ 1 つのストレージ コンテナー。

これらすべてのリソースは、オーケストレーター関数またはアクティビティ関数の実行時 (またはスケジュール時) に、既定の Azure ストレージ アカウントに自動的に作成されます。 これらのリソースがどのように使用されるかについては、[パフォーマンスとスケール](durable-functions-perf-and-scale.md)に関する記事で説明しています。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [バージョン管理の方法](durable-functions-versioning.md)

