---
title: Azure Cosmos DB を使用したデータのグローバル分散 | Microsoft Docs
description: グローバル分散型のマルチモデル データベース サービスである Azure Cosmos DB のグローバル データベースを使用した、地球規模の geo レプリケーション、マルチマスター、フェールオーバー、データ復旧について学習します。
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.openlocfilehash: 227c243d82665dc533e3bfa6a1fe3e9bb775a262
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408897"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Azure Cosmos DB でのグローバルなデータの分散

Azure はユビキタスです。50 か所以上の地理的リージョンでグローバル展開されており、継続的に拡大しています。 その世界的なプレゼンスとマルチマスターのサポートにより、Azure が開発者に提供する差別化機能の 1 つは、グローバル分散アプリケーションを簡単に構築し、デプロイ、管理できることです。

[Azure Cosmos DB](../cosmos-db/introduction.md) は、ミッション クリティカルなアプリケーション向けの、Microsoft のグローバル分散型マルチモデル データベース サービスです。 Azure Cosmos DB は、ターンキー グローバル分散、[スループットとストレージの世界規模でのエラスティック スケーリング](../cosmos-db/partition-data.md)、99 パーセンタイルの 1 桁ミリ秒の読み書き待機時間、[明確に定義された整合性モデル](consistency-levels.md)を提供し、高可用性を保証します。これらはすべて[業界最高レベルの包括性を持った SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) によってサポートされています。 Azure Cosmos DB では、[すべてのデータのインデックスが自動的に作成](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)されるため、ユーザーがスキーマやインデックスの管理に対処する必要はありません。

## <a name="global-distribution-with-multi-master"></a>マルチマスターによるグローバル分散

クラウド サービスとしての Azure Cosmos DB は、ドキュメント、キー値、グラフ、列ファミリ データ モデルに対してマルチテナント、グローバル分散、マルチマスターをサポートするように、慎重に設計されています。

![パーティション分割され、3 つのリージョンに分散された Azure Cosmos DB コンテナー](./media/distribute-data-globally/global-apps.png)

**1 つの Azure Cosmos DB コンテナーをパーティション分割し、複数の Azure リージョンに分散**

Azure Cosmos DB を構築することで学んだように、グローバル分散を後付けすることはできません。 "単一サイト" のデータベース システムに "上乗せ" することはできないのです。 グローバル分散データベースが提供する機能は、"単一サイト" データベースが提供する従来の地理的なディザスター リカバリー (Geo-DR) によって提供される機能の枠を越えて多岐にわたります。 Geo-DR 機能を提供する単一サイト データベースは、グローバル分散データベースの厳密なサブセットです。

Azure Cosmos DB のターンキー グローバル分散により、開発者はデータベース ログ上でラムダ パターンを使用するか、複数のリージョン間で "二重書き込み" を実行することによって、レプリケーションの独自のスキャフォールディングを構築する必要はありません。 このような方法の正確性を保証しながら、厳密な SLA を提供することは不可能であるため、これらの方法はお勧め*しません*。

## <a id="Next Steps"></a>次のステップ

* [Azure Cosmos DB でターンキー グローバル分散を有効にする方法](distribute-data-globally-turnkey.md)

* [Azure Cosmos DB のグローバル分散の主な利点](distribute-data-globally-benefits.md)

* [Azure Cosmos DB のグローバル データベース レプリケーションを構成する方法](tutorial-global-distribution-sql-api.md)

* [Azure Cosmos DB アカウントのマルチマスターを有効にする方法](enable-multi-master.md)

* [Azure Cosmos DB での自動および手動フェールオーバーの動作](regional-failover.md)

* [Azure Cosmos DB での競合の解決について](multi-master-conflict-resolution.md)

* [オープン ソース NoSQL データベースでのマルチマスターの使用](multi-master-oss-nosql.md)
