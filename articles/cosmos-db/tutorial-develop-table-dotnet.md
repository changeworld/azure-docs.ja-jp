---
title: 'Azure Cosmos DB: .NET での Table API を使用した開発 | Microsoft Docs'
description: .NET を使用した Azure Cosmos DB の Table API による開発方法について
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: e6511b9511d2598b58fd3afee34803ceb09ac5ce
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38582854"
---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Azure Cosmos DB: .NET での Table API を使用した開発

Azure Cosmos DB は、Microsoft のグローバルに配布されるマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバル配布と水平方向のスケール機能を活用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成および照会できます。

このチュートリアルに含まれるタスクは次のとおりです。 

> [!div class="checklist"] 
> * Azure Cosmos DB アカウントを作成する 
> * app.config ファイルで機能を有効にする 
> * [Table API](table-introduction.md) を使用してテーブルを作成する
> * エンティティをテーブルに追加する 
> * エンティティのバッチを挿入する 
> * 単一のエンティティを取得する 
> * 自動セカンダリ インデックスを使用してエンティティのクエリを行う 
> * エンティティを置換する 
> * エンティティを削除する 
> * テーブルを削除する
 
## <a name="tables-in-azure-cosmos-db"></a>Azure Cosmos DB のテーブル 

Azure Cosmos DB では、スキーマレス設計のキー/値ストアを必要とするアプリケーションのために [Table API](table-introduction.md) が提供されます。 Azure Cosmos DB Table API と [Azure Table Storage](../storage/common/storage-introduction.md) はどちらも、同じ SDK と REST API をサポートするようになりました。 Azure Cosmos DB を使用して、高スループット要件のテーブルを作成できます。

このチュートリアルは、Azure Table Storage SDK の知識があり、Azure Cosmos DB を使用して提供される Premium 機能を使用する予定がある開発者が対象です。 「[.NET を使用して Azure Table Storage を使用する](table-storage-how-to-use-dotnet.md)」に基づいて、セカンダリ インデックス、プロビジョニング済みスループット、マルチホームなどの追加機能を活用する方法を説明します。 このチュートリアルでは、Azure Portal を使用して Azure Cosmos DB アカウントを作成し、Table API アプリケーションを構築およびデプロイする方法を説明します。 テーブルの作成と削除や、テーブル データの挿入、更新、削除、クエリを実行する .NET の例についても説明します。 

現在 Azure Table Storage を使っている場合、Azure Cosmos DB Table API では次のメリットが得られます。

- マルチホーム機能と[自動または手動のフェールオーバー](regional-failover.md)を備えた、ターンキー [グローバル分散](distribute-data-globally.md)
- すべてのプロパティ ("セカンダリ インデックス") に対するスキーマ非依存自動インデックス作成と高速クエリのサポート 
- 任意の数のリージョンにわたる[ストレージとスループットの独立した拡張](partition-data.md)のサポート
- [テーブルごとの専用スループット](request-units.md)のサポート (1 秒あたりの要求数を数百件から数百万件まで拡張できる)
- [調整可能な 5 つの一貫性レベル](consistency-levels.md)のサポート。アプリケーションのニーズに基づいて、可用性、待機時間、一貫性のトレードオフを行います。
- 1 リージョンで 99.99% の可用性 (可用性を上げるためにリージョンを追加することもでき、一般的な可用性に関して[業界をリードする包括的な SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) が提供される)
- 既存の Azure Storage .NET SDK の利用、アプリケーションのコード変更不要

このチュートリアルでは、.NET SDK を使う Azure Cosmos DB Table API について説明します。 NuGet から [Azure Cosmos DB Table API .NET SDK](https://aka.ms/tableapinuget) をダウンロードすることができます。

Azure Table Storage の複雑なタスクについては、次を参照してください。

* [Azure Cosmos DB Table API の概要](table-introduction.md)
* 利用可能な API の詳細については、Table service のリファレンス ドキュメント ([Azure Cosmos DB Table API .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet))

### <a name="about-this-tutorial"></a>このチュートリアルについて
このチュートリアルは、Azure Table Storage SDK の知識があり、Azure Cosmos DB を使用して提供される Premium 機能を使用する予定がある開発者が対象です。 「[.NET を使用して Azure Table Storage を使用する](table-storage-how-to-use-dotnet.md)」に基づいて、セカンダリ インデックス、プロビジョニング済みスループット、マルチホームなどの追加機能を活用する方法を説明します。 Azure Portal を使用して Azure Cosmos DB アカウントを作成し、Table アプリケーションを構築およびデプロイする方法も説明します。 テーブルの作成と削除や、テーブル データの挿入、更新、削除、クエリを実行する .NET の例についても説明します。 

まだ Visual Studio 2017 をインストールしていない場合は、**無料**の [Visual Studio 2017 Community エディション](https://www.visualstudio.com/downloads/)をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>データベース アカウントの作成

まず最初に、Azure Portal で Azure Cosmos DB アカウントを作成します。  
 
> [!IMPORTANT]  
> 新しいテーブル API アカウントを作成して一般公開のテーブル API SDK を操作する必要があります。 プレビュー期間中に作成されたテーブル API アカウントは、一般公開の SDK ではサポートされません。 
>

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

github で Table アプリの複製を作成し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. git bash などの git ターミナル ウィンドウを開き、`cd` コマンドを使用して、サンプル アプリをインストールするフォルダーに変更します。 

    ```bash
    cd "C:\git-samples"
    ```

2. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```

3. Visual Studio でソリューション ファイルを開きます。 

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。 これでアプリが、ホストされているデータベースと通信できます。 

1. [Azure Portal](http://portal.azure.com/) で **[接続文字列]** をクリックします。 

    画面の右側にある [コピー] ボタンを使ってプライマリ接続文字列をコピーします。

    ![[接続文字列] ウィンドウで [接続文字列] を確認してコピーする](./media/create-table-dotnet/connection-string.png)

2. Visual Studio で app.config ファイルを開きます。 

3. このチュートリアルでは Storage Emulator を使用しないため、8 行目の StorageConnectionString のコメントを解除し、7 行目の StorageConnectionString をコメント アウトします。 7 行目と 8 行目は次のようになります。

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. ポータルのプライマリ接続文字列を、8 行目の StorageConnectionString に貼り付けます。 引用符の内側に文字列を貼り付けます。
   
    > [!IMPORTANT]
    > エンドポイントで documents.azure.com を使用している場合は、プレビュー アカウントを持っていることになるため、[新しいテーブル API](#create-a-database-account) アカウントを作成して、一般公開のテーブル API SDK を操作する必要があります。 
    >

    8 行目は次のようになります。

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=txZACN9f...==;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

5. app.config ファイルを保存します。

これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 

## <a name="azure-cosmos-db-capabilities"></a>Azure Cosmos DB の機能
Azure Cosmos DB では、Azure Table Storage API に含まれないいくつもの機能がサポートされます。 

特定の機能には、接続ポリシーと一貫性レベルを指定できる CreateCloudTableClient の新しいオーバーロードを使ってアクセスします。

| テーブル接続の設定 | 説明 |
| --- | --- |
| 接続モード  | Azure Cosmos DB では 2 つの接続モードがサポートされます。 `Gateway` モードでは、要求は常に Azure Cosmos DB ゲートウェイに対して行われ、そこから対応するデータ パーティションに転送されます。 `Direct` 接続モードでは、クライアントがテーブルとパーティションのマッピングをフェッチし、要求がデータ パーティションに直接行われます。 既定値の `Direct` をお勧めします。  |
| 接続プロトコル | Azure Cosmos DB では 2 つの接続プロトコル、`Https` と `Tcp` がサポートされます。 軽量であるため、既定の `Tcp` をお勧めします。 |
| 優先される場所 | 読み取りの優先場所 (マルチホーム) をコンマで区切って指定します。 各 Azure Cosmos DB アカウントは 1 から 30 以上のリージョンに関連付けることができます。 各クライアント インスタンスは、低待機時間で読み取りを行えるように、優先順位を付けてこれらのリージョンのサブセットを指定できます。 リージョンは[表示名](https://msdn.microsoft.com/library/azure/gg441293.aspx) (`West US` など) を使用して指定する必要があります。 [マルチホーム API](tutorial-global-distribution-table.md)に関する記事も参照してください。 |
| 整合性レベル | 適切に定義された 5 個の一貫性レベル、`Strong`、`Session`、`Bounded-Staleness`、`ConsistentPrefix`、`Eventual` から選択して、待機時間、一貫性、可用性のトレードオフを行うことができます。 既定値は `Session`です。 一貫性レベルの選択肢によって、複数リージョンのセットアップでパフォーマンスに大きな違いが生じます。 詳しくは、[一貫性レベル](consistency-levels.md)に関する記事をご覧ください。 |

他の機能は、次の `appSettings` 構成値で有効にすることができます。

| キー | 説明 |
| --- | --- |
| TableQueryMaxItemCount | 1 回のラウンド トリップでテーブル クエリごとに返される最大項目数を構成します。 既定値の `-1` では、Azure Cosmos DB が実行時に値を動的に決定できます。 |
| TableQueryEnableScan | クエリがすべてのフィルターでインデックスを使用できない場合、スキャンを使用して実行します。 既定値は `false`です。|
| TableQueryMaxDegreeOfParallelism | クロス パーティション クエリを実行するための並列処理の次数。 `0` は、プリフェッチなしの直列処理、`1` はプリフェッチありの直列処理です。これより値が大きくなるにつれて、並列処理の速度が向上します。 既定値の `-1` では、Azure Cosmos DB が実行時に値を動的に決定できます。 |

既定値を変更するには、Visual Studio のソリューション エクスプローラーから `app.config` ファイルを開きます。 次に示す `<appSettings>` 要素の内容を追加します。 `account-name` をストレージ アカウントの名前に置き換え、`account-key` をアカウントのアクセス キーに置き換えます。 

```xml
<configuration>
    ...
    <appSettings>
      <!-- Client options -->
      <add key="CosmosDBStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://account-name.table.cosmosdb.azure.com" />
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />

      <!-- Table query options -->
      <add key="TableQueryMaxItemCount" value="-1"/>
      <add key="TableQueryEnableScan" value="false"/>
      <add key="TableQueryMaxDegreeOfParallelism" value="-1"/>
      <add key="TableQueryContinuationTokenLimitInKb" value="16"/>
            
    </appSettings>
</configuration>
```

アプリで何が行われているかを簡単に確認してみましょう。 `Program.cs` ファイルを開くと、これらのコード行によって Table リソースが作成されることがわかります。 

## <a name="create-the-table-client"></a>テーブル クライアントを作成する
テーブル アカウントに接続するための `CloudTableClient` を初期化してします。

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```
このクライアントは、`TableConnectionMode``TableConnectionProtocol``TableConsistencyLevel`、`TablePreferredLocations` の各構成値を使用して初期化されます (アプリの設定に指定されている場合)。

## <a name="create-a-table"></a>テーブルを作成する
次に、`CloudTable` を使用してテーブルを作成します。 Azure Cosmos DB のテーブルは、ストレージとスループットそれぞれを個別に拡張できます。パーティション化はサービスによって自動的に処理されます。 Azure Cosmos DB では、固定サイズのテーブルと無制限のテーブルの両方がサポートされます。 詳しくは、[Azure Cosmos DB でのパーティション分割](partition-data.md)に関する記事をご覧ください。 

```csharp
CloudTable table = tableClient.GetTableReference("people");
400
table.CreateIfNotExists(throughput: 800);
```

テーブルの作成方法に大きな違いがあります。 Azure Cosmos DB ではスループットが予約されます。これはトランザクションの使用量に基づく Azure Storage のモデルとは異なります。 スループットは専用として予約されるため、要求レートがプロビジョニングされたスループット以下である場合にもスロットルされることはありません。

CreateIfNotExists のパラメーターとして組み込むことで、既定のスループットを構成できます。

1 KB のエンティティの読み取りは 1 RU に正規化され、他の操作は、CPU、メモリ、IOPS の消費量に基づく固定 RU 値に正規化されます。 詳しくは、「[Azure Cosmos DB の要求ユニット](request-units.md)」および特に[キー値ストア](key-value-store-cost.md)に関するページをご覧ください。

次には、Azure Table Storage SDK を使用する単純な読み取りと書き込み (CRUD) の操作について説明します。 このチュートリアルでは、Azure Cosmos DB によって数ミリ秒という低待機時間と高速クエリが実現することが予測されます。

## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する
Azure Table Storage のエンティティは `TableEntity` クラスから拡張されます。`PartitionKey` プロパティと `RowKey` プロパティを持つ必要があります。 ここでカスタマー エンティティの定義例を示します。

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

次のスニペットは、Azure Storage SDK を使用してエンティティを挿入する方法を示します。 Azure Cosmos DB は、世界中であらゆるスケールで低待機時間を保証するように設計されています。

Azure Cosmos DB アカウントと同じリージョンで実行するアプリケーションの場合、書き込みが完了する時間は p99 では 15 ミリ秒未満、p50 では 6 ミリ秒以内です。 この待機時間から、書き込みがクライアントで確認されるのは、同期レプリケートと永続コミットが行われ、すべてのコンテンツのインデックスが作成された後であることがわかります。


```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>エンティティのバッチを挿入する
Azure Table Storage ではバッチ操作 API がサポートされます。これを使用すると、更新、削除、挿入をバッチ操作で組み合わせることができます。

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
table.ExecuteBatch(batchOperation);
```
## <a name="retrieve-a-single-entity"></a>単一のエンティティを取得する
Azure Cosmos DB での取得 (GET) は、同じ Azure リージョンの場合、p99 で 10 ミリ秒未満、p50 では 1 ミリ秒以内で完了します。 低待機時間読み取りのためアカウントに任意の数のリージョンを追加することができ、`TablePreferredLocations` を設定して、ローカル リージョンから読み取るようにアプリケーションをデプロイできます ("マルチホーム")。 

単一のエンティティを取得するには、次のスニペットを使用します。

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
> [!TIP]
> マルチホーム API について詳しくは、[複数リージョンの開発](tutorial-global-distribution-table.md)に関する記事をご覧ください。
>

## <a name="query-entities-using-automatic-secondary-indexes"></a>自動セカンダリ インデックスを使用してエンティティのクエリを行う
テーブルのクエリには `TableQuery` クラスを使用します。 Azure Cosmos DB には、書き込みに対して最適化されたデータベース エンジンがあり、テーブル内のすべての列のインデックスを自動的に作成します。 Azure Cosmos DB でのインデックス作成はスキーマに依存しません。 したがって、複数の行でスキーマが異なる場合、またはスキーマが時間経過に伴って変化する場合でも、自動的にインデックスが作成されます。 Azure Cosmos DB では自動セカンダリ インデックスがサポートされるため、すべてのプロパティに対するクエリはこのインデックスを使用することができ、効率よく処理されます。

```csharp
CloudTable table = tableClient.GetTableReference("people");

// Filter against a property that's not partition key or row key
TableQuery<CustomerEntity> emailQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal, "Ben@contoso.com"));

foreach (CustomerEntity entity in table.ExecuteQuery(emailQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

Azure Cosmos DB は、Table API について Azure Table Storage と同じクエリの機能をサポートします。 また、並べ替え、集計、地理空間クエリ、階層、さまざまな組み込み関数もサポートしています。 将来のサービス更新プログラムで Table API に追加機能が提供される予定です。 このような機能の概要については、[Azure Cosmos DB のクエリ](sql-api-sql-query.md)に関する記事をご覧ください。 

## <a name="replace-an-entity"></a>エンティティを置換する
エンティティを更新するには、そのエンティティを Table service から取得し、エンティティ オブジェクトを変更して、変更を Table service に戻して保存します。 次のコードは、既存のユーザーの電話番号を変更します。 

```csharp
TableOperation updateOperation = TableOperation.Replace(updateEntity);
table.Execute(updateOperation);
```
同様に、`InsertOrMerge` または `Merge` の操作を実行できます。  

## <a name="delete-an-entity"></a>エンティティを削除する
エンティティは、取得後に簡単に削除できます。エンティティの更新のときと同じパターンを使用します。 次のコードは、ユーザー エンティティを取得して削除します。

```csharp
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
table.Execute(deleteOperation);
```

## <a name="delete-a-table"></a>テーブルを削除する
最後に、次のコード例は、ストレージ アカウントからテーブルを削除します。 Azure Cosmos DB では、テーブルを削除してからすぐに再作成できます。

```csharp
CloudTable table = tableClient.GetTableReference("people");
table.DeleteIfExists();
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Cosmos DB で Table API の使用を開始する方法について説明し、次のタスクを実行しました。 

> [!div class="checklist"] 
> * Azure Cosmos DB アカウントの作成 
> * app.config ファイルでの機能の有効化 
> * テーブルの作成 
> * エンティティのテーブルへの追加する 
> * エンティティのバッチの挿入 
> * 単一のエンティティの取得 
> * 自動セカンダリ インデックスを使用したエンティティのクエリ 
> * エンティティの置換 
> * エンティティの削除 
> * テーブルの削除  

次のチュートリアルに進み、テーブル データのクエリについて学ぶことができます。 

> [!div class="nextstepaction"]
> [Table API を使用したクエリ](tutorial-query-table.md)
