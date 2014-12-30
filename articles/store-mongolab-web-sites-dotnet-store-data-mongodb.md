<properties urlDisplayName="Website with MongoDB on MongoLab" pageTitle="MongoLab の MongoDB を使用する Web サイトの作成 (.NET)" metaKeywords="" description="Learn how to create an Azure website that stores data in MongoDB hosted by MongoLab." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create a C# ASP.NET Application on Azure with MongoDB using the MongoLab Add-On" authors="chris@mongolab.com, eric@mongolab.com" solutions="" manager="mongolab" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/17/2014" ms.author="chris@mongolab.com" />



# MongoLab アドオンを使用して Azure で MongoDB 対応の C# ASP.NET アプリケーションを作成する

<p><em>執筆: Eric Sedor (MongoLab)</em></p>

こんにちは、冒険家のみなさん。サービスとしての MongoDB にようこそ。このチュートリアルでは、次のことについて説明します。

1. [データベースの準備][provision] - Azure ストアの [MongoLab ](http://mongolab.com) アドオンは、Azure クラウドでホスティングされ MongoLab のクラウド データベース プラットフォームで管理されている MongoDB データベースを提供します。
1. [アプリケーションの作成][create] - メモをとるための単純な C# ASP.NET MVC アプリケーションです。
1. [アプリケーションのデプロイ][deploy] - 構成フックをいくつか結び付けて、コードを簡単に配置できるようにします。
1. [データベースの管理][manage] - 最後に、データの検索や視覚化、修正が簡単にできる MongoLab の Web ベースのデータベース管理ポータルを紹介します。

このチュートリアルに関して質問がある場合は、いつでも遠慮なく [support@mongolab.com](mailto:support@mongolab.com) にメールを送ってください。

## クイック スタート
作業対象の Azure アプリケーションと Web サイトが既にある場合、または Azure ストアにある程度慣れている場合は、このセクションを使ってクイック スタートを目指してください。そうでない場合は、以下の「[データベースの準備][provision]」に進んでください。
 
1. Azure ストアを開きます。  
![Store][button-store]
1. MongoLab アドオンを購入します。  
![MongoLab][entry-mongolab]
1. アドオン一覧で MongoLab アドオンをクリックし、**[接続文字列]** をクリックします。  
![ConnectionInfoButton][button-connectioninfo]  
1. MONGOLAB_URI をクリップボードにコピーします。  
![ConnectionInfoScreen][screen-connectioninfo]  
**この URI は、データベース ユーザー名とパスワードを含んでいます。機密情報として扱い、他人と共有しないでください。**
1. Azure Web アプリケーションの [構成] メニューの [接続文字列] ボックスに値を追加します。  
![WebSiteConnectionStrings][focus-website-connectinfo]
1. **[名前]** に「MONGOLAB\_URI」と入力します。
1. **[値]** に、先にコピーした接続文字列を貼り付けます。
1. [種類] ボックスの一覧の (既定値の **[SQLAzure]** の代わりに) **[カスタム]** を選択します。
1. Visual Studio で Mongo C# ドライバーをインストールします。**[ツール]、[ライブラリ パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。パッケージ マネージャー コンソールで「**Install-Package mongocsharpdriver**」と入力し、**Enter** キーを押します。
1. 環境変数から MongoLab 接続 URI を取得するフックをコードに記述します。

        using MongoDB.Driver;  
        ...
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        ...
        MongoUrl url = new MongoUrl(connectionString);
        MongoClient client = new MongoClient(url);
注:Azure では、宣言された元の接続文字列名に **CUSTOMCONNSTR\_** というプレフィックスが追加されます。そのためコードでは **MONGOLAB\_URI** ではなく **CUSTOMCONNSTR\_MONGOLAB\_URI** を参照しています。

では完全なチュートリアルに進みましょう...

<h2><a name="provision"></a>データベースの準備</h2>

[WACOM.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

<h2><a name="create"></a>アプリケーションの作成</h2>

このセクションでは、C# ASP.NET Visual Studio プロジェクトを作成し、単純なメモ アプリケーションを作成するために C# MongoDB ドライバーの使用法を説明します。自分の Web サイトを開いて、メモを書き込み、残されたすべてのメモを表示できるようになります。

この開発作業では、Visual Studio Express 2013 for Web を使用します。

### プロジェクトを作成する
サンプル アプリケーションでは概要を示すために Visual Studio テンプレートを使用します。.NET Framework 4.5 を使用してください。

1. **[ファイル] メニューの [新しいプロジェクト]** をクリックします。[新しいプロジェクト] ダイアログ ボックスが表示されます。    
![NewProject][dialog-mongolab-csharp-newproject]
1. **[インストール済み]、[テンプレート]、[Visual C#]、[Web]** の順に選択します。
1. [.NET バージョン] ボックスの一覧の **[.NET Framework 4.5]** を選択します。
1. **[MVC アプリケーション]** を選択します。  
1. **[プロジェクト名]** ボックスに「_mongoNotes_」と入力します。別の名前にした場合は、このチュートリアルで用意されているコードを修正する必要があります。
1. **[ツール]、[ライブラリ パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。パッケージ マネージャー コンソールで「**Install-Package mongocsharpdriver**」と入力し、**Enter** キーを押します。  
![PMConsole][focus-mongolab-csharp-pmconsole] 
MongoDB C# ドライバーがこのプロジェクトに統合され、_packages.config_ ファイルに対して次の行が自動的に追加されます。

        < package id="mongocsharpdriver" version="1.9.2" targetFramework="net45" / >

### メモ モデルを追加する
まず、単純に日付とテキスト コンテンツを保持するメモ用のモデルを作成します。

1. ソリューション エクスプローラーで、**[Models]** フォルダーを右クリックし、**[追加]、[クラス]** の順に選択します。この新しいクラスの名前を「*Note.cs*」とします。
1. このクラス用に自動生成されたコードを次のコードに置き換えます。  

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using MongoDB.Bson.Serialization.Attributes;
        using MongoDB.Bson.Serialization.IdGenerators;
        using MongoDB.Bson;
                
        namespace mongoNotes.Models
        {
            public class Note
            {
                public Note()
                {
                    Date = DateTime.UtcNow;
                }
                
                private DateTime date;
        
                [BsonId(IdGenerator = typeof(CombGuidGenerator))]
                public Guid Id { get; set; }
        
                [BsonElement("Note")]
                public string Text { get; set; }
        
                [BsonElement("Date")]
                public DateTime Date {
                    get { return date.ToLocalTime(); }
                    set { date = value;}
                }
            }
        }

### データ アクセス レイヤーを追加する
MongoDB にアクセスしてメモを取得し保存する手段を確立することが重要です。データ アクセス レイヤーは、Note モデルを使用し、後で HomeController に結び付けられます。

1. ソリューション エクスプローラーで **mongoNotes** プロジェクトを右クリックして、**[追加]、[新しいフォルダー]** の順に選択します。フォルダー名を「**DAL**」とします。
1. ソリューション エクスプローラーで、**[DAL]** を右クリックし、**[追加]、[クラス]** の順に選択します。この新しいクラスの名前を「*Dal.cs*」とします。
1. このクラス用に自動生成されたコードを次のコードに置き換えます。  

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using mongoNotes.Models;
        using MongoDB.Driver;
        using System.Configuration;

        namespace mongoNotes
        {
            public class Dal : IDisposable
            {
                private MongoServer mongoServer = null;
                private bool disposed = false;
        
                private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
                MongoUrl url;
        
                private string dbName = "myMongoApp";
                private string collectionName = "Notes";
        
                // Default constructor.        
                public Dal()
                {
                    url = new MongoUrl(connectionString);
                }
           
                public List<Note> GetAllNotes()
                {
                    try
                    {
                        MongoCollection<Note> collection = GetNotesCollection();
                        return collection.FindAll().ToList<Note>();
                    }
                    catch (MongoConnectionException)
                    {
                        return new List<Note>();
                    }
                }
        
                // Creates a Note and inserts it into the collection in MongoDB.
                public void CreateNote(Note note)
                {
                    MongoCollection<Note> collection = getNotesCollectionForEdit();
                    try
                    {
                        collection.Insert(note);
                    }
                    catch (MongoCommandException ex)
                    {
                        string msg = ex.Message;
                    }
                }
        
                private MongoCollection<Note> GetNotesCollection()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
                    return noteCollection;
                }
        
                private MongoCollection<Note> getNotesCollectionForEdit()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> notesCollection = database.GetCollection<Note>(collectionName);
                    return notesCollection;
                }
        
                # region IDisposable
        
                public void Dispose()
                {
                    this.Dispose(true);
                    GC.SuppressFinalize(this);
                }
        
                protected virtual void Dispose(bool disposing)
                {
                    if (!this.disposed)
                    {
                        if (disposing)
                        {
                            if (mongoServer != null)
                            {
                                this.mongoServer.Disconnect();
                            }
                        }
                    }
        
                    this.disposed = true;
                }
        
                # endregion
            }
        }
1. 上のコードのうち次のコードに注意してください。  
            
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        private string dbName = "myMongoApp";  
ここで、後で構成する環境変数にアクセスしています。開発目的で実行中のローカル mongo インスタンスがある場合は、この値を一時的に "localhost" に設定することもできます。  
  
  また、データベース名も設定します。具体的には、**dbName** の値を、MongoLab アドオンを準備したときに入力した名前に設定します。
1. 最後に、**GetNotesCollection()** 内の次のコードを確認します。  

        MongoClient client = new MongoClient(url);
        mongoServer = client.GetServer();
        MongoDatabase database = mongoServer.GetDatabase(dbName);
        MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
  There's nothing to change here; Just be aware that this is how you get a MongoCollection object for performing inserts, updates, and queries, such as the following in **GetAllNotes()**:  

        collection.FindAll().ToList<Note>();

C# MongoDB ドライバーの活用方法の詳細については、mongodb.org にある [CSharp ドライバー クイック スタートに関するページ](http://www.mongodb.org/display/DOCS/CSharp+Driver+Quickstart "CSharp Driver Quickstart") を確認してください。

### 作成ビューを追加する
次は新しいメモを作成するビューを追加します。

1. ソリューション エクスプローラーで **[ビュー] の [ホーム]** を右クリックして、**[追加]、[ビュー]** の順に選択します。このビューの名前を「**Create**」として **[追加]** をクリックします。
1. このビュー用に自動生成されたコード (**Create.cshtml**) を次のコードに置き換えます。  

        @model mongoNotes.Models.Note
        
        <script src="@Url.Content("~/Scripts/jquery-1.5.1.min.js")" type="text/javascript"></script>
        <script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
        <script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
        
        @using (Html.BeginForm("Create", "Home")) {
            @Html.ValidationSummary(true)
            <fieldset>
                <legend>New Note</legend>
                <h3>New Note</h3>       
                <div class="editor-label">
                    @Html.LabelFor(model => model.Text)
                </div>
                <div class="editor-field">
                    @Html.EditorFor(model => model.Text)
                </div>
               <p>
                    <input type="submit" value="Create" />
               </p>
           </fieldset>
        }

### index.cshtml を修正する
次に、Web サイトでメモを表示し作成するときに使用する単純なレイアウトを作成します。

1. **[ビュー] の [ホーム]** にある **Index.cshtml** を開いて、その内容を次のコードに置き換えます。  

        @model IEnumerable<mongoNotes.Models.Note>
        
        @{
            ViewBag.Title = "Notes";
        }
        
        <h2>My Notes</h2>

        <table border="1">
            <tr>
                <th>Date</th>
                <th>Note Text</th>      
            </tr>
        
        @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Date)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Text)
                </td>       
            </tr>
        }
        
        </table>
        <div>  @Html.Partial("Create", new mongoNotes.Models.Note())</div>

### HomeController.cs を修正する
最後に、HomeController でデータ アクセス レイヤーをインスタンス化して、それをビューに適用する必要があります。

1. ソリューション エクスプローラーで、**[Controllers]** にある **HomeController.cs** を開いて、その内容を次のコードに置き換えます。  

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using System.Web.Mvc;
        using mongoNotes.Models;
        using System.Configuration;
        
        namespace mongoNotes.Controllers
        {
            public class HomeController : Controller, IDisposable
            {
                private Dal dal = new Dal();
                private bool disposed = false;
                //
                // GET: /Task/
        
                public ActionResult Index()
                {
                    return View(dal.GetAllNotes());
                }
        
                //
                // GET: /Task/Create
        
                public ActionResult Create()
                {
                    return View();
                }
        
                //
                // POST: /Task/Create
        
                [HttpPost]
                public ActionResult Create(Note note)
                {
                    try
                    {
                        dal.CreateNote(note);
                        return RedirectToAction("Index");
                    }
                    catch
                    {
                        return View();
                    }
                }
        
                public ActionResult About()
                {
                    return View();
                }
        
                # region IDisposable
        
                new protected void Dispose()
                {
                    this.Dispose(true);
                    GC.SuppressFinalize(this);
                }
        
                new protected virtual void Dispose(bool disposing)
                {
                    if (!this.disposed)
                    {
                        if (disposing)
                        {
                            this.dal.Dispose();
                        }
                    }
        
                    this.disposed = true;
                }
        
                # endregion
        
            }
        }
    
    
<h2><a name="deploy"></a>アプリケーションの展開</h2>

アプリケーションの開発が済んだため、今度はアプリケーションをホスティングする Azure の Web サイトを作成し、その Web サイトを構成して、コードを展開します。このセクションで中心になるのは、MongoDB 接続文字列 (URI) の使用法です。Web サイトではこの URI を設定した環境変数を構成して、コードから URI を分離します。データベースに接続する資格情報を含むため、URI は機密情報として扱う必要があります。

### 新しい Web サイトを作成して発行設定ファイルを取得する
Azure で Web サイトを作成するのは、特に Azure では Visual Studio 用の発行プロファイルが自動生成されるため、とても簡単です。

1. Azure ポータルで **[新規]** をクリックします。  
![New][button-new]
1. **[コンピューティング]、[Web サイト]、[簡易作成]** の順に選択します。  
![CreateSite][screen-mongolab-newwebsite]
1. URL のプレフィックスを入力します。好みの名前を選択します。ただし、重複した名前は使用できません ("mongoNotes" はおそらく使用できません)。
1. **[Web サイトの作成]** をクリックします。
1. Web サイトの作成が完了したら、Web サイト一覧で、作成した Web サイトの名前をクリックします。Web サイトのダッシュボードが表示されます。  
![WebSiteDashboard][screen-mongolab-websitedashboard]
1. **[概要]** の **[発行プロファイルのダウンロード]** をクリックして、任意のディレクトリに .PublishSettings ファイルを保存します。  
![DownloadPublishProfile][button-website-downloadpublishprofile]

Visual Studio で直接 Web サイトを構成することもできます。Azure アカウントを Visual Studio にリンクするとき、メッセージに従ってそこから Web サイトを構成します。完了したら、ソリューション エクスプローラーでプロジェクト名を右クリックするたけで、Azure にデプロイできます。次の手順に記載されているように、MongoLab 接続文字列は引き続き構成する必要があります。

### MongoLab 接続文字列を取得する

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

### Web サイトの環境変数に接続文字列を追加する

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

### Web サイトを発行する
1. Visual Studio のソリューション エクスプローラーで、**mongoNotes** プロジェクトを右クリックし、**[発行]** をクリックします。[発行] ダイアログ ボックスが表示されます。  
![Publish][dialog-mongolab-vspublish]
1. **[インポート]** をクリックし、先ほどダウンロードしたディレクトリにある .PublishSettings ファイルを選択します。このファイルは [発行] ダイアログ ボックスの値を自動的に設定します。
1. **[接続の検証]** をクリックしてファイルをテストします。
1. 検証に成功したら、**[発行]** をクリックします。発行が完了すると、新しいブラウザー タブが開き、Web サイトが表示されます。
1. メモのテキストを入力して、**[Create]** をクリックし、結果を確認します。  
![HelloMongoAzure][screen-mongolab-sampleapp]

<h2><a name="manage"></a>データベースの管理</h2>

[WACOM.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

ご利用ありがとうございます。これで、MongoLab がホスティングする MongoDB データベースに支えられた C# ASP.NET アプリケーションが公開されました。MongoLab データベースを利用していて、データベースに関する疑問または不明点がある場合や、MongoDB や C# ドライバーそのものに関するヘルプが必要な場合は、[support@mongolab.com](mailto:support@mongolab.com) にお問い合わせください。それではお元気で。

[screen-mongolab-sampleapp]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-sampleapp.png
[dialog-mongolab-vspublish]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-vspublish.png
[button-website-downloadpublishprofile]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-website-downloadpublishprofile.png
[screen-mongolab-websitedashboard]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-newwebsite.png
[button-new]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-new.png
[dialog-mongolab-csharp-newproject]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-newproject.png
[dialog-mongolab-csharp-projecttemplate]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-projecttemplate.png
[focus-mongolab-csharp-pmconsole]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/focus-mongolab-csharp-pmconsole.png
[button-store]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-store.png
[entry-mongolab]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/entry-mongolab.png 
[button-connectioninfo]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-connectioninfo.png
[screen-connectioninfo]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage


<!--HONumber=35_1-->
