<properties 
	pageTitle=".NET からテーブル ストレージを使用する方法 | Microsoft Azure" 
	description="Microsoft Azure Table ストレージを使用してテーブルを作成および削除する方法、さらにテーブル内のエンティティを挿入および照会する方法について説明します。" 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tamram"/>


# .NET からテーブル ストレージを使用する方法

[AZURE.INCLUDE [storage-selector-table-include](../includes/storage-selector-table-include.md)]

## 概要

このガイドでは、Azure Table ストレージ サービスを使用して 
一般的なシナリオを実行する方法について説明します。例は C\# のコードで記述され、Azure .NET 用ストレージ クライアント ライブラリを利用しています。紹介するシナリオは、**テーブルの作成と削除**、**テーブル エンティティの操作**などです。

> [AZURE.NOTE] このガイドは、Azure .NET 用ストレージ クライアント ライブラリ 2.x 以上を対象としています。推奨されるバージョンは、ストレージ クライアント ライブラリ 4.x です。これは [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) から、または [Azure SDK for .NET](/downloads/) の一部として提供されています。ストレージ クライアント ライブラリの取得の詳細については、以下の[プログラムでテーブル ストレージにアクセスする](#programmatically-access-table-storage) を参照してください。

[AZURE.INCLUDE [storage-table-concepts-include](../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../includes/storage-configure-connection-string-include.md)]

## プログラムでテーブル ストレージにアクセスする

### アセンブリの取得
NuGet を使用して  `Microsoft.WindowsAzure.Storage.dll` アセンブリを取得できます。**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。"WindowsAzure.Storage" をオンライン検索し、**[インストール]** をクリックして、Azure Storage パッケージと依存関係をインストールします。

`Microsoft.WindowsAzure.Storage.dll` は、<a href="http://azure.microsoft.com/develop/net/#">.NET デベロッパー センター</a>からダウンロードできる Azure SDK for .NET にも含まれています。このアセンブリは  `%Program Files%\Microsoft SDKs\Azure\.NET SDK\<sdk-version>\ref\` ディレクトリにインストールされます。

### 名前空間宣言
プログラムを使用して Azure Storage にアクセスするすべての C\# ファイルの冒頭部分に、次の名前空間宣言コードを追加します。

    using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

必ず  `Microsoft.WindowsAzure.Storage.dll` アセンブリを参照してください。

### 接続文字列の取得
**CloudStorageAccount** 型を使用してストレージ アカウント情報を表すことができます。Azure プロジェクト テンプレートを 
使用している場合や、
Microsoft.WindowsAzure.CloudConfigurationManager 名前空間への参照を使用している場合、**CloudConfigurationManager** 型を使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

作成しているアプリケーションに Microsoft.WindowsAzure.CloudConfigurationManager への参照が含まれておらず、接続文字列が前に示したとおり `web.config` または `app.config` に格納されている場合は、**ConfigurationManager** を使用して接続文字列を取得できます。System.Configuration.dll への参照をプロジェクトに追加し、対応する名前空間宣言を追加する必要があります。

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"]);

### ODataLib 依存
.NET 用ストレージ クライアント ライブラリの ODataLib 依存は、WCF Data Services ではなく、NuGet から入手できる ODataLib (バージョン 5.0.2) パッケージで解決されます。ODataLib ライブラリは、直接ダウンロードすることも、NuGet を使用してコード プロジェクトで参照することもできます。具体的な ODataLib パッケージは [OData]、[Edm]、および [Spatial] です。

## テーブルを作成する

**CloudTableClient** オブジェクトを使用するテーブルとエンティティの参照オブジェクトを取得できます。次のコードは、**CloudTableClient** オブジェクトを作成し、これを使用して新しいテーブルを作成します。このガイドのすべてのコードでは、作成するアプリケーションが Azure Cloud Services プロジェクトであること、Azure アプリケーションのサービス構成に格納されているストレージ接続文字列を使用することを前提としています。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

## エンティティをテーブルに追加する

エンティティは、
**TableEntity** から派生するカスタム クラスを使用して、C\# オブジェクトにマップされます。エンティティをテーブルに追加するには、エンティティのプロパティを定義するクラスを作成します。次のコードは、ユーザーの名を行キーとして、姓をパーティション キーとしてそれぞれ使用するエンティティ クラスを定義します。エンティティのパーティション キーと行キーの組み合わせで、テーブル内のエンティティを一意に識別します。同じパーティション キーを持つエンティティは、異なるパーティション キーを持つエンティティよりも迅速に照会できます。一方、多様なパーティション キーを使用すると、並列操作の拡張性が向上します。テーブル サービスに格納するプロパティは `get` と `set` の両方を公開する、サポートされている型のパブリック プロパティである必要があります。
また、エンティティ型で、パラメーターのないコンストラクターを必ず公開する必要があります。 *must*

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

エンティティに関連するテーブル操作は、「方法: テーブルの作成」で作成した **CloudTable** オブジェクトを使用して実行します。実行する操作は、**TableOperation** オブジェクトによって表されます。次のコード例では、まず **CloudTable** オブジェクトを作成し、次に **CustomerEntity** オブジェクトを作成します。その後、操作を準備するために、ユーザー エンティティをテーブルに挿入する **TableOperation** を作成します。最後に、**CloudTable.Execute** を呼び出して操作を実行します。

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

## エンティティのバッチを挿入する

1 回の書き込み操作でエンティティのバッチをテーブルに挿入できます。バッチ操作に関しては、次の事項にも留意してください。

1.  更新、削除、および挿入を同じ 1 回のバッチ操作で実行できます。
2.  1 つのバッチ操作には、最大 100 個のエンティティを含めることができます。
3.  1 つのバッチ操作に含まれるすべてのエンティティのパーティション キーが
    同じである必要があります。
4.  クエリをバッチ操作として実行することもできますが、バッチ内の唯一の操作である必要があります。

<!-- -->
次のコード例は、2 つのエンティティ オブジェクトを作成し、**Insert** メソッドを使用して **TableBatchOperation** に追加します。その後、**CloudTable.Execute** を呼び出して操作を実行します。

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

## パーティション内のすべてのエンティティを取得する

テーブルに対してパーティション内のすべてのエンティティを照会する場合は、**TableQuery** オブジェクトを使用します。
次のコード例は、 'Smith' がパーティション キーであるエンティティに対してフィルターを指定します。この例は、クエリ結果の各エンティティのフィールドをコンソールに出力します。

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

## パーティション内の一定範囲のエンティティを取得する

パーティション内の一部のエンティティのみを照会する場合は、パーティション キー フィルターと行キー フィルターを組み合わせて範囲を指定できます。次のコード例は、2 つのフィルターを使用して、行キー (名) がアルファベットの "E" より前の文字で始まる、 'Smith' というパーティション内のすべてのエンティティを取得し、クエリ結果を出力します。

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

## 単一のエンティティを取得する

単一の特定のエンティティを取得するクエリを記述することができます。次のコードは、**TableOperation** を使用して、 'Ben Smith' というユーザーを指定します。
このメソッドで返されるのは、エンティティのコレクションではなく、単一のエンティティのみです。したがって、**TableResult.Result** の戻り値は **CustomerEntity** です。
クエリでパーティション キーと行キーの両方を指定することが、テーブル サービスから単一のエンティティを取得するための最速の方法です。

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

## エンティティを置換する

エンティティを更新するには、そのエンティティをテーブル サービスから取得し、エンティティ オブジェクトを変更して、変更をテーブル サービスに戻して保存します。次のコードは、既存のユーザーの電話番号を変更します。このコードでは、**Insert** を呼び出す代わりに 
**Replace** を使用しています。これにより、サーバーでエンティティが完全に置換されます。ただし、エンティティを取得した後にサーバーでエンティティが変更された場合は、操作が失敗します。このエラーは、取得と更新の間にアプリケーションの別のコンポーネントによって行われた変更が意図せず上書きされるのを防ぐためのものです。このエラーを正しく処理するには、もう一度エンティティを取得し、変更を加えて (その変更がまだ有効な場合)、再び **Replace** 操作を実行します。次のセクションでは、この動作をオーバーライドする方法を説明します。

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

## エンティティの挿入または置換を行う

**Replace** 操作は、サーバーからエンティティを取得した後にエンティティが変更されていると失敗します。さらに、**Replace** が成功するためには、先にエンティティをサーバーから取得する必要があります。
しかし、サーバーにエンティティが存在するかどうかわからないが、現在格納されている値は不適切であるため、すべての値を上書きする必要がある場合もあります。そのような場合は、**InsertOrReplace** 操作を使用します。この操作は、最終更新日時に関係なく、エンティティが存在しない場合は挿入し、存在する場合は置換します。次のコード例では、先ほどと同じように Ben Smith のユーザー エンティティを取得していますが、今度は **InsertOrReplace** を使用してサーバーに保存しています。そのため、取得操作と更新操作の間に行われたエンティティの更新は上書きされます。

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

## エンティティ プロパティのサブセットを照会する

テーブル クエリでは、エンティティのすべてのプロパティではなくごくわずかのプロパティだけをエンティティから取得できます。プロジェクションと呼ばれるこの方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。次のコードのクエリは、テーブル内のエンティティの電子メール アドレスだけを返します。これは、**DynamicTableEntity** のクエリと **EntityResolver** を使用して行われます。プロジェクションの詳細については、この[ブログの記事][]を参照してください。プロジェクションはローカル ストレージ エミュレーターではサポートされていません。したがって、このコードはテーブル サービスのアカウントを使用している場合にのみ機能します。

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

## エンティティを削除する

エンティティは、取得後に簡単に削除できます。エンティティの更新のときと同じパターンを使用します。次のコードは、ユーザー エンティティを取得して削除します。

    // 接続文字列からストレージ アカウントを取得します
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

## テーブルを削除する

最後に、次のコード例は、ストレージ アカウントからテーブルを削除します。削除されたテーブルは、削除後の一定期間は再作成できなくなります。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## 次のステップ

これで、テーブル ストレージの基本を学習できました。さらに複雑なストレージ タスクについては、次のリンク先を参照してください。

<ul>
<li>利用可能な API の詳細については、テーブル サービスのリファレンス ドキュメントを参照してください。
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">.NET 用ストレージ クライアント ライブラリ リファレンス</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/azure/dd179355">REST API リファレンス</a></li>
  </ul>
</li>
<li>Azure Storage を使用して実行できるさらに高度なタスクについては、<a href="http://msdn.microsoft.com/library/azure/gg433040.aspx">Azure のデータの格納とアクセス</a>に関するページを参照してください。</li>
<li><a href="../websites-dotnet-webjobs-sdk/">Azure WebJobs SDK を使用して Azure Storage で作業するためのコードの記述を簡略化する方法を説明します。</li>
<li>Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。
  <ul>
    <li>非構造化データの格納には、<a href="/documentation/articles/storage-dotnet-how-to-use-blobs/">BLOB ストレージ</a>を使用します。</li>
    <li>構造化データの格納には、<a href="/documentation/articles/storage-dotnet-how-to-use-queues/">キュー ストレージ</a>を使用します。</li>
    <li>リレーショナル データの格納には、<a href="/documentation/articles/sql-database-dotnet-how-to-use/">SQL データベース</a>を使用します。</li>
  </ul>
</li>
</ul>

  [Azure SDK for .NET のダウンロードとインストール]: /develop/net/
  [Visual Studio を使用した Azure プロジェクトの作成]: http://msdn.microsoft.com/library/azure/ee405487.aspx
  
  [BLOB5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [BLOB6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [BLOB7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [BLOB8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [BLOB9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png
  
  [ブログの投稿]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [.NET クライアント ライブラリ リファレンス]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Azure でのデータの格納とアクセス]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure のストレージ チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/
  [接続文字列の構成]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [方法: プログラムでテーブル ストレージにアクセスする]: #tablestorage

<!--HONumber=49-->