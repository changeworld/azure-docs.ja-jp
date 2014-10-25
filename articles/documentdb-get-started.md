<properties title="Get started with a DocumentDB account" pageTitle="Get started with a DocumentDB account | Azure" description="Learn how to create and configure an Azure DocumentDB account, create databases, create collections, and store JSON documents within the account." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="paulettm" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# DocumentDB アカウントの使用

このガイドでは、**Azure DocumentDB (プレビュー)** の使用を開始する方法について説明します。サンプルは C\# コードで記述され、DocumentDB .NET SDK を使用しています。紹介されているシナリオには、DocumentDB アカウントの作成と構成、データベースの作成、コレクションの作成、およびアカウント内での JSON ドキュメントの格納が含まれています。Azure DocumentDB の使用の詳細については、「次のステップ」のセクションを参照してください。

この概要ガイドを使用するには、DocumentDB アカウントとアカウントのアクセス キー (プライマリまたはセカンダリ) を持っている必要があります。詳細については、次を参照してください。

-   [DocumentDB アカウントの作成方法][DocumentDB アカウントの作成方法]

## 目次

-   [DocumentDB アカウントへの接続][DocumentDB アカウントへの接続]
-   [データベースの作成][データベースの作成]
-   [コレクションの作成][コレクションの作成]
-   [ドキュメントの作成][ドキュメントの作成]
-   [DocumentDB リソースのクエリ][DocumentDB リソースのクエリ]
-   [次のステップ][次のステップ]

## <span id="Connect"></span></a>DocumentDB アカウントへの接続

プログラムによって DocumentDB を利用するためのさまざまな SDK および API があります。次のサンプルは C\# コードで記述され、DocumentDB .NET SDK を使用しています。

最初に、DocumentDB アカウントへの接続を確立するために DocumentClient を作成します。C\# アプリケーションで次の参照が必要です。

    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Microsoft.Azure.Documents;  

DocumentClient は、DocumentDB アカウント エンドポイントおよびアカウントに関連付けられたプライマリ/セカンダリ アクセス キーを使用してインスタンス化できます。

DocumentDB アカウント エンドポイントとキーは、DocumentDB アカウントの Azure 管理プレビュー ポータル ブレードから取得できます。

![][]

> [キー] ブレードから利用可能な DocumentDB アクセス キーは、DocumentDB アカウントおよびそのリソースへの管理アクセスを付与する点に注意してください。DocumentDB では、付与されているアクセス許可に応じて、クライアントがアカウント キーなしで、DocumentDB アカウント内のリソースの読み取り、書き込み、および削除を実行するためのリソース キーの使用もサポートされます。

次の例のようなコードを使用してクライアントを作成します。

    private static string endpointUrl = "<your endpoint URI>";
    private static string authorizationKey = "<your key>";

    //Create a new instance of the DocumentClient
    var client = new DocumentClient(new Uri(endpointUrl), authorizationKey);  

**警告:** ソース コード内に資格情報を保存することは絶対に避けてください。このサンプルでは、単純化するためにあえてソース コード内に記述しています。資格情報の格納方法の詳細については、「[Windows Azure Web Sites: How Application Strings and Connection Strings Work (Windows Azure Web サイト: アプリケーション文字列と接続文字列の動作)][Windows Azure Web Sites: How Application Strings and Connection Strings Work (Windows Azure Web サイト: アプリケーション文字列と接続文字列の動作)]」を参照してください。

これで、DocumentDB アカウントへの接続方法および DocumentClient のインスタンスの作成方法がわかりました。次に、DocumentDB リソースの使用方法について説明します。

## <span id="CreateDB"></span></a>データベースの作成

.NET SDK を使用して、DocumentClient の CreateDatabaseAsync メソッドにより DocumentDB データベースを作成できます。

    //Create a Database
     Database database = await client.CreateDatabaseAsync(
        new Database
        {
        Id = "FamilyRegistry"
        });

## <span id="CreateColl"></span></a>コレクションの作成

.NET SDK を使用して、DocumentClient の CreateDocumentCollectionAsync メソッドにより DocumentDB コレクションを作成できます。前の手順で作成したデータベースには、さまざまなプロパティがありますが、その 1 つが SelfLink プロパティです。この情報を使用して、コレクションを作成できます。

        //Create a document collection 
    documentCollection = new DocumentCollection
        {
            Id = "FamilyCollection"
        };

        documentCollection = await client.CreateDocumentCollectionAsync(database.SelfLink,documentCollection); 

## <span id="CreateDoc"></span></a>ドキュメントの作成

.NET SDK を使用して、DocumentClient の CreateDocumentAsync メソッドにより DocumentDB ドキュメントを作成できます。前の手順で作成したコレクションには、さまざまなプロパティがありますが、その 1 つが DocumentsLink プロパティです。この情報を使用して、1 つ以上のドキュメントを挿入できます。この例の目的に基づき、名前、性別、年齢など家族の属性を示す Family クラスを保持していると仮定しています。

    private static async Task CreateDocuments(string    colSelfLink)
    {
        Family AndersonFamily = new Family
        {
            Id = "AndersenFamily",
            LastName = "Andersen",
            Parents =  new Parent[] {
                new Parent { FirstName = "Thomas" },
                new Parent { FirstName = "Mary Kay"}
            },
            Children = new Child[] {
                new Child
                { 
                    FirstName = "Henriette Thaulow", 
                    Gender = "female", 
                    Grade = 5, 
                    Pets = new [] {
                        new Pet { GivenName = "Fluffy" } 
                    }
                } 
            },
            Address = new Address { State = "WA", County = "King", City = "Seattle" },
            IsRegistered = true
        };

        await client.CreateDocumentAsync(colSelfLink, AndersonFamily);

        Family WakefieldFamily = new Family
        {
            Id = "WakefieldFamily",
            Parents = new [] {
                new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
                new Parent { FamilyName= "Miller", FirstName= "Ben" }
            },
            Children = new Child[] {
                new Child
                {
                    FamilyName= "Merriam", 
                    FirstName= "Jesse", 
                    Gender= "female", 
                    Grade= 8,
                    Pets= new Pet[] {
                        new Pet { GivenName= "Goofy" },
                        new Pet { GivenName= "Shadow" }
                    }
                },
                new Child
                {
                    FamilyName= "Miller", 
                    FirstName= "Lisa", 
                    Gender= "female", 
                    Grade= 1
                }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
        };

        await client.CreateDocumentAsync(colSelfLink, WakefieldFamily);

    }

## <span id="Query"></span></a>DocumentDB リソースのクエリ

DocumentDB では、各コレクションに格納された JSON ドキュメントに対するリッチ クエリをサポートしています。次のサンプル コードは、前の手順で挿入したドキュメントに対して実行できる、さまざまなクエリを示しています。DocumentDB SQL 構文と LINQ の両方が使用されています。

    //
    //Querying the documents using DocumentDB SQL for the Andersen family
    //
    foreach (var family in client.CreateDocumentQuery(collectionLink, 
    "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
    Console.WriteLine("\tRead {0} from SQL", family);
    }

    //
    //Querying the documents using LINQ for the Andersen family
    //
    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
     Console.WriteLine("\tRead {0} from LINQ", family);
    }

    //
    //Querying the documents using LINQ lambdas for the Andersen family
    //
    foreach (var family in client.CreateDocumentQuery(collectionLink)
    .Where(f => f.Id == "AndersenFamily")
    .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    //
    //DocumentDB SQL -  using <> interchangably with != for "not equals"
    //
    families = client.CreateDocumentQuery<Family>(colSelfLink, "SELECT * FROM Families f WHERE f.id <> 'AndersenFamily'");

    //   
    // LINQ - combine equality and inequality
    //
    families = from f in client.CreateDocumentQuery<Family>(colSelfLink)
           where f.Id == "Wakefield" && f.Address.City != "NY"
           select f; 

## <span id="NextSteps"></span></a>次のステップ

-   [DocumentDB アカウントを監視する][DocumentDB アカウントを監視する]方法について学習します。
-   プログラミング モデルの詳細については、[DocumentDB ドキュメント ページ][DocumentDB ドキュメント ページ]の開発のセクションを参照してください。

  [DocumentDB アカウントの作成方法]: ../documentdb-create-account/
  [DocumentDB アカウントへの接続]: #Connect
  [データベースの作成]: #CreateDB
  [コレクションの作成]: #CreateColl
  [ドキュメントの作成]: #CreateDoc
  [DocumentDB リソースのクエリ]: #Query
  [次のステップ]: #NextSteps
  []: ./media/documentdb-get-started/gs1.png
  [Windows Azure Web Sites: How Application Strings and Connection Strings Work (Windows Azure Web サイト: アプリケーション文字列と接続文字列の動作)]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [DocumentDB アカウントを監視する]: http://go.microsoft.com/fwlink/p/?LinkId=402378
  [DocumentDB ドキュメント ページ]: http://go.microsoft.com/fwlink/p/?LinkID=402319
