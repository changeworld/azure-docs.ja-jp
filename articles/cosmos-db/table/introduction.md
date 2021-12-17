---
title: Azure Cosmos DB Table API の概要
description: Azure Tables API と Azure Cosmos DB を使用して、大量のキー値データを低遅延で格納および照会する方法を説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 11/03/2021
ms.author: sngun
ms.openlocfilehash: aa065a4c5cd8100ee9e6cb235555f9aefc910643
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504780"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Azure Cosmos DB の概要:テーブル API
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

[Azure Cosmos DB](introduction.md) には、Azure Table Storage 向けに作成されたアプリケーションの中でも、以下に挙げたような特に高度な機能を必要とするアプリケーションのための Table API が用意されています。

* [ターンキー グローバル分散](../distribute-data-globally.md)。
* 世界規模での[専用スループット](../partitioning-overview.md) (プロビジョニングされたスループットを使用している場合)。
* 99 パーセンタイルで 10 ミリ秒未満の待ち時間。
* 高可用性の保証。
* 自動セカンダリ インデックス作成。

[Azure テーブル SDK](https://devblogs.microsoft.com/azure-sdk/announcing-the-new-azure-data-tables-libraries/) は、.NET、Java、Python、Node.js、Go で利用できます。 これらの SDK を使用することで、Table Storage または Cosmos DB テーブルを対象にすることができます。 Azure テーブル SDK を使用して Azure Table Storage 用に作成されたアプリケーションは、Premium 機能を利用するために、コードを変更せずに Azure Cosmos DB Table API に移行できます。

> [!NOTE]
> Azure Cosmos DB の Table API で[サーバーレス容量モード](../serverless.md)が利用できるようになりました。

> [!IMPORTANT]
> .NET Azure テーブル SDK [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/) では、Table API によってサポートされる最新の機能が提供されます。 Azure Tables クライアント ライブラリは、コードを変更しない Azure Table Storage または Azure Cosmos DB table service エンドポイントをシームレスにターゲットにできます。

## <a name="table-offerings"></a>Table のサービス

現在 Azure Table Storage を使用している場合、Azure Cosmos DB Table API に移行することにより次のメリットが得られます。

| 特徴量 | Azure Table Storage | Azure Cosmos DB Table API |
| --- | --- | --- |
| Latency | 高速だが、待ち時間の上限はなし。 | 読み取りと書き込みの待ち時間は数ミリ秒であり、世界中のどこでもあらゆるスケールで 99 パーセンタイルの 10 ミリ秒未満の読み取りおよび書き込み待ち時間でサポート。 |
| スループット | 可変スループット モデル。 テーブルには、20,000 操作/秒のスケーラビリティの制限あり。 | SLA によって保証された、[テーブルごとの専用の予約済みスループット](../request-units.md)を備えた高いスケーラビリティ。 アカウントにはスループットの上限がなく、テーブルあたり 10,000, 000 操作/秒以上に対応。 |
| グローバル配信 | 高可用性のために 1 つの読み取り可能なセカンダリ読み取りリージョンをオプションで備えた単一リージョン。 | 1 から任意の数のリージョンにわたる[ターンキー グローバル分散](../distribute-data-globally.md)。 いつでも、世界中どこにでも、[自動フェールオーバーと手動フェールオーバー](../high-availability.md)を実行可能。 任意のリージョンで書き込み操作を受け入れることができる複数書き込みリージョン。 |
| インデックス作成 | PartitionKey と RowKey のプライマリ インデックスのみ。 セカンダリ インデックスなし。 | 既定ではすべてのプロパティに対する自動および完全なインデックス作成。インデックス管理なし。 |
| クエリ | クエリの実行では、プライマリ キーにインデックスを使用し、それ以外の場合はスキャンする。 | クエリは、クエリ時間の短縮のためにプロパティの自動インデックス作成を利用できる。 |
| 一貫性 | プライマリ リージョン内では厳密な整合性。 セカンダリ リージョン内では最終的な整合性。 | アプリケーションのニーズに基づいて、可用性、待ち時間、スループット、および整合性のトレードオフを行う[明確に定義された 5 つの整合性レベル](../consistency-levels.md)。 |
| 価格 | 使用量ベース。 | [使用量ベース](../serverless.md)と[プロビジョニング済みの容量](../set-throughput.md)の両方のモードで使用できます。 |
| SLA | レプリケーション戦略によっては、99.9% から 99.99% の可用性。 | 1 つのリージョンのアカウントでは、読み取り可用性が 99.999%、書き込みの可用性が 99.99% であり、複数リージョンのアカウントでは 99.999% の書き込み可用性。 可用性、待ち時間、スループット、および一貫性をカバーする[包括的な SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)。 |

## <a name="get-started"></a>はじめに

[Azure Portal](https://portal.azure.com) で Azure Cosmos DB アカウントを作成しましょう。 その後、[.NET を使用したテーブル API のクイックスタート](create-table-dotnet.md)をお読みください。

## <a name="next-steps"></a>次のステップ

使用し始めるためのいくつかのヒントを次に示します。
* [テーブル API を使用した .NET アプリケーションの構築](create-table-dotnet.md)
* [.NET での Table API を使用した開発](tutorial-develop-table-dotnet.md)
* [Table API を使用してテーブル データのクエリを実行する方法](tutorial-query-table.md)
* [テーブル API を使用して Azure Cosmos DB グローバル分散を設定する方法](tutorial-global-distribution-table.md)
* [Azure Cosmos DB Table .NET Standard SDK](dotnet-standard-sdk.md)
* [Azure Cosmos DB Table .NET SDK](dotnet-sdk.md)
* [Azure Cosmos DB Table Java SDK](java-sdk.md)
* [Azure Cosmos DB Table Node.js SDK](nodejs-sdk.md)
* [Azure Cosmos DB Table SDK for Python](python-sdk.md)
