<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="DocumentDB を使用した ASP.NET MVC Web アプリケーションの構築 | Azure" description="DocumentDB を使用して To Do List Web アプリケーションを作成する方法について説明します。データは Azure にホストされた ASP.NET MVC Web アプリケーションに格納してアクセスします。"  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />


<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="ryancraw" />

<a name="_Toc395809351">Build a web application with ASP.NET MVC using DocumentDB</a>
=======================================================================================================
<a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Overview</a>
==============================================================================================

<a name="_Toc395637759">Scenario</a>
------------------------------------

To highlight how customers can efficiently leverage Azure DocumentDB to store and query JSON documents, this document provides an end-to-end walk-through of building a ToDo List web application using Azure Document DB.

This walk-through shows you how to use DocumentDB service provided by Azure to store and access data from an ASP.NET MVC web application hosted on Azure and presumes that you have some prior experience using ASP.NET MVC and Azure Websites.

You will learn:

1\. Creating and provisioning a DocumentDB Account

2\. Creating a ASP.NET MVC Application

3\. Connecting to and using Azure DocumentDB from your web application

4\. Deploying the Web Application to Azure Websites

By following this walk-through, you will build a simple web-based task-management application that allows creating, retrieving and completing of tasks. The tasks will be stored as JSON documents in Azure DocumentDB.

![Alt text](./media/documentdb-dotnet-application/image1.png)


<a name="_Toc395637760">Prerequisites</a>
================================================================

Before following the instructions in this article, you should ensure that you have the following installed:

Git for Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (or [Visual Studio Express][] which is the free version)

Azure SDK for .NET version 2.3 or higher, available through [Microsoft Web Platform Installer][]

All the screen shots in this document have been taken using Visual Studio 2013 with Update 3 applied and Azure SDK for .NET version 2.4. If your system is configured with different versions it is possible that your screens and options won't match entirely, but if you meet the above prerequisites this solution should work.

<a name="_Toc395637761">Create a DocumentDB database account</a>
================================================================

To provision a DocumentDB database account in Azure, open the Azure Management Portal and either Click the Azure Gallery tile on the homepage or click "+" in the lower left hand corner of the screen.

![Alt text](./media/documentdb-dotnet-application/image2.png)


This will open the Azure Gallery, where you can select from the many available Azure services. In the Gallery, select "Data, storage and backup" from the list of categories.

![Alt text](./media/documentdb-dotnet-application/image3.png)

From here, select the option for Azure DocumentDB

![Alt text](./media/documentdb-dotnet-application/image4.png)


Then select "Create" from the bottom of the screen

![Alt text](./media/documentdb-dotnet-application/image5.png)

This will open up the "New DocumentDB" blade where you can specify the name, region, scale, resource group and other settings for your new account.

![Alt text](./media/documentdb-dotnet-application/image6.png)


Once you're done supplying the values for your account, click "Create" and the provisioning process will begin creating your database account.
When the provisioning process is complete, you should see a notification appear in the notifications area of the portal and the tile on your start screen (if you selected to create one) will change to show the completed action.

![Alt text](./media/documentdb-dotnet-application/image7.png)


Once provisioning is complete, clicking the DocumentDB tile from the start screen will bring up the main blade for this newly created DocumentDB account.

![Alt text](./media/documentdb-dotnet-application/image8.png) 
![Alt text](./media/documentdb-dotnet-application/image9.png)


Using the "Keys" button, access your endpoint URL and the Primary Key, copy these to your clipboard and keep them handy as we will use these values in the web application we will use these values in the web application we will create next.

We will now walk through how to create a new ASP.NET MVC application from the ground-up. For your reference the complete solution can be downloaded [here].

<a name="_Toc395637762">Create a new ASP.NET MVC application</a>
================================================================

In Visual Studio, click File - New Project and select the option to create a new ASP.NET MVC Web Application.

![Alt text](./media/documentdb-dotnet-application/image10.png)


Select where you would like to create the project, what you want the project to be called (I'm using "todo") and click Ok.

![Alt text](./media/documentdb-dotnet-application/image11.png)


If you plan on hosting your application in Azure then select the box on the lower right to "Host in the cloud". We've selected to host in the cloud, and run the application hosted in an Azure Website. Selecting this option will pre-provision an Azure Website for you and make life a lot easier when it comes times to deploy the final working application. If you want to host this elsewhere or don't want to configure Azure upfront, then just un-check the "Host in the Cloud" option.

Select OK and let Visual Studio do its thing around scaffolding the empty ASP.NET MVC template. If you chose to host this in the Cloud you will see at least one additional screen asking you to login to your Azure account and provide some values for your new Website. Proceed to supply all these Azure values and continue. 

I haven't chosen a "Database server" here because we're not using an Azure SQL Database Server here, we're going to be creating a new Azure DocumentDB Account later on in the management portal. 

![Alt text](./media/documentdb-dotnet-application/image11_1.png)

Once Visual Studio has finished creating the boilerplate MVC application you have an empty ASP.NET application that you can run locally.

We'll skip running locally because I'm sure we've all seen the ASP.NET "Hello World" application. Let's go straight to adding DocumentDB to this project and building our application.

</h1>
<a name="_Toc395637767">Adding DocumentDB to your project</a>
=============================================================

That takes care of most of the ASP.NET MVC plumbing that we need for
this solution. Now let's get to the real purpose of this tutorial, adding Azure DocumentDB to our web application.

### 

### <a name="_Toc395637764">Installing the NuGet package</a>

The DocumentDB .NET SDK is packaged and distributed as a NuGet package. Using the NuGet package manager in Visual Studio (which you can get to by Right-Clicking on the Project and choosing "Manage NuGet Packages"

![Alt text](./media/documentdb-dotnet-application/image21.png)

Search for Online for "Azure DocumentDB" and install the package. This will download and install the DocumentDB package as well as all dependencies, like Newtonsoft.Json.

**NOTE:** While the service is still in preview, the NuGet package is marked as "Prerelease" so you need to include the option to "Include Prerelease", else you will not find the package. 

Alternatively you could use the Package Command Console to install the package by typing;

    Install-Package Microsoft.Azure.Documents.Client -Pre

Once installed your Visual Studio solution should resemble the following with two new references added;

![Alt text](./media/documentdb-dotnet-application/image22.png)


<a name="_Toc395637763">Setting up the ASP.NET MVC application</a>
==================================================================

### 

### <a name="_Toc395637764">Add a Model</a>

Let's begin by creating the **M** in MVC, the model. In Solution Explorer, right-click the *Models* folder and then click **Add**, then **Class**

![Alt text](./media/documentdb-dotnet-application/image12.png)

Name your new Class, **Item** and then add the following code in to this new Class

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
		
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
		
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
		
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

All data in DocumentDB is passed over the wire, and stored, as JSON. To control the way your objects are serialized/deserialized by JSON.NET you can use the JsonProperty attribute as demonstrated in the Item class we just created. You don't **have** to do this but i wanted to ensure that my properties followed the JSON camelCase naming conventions. 

Not only can you control the format of the property name when it goes in to JSON, but you can entirely rename your .NET properties like I did with the Description property. 

You can, if you like, use JsonConverter objects here as well to completely control how serialization is handled.  

In order to get Visual Studio to resolve the "JsonProperty" attribute used here you need to add the following using statement to the using section of your class file;

    using Newtonsoft.Json;

### <a name="_Toc395637765">Add a Controller</a>

That takes care of the M, now let's create the **C** in MVC, a controller class.
In **Solution Explorer**, right-click the *Controllers* folder and then click **Add**, then **Controller**.

![Alt text](./media/documentdb-dotnet-application/image13.png)

![Alt text](./media/documentdb-dotnet-application/image14.png)


In the **Add Scaffold** dialog box, click **MVC 5 Controller - Empty.** Click **Add.**

![Alt text](./media/documentdb-dotnet-application/image15.png)

Name your new Controller, **ItemController.**

Visual Studio will now add the ItemController your Solution Explorer should look like similar to below.

![Alt text](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766">Add Views</a>

And finally, let's create the **V** in MVC, a view.


#### Add Item Index View

Expand the ***Views***  folder in Solution Explorer and location the (empty) Item folder which Visual Studio would've created for you when you added the *ItemController* earlier. Right click on ***Item*** and choose to Add a new View.

![Alt text](./media/documentdb-dotnet-application/image17.png)

In the "Add View" dialog. Call your view "***Index***", use the ***List*** Template, select the ***Item (todo.Models)*** which we created earlier as the class and finally use the ***~Views/Shared/_Layout.cshtml*** in the Solution as the Layout page.

![Alt text](./media/documentdb-dotnet-application/image18.png)


Once all these values are set, click Add and let Visual Studio create your view for you. Visual Studio will create a template view. Once it is done, it will open the cshtml file created. We can close this document in Visual Studio as we will come back to it later.

#### Add New Item View

In a similar fashion to above, create a new View for creating new Items as per the example shown below;

![Alt text](./media/documentdb-dotnet-application/image19.png)

#### Add Edit Item View

<a name="_Toc395888515"></a>
============================

And finally, add one last View for editing an Item in the same way as before;

![Alt text](./media/documentdb-dotnet-application/image20.png)


Once this is done, close the cshtml documents in Visual Studio as we will return to these Views later.

</h1>
<a name="_Toc395637769">Wiring up DocumentDB</a>
------------------------------------------------

### <a name="_Toc395637770">Listing Incomplete Items</a>

Open the **ItemController** and remove all the code within the class (but leave the class) that Visual Studio added. We'll rebuild it piece by piece using DocumentDB.

Add the following code snippet within the now empty ItemController class;

    public ActionResult Index()
    {
        var items = DocumentDBRepository.GetIncompleteItems();
        return View(items);
    }

This code also uses a "pseudo repository" class called DocumentDBRepository which we have yet to create. This is actually just a Helper class that contains all the DocumentDB specific code. For the purposes of this walk-through we aren't going to implement a full data access layer with dependency injection, and factories and repository patterns, as you would probably be doing if you were building a real world application. 
For the purposes of this walk-through we're just going to put all the data access logic directly in to one project to keep things simple and focus on the DocumentDB specific bits.

Add a new Class to your project and call it **DocumentDBRepository.**
Replace the code in the class file with the following;

    public static class DocumentDBRepository
    {
        private static string databaseId;
        private static String DatabaseId
        {
			get
			{
				if (string.IsNullOrEmpty(databaseId))
				{
					databaseId = ConfigurationManager.AppSettings["database"];
				}
			
				return databaseId;
			}
        }
			  
        private static string collectionId;
        private static String CollectionId
        {
			get
			{
				if (string.IsNullOrEmpty(collectionId))
				{
					collectionId = ConfigurationManager.AppSettings["collection"];
				}
			
				return collectionId;
			}
        }
				
        private static Database database;
        private static Database Database
        {
			get
			{
				if (database == null)
				{
					database = ReadOrCreateDatabase();
				}
				
				return database;
			}
        }
		
        private static DocumentCollection collection;
        private static DocumentCollection Collection
        {
			get
			{
				if (collection == null)
				{
					collection = ReadOrCreateCollection(Database.SelfLink);
				}
				
				return collection;
			}
        }
		
        private static DocumentClient client;
        private static DocumentClient Client
        {
            get
            {
                if (client == null)
                {
					string endpoint = ConfigurationManager.AppSettings["endpoint"];
					string authKey = ConfigurationManager.AppSettings["authKey"];
					Uri endpointUri = new Uri(endpoint);
					client = new DocumentClient(endpointUri, authKey);
                }
                
                return client;
            }
        }
    } 

#### 

Spend some time resolving all the namespaces in Visual Studio. All namespaces will be easy to resolve in Visual Studio as long as the NuGet package was installed successfully. The references to the ReadOrCreateDatabase and ReadOrCreateCollection methods will remain unresolved until we have added them, which comes next. 

There are two method calls used here for reading or creating DocumentDB Databases and Document Collections.
So add the following two methods to this class;

    private static DocumentCollection ReadOrCreateCollection(string databaseLink)
    {
        var col = Client.CreateDocumentCollectionQuery(databaseLink)
                          .Where(c => c.Id == CollectionId)
                          .AsEnumerable()
                          .FirstOrDefault();

        if (col == null)
        {
            col = Client.CreateDocumentCollectionAsync(databaseLink, new DocumentCollection { Id = CollectionId }).Result;
        }
		
        return col;
    }

    private static Database ReadOrCreateDatabase()
    {
        var db = Client.CreateDatabaseQuery()
                        .Where(d => d.Id == DatabaseId)
                        .AsEnumerable()
                        .FirstOrDefault();
		
        if (db == null)
        {
            db = Client.CreateDatabaseAsync(new Database { Id = DatabaseId }).Result;
        }
		
        return db;
    }

</h1>

#### 

This takes care setting up the Database, a DocumentCollection, and creating some code to connect to DocumentDB through the DocumentClient. 

We're reading some values from configuration, so open the **web.config** and add the following lines under the
<AppSettings\> section;

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>


</h4>

ここで、作業を実行するコードをいくつか追加してみましょう。 
ToDo リスト アプリケーションを使用して、最初に未完了の項目を表示します。次のメソッドにより、扱っているリポジトリ クラス内の任意の場所にコピーして貼り付けることができます。
    
    public static List<Item> GetIncompleteItems()
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => !d.Completed)
                .AsEnumerable()
                .ToList<Item>();
    }
    
*CreateDocumentQuery* の参照を解決するためには、次の using ディレクティブを手動で追加する必要があります。

    using Microsoft.Azure.Documents.Linq;

この時点で、エラーなくソリューションをビルドすることは可能です。

ここで仮にアプリケーションを実行した場合は、Home コントローラーとその Index ビューが表示されます。これは、作業の開始時に選択した MVC テンプレート プロジェクトの既定の動作であって、期待していた動作ではありません。この MVC アプリケーションのルーティングに手を加えて、この動作を変更しましょう。

***App\_Start\RouteConfig.cs*** を開き、"defaults:" で始まる行を見つけて、次のように変更します。

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

ここでは、ルーティングの動作を制御するための値を URL に指定していない場合、"Home" の代わりに "Item" をコント ローラーとしてユーザー インデックスをビューとして使用するように ASP.NET MVC に指示しています。
アプリケーションを実行すると、**ItemController** を呼び出し、**GetIncompleteItems** メソッドの結果を Views\Item\Index ビューに返します。 

このプロジェクトをビルドして実行すると、次のように表示されます。    

![Alt text](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771">項目の追加</a>

空のグリッドでは物足りないので、データベースにいくつか項目を追加してみましょう。

新しい項目を作成するためのビューは作成済みです。また、その作成画面にユーザーを誘導するためのボタンも既にインデックス ビューに存在します。そこで、DocumentDB にレコードを保存するためのコードを Controller と Repository に追加しましょう。

***ItemController.cs*** を開いて次のコード スニペットを追加します。このコードによって、Create アクションに対応する処理が ASP.NET MVC に伝えられます。このケースでは、関連する Create.cshtml ビュー (先ほど作成したもの) をレンダリングする処理が該当します。

    public ActionResult Create()
    { 
	return View(); 
    }

このコントローラーには、Create ビューから送信された内容を受け取るためのコードを追加する必要があります。

次のコード ブロックを追加してください。フォームの POST に対し、このコントローラーで行う処理を ASP.NET MVC に伝えます。
	
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Create([Bind(Include = "Id,Name,Description,Completed")] Item item)  
    {
		if (ModelState.IsValid)  
		{  
		    await repo.CreateDocument(item);  
		    return RedirectToAction("Index");  
		}   
    	return View(item);   
    }

**セキュリティ上の注意**: [ValidateAntiForgeryToken] 属性は、クロスサイト リクエスト フォージェリ攻撃に対してこのアプリケーションを保護するためにここで使用されます。この属性を追加するだけでなく、偽造防止トークンもビューで処理する必要があります。この詳細と正しい実装方法については、[クロスサイト リクエスト フォージェリの防止に関するページ][]を参照してください。この記事の最後にあるダウンロードのリンクのソース コードから実装できます。

**セキュリティ上の注意**: メソッド パラメーターの [Bind] 属性も使用して、オーバーポスティング攻撃から保護します。詳細については、[ASP.NET MVC での基本的な CRUD 操作][]を参照してください。

これで実装されました。Items Controller は、次のメソッドを DocumentDBRepository クラスに追加するために、フォームからリポジトリ クラスの CreateDocument メソッドに Item を  (安全に) 渡します。

    public static async Task<Document> CreateItemAsync(Item item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item);
    }

このメソッドは単に、渡されたオブジェクトを受け取って、DocumentDB に保存します。
データベースに新しい項目を追加するために必要なコードは以上です。


### <a name="_Toc395637772">項目の編集</a>

最後に、データベース内の項目を編集する機能と、タスクが完了したときにそれらを完了済みとしてマークするための機能を実装します。項目の追加機能と同様、編集のためのビューは既に追加してあります。後は、コントローラーと DocumentDBRepository クラスに少しのコードを追加するだけです。

ItemController クラスに次のコードを追加します。

    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
    {
        if (ModelState.IsValid)
        {
            await DocumentDBRepository.UpdateItemAsync(item);
            return RedirectToAction("Index");
        }

        return View(item);
    }
	
    public ActionResult Edit(string id)
    {
        if (id == null)
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
        }

        Item item = (Item)DocumentDBRepository.GetItem(id);
        if (item == null)
        {
            return HttpNotFound();
        }

        return View(item);
    }

1 つ目のメソッドは、ユーザーが [Index] ビューから [Edit] リンクをクリックしたときに引き起こされる Http Get を処理するものです。このメソッドによって、DocumentDB から Document が取得されて、[Edit] ビューに渡されます。

[Edit] ビューは、IndexController に Http Post を行います。 
追加した 2 つ目のメソッドは、更新されたオブジェクトをデータベースに保存するために、DocumentDB への引き渡しを処理します。

DocumentDBRepository クラスに次のコードを追加してください。

    public static Item GetItem(string id)
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => d.Id == id)
                    .AsEnumerable()
                    .FirstOrDefault();
    }
	
    public static async Task<Document> UpdateItemAsync(Item item)
    {
        Document doc = Client.CreateDocumentQuery(Collection.DocumentsLink)
                            .Where(d => d.Id == item.Id)
                            .AsEnumerable()
                            .FirstOrDefault();

        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

上の 2 つのメソッドのうち 1 つ目は、DocumentDB から Item を取得するものです。この Item が ItemController に戻された後、[Edit] ビューに渡されます。

2 つ目のメソッドは、DocumentDB 内のドキュメントを、ItemController から渡されたドキュメントに置き換えるものです。

以上で、アプリケーションを実行するために必要な要素はすべて整いました。未完了項目をリストしたり、新しい項目を追加したり、項目を編集したりすることができます。

</h3>
<a name="_Toc395637773">ローカルでのアプリケーションの実行</a>
=========================================================

### 

ローカル コンピューターでアプリケーションをテストするには、Visual Studio で F5 キーを押します。すると、アプリケーションがビルドされてブラウザーが起動し、先ほど見た空のグリッド ページが表示されます。

![Alt text](./media/documentdb-dotnet-application/image24.png)

1\. Item、Item Name、Category の各フィールドを使用して情報を入力し、**[Create New]** リンクをクリックして、いくつか値を指定してください。[Completed] チェック ボックスはオフのままとします。オンにした場合、新しい項目が完了済みの状態で追加されるため、最初のリストに表示されません。

![Alt text](./media/documentdb-dotnet-application/image25.png)

[Create] をクリックすると、[Index] ページにリダイレクトされ、追加した項目がリストに表示されます。

![Alt text](./media/documentdb-dotnet-application/image26.png)

Todo リストに他にもいくつか項目を追加してみてください。

2\. リストの項目の横にある [Edit] をクリックすると、[Edit] ビューが表示され、対象オブジェクトのプロパティを更新することができます。"Completed" フラグもこのビューで更新できます。このフラグをオンにした場合、項目が完了済みとしてマークされ、未完了タスクのリストから削除されます。

![Alt text](./media/documentdb-dotnet-application/image27.png)

3\. タスクを完了済みにするには、このチェック ボックスをオンにして **[Save]** をクリックするだけです。最初のリスト ページにリダイレクトされ、その項目がリストから消えていることがわかります。

</h3>
<a name="_Toc395637774">Azure Websites へのアプリケーションのデプロイ</a>
================================================================

### 

以上で、DocumentDB と連携するアプリケーションが完成しました。今度は、このアプリケーションを Azure Websites にデプロイします。

空の ASP.NET MVC プロジェクトを作成するときに [クラウドでホストする] を選択した場合、デプロイはごく簡単です。必要な作業の大半は自動的に行われます。このアプリケーションをすべてのユーザーに公開するには、ソリューション エクスプローラーでプロジェクトを右クリックし、[発行] を選択します (ローカル実行したままになっていないことを確認してください)。

![Alt text](./media/documentdb-dotnet-application/image28.png)

すべての設定は、資格情報に従って自動的に構成されます。実際、Web サイトは既に、Azure の "作成先 URL" (下図参照) に作成されており、後は **[発行]** をクリックするだけです。

![Alt text](./media/documentdb-dotnet-application/image29.png)

数秒すると、Web アプリケーションの発行が完了し、ブラウザーが起動されます。作成したアプリケーションが Azure で実行されているようすが確認できます。

</h3>



<a name="_Toc395637775">まとめ</a>
======================================

### 

ご利用ありがとうございます。ここでは初めての方を対象に、Azure DocumentDB を使用した ASP.NET MVC アプリケーションを作成し、Azure Websites に発行する方法を説明しました。ここで省略した詳細や削除の機能など、アプリケーション全体のソースコードは、[ここ][]でダウンロードできます。


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
[Microsoft Web プラットフォーム インストーラー]: http://www.microsoft.com/web/downloads/platform.aspx
[ここ]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[クロスサイト リクエスト フォージェリの防止]: http://go.microsoft.com/fwlink/?LinkID=517254
[ASP.NET MVC での基本的な CRUD 操作]: http://go.microsoft.com/fwlink/?LinkId=317598
