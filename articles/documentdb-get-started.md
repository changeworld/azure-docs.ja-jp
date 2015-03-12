<properties 
	pageTitle="<DocumentDB .NET SDK の使用 | Azure" 
	description="<Azure DocumentDB アカウントの作成と構成について説明します。また、アカウント内でデータベースやコレクションを作成したり、JSON ドキュメントを保存したりする方法についても説明します。" 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/12/2014" 
	ms.author="mimig"/>

#DocumentDB .NET SDK の使用  

このガイドでは、[**Microsoft Azure DocumentDB (プレビュー)**](https://portal.azure.com/#gallery/Microsoft.DocumentDB)および DocumentDB .NET SDK の使用を開始する方法について説明します。DocumentDB は NoSQL ドキュメント データベースで、多数の API と SDK を利用できます。サンプルは C# コードで記述され、[DocumentDB .NET SDK](http://go.microsoft.com/fwlink/p/?linkid=402989) を使用しています。DocumentDB .NET SDK は NuGet パッケージとしてパッケージ化され、配布されています。 

紹介されているシナリオには、DocumentDB アカウントの作成と構成、データベースの作成、コレクションの作成、およびアカウント内での JSON ドキュメントの格納が含まれています。これらのサンプルはそれぞれ、[GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) で使用可能な完全なソリューションの一部です。[ソリューションをダウンロード]して(#GetSolution) 含まれるサンプル コードを確認したり、この記事でサンプルを確認できます。

##目次

-	[DocumentDB アカウントへの接続](#Connect)
-	[データベースの作成](#CreateDB)
-	[コレクションの作成](#CreateColl)
-	[ドキュメントの作成](#CreateDoc)
-	[DocumentDB リソースのクエリ](#Query)
-	[完全なソリューションの取得](#GetSolution)
-	[次のステップ](#NextSteps)

##<a id="Connect"></a>DocumentDB アカウントへの接続

最初に、DocumentDB アカウントへの接続を確立するために **DocumentClient** クラスの新しいインスタンスを作成します。   C# アプリケーションで次の参照が必要です。  

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
 
**DocumentClient** は、DocumentDB アカウント エンドポイントおよびアカウントに関連付けられたプライマリ/セカンダリ アクセス キーを使用してインスタンス化できます。  

DocumentDB アカウント エンドポイントとキーは、DocumentDB アカウントの [Azure プレビュー管理ポータル](https://portal.azure.com/#gallery/Microsoft.DocumentDB) ブレードから取得できます。 

![][1]
 
>[キー] ブレードから利用可能な DocumentDB アクセス キーは、DocumentDB アカウントおよびそのリソースへの管理アクセスを付与する点に注意してください。DocumentDB では、付与されているアクセス許可に応じて、クライアントがアカウント キーなしで、DocumentDB アカウント内のリソースの読み取り、書き込み、および削除を実行するためのリソース キーの使用もサポートされます。    

次の例のようなコードを使用してクライアントを作成します。  

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";
    
    // Create a new instance of the DocumentClient.
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

**警告:** ソース コード内に資格情報を保存することは絶対に避けてください。これはサンプルであるため、資格情報はあえてソース コード内に記述しています。資格情報の格納方法の詳細については、「[Windows Azure Web Sites: How Application Strings and Connection Strings Work (Windows Azure Web サイト: アプリケーション文字列と接続文字列の動作)](http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)」を参照してください。

これで、DocumentDB アカウントへの接続方法および **DocumentClient** のインスタンスの作成方法がわかりました。次に、DocumentDB リソースの使用方法について説明します。  

##<a id="CreateDB"></a>データベースの作成
DocumentDB .NET SDK を使用して、DocumentClient の **CreateDatabaseAsync** メソッドにより DocumentDB データベースを作成できます。  

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>コレクションの作成  

DocumentDB .NET SDK を使用して、DocumentClient の **CreateDocumentCollectionAsync** メソッドにより DocumentDB コレクションを作成できます。前の手順で作成したデータベースには、さまざまなプロパティがありますが、その 1 つが **CollectionsLink** プロパティです。この情報を使用して、コレクションを作成できます。  

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });
    
##<a id="CreateDoc"></a>ドキュメントの作成	
DocumentDB .NET SDK を使用して、DocumentClient の **CreateDocumentAsync** メソッドにより DocumentDB ドキュメントを作成できます。前の手順で作成したコレクションには、さまざまなプロパティがありますが、その 1 つが **DocumentsLink** プロパティです。この情報を使用して、1 つ以上のドキュメントを挿入できます。この例の目的に基づき、名前、性別、年齢など家族の属性を示す Family クラスを保持していると仮定しています。  

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
 

##<a id="Query"></a>DocumentDB リソースのクエリ
DocumentDB では、各コレクションに格納された JSON ドキュメントに対するリッチ クエリをサポートしています。次のサンプル コードは、前の手順で挿入したドキュメントに対して実行できる、さまざまなクエリを示しています。DocumentDB SQL 構文と LINQ の両方が使用されています。  

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = \"AndersenFamily\"");

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

##<a id="GetSolution"></a>完全なソリューションの取得
この記事のすべてのサンプルを含む GetStarted ソリューションをビルドするには、以下が必要です。

-   [DocumentDB アカウント][documentdb-create-account].
-   GitHub で使用可能な [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) ソリューション 

Visual Studio 2013 で DocumentDB .NET SDK への参照を復元するには、ソリューション エクスプローラーで GetStarted ソリューションを右クリックし、[NuGet パッケージの復元を有効にする] をクリックして参照を復元します。 

##<a id="NextSteps"></a>次のステップ
-	[DocumentDB アカウントを監視する](http://go.microsoft.com/fwlink/p/?LinkId=402378)方法について学習します。
-	プログラミング モデルの詳細については、[DocumentDB ドキュメント ページ](http://go.microsoft.com/fwlink/p/?LinkID=402319)の開発のセクションを参照してください。


[DocumentDB アカウントへの接続]: #Connect
[データベースの作成]: #CreateDB
[コレクションの作成]: #CreateColl
[ドキュメントの作成]: #CreateDoc
[DocumentDB リソースのクエリ]: #Query
[次のステップ]: #NextSteps
[doc-landing-page]: ../documentation/services/documentdb/
[documentdb-create-account]: ../documentdb-create-account/
[documentdb-manage]: ../documentdb-manage/

[1]: ./media/documentdb-get-started/gs1.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
