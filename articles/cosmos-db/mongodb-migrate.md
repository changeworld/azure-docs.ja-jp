---
title: Azure Cosmos DB の MongoDB 用 API で mongoimport と mongorestore を使用する | Microsoft Docs
description: mongoimport と mongorestore を使用して、MongoDB 用 API アカウントにデータをインポートする方法について説明します
keywords: mongoimport, mongorestore
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 50bb34d86780dec003c63b5ff0a3884049dd47c1
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871015"
---
# <a name="tutorial-migrate-your-data-to-azure-cosmos-db-mongodb-api-account"></a>チュートリアル: Azure Cosmos DB MongoDB API アカウントにデータを移行する

このチュートリアルでは、MongoDB に格納されているデータを Azure Cosmos DB MongoDB API アカウントに移行する方法を説明します。 MongoDB からデータをインポートしており、Azure Cosmos DB SQL API でそれを使用する予定がある場合は、[データ移行ツール](import-data.md)を使用してデータをインポートする必要があります。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * 移行の計画
> * 移行の前提条件
> * mongoimport を使用してデータを移行する
> * mongorestore を使用してデータを移行する

データを MongoDB API アカウントに移行する前に、サンプルの MongoDB データがあることを確認してください。 サンプルの MongoDB データベースがない場合は、[MongoDB コミュニティ サーバー](https://www.mongodb.com/download-center)をダウンロードしてインストールし、サンプル データベースを作成して、mongoimport.exe または mongorestore.exe を使用してサンプル データをアップロードできます。 

## <a name="plan-for-migration"></a>移行の計画

1. コレクションを事前に作成し、拡大縮小します。
        
   * 既定では、Azure Cosmos DB は、毎秒 1,000 要求ユニット (RU/秒) で新しい MongoDB コレクションをプロビジョニングします。 mongoimport または mongorestore を使用して移行を開始する前に、[Azure portal](https://portal.azure.com) または MongoDB ドライバーとツールから、すべてのコレクションを事前に作成します。 データのサイズが 10 GB を超える場合は、適切なシャード キーを使用して[パーティション分割コレクション](partition-data.md)を作成します。 MongoDB は、エンティティ データをコレクションに格納することを推奨しています。 同等のサイズのエンティティは同じ場所に配置して、Azure Cosmos データベース レベルでスループットをプロビジョニングすることができます。

   * [Azure portal](https://portal.azure.com) で、コレクションのスループットを 1,000 RU/秒 (単一パーティションのコレクションの場合) または 2,500 RU/秒 (移行の期間のみシャード化されたコレクションの場合) から引き上げます。 スループットが高くなるほど、レート制限を回避し、移行に要する時間を短縮できます。 移行直後にスループットを低くすることでコストを削減できます。

   * コレクション レベルでの RU/秒 のプロビジョニングに加えて、親データベース レベルの一連のコレクションの RU/秒 のプロビジョニングも行う場合があります。 これを行うには、データベースとコレクションの事前作成と、各コレクションのシャード キーの定義が必要です。

   * お気に入りのツールやドライバー、SDK を使用してシャード コレクションを作成できます。 この例では、Mongo シェルを使用してシャード コレクションを作成します。

        ```bash
        db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
        ```
    
        結果:

        ```JSON
        {
            "_t" : "ShardCollectionResponse",
            "ok" : 1,
            "collectionsharded" : "admin.people"
        }
        ```

1. 1 回のドキュメントの書き込みに対するおおよその RU 請求金額を計算します。

   a. MongoDB シェルを使用して Azure Cosmos DB MongoDB API アカウントに接続します。 手順については、「[Connect a MongoDB application to Azure Cosmos DB (Azure Cosmos DB への MongoDB アプリケーションの接続)](connect-mongodb-account.md)」を参照してください。
    
   b. MongoDB シェルからサンプル ドキュメントの 1 つを使用して、サンプルの挿入コマンドを実行します。
   
      ```bash
      db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
      ```
        
   c. ```db.runCommand({getLastRequestStatistics: 1})``` を実行すると、次のような応答が返されます。
     
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
        
    d. 要求の使用量を書き留めます。
    
1. マシンから Azure Cosmos DB クラウド サービスまでの待ち時間を決定します。
    
    a. MongoDB シェルで ```setVerboseShell(true)``` コマンドを使用して、詳細ログ記録を有効にします。
    
    b. ```db.coll.find().limit(1)``` を使用して、データベースに対して単純なクエリを実行します。 次のような応答が返されます。

       ```bash
       Fetched 1 record(s) in 100(ms)
       ```
        
1. 移行前に挿入したドキュメントを削除して、重複するドキュメントがないことを確認します。 ドキュメントを削除するには、```db.coll.remove({})``` コマンドを使用します。

1. *batchSize* と *numInsertionWorkers* の概算値を計算します。

    * *batchSize* については、プロビジョニングされた RU の総数を、手順 3. の 1 回のドキュメント書き込みで消費された RU 数で除算します。
    
    * 計算された *batchSize* 値が 24 以下の場合は、その数値を *batchSize* 値として使用します。
    
    * 計算された *batchSize* 値が 24 を超える場合は、*batchSize* 値を 24 に設定します。
    
    * *numInsertionWorkers* の場合は、*numInsertionWorkers = (プロビジョニングされたスループット * 秒単位の待機時間) / (バッチ サイズ * 1 回の書き込みのために消費された RU 数)* という数式を使用します。
        
    |プロパティ|値|
    |--------|-----|
    |batchSize| 24 |
    |プロビジョニングされた RU 数 | 10000 |
    |Latency | 0.100 秒 |
    |1 回のドキュメント書き込みで消費された RU | 10 RU |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10,000 RU x 0.1) / (24 x 10 RU) = 4.1666*

1. 移行コマンドを実行します。 データを移行するオプションについては、以降のセクションで説明します。

   ```bash
   mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```
   または mongorestore で実行します (すべてのコレクションで、スループットが前の計算で使用された RU の数値以上に設定されていることを確認します):
   
   ```bash
   mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
   ```

## <a name="prerequisites-for-migration"></a>移行の前提条件

* **スループットを上げる**: データの移行にかかる時間は、個別のコレクションまたは一連のコレクションに対して設定したスループットの量に依存します。 大規模なデータ移行では、スループットが上がっていることを確認します。 移行が完了したら、コストを節約するためにスループットを下げます。 [Azure Portal](https://portal.azure.com) でスループットを上げることの詳細については、[Azure Cosmos DB のパフォーマンス レベルと価格レベル](performance-levels.md)に関するページを参照してください。

* **SSL を有効にする:** Azure Cosmos DB には、厳密なセキュリティ要件と基準が存在します。 アカウントを操作するときは、SSL が有効になっていることを確認してください。 この記事で説明する手順の中に、mongoimport と mongorestore で SSL を有効にする方法が含まれています。

* **Azure Cosmos DB リソースを作成する:** データの移行を開始する前に、Azure portal のすべてのコレクションを事前に作成します。 データベース レベルのスループットがある Azure Cosmos DB アカウントに移行しようとしている場合は、Azure Cosmos DB コレクションの作成時に必ずパーティション キーを提供するようにしてください。

## <a name="get-your-connection-string"></a>接続文字列を取得する 

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**[Azure Cosmos DB]** エントリをクリックします。
1. **[サブスクリプション]** ウィンドウで、自分のアカウント名を選択します。
1. **[接続文字列]** ブレードで、**[接続文字列]** をクリックします。

   右側のウィンドウに、自分のアカウントに正常に接続するために必要なすべての情報が表示されます。

   ![[接続文字列] ブレード](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="migrate-data-by-using-mongoimport"></a>mongoimport を使用してデータを移行する

Azure Cosmos DB アカウントにデータをインポートするには、次のテンプレートを使用します。 *ホスト*、*ユーザー名*および*パスワード*には、自分のアカウントに固有の値を入力します。  

テンプレート:

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

例:  

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="migrate-data-by-using-mongorestore"></a>mongorestore を使用してデータを移行する

MongoDB 用 API アカウントにデータを復元するには、次のテンプレートを使用してインポートを実行します。 *ホスト*、*ユーザー名*および*パスワード*には、自分のアカウントに固有の値を入力します。

テンプレート:

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

例:

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="next-steps"></a>次の手順

次のチュートリアルに進み、Azure Cosmos DB を使用して MongoDB データにクエリを実行する方法を学習することができます。 

> [!div class="nextstepaction"]
>[MongoDB データにクエリを実行する方法](../cosmos-db/tutorial-query-mongodb.md)
