---
title: Java 用 Azure Tables クライアント ライブラリを使用する
description: Java 用 Azure Tables クライアント ライブラリを使用して、構造化データをクラウドに格納します。
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: Java
ms.topic: sample
ms.date: 12/10/2020
author: ThomasWeiss
ms.author: thweiss
ms.custom: devx-track-java
ms.openlocfilehash: 7b390f4be49c8785c7ee8a2dca588ee3cfb7d571
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719582"
---
# <a name="how-to-use-the-azure-tables-client-library-for-java"></a>Java 用 Azure Tables クライアント ライブラリを使用する方法

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

この記事では、テーブルの作成、データの格納、データに対する CRUD 操作の実行を行う方法について説明します。 サンプルは Java で記述され、[Java 用 Azure Tables クライアント ライブラリ][Azure Tables client library for Java]を利用しています。 テーブルの **作成**、**一覧表示**、および **削除** と、テーブル内のエンティティの **挿入**、**照会**、**変更**、および **削除** の各シナリオについて説明します。 テーブルの詳細については、「 [次のステップ](#next-steps) 」のセクションを参照してください。

> [!IMPORTANT]
> Table Storage と Cosmos DB テーブルをサポートする Azure Tables クライアント ライブラリの最新バージョンは、[12+][Azure Tables client library for Java] です。

## <a name="create-an-azure-service-account"></a>Azure サービス アカウントを作成する

[!INCLUDE [cosmos-db-create-azure-service-account](../includes/cosmos-db-create-azure-service-account.md)]

**Azure Storage アカウントを作成する**

[!INCLUDE [cosmos-db-create-storage-account](../includes/cosmos-db-create-storage-account.md)]

**Azure Cosmos DB アカウントの作成**

[!INCLUDE [cosmos-db-create-tableapi-account](../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-java-application"></a>Java アプリケーションの作成

この記事のサンプルを使用するには、以下の手順を実行します。
1. [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-support-on-azure#supported-java-versions-and-update-schedule) をインストールします。
2. Azure サブスクリプションで Azure ストレージ アカウントまたは Azure Cosmos DB アカウントを作成します。
3. 開発システムが、GitHub の [Java 用 Azure Tables クライアント ライブラリ][Azure Tables client library for Java] リポジトリに示されている最小要件と依存関係を満たしていることを確認します。
4. 指示に従って、そのリポジトリからシステムに Azure Storage Libraries for Java をダウンロードしてインストールします。
5. この記事の例を使用する Java アプリを作成します。

## <a name="configure-your-app-to-access-table-storage"></a>Table Storage にアクセスするようにアプリを構成する

*pom.xml* ファイルの `dependencies` セクションに、次のエントリを追加します。

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-data-tables</artifactId>
  <version>12.1.1</version>
</dependency>
```

次に、Azure Tables API を使用してテーブルにアクセスする Java ファイルの先頭に、`import` ステートメントを追加します。

```java
// Include the following imports to use table APIs
import com.azure.data.tables.TableClient;
import com.azure.data.tables.TableClientBuilder;
import com.azure.data.tables.TableServiceClient;
import com.azure.data.tables.TableServiceClientBuilder;
import com.azure.data.tables.models.ListEntitiesOptions;
import com.azure.data.tables.models.TableEntity;
import com.azure.data.tables.models.TableEntityUpdateMode;
import com.azure.data.tables.models.TableTransactionAction;
import com.azure.data.tables.models.TableTransactionActionType;
```

## <a name="add-your-connection-string"></a>接続文字列を追加する

Azure ストレージ アカウントまたは Azure Cosmos DB Table API アカウントのいずれかに接続できます。 使用しているアカウントの種類に基づいて接続文字列を取得します。

### <a name="add-an-azure-storage-connection-string"></a>Azure Storage 接続文字列を追加する

Azure Tables クライアントでは、ストレージ接続文字列を使用して、データ管理サービスにアクセスするためのエンドポイントおよび資格情報を保存することができます。 クライアント アプリを実行する際は、ストレージ接続文字列を次の形式で指定する必要があります。**AccountName** と **AccountKey** の値には、[Azure portal](https://portal.azure.com) に表示されるストレージ アカウントの名前とプライマリ アクセス キーを使用します。

この例では、接続文字列を保持する静的フィールドを宣言する方法を示しています。

```java
// Define the connection-string with your values.
public final String connectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=core.windows.net;
```

### <a name="add-an-azure-cosmos-db-table-api-connection-string"></a>Azure Cosmos DB Table API 接続文字列の追加

Azure Cosmos DB アカウントは、接続文字列を使用して、テーブルのエンドポイントと資格情報を格納します。 クライアント アプリケーションを実行する際は、Azure Cosmos DB 接続文字列を次の形式で指定する必要があります。**AccountName** と **AccountKey** の値には、[Azure portal](https://portal.azure.com) に表示される Azure Cosmos DB アカウントの名前とプライマリ アクセス キーを使用します。

この例では、Azure Cosmos DB 接続文字列を保持する静的フィールドを宣言する方法が示されています。

```java
public final String connectionString =
    "DefaultEndpointsProtocol=https;" + 
    "AccountName=your_cosmosdb_account;" + 
    "AccountKey=your_account_key;" + 
    "TableEndpoint=https://your_endpoint;" ;
```

Azure のロール内で実行されているアプリでは、この文字列をサービス構成ファイル *ServiceConfiguration.cscfg* に格納できます。 ここには、`System.getenv` メソッドを呼び出すことでアクセスできます。 次の例では、サービス構成ファイル内の **ConnectionString** という名前の *Setting* 要素から接続文字列を取得しています。

```java
// Retrieve storage account from connection-string.
String connectionString = System.getenv("ConnectionString");
```

接続文字列は、プロジェクトの config.properties ファイルに格納することもできます。

```java
connectionString = DefaultEndpointsProtocol=https;AccountName=your_account;AccountKey=your_account_key;TableEndpoint=https://your_table_endpoint/
```

次のサンプルでは、これらのメソッドのいずれかを使用してストレージ接続文字列を取得していることを前提としています。

## <a name="create-a-table"></a>テーブルを作成する

`TableServiceClient` オブジェクトを使用すると、Tables サービスを操作して、テーブルを作成、一覧表示、および削除することができます。 次のコードは、`TableServiceClient` オブジェクトを作成し、それを使用して新しい `TableClient` オブジェクトを作成します。これは `Employees` という名前のテーブルを表します。

```java
try
{
    final String tableName = "Employees";

    // Create a TableServiceClient with a connection string.
    TableServiceClient tableServiceClient = new TableServiceClientBuilder()
        .connectionString(connectionString)
        .buildClient();

    // Create the table if it not exists.
    TableClient tableClient = tableServiceClient.createTableIfNotExists(tableName);

}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-tables"></a>テーブルを一覧表示する

テーブルの一覧を取得するには、`TableServiceClient.listTables` メソッドを呼び出して、テーブル名の反復可能な一覧を取得します。

```java
try
{
    // Create a TableServiceClient with a connection string.
    TableServiceClient tableServiceClient = new TableServiceClientBuilder()
        .connectionString(connectionString)
        .buildClient();

    // Loop through a collection of table names.
    tableServiceClient.listTables().forEach(tableItem -> 
        System.out.printf(tableItem.getName())
    );
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する

次のコードでは、顧客データの格納用に `TableEntity` クラスの新しいインスタンスを作成しています。 このコードでは、`TableClient` オブジェクトの `upsertEntity` メソッドを呼び出しています。 このメソッドは、新しい顧客エンティティを `Employees` テーブルに挿入するか、すでにエンティティが存在する場合はそれを置き換えます。

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
     TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a new employee TableEntity.
    String partitionKey = "Sales";
    String rowKey = "0001";
    Map<String, Object> personalInfo= new HashMap<>();
    personalInfo.put("FirstName", "Walter");
    personalInfo.put("LastName", "Harp");
    personalInfo.put("Email", "Walter@contoso.com");
    personalInfo.put("PhoneNumber", "425-555-0101");
    TableEntity employee = new TableEntity(partitionKey, rowKey).setProperties(personalInfo);
        
    // Upsert the entity into the table
    tableClient.upsertEntity(employee);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-a-batch-of-entities"></a>エンティティのバッチを挿入する

1 回の書き込み操作でエンティティのバッチをテーブル サービスに挿入できます。 次のコードでは、`List<TableTransactionAction>` オブジェクトを作成し、それに 3 つの upsert 操作を追加しています。 各操作は、新しい `TableEntity` オブジェクトを作成し、そのプロパティを設定した後、`TableClient` オブジェクトの `submitTransaction` メソッドを呼び出すことで追加されます。

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    String partitionKey = "Sales";
    List<TableTransactionAction> tableTransactionActions = new ArrayList<>();
    
    Map<String, Object> personalInfo1 = new HashMap<>();
    personalInfo1.put("FirstName", "Jeff");
    personalInfo1.put("LastName", "Smith");
    personalInfo1.put("Email", "Jeff@contoso.com");
    personalInfo1.put("PhoneNumber", "425-555-0104");
    
    // Create an entity to add to the table.
    tableTransactionActions.add(new TableTransactionAction(
        TableTransactionActionType.UPSERT_MERGE,
        new TableEntity(partitionKey, "0001")
            .setProperties(personalInfo1)
    ));
    
    Map<String, Object> personalInfo2 = new HashMap<>();
    personalInfo2.put("FirstName", "Ben");
    personalInfo2.put("LastName", "Johnson");
    personalInfo2.put("Email", "Ben@contoso.com");
    personalInfo2.put("PhoneNumber", "425-555-0102");
    
    // Create another entity to add to the table.
    tableTransactionActions.add(new TableTransactionAction(
        TableTransactionActionType.UPSERT_MERGE,
        new TableEntity(partitionKey, "0002")
            .setProperties(personalInfo2)
    ));
    
    Map<String, Object> personalInfo3 = new HashMap<>();
    personalInfo3.put("FirstName", "Denise");
    personalInfo3.put("LastName", "Rivers");
    personalInfo3.put("Email", "Denise@contoso.com");
    personalInfo3.put("PhoneNumber", "425-555-0103");
    
    // Create a third entity to add to the table.
    tableTransactionActions.add(new TableTransactionAction(
        TableTransactionActionType.UPSERT_MERGE,
        new TableEntity(partitionKey, "0003")
            .setProperties(personalInfo3)
    ));

    // Submit transaction on the "Employees" table.
    tableClient.submitTransaction(tableTransactionActions);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

バッチ操作に関しては、次の事項にも留意してください。

* 最大 100 個の挿入、削除、マージ、置換、挿入/マージ、挿入/置換の操作を任意の組み合わせで 1 つのバッチで実行できます。
* 取得をバッチ操作として実行することもできますが、バッチ内の唯一の操作であることが必要です。
* 1 つのバッチ操作に含まれるすべてのエンティティのパーティション キーが同じである必要があります。
* バッチ操作のデータ ペイロードは 4 MB に制限されています。

## <a name="retrieve-all-entities-in-a-partition"></a>パーティション内のすべてのエンティティを取得する

テーブルに対してパーティション内のエンティティのクエリを実行する場合は、`ListEntitiesOptions` を使用できます。 `ListEntitiesOptions.setFilter` を呼び出して、指定された結果の型を返す、特定のテーブルに対するクエリを作成します。 次のコードは、`Sales` がパーティション キーであるエンティティに対してフィルターを指定します。 このクエリが `TableClient` オブジェクトの `listEntities` の呼び出しで実行されると、`TableEntity` の `Iterator` が返されます。 その後、返された `Iterator` を "ForEach" ループ内で使用して、結果を処理できます。 このコードは、クエリ結果の各エントリのフィールドをコンソールに出力します。

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a filter condition where the partition key is "Sales".
    ListEntitiesOptions options = new ListEntitiesOptions().setFilter(PARTITION_KEY + " eq 'Sales'");

    // Loop through the results, displaying information about the entities.
    tableClient.listEntities(options, null, null).forEach(tableEntity -> {
        System.out.println(tableEntity.getPartitionKey() +
            " " + tableEntity.getRowKey() +
            "\t" + tableEntity.getProperty("FirstName") +
            "\t" + tableEntity.getProperty("LastName") +
            "\t" + tableEntity.getProperty("Email") +
            "\t" + tableEntity.getProperty("PhoneNumber"));
    });
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>パーティション内の一定範囲のエンティティを取得する

パーティション内の一部のエンティティに対してのみクエリを実行する場合は、フィルター内で比較演算子を使用して範囲を指定します。 次のコードは、2 つのフィルターを組み合わせて、'0001' と '0.0004' の間の行キーを持つパーティション `Sales` 内のすべてのエンティティを取得します。 その後で、クエリ結果が出力されます。 このガイドのバッチ挿入に関するセクションでテーブルに追加したエンティティを使用すると、この場合は 2 つのエンティティ (Ben と Denise) だけが返されます。

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String tableName = "Employees";

    // Create a TableServiceClient with a connection string.
    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a filter condition where the partition key is "Sales".
    ListEntitiesOptions options = new ListEntitiesOptions().setFilter(PARTITION_KEY + " eq 'Sales' AND " + ROW_KEY + " lt '0004' AND ROW_KEY + " gt '0001'");
    
    // Loop through the results, displaying information about the entities.
    tableClient.listEntities(options, null, null).forEach(tableEntity -> {
        System.out.println(tableEntity.getPartitionKey() +
            " " + tableEntity.getRowKey() +
            "\t" + tableEntity.getProperty("FirstName") +
            "\t" + tableEntity.getProperty("LastName") +
            "\t" + tableEntity.getProperty("Email") +
            "\t" + tableEntity.getProperty("PhoneNumber"));
    });
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-single-entity"></a>単一のエンティティを取得する

単一の特定のエンティティを取得するクエリを記述することができます。 次のコードでは、`TableClient.getEntity` を呼び出し、パーティション キーおよび行キーのパラメーターを使用して、従業員 "Jeff Smith" のエンティティを取得します。同じ操作は、`ListEntitiesOptions` を作成してフィルターを使用する方法でも可能です。 この取得操作が実行されると、1 つのコレクションではなく、1 つのエンティティのみ返されます。 パーティション キーおよび行キーが正確に一致するエンティティがない場合は、`null` 値が返されます。 クエリでパーティション キーと行キーの両方を指定することが、Table service から単一のエンティティを取得するための最速の方法です。

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Get the specific entity.
    TableEntity specificEntity = tableClient.getEntity("Sales", "0001");

    // Output the entity.
    if (specificEntity != null)
    {
        System.out.println(specificEntity.getPartitionKey() +
            " " + specificEntity.getRowKey() +
            "\t" + specificEntity.getProperty("FirstName") +
            "\t" + specificEntity.getProperty("LastName"));
            "\t" + specificEntity.getProperty("Email") +
            "\t" + specificEntity.getProperty("PhoneNumber"));
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="modify-an-entity"></a>エンティティを変更する

エンティティを変更するには、そのエンティティをテーブル サービスから取得し、エンティティ オブジェクトに変更を加えて、その変更を置換またはマージ操作でテーブル サービスに戻して保存します。 次のコードは、既存のユーザーの電話番号を変更します。 このコードでは、挿入するときのように `tableClient.upsertEntity` を呼び出すのではなく、`tableClient.updateEntity` を呼び出しています。

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Get the specific entity.
    TableEntity specificEntity = tableClient.getEntity("Sales", "0001");

    // Specify a new phone number
    specificEntity.getProperties().put("PhoneNumber", "425-555-0105");

    // Update the specific entity
    tableClient.updateEntity(specificEntity, TableEntityUpdateMode.REPLACE);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="query-a-subset-of-entity-properties"></a>エンティティ プロパティのサブセットを照会する

テーブルに対するクエリでは、ごくわずかのプロパティだけをエンティティから取得できます。 プロジェクションと呼ばれるこの方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。 次のコードのクエリは、`ListEntitiesOptions.setSelect` メソッドを使って、テーブル内のエンティティの電子メール アドレスだけを返します。

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a filter condition that retrieves only the Email property.
    List<String> attributesToRetrieve = new ArrayList<>();
    attributesToRetrieve.add("Email");
    
    ListEntitiesOptions options = new ListEntitiesOptions().setSelect(attributesToRetrieve);

    // Loop through the results, displaying the Email values.
    tableClient.listEntities(options, null, null).forEach(tableEntity -> {
        System.out.println(tableEntity.getProperty("Email"));
    });
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-or-replace-an-entity"></a>エンティティの挿入または置換を行う

エントリをテーブルに追加するときは、多くの場合、そのエントリがテーブル内に既に存在しているかどうかを把握していません。 挿入または置換操作では、1 つの要求を行うことができます。 この要求は、エンティティが存在しない場合はエンティティを挿入し、存在する場合は置換します。 これまでの例に対して、次のコードは "Walter Harp" のエンティティを挿入または置換します。 新しいエンティティを作成すると、このコードによって `TableClient.upsertEntity` メソッドが呼び出されます。

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a new table entity.
    Map<String, Object> properties = new HashMap<>();
    properties.put("FirstName", "Walter");
    properties.put("LastName", "Harp");
    properties.put("Email", "Walter@contoso.com");
    properties.put("PhoneNumber", "425-555-0101");
        
    TableEntity newEmployee = new TableEntity("Sales", "0004")
        .setProperties(properties);
        
    // Add the new customer to the Employees table.
    tableClient.upsertEntity(newEmployee);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-an-entity"></a>エンティティを削除する

エンティティを削除するには、`TableClient.deleteEntity` を使用してパーティション キーと行キーを指定します。

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    Delete the entity for partition key 'Sales' and row key '0001' from the table.
    tableClient.deleteEntity("Sales", "0001");
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-table"></a>テーブルを削除する

最後に、次のコードを実行すると、アカウントからテーブルが削除されます。 削除した後、約 40 秒間はテーブルを再作成できません。

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Delete the table and all its data.
    tableClient.deleteTable();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>次のステップ

* [Java での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-java-getting-started)
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。
* [Java 用 Azure Tables クライアント ライブラリ][Azure Tables client library for Java]
* [Azure Tables クライアント ライブラリのレファレンス][Azure Tables client library reference documentation]
* [Azure Tables REST API][Azure Tables REST API]
* [Azure Tables チームのブログ][Azure Tables Team Blog]

詳細については、「[Azure for Java developers (Java 開発者向けの Azure)](/java/azure)」を参照してください。

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Tables client library for Java]: https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/tables/azure-data-tables
[Azure Tables client library reference documentation]: https://azure.github.io/azure-sdk-for-java/tables.html
[Azure Tables REST API]: ../../storage/tables/table-storage-overview.md
[Azure Tables Team Blog]: https://blogs.msdn.microsoft.com/windowsazurestorage/