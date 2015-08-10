<properties 
	pageTitle="Azure Storage の使用" 
	description="Visual Studio の ASP.NET プロジェクトで Azure テーブル ストレージの使用を開始する方法" 
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

# Azure Storage の使用 (ASP.NET プロジェクト)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet-getting-started-tables.md)
> - [What Happened](vs-storage-aspnet-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet-getting-started-queues.md)
> - [Tables](vs-storage-aspnet-getting-started-tables.md)

## 概要
この記事では、Visual Studio の **[接続済みサービスの追加]** ダイアログを使用して Azure Cloud Services で Azure ストレージ アカウントを参照または作成した後に、Visual Studio で Azure テーブル ストレージの使用を開始する方法について説明します。Azure テーブル ストレージの使用についてのより一般的な情報については、[「.NET からテーブル ストレージを使用する方法」](storage-dotnet-how-to-use-tables.md)を参照してください。


Azure テーブル ストレージ サービスを使用すると、大量の構造化データを格納できるようになります。このサービスは、Azure クラウドの内部および外部からの認証された呼び出しを受け付ける NoSQL データストアです。Azure のテーブルは、構造化された非リレーショナル データを格納するのに最適です。


この記事では、Azure テーブルでの一般的なタスクを実行する方法について説明します。例は C# のコードで記述され、Azure .NET 用ストレージ クライアント ライブラリを利用しています。紹介するシナリオは、**テーブルの作成と削除**、**テーブル エンティティの操作**などです。

##Visual Studio サーバー エクスプローラーで Azure ストレージ テーブルを作成する

[AZURE.INCLUDE [vs-create-table-in-server-explorer](../../includes/vs-create-table-in-server-explorer.md)]

##コードでテーブルにアクセスする 



1. C# ファイル冒頭の名前空間宣言に、次の `using` ステートメントが含まれていることを確認してください。

	using Microsoft.Azure; using Microsoft.WindowsAzure.Storage; using Microsoft.WindowsAzure.Storage.Auth; using Microsoft.WindowsAzure.Storage.Table;

2. ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

    **注:** 上記のコードはすべて、以下の例に示すコードの前に使用してください。

3. ストレージ アカウント内のテーブル オブジェクトを参照する **CloudTableClient** オブジェクトを取得します。

	    // Create the table client.
    	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. 特定のテーブルとエンティティを参照する **CloudTable** 参照オブジェクトを取得します。
	
    	// Get a reference to a table named "peopleTable"
	    CloudTable table = tableClient.GetTableReference("peopleTable");

###コードでテーブルを作成する

Visual Studio **サーバー エクスプローラー**を使用せずに、コードで Azure テーブルを作成するには、`CreateIfNotExistsAsync()` への呼び出しを追加します。

	// Create the CloudTable if it does not exist
	await table.CreateIfNotExistsAsync();

	// Get a reference to the **CloudTable** object named 'peopleTable' as described in "Access a table in code"

	// Create a new customer entity.
	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
	customer1.Email = "Walter@contoso.com";
	customer1.PhoneNumber = "425-555-0101";
	
	// Create the TableOperation that inserts the customer entity.
	TableOperation insertOperation = TableOperation.Insert(customer1);
	
	// Execute the insert operation.
	await peopleTable.ExecuteAsync(insertOperation);

##エンティティのバッチを挿入する

1 回の書き込み操作で複数のエンティティをテーブルに挿入できます。次のコード例は、2 つのエンティティ オブジェクト ("Jeff Smith" と "Ben Smith") を作成し、Insert メソッドを使用して **TableBatchOperation** オブジェクトにそれらのエンティティ オブジェクトを追加し、CloudTable.ExecuteBatchAsync を呼び出して操作を開始します。

	// Get a reference to a **CloudTable** object named 'peopleTable' as described in "Access a table in code"
	
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
テーブルに対してパーティション内のすべてのエンティティを照会する場合は、**TableQuery** オブジェクトを使用します。次のコード例は、'Smith' がパーティション キーであるエンティティに対してフィルターを指定します。この例は、クエリ結果の各エンティティのフィールドをコンソールに出力します。

	// Get a reference to a **CloudTable** object named 'peopleTable' as described in "Access a table in code"

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
単一の特定のエンティティを取得するクエリを記述することができます。次のコードは、**TableOperation** オブジェクトを使用して、"Ben Smith" という名前のユーザーを指定します。このメソッドで返されるのは、エンティティのコレクションではなく、単一のエンティティのみです。したがって、TableResult.Result の戻り値は **CustomerEntity** オブジェクトです。クエリでパーティション キーと行キーの両方を指定することが、**テーブル** サービスから単一のエンティティを取得するための最速の方法です。

	// Get a reference to a **CloudTable** object named 'peopleTable' as described in "Access a table in code"
	
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

	// Get a reference to a **CloudTable** object named 'peopleTable' as described in "Access a table in code"
	
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

## 次のステップ

[AZURE.INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]



  [Download and install the Azure SDK for .NET]: /develop/net/
  [Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx
  
  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png
  
  [blog post]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [How to: Programmatically access Table Storage]: #tablestorage



 

<!---HONumber=July15_HO5-->