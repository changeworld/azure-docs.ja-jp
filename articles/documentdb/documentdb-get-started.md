<properties
	pageTitle="NoSQL チュートリアル: DocumentDB .NET SDK | Microsoft Azure"
	description="DocumentDB .NET SDK を使用してオンライン データベースと C# コンソール アプリケーションを作成する NoSQL チュートリアル。DocumentDB は、JSON 用の NoSQL データベースです。"
	keywords="NoSQL チュートリアル, オンライン データベース, C# コンソール アプリケーション"
	services="documentdb"
	documentationCenter=".net"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/30/2016"
	ms.author="anhoh"/>

# NoSQL チュートリアル: DocumentDB C# コンソール アプリケーションの作成

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.JS](documentdb-nodejs-get-started.md)

DocumentDB .NET SDK の NoSQL チュートリアルへようこそ。 このチュートリアルに従うことで、DocumentDB リソースを作成し、クエリするコンソール アプリケーションを準備することができます。

ここで説明する操作は以下のとおりです。

- DocumentDB アカウントを作成して接続する
- Visual Studio ソリューションを構成する
- オンライン データベースを作成する
- コレクションを作成する
- JSON ドキュメントを作成する
- コレクションをクエリする
- データベースを削除する

時間がなくても 心配はありません。 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) で完全なソリューションを入手できます。手順については「[完全なソリューションの取得](#GetSolution)」を参照してください。

その後で、このページの上部または下部にある投票ボタンを使用して、フィードバックをお寄せください。マイクロソフトから直接ご連絡を差し上げて問題がなければ、コメント欄に電子メール アドレスをご記入ください。

それでは始めましょう。

## 前提条件

以下のものがそろっていることを確認してください。

- アクティブな Azure アカウント。サブスクリプションがない場合でも、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
- [Visual Studio 2013/Visual Studio 2015](http://www.visualstudio.com/)。

## 手順 1: DocumentDB アカウントを作成する

DocumentDB アカウントを作成しましょう。使用するアカウントが既にある場合は、「[Visual Studio ソリューションをセットアップする](#SetupVS)」に進んでかまいません。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a>手順 2: Visual Studio ソリューションをセットアップする

1. コンピューターで **Visual Studio** を開きます。
2. **[ファイル]** メニューで、**[新規]**、**[プロジェクト]** の順に選択します。
3. **[新しいプロジェクト]** ダイアログで、**[テンプレート]**、**[Visual C#]**、**[コンソール アプリケーション]** の順に選択し、プロジェクトの名前を指定して、**[OK]** をクリックします。
4. **ソリューション エクスプローラー**で、Visual Studio ソリューションの下にある新しいコンソール アプリケーションを右クリックします。
5. メニューの **[NuGet パッケージの管理...]** をクリックします。
6. **[NuGet パッケージの管理]** ウィンドウの左端のパネルで、**[オンライン]**、**[nuget.org]** の順にクリックします。
7. **[オンライン検索]** 入力ボックスで、**DocumentDB クライアント ライブラリ**を検索します。
8. 結果で **Microsoft Azure DocumentDB クライアント ライブラリ**を探し、**[インストール]** をクリックします。DocumentDB クライアント ライブラリのパッケージ ID は [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB) です。

以上です。 これでセットアップは終了です。いくつかのコードの記述を開始しましょう。このチュートリアルの完成したコード プロジェクトは [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs) にあります。

##<a id="Connect"></a>手順 3: DocumentDB アカウントに接続する

まず、Program.cs ファイルで、C# アプリケーションの先頭に以下の参照を追加します。

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

> [AZURE.IMPORTANT] この NoSQL チュートリアルを完成させるには、上記の依存関係を追加します。

次に、DocumentDB アカウント エンドポイントと、プライマリまたはセカンダリ アクセス キーを保存します。これは、[Azure ポータル](https://portal.azure.com)にあります。

![C# コンソール アプリケーションを作成するために NoSQL チュートリアルで使用される Azure ポータルのスクリーン ショット。アクティブなハブが強調表示され、[DocumentDB アカウント] ブレードで [キー] ボタンが強調表示され、[キー] ブレードで URI 値、プライマリ キー値、およびセカンダリ キーの値が強調表示されている DocumentDB アカウントを示します][keys]

    private const string EndpointUrl = "<your endpoint URI>";
    private const string AuthorizationKey = "<your key>";

**DocumentClient** の新しいインスタンスを作成して、作業の開始デモ アプリケーションを起動します。**GetStartedDemo** という新しい非同期タスクを作成し、新しい **DocumentClient** をインスタンス化します。

	private static async Task GetStartedDemo()
	{
		// Create a new instance of the DocumentClient
		DocumentClient client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
	}

以下のコードのような **Main** メソッドから非同期タスクを呼び出します。

	public static void Main(string[] args)
	{
		try
		{
			GetStartedDemo().Wait();
		}
		catch (Exception e)
		{
			Exception baseException = e.GetBaseException();
			Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
		}
	}

これで、DocumentDB アカウントへの接続方法および **DocumentClient** クラスのインスタンスの作成方法がわかりました。次に、DocumentDB リソースの使用方法について説明します。

## 手順 4: オンライン データベースを作成する
DocumentDB [データベース](documentdb-resources.md#databases)は、**DocumentClient** クラスの [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) メソッドを使用して作成できます。データベースは、コレクションに分割された JSON ドキュメント ストレージの論理上のコンテナーです。**DocumentClient** を作成した後、**GetStartedDemo** メソッドで新しいデータベースを作成します。

	// Check if the database FamilyDB does not exist
	string databaseName = "FamilyDB";
	
	try
	{
		await this.client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(familyName));
	}
	catch (DocumentClientException de)
	{
		// If the database does not exist, create a new database
		if (de.StatusCode == HttpStatusCode.NotFound)
		{
			await this.client.CreateDatabaseAsync(new Database { Id = familyName });
			this.WriteToConsoleAndPromptToContinue("Created {0}", databaseName);
		}
		else
		{
			throw;
		}
	}
	
##<a id="CreateColl"></a>手順 5: コレクションを作成する  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** は、予約済みのスループットで新しいコレクションを作成します。これによって価格に影響があります。詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/documentdb/)を参照してください。

[コレクション](documentdb-resources.md#collections)は、**DocumentClient** クラスの [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) メソッドを使用して作成できます。コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。**GetStartedDemo** メソッドでデータベースを作成してから、**FamilyCollection** という名前の新しいコレクションを作成します。

	string collectionName = "FamilyCollection";
	try
	{
		await this.client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName));
	}
	catch (DocumentClientException de)
	{
		// If the document collection does not exist, create a new collection
		if (de.StatusCode == HttpStatusCode.NotFound)
		{
			// Configure the collection. Optionally, you can configure partitioning and indexing behavior of the collection here.
			DocumentCollection collectionInfo = new DocumentCollection();
			collectionInfo.Id = collectionName;

			// DocumentDB collections can be reserved with throughput specified in request units/second. 1 RU is a normalized request equivalent to the read
			// of a 1KB document.  Here we create a collection with 400 RU/s. 
			await this.client.CreateDocumentCollectionAsync(
				UriFactory.CreateDatabaseUri(databaseName),
				new DocumentCollection { Id = collectionName },
				new RequestOptions { OfferThroughput = 400 });

			this.WriteToConsoleAndPromptToContinue("Created {0}", collectionName);
		}
		else
		{
			throw;
		}
	}

##<a id="CreateDoc"></a>手順 6: JSON ドキュメントを作成する
[ドキュメント](documentdb-resources.md#documents)は、**DocumentClient** クラスの [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) メソッドを使用して作成できます。ドキュメントは、ユーザー定義の (ユーザーが自由に定義できる) JSON コンテンツです。ここで 1 つ以上のドキュメントを挿入できます。データベースに保存するデータが既にある場合には、DocumentDB の[データ移行ツール](documentdb-import-data.md)を使用できます。

まず、この例の DocumentDB 内に格納するオブジェクトの **Family** クラスを作成する必要があります。さらに、**Family** 内で使用するサブクラスとして、**Parent**、**Child**、**Pet**、**Address** を作成します。ドキュメントには、JSON で **id** としてシリアル化される **Id** プロパティが必要であることに注意してください。**GetStartedDemo** の後に次の内部サブクラスを追加することで、これらのクラスを作成します。

	public class Family
	{
		[JsonProperty(PropertyName = "id")]
		public string Id { get; set; }
		public string LastName { get; set; }
		string string District { get; set; }
		public Parent[] Parents { get; set; }
		public Child[] Children { get; set; }
		public Address Address { get; set; }
		public bool IsRegistered { get; set; }
	}
	
	public class Parent
	{
		public string FamilyName { get; set; }
		public string FirstName { get; set; }
	}

	public class Child
	{
		public string FamilyName { get; set; }
		public string FirstName { get; set; }
		public string Gender { get; set; }
		public int Grade { get; set; }
		public Pet[] Pets { get; set; }
	}

	public class Pet
	{
		public string GivenName { get; set; }
	}

	public class Address
	{
		public string State { get; set; }
		public string County { get; set; }
		public string City { get; set; }
	}


次に、**GetStartedDemo** 非同期メソッド内でドキュメントを作成します。まず、次のように **CreateFamilyDocumentIfNotExists** メソッドを作成します。

	private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
	{
		try
		{
			await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
		}
		catch (DocumentClientException de)
		{
			if (de.StatusCode == HttpStatusCode.NotFound)
			{
				await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
				this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
			}
			else
			{
				throw;
			}
		}
	}

さらに、以下のように 2 つのドキュメントを挿入します。1 つは Andersen Family のドキュメント、もう 1 つは Wakefield Family のドキュメントです。

	// Insert a document, here we create a Family object
	Family andersenFamily = new Family
	{
		Id = "Andersen.1",
		LastName = "Andersen",
		Parents = new Parent[] 
		{
			new Parent { FirstName = "Thomas" },
			new Parent { FirstName = "Mary Kay" }
		},
		Children = new Child[] 
		{
			new Child
			{
				FirstName = "Henriette Thaulow",
				Gender = "female",
				Grade = 5,
				Pets = new Pet[] 
				{
					new Pet { GivenName = "Fluffy" }
				}
			}
		},
		District = "WA5",
		Address = new Address { State = "WA", County = "King", City = "Seattle" },
		IsRegistered = true
	};

	await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

	Family wakefieldFamily = new Family
	{
		Id = "Wakefield.7",
		LastName = "Wakefield",
		Parents = new Parent[]
		{
			new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
			new Parent { FamilyName = "Miller", FirstName = "Ben" }
		},
		Children = new Child[]
		{
			new Child
			{
				FamilyName = "Merriam",
				FirstName = "Jesse",
				Gender = "female",
				Grade = 8,
				Pets = new Pet[]
				{
					new Pet { GivenName = "Goofy" },
					new Pet { GivenName = "Shadow" }
				}
			},
			new Child
			{
				FamilyName = "Miller",
				FirstName = "Lisa",
				Gender = "female",
				Grade = 1
			}
		},
		District = "NY23",
		Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
		IsRegistered = false
	};

	await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

DocumentDB アカウントで、次のデータベース、コレクション、およびドキュメントを作成しました。

![C# コンソール アプリケーションを作成するために NoSQL チュートリアルで使用されるアカウント、オンライン データベース、コレクション、およびドキュメントの間の階層関係を示す図](./media/documentdb-get-started/nosql-tutorial-account-database.png)

##<a id="Query"></a>手順 7: DocumentDB リソースをクエリする

DocumentDB では、各コレクションに格納された JSON ドキュメントに対する豊富な[クエリ](documentdb-sql-query.md)をサポートしています。次のサンプル コードは、前の手順で挿入したドキュメントに対して実行できる、さまざまなクエリを示しています。DocumentDB SQL 構文と LINQ の両方が使用されています。これらのクエリを **GetStartedDemo** 非同期メソッドに追加します。

	// Run a simple query via LINQ. DocumentDB indexes all properties, so queries can be completed efficiently and with low latency.
	// Here we find the Andersen family via its LastName
	IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
		UriFactory.CreateDocumentCollectionUri(databaseName, collectionName))
		.Where(f => f.LastName == "Andersen");

	// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
	Console.WriteLine("Running LINQ query...");
	foreach (Family family in familyQuery)
	{
			Console.WriteLine("\tRead {0}", family);
	}

	// Now execute the same query via direct SQL
	IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
		UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
		"SELECT * FROM Family WHERE Family.lastName = 'Andersen'");

	Console.WriteLine("Running direct SQL query...");
	foreach (Family family in familyQuery)
	{
		Console.WriteLine("\tRead {0}", family);
	}

次の図では、作成したコレクションに対して DocumentDB SQL クエリ構文がどのように呼び出されるか、および同じロジックが LINQ クエリにも適用されることを示します。

![C# コンソール アプリケーションを作成するために NoSQL チュートリアルで使用されるクエリの範囲と意味を示す図](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

DocumentDB クエリのスコープは既に 1 つのコレクションに設定されているので、クエリでは [FROM](documentdb-sql-query.md#from-clause) キーワードを省略できます。したがって、"FROM Families f" は "FROM root r" または他の任意の変数名に置き換えることができます。DocumentDB は、Families、root、または任意の変数名が、既定で現在のコレクションを参照しているものと推測します。

##<a id="DeleteDatabase"></a>手順 8: オンライン データベースを削除する

作成したデータベースを削除すると、データベースとすべての子リソース (コレクション、ドキュメントなど) が削除されます。**GetStartedDemo** 非同期メソッドの末尾に次のコード スニペットを追加することで、データベースとドキュメント クライアントを削除できます。

	// Clean up/delete the database
	await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));

##<a id="Run"></a>手順 9. C# コンソール アプリケーションを実行する

これで、アプリケーションを実行する準備が整いました。**Main** メソッドの最後に、次のコード行を追加します。このコードにより、アプリケーションの実行が完了する前にコンソール出力を読み取ることができます。

	Console.ReadLine();

Visual Studio で F5 キーを押して、デバッグ モードでアプリケーションをビルドします。

開始したアプリケーションの出力が表示されます。出力では追加したクエリの結果が表示されます。次の例のようなものになるはずです。

	Created FamilyDB
	Press any key to continue ...
	Created FamilyCollection
	Press any key to continue ...
	Created Family Andersen.1
	Press any key to continue ...
	Created Family Wakefield.7
	Press any key to continue ...
	Running LINQ query...
		Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
	Running direct SQL query...
		Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
	End of demo, press any key to exit.

ご利用ありがとうございます。 この NoSQL チュートリアルを完了し、実用的な C# コンソール アプリケーションを入手しました。

##<a id="GetSolution"></a> 完全な NoSQL チュートリアル ソリューションを入手する
この記事のすべてのサンプルを含む GetStarted ソリューションをビルドするには、以下が必要です。

-   [DocumentDB アカウント][documentdb-create-account]。
-   GitHub で入手可能な [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) ソリューション。

Visual Studio で DocumentDB .NET SDK への参照を復元するには、ソリューション エクスプローラーで **GetStarted** ソリューションを右クリックし、**[NuGet パッケージの復元を有効にする]** をクリックします。次に、「[DocumentDB アカウントへの接続](#Connect)」の説明に従って、App.config ファイルの EndpointUrl と AuthorizationKey の値を更新します。

## 次のステップ

-   さらに複雑な ASP.NET MVC NoSQL チュートリアルが必要ですか。 「[DocumentDB を使用した ASP.NET MVC Web アプリケーションの構築](documentdb-dotnet-application.md)」を参照してください。
-	[DocumentDB アカウントを監視する](documentdb-monitor-accounts.md)方法について学習します。
-	[クエリのプレイ グラウンド](https://www.documentdb.com/sql/demo)でサンプル データセットに対してクエリを実行します。
-	プログラミング モデルの詳細については、[DocumentDB のドキュメントに関するページ](https://azure.microsoft.com/documentation/services/documentdb/)の「開発」セクションを参照してください。

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png

<!---HONumber=AcomDC_0330_2016-->