---
title: 変更フィード推定機能を使用する - Azure Cosmos DB
description: 変更フィード推定機能を使用して、お使いの変更フィード プロセッサの進行状況を分析する方法について説明します。
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/01/2021
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: 5d4e461b25a25ecdf0d4d89ee7f1c82b9d4a0737
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220166"
---
# <a name="use-the-change-feed-estimator"></a>変更フィード推定機能を使用する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、変更フィードを読み取るお使いの[変更フィード プロセッサ](./change-feed-processor.md) インスタンスの進行状況を監視する方法について説明します。

## <a name="why-is-monitoring-progress-important"></a>進行状況の監視が重要な理由

変更フィード プロセッサは、お使いの[変更フィード](./change-feed.md)を前方に移動し、変更をデリゲート実装に配信するポインターとして機能します。

お使いの展開されている変更フィード プロセッサは、CPU、メモリ、ネットワークなどの使用可能なリソースに基づいた特定の速度で変更を処理できます。

この速度が、お使いの Azure Cosmos コンテナーで変更が発生する速度よりも遅い場合は、お使いのプロセッサが遅延するようになります。

このシナリオを特定することにより、変更フィード プロセッサの展開を拡張する必要があるかどうかがわかります。

## <a name="implement-the-change-feed-estimator"></a>変更フィード推定機能の実装

### <a name="as-a-push-model-for-automatic-notifications"></a>自動通知のプッシュ モデルとして

変更フィード推定機能は、[変更フィード プロセッサ](./change-feed-processor.md)と同様に、プッシュ モデルとして機能できます。 推定機能は、最後に処理された項目 (リース コンテナーの状態で定義される) とコンテナーの最新の変更の差を測定し、この値をデリゲートにプッシュします。 この測定値が取得される間隔 (既定値は 5 秒) は、カスタマイズも可能です。

たとえば、お使いの変更フィード プロセッサが次のように定義されているとします。

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

プロセッサで `GetChangeFeedEstimatorBuilder` が使用されるよう、推定機能を初期化する正しい方法は次のようになります。

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

ここでは、プロセッサと推定機能の両方の `leaseContainer` と名前が同じです。

他の 2 つのパラメーターはデリゲートです。このデリゲートは、プロセッサによって **読み取られる保留中の変更の数** と、この測定値を取得する時間間隔を表す数値を受け取ります。

推定を受け取るデリゲートの例は次のとおりです。

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

この推定はお使いの監視ソリューションに送信して、時間と共に進行状況がどのようなものかを把握するのに使用できます。

### <a name="as-an-on-demand-detailed-estimation"></a>オンデマンドの詳細な推定として

プッシュ モデルとは異なり、オンデマンドで推定を取得できる代替手段があります。 このモデルでは、次のような、より詳細な情報も提供されます。

* リースあたりの推定ラグ。
* 各リースを所有して処理するインスタンス。これにより、インスタンスに問題があるかどうかを識別できます。

たとえば、変更フィード プロセッサがこのように定義されているとします。

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimatorDetailed)]

同じリース構成で、次のように推定器を作成できます。

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimatorDetailed)]

必要なときに必要な頻度で、詳細な推定を取得できます。

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=GetIteratorEstimatorDetailed)]

各 `ChangeFeedProcessorState` には、リース情報とラグ情報、およびそれを所有している現在のインスタンスがだれであるかが含まれます。 

> [!NOTE]
> 変更フィード推定機能は、お使いの変更フィード プロセッサの一部として配置する必要はありません。また、同じプロジェクトに含める必要もありません。 個別に、まったく別のインスタンスで実行でき、そうすることをお勧めします。 同じ名前とリース構成のみを使用する必要があります。

## <a name="additional-resources"></a>その他のリソース

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub の使用例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub のその他のサンプル](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>次のステップ

以下の記事で、変更フィード プロセッサに関してさらに詳しく知ることができます。

* [変更フィード プロセッサの概要](change-feed-processor.md)
* [変更フィード プロセッサの開始時刻](./change-feed-processor.md#starting-time)