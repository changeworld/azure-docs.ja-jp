<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="Build a web application with ASP.NET MVC using DocumentDB | Azure" description="Learn how to use DocumentDB to build a To Do List web application. You'll store and access data from an ASP.NET MVC web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="hawong"></tags>

# <a name="_Toc395809351">DocumentDB を使用した ASP.NET MVC Web アプリケーションの構築</a>

# <a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">概要</a>

## <a name="_Toc395637759">シナリオ</a>

このドキュメントでは、Azure DocumentDB を効果的に活用して、JSON ドキュメントの
保存とクエリを行う方法を取り上げます。ToDo リストの Web アプリケーションを
 Azure DocumentDB を使って構築するエンド ツー エンドのチュートリアルを紹介
しています。

チュートリアルでは、Azure に用意されている DocumentDB サービスを使用して、
Azure にホストされている ASP.NET MVC Web アプリケーションからデータを保存したり
データにアクセスしたりする方法を説明します。ASP.NET MVC と Azure Websites の
使用経験がある読者を想定しています。

学習内容:

1. DocumentDB アカウントを作成してプロビジョニングする

2. ASP.NET MVC アプリケーションを作成する

3. Web アプリケーションから Azure DocumentDB に接続して使用する

4. Web アプリケーションを Azure Websites にデプロイする

このチュートリアルでは、タスクを作成、取得、完了する機能を備えた、
単純な Web ベースのタスク管理アプリケーションを作成
します。対象となるタスクは、JSON ドキュメントとして Azure
DocumentDB に保存するものとします。

![Alt text][]

# <a name="_Toc395637760">前提条件</a>

この記事の手順を実行する前に、次のソフトウェアがインストール
されていることを確認してください。

Git for Windows (<http://www.git-scm.com/downloads>)

Visual Studio 2013 (またはその無償版の
[Visual Studio Express][])

Azure SDK for .NET Version 2.3 以降
([Microsoft Web Platform Installer][] にて提供)

このドキュメントに掲載されているすべてのスクリーンショットは、
Visual Studio 2013 Update 3 および Azure SDK for .NET Version2.4 で
撮影しました。ご利用のシステムにインストールされているバージョンと異なる場合、
画面やオプション設定が一部異なる可能性もありますが、上記の
前提条件を満たしていれば、アプリケーションの動作に支障はありません。

# <a name="_Toc395637761">DocumentDB データベース アカウントの作成</a>

DocumentDB データベース アカウントを Azure にプロビジョニングするには、
Azure の管理ポータルを開いて、ホームページにある Azure ギャラリー タイルをクリックするか、
または、画面の左下隅にある [+] をクリックします。

![Alt text][1]

これで Azure のギャラリーが開き、Azure に用意されているさまざまな
サービスを選択することができます。ギャラリーのカテゴリ一覧から
[データ、ストレージ、バックアップ] を選択します。

![Alt text][2]

その中から、Azure DocumentDB に該当するオプションを選択します。

![Alt text][3]

次に、画面の一番下にある [作成] を選択します。

![Alt text][4]

[新しい DocumentDB] ブレードが開き、新しいアカウントの名前、
リージョン、スケール、リソース グループなどの設定を指定することが
できます。

![Alt text][5]

アカウントに使用する値を指定した後、[作成] をクリックすると、プロビジョニング プロセスが開始されて、
データベース アカウントが作成されます。プロビジョニング プロセスが完了すると、ポータルの通知領域に
その旨が表示され、スタート画面上のタイル (タイルを作成する設定になっている場合) が、処理の完了を示す表示に
変化します。

![Alt text][6]

プロビジョニングの完了後、スタート画面から DocumentDB
タイルをクリックすると、新しく作成した DocumentDB アカウントの
メイン ブレードが表示されます。

![Alt text][7]
![Alt text][8]

[キー] ボタンを使用してエンドポイントの URL とプライマリ キーを表示し、
それらの値をクリップボードにコピーして、いつでも呼び出せる場所に保存してください。
これから作成する Web アプリケーションの中でそれらの値を使用します。

次のセクションで、新しい ASP.NET MVC アプリケーションをゼロから作成する方法について
説明します。完成したソリューションはこちらのページからダウンロードできます。ご参照ください。

# <a name="_Toc395637762">新しい ASP.NET MVC アプリケーションの作成</a>

Visual Studio で [ファイル]、[新しいプロジェクト] の順にクリックし、
新しい ASP.NET MVC Web アプリケーションを作成するためのオプションを選択します。

![Alt text][9]

プロジェクトの作成先となる場所を選択し、[OK] をクリックします。

![Alt text][10]

アプリケーションを Azure でホスティングする場合は、右下にある [クラウドでホストする] チェック ボックスをオンにしてください。このチュートリアルでも、クラウドでホストするように選択しています (Azure Websites をホストとしてアプリケーションを実行します)。
このオプションを選択すると、Azure Websites があらかじめ自動的にプロビジョニングされ、
最終的に完成したアプリケーションをデプロイする作業が段違いにやりやすくなります。

[OK] をクリックすると、Visual Studio のスキャフォールディング機能によって
空の ASP.NET MVC テンプレートが作成されます。Azure アカウントにログインして新しい
Web サイトに使用する値を指定するよう求める画面が別途表示されることも
あります。画面の指示に従って、Azure に関連したそれらの値をすべて入力したうえで続行してください。

Visual Studio によってスケルトン MVC アプリケーションが作成されると、
空の ASP.NET アプリケーションをローカルに実行できる状態となります。

読者の皆さんは、ASP.NET の "Hello World" アプリケーションで体験済みだと思いますので、ローカル実行する手順は省略します。早速このプロジェクトに DocumentDB を追加して、アプリケーションを構築しましょう。

# <a name="_Toc395637763">ASP.NET MVC アプリケーションのセットアップ</a>

### 

### <a name="_Toc395637764">モデルの追加</a>

最初に作成するのは、MVC の "**M**" (モデル) です。ソリューション
エクスプローラーで *Models* フォルダーを右クリックし、**[追加]**、
**[クラス]** の順にクリックします。

![Alt text][11]

新しいクラスに **Item** という名前を付け、次のコードを追加します。

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public int ID { get; set; }
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

DocumentDB のすべてのデータは、JSON 形式でネットワーク越しに渡され、保存されます。
JSON.NET によるオブジェクトのシリアル化/逆シリアル化の動作は、
JsonProperty 属性を使用して制御できます。
上のコードでは、この属性を使用して、JSON の小文字プロパティ名の表記規則に準拠させています。
ただし、これは**必須**ではありません。

### <a name="_Toc395637765">コントローラーの追加</a>

以上が、M (モデル) に相当する部分です。今度は、MVC の "**C**" に相当する
コントローラー クラスを作成しましょう。
**ソリューション エクスプローラー**で *Controllers* フォルダーを右クリックし、
**[追加]**、**[コントローラー]** の順にクリックします。

![Alt text][12]

![Alt text][13]

**[スキャフォールディングの追加]** ダイアログ ボックスで **[MVC 5 コントローラー - 空]** をクリックし、
**[追加]** をクリックします。

![Alt text][14]

新しいコントローラーに **ItemController** という名前を付けます。

ソリューション エクスプローラーに ItemController が追加されます (下図参照)。

![Alt text][15]

### <a name="_Toc395637766">ビューの追加</a>

最後に作成するのは MVC の "**V**" (ビュー) です。

#### 項目のインデックス ビューの追加

ソリューション エクスプローラーで ***Views*** フォルダーを展開し、
先ほど *ItemController* を追加したときに Visual Studio
によって自動的に作成された (空の) Item フォルダーを探します。**[*Item*]** を右クリックし、
新しいビューを追加します。

![Alt text][16]

[ビューの追加] ダイアログで、ビューに "***Index***" という名前を付け、[テンプレート] には
「***List***」を使用します。さらに、先ほど作成した「***Item (Todo.Models)***」をクラスとして選択し、
最後に、ソリューションのレイアウト ページとして「***\_Layout.cshtml***」を指定します。

![Alt text][17]

以上の値をすべて設定し、[追加] をクリックして、Visual Studio で
自動的にビューを作成します。これでテンプレートのビューが作成されます。
完了すると、作成された cshtml ファイルが表示されます。このドキュメントは閉じてください。
後で再度使用します。

#### 新しい項目を作成するためのビューの追加

同様に、項目を新規作成するためのビューを新たに作成します。
以下の例を参照してください。

![Alt text][18]

#### 項目を編集するためのビューの追加

# <a name="_Toc395888515"></a>

最後にもう 1 つ、項目を編集するためのビューを同じように作成します。

![Alt text][19]

この作業が済んだら、Visual Studio に表示されている cshtml ドキュメントは閉じてください。
これらのビューは後で使用します。

</h1>
# <a name="_Toc395637767">プロジェクトへの DocumentDB の追加</a>

以上で、このソリューションに必要な ASP.NET MVC の構成要素が完成しました。
いよいよチュートリアルの本題に入ります。作成した Web アプリケーションに Azure DocumentDB を追加しましょう。

</h1>
## <a name="_Toc395637768">DocumentDB NuGet パッケージのインストール</a>

DocumentDB .NET SDK は、NuGet パッケージとして配布されます。
以降の作業には、Visual Studio で [プロジェクト] を右クリックし、[NuGet パッケージの管理] をクリックして表示される NuGet パッケージ マネージャーを使用します。

![Alt text][20]

オンライン検索で "Azure DocumentDB" を検索し、パッケージをインストールします。これにより、
DocumentDB パッケージだけでなく、
依存関係のあるすべてのコンポーネント (Newtonsoft.Json など) がダウンロードされてインストールされます。

以下の図は、インストール完了後の Visual Studio ソリューションの状態です。
新しい参照が 2 つ追加されていることがわかります。

![Alt text][21]

</h1>
## <a name="_Toc395637769">DocumentDB の接続</a>

### <a name="_Toc395637770">未完了項目の一覧表示</a>

**ItemController** を開いて、Visual Studio によって追加されたクラス内のコードをすべて削除してください (クラスそのものは残します)。
DocumentDB を使用して最初から作り直します。

空になった ItemController クラス内に次のコード スニペットを
追加します。

    public async Task<ActionResult> Index() 
    {
        var items = await repo.GetIncompleteItems();
        return View(items);
    }

このコードには、DocumentDB の "擬似的リポジトリ" クラスが
使用されています。実際には、DocumentDB 固有のコードをすべて含んだ単なるヘルパー クラスです。
このチュートリアルでは、その目的上、実際のアプリケーションを
構築する場合とは異なり、リポジトリ パターンと依存関係の挿入を用いた完全な
データ アクセス レイヤーを実装することはしません。簡潔さを優先し、
すべてのデータ アクセス ロジックを 1 つのプロジェクトに配置
することにします。

新しいクラスをプロジェクトに追加し、**DocumentDBRepository** という名前を付けます。
このクラス ファイル内のコードを次のように置き換えてください。

    public static class DocumentDBRepository
    {
        public static async Task<List<Item>> GetIncompleteItems()
        {
            return await Task<List<Item>>.Run( () => 
                Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => !d.Completed)
                    .AsEnumerable()
                    .ToList<Item>());
        }
    } 

#### 

未解決の参照は、適切な using ステートメントを追加して
解決してください。NuGet パッケージが正しくインストールされていれば、Visual Studio 内のすべての参照はスムーズに解決されます。
*CreateDocumentQuery* の参照を解決するためには、
次の using ディレクティブを手動で追加する必要があります。

    using Microsoft.Azure.Documents.Linq; 

ただし、このままではまだ正常に機能しません。まだ擬似リポジトリ クラスに
手作業で追加する必要のあるコードが残っています。以下のコード
をリポジトリ クラスに追加してください。

</h4>
        private static Database database;
        private static Database Database
        {
            get
            {
                if (database == null)
                {
                    ReadOrCreateDatabase().Wait();
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
                    ReadOrCreateCollection(Database.SelfLink).Wait();
                }
                
                return collection;
            }
        }

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

        private static DocumentClient client;
        private static DocumentClient Client
        {
            get
            {
                if (client == null)
                {
                    String endpoint = ConfigurationManager.GetSetting("endpoint")
                    string authKey = ConfigurationManager.GetSetting("authKey");
                    Uri endpointUri = new Uri(endpoint);
                    client = new DocumentClient(endpointUri, authKey);
                }
                
                return client;
            }
        }

        private static async Task ReadOrCreateCollection(string databaseLink)
        {
            var collections = Client.CreateDocumentCollectionQuery(databaseLink)
                              .Where(col => col.Id == CollectionId).ToArray();
            
            if (collections.Any())
            {
                collection = collections.First();
            }
            else
            {
                collection = await Client.CreateDocumentCollectionAsync(databaseLink, 
                    new DocumentCollection { Id = CollectionId });
            }
        }
     
        private static async Task ReadOrCreateDatabase()
        {
            var databases = Client.CreateDatabaseQuery()
                            .Where(db => db.Id == DatabaseId).ToArray();
            
            if (databases.Any())
            {
                database = databases.First();
            }
            else
            {
                Database database = new Database { Id = DatabaseId };   
                database = await Client.CreateDatabaseAsync(database);
            }
        }

</h1>
#### 

GetIncompleteItems() などのメソッドに初めてアクセスするときに、そのメソッドによって
Client が呼び出され、そこで Azure DocumentDB との接続が確立されます。また、
GetIncompleteItems() の最初の呼び出しで、このアプリケーションの
Database と DocumentCollection の読み取りまたは作成が試みられます。この処理が
必要になるのは 1 回だけです。その後は、両方のリソースの SelfLink プロパティをキャッシュしておけば、
アプリケーション内で再利用することができます。

後で同じアプリケーション内から DocumentDB に接続するときは、
その都度インスタンスを新しく作成するのではなく、同じ Client、Database、
Collection のインスタンスが再利用されます。

**web.config** を開いて、<appsettings\> セクションの下に次の行を追加します。

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>

</h4>
この時点で、エラーなくソリューションをビルドすることは可能です。

ここで仮にアプリケーションを実行した場合は、Home コントローラーと
その Index ビューが表示されます。これは、作業の開始時に選択した
MVC テンプレート プロジェクトの既定の動作であって、私たちが期待する動作ではありません。
この MVC アプリケーションのルーティングに手を加えて、この動作を変更しましょう。

App\_Start フォルダーにある ***RouteConfig.cs*** を開き、
"defaults:" で始まる行を探し、次のように変更します。

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

このコードは、ルーティングの動作を制御する値を URL に指定しなかった場合、コントローラーとして "Home" ではなく "Item" を、ビューとしてユーザー Index を使用するように ASP.NET MVC に対して命令しています。
ここでアプリケーションを実行すると、**ItemController** が呼び出され、**GetIncompleteItems** メソッドの結果が Views\\Item\\Index ビューに返されます。
このプロジェクトを今実行した場合、次のような画面が表示されます。

![Alt text][22]

### <a name="_Toc395637771">項目の追加</a>

空のグリッドでは物足りないので、データベースにいくつか項目を
追加してみましょう。

新しい項目を作成するためのビューは作成済みです。また、その作成画面にユーザーを誘導するためのボタンも既にインデックス ビューに存在します。
そこで、
DocumentDB にレコードを保存するためのコードを Controller と Repository に追加しましょう。

***ItemController.cs*** を開いて次のコード スニペットを追加します。
このコードによって、Create アクションに対応する処理が ASP.NET MVC に伝えられます。
このケースでは、関連する Create.cshtml ビュー (先ほど作成したもの) をレンダリングする処理が該当します。

    public ActionResult Create()
    { 
          return View(); 
    }

このコントローラーには、Create ビューから送信された内容を
受け取るためのコードを追加する必要があります。

次のコード ブロックを追加してください。フォームの POST に対し、このコントローラーで行う処理を ASP.NET MVC に伝えます。

    [HttpPost]  
    public async Task<ActionResult> Create(Item item)  
    {
        if (ModelState.IsValid)  
        {  
            await repo.CreateDocument(item);  
            return RedirectToAction("Index");  
        }   
        return View(item);   
    }

これで、ItemController は、フォームから入力された Item を
擬似リポジトリ クラスの CreateDocument メソッドに渡すようになります。
そこで、次のメソッドを DocumentDBRepository クラスに追加します。

    public static async Task<Document> CreateDocument(dynamic item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item); 
    }

このメソッドは単に、渡されたオブジェクトを受け取って、
DocumentDB に保存します。

データベースに新しい項目を追加するために必要なコードは以上です。

### <a name="_Toc395637772">項目の編集</a>

最後に、データベース内の項目を編集する機能と、
タスクが完了したときにそれらを完了済みとしてマークするための機能を実装します。
項目の追加機能と同様、編集のためのビューは既に追加してあります。
後は、コントローラーと DocumentDBRepository クラスに少しのコードを追加するだけです。

ItemController クラスに次のコードを追加します。

    public async Task<ActionResult> Edit(string id)    
    {  
        if (id == null)  
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);   
        }

        Item item = await repo.GetDocument(id);
        if (item == null)
        {       
            return HttpNotFound();
        }
        
        return View(item);
    }

    [HttpPost] 
    public async Task<ActionResult> Edit(Item item)  
    {
        if (ModelState.IsValid)
        {
             await repo.UpdateDocument(item);
             return RedirectToAction("Index");
        }
        
        return View(item); 
    }

1 つ目のメソッドは、ユーザーが [Index] ビューから [Edit] リンクを
クリックしたときに引き起こされる Http Get を処理するものです。このメソッドによって、DocumentDB から
Document が取得されて、[Edit] ビューに渡されます。

### 

その後、[Edit] ビューから、IndexController に Http Post が返されます。
これを処理するのが 2 つ目に追加したメソッドで、更新済みのオブジェクト
が DocumentDB に渡され、データベースに保存されます。

DocumentDBRepository クラスに次のコードを追加してください。

    public static async Task<Item> GetDocument(string id)
    {
        return await Task<Item>.Run(() =>
            Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => d.ID == id)
                .AsEnumerable()
                .FirstOrDefault());
    }

    public static async Task<Document> UpdateDocument(Item item)
    {
        var doc = Client.CreateDocumentQuery<Document>(Collection.DocumentsLink)
                    .Where(d => d.Id == item.ID)
                    .AsEnumerable().FirstOrDefault();
        
        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

上の 2 つのメソッドのうち 1 つ目は、DocumentDB から Item を取得するものです。
この Item が ItemController に戻された後、[Edit] ビューに渡されます。

2 つ目のメソッドは、DocumentDB 内のドキュメントを、
ItemController から渡されたドキュメントに置き換えるものです。

以上で、アプリケーションを実行するために必要な要素はすべて整いました。
未完了項目をリストしたり、新しい項目を追加したり、項目を編集したりすることができます。

</h3>
# <a name="_Toc395637773">ローカルでのアプリケーションの実行</a>

### 

ローカル コンピューターでアプリケーションをテストするには、Visual Studio で
F5 キーを押します。すると、アプリケーションがビルドされてブラウザーが起動し、
先ほど見た空のグリッド ページが表示されます。

![Alt text][23]

1\.Item、Item Name、Category の各フィールドを使用して情報を入力し、
**[Create New]** リンクをクリックして、いくつか値を指定してください。
[Completed] チェック ボックスはオフのままとします。オンにした場合、新しい項目が
完了済みの状態で追加されるため、最初のリストに表示されません。

![Alt text][24]

[Create] をクリックすると、[Index] ページにリダイレクトされ、
追加した項目がリストに表示されます。

![Alt text][25]

Todo リストに他にもいくつか項目を追加してみてください。

2\.リストの項目の横にある [Edit] をクリックすると、[Edit] ビューが
表示され、対象オブジェクトのプロパティを
更新することができます。"Completed" フラグもこのビューで更新できます。このフラグをオンにした場合、
項目が完了済みとしてマークされ、未完了タスクのリストから削除されます。

![Alt text][26]

3\.タスクを完了済みにするには、このチェック ボックスをオンにして
**[Save]** をクリックするだけです。最初のリスト ページにリダイレクトされ、
その項目がリストから消えていることがわかります。

</h3>
# <a name="_Toc395637774">Azure Websites へのアプリケーションのデプロイ</a>

### 

以上で、DocumentDB と連携するアプリケーションが完成しました。
今度は、このアプリケーションを Azure Websites にデプロイしたいと思います。

空の ASP.NET MVC プロジェクトを作成するときに [クラウドでホストする] を
選択した場合、デプロイはごく簡単です。必要な作業の大半は自動的に
行われます。このアプリケーションをすべてのユーザーに公開するには、
ソリューション エクスプローラーでプロジェクトを右クリックし、[発行] を選択します (
ローカル実行したままになっていないことを確認してください)。

![Alt text][27]

すべての設定は、資格情報に従って自動的に構成されます。
実際、Web サイトは既に、Azure の "作成先 URL" (下図参照) に作成されており、
後は **[発行]** をクリックするだけです。

![Alt text][28]

数秒すると、Web アプリケーションの発行が完了し、
ブラウザーが起動されます。作成したアプリケーションが Azure で実行されているようすが
確認できます。

</h3>
# <a name="_Toc395637775">まとめ</a>

### 

ご利用ありがとうございます。ここでは初めての方を対象に、Azure DocumentDB を使用した ASP.NET MVC アプリケーションを作成し、Azure Websites に発行する方法を説明しました。アプリケーションの完全なソース コードはこちらからダウンロードできます。ぜひご参考ください。

[ダウンロードはこちら] (<http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409>)

  [Alt text]: ./media/documentdb-dotnet-application/image1.png
  [Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [1]: ./media/documentdb-dotnet-application/image2.png
  [2]: ./media/documentdb-dotnet-application/image3.png
  [3]: ./media/documentdb-dotnet-application/image4.png
  [4]: ./media/documentdb-dotnet-application/image5.png
  [5]: ./media/documentdb-dotnet-application/image6.png
  [6]: ./media/documentdb-dotnet-application/image7.png
  [7]: ./media/documentdb-dotnet-application/image8.png
  [8]: ./media/documentdb-dotnet-application/image9.png
  [9]: ./media/documentdb-dotnet-application/image10.png
  [10]: ./media/documentdb-dotnet-application/image11.png
  [11]: ./media/documentdb-dotnet-application/image12.png
  [12]: ./media/documentdb-dotnet-application/image13.png
  [13]: ./media/documentdb-dotnet-application/image14.png
  [14]: ./media/documentdb-dotnet-application/image15.png
  [15]: ./media/documentdb-dotnet-application/image16.png
  [16]: ./media/documentdb-dotnet-application/image17.png
  [17]: ./media/documentdb-dotnet-application/image18.png
  [18]: ./media/documentdb-dotnet-application/image19.png
  [19]: ./media/documentdb-dotnet-application/image20.png
  [20]: ./media/documentdb-dotnet-application/image21.png
  [21]: ./media/documentdb-dotnet-application/image22.png
  [22]: ./media/documentdb-dotnet-application/image23.png
  [23]: ./media/documentdb-dotnet-application/image24.png
  [24]: ./media/documentdb-dotnet-application/image25.png
  [25]: ./media/documentdb-dotnet-application/image26.png
  [26]: ./media/documentdb-dotnet-application/image27.png
  [27]: ./media/documentdb-dotnet-application/image28.png
  [28]: ./media/documentdb-dotnet-application/image29.png
