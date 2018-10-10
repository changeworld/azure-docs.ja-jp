---
title: Azure Cosmos DB Cassandra API アカウントにデータを移行する
description: CQL Copy コマンドと Spark を使用して、Apache Cassandra から Azure Cosmos DB Cassandra API にデータをコピーする方法について説明します。
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.author: govindk
ms.topic: tutorial
ms.date: 09/24/2018
ms.reviewer: sngun
ms.openlocfilehash: 0bf5e47513ded4b2c65e7291db497e53a42776a8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46976178"
---
# <a name="migrate-your-data-to-azure-cosmos-db-cassandra-api-account"></a>Azure Cosmos DB Cassandra API アカウントにデータを移行する

このチュートリアルでは、Apache Cassandra のデータを Azure Cosmos DB Cassandra API に移行する方法を説明します。 

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * 移行の計画
> * 移行の前提条件
> * cqlsh COPY コマンドを使用してデータを移行する
> * Spark を使用してデータを移行する 

## <a name="plan-for-migration"></a>移行の計画

Azure Cosmos DB Cassandra API にデータを移行する前に、ワークロードに必要なスループットを推定する必要があります。 一般に、CRUD 操作で必要な平均スループットで始めた後、抽出、変換、読み込み (ETL) またはスパイク性の操作に必要な追加スループットを含めることをお勧めします。 移行を計画するには次の詳細情報が必要です。 

* **既存データ サイズまたは推定データ サイズ:** 最小データベース サイズとスループット要件を定義します。 新しいアプリケーションのデータ サイズを推定する場合は、データが行に一様に分布しているものと見なし、データ サイズを掛けることによって値を推定できます。 

* **必要なスループット:** 読み取り (クエリ/取得) および書き込み (更新/削除/挿入) の概算スループット レート。 この値は、必要な要求ユニット数と安定した状態データのサイズを計算するために必要です。  

* **スキーマの取得:** cqlsh を使用して既存の Cassandra クラスターに接続し、Cassandra からスキーマをエクスポートします。 

  ```bash
  cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
  ```

既存のワークロードの要件を明らかにした後は、収集したスループットの要件に従って Azure Cosmos DB アカウント、データベース、およびコンテナーを作成する必要があります。  

* **操作の RU 料金の決定:** 任意の Azure Cosmos DB Cassandra API SDK を使用して、RU を決定できます。 この例では、.NET バージョンでの RU 負担取得方法を示します。

  ```csharp
  var tableInsertStatement = table.Insert(sampleEntity);
  var insertResult = await tableInsertStatement.ExecuteAsync();

  foreach (string key in insertResult.Info.IncomingPayload)
    {
       byte[] valueInBytes = customPayload[key];
       string value = Encoding.UTF8.GetString(valueInBytes);
       Console.WriteLine($"CustomPayload:  {key}: {value}");
    }
  ```

* **必要なスループットの割り当て:** Azure Cosmos DB は、要件の増大に従って、ストレージとスループットを自動的にスケーリングできます。 [Azure Cosmos DB の要求ユニット計算ツール](https://www.documentdb.com/capacityplanner)を使って、スループットのニーズを推定することができます。 

## <a name="prerequisites-for-migration"></a>移行の前提条件

* **Azure Cosmos DB Cassandra API アカウントでテーブルを作成する:** データの移行を開始する前に、Azure portal または cqlsh からすべてのテーブルを事前に作成します。

* **スループットを上げる:** データの移行にかかる時間は、Azure Cosmos DB のテーブルに対してプロビジョニングしたスループットの量に依存します。 移行の間だけスループットを高くします。 スループットが高くなるほど、レート制限を回避し、移行に要する時間を短縮できます。 移行が完了したら、コストを節約するためにスループットを下げます。 スループットの引き上げについて詳しくは、Azure Cosmos DB コンテナーに対する[スループットの設定](set-throughput.md)に関するページをご覧ください。 また、Azure Cosmos DB アカウントをソース データベースと同じリージョンにすることもお勧めします。 

* **SSL を有効にする:** Azure Cosmos DB には、厳密なセキュリティ要件と基準が存在します。 アカウントを操作するときは、SSL が有効になっていることを確認してください。 SSH で CQL を使用する場合は、SSL の情報を指定するオプションがあります。

## <a name="options-to-migrate-data"></a>データを移行するためのオプション

次のオプションを使用して、既存の Cassandra ワークロードから Azure Cosmos DB にデータを移動できます。

* [cqlsh COPY コマンドの使用](#using-cqlsh-copy-command)  
* [Spark の使用](#using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>cqlsh COPY コマンドを使用してデータを移行する

Azure Cosmos DB Cassandra API アカウントにローカル データをコピーするには、[CQL COPY コマンド](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh)を使用します。 データをコピーするには、次の手順を使用します。

1. Cassandra API アカウントの接続文字列情報を取得します。

   * [Azure portal](https://portal.azure.com) にサインインし、Azure Cosmos DB アカウントに移動します。

   * **[接続文字列]** ウィンドウを開きます。このウィンドウには、cqlsh から Cassandra API アカウントに接続するために必要なすべての情報が含まれます。

2. ポータルからの接続情報を使用して cqhsh にサインインします。

3. CQL COPY コマンドを使用して、ローカル データを Cassandra API アカウントにコピーします。

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Spark を使用してデータを移行する 

Spark を使用して Azure Cosmos DB Cassandra API にデータを移行するには、次の手順を使用します。

- [Azure Databricks](cassandra-spark-databricks.md) または [HDInsight クラスター](cassandra-spark-hdinsight.md)をプロビジョニングします 

- [テーブル コピー操作](cassandra-spark-table-copy-ops.md)を使用して、ターゲットの Cassandra API エンドポイントにデータを移動します 

Azure 仮想マシンまたは他のクラウドの既存クラスターにデータが存在する場合は、Spark ジョブを使用したデータの移行が推奨されるオプションです。 これには、Spark を 1 回または定期的な取り込みの仲介者として設定する必要があります。 オンプレミスと Azure の間に ExpressRoute 接続を使用することで、この移行を速くできます。 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Cosmos DB Cassandra API アカウントにデータを移行する方法を学びました。 これで、概念セクションに進み、Azure Cosmos DB の詳細について学習できるようになりました。 

> [!div class="nextstepaction"]
> [Azure Cosmos DB の調整可能なデータの一貫性レベル](../cosmos-db/consistency-levels.md)


