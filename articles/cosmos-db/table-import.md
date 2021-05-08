---
title: Azure Cosmos DB の Table API アカウントに既存のデータを移行する
description: Azure Cosmos DB の Azure Table API アカウントに、オンプレミスまたはクラウドのデータを移行またはインポートする方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 77f4c928db695bd4193ad46c93e0efbd16decf29
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443340"
---
# <a name="migrate-your-data-to-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API アカウントにデータを移行する
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

このチュートリアルでは、Azure Cosmos DB [Table API](table-introduction.md) で使用するデータをインポートする手順を示します。 Azure Table Storage に格納データがある場合、データ移行ツールまたは AzCopy を使用してデータを Azure Cosmos DB Table API にインポートできます。 

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * データ移行ツールを使用したデータのインポート
> * AzCopy を使用したデータのインポート

## <a name="prerequisites"></a>前提条件

* **スループットを向上させる:** データの移行にかかる時間は、個別のコンテナーまたは一連のコンテナーに対して設定したスループットの量に依存します。 大規模なデータ移行を行うときは、必ずスループットを上げておいてください。 移行が完了したら、コストを節約するためにスループットを下げます。

* **Azure Cosmos DB リソースを作成する:** データの移行を開始する前に、Azure portal ですべてのテーブルを作成します。 データベースレベルのスループットがある Azure Cosmos DB アカウントに移行しようとしている場合は、Azure Cosmos DB テーブルの作成時に必ずパーティション キーを提供するようにしてください。

## <a name="data-migration-tool"></a>データ移行ツール

Azure Cosmos DB のコマンド ライン データ移行ツール (dt.exe) を使用して、既存の Azure Table Storage データを Table API アカウントにインポートできます。 

テーブル データを移行するには:

1. 移行ツールを [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool) からダウンロードします。
2. 実際のシナリオに合ったコマンド ライン引数を使用して `dt.exe` を実行します。 `dt.exe` は次の形式のコマンドを受け取ります。

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

このコマンドでサポートされているオプションは次のとおりです。

* **/ErrorLog:** 省略可能。 データ転送エラーをリダイレクトする CSV ファイルの名前。
* **/OverwriteErrorLog:** 省略可能。 エラー ログ ファイルを上書きします。
* **/ProgressUpdateInterval:** 省略可能。既定値は `00:00:01` です。 画面上でデータ転送の進行状況を更新する間隔を指定します。
* **/ErrorDetails:** 省略可能。既定値は `None` です。 特定のエラー (`None`、`Critical`、または `All`) に関して、詳細なエラー情報を表示するよう指定します。
* **/EnableCosmosTableLog:** 省略可能。 ログを Azure Cosmos DB テーブル アカウントに送信します。 設定した場合、送信先アカウントの接続文字列が既定値となります (`/CosmosTableLogConnectionString` を一緒に指定した場合を除く)。 ツールのインスタンスが複数同時に実行されている場合に役立ちます。
* **/CosmosTableLogConnectionString:** 省略可能。 リモートの Azure Cosmos DB テーブル アカウントにログを送信するための接続文字列。

### <a name="command-line-source-settings"></a>コマンドラインのソース設定

移行のソースとして Azure Table Storage を定義する場合は、次のソース オプションを使用します。

* **/s:AzureTable:** Table Storage からデータを読み取ります。
* **/s.ConnectionString:** テーブル エンドポイントの接続文字列。 これは Azure portal から取得できます。
* **/s.LocationMode:** 省略可能。既定値は `PrimaryOnly` です。 Table Storage に接続するときに使用する配置モード (`PrimaryOnly`、`PrimaryThenSecondary`、`SecondaryOnly`、`SecondaryThenPrimary`) を指定します。
* **/s.Table:** Azure テーブルの名前。
* **/s.InternalFields:** `RowKey` と `PartitionKey` がインポートに必要となるため、テーブルの移行では `All` に設定します。
* **/s.Filter:** 省略可能。 適用するフィルター文字列です。
* **/s.Projection:** 省略可能。 選択する列の一覧です。

Table Storage からインポートするときにソース接続文字列を取得するには、Azure portal を開きます。 **[ストレージ アカウント]**  >  **[アカウント]**  >  **[アクセス キー]** を選択し、 **[接続文字列]** の値をコピーします。

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="[ストレージ アカウント] > [アカウント] > [アクセス キー] オプションを示すスクリーンショット。コピー アイコンが強調表示されています。":::

### <a name="command-line-target-settings"></a>コマンドラインのターゲット設定

移行のターゲットとして Azure Cosmos DB Table API を定義する場合は、次のターゲット オプションを使用します。

* **/t:TableAPIBulk:** データを Azure Cosmos DB Table API に一括でアップロードします。
* **/t.ConnectionString:** テーブル エンドポイントの接続文字列。
* **/t.TableName:** 書き込み先となるテーブルの名前を指定します。
* **/t.Overwrite:** 省略可能。既定値は `false` です。 既存の値を上書きするかどうかを指定します。
* **/t.MaxInputBufferSize:** 省略可能。既定値は `1GB` です。 バッファーに入力するバイト数の概算値を指定します。この値を超えると、シンクにデータがフラッシュされます。
* **/t.Throughput:** 省略可能。指定しなかった場合、サービスの既定値が使用されます。 テーブルに対して構成するスループットを指定します。
* **/t.MaxBatchSize:** 省略可能。既定値は `2MB` です。 バッチ サイズをバイト単位で指定します。

### <a name="sample-command-source-is-table-storage"></a>サンプル コマンド: ソースが Table Storage

Table Storage から Table API にインポートする方法を示すコマンドライン サンプルは以下のとおりです。

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>AzCopy を使用してデータを移行する

AzCopy コマンドライン ユーティリティを使用して、Table Storage から Azure Cosmos DB Table API にデータを移行することもできます。 AzCopy を使用するには、まず「[Table Storage からデータをエクスポートする](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage)」の説明に従って、データをエクスポートします。 [Azure Cosmos DB Table API](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage) に関するページの説明に従って、データを Azure Cosmos DB にインポートします。

Azure Cosmos DB にインポートする際は、次のサンプルを参照してください。 `/Dest` 値では `core` ではなく `cosmosdb` を使用することに注意してください。

インポート コマンド例:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB Table API を使用してデータにクエリを実行する方法を確認します。 

> [!div class="nextstepaction"]
>[データにクエリを実行する方法](../cosmos-db/tutorial-query-table.md)




