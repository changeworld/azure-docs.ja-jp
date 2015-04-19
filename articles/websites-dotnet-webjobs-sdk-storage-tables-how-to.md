<properties 
	pageTitle="Web ジョブ SDK を使用して Azure テーブル ストレージを使用する方法" 
	description="Web ジョブ SDK を使用して Azure テーブル ストレージを使用する方法について説明します。テーブルを作成し、エンティティをテーブルに追加し、既存のテーブルを読み取ります。" 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="tdykstra"/>

# Web ジョブ SDK を使用して Azure テーブル ストレージを使用する方法

このガイドでは、[Web ジョブ SDK](websites-dotnet-webjobs-sdk.md) バージョン 1.x を使用して Azure のストレージ テーブルを読み書きする方法を示す c# のコード サンプルを提供します。

このガイドでは、読者が [ストレージ アカウントを示す接続文字列を使用して Visual Studio に Web ジョブ プロジェクトを作成する方法を理解していることを前提に説明します。](websites-dotnet-webjobs-sdk-get-started.md).
		
コード スニペットには、[手動で呼び出される](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#manual)  `Table` 関数で使用される属性を示すものもあります。すなわち、それらはトリガー属性では呼び出されません。 

## 目次

-   [テーブルにエンティティを追加する方法](#ingress)
-   [リアルタイム監視](#monitor)
-   [テーブルから複数のエンティティを読み取る方法](#multiple)
-   [テーブルから 1 つのエンティティを読み取る方法](#readone)
-   [NET ストレージ API を直接使用して、テーブルを操作する方法](#readone)
-   [キューの操作方法に関する記事で扱う関連トピック](#queues)
-   [次のステップ](#nextsteps)

## <a id="ingress"></a> テーブルにエンティティを追加する方法

テーブルにエンティティを追加するには、 `ICollector<T>` または  `T` specifies the schema of the entities you want to add. The attribute constructor takes a string parameter that specifies the name of the table.  が指定する  `IAsyncCollector<T>` パラメーターで  `Table` 属性を使用します。

 *Ingress* という名前のテーブルに対する The following code sample adds `Person` エンティティ。

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

通常、 `ICollector` で使用する型は  `TableEntity` から派生するか、または `ITableEntity` を実装しますが、必ずしもそうする必要はありません。次のいずれかの  `Person` クラスは、前述の  `Ingress` メソッドでコードを操作します。

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

Azure ストレージ API を直接操作する場合は、 `CloudStorageAccount` パラメーターをメソッド シグネチャに追加します。

## <a id="monitor"></a> リアルタイム監視

データの受信関数は多くの場合、大量のデータを処理するため、Web ジョブ SDK のダッシュ ボードはリアルタイムの監視データを提供します。**[呼び出しログ]** セクションでは、関数がまだ実行されているかどうかを表示します。

![Ingress function running](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

**[呼び出しの詳細]** ページは、実行中の関数の進行状況 (書き込まれるエンティティの数) を報告し、中止する機会を知らせます。 

![Ingress function running](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

関数が終了すると、**[呼び出しの詳細]** ページは書き込まれた行数を報告します。

![Ingress function finished](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a> テーブルから複数のエンティティを読み取る方法

テーブルを読み取るには、型が TableEntity から  `T` derives from `` する `IQueryable<T>` パラメーターまたは  `ITableEntity` を実装する  `Table` 属性を使用します。

次のコード サンプルは、 `Ingress` テーブルのすべての行を読み取り、ログに記録します。
 
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

### <a id="readone"></a> テーブルから 1 つのエンティティを読み取る方法

1 つのテーブル エンティティにバインドする場合に、パーティション キーと行キーを指定する追加の 2 つのパラメーターを持つ  `Table` 属性コンストラクターがあります。

次のコード サンプルは、キュー メッセージで受信されたパーティション キー値と行キー値に基づいて  `Person` エンティティのテーブル行を読み取ります。  

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


この例の  `Person` クラスでは、 `ITableEntity` を実装する必要はありません。

## <a id="storageapi"></a> NET ストレージ API を直接使用して、テーブルを操作する方法

より柔軟にテーブルを操作するために、 `CloudTable` オブジェクトで  `Table` 属性を使用することもできます。

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

 `CloudTable` オブジェクトの使用方法の詳細については、「[.NET からテーブル ストレージを使用する方法]」をご覧ください(storage-dotnet-how-to-use-tables.md)。 

## <a id="queues"></a>キューの操作方法に関する記事で扱う関連トピック

キュー メッセージによってトリガーされるテーブル処理またはテーブル処理に固有ではない Web ジョブ SDK のシナリオキューの詳細については、「[Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 」をご覧ください。

この記事で取り上げている関連のトピックは、次のとおりです。

* Async 関数
* 複数のインスタンス
* 正常なシャットダウン
* 関数本体での Web ジョブ SDK 属性の使用
* コード内での SDK 接続文字列の設定
* コード内での Web ジョブ SDK コンストラクター パラメーターの値の設定
* 手動による関数のトリガー
* ログの記述

## <a id="nextsteps"></a> 次のステップ

このガイドでは、Azure のテーブルを操作するための一般的なシナリオを処理する方法を示すコード サンプルを提供しています。Azure Web ジョブと Web ジョブ SDK の使用方法の詳細については、「[Azure Web Jobs Recommended Resources (Azure Web ジョブの推奨リソース)](http://go.microsoft.com/fwlink/?linkid=390226)」をご覧ください。


<!--HONumber=42-->
