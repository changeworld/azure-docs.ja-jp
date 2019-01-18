---
title: mongoimport と mongorestore を使用して MongoDB のデータを Azure Cosmos DB に移行する
description: mongoimport と mongorestore を使用して Cosmos DB にデータをインポートする方法について説明します。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
Customer intent: As a developer, I want to migrate the data from my existing MongoDB to Cosmos DB.
ms.openlocfilehash: 9226a49af67659ebd7bebd9beca397830ee808bb
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039052"
---
# <a name="migrate-your-mongodb-data-to-azure-cosmos-db"></a>MongoDB のデータを Azure Cosmos DB に移行する

 このチュートリアルでは、MongoDB に格納されているデータを、Cosmos DB の MongoDB 用 API を使用するように構成された Azure Cosmos DB に移行する方法を説明します。 MongoDB からデータをインポートし、Azure Cosmos DB SQL API でそれを使用する場合は、[データ移行ツール](import-data.md)を使用してデータをインポートする必要があります。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * 移行プランを作成する。
> * mongoimport を使用してデータを移行する。
> * mongorestore を使用してデータを移行する。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

以降を始める前に、次の前提条件を確認して満たしておいてください。

### <a name="plan-for-the-migration"></a>移行を計画する

このセクションでは、データの移行を計画する方法について説明します。 RU の料金を見積もり、お使いのコンピューターからクラウド サービスまでの待ち時間を特定して、挿入ワーカーのバッチ サイズと数を計算します。


#### <a name="pre-create-and-scale-your-collections"></a>コレクションを事前に作成してスケーリングする

mongoimport または mongorestore を使用して移行を行う前に、[Azure portal](https://portal.azure.com) または MongoDB のドライバーとツールから、すべてのコレクションを事前に作成します。 

[Azure portal](https://portal.azure.com) で、移行に合わせてコレクションのスループットを高くします。 スループットが高くなるほど、レート制限を回避し、移行に要する時間を短縮できます。 移行直後にスループットを低くすることでコストを削減できます。

コレクション レベルでのスループットのプロビジョニングだけでなく、コレクションのセットに対してデータベース レベルでスループットをプロビジョニングし、プロビジョニング済みのスループットを共有することもできます。 データベースとコレクションを事前に作成し、共有スループット データベースの各コレクションに対してシャード キーを定義する必要があります。

好みのツール、ドライバー、SDK を使用して、シャード化されたコレクションを作成できます。 この例では、Mongo シェルを使用してシャード コレクションを作成します。

```bash
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
そのコマンドでは、次の結果が返されます。

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

#### <a name="calculate-the-approximate-ru-charge-for-a-single-document-write"></a>1 回のドキュメント書き込みに対するおおよその RU 料金を計算する

MongoDB シェルから、Cosmos DB の MongoDB 用 API を使用するように構成された Cosmos アカウントに接続します。 手順については、「[Azure Cosmos DB への MongoDB アプリケーションの接続](connect-mongodb-account.md)」をご覧ください。

次に、サンプル ドキュメントの 1 つを使用して、サンプルの挿入コマンドを実行します。
   
```bash
db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
```
        
コマンド `db.runCommand({getLastRequestStatistics: 1})`を実行します。

次の出力のような応答を受け取ります。
     
```bash
globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "insert",
    "RequestCharge": 10,
    "RequestDurationInMilliSeconds": NumberLong(50)
}
```
        
要求の使用量を書き留めます。
    
#### <a name="determine-the-latency-from-your-machine-to-cosmos-db"></a>自分のマシンから Cosmos DB までの待ち時間を決定する
    
MongoDB シェルで `setVerboseShell(true)` コマンドを使用して、詳細ログ記録を有効にします。
    
`db.coll.find().limit(1)` コマンドを使用して、データベースに対して基本的なクエリを実行します。

次の出力のような応答を受け取ります。

```bash
Fetched 1 record(s) in 100(ms)
```
        
移行を行う前に、挿入したドキュメントを削除して、ドキュメントが重複しないようにします。 `db.coll.remove({})` コマンドでドキュメントを削除できます。

#### <a name="calculate-the-approximate-values-for-the-batchsize-and-numinsertionworkers-properties"></a>batchSize プロパティと numInsertionWorkers プロパティの概算値を計算する

**batchSize** プロパティについては、プロビジョニング済み合計スループット (RU/秒) を、1 回のドキュメント書き込みで消費される RU で割ります (「自分のマシンから Cosmos DB までの待ち時間を決定する」セクションを参照)。 計算された値が 24 以下の場合は、その値をプロパティの値として使用します。 計算された値が 24 を超える場合は、プロパティの値を 24 に設定します。
    
**numInsertionWorkers** プロパティの値については、次の計算式を使用します。

`numInsertionWorkers = (Provisioned RUs throughput * Latency in seconds) / (batchSize * Consumed RUs for a single write)`

**numInsertionWorkers** プロパティの値の計算には、次の値を使用できます。

| プロパティ | 値 |
|--------|-----|
| **batchSize** | 24 |
| プロビジョニング済み RU 数 | 10,000 |
| Latency | 0.100 秒 |
| 消費された RU 数 | 10 RU |
| **numInsertionWorkers** | (10,000 RU x 0.100 秒) / (24 x 10 RU) = **4.1666** |

**monogoimport** 移行コマンドを実行します。 コマンドのパラメーターについては、この記事の後の方で説明します。

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
```

**monogorestore** コマンドを使用することもできます。 すべてのコレクションのスループットが、前の計算で使用された RU の数値以上に設定されていることを確認します。
   
```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
```

### <a name="complete-the-prerequisites"></a>前提条件を満たす

移行を計画した後、次の手順を実行します。 

* **サンプル データを取得する**:移行を始める前に、サンプル データがあることを確認します。 

* **スループットを高くする**:データの移行にかかる時間は、個別のコレクションまたはデータベースに対してプロビジョニングしたスループットの量に依存します。 大規模なデータ移行では、スループットが上がっていることを確認します。 移行が完了したら、コストを節約するためにスループットを下げます。 

* **SSL を有効にする**:Cosmos DB には、厳密なセキュリティ要件と基準があります。 Cosmos アカウントを操作するときは、SSL が有効になっていることを確認してください。 この記事の手順には、mongoimport および mongorestore コマンドで SSL を有効にする方法が含まれています。

* **Cosmos DB リソースを作成する**:移行を始める前に、Azure portal ですべてのコレクションを事前に作成します。 データベース レベルでスループットがプロビジョニングされた Cosmos アカウントに移行する場合は、コレクションを作成するときにパーティション キーを指定します。

* **接続文字列を取得する**:[Azure portal](https://portal.azure.com) で、左側の **[Azure Cosmos DB]** エントリを選択します。 **[サブスクリプション]** で、自分のアカウント名を選択します。 **[接続文字列]** で **[接続文字列]** を選択します。 ポータルの右側に、アカウントに接続するために必要な情報が表示されます。

    ![接続文字列情報](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="use-mongoimport"></a>mongoimport を使用する

Cosmos アカウントにデータをインポートするには、次のテンプレートを使用します。

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

\<your_hostname>、\<your_username>、\<your_password> の各パラメーターを、自分のアカウントに固有の値に置き換えます。 次の例では、\<your_database> の値として **sampleDB** を使用し、\<your_collection> の値として **sampleColl** を使用します。

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="use-mongorestore"></a>mongorestore を使用する

Cosmos DB の MongoDB 用 API で構成されている Cosmos アカウントにデータを復元するには、次のテンプレートを使用してインポートを実行します。

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

\<your_hostname>、\<your_username>、\<your_password> の各パラメーターを、自分のアカウントに固有の値に置き換えます。 次の例では、\<path_to_backup> の値として **./dumps/dump-2016-12-07** を使用します。

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --db mydatabase --collection mycollection --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソースが必要なくなったら、リソース グループ、Cosmos アカウント、およびすべての関連リソースを削除できます。 リソース グループを削除するには次のようにします。

1. Cosmos アカウントを作成したリソース グループに移動します。
1. **[リソース グループの削除]** を選択します。
1. 削除するリソース グループの名前を確認し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

引き続き次のチュートリアルで、Cosmos DB の MongoDB 用 API を使用してデータのクエリを実行する方法を学習してください。 

> [!div class="nextstepaction"]
> [MongoDB データにクエリを実行する方法](../cosmos-db/tutorial-query-mongodb.md)
