<properties 
	pageTitle="Azure テーブル ストレージと Visual Studio 接続済みサービスの概要" 
	description="Visual Studio の ASP.NET 5 プロジェクトで Azure テーブル ストレージの使用を開始する方法" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="patshea123"/>

# Azure テーブル ストレージと Visual Studio 接続済みサービスの概要

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-tables.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

##概要

この記事では、Visual Studio の **[接続済みサービスの追加]** ダイアログを使用して ASP.NET 5 プロジェクトで Azure ストレージ アカウントを参照または作成した後に、Visual Studio で Azure テーブル ストレージの使用を開始する方法について説明します。

Azure テーブル ストレージ サービスを使用すると、大量の構造化データを格納できるようになります。このサービスは、Azure クラウドの内部および外部からの認証された呼び出しを受け付ける NoSQL データストアです。Azure のテーブルは、構造化された非リレーショナル データを格納するのに最適です。

**接続済みサービスの追加** 操作によって、プロジェクト内の Azure ストレージにアクセスする適切な NuGet パッケージがインストールされ、プロジェクトの構成ファイルに、ストレージ アカウントの接続文字列が追加されます。

Azure テーブル ストレージの使用についてのより一般的な情報については、[「.NET からテーブル ストレージを使用する方法」](storage-dotnet-how-to-use-tables.md)を参照してください。

最初に、ストレージ アカウント内にテーブルを作成する必要があります。コードで Azure テーブルを作成する方法を説明します。テーブルおよびエンティティの基本的な操作についても説明します (テーブル エンティティの追加、変更、読み取りなど)。例は C# のコードで記述され、Azure .NET 用ストレージ クライアント ライブラリを利用しています。

**注:** ASP.NET 5 で Azure ストレージへの呼び出しを実行する API の一部は非同期です。詳細については、[Async および Await を使用した非同期プログラミング](http://msdn.microsoft.com/library/hh191443.aspx)に関するページを参照してください。次のコードでは、非同期のプログラミング方法を使用していることを前提としています。

##コードでテーブルにアクセスする

ASP.NET 5 プロジェクト内のテーブルにアクセスするには、Azure テーブル ストレージにアクセスする C# ソース ファイルに、次の項目を含める必要があります。

1. C# ファイル冒頭の名前空間宣言に、次の `using` ステートメントが含まれていることを確認してください。

using Microsoft.Framework.Configuration; using Microsoft.WindowsAzure.Storage; using Microsoft.WindowsAzure.Storage.Table; using System.Threading.Tasks; using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. ストレージ アカウント情報を表す `CloudStorageAccount` オブジェクトを取得します。次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。

	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
	        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **注:** 上記のコードはすべて、以下の例に示すコードの前に使用してください。


3. ストレージ アカウント内のテーブル オブジェクトを参照する `CloudTableClient` オブジェクトを取得します。

	    // Create the table client.
    	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. 特定のテーブルとエンティティを参照する `CloudTable` 参照オブジェクトを取得します。
	
    	// Get a reference to a table named "peopleTable"
	    CloudTable table = tableClient.GetTableReference("peopleTable");

##コードでテーブルを作成する

`CreateIfNotExistsAsync()` に呼び出しを追加するだけで Azure テーブルが作成できます。

	// Create the CloudTable if it does not exist
	await table.CreateIfNotExistsAsync();

##エンティティをテーブルに追加する

エンティティをテーブルに追加するには、エンティティのプロパティを定義するクラスを作成します。次のコードは、ユーザーの名を行キーとし、姓をパーティション キーとして使用する **CustomerEntity** という名前のエンティティ クラスを定義します。

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

エンティティに関連するテーブル操作は、"コードでテーブルにアクセスする" で作成した `CloudTable` オブジェクトを使用して実行されます。 `TableOperation` オブジェクトは、実行する操作を表しています。次のコード例では、`CloudTable` オブジェクトと `CustomerEntity` オブジェクトを作成します。その後、操作を準備するために、ユーザー エンティティをテーブルに挿入する `TableOperation` を作成します。最後に、CloudTable.ExecuteAsync を呼び出して操作を実行します。

	// Get a reference to the CloudTable object named 'peopleTable' as described in "Access a table in code"

	// Create a new customer entity.
	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
	customer1.Email = "Walter@contoso.com";
	customer1.PhoneNumber = "425-555-0101";
	
	// Create the TableOperation that inserts the customer entity.
	TableOperation insertOperation = TableOperation.Insert(customer1);
	
	// Execute the insert operation.
	await peopleTable.ExecuteAsync(insertOperation);

##エンティティのバッチを挿入する

1 回の書き込み操作で複数のエンティティをテーブルに挿入できます。次のコード例は、2 つのエンティティ オブジェクト ("Jeff Smith" と "Ben Smith") を作成し、Insert メソッドを使用して `TableBatchOperation` オブジェクトにそれらのエンティティ オブジェクトを追加し、CloudTable.ExecuteBatchAsync を呼び出して操作を開始します。

	// Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"
	
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
	await peopleTable.ExecuteBatchAsync(batchOperation);

##パーティション内のすべてのエンティティを取得する
テーブルに対してパーティション内のすべてのエンティティを照会する場合は、`TableQuery` オブジェクトを使用します。次のコード例は、'Smith' がパーティション キーであるエンティティに対してフィルターを指定します。この例は、クエリ結果の各エンティティのフィールドをコンソールに出力します。

	// Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"

	// Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
    	TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
		token = resultSegment.ContinuationToken;

		foreach (CustomerEntity entity in resultSegment.Results)
    	{
    		Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
    		entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

    return View();


##単一のエンティティを取得する
単一の特定のエンティティを取得するクエリを記述することができます。次のコードは、`TableOperation` オブジェクトを使用して、"Ben Smith" という名前のユーザーを指定します。このメソッドで返されるのは、エンティティのコレクションではなく、単一のエンティティのみです。したがって、`TableResult.Result` の戻り値は `CustomerEntity` オブジェクトです。クエリでパーティション キーと行キーの両方を指定することが、**テーブル** サービスから単一のエンティティを取得するための最速の方法です。

	// Get a reference to a CloudTableobject named 'peopleTable' as described in "Access a table in code"
	
	// Create a retrieve operation that takes a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the retrieve operation.
	TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);
	
	// Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

##エンティティを削除する
エンティティは、検索して削除できます。次のコードは、"Ben Smith" という名前のユーザー エンティティを検索し、見つかったら削除します。

	// Get a reference to a CloudTableobject named 'peopleTable' as described in "Access a table in code"
	
	// Create a retrieve operation that expects a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the operation.
	TableResult retrievedResult = peopleTable.Execute(retrieveOperation);
	
	// Assign the result to a CustomerEntity object.
	CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;
	
	// Create the Delete TableOperation and then execute it.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
	
	   // Execute the operation.
	   await peopleTable.ExecuteAsync(deleteOperation);
	
	   Console.WriteLine("Entity deleted.");
	}
	
	else
	   Console.WriteLine("Couldn't delete the entity.");

##次のステップ

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

<!---HONumber=August15_HO6-->