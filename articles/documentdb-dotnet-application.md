<properties 
	pageTitle="DocumentDB を使用した ASP.NET MVC Web アプリケーションの構築 | Azure" 
	description="DocumentDB を使用して To Do List Web アプリケーションを作成する方法について説明します。データは Azure にホストされた ASP.NET MVC Web アプリケーションに格納してアクセスします。" 
	services="documentdb" 
	documentationCenter="" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="ryancraw"/>

<a name="_Toc395809351">DocumentDB を使用した ASP.NET MVC Web アプリケーションの構築</a>
=======================================================================================================
<a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">概要</a>
==============================================================================================

<a name="_Toc395637759">シナリオ</a>
------------------------------------

このドキュメントでは、Azure DocumentDB を効果的に活用して、JSON ドキュメントの保存とクエリを行う方法を取り上げます。ToDo リストの Web アプリケーションを Azure DocumentDB を使って構築するエンド ツー エンドの手順を紹介しています。

この手順紹介では、Azure に用意されている DocumentDB サービスを使用して、Azure にホストされている ASP.NET MVC Web アプリケーションからデータを保存したりデータにアクセスしたりする方法を説明します。ASP.NET MVC と Azure Web サイトの使用経験がある読者を想定しています。

学習内容:

1\. DocumentDB アカウントを作成してプロビジョニングする

2\. ASP.NET MVC アプリケーションを作成する

3\. Web アプリケーションから Azure DocumentDB に接続して使用する

4\. Web アプリケーションを Azure Websites にデプロイする

この手順では、タスクを作成、取得、完了する機能を備えた、単純な Web ベースのタスク管理アプリケーションを作成します。対象となるタスクは、JSON ドキュメントとして Azure DocumentDB に保存するものとします。

![Alt text](./media/documentdb-dotnet-application/image1.png)


<a name="_Toc395637760">Prerequisites</a>
================================================================

この記事の手順を実行する前に、次のソフトウェアがインストールされていることを確認してください。

Git for Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (またはその無償版の [Visual Studio Express][])

Azure SDK for .NET Version 2.3 以降[(Microsoft Web Platform Installer にて提供)][]

このドキュメントに掲載されているすべてのスクリーンショットは、Visual Studio 2013 Update 3 および Azure SDK for .NET Version2.4 で撮影しました。ご利用のシステムにインストールされているバージョンと異なる場合、画面やオプション設定が一部異なる可能性もありますが、上記の前提条件を満たしていれば、アプリケーションの動作に支障はありません。

<a name="_Toc395637761">DocumentDB データベース アカウントの作成</a>
================================================================

DocumentDB データベース アカウントを Azure にプロビジョニングするには、Azure の管理ポータルを開いて、ホームページにある Azure ギャラリー タイルをクリックするか、または、画面の左下隅にある [+] をクリックします。

![Alt text](./media/documentdb-dotnet-application/image2.png)


これで Azure のギャラリーが開き、Azure に用意されているさまざまなサービスを選択できます。ギャラリーのカテゴリ一覧から [データ、ストレージ、バックアップ] を選択します。

![Alt text](./media/documentdb-dotnet-application/image3.png)

その中から、Azure DocumentDB に該当するオプションを選択します。

![Alt text](./media/documentdb-dotnet-application/image4.png)


次に、画面の一番下にある [作成] を選択します。

![Alt text](./media/documentdb-dotnet-application/image5.png)

[新しい DocumentDB] ブレードが開き、新しいアカウントの名前、リージョン、スケール、リソース グループなどの設定を指定できます。

![Alt text](./media/documentdb-dotnet-application/image6.png)


アカウントの値を指定した後、[作成] をクリックすると、プロビジョニング プロセスが開始されて、データベース アカウントが作成されます。
プロビジョニング プロセスが完了すると、ポータルの通知領域にその旨が表示され、スタート画面上のタイル (タイルを作成する設定になっている場合) が、処理の完了を示す表示に変化します。

![Alt text](./media/documentdb-dotnet-application/image7.png)


プロビジョニングの完了後、スタート画面から DocumentDB タイルをクリックすると、新しく作成した DocumentDB アカウントのメイン ブレードが表示されます。

![Alt text](./media/documentdb-dotnet-application/image8.png) 
![Alt text](./media/documentdb-dotnet-application/image9.png)


[キー] ボタンを使用してエンドポイントの URL とプライマリ キーを表示し、それらの値をクリップボードにコピーして、いつでも呼び出せる場所に保存してください。これから作成する Web アプリケーションの中でそれらの値を使用します。

次のセクションで、新しい ASP.NET MVC アプリケーションをゼロから作成する方法について説明します。完成したソリューションは[こちら]のページからダウンロードできます。ご参照ください。

<a name="_Toc395637762">新しい ASP.NET MVC アプリケーションの作成</a>
================================================================

Visual Studio で [ファイル]、[新しいプロジェクト] の順にクリックし、新しい ASP.NET MVC Web アプリケーションを作成するためのオプションを選択します。

![Alt text](./media/documentdb-dotnet-application/image10.png)


プロジェクトを作成する場所と、そのプロジェクトの呼び方 (たとえば "todo" など) を選択して、[OK] をクリックします。

![Alt text](./media/documentdb-dotnet-application/image11.png)


アプリケーションを Azure でホスティングする場合は、右下にある [クラウドでホストする] チェック ボックスをオンにしてください。このチュートリアルでも、クラウドでホストするように選択しています (Azure Websites をホストとしてアプリケーションを実行します)。このオプションを選択すると、Azure Websites があらかじめ自動的にプロビジョニングされ、最終的に完成したアプリケーションをデプロイする作業が段違いにやりやすくなります。これを他の場所でホストする場合、または Azure を事前に設定しない場合は、[クラウドでホストする] オプションのチェックをはずします。

[OK] をクリックすると、Visual Studio のスキャフォールディング機能によって空の ASP.NET MVC テンプレートが作成されます。これをクラウドでホストすることを選択した場合、少なくとも 1 つの画面が表示され、Azure アカウントにログインして新しい Web サイトの値を指定するよう求められます。画面の指示に従って、Azure に関連したそれらの値をすべて入力したうえで続行してください。 

ここでは Azure SQL データベース サーバーを使用しないため、ここで [データベース サーバー] を選択していません。後で Azure DocumentDB アカウントを管理ポータルで作成する予定です。 

![Alt text](./media/documentdb-dotnet-application/image11_1.png)

Visual Studio によってスケルトン MVC アプリケーションが作成されると、空の ASP.NET アプリケーションをローカルに実行できる状態となります。

読者の皆さんは、ASP.NET の "Hello World" アプリケーションで体験済みだと思いますので、ローカル実行する手順は省略します。早速このプロジェクトに DocumentDB を追加して、アプリケーションを構築しましょう。

</h1>
<a name="_Toc395637767">プロジェクトへの DocumentDB の追加</a>
=============================================================

以上で、このソリューションに必要な ASP.NET MVC の構成要素が完成しました。いよいよチュートリアルの本題に入ります。作成した Web アプリケーションに Azure DocumentDB を追加しましょう。

### 

### <a name="_Toc395637764">NuGet パッケージのインストール</a>

DocumentDB .NET SDK は、NuGet パッケージの形式で配布されています。以降の作業には、Visual Studio で [プロジェクト] を右クリックし、[NuGet パッケージの管理] をクリックして表示される NuGet パッケージ マネージャーを使用します。

![Alt text](./media/documentdb-dotnet-application/image21.png)

オンライン検索で "Azure DocumentDB" を検索し、パッケージをインストールします。これにより、DocumentDB パッケージだけでなく、依存関係のあるすべてのコンポーネント (Newtonsoft.Json など) がダウンロードされてインストールされます。

**注:** サービスはまだプレビューですが、NuGet パッケージは「プレリリース」とマークされるため、「プレリリースを含める」にオプションを含めないと、パッケージを見つけられません。

また、パッケージ コマンド コンソールを使用して、次のように入力し、パッケージをインストールすることもできます。

    Install-Package Microsoft.Azure.Documents.Client -Pre

以下の図は、インストール完了後の Visual Studio ソリューションの状態です。新しい参照が 2 つ追加されていることがわかります。

![Alt text](./media/documentdb-dotnet-application/image22.png)


<a name="_Toc395637763">ASP.NET MVC アプリケーションのセットアップ</a>
==================================================================

### 

### <a name="_Toc395637764">モデルの追加</a>

最初に作成するのは、MVC の **M** (モデル) です。ソリューション エクスプローラーで  *Models* フォルダーを右クリックし、[**追加**]、[**クラス**] の順にクリックします。

![Alt text](./media/documentdb-dotnet-application/image12.png)

新しいクラスに **Item** という名前を付け、次のコードを追加します。

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

DocumentDB のすべてのデータは、JSON 形式でネットワーク越しに渡され、保存されます。JSON.NET によってオブジェクトをシリアル化または逆シリアル化する方法を制御するには、作成した Item クラスで示したとおり、JsonProperty 属性を使用できます。これを行う**必要はありません**が、プロパティが JSON camelCase 名前付け規則に従っていることを確認する必要があります。 

JSON プロパティー名の形式を制御できるだけでなく、Description プロパティーに対して行ったように、.NET プロパティーの名前全体を変更することもできます。 

必要があれば、ここで JsonConverter オブジェクトも使用して、シリアル化の方法を完全に制御できます。  

ここで使用されている "JsonProperty" 属性を Visual Studio で解決するために、クラス ファイルの using セクションに次の using ステートメントを追加する必要があります。

    using Newtonsoft.Json;

### <a name="_Toc395637765">コントローラーの追加</a>

以上が、M (モデル) に相当する部分です。今度は、MVC の **C** に相当するコントローラー クラスを作成しましょう。
**ソリューション エクスプローラー**で  *Controllers* フォルダーを右クリックし、[**追加**]、[**クラス**] の順にクリックします。

![Alt text](./media/documentdb-dotnet-application/image13.png)

![Alt text](./media/documentdb-dotnet-application/image14.png)


[**スキャフォールディングの追加**] ダイアログ ボックスで [**MVC 5 コントローラー - 空]** をクリックし、[追加] をクリックします。**[追加]** をクリックします。

![Alt text](./media/documentdb-dotnet-application/image15.png)

新しいコントローラーに **ItemController** という名前を付けます。

ソリューション エクスプローラーに ItemController が追加されます (下図参照)。

![Alt text](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766">ビューの追加</a>

最後に作成するのは MVC の **V** (ビュー) です。


#### 項目のインデックス ビューの追加

ソリューション エクスプローラーで ***Views*** フォルダーを展開し、先ほど  *ItemController* を追加したときに Visual Studio によって自動的に作成された (空の) Item フォルダーを探します。***Item*** を右クリックし、新しいビューを追加します。

![Alt text](./media/documentdb-dotnet-application/image17.png)

[ビューの追加] ダイアログで、ビューに "***Index***" という名前を付け、[テンプレート] には ***List*** を使用します。さらに、先ほど作成した ***Item (todo.Models)*** をクラスとして選択し、最後に、ソリューションのレイアウト ページとして ***~Views/Shared/_Layout.cshtml*** を指定します。

![Alt text](./media/documentdb-dotnet-application/image18.png)


以上の値をすべて設定し、[追加] をクリックして、Visual Studio で自動的にビューを作成します。これでテンプレートのビューが作成されます。完了すると、作成された cshtml ファイルが表示されます。このドキュメントは閉じてください。後で再度使用します。

#### 新しい項目を作成するためのビューの追加

同様に、項目を新規作成するためのビューを新たに作成します。以下の例を参照してください。

![Alt text](./media/documentdb-dotnet-application/image19.png)

#### 項目を編集するためのビューの追加

<a name="_Toc395888515"></a>
============================

最後にもう 1 つ、項目を編集するためのビューを同じように作成します。

![Alt text](./media/documentdb-dotnet-application/image20.png)


この作業が済んだら、Visual Studio に表示されている cshtml ドキュメントは閉じてください。これらのビューは後で使用します。

</h1>
<a name="_Toc395637769">DocumentDB の接続</a>
------------------------------------------------

### <a name="_Toc395637770">未完了項目の一覧表示</a>

**ItemController** を開いて、Visual Studio によって追加されたクラス内のコードをすべて削除してください (クラスそのものは残します)。DocumentDB を使用して最初から作り直します。

空になった ItemController クラス内に次のコード スニペットを追加します。

    public ActionResult Index()
    {
        var items = DocumentDBRepository.GetIncompleteItems();
        return View(items);
    }

このコードはまた、まだ作成していない DocumentDBRepository という "擬似リポジトリ" クラスを使用します。これは実際には、DocumentDB 特有のコードをすべて含む Helper クラスです。このチュートリアルでは、その目的上、実際のアプリケーションを構築する場合とは異なり、ファクトリーおよびリポジトリ パターンと依存関係の挿入を用いた完全なデータ アクセス レイヤーを実装することはしません。 
この手順では、簡潔さを優先し、すべてのデータ アクセス ロジックを 1 つのプロジェクトに配置して、DocumentDB 特定のビットに焦点を当てます。

新しいクラスをプロジェクトに追加して、名前を **DocumentDBRepository.** とします。
クラス ファイルのコードを次のコードに置き換えます。

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

Visual Studio ですべての名前空間を解決するには、少し時間がかかります。NuGet パッケージが正しくインストールされていれば、Visual Studio 内のすべての名前空間はスムーズに解決されます。ReadOrCreateDatabase および ReadOrCreateCollection メソッドは、それらが追加されるまで未解決のままです。これについては後述します。 

ここで DocumentDB データベースおよびドキュメント コレクションを読み取る、または作成するために使用される 2 つのメソッド呼び出しがあります。
次の 2 つのメソッドをクラスに追加します。

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

これにより、Database および DocumentCollection のセットアップと、DocumentClient を通じて DocumentDB に接続するいくつかのコードの作成が処理されます。 

設定からいくつかの値を読み取るので、**web.config** を開いて、以下の行を
<AppSettings\> セクションに追加します。

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

***App\_Start\RouteConfig.cs*** を開いて、"defaults:" で始まる行を見つけ、以下と同様にその行を変更します。

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

**セキュリティ上の注意**:[ValidateAntiForgeryToken] 属性は、クロスサイト リクエスト フォージェリ攻撃に対してこのアプリケーションを保護するためにここで使用されます。この属性を追加するだけでなく、偽造防止トークンもビューで処理する必要があります。この詳細と正しい実装方法については、[クロスサイト リクエスト フォージェリの防止に関するページ](http://go.microsoft.com/fwlink/?LinkID=517254)を参照してください。この記事の最後にあるダウンロードのリンクのソース コードから実装できます。

**セキュリティ上の注意**:メソッド パラメーターの [Bind] 属性も使用して、オーバーポスティング攻撃から保護します。詳細については、[ASP.NET MVC での基本的な CRUD 操作][]を参照してください。

これで実装されました。Items Controller は、次のメソッドを DocumentDBRepository クラスに追加するために、フォームからリポジトリ クラスの CreateDocument メソッドに Item を (安全に) 渡します。

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

1\.Item、Item Name、Category の各フィールドを使用して情報を入力し、**[Create New]** リンクをクリックして、いくつか値を指定してください。[Completed] チェック ボックスはオフのままとします。オンにした場合、新しい項目が完了済みの状態で追加されるため、最初のリストに表示されません。

![Alt text](./media/documentdb-dotnet-application/image25.png)

[Create] をクリックすると、[Index] ページにリダイレクトされ、追加した項目がリストに表示されます。

![Alt text](./media/documentdb-dotnet-application/image26.png)

Todo リストに他にもいくつか項目を追加してみてください。

2\.リストの項目の横にある [Edit] をクリックすると、[Edit] ビューが表示され、対象オブジェクトのプロパティを更新することができます。"Completed" フラグもこのビューで更新できます。このフラグをオンにした場合、項目が完了済みとしてマークされ、未完了タスクのリストから削除されます。

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
============================

### 

ご利用ありがとうございます。ここでは初めての方を対象に、Azure DocumentDB を使用した ASP.NET MVC アプリケーションを作成し、Azure Websites に発行する方法を説明しました。ここで省略した詳細や削除の機能など、アプリケーション全体のソースコードは、[ここ]()でダウンロードできます。


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/ja-jp/products/visual-studio-express-vs.aspx
[Microsoft Web プラットフォーム インストーラー]: http://www.microsoft.com/web/downloads/platform.aspx
[ここからダウンロードします]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[クロスサイト リクエスト フォージェリの防止]: http://go.microsoft.com/fwlink/?LinkID=517254
[ASP.NET MVC での基本的な CRUD 操作]: http://go.microsoft.com/fwlink/?LinkId=317598

<!--HONumber=27-->

<!--HONumber=46--> 
