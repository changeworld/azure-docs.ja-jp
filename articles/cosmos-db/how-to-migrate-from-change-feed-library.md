---
title: 変更フィード プロセッサ ライブラリから Azure Cosmos DB .NET V3 SDK に移行する
description: ご利用のアプリケーションを、変更フィード プロセッサ ライブラリの使用から、Azure Cosmos DB SDK V3 の使用に移行する方法について説明します。
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: f651beb181430f65d0b4c86f285e74958f8366eb
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588885"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>変更フィード プロセッサ ライブラリから Azure Cosmos DB .NET V3 SDK に移行する

この記事では、[変更フィード プロセッサ ライブラリ](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet)を使用する既存のアプリケーションのコードを、最新バージョンの .NET SDK (.NET V3 SDK とも呼ばれる) の[変更フィード](change-feed.md)機能に移行するために必要な手順について説明します。

## <a name="required-code-changes"></a>必要なコード変更

.NET V3 SDK には、いくつかの破壊的変更があります。ご利用のアプリケーションを移行する上で重要な手順を次に示します。

1. `DocumentCollectionInfo` インスタンスを、監視されているコンテナーおよびリース コンテナーに対する `Container` 参照に変換します。
1. `WithProcessorOptions` を使用しているカスタマイズについては、間隔に `WithLeaseConfiguration` と `WithPollInterval` を使用し、[開始時刻](how-to-configure-change-feed-start-time.md)に `WithStartTime` を使用し、最大項目数を定義するために `WithMaxItems` を使用するように更新する必要があります。
1. `GetChangeFeedProcessorBuilder` 上の `processorName` については `ChangeFeedProcessorOptions.LeasePrefix` 上で構成されている値と一致するように設定します。それ以外の場合は `string.Empty` を使用します。
1. 変更は `IReadOnlyList<Document>` として配信されなくなりました。代わりに、それは `IReadOnlyCollection<T>` となります。ここで、`T` は定義する必要のある型です。基本項目クラスはもうありません。
1. 変更を処理する場合、実装は不要になりました。その代わりに、[デリゲートを定義](change-feed-processor.md#implementing-the-change-feed-processor)する必要があります。 デリゲートは静的関数とすることができます。実行中に状態を維持する必要がある場合は、独自のクラスを作成して、インスタンス メソッドをデリゲートとして渡すこともできます。

たとえば、変更フィード プロセッサを構築する元のコードが次のようであるとします。

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="ChangeFeedProcessorLibrary":::

移行されたコードは次のようになります。

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="ChangeFeedProcessorMigrated":::

そして、デリゲートは次のような静的メソッドにすることができます。

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="Delegate":::

## <a name="state-and-lease-container"></a>静的なリース コンテナー

変更フィード プロセッサ ライブラリと同様に、.NET V3 SDK の変更フィード機能でも[リース コンテナー](change-feed-processor.md#components-of-the-change-feed-processor)を使用して状態が格納されます。 ただし、スキーマは異なります。

SDK V3 変更フィード プロセッサでは、移行されたアプリケーション コードの最初の実行時に、古いライブラリの状態が検出され、新しいスキーマに自動的に移行されます。 

古いコードを使用するアプリケーションを安全に停止し、そのコードを新しいバージョンに移行し、移行されたアプリケーションを起動することができ、アプリケーションの停止中に発生した変更はいずれも新しいバージョンによって取得および処理されます。

> [!NOTE]
> ライブラリを使用するアプリケーションから .NET V3 SDK への移行は一方向となります。これは、状態 (リース) が新しいスキーマに移行されるため、 この移行に下位互換性はありません。


## <a name="additional-resources"></a>その他のリソース

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub の使用例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub のその他のサンプル](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>次のステップ

以下の記事で、変更フィード プロセッサに関してさらに詳しく知ることができます。

* [変更フィード プロセッサの概要](change-feed-processor.md)
* [変更フィード推定機能の使用](how-to-use-change-feed-estimator.md)
* [変更フィード プロセッサの開始時刻](how-to-configure-change-feed-start-time.md)
