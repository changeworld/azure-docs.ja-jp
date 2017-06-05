---
title: "Azure Cosmos DB の Table API の概要 | Microsoft Docs"
description: "人気のある OSS MongoDB API と Azure Cosmos DB を使用して、大量のキー値データを低遅延で格納およびクエリする方法を説明します。"
services: cosmos-db
author: bhanupr
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: d1e1f977c9023f4727d8c444b5e490e8f5ba8d9e
ms.contentlocale: ja-jp
ms.lasthandoff: 05/31/2017


---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Azure Cosmos DB の概要: Table API

[Azure Cosmos DB](introduction.md) は、ミッション クリティカルなアプリケーション向けの、Microsoft のグローバル分散マルチモデル データベース サービスです。 Azure Cosmos DB は、[ターン キー グローバル分散](distribute-data-globally.md)、[スループットとストレージの世界規模でのエラスティック スケーリング](partition-data.md)、99 パーセンタイルの 1 桁ミリ秒の待機時間、[明確に定義された 5 種類の整合性レベル](consistency-levels.md)を提供し、高可用性を保証します。これらはすべて[業界最高レベルの SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) によってサポートされています。 Azure Cosmos DB は、[データのインデックスを自動的に作成](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)します。スキーマとインデックスの管理に対処する必要はありません。 Azure Cosmos DB はマルチモデルであり、ドキュメント、キーと値、グラフ、列指向の各データ モデルをサポートします。 

![Azure Table Storage API と Azure Cosmos DB](./media/table-introduction/premium-tables.png) 

Azure Cosmos DB では、柔軟なスキーマによるキー/値ストアを必要とするアプリケーションのために Table API が提供されます。 Azure Cosmos DB で [Azure Table Storage](../storage/storage-introduction.md) SDK および REST API を使用できます。 Azure Cosmos DB を使用して、高スループット要件のテーブルを作成できます。 Azure Cosmos DB ではスループットが最適化されたテーブル (通称 "Premium テーブル") がサポートされ、現在パブリック プレビューが行われています。 

Azure Table Storage は、ストレージ要件が高くスループット要件が低いテーブルに対して、引き続き使用できます。 Azure Cosmos DB では、将来の更新時にストレージ最適化テーブルのサポートが導入される予定であり、既存および新規の Azure Table Storage アカウントは Azure Cosmos DB にアップグレードされます。

## <a name="premium-and-standard-table-apis"></a>Premium および標準の Table API
現在 Azure Table Storage を使用している場合、Azure Cosmos DB の "Premium テーブル" プレビューに移行することにより次のメリットが得られます。

|  | Azure Table Storage | Azure Cosmos DB: Table Storage (プレビュー) |
| --- | --- | --- |
| 待機時間 | 高速だが、待機時間の上限はなし | 読み取りと書き込みの待機時間は 1 桁ミリ秒であり、世界中のどこでもあらゆるスケールで 99 パーセンタイルの 10 ms 未満の読み取り待機時間と 15 ms 未満の書き込み待機時間でサポート |
| スループット | 拡張性は高いが、専用スループット モデルなし。 テーブルには、20,000 操作/秒の拡張性の制限あり | SLA によって保証された、[テーブルごとの専用の予約済みスループット](request-units.md)を備えた高い拡張性。 アカウントにはスループットの上限がなく、テーブルあたり 10,000, 000 操作/秒以上に対応 |
| グローバル分散 | HA のために 1 つの読み取り可能なセカンダリ読み取りリージョンをオプションで備えた単一リージョン。 フェールオーバーを開始できない | 1 から 30 以上の地域にわたる[ターンキー グローバル分散](distribute-data-globally.md)。世界中のどこでもいつでも[自動および手動フェールオーバー](regional-failover.md)をサポート |
| インデックス作成 | PartitionKey と RowKey のプライマリ インデックスのみ。 セカンダリ インデックスなし | すべてのプロパティに対する自動および完全なインデックス作成。インデックス管理なし |
| クエリ | クエリの実行では、プライマリ キーにインデックスを使用し、それ以外の場合はスキャンする。 | クエリは、クエリ時間の短縮のためにプロパティの自動インデックス作成を利用できる。 Azure Cosmos DB のデータベース エンジンは、集計、地理空間、および並べ替えをサポート可能。 |
| 整合性 | プライマリ リージョン内では強力、セカンダリ リージョンでは成り行き次第 | アプリケーションのニーズに基づいて、可用性、待機時間、スループット、および整合性のトレードオフを行う[明確に定義された 5 つの整合性レベル](consistency-levels.md) |
| 価格 | ストレージ最適化  | スループット最適化 |
| SLA | 99.9% の可用性 | 単一のリージョン内では 99.99% の可用性、さらにリージョンを追加して可用性を高めることが可能。 一般公開に対する[業界トップレベルの包括的な SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) |

## <a name="how-to-get-started"></a>ファースト ステップ

[Azure Portal](https://portal.azure.com) で Azure Cosmos DB アカウントを作成し、「[.NET を使用した Table API のクイック スタート](create-table-dotnet.md)」から開始します。 

## <a name="next-steps"></a>次のステップ

使用し始めるためのいくつかのヒントを次に示します。
* 既存の .NET Table SDK を使用した [Azure Cosmos DB の Table API](create-table-dotnet.md) の概要。
* [Azure Cosmos DB の世界規模での分散](distribute-data-globally.md)について理解します。
* [Azure Cosmos DB におけるスループットのプロビジョニング](request-units.md)について理解します。
