<properties
	pageTitle="DocumentDB .NET SDK の使用 | Azure"
	description="Azure DocumentDB アカウントを作成して構成する方法について説明します。また、NoSQL ドキュメント データベース アカウント内でデータベースやコレクションを作成したり、JSON ドキュメントを保存したりする方法についても説明します。"
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
	ms.topic="get-started-article" 
	ms.date="05/19/2015"
	ms.author="anhoh"/>

#DocumentDB .NET SDK の使用  

このチュートリアルでは、[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) および [DocumentDB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) の使用を開始する方法について説明します。DocumentDB リソースを作成およびクエリして出力をコンソール ウィンドウに書き込むコンソール アプリケーションを作成します。

DocumentDB は NoSQL ドキュメント データベースで、[多数の API と SDK を利用](https://msdn.microsoft.com/library/dn781482.aspx)できます。この記事のコードは C# で記述されており、DocumentDB .NET SDK を使用しています。DocumentDB .NET SDK は NuGet パッケージとしてパッケージ化され、配布されています。

この記事では以下のシナリオが扱われています。

- DocumentDB アカウントを作成して接続する
- Visual Studio ソリューションに DocumentDB を追加する
- データベースを作成する
- コレクションを作成する
- JSON ドキュメントを作成する
- リソースのクエリを実行する
- データベースを削除する

チュートリアルを最後まで実施する時間はないが、動作するソリューションは手に入れたい場合もあります。 心配はありません。[GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) で完全なソリューションを入手できます。手順については「[完全なソリューションの取得](#GetSolution)」を参照してください。

## 前提条件

この記事の手順を実行する前に、次のソフトウェアがインストールされていることを確認してください。

- アクティブな Azure アカウントアカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。
- [Visual Studio 2013](http://www.visualstudio.com/) Update 4 以降。

## 手順 1: DocumentDB アカウントを作成する

最初に、DocumentDB アカウントを作成します。既にアカウントがある場合は、「[Visual Studio ソリューションをセットアップする](#SetupVS)」に進んでかまいません。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a>手順 2: Visual Studio ソリューションをセットアップする

1. コンピューターで **Visual Studio** を開きます。
2. **[ファイル]** メニューの **[新規作成]** を選択し、**[プロジェクト]** を選択します。
3. **[新しいプロジェクト]** ダイアログで、**[テンプレート]**、**[Visual C#]**、**[コンソール アプリケーション]** の順に選択し、プロジェクトの名前を指定して、**[追加]** をクリックします。
4. **ソリューション エクスプローラー**で、Visual Studio ソリューションの下にある新しいコンソール アプリケーションを右クリックします。
5. メニューの **[NuGet パッケージの管理...]** をクリックします。
6. **[NuGet パッケージの管理]** ウィンドウの左端のパネルで、**[オンライン]**、**[nuget.org]** の順にクリックします。
7. **[オンライン検索]** 入力ボックスで、**DocumentDB クライアント ライブラリ**を検索します。
8. 結果で **Microsoft Azure DocumentDB クライアント ライブラリ**を探し、**[インストール]** をクリックします。DocumentDB クライアント ライブラリのパッケージ ID は [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB) です。

以上です。 これで DocumentDB を使用する準備が整いました。

##<a id="Connect"></a>手順 3: DocumentDB アカウントに接続する

最初に、DocumentDB アカウントへの接続を確立するために [DocumentClient](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.aspx) クラスの新しいインスタンスを作成します。C# アプリケーションの先頭に次の参照が必要です。

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

**DocumentClient** は、DocumentDB アカウント エンドポイントと、アカウントに関連付けられたプライマリまたはセカンダリ アクセス キーを使用してインスタンス化できます。これらのプロパティをクラスに追加します。

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";

クラスに **GetStartedDemo** という名前の新しい非同期タスクを作成します。この新しいタスク内に **DocumentClient** を作成して設定します。

	private static async Task GetStartedDemo()
    {
		// Create a new instance of the DocumentClient.
    	var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
	}

次のようなコードを使用して、Main メソッドから非同期タスクを呼び出します。

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

> [AZURE.WARNING]ソース コード内に資格情報を保存することは絶対に避けてください。このサンプルでは、単純化するために資格情報をあえてソース コード内に記述しています。運用環境で資格情報を格納する方法の詳細については、「[Azure Web Sites: How Application Strings and Connection Strings Work (Azure Web Sites: アプリケーション文字列と接続文字列の動作)](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)」を参照してください。ソース コードの外部に資格情報を格納する例については、[GitHub](https://github.com/Azure/azure-documentdb-net/blob/master/tutorials/get-started/src/Program.cs) のサンプル アプリケーションをご覧ください。

EndpointUrl と AuthorizationKey の値には、DocumentDB アカウントの [[キー]](https://portal.azure.com) ブレードから取得できる DocumentDB アカウントの URI とプライマリ キーを指定します。

![DocumentDB アカウントを示す、アクティブなハブ、[DocumentDB アカウント] ブレードの [キー] ボタン、[キー] ブレードの URI の値、プライマリ キーの値、およびセカンダリ キーの値が強調表示されている Azure プレビュー ポータルのスクリーン ショット][keys]

これらのキーにより、DocumentDB アカウントとそのリソースに対する管理アクセス権が付与されます。DocumentDB では、付与されているアクセス許可に応じて、クライアントがアカウント キーなしで、DocumentDB アカウント内のリソースの読み取り、書き込み、および削除を実行するためのリソース キーの使用もサポートされます。リソース キーの詳細については、「[アクセス許可](documentdb-resources.md#permissions)」および「[アクセス キーを表示、コピー、および再生成する](documentdb-manage-account.md#keys)」を参照してください。

これで、DocumentDB アカウントへの接続方法および **DocumentClient** クラスのインスタンスの作成方法がわかりました。次に、DocumentDB リソースの使用方法について説明します。

## 手順 4: データベースを作成する
[データベース](documentdb-resources.md#databases)は、**DocumentClient** クラスの [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) メソッドを使用して作成できます。データベースは、コレクションに分割されたドキュメント ストレージの論理上のコンテナーです。**DocumentClient** を作成した後、**GetStartedDemo** メソッドで新しいデータベースを作成します。

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>手順 5: コレクションを作成する  

> [AZURE.WARNING]**CreateDocumentCollectionAsync** は新しい S1 コレクションを作成します。これによって価格に影響があります。詳細については、[料金のページ](https://azure.microsoft.com/pricing/details/documentdb/)を参照してください。

[コレクション](documentdb-resources.md#collections)は、**DocumentClient** クラスの [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) メソッドを使用して作成できます。コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。新しく作成されたコレクションは、[S1 パフォーマンス レベル](documentdb-performance-levels.md)にマップされます。前の手順で作成したデータベースには、さまざまなプロパティがありますが、その 1 つが [CollectionsLink](https://msdn.microsoft.com/library/microsoft.azure.documents.database.collectionslink.aspx) プロパティです。この情報を使用して、データベースの作成後にコレクションを作成できます。

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });

##<a id="CreateDoc"></a>手順 6: ドキュメントを作成する
[ドキュメント](documentdb-resources.md#documents)は、**DocumentClient** クラスの [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) メソッドを使用して作成できます。ドキュメントは、ユーザー定義の (ユーザーが自由に定義できる) JSON コンテンツです。前の手順で作成したコレクションには、さまざまなプロパティがありますが、その 1 つが [DocumentsLink](https://msdn.microsoft.com/library/microsoft.azure.documents.documentcollection.documentslink.aspx) プロパティです。この情報を使用して、1 つ以上のドキュメントを挿入できます。データベースに保存するデータが既にある場合には、DocumentDB の[データ移行ツール](documentdb-import-data.md)を使用できます。

最初に、**Parent**、**Child**、**Pet**、**Address**、**Family** の各クラスを作成する必要があります。次の内部サブクラスを追加することで、これらのクラスを作成します。

    internal sealed class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    internal sealed class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    internal sealed class Pet
    {
        public string GivenName { get; set; }
    }

    internal sealed class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }

    internal sealed class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
    }

次に、**GetStartedDemo** 非同期メソッド内でドキュメントを作成します。

    // Create the Andersen family document.
	Family AndersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents =  new Parent[] {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay"}
        },
        Children = new Child[] {
            new Child {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[] {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
    };

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, AndersenFamily);

    // Create the WakeField family document.
    Family WakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
        Parents = new Parent[] {
            new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
            new Parent { FamilyName= "Miller", FirstName= "Ben" }
        },
        Children = new Child[] {
            new Child {
                FamilyName= "Merriam",
                FirstName= "Jesse",
                Gender= "female",
                Grade= 8,
                Pets= new Pet[] {
                    new Pet { GivenName= "Goofy" },
                    new Pet { GivenName= "Shadow" }
                }
            },
            new Child {
                FamilyName= "Miller",
                FirstName= "Lisa",
                Gender= "female",
                Grade= 1
            }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
    };

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, WakefieldFamily);

DocumentDB アカウントで、次のデータベース、コレクション、およびドキュメントを作成しました。

![アカウント、データベース、コレクション、およびドキュメントの間の階層関係を示す図](./media/documentdb-get-started/account-database.png)

##<a id="Query"></a>手順 7: DocumentDB リソースをクエリする

DocumentDB では、各コレクションに格納された JSON ドキュメントに対する豊富な[クエリ](documentdb-sql-query.md)をサポートしています。次のサンプル コードは、前の手順で挿入したドキュメントに対して実行できる、さまざまなクエリを示しています。DocumentDB SQL 構文と LINQ の両方が使用されています。これらのクエリを **GetStartedDemo** 非同期メソッドに追加します。

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = "AndersenFamily"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery(documentCollection.DocumentsLink)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery(documentCollection.DocumentsLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    // Query the documents using DocumentSQL with one join.
    var items = client.CreateDocumentQuery<dynamic>(documentCollection.DocumentsLink,
        "SELECT f.id, c.FirstName AS child " +
        "FROM Families f " +
        "JOIN c IN f.Children");

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

    // Query the documents using LINQ with one join.
    items = client.CreateDocumentQuery<Family>(documentCollection.DocumentsLink)
        .SelectMany(family => family.Children
            .Select(children => new
            {
                family = family.Id,
                child = children.FirstName
            }));

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

次の図では、作成したコレクションに対して DocumentDB SQL クエリ構文がどのように呼び出されるか、および同じロジックが LINQ クエリにも適用されることを示します。

![クエリのスコープおよび意味を示す図](./media/documentdb-get-started/collection-documents.png)

DocumentDB クエリのスコープは既に 1 つのコレクションに設定されているので、クエリでは [FROM](documentdb-sql-query.md/#from-clause) キーワードを省略できます。したがって、"FROM Families f" は "FROM root r" または他の任意の変数名に置き換えることができます。DocumentDB は、Families、root、またはその他の変数名が、既定で現在のコレクションを参照しているものと推測します。

##<a id="DeleteDatabase"></a>手順 8: データベースを削除する

作成したデータベースを削除すると、データベースとすべての子リソース (コレクション、ドキュメントなど) が削除されます。**GetStartedDemo** 非同期メソッドの末尾に次のコード スニペットを追加することで、データベースとドキュメント クライアントを削除できます。

    // Clean up/delete the database
    await client.DeleteDatabaseAsync(database.SelfLink);
	client.Dispose();

##<a id="Run"></a>手順 9: アプリケーションを実行する

これで、アプリケーションを実行する準備が整いました。**Main** メソッドの最後に、次のコード行を追加します。このコードにより、アプリケーションの実行が完了する前にコンソール出力を読み取ることができます。

	Console.ReadLine();

Visual Studio で F5 キーを押して、デバッグ モードでアプリケーションをビルドします。

開始したアプリケーションの出力が表示されます。出力では追加したクエリの結果が表示されます。次の例のようなものになるはずです。

	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
    	{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from SQL
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ query
	{
	  "id": "AndersenFamily",
 	  "child": "Henriette Thaulow"
	}
	{
	  "id": "WakefieldFamily",
	  "child": "Jesse"
	}
	{
	  "id": "WakefieldFamily",
	  "child": "Lisa"
	}
	{ family = AndersenFamily, child = Henriette Thaulow }
	{ family = WakefieldFamily, child = Jesse }
	{ family = WakefieldFamily, child = Lisa }


> [AZURE.NOTE]データベースを削除せずにアプリケーションを複数回実行すると、既に使用されている ID を指定して新しいデータベースを作成することによる問題が発生する場合があります。これを回避するには、同じ ID を持つデータベース、コレクション、ドキュメントが既に存在するかどうかを確認します。その方法については、[GitHub のページ](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started)を参照してください。

##<a id="GetSolution"></a>完全なソリューションを取得する
この記事のすべてのサンプルを含む GetStarted ソリューションをビルドするには、以下が必要です。

-   [DocumentDB アカウント][documentdb-create-account]。
-   GitHub で入手可能な [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) ソリューション。

Visual Studio 2013 で DocumentDB .NET SDK への参照を復元するには、ソリューション エクスプローラーで **GetStarted** ソリューションを右クリックし、**[NuGet パッケージの復元を有効にする]** をクリックします。次に、「[DocumentDB アカウントへの接続](#Connect)」の説明に従って、App.config ファイルの EndpointUrl と AuthorizationKey の値を更新します。

## 次のステップ
-   さらに複雑な ASP.NET MVC のサンプルが必要ですか。 「[DocumentDB を使用した ASP.NET MVC Web アプリケーションの構築](documentdb-dotnet-application.md)」を参照してください。
-	[DocumentDB アカウントを監視する](documentdb-monitor-accounts.md)方法について学習します。
-	[クエリのプレイ グラウンド](https://www.documentdb.com/sql/demo)でサンプル データセットに対してクエリを実行します。
-	プログラミング モデルの詳細については、[DocumentDB ドキュメント ページ](../../services/documentdb/)の「開発」セクションを参照してください。

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-get-started/keys.png
 

<!---HONumber=July15_HO3-->