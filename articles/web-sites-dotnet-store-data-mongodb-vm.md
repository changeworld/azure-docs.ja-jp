<properties urlDisplayName="Website with MongoDB VM" pageTitle="仮想マシン上の MongoDB を使用した .NET Web サイト - Azure" metaKeywords="Azure Git ASP.NET MongoDB, Git .NET, Git MongoDB, ASP.NET MongoDB, Azure MongoDB, Azure ASP.NET, Azure tutorial" description="A tutorial that teaches you how to use Git to deploy an ASP.NET app to an Azure website connected to MongoDB on a virtual machine." metaCanonical="" services="web-sites,virtual-machines" documentationCenter=".NET" title="Create an Azure website that connects to MongoDB running on a virtual machine in Azure" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />


# Azure の仮想マシンで実行される MongoDB に接続する Azure Web サイトを作成する

Git を使用すると、ASP.NET アプリケーションを Azure Web サイトにデプロイできます。このチュートリアルでは、Azure の仮想マシンで実行されている MongoDB データベースに接続する、ASP.NET MVC の単純なフロントエンド タスク一覧アプリケーションをビルドします。[MongoDB][MongoDB] は、高いパフォーマンスを特徴とし、広く普及しているオープン ソースの NoSQL データベースです。開発用コンピューターで ASP.NET アプリケーションを実行してテストした後、Git を使用してアプリケーションを Azure Web サイトにアップロードします。

[WACOM.INCLUDE [create-account-and-websites-and-vms-note](../includes/create-account-and-websites-and-vms-note.md)]



##概要##

このチュートリアルでは、次のことについて説明します。

- [仮想マシンを作成して MongoDB をインストールする](#virtualmachine)
- [開発用コンピューターで My Task List ASP.NET アプリケーションを作成して実行する](#createapp)
- [Azure の Web サイトを作成する](#createwebsite)
- [Git を使用して Web サイトに ASP.NET アプリケーションをデプロイする](#deployapp)


##背景知識##

このチュートリアルでは次の事項に関する知識があると楽ですが、必須ではありません。

* MongoDB の C# ドライバー。MongoDB 用 C# アプリケーションの開発の詳細については、MongoDB の [CSharp Language Center][MongoC#LangCenter] を参照してください。 
* ASP .NET Web アプリケーション フレームワーク。詳細については、[ASP.net の Web サイト][ASP.NET]を参照してください。
* ASP .NET MVC Web アプリケーション フレームワーク。詳細については、[ASP.NET MVC の Web サイト][MVCWebSite]を参照してください。
* Azure。詳細については、[Azure][WindowsAzure] サイトを参照してください。


##準備##

このセクションでは、Azure の仮想マシンを作成し、MongoDB をインストールして、開発環境を設定する方法について説明します。

<a id="virtualmachine"></a> 
###仮想マシンを作成して MongoDB をインストールする###

このチュートリアルは、Azure に仮想マシンが作成済みであることを前提としています。仮想マシンの作成後、仮想マシンに MongoDB をインストールする必要があります。

* Windows 仮想マシンを作成して MongoDB をインストールする方法については、「[Install MongoDB on a virtual machine running Windows Server in Azure (Azure 上で Windows Server を実行する仮想マシンへの MongoDB のインストール)][InstallMongoOnWindowsVM]」を参照してください。
* 別の方法として、Linux 仮想マシンを作成して MongoDB をインストールする方法については、「[Install MongoDB on a virtual machine running CentOS Linux in Azure (Azure 上で CentOS Linux を実行する仮想マシンへの MongoDB のインストール)][InstallMongoOnCentOSLinuxVM]」を参照してください。

Azure に仮想マシンを作成して MongoDB をインストールしたら、仮想マシンの DNS 名 (たとえば "testlinuxvm.cloudapp.net") とエンドポイントで指定した MongoDB 用の外部ポートを忘れずに記録してください。この情報は後で必要になります。

### Visual Studio のインストール###

最初に、[Visual Studio Express 2013 for Web] [VSEWeb] または [Visual Studio 2013] [VSUlt] をインストールして実行します。

Visual Studio は IDE (統合開発環境) です。ドキュメントの作成に Microsoft Word を使用するのと同じように、アプリケーションの作成には IDE を使用します。このチュートリアルでは Microsoft Visual Studio 2013 を使用しますが、Microsoft Visual Studio の無料版である Microsoft Visual Studio Express 2013 を使用することもできます。

<a id="createapp"></a>
##開発用コンピューターで My Task List ASP.NET アプリケーションを作成して実行する##

このセクションでは、Visual Studio を使用して "My Task List" という ASP.NET アプリケーションを作成します。このアプリケーションはローカルで実行されますが、Azure 上の仮想マシンに接続し、そこに作成した MongoDB インスタンスを使用します。

###アプリケーションを作成する###
Visual Studio で、**[新しいプロジェクト]** をクリックします。

![Start Page New Project][StartPageNewProject]

**[新しいプロジェクト]** ウィンドウの左側のウィンドウで、**[Visual C#]** を選択し、**[Web]** を選択します。中央のウィンドウで、**[ASP.NET Web アプリケーション]** を選択します。ウィンドウの下部で、プロジェクトに「MyTaskListApp」という名前を付け、**[OK]** を選択します。

![New Project Dialog][NewProjectMyTaskListApp]

**[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[MVC]**を選択し、**[OK]** をクリックします。

![Select MVC Template][VS2013SelectMVCTemplate]

プロジェクトが完成すると、テンプレートから作成された既定のページが表示されます。

![Default ASP.NET MVC Application][VS2013DefaultMVCApplication]

###MongoDB C# ドライバーをインストールする

MongoDB では、ドライバーを通じてクライアント側の C# アプリケーションをサポートしています。このドライバーをローカルの開発用コンピューターにインストールする必要があります。C# ドライバーは NuGet から入手できます。

MongoDB C# ドライバーをインストールするには、以下を実行します。

1. **ソリューション エクスプローラー**で、**MyTaskListApp** プロジェクトの下の **[参照]** を右クリックし、**[NuGet パッケージの管理]** をクリックします。

	![Manage NuGet Packages][VS2013ManageNuGetPackages]

2. **[NuGet パッケージの管理]** ウィンドウの左側のウィンドウで、**[オンライン]** をクリックします。右側の**[オンラインで検索]** ボックスに「mongocsharpdriver」と入力します。**[インストール]** をクリックして、ドライバーをインストールします。

	![Search for MongoDB C# Driver][SearchforMongoDBCSharpDriver]

3. **[同意する]** をクリックして、10gen, Inc. のライセンス条項に同意します。

4. ドライバーがインストールされたら、**[閉じる]** をクリックします。
	![MongoDB C# Driver Installed][MongoDBCsharpDriverInstalled]


これで MongoDB C# ドライバーがインストールされました。プロジェクトには、**MongoDB.Driver.dll** ライブラリと **MongoDB.Bson.dll** ライブラリへの参照が追加されています。

![MongoDB C# Driver References][MongoDBCSharpDriverReferences]

###モデルを追加する###
**ソリューション エクスプローラー**で、*Models* フォルダーを右クリックし、**[追加]**、新しい  **[クラス]** の順にクリックし、名前を「*TaskModel.cs*」にします。*TaskModel.cs* で、既存のコードを次のコードに置き換えます。

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MongoDB.Bson.Serialization.Attributes;
	using MongoDB.Bson.Serialization.IdGenerators;
	using MongoDB.Bson;
	
	namespace MyTaskListApp.Models
	{
	    public class MyTask
	    {
	        [BsonId(IdGenerator = typeof(CombGuidGenerator))]
	        public Guid Id { get; set; }
	
	        [BsonElement("Name")]
	        public string Name { get; set; }
	
	        [BsonElement("Category")]
	        public string Category { get; set; }
	
	        [BsonElement("Date")]
	        public DateTime Date { get; set; }
	
	        [BsonElement("CreatedDate")]
	        public DateTime CreatedDate { get; set; }
	
	    }
	}

###データ アクセス レイヤーを追加する###
**ソリューション エクスプローラー**で、*MyTaskListApp* プロジェクトを右クリックし、**[追加]**、**[新しいフォルダー]** の順にクリックし、名前を *DAL* にします。*DAL* フォルダーを右クリックし、**[追加]**、新しい **[クラス]** の順にクリックします。クラス ファイルに *Dal.cs* という名前を付けます。*Dal.cs* で、既存のコードを次のコードに置き換えます。

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MyTaskListApp.Models;
	using MongoDB.Driver;
	using System.Configuration;
	
	namespace MyTaskListApp
	{
	    public class Dal : IDisposable
	    {
	        private MongoServer mongoServer = null;
	        private bool disposed = false;
	
	        // To do: update the connection string with the DNS name
			// or IP address of your server. 
			//For example, "mongodb://testlinux.cloudapp.net"
	        private string connectionString = "mongodb://<vm-dns-name>";
	
	        // This sample uses a database named "Tasks" and a 
			//collection named "TasksList".  The database and collection 
			//will be automatically created if they don't already exist.
	        private string dbName = "Tasks";
	        private string collectionName = "TasksList";
	
	        // Default constructor.        
	        public Dal()
	        {
	        }        
	
	        // Gets all Task items from the MongoDB server.        
	        public List<MyTask> GetAllTasks()
	        {
	            try
	            {
	                MongoCollection<MyTask> collection = GetTasksCollection();
	                return collection.FindAll().ToList<MyTask>();
	            }
	            catch (MongoConnectionException)
	            {
	                return new List<MyTask >();
	            }
	        }
	
	        // Creates a Task and inserts it into the collection in MongoDB.
	        public void CreateTask(MyTask task)
	        {
	            MongoCollection<MyTask> collection = GetTasksCollectionForEdit();
	            try
	            {
	                collection.Insert(task, SafeMode.True);
	            }
	            catch (MongoCommandException ex)
	            {
	                string msg = ex.Message;
	            }
	        }
	
	        private MongoCollection<MyTask> GetTasksCollection()
	        {
	            MongoServer server = MongoServer.Create(connectionString);
	            MongoDatabase database = server[dbName];
	            MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
	        }
	
	        private MongoCollection<MyTask> GetTasksCollectionForEdit()
	        {
	            MongoServer server = MongoServer.Create(connectionString);
	            MongoDatabase database = server[dbName];
	            MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
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

###コントローラーを追加する###
**ソリューション エクスプローラー**で、*Controllers\HomeController.cs* ファイルを開いて、既存のコードを次のコードに置き換えます。

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.Mvc;
	using MyTaskListApp.Models;
	using System.Configuration;
	
	namespace MyTaskListApp.Controllers
	{
	    public class HomeController : Controller, IDisposable
	    {
	        private Dal dal = new Dal();
	        private bool disposed = false;
	        //
	        // GET: /MyTask/
	
	        public ActionResult Index()
	        {
	            return View(dal.GetAllTasks());
	        }
	
	        //
	        // GET: /MyTask/Create
	
	        public ActionResult Create()
	        {
	            return View();
	        }
	
	        //
	        // POST: /MyTask/Create
	
	        [HttpPost]
	        public ActionResult Create(MyTask task)
	        {
	            try
	            {
	                dal.CreateTask(task);
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

###サイト スタイルを設定する###
ページ上部のタイトルを変更するには、**ソリューション エクスプローラー**で *Views\Shared\\_Layout.cshtml* ファイルを開き、ナビゲーション ヘッダーの "Application name" を、次のように "My Task List Application" に置き換えます。

 	@Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

タスク一覧メニューを設定するために、*\Views\Home\Index.cshtml* ファイルを開き、既存のコードを次のコードに置き換えます。
	
	@model IEnumerable<MyTaskListApp.Models.MyTask>
	
	@{
	    ViewBag.Title = "My Task List";
	}
	
	<h2>My Task List</h2>
	
	<table border="1">
	    <tr>
	        <th>Task</th>
	        <th>Category</th>
	        <th>Date</th>
	        
	    </tr>
	
	@foreach (var item in Model) {
	    <tr>
	        <td>
	            @Html.DisplayFor(modelItem => item.Name)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Category)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Date)
	        </td>
	        
	    </tr>
	}
	
	</table>
	<div>  @Html.Partial("Create", new MyTaskListApp.Models.MyTask())</div>


To add the ability to create a new task, right-click the *Views\Home\\* folder and **Add** a **View**.  Name the view *Create*. Replace the code with the following:

	@model MyTaskListApp.Models.MyTask
	
	<script src="@Url.Content("~/Scripts/jquery-1.10.2.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
	
	@using (Html.BeginForm("Create", "Home")) {
	    @Html.ValidationSummary(true)
	    <fieldset>
	        <legend>New Task</legend>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Name)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Name)
	            @Html.ValidationMessageFor(model => model.Name)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Category)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Category)
	            @Html.ValidationMessageFor(model => model.Category)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Date)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Date)
	            @Html.ValidationMessageFor(model => model.Date)
	        </div>
	
	        <p>
	            <input type="submit" value="Create" />
	        </p>
	    </fieldset>
	}

**ソリューション エクスプローラー**の表示は次のようになります。

![Solution Explorer][SolutionExplorerMyTaskListApp]

###MongoDB 接続文字列を設定する###
**ソリューション エクスプローラー**で、*DAL/Dal.cs* ファイルを開きます。次のコード行を見つけます。

	private string connectionString = "mongodb://<vm-dns-name>";

`<vm-dns-name>` を、このチュートリアルの「[仮想マシンを作成して MongoDB をインストールする][]」で作成した MongoDB を実行する仮想マシンの DNS 名に置き換えます。仮想マシンの DNS 名を確認するには、Azure の管理ポータルで **[仮想マシン]** をクリックし、**[DNS 名]** の値を確認します。

仮想マシンの DNS 名が "testlinuxvm.cloudapp.net" で、MongoDB が既定のポート 27017 をリッスンしている場合、その接続文字列のコード行は次のようになります。

	private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

仮想マシンのエンドポイントで MongoDB 用に別の外部ポートが指定されている場合は、接続文字列で次のようにしてポートを指定できます。

 	private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

MongoDB の接続文字列の詳細については、[MongoDB の接続に関するページ][MongoConnectionStrings]を参照してください。

###ローカル デプロイメントをテストする###

開発用コンピューターでアプリケーションを実行するには、**[デバッグ]** メニューの **[デバッグ開始]** をクリックするか、**F5** キーを押します。IIS Express が起動し、ブラウザーが開いて、アプリケーションのホーム ページが表示されます。新しいタスクを追加でき、そのデータは Azure の仮想マシンで実行されている MongoDB データベースに追加されます。

![My Task List Application][TaskListAppBlank]

<h2>Azure の Web サイトに ASP.NET Web アプリケーションをデプロイする</h2>

このセクションでは、Web サイトを作成し、Git を使用して My Task List ASP.NET アプリケーションをデプロイします。

<a id="createwebsite"></a> 
###Azure の Web サイトを作成する###
このセクションでは、Azure Web サイトを作成します。

1. Web ブラウザーを開き、[Azure の管理ポータル][AzurePortal]にアクセスします。Azure のアカウントを使用してサインインします。 
2. ページの下部で、**[+新規]**、**[Web サイト]**、**[簡易作成]** の順にクリックします。
3. アプリケーションの URL 用として一意のプレフィックスを入力します。
4. リージョンを選択します。
5. **[Web サイトの作成]** をクリックします。

![Create a new web site][WAWSCreateWebSite]

6. Web サイトがすぐに作成され、**[Web サイト]** に表示されます。

![WAWSDashboardMyTaskListApp][WAWSDashboardMyTaskListApp]

<a id="deployapp"></a> 
###Git を使用して Web サイトに ASP.NET アプリケーションをデプロイする
このセクションでは、Git を使用して My Task List アプリケーションをデプロイします。

1. **[Web サイト]** で Web サイトの名前をクリックし、**[ダッシュボード]** をクリックします。右側の [概要] で、**[ソース管理からのデプロイの設定]** をクリックします。
2. **[ソース コードの位置]** ページで、**[ローカル Git リポジトリ]** を選択し、**[次へ]** の矢印をクリックします。 
3. Git リポジトリがすぐに作成されます。次のセクションで使用するため、表示されたページの指示を書き留めてください。

	![Git Repository is Ready][Image9]

4. **[ローカル ファイルを Azure にプッシュする]** の下に、Azure にコードをプッシュする手順が表示されます。手順は次の図のようになります。

	![Push local files to Azure][Image10]
	
5. Git をまだインストールしていない場合は、ステップ 1 の**"こちらで入手してください"** というリンクを使用してインストールします。
6. ステップ 2 の手順に従って、ローカル ファイルをコミットします。  
7. ステップ 3 の手順に従って、リモート Azure リポジトリを追加し、ファイルを Azure Web サイトにプッシュします。
8. デプロイが完了したら、次のような確認メッセージが表示されます。

	![Deployment Complete][Image11]

9. これで Azure Web サイトが使用可能になりました。**[ダッシュボード]** ページでサイトを確認し、**[サイトの URL]** フィールドでサイトの URL を確かめます。このチュートリアルの手順に従うと、サイトは次の URL で利用できるようになっています:: http://mytasklistapp.azurewebsites.net.

##まとめ##

これで、Azure Web サイトに ASP .NET アプリケーションをデプロイできました。サイトを表示するには、**[ダッシュボード]** ページで **[サイトの URL]** フィールドのリンクをクリックします。MongoDB 用 C# アプリケーションの開発の詳細については、[CSharp Language Center のページ][MongoC#LangCenter]を参照してください。 


<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnCentOSLinuxVM]: /ja-jp/manage/linux/common-tasks/mongodb-on-a-linux-vm/
[InstallMongoOnWindowsVM]: /ja-jp/manage/windows/common-tasks/install-mongodb/
[VSEWeb]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
[VSUlt]: http://www.microsoft.com/visualstudio/eng/2013-downloads

<!-- IMAGES -->


[StartPageNewProject]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
[NewProjectMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
[VS2013SelectMVCTemplate]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
[VS2013DefaultMVCApplication]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
[VS2013ManageNuGetPackages]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
[SearchforMongoDBCSharpDriver]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
[MongoDBCsharpDriverInstalled]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
[MongoDBCSharpDriverReferences]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
[SolutionExplorerMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
[TaskListAppBlank]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
[WAWSCreateWebSite]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
[WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
[Image9]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
[Image10]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
[Image11]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png

<!-- TOC BOOKMARKS -->
[仮想マシンを作成して MongoDB をインストールする]: #virtualmachine
[開発用コンピューターで My Task List ASP.NET アプリケーションを作成して実行する]: #createapp
[Azure の Web サイトを作成する]: #createwebsite
[Git を使用して Web サイトに ASP.NET アプリケーションをデプロイする]: #deployapp

<!--HONumber=35.1-->
