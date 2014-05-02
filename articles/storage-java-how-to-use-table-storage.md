<properties linkid="dev-java-how-to-use-table-storage" urlDisplayName="テーブル サービス" pageTitle="テーブル ストレージの使用方法 (Java) | Microsoft Azure" metaKeywords="Azure テーブル ストレージ サービス, Azure テーブル サービス Java, テーブル ストレージ Java" description="Azure でのテーブル ストレージ サービスの使用方法について説明します。コード サンプルは Java で記述されています。" metaCanonical="" services="storage" documentationCenter="Java" title="Java からテーブル ストレージ サービスを使用する方法" authors="" solutions="" manager="" editor="" />


# Java からテーブル ストレージ サービスを使用する方法

このガイドでは、Azure テーブル ストレージ サービスを使用して
一般的なシナリオを実行する方法について説明します。コード サンプルは Java で
記述されています。紹介するシナリオは、**テーブルの作成と削除、
テーブルのエンティティの挿入とクエリ実行**などです。テーブルの詳細については、
「[次のステップ](#NextSteps)」を参照してください。

## <a name="Contents"> </a>目次

* [テーブル ストレージとは](#what-is)
* [概念](#Concepts)
* [Azure のストレージ アカウントの作成](#CreateAccount)
* [Java アプリケーションの作成](#CreateApplication)
* [テーブル ストレージにアクセスするようにアプリケーションを構成する](#ConfigureStorage)
* [Azure のストレージ接続文字列の設定](#ConnectionString)
* [方法: テーブルを作成する](#CreateTable)
* [方法: エンティティをテーブルに追加する](#AddEntity)
* [方法: エンティティのバッチを挿入する](#InsertBatch)
* [方法: パーティション内のすべてのエンティティを取得する](#RetrieveEntities)
* [方法: パーティション内の一定範囲のエンティティを取得する](#RetrieveRange)
* [方法: 単一のエンティティを取得する](#RetriveSingle)
* [方法: エンティティを変更する](#ModifyEntity)
* [方法: エンティティ プロパティのサブセットを照会する](#QueryProperties)
* [方法: エンティティを挿入または置換する](#InsertOrReplace)
* [方法: エンティティを削除する](#DeleteEntity)
* [方法: テーブルを削除する](#DeleteTable)
* [次のステップ](#NextSteps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

##<a name="CreateAccount"></a>Azure のストレージ アカウントの作成

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="CreateApplication"></a>Java アプリケーションの作成

このガイドで使用するストレージ機能は、Java アプリケーション内でローカルで
実行することも、Azure の Web ロールまたは worker ロールで動作するコード内で
実行することもできます。前提条件として、Java Development Kit (JDK) を
ダウンロードしてインストールしているとします。また、[Azure SDK for Java のページ]の指示に従って、Azure Libraries for Java と
Azure SDK をインストールし、Azure サブスクリプションに
Azure のストレージ アカウントを作成しているとします。

アプリケーションの作成には、メモ帳などの任意の開発ツールを使用できます。要件は、Java プロジェクトをコンパイルできること、
および Azure Libraries for Java を参照できることのみです。

## <a name="ConfigureStorage"> </a>テーブル ストレージにアクセスするようにアプリケーションを構成する

Azure ストレージ API を使用してテーブルにアクセスする Java ファイルの
先頭には、次の import ステートメントを追加します。

    // Include the following imports to use table APIs
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.table.client.*;
    import com.microsoft.windowsazure.services.table.client.TableQuery.*;

## <a name="ConnectionString"> </a>Azure のストレージ接続文字列の設定

Azure ストレージ クライアントでは、ストレージ接続文字列を使用して、
データ管理サービスにアクセスするためのエンドポイントおよび資格情報を保存します。クライアント アプリケーションの実行時、ストレージ接続文字列を次の
形式で指定する必要があります。*AccountName* と *AccountKey*
の値には、管理ポータルに表示されるストレージ アカウントの名前と
プライマリ アクセス キーを使用します。この例では、
接続文字列を保持する静的フィールドを宣言する方法を示しています。

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Azure 上のロール内で実行されるアプリケーションでは、
この文字列はサービス構成ファイルである
ServiceConfiguration.cscfg に格納でき、
**RoleEnvironment.getConfigurationSettings** メソッドの呼び出しを使用してアクセスできます。次の例では、
接続文字列をサービス構成ファイル内の **Setting** 要素 
(*StorageConnectionString*) から取得します。

    // Retrieve storage account from connection-string
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

次のサンプル コードでは、これら 2 つの定義のいずれかを使用してストレージ
接続文字列を取得するとします。

## <a name="CreateTable"> </a>方法: テーブルを作成する

**CloudTableClient** オブジェクトを使用すると、テーブルとエンティティの参照オブジェクトを取得できます。次のコードは、**CloudTableClient** オブジェクトを作成し、これを使用して新しいテーブルを作成します。このガイドのすべてのコードでは、
Azure アプリケーションのサービス構成に格納されている
ストレージ接続文字列を使用します。そのほかにも 
**CloudStorageAccount** オブジェクトを作成する方法はあります。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create the table if it doesn't exist.
    String tableName = "people";
    tableClient.createTableIfNotExists(tableName);

## <a name="AddEntity"> </a>方法: エンティティをテーブルに追加する

エンティティは、
**TableEntity** を実装するカスタム クラスを使用して Java オブジェクトにマップされます。コードがシンプルになるように、**TableServiceEntity** クラスでは
**TableEntity** を実装し、リフレクションを使用することで、プロパティを、
それらのプロパティの名前が付いた getter および setter メソッドにマップしています。エンティティをテーブルに追加するには、最初に、
エンティティのプロパティを定義するクラスを作成します。次のコードは、ユーザーの名を行キーとして、姓を
パーティション キーとして使用するエンティティ クラスを
定義します。エンティティのパーティション キーと行キーの組み合わせで、
テーブル内のエンティティを一意に識別します。同じパーティション キーを
持つエンティティは、異なるパーティション キーを持つ
エンティティよりも迅速に照会できます。

    public class CustomerEntity extends TableServiceEntity {
        public CustomerEntity(String lastName, String firstName) {
            this.partitionKey = lastName;
            this.rowKey = firstName;
        }

        public CustomerEntity() { }

        String email;
        String phoneNumber;
        
        public String getEmail() {
            return this.email;
        }
        
        public void setEmail(String email) {
            this.email = email;
        }
        
        public String getPhoneNumber() {
            return this.phoneNumber;
        }
        
        public void setPhoneNumber(String phoneNumber) {
            this.phoneNumber = phoneNumber;
        }
    }

エンティティに関連するテーブル操作には **TableOperation** オブジェクトが必要です。
このオブジェクトを使用して、エンティティに対して実行する操作を定義します。
定義した操作は、**CloudTableClient** オブジェクトを使用して実行できます。次のコードでは、
顧客データの格納用に **CustomerEntity** クラスの新しい
インスタンスを作成しています。次に、このコードでは 
**TableOperation.insert** を呼び出し、テーブルへのエンティティの挿入用に
**TableOperation** オブジェクトを作成して、そのオブジェクトを新しい
**CustomerEntity** に関連付けています。最後に、このコードでは **execute** の 
**CloudTableClient** メソッドを呼び出し、"people" テーブルと新しい 
**TableOperation** を指定しています。それにより、新しい顧客エンティティを
"people" テーブルに挿入する要求がストレージ サービスに送信されるようになっています。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer1 = TableOperation.insert(customer1);

    // Submit the operation to the table service.
    tableClient.execute("people", insertCustomer1);

## <a name="InsertBatch"> </a>方法: エンティティのバッチを挿入する

1 回の書き込み操作でエンティティのバッチをテーブル サービスに
挿入できます。次のコードでは、**TableBatchOperation** オブジェクトを作成し、
3 つの挿入操作を追加しています。追加する各挿入操作では、
新しいエンティティ オブジェクトを作成してその値を設定してから、
**TableBatchOperation** オブジェクトの **insert** メソッドを呼び出して、
エンティティを新しい挿入操作に関連付けています。次に、このコードでは 
**CloudTableClient** の **execute** を呼び出して、"people" テーブルと
**TableBatchOperation** オブジェクトを指定しています。
それにより、テーブル操作のバッチがストレージ サービスに 1 つの要求で送信されるようになっています。バッチ操作に関しては、次の事項にも留意してください。

1. 最大 100 個の挿入、削除、マージ、置換、挿入/マージ、
    挿入/置換の操作を任意の組み合わせで 1 つのバッチで
    実行できます。
2. 取得をバッチ操作として実行することもできますが、
    バッチ内の唯一の操作であることが必要です。
3. 1 つのバッチ操作に含まれるすべてのエンティティの
    パーティション キーが同じである必要があります。
4. バッチ操作のデータ ペイロードは 4 MB に制限されています。

<!-- -->

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Define a batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity to add to the table.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insert(customer);

    // Create another customer entity to add to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insert(customer2);

    // Create a third customer entity to add to the table.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insert(customer3);

    // Execute the batch of operations on the "people" table.
    tableClient.execute("people", batchOperation);

## <a name="RetrieveEntities"> </a>方法: パーティション内のすべてのエンティティを取得する

テーブルに対してパーティション内のエンティティを照会する場合は、
**TableQuery** を使用できます。**TableQuery.from** を呼び出して、特定のテーブルに
対するクエリを作成し、指定した型の結果が返るようにします。次のコードは、
"Smith" がパーティション キーであるエンティティに対してフィルターを指定します。
**TableQuery.generateFilterCondition** はクエリのフィルターを
作成するためのヘルパー メソッドです。**TableQuery.from** メソッドによって返された参照の
**where** を呼び出して、フィルターをクエリに適用します。クエリが 
**CloudTableClient** オブジェクトの **execute** の呼び出しを
使用して実行されると、指定した **CustomerEntity** 型の結果が
**Iterator** に格納されて返されます。その後、返された **Iterator** を for each ループ内で
使用して、結果を処理できます。このコードは、クエリ結果の各エンティティの
フィールドをコンソールに出力します。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        TableConstants.PARTITION_KEY, 
        QueryComparisons.EQUAL,
        "Smith");

    // Specify a partition query, using "Smith" as the partition key filter.
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from("people", CustomerEntity.class)
        .where(partitionFilter);

    // Loop through the results, displaying information about the entity.
    for (CustomerEntity entity : tableClient.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() + " " + entity.getRowKey() + 
            "\t" + entity.getEmail() + "\t" + entity.getPhoneNumber());
    }

## <a name="RetrieveRange"> </a>方法: パーティション内の一定範囲のエンティティを取得する

パーティション内の一部のエンティティのみ照会する場合は、
フィルター内で比較演算子を使用して範囲を指定できます。次のコードは、
2 つのフィルターを組み合わせて、行キー (名) がアルファベットの "E" までの
文字で始まる、"Smith" というパーティション内のすべてのエントリを取得します。
その後で、クエリ結果が出力されます。このガイドのバッチ挿入に関するセクションで
テーブルに追加したエンティティを使用すると、この場合は 2 つのエンティティ
(Ben Smith と Denise Smith) だけが返されます。Jeff Smith は返されません。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        TableConstants.PARTITION_KEY, 
        QueryComparisons.EQUAL,
        "Smith");

    // Create a filter condition where the row key is less than the letter "E".
    String rowFilter = TableQuery.generateFilterCondition(
        TableConstants.ROW_KEY, 
        QueryComparisons.LESS_THAN,
        "E");

    // Combine the two conditions into a filter expression.
    String combinedFilter = TableQuery.combineFilters(partitionFilter, 
            Operators.AND, rowFilter);

    // Specify a range query, using "Smith" as the partition key,
    // with the row key being up to the letter "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from("people", CustomerEntity.class)
        .where(combinedFilter);

    // Loop through the results, displaying information about the entity
    for (CustomerEntity entity : tableClient.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() + " " + entity.getRowKey() + 
            "\t" + entity.getEmail() + "\t" + entity.getPhoneNumber());
    }

## <a name="RetriveSingle"> </a>方法: 単一のエンティティを取得する

単一の特定のエンティティを取得するクエリを記述することができます。次のコードでは、
**TableOperation.retrieve** を呼び出し、パーティション キーと行キーの
パラメーターを使用して、顧客 'Jeff Smith' を指定しています。同じ操作は
**TableQuery** を作成してフィルターを使用することでも可能です。この
取得操作が実行されると、1 つのコレクションではなく、1 つのエンティティ
のみ返されます。**getResultAsType** メソッドは、結果を設定先の型である
**CustomerEntity** オブジェクト型にキャストします。この型が
クエリに指定した型と互換性がない場合は、
例外がスローされます。パーティション キーおよび行キーが正確に一致する
エンティティがない場合は、null 値が返されます。クエリでパーティション キーと行キーの両方を指定することが、
テーブル サービスから単一のエンティティを取得するための最速の方法です。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    TableOperation retrieveSmithJeff = 
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        tableClient.execute("people", retrieveSmithJeff).getResultAsType();

## <a name="ModifyEntity"> </a>方法: エンティティを変更する

エンティティを変更するには、そのエンティティをテーブル サービスから取得し、
エンティティ オブジェクトに変更を加えて、その変更を置換またはマージ操作で
テーブル サービスに戻して保存します。次のコードは、既存のユーザーの
電話番号を変更します。挿入の場合のように **TableOperation.insert** を呼び出すのではなく、
このコードは **TableOperation.replace** を呼び出します。この
アプリケーションがエンティティを取得した後で別のアプリケーションが
変更を加えていない限り、**CloudTableClient.execute** メソッドは
テーブル サービスを呼び出し、このエンティティは置き換えられます。別のアプリケーションが変更を加えた場合は、例外がスローされるので、
このエンティティを取得して変更し、もう一度保存する必要があります。
このオプティミスティック同時実行制御の再試行パターンは、
分散したストレージ システムでは一般的です。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = 
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        tableClient.execute("people", retrieveSmithJeff).getResultAsType();
        
    // Specify a new phone number.
    specificEntity.setPhoneNumber("425-555-0105");

    // Create an operation to replace the entity.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Submit the operation to the table service.
    tableClient.execute("people", replaceEntity);

## <a name="QueryProperties"> </a>方法: エンティティ プロパティのサブセットを照会する

テーブルに対するクエリでは、ごくわずかのプロパティだけをエンティティから取得できます。
プロジェクションと呼ばれるこの方法では、帯域幅の使用が削減され、クエリの
パフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。次のコードのクエリは、
**select** メソッドを使用して、テーブル内のエンティティの
電子メール アドレスだけを返します。結果は **EntityResolver**
によって **String** コレクションへのプロジェクション 
(サーバーから返されるエンティティの型変換) が行われます。プロジェクションの
詳細については、この[ブログの記事][]を参照してください。プロジェクションは
ローカル ストレージ エミュレーターではサポートされていません。したがって、
このコードはテーブル サービスのアカウントを使用している場合にのみ機能します。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Define a projection query that retrieves only the Email property
    TableQuery<CustomerEntity> projectionQuery = 
        TableQuery.from("people", CustomerEntity.class)
        .select(new String[] {"Email"});

    // Define a Entity resolver to project the entity to the Email value.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp,
                HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Loop through the results, displaying the Email values.
    for (String projectedString : 
            tableClient.execute(projectionQuery, emailResolver)) {
        System.out.println(projectedString);
    }

## <a name="InsertOrReplace"> </a>方法: エンティティを挿入または置換する

エントリをテーブルに追加するときは、多くの場合、そのエントリがテーブル内に
既に存在しているかどうかを把握していません。エンティティの挿入または置換操作では、エンティティが存在しない場合に
そのエンティティを挿入し、エンティティが存在する場合はその既存のエンティティを
置き換えるという操作を 1 つの要求で処理することができます。これまでの例に対して、
次のコードは "Walter Harp" のエンティティを挿入または置換します。新しい
エンティティを作成すると、このコードは
**TableOperation.insertOrReplace** メソッドを呼び出します。その後、
**CloudTableClient** の **execute** を呼び出し、テーブルとそのテーブルの
挿入および置換操作をパラメーターとして渡します。エンティティの一部のみ更新するには、
**TableOperation.insertOrMerge** メソッドを代わりに使用できます。
挿入または置換はローカル ストレージ エミュレーターでは
サポートされていません。したがって、このコードはテーブル サービスの
アカウントを使用している場合にのみ機能します。挿入または置換、および挿入またはマージの詳細については、
この[ブログの記事][]を参照してください。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create a new customer entity.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Submit the operation to the table service.
    tableClient.execute("people", insertCustomer5);

## <a name="DeleteEntity"> </a>方法: エンティティを削除する

エンティティは、取得後に簡単に削除できます。エンティティを
取得したら、削除するエンティティを指定して **TableOperation.delete**
を呼び出します。その後、**CloudTableClient** の **execute** を呼び出します。次のコードは、
ユーザー エンティティを取得して削除します。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    CustomerEntity entitySmithJeff =
            tableClient.execute("people", retrieveSmithJeff).getResultAsType();

    // Create an operation to delete the entity.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Submit the delete operation to the table service.
    tableClient.execute("people", deleteSmithJeff);

## <a name="DeleteTable"> </a>方法: テーブルを削除する

最後に、次のコードは、ストレージ アカウントからテーブルを削除します。削除された
テーブルは、削除後の一定期間 (通常は 40 秒未満) は
再作成できなくなります。

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Delete the table and all its data if it exists.
    tableClient.deleteTableIfExists("people");

## <a name="NextSteps"> </a>次のステップ

これで、テーブル ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、次のリンク先を参照してください。

-   MSDN リファレンス: [Azure のデータの格納と
    アクセス]
-  [Azure のストレージ チーム ブログ][]


[REST API の使用]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh264518.aspx
[ブログ記事]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
[Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
[Azure ストレージ チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/
[Azure SDK for Java]: http://www.windowsazure.com/ja-jp/develop/java/
[Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx

