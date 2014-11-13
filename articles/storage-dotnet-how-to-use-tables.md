<properties urlDisplayName="Table Service" pageTitle=".NET から Table ストレージを使用する方法 | Microsoft Azure" metaKeywords="Get started Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage   Azure table .NET   Azure table storage .NET   Azure table C#   Azure table storage C#" description="Microsoft Azure Table ストレージを使用してテーブルを作成および削除する方法、さらにテーブル内のエンティティを挿入および照会する方法について説明します。" services="storage" documentationCenter=".NET" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Microsoft Azure Table ストレージを使用する方法" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# How to use Table Storage from .NET (.NET からテーブル ストレージを使用する方法)

このガイドでは、Azure テーブル ストレージ サービスを使用して一般的な
シナリオを実行する方法について説明します。例は C# のコードで記述され、
Azure .NET 用ストレージ クライアント ライブラリを利用しています。紹介するシナリオは、**テーブルの作成と
削除**、**テーブル エンティティの操作**などです。テーブルの
詳細については、「[次のステップ][次のステップ]」のセクションを参照してください。

> [WACOM.NOTE] このガイドは、Azure .NET 用ストレージ クライアント ライブラリ 2.x 以上を対象としています。推奨されるバージョンはストレージ クライアント ライブラリ 4.x です。これは、[NuGet][NuGet] から、または [Azure SDK for .NET][Azure SDK for .NET] の一部として提供されています。ストレージ クライアント ライブラリの入手方法の詳細については、「[方法: プログラムでテーブル ストレージにアクセスする][方法: プログラムでテーブル ストレージにアクセスする]」を参照してください。

## 目次

-   [テーブル サービスとは][テーブル サービスとは]
-   [概念][概念]
-   [Azure のストレージ アカウントの作成][Azure のストレージ アカウントの作成]
-   [ストレージ接続文字列の設定][ストレージ接続文字列の設定]
-   [方法: プログラムでテーブル ストレージにアクセスする][方法: プログラムでテーブル ストレージにアクセスする]
-   [方法: テーブルの作成][方法: テーブルの作成]
-   [方法: エンティティをテーブルに追加する][方法: エンティティをテーブルに追加する]
-   [方法: エンティティのバッチを挿入する][方法: エンティティのバッチを挿入する]
-   [方法: パーティション内のすべてのエンティティを取得する][方法: パーティション内のすべてのエンティティを取得する]
-   [方法: パーティション内の一定範囲のエンティティを取得する][方法: パーティション内の一定範囲のエンティティを取得する]
-   [方法: 単一のエンティティを取得する][方法: 単一のエンティティを取得する]
-   [方法: エンティティを置換する][方法: エンティティを置換する]
-   [方法: エンティティの挿入または置換を行う][方法: エンティティの挿入または置換を行う]
-   [方法: エンティティ プロパティのサブセットを照会する][方法: エンティティ プロパティのサブセットを照会する]
-   [方法: エンティティを削除する][方法: エンティティを削除する]
-   [方法: テーブルを削除する][方法: テーブルを削除する]
-   [次のステップ][次のステップ]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## 

## <a name="create-account"></a><span class="short-header">アカウントの作成</span>Azure の Storage アカウントの作成

</h2>
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## 

## <a name="setup-connection-string"></a><span class="short-header">接続文字列の設定</span>ストレージ接続文字列の設定

</h2>
[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## 

## <a name="configure-access"> </a><span class="short-header">プログラムでのアクセス</span>方法: プログラムでテーブル ストレージにアクセスする

</h2>
### アセンブリの取得

NuGet を使用して `Microsoft.WindowsAzure.Storage.dll` アセンブリを取得できます。**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。"WindowsAzure.Storage" をオンライン検索し、**[インストール]** をクリックして Azure Storage のパッケージと依存関係をインストールします。

`Microsoft.WindowsAzure.Storage.dll` は、[.NET デベロッパー センター][.NET デベロッパー センター]からダウンロードできる Azure SDK for .NET にも含まれています。このアセンブリは `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`lt;sdk-version\>\\ref\\</code> ディレクトリにインストールされます。

### 名前空間宣言

プログラムを使用して Azure ストレージにアクセスするすべての C#
ファイルの冒頭部分に、次の名前空間宣言を追加します。

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

必ず `Microsoft.WindowsAzure.Storage.dll` アセンブリを参照してください。

### 接続文字列の取得

**CloudStorageAccount** 型を使用してストレージ アカウント
情報を表すことができます。Azure プロジェクト
テンプレートを使用している場合や、
Microsoft.WindowsAzure.CloudConfigurationManager 名前空間
への参照がある場合は、**CloudConfigurationManager** 型
を使用して Azure サービス構成からストレージ接続文字列
とストレージ アカウント情報を取得できます。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

作成しているアプリケーションに Microsoft.WindowsAzure.CloudConfigurationManager への参照が含まれておらず、接続文字列が前に示したように `web.config` または `app.config` に格納されている場合は、**ConfigurationManager** を使用して接続文字列を取得できます。System.Configuration.dll への参照をプロジェクトに追加し、対応する名前空間宣言を追加する必要があります。

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### ODataLib 依存

.NET 用ストレージ クライアント ライブラリの ODataLib 依存は、WCF Data Services ではなく、NuGet から入手できる ODataLib (バージョン 5.0.2) パッケージで解決されます。ODataLib ライブラリは、直接ダウンロードすることも、NuGet を使用してコード プロジェクトで参照することもできます。具体的な ODataLib パッケージは、[OData][OData]、[Edm][Edm]、および [Spatial][Spatial] です。

## <a name="create-table"></a><span class="short-header">テーブルの作成</span>方法: テーブルの作成

**CloudTableClient** オブジェクトを使用すると、テーブルとエンティティの
参照オブジェクトを取得できます。次のコードは、**CloudTableClient** オブジェクトを作成し、
これを使用して新しいテーブルを作成します。このガイドのすべてのコードでは、
作成するアプリケーションが Azure Cloud Service プロジェクト
であること、Azure アプリケーションのサービス構成に格納されているストレージ接続文字列を使用することを前提としています。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

## <a name="add-entity"></a><span class="short-header">テーブルへのエンティティの追加</span>方法: エンティティをテーブルに追加する

エンティティは、
**TableEntity** から派生するカスタム クラスを使用して C# オブジェクトにマップされます。エンティティをテーブルに追加するには、
エンティティのプロパティを定義するクラスを作成します。次のコードは、
ユーザーの名を行キーとして、姓をパーティション キーとしてそれぞれ
使用するエンティティ クラスを定義します。エンティティのパーティション キー
と行キーの組み合わせで、テーブル内のエンティティを一意に識別します。同じ
パーティション キーを持つエンティティは、異なるパーティション キーを持つ
エンティティよりも迅速に照会できます。一方、多様なパーティション キー
を使用すると、並列操作の拡張性が向上します。テーブル サービスに格納するプロパティは、
`get` と `set` の両方を公開する、サポートされている型のパブリック プロパティである必要があります。
また、エンティティ型で、パラメーターのないコンストラクターを公開する*必要があります*。

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

エンティティに関連するテーブル操作は、「方法: テーブルの作成」で作成した **CloudTable**
オブジェクトを使用して実行します。実行する操作は、
**TableOperation** オブジェクトによって表されます。次のコード例では、まず **CloudTable** オブジェクトを作成し、次に **CustomerEntity** オブジェクトを作成します。その後、操作を準備するために、ユーザー エンティティをテーブルに挿入する **TableOperation** を作成します。最後に、**CloudTable.Execute** を呼び出して操作を実行します。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

## <a name="insert-batch"></a><span class="short-header">エンティティのバッチの挿入</span>方法: エンティティのバッチを挿入する

1 回の書き込み操作でエンティティのバッチをテーブルに
挿入できます。バッチ操作に関しては、
次の事項にも留意してください。

1.  更新、削除、および挿入を同じ 1 回のバッチ操作で実行できます。
2.  1 つのバッチ操作には、最大 100 個のエンティティを含めることができます。
3.  1 つのバッチ操作に含まれるすべてのエンティティの
    パーティション キーが同じである必要があります。
4.  クエリをバッチ操作として実行することもできますが、バッチ内の唯一の操作である必要があります。

<!-- -->

次のコード例は、2 つのエンティティ オブジェクトを作成し、
**Insert** メソッドを使用して **TableBatchOperation** に追加します。その後、**CloudTable.Execute** を呼び出して操作を実行します。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

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

## <a name="retrieve-all-entities"></a><span class="short-header">すべてのエンティティの取得</span>方法: パーティション内のすべてのエンティティを取得する

テーブルに対してパーティション内のすべてのエンティティを照会する場合は、**TableQuery** オブジェクトを使用します。
次のコード例は、'Smith' がパーティション キーであるエンティティに対してフィルターを
指定します。この例は、クエリ結果の
各エンティティのフィールドをコンソールに出力します。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-range-entities"></a><span class="short-header">一定範囲のエンティティの取得</span>方法: パーティション内の一定範囲のエンティティを取得する

パーティション内の一部のエンティティのみを照会する場合は、
パーティション キー フィルターと行キー フィルターを組み合わせて範囲を指定できます。次のコード例は、
2 つのフィルターを使用して、行キー (名) がアルファベットの "E" より
前の文字で始まる、"Smith" というパーティション内のすべてのエンティティを取得し、
クエリ結果を出力します。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-single-entity"></a><span class="short-header">単一のエンティティの取得</span>方法: 単一のエンティティを取得する

単一の特定のエンティティを取得するクエリを記述することができます。次の
コードは、**TableOperation** を使用して、'Ben Smith' というユーザーを指定します。
このメソッドで返されるのは、エンティティの
コレクションではなく、単一のエンティティのみです。したがって、**TableResult.Result** の戻り値は **CustomerEntity** です。
クエリでパーティション キーと行キーの両方を指定することが、
テーブル サービスから単一のエンティティを取得するための最速の方法です。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="replace-entity"></a><span class="short-header">エンティティの置換</span>方法: エンティティを置換する

エンティティを更新するには、そのエンティティをテーブル サービス
から取得し、エンティティ オブジェクトを変更して、変更をテーブル サービスに戻して保存します。次の
コードは、既存のユーザーの電話番号を変更します。このコードでは、
**Insert** を呼び出す代わりに
**Replace** を使用しています。これにより、サーバーでエンティティが完全に置換されます。
ただし、エンティティを取得した後にサーバーでエンティティが
変更された場合は、操作が失敗します。このエラーは、取得と更新の間に
アプリケーションの別のコンポーネントによって行われた変更が意図せず
上書きされるのを防ぐためのものです。このエラーを正しく処理するには、
もう一度エンティティを取得し、変更を加えて (その変更がまだ
有効な場合)、再び **Replace** 操作を実行します。次のセクションでは、
この動作をオーバーライドする方法を説明します。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-0105";

       // Create the InsertOrReplace TableOperation
       TableOperation updateOperation = TableOperation.Replace(updateEntity);

       // Execute the operation.
       table.Execute(updateOperation);

       Console.WriteLine("Entity updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="insert-or-replace-entity"></a><span class="short-header">エンティティの挿入または置換</span>方法: エンティティの挿入または置換を行う

**Replace** 操作は、サーバーからエンティティを取得した後に
エンティティが変更されていると失敗します。さらに、
**Replace** が成功するためには、先にエンティティをサーバーから取得する必要があります。
しかし、サーバーにエンティティが存在するかどうかわからないが、
現在格納されている値は不適切であるため、すべての値を上書きする
必要がある場合もあります。そのような場合は、**InsertOrReplace**
 操作を使用します。この操作は、最終更新日時に関係なく、エンティティが
存在しない場合は挿入し、存在する場合は置換します。次の
コード例では、先ほどと同じように Ben Smith のユーザー エンティティを取得していますが、今度は **InsertOrReplace** を使用してサーバーに保存しています。そのため、
取得操作と更新操作の間に行われたエンティティの更新は上書きされ
ます。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-1234";

       // Create the InsertOrReplace TableOperation
       TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

       // Execute the operation.
       table.Execute(insertOrReplaceOperation);

       Console.WriteLine("Entity was updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="query-entity-properties"></a><span class="short-header">プロパティのサブセットの照会</span>方法: エンティティ プロパティのサブセットを照会する

テーブル クエリでは、エンティティのすべてのプロパティではなくごくわずかのプロパティだけをエンティティから取得できます。プロジェクションと呼ばれるこの方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。次のコードの
クエリは、テーブル内のエンティティの電子メール アドレスだけを
返します。これは、**DynamicTableEntity** のクエリと
**EntityResolver** を使用して行われます。プロジェクションの詳細については、この[ブログの記事][ブログの記事]を参照してください。プロジェクションはローカル ストレージ エミュレーターではサポートされていません。したがって、このコードはテーブル サービスのアカウントを使用している場合にのみ機能します。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and only select the Email property
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

## <a name="delete-entity"></a><span class="short-header">エンティティの削除</span>方法: エンティティを削除する

エンティティは、取得後に簡単に削除できます。エンティティの更新のときと
同じパターンを使用します。次のコードは、
ユーザー エンティティを取得して削除します。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       table.Execute(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Could not retrieve the entity.");

## <a name="delete-table"></a><span class="short-header">テーブルの削除</span>方法: テーブルを削除する

最後に、次のコード例は、ストレージ アカウントからテーブルを削除します。削除
されたテーブルは、削除後の一定期間は再作成できなくなります。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## <a name="next-steps"></a><span class="short-header">次のステップ</span>次のステップ

これで、テーブル ストレージの基本を学習できました。さらに複雑な
ストレージ タスクを実行する方法については、次のリンク先を参照してください。

-   利用可能な API の詳細については、テーブル サービスのリファレンス ドキュメントを参照してください。
    -   [.NET 用ストレージ クライアント ライブラリ リファレンス][.NET 用ストレージ クライアント ライブラリ リファレンス]
    -   [REST API リファレンス][REST API リファレンス]
-   Azure Storage を使用して実行できるさらに高度なタスクについては、「[Azure のデータの格納とアクセス][Azure のデータの格納とアクセス]」を参照してください。
-   Azure Websites のバックエンド プロセスで Azure Storage を使用する方法については、「[Get Started with the Azure WebJobs SDK (Azure Web ジョブ SDK の概要)][Get Started with the Azure WebJobs SDK (Azure Web ジョブ SDK の概要)]」を参照してください。
-   Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。
    -   非構造化データの格納には、[BLOB ストレージ][BLOB ストレージ]を使用します。
    -   構造化データの格納には、[キュー ストレージ][キュー ストレージ]を使用します。
    -   リレーショナル データの格納には、[SQL データベース][SQL データベース]を使用します。

  [次のステップ]: #next-steps
  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [Azure SDK for .NET]: /ja-jp/downloads/
  [方法: プログラムでテーブル ストレージにアクセスする]: #configure-access
  [テーブル サービスとは]: #what-is
  [概念]: #concepts
  [Azure のストレージ アカウントの作成]: #create-account
  [ストレージ接続文字列の設定]: #setup-connection-string
  [方法: テーブルの作成]: #create-table
  [方法: エンティティをテーブルに追加する]: #add-entity
  [方法: エンティティのバッチを挿入する]: #insert-batch
  [方法: パーティション内のすべてのエンティティを取得する]: #retrieve-all-entities
  [方法: パーティション内の一定範囲のエンティティを取得する]: #retrieve-range-entities
  [方法: 単一のエンティティを取得する]: #retrieve-single-entity
  [方法: エンティティを置換する]: #replace-entity
  [方法: エンティティの挿入または置換を行う]: #insert-or-replace-entity
  [方法: エンティティ プロパティのサブセットを照会する]: #query-entity-properties
  [方法: エンティティを削除する]: #delete-entity
  [方法: テーブルを削除する]: #delete-table
  [.NET デベロッパー センター]: http://www.windowsazure.com/ja-jp/develop/net/#
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [ブログの記事]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [.NET 用ストレージ クライアント ライブラリ リファレンス]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API リファレンス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dd179355
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
  [Get Started with the Azure WebJobs SDK (Azure Web ジョブ SDK の概要)]: /ja-jp/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [BLOB ストレージ]: /ja-jp/documentation/articles/storage-dotnet-how-to-use-blobs/
  [キュー ストレージ]: /ja-jp/documentation/articles/storage-dotnet-how-to-use-queues/
  [SQL データベース]: /ja-jp/documentation/articles/sql-database-dotnet-how-to-use/
