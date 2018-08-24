---
title: Azure Cosmos DB への Cassandra データのインポート | Microsoft Docs
description: CQL Copy コマンドを使用して Azure Cosmos DB に Cassandra データをコピーする方法を説明します。
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: b53328875f2242faba369dea0df655bc78117009
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "41918785"
---
# <a name="azure-cosmos-db-import-cassandra-data"></a>Azure Cosmos DB: Cassandra データをインポートする

このチュートリアルでは、Cassandra Query Language (CQL) Copy コマンドを使用して Azure Cosmos DB に Cassandra データをインポートする手順について説明します。 

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * 接続文字列の取得
> * cqlsh COPY コマンドを使用したデータのインポート
> * Spark コネクタを使用したインポート 

# <a name="prerequisites"></a>前提条件

* [Apache Cassandra](http://cassandra.apache.org/download/) をインストールし、*cqlsh*　が存在することを明確に確認します。
* スループットを上げます。データの移行にかかる時間は、テーブルに対してプロビジョニングしたスループットの量に依存します。 大規模なデータ移行では、スループットが上がっていることを確認します。 移行が完了したら、コストを節約するためにスループットを下げます。 [Azure Portal](https://portal.azure.com) でスループットを上げることの詳細については、「[Azure Cosmos DB コンテナーのスループットの設定](set-throughput.md)」を参照してください。
* SSL を有効にする: Azure Cosmos DB には、厳密なセキュリティ要件と基準が存在します。 アカウントを操作するときは、SSL が有効になっていることを確認してください。 SSH で CQL を使用する場合は、SSL の情報を指定するオプションがあります。 

## <a name="find-your-connection-string"></a>接続文字列を見つける

1. [Azure Portal](https://portal.azure.com) で、左側の **[Azure Cosmos DB]** をクリックします。

2. **[サブスクリプション]** ウィンドウで、自分のアカウント名を選択します。

3. **[接続文字列]** をクリックします。 右側のウィンドウに、自分のアカウントに正常に接続するために必要なすべての情報が表示されます。

    ![接続文字列のページ](./media/cassandra-import-data/keys.png)

## <a name="use-cqlsh-copy"></a>cqlsh COPY を使用する

Cassandra API で使用するために Cassandra データを Azure Cosmos DB にインポートするには、次のガイダンスに従います。

1. ポータルからの接続情報を使用して cqhsh にログインします。
2. [CQL COPY コマンド](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh)を使用してローカル データを Apache Cassandra API エンドポイントにコピーします。 待機時間の問題を最小限にするため、ソースとターゲットが同じデータ センター内にあるようにします。

### <a name="guide-for-moving-data-with-cqlsh"></a>cqlsh でデータを移動するためのガイド

1. テーブルを事前に作成し、拡大縮小します。
    * 既定では、Azure Cosmos DB は毎秒 1,000 の要求ユニット (RU/秒) で新しい Cassandra API テーブルをプロビジョニングします (CQL ベースの作成は 400 RU/秒でプロビジョニングされます)。 cqlsh を使用して移行を開始する前に、[Azure Portal](https://portal.azure.com) または cqlsh からすべてのテーブルを事前作成します。 

    * 移行の間は、[Azure Portal](https://portal.azure.com) から、テーブルのスループットを既定のスループット (400 または 1000 RU/秒) から 10,000 RU/秒に上げます。 スループットが高くなるほど、レート制限を回避し、移行に要する時間を短縮できます。 時間単位で課金される Azure Cosmos DB では、移行直後にスループットを低くすることでコストを削減できます。

2. 1 つの操作の RU 負担を決定します。 これは、Azure Cosmos DB の任意の Cassandra API SDK を使用して行えます。 この例では、.NET バージョンでの RU 負担取得方法を示します。 

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
            {
                byte[] valueInBytes = customPayload[key];
                string value = Encoding.UTF8.GetString(valueInBytes);
                Console.WriteLine($“CustomPayload:  {key}: {value}”);
            }
 
    ``` 

3. マシンから Azure Cosmos DB サービスまでの待ち時間を決定します。 Azure データ センター内の場合、待機時間は 1 桁の短い方のミリ秒数にする必要があります。 Azure データ センター外の場合は、psping または azurespeed.com を使用して、マシンの場所からのおおよその待機時間を取得できます。   

4. 良好なパフォーマンスを提供するパラメーター (NUMPROCESS、INGESTRATE、MAXBATCHSIZE、または MINBATCHSIZE) の適切な値を計算します。 

5. 最後の移行コマンドを実行します。 このコマンドの実行は、接続文字列情報を使用して cqlsh を開始したことを前提としています。

   ```
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="use-spark-to-import-data"></a>Spark を使用してデータをインポートする

Azure 仮想マシン内の既存のクラスターに置かれたデータの場合、Spark を使用してデータをインポートすることも、選択可能なオプションです。 これには、Spark を 1 回または定期的な取り込みの仲介者として設定する必要があります。 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下のタスクを完了する方法について学習しました。

> [!div class="checklist"]
> * 接続文字列の取得
> * cql Copy コマンドを使用したデータのインポート
> * Spark コネクタを使用したインポート 

これで、「概念」セクションに進み、Azure Cosmos DB の詳細について学習できるようになりました。 

> [!div class="nextstepaction"]
>[Azure Cosmos DB の調整可能なデータの一貫性レベル](../cosmos-db/consistency-levels.md)
