---
title: Azure Cosmos コンテナー用のグローバルに分散されたトランザクション ストレージと分析ストレージ (プライベート プレビュー)
description: Azure Cosmos コンテナー用のトランザクション ストレージと分析ストレージの概要と、その構成オプションについて説明します。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 18cf43ba137c92fc00d5f8e82e13501d03b4b6a3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445400"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Azure Cosmos コンテナー用のグローバルに分散されたトランザクション ストレージと分析ストレージ

Azure Cosmos コンテナーは、トランザクション ストレージ エンジンと更新可能な分析ストレージ エンジン (プライベート プレビュー) の 2 つのストレージ エンジンによって内部的にサポートされます。 どちらのストレージ エンジンも、迅速な更新のためにログ構造化および書き込み最適化がなされています。 ただし、それぞれのエンコード方法は異なります。

* **トランザクション ストレージ エンジン** – トランザクションの読み取りとクエリの高速化のために行指向形式でエンコードされています。

* **分析ストレージ エンジン** - 分析のクエリとスキャンの高速化のために列形式でエンコードされています。

![ストレージ エンジンと Azure Cosmos DB API のマッピング](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

トランザクション ストレージ エンジンはローカル SSD によってサポートされますが、分析ストレージは低コストのクラスター外 SSD ストレージに格納されます。 次の表は、トランザクション ストレージと分析ストレージの主な違いを示しています。


|機能  |トランザクション ストレージ  |分析ストレージ |
|---------|---------|---------|
|Azure Cosmos コンテナーあたりの最大ストレージ容量 |   無制限      |    無制限     |
|論理パーティション キーあたりの最大ストレージ容量   |   10 GB      |   無制限      |
|ストレージのエンコード  |   行指向 (内部形式を使用)。   |   列指向 (Apache Parquet 形式を使用)。 |
|ストレージの場所 |   ローカル/クラスター内 SSD によってサポートされるレプリケートされたストレージ。 |  低コストのリモート/クラスター外 SSD によってサポートされるレプリケートされたストレージ。       |
|Durability  |    99.99999 (7 から 9 秒)     |  99.99999 (7 から 9 秒)       |
|データにアクセスする API  |   SQL、MongoDB、Cassandra、Gremlin、Tables、etcd。       | Apache Spark         |
|リテンション期間 (有効期限 (TTL))   |  ポリシーに基づき、`DefaultTimeToLive` プロパティを使用して Azure Cosmos コンテナー上に構成されます。       |   ポリシーに基づき、`ColumnStoreTimeToLive` プロパティを使用して Azure Cosmos コンテナー上に構成されます。      |
|GB あたりの料金    |   [価格に関するページ](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   [価格に関するページ](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|ストレージ トランザクションの料金    |  [価格に関するページ](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   [価格に関するページ](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>トランザクション ストレージと分析ストレージの利点

### <a name="no-etl-operations"></a>ETL 操作が不要

従来の分析パイプラインは、コンピューティング層とストレージ層の間の ETL (抽出、変換、読み込み) 操作を必要とするステージが複数あり、複雑になっています。 そのため、データ処理アーキテクチャが複雑になります。 これは、ストレージとコンピューティングのステージが複数あることによってコストが上昇し、ストレージとコンピューティングのさまざまなステージ間で大量のデータが転送されるために待機時間が長くなることを意味します。  

Azure Cosmos DB では、ETL 操作を実行するオーバーヘッドがありません。 各 Azure Cosmos コンテナーは、トランザクション ストレージ エンジンと分析ストレージ エンジンの両方によってサポートされており、トランザクション ストレージ エンジンと分析ストレージ エンジン間のデータ転送はデータベース エンジン内で行われるため、ネットワーク ホップがありません。 そのため、従来の分析ソリューションと比べて、待機時間とコストが大幅に削減されます。 また、トランザクション ワークロードと分析ワークロード用に、グローバルに分散された単一のストレージ システムを利用できます。  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>分析ストレージの複数バージョンを保存、更新、クエリを実行

分析ストレージは完全に更新可能であり、Azure Cosmos コンテナー上で発生したすべてのトランザクション更新の完全なバージョン履歴が保持されます。

トランザクション ストレージに対して行われたすべての更新は、必ず 30 秒以内に分析ストレージから見えるようになります。 

### <a name="globally-distributed-multi-master-analytical-storage"></a>グローバルに分散されたマルチマスター分析ストレージ

Azure Cosmos アカウントが単一のリージョンにスコープ指定されている場合、コンテナー内の (トランザクション ストレージと分析ストレージに) 格納されているデータも単一のリージョンにスコープ指定されます。 一方、Azure Cosmos アカウントがグローバルに分散されている場合は、コンテナーに格納されているデータもグローバルに分散されます。

複数の書き込みリージョンで構成された Azure Cosmos アカウントの場合、コンテナー内の (トランザクション ストレージと分析ストレージの両方) への書き込みは、常にローカル リージョンで実行されるため高速になります。

単一または複数リージョンの Azure Cosmos アカウントの場合、単一の書き込みリージョン (シングルマスター) か複数の書き込みリージョン (マルチマスター) かに関係なく、トランザクションと分析の両方の読み取り/クエリが、指定されたリージョンでローカルに実行されます。

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>トランザクション ワークロードと分析ワークロード間のパフォーマンスの分離

指定されたリージョンでは、トランザクション ワークロードはコンテナーのトランザクション/行ストレージに対して動作します。 一方、分析ワークロードは、コンテナーの分析/列ストレージに対して動作します。 2 つのストレージ エンジンは独立して動作し、ワークロード間のパフォーマンスは厳密に分離されます。

トランザクション ワークロードでは、プロビジョニングされたスループット (RU) が消費されます。 トランザクション ワークロードとは異なり、分析ワークロードのスループットは実際の消費量に基づきます。 分析ワークロードでは、オンデマンドでリソースが消費されます。

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB の Time to Live](time-to-live.md)
