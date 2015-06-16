<properties 
	pageTitle="Web ジョブ SDK を使用して Azure テーブル ストレージを使用する方法" 
	description="Web ジョブ SDK で Azure テーブル ストレージを使用する方法について説明します。テーブルを作成し、エンティティをテーブルに追加し、既存のテーブルを読み取ります。" 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/03/2015" 
	ms.author="tdykstra"/>

# Web ジョブ SDK を使用して Azure テーブル ストレージを使用する方法

## 概要

このガイドでは、[Web ジョブ SDK](websites-dotnet-webjobs-sdk.md) バージョン 1.x を使用して Azure ストレージ テーブルを読み書きする方法を示す C# コード サンプルについて説明します。

このガイドは、[Visual Studio でストレージ アカウントを指定する接続文字列を使って Web ジョブ プロジェクトを作成する方法](websites-dotnet-webjobs-sdk-get-started.md)を理解していることを前提としています。
		
一部のコード スニペットは、[手動で呼び出される](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual)関数で使用される  `Table` 属性を表示します。つまり、トリガー属性を使用して呼び出すのではありません。 

## <a id="ingress"></a>エンティティをテーブルに追加する方法

エンティティをテーブルに追加するには、 `ICollector<T>` を持つ  `Table` 属性または  `T` specifies the schema of the entities you want to add. The attribute constructor takes a string parameter that specifies the name of the table.  `IAsyncCollector  パラメーターを使用します。ここで、<T>` は 

The following code sample adds `Person` *Ingress* という名前のテーブルに対するエンティティです。

		[NoAutomaticTrigger]
		public static void IngressDemo(
		    [Table("Ingress")] ICollector<Person> tableBinding)
		{
		    for (int i = 0; i < 100000; i++)
		    {
		        tableBinding.Add(
		            new Person() { 
		                PartitionKey = "Test", 
		                RowKey = i.ToString(), 
		                Name = "Name" }
		            );
		    }
		}

通常、 `ICollector` と連携して使用する種類は、 `TableEntity` から派生するか、 `ITableEntity` を実装しますが、しなくてもかまいません。次のいずれかの  `Person` クラスは、前の  `Ingress`  メソッドに示されているコードと連携します。

		public class Person : TableEntity
		{
		    public string Name { get; set; }
		}

		public class Person
		{
		    public string PartitionKey { get; set; }
		    public string RowKey { get; set; }
		    public string Name { get; set; }
		}

Azure のストレージ API を直接操作する場合は、メソッドシグネチャに  `CloudStorageAccount` パラメーターを追加することもできます。

## <a id="monitor"></a>リアルタイム監視

多くの場合、データの受信関数は大量のデータを処理するため、Web ジョブ SDK のダッシュボードはリアルタイムの監視データを提供します。**呼び出しログ**セクションは、関数がまだ実行であるかどうかを示します。

![実行中の受信関数](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

**呼び出しの詳細**ページは、実行中の関数の進行状況 (書き込まれるエンティティの数) を報告し、実行を中止できます。 

![実行中の受信関数](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

関数が終了したら、**呼び出しの詳細**ページは書き込まれた行の数を報告します。

![完了した受信関数](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a>テーブルから複数のエンティティを読み取る方法

テーブルを読み取るには、種類が  `T` derives from `TableEntity` の  `IQueryable<T>` パラメーターで  `Table` 属性を使用するか  `ITableEntity` を実装します。

次のコード サンプルは、 `Ingress` テーブルからすべての行を読み取り、ログに記録します。
 
		public static void ReadTable(
		    [Table("Ingress")] IQueryable<Person> tableBinding,
		    TextWriter logger)
		{
		    var query = from p in tableBinding select p;
		    foreach (Person person in query)
		    {
		        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}", 
		            person.PartitionKey, person.RowKey, person.Name);
		    }
		}

### <a id="readone"></a>テーブルから 1 つのエンティティを読み取る方法

1 つのテーブル エンティティにバインドする際に、パーティション キーと行キーを指定できる追加の 2 つのパラメーターを持つ  `Table` 属性コンストラクターがあります。

次のコード サンプルは、キュー メッセージで受信したパーティション キー値と行キー値に基づいた  `Person` エンティティのテーブル行を読み取ります。  

		public static void ReadTableEntity(
		    [QueueTrigger("inputqueue")] Person personInQueue,
		    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
		    TextWriter logger)
		{
		    if (personInTable == null)
		    {
		        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
		                personInQueue.PartitionKey, personInQueue.RowKey);
		    }
		    else
		    {
		        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
		                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
		    }
		}


この例の  `Person` クラスは、 `ITableEntity` を実装する必要はありません。

## <a id="storageapi"></a>.NET ストレージ API を直接使用して、テーブルを操作する方法

テーブルを操作する際に、 `CloudTable` オブジェクトを使用して、より柔軟に  `Table` 属性を使用することもできます。

次のコード サンプルは、 `CloudTable` オブジェクトを使用して、1 つのエンティティを  *Ingress* テーブルに追加します。 
 
		public static void UseStorageAPI(
		    [Table("Ingress")] CloudTable tableBinding,
		    TextWriter logger)
		{
		    var person = new Person()
		        {
		            PartitionKey = "Test",
		            RowKey = "100",
		            Name = "Name"
		        };
		    TableOperation insertOperation = TableOperation.Insert(person);
		    tableBinding.Execute(insertOperation);
		}

 `CloudTable` オブジェクトの使用方法の詳細については、「[.NET からテーブル ストレージを使用する方法](../storage-dotnet-how-to-use-tables.md)」をご覧ください。 

## <a id="queues"></a>キューのハウツー記事で紹介されている関連トピック

キュー メッセージによってトリガーされるテーブルの処理の方法、テーブル処理に固有ではない Web ジョブ SDK のシナリオについては、「[Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)」をご覧ください。 

その記事では、以下のようなトピックが紹介されています。

* Async 関数
* 複数のインスタンス
* 正常なシャットダウン
* 関数本体での Web ジョブ SDK 属性の使用
* コード内での SDK 接続文字列の設定
* コードの Web ジョブ SDK コンストラクター パラメーター値の設定
* 手動での関数のトリガー
* ログの書き込み

## <a id="nextsteps"></a>次のステップ

このガイドでは、Azure テーブルを操作するための一般的なシナリオの処理方法を示すコードのサンプルを提供しました。Azure Web ジョブと Web ジョブ SDK の使用方法の詳細については、[Azure Web ジョブの推奨リソース](http://go.microsoft.com/fwlink/?linkid=390226)に関するページをご覧ください。


<!--HONumber=52--> 