---
title: Azure Cosmos DB の Cassandra API アカウントにデータを移行する - チュートリアル
description: このチュートリアルでは、Apache Cassandra から Azure Cosmos DB の Cassandra API アカウントにデータをコピーする方法について説明します。
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 7c55cbf4b8739a885c499c820a7e68825522ea4e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449292"
---
# <a name="tutorial-migrate-your-data-to-a-cassandra-api-account"></a>チュートリアル: Cassandra API アカウントにデータを移行する
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

開発者は、所有しているオンプレミスまたはクラウドで実行される既存の Cassandra のワークロードを Azure に移行したいと考えることがあります。 そのようなワークロードを、Azure Cosmos DB の Cassandra API アカウントに移行できます。 このチュートリアルでは、Apache Cassandra のデータを Azure Cosmos DB の Cassandra API アカウントに移行するために利用できる複数のオプションについて、その手順を説明します。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * 移行の計画
> * 移行の前提条件
> * `cqlsh` `COPY` コマンドを使用してデータを移行する
> * Spark を使用してデータを移行する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites-for-migration"></a>移行の前提条件

* **スループットのニーズを推定する:** Azure Cosmos DB の Cassandra API アカウントにデータを移行する前に、ワークロードに必要なスループットを推定する必要があります。 一般に、CRUD 操作で必要な平均スループットから始め、次に、抽出、変換、読み込み、またはスパイク時の操作に必要な追加スループットを含めます。 移行を計画するには次の詳細情報が必要です。 

  * **既存のデータ サイズまたは推定データ サイズ:** データベースの最小サイズとスループットの要件を定義します。 新しいアプリケーションのデータ サイズを推定する場合は、データが行に一様に分散しているものと見なし、データ サイズを掛けることによって値を推定できます。 

  * **必要なスループット:** 読み取り (クエリ、取得) および書き込み (更新、削除、挿入) 操作の概算スループット レート。 この値は、必要な要求ユニット数と安定した状態のデータ サイズを計算するために必要です。  

  * **スキーマ:** `cqlsh` を使用して既存の Cassandra クラスターに接続し、Cassandra からスキーマをエクスポートします。 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    既存のワークロードの要件を明らかにした後は、収集したスループットの要件に従って Azure Cosmos DB アカウント、データベース、およびコンテナーを作成します。  

  * **操作の RU 料金の決定:** Cassandra API によってサポートされている任意の SDK を使用して、RU を決定できます。 この例では、.NET バージョンでの RU 負担取得方法を示します。

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         double value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **必要なスループットの割り当て:** Azure Cosmos DB は、要件の増大に従って、ストレージとスループットを自動的にスケーリングできます。 [Azure Cosmos DB の要求ユニット計算ツール](https://www.documentdb.com/capacityplanner)を使って、スループットのニーズを推定することができます。 

* **Cassandra API アカウントでテーブルを作成する:** データの移行を開始する前に、Azure portal または `cqlsh` を使用してすべてのテーブルを事前に作成します。 データベースレベルのスループットがある Azure Cosmos DB アカウントに移行しようとしている場合は、コンテナーの作成時に必ずパーティション キーを指定するようにしてください。

* **スループットを向上させる:** データの移行にかかる時間は、Azure Cosmos DB のテーブルに対してプロビジョニングしたスループットの量に依存します。 移行の間だけスループットを高くします。 スループットが高くなるほど、レート制限を回避し、移行に要する時間を短縮できます。 移行が完了したら、コストを節約するためにスループットを下げます。 また、Azure Cosmos DB アカウントをソース データベースと同じリージョンに作成することもお勧めします。 

* **TLS を有効にする:** Azure Cosmos DB には、厳密なセキュリティ要件と基準があります。 アカウントを操作するときは、TLS が有効になっていることを確認してください。 SSH で CQL を使用する場合は、TLS の情報を指定するオプションがあります。

## <a name="options-to-migrate-data"></a>データを移行するためのオプション

`cqlsh` `COPY` コマンドまたは Spark を使用して、既存の Cassandra ワークロードから Azure Cosmos DB にデータを移動できます。 

### <a name="migrate-data-by-using-the-cqlsh-copy-command"></a>cqlsh COPY コマンドを使用してデータを移行する

[CQL COPY コマンド](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh)を使用して、ローカル データを Azure Cosmos DB の Cassandra API アカウントにコピーします。

1. Cassandra API アカウントの接続文字列情報を取得します。

   * [Azure portal](https://portal.azure.com) にサインインし、Azure Cosmos DB アカウントに移動します。

   * **[接続文字列]** ウィンドウを開きます。 ここには、`cqlsh` から Cassandra API アカウントに接続するために必要なすべての情報が表示されます。

1. ポータルからの接続情報を使用して `cqlsh` にサインインします。

1. `CQL` `COPY` コマンドを使用して、ローカル データを Cassandra API アカウントにコピーします。

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

### <a name="migrate-data-by-using-spark"></a>Spark を使用してデータを移行する 

Spark を使用して Cassandra API アカウントにデータを移行するには、次の手順を使用します。

1. [Azure Databricks クラスター](cassandra-spark-databricks.md)または [Azure HDInsight クラスター](cassandra-spark-hdinsight.md)をプロビジョニングします。 

1. [テーブルのコピー操作](cassandra-spark-table-copy-ops.md)を使用して、コピー先の Cassandra API エンドポイントにデータを移動します。 

Azure 仮想マシンまたは他のクラウド上の既存のクラスターにデータが存在する場合は、Spark ジョブを使用したデータの移行が推奨されるオプションです。 これを行うには、1 回限りまたは定期的なインジェストの中間段階として Spark を設定する必要があります。 オンプレミス環境と Azure の間で Azure ExpressRoute 接続を使用することで、この移行を加速できます。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、リソース グループ、Azure Cosmos DB アカウント、およびすべての関連リソースを削除できます。 これを行うには、仮想マシン用のリソース グループを選択し、 **[削除]** を選択した後、削除するリソース グループの名前を確認します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Cosmos DB の Cassandra API アカウントにデータを移行する方法を説明しました。 次に、Azure Cosmos DB のその他の概念を学習します。

> [!div class="nextstepaction"]
> [Azure Cosmos DB の調整可能なデータの一貫性レベル](../cosmos-db/consistency-levels.md)




