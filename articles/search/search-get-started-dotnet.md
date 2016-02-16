<properties
	pageTitle=".NET での Azure Search アプリケーションの初めての使用 | Microsoft Azure | ホスト型クラウド検索サービス"
	description="Azure Search .NET SDK の .NET クライアント ライブラリを使用して Visual Studio ソリューションをビルドする方法についてのチュートリアルです。"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# .NET での Azure Search アプリケーションの初めての使用

検索エクスペリエンスとして Azure Search を使用するカスタム .NET 検索アプリケーションを Visual Studio 2013 以降で作成する方法を説明します。このチュートリアルでは、[Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) と Azure Search サービス REST API を使用します。

このサンプルを実行するには、Azure Search サービスが必要です。このサービスには、[Azure ポータル](https://portal.azure.com)でサインアップできます。詳しい手順については、「[ポータルでの Azure Search サービスの作成](search-create-service-portal.md)」を参照してください。

## データについて

このサンプル アプリケーションでは、[米国地質調査所 (USGS)](http://geonames.usgs.gov/domestic/download_data.htm) からのデータをロードアイランド州でフィルター処理してデータ サイズを削減して使用します。このデータを使用して、病院や学校などの目立つ建物および河川、湖沼、山などの地理的特徴を返す検索アプリケーションを作成します。

このアプリケーションでは、**DataIndexer** プログラムは [インデクサー](https://msdn.microsoft.com/library/azure/dn798918.aspx) を使用してインデックスを作成して読み込み、パブリック Azure SQL Database からフィルター処理された USGS データセットを取得します。オンライン データ ソースに対する資格情報と接続情報は、ソース コードで提供されます。それ以上の構成は必要ありません。

> [AZURE.NOTE] このデータセットにフィルターを提供し、無料価格レベルのドキュメントを 10,000 件未満に制限しました。別の価格レベルを使用する場合、この制限は適用されません。各価格レベルの容量の詳細については、「[制限および制約](search-limits-quotas-capacity.md)」を参照してください。

<a id="sub-2"></a>
## Azure Search サービスのサービス名と API キーの取得 ##

サービスを作成したら、ポータルに戻って URL または `api-key` を取得します。Search サービスに接続するには、URL に加えて、呼び出しを認証するための `api-key` が必要になります。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ジャンプ バーで、**[Search サービス]** をクリックして、サブスクリプション用にプロビジョニングされたすべての Azure Search サービスの一覧を表示します。
3. 使用するサービスを選択します。
4. サービスのダッシュボードには、基本情報のタイルのほか、管理者キーにアクセスするためのキー アイコンが表示されます。

   ![][3]

3. サービスの URL と管理者キーをコピーします。これらは、後で Visual Studio プロジェクトの app.config および web.config ファイルに追加するときに必要になります。

## 新しいプロジェクトとソリューションの開始

このソリューションには次の 2 つのプロジェクトが含まれています。

- **DataIndexer** は、Visual C# のコンソール アプリケーションであり、データの読み込みに使用されます
- **SimpleSearchMVCApp** は、Visual C# の ASP.NET MVC Web アプリケーションであり、クエリを実行して検索結果を返すために使用されます

この手順では、両方のプロジェクトを作成します。

1. **Visual Studio** で、**[新しいプロジェクト]**、**[Visual C#]**、**[コンソール アプリケーション]** の順に選択します。
2. プロジェクトの名前を **DataIndexer** に、ソリューションの名前を **AzureSearchDotNetDemo** に設定します。
3. ソリューション エクスプローラーで、ソリューションを右クリックし、**[追加]**、**[新しいプロジェクト]**、**[Visual C#]**、**[ASP.NET Web アプリケーション]** の順に選択します。
4. プロジェクトの名前を **SimpleSearchMVCApp** に設定します。
5. 新しい ASP.NET プロジェクトで、MVC テンプレートを選択し、このチュートリアルで使用しないプログラム アーティファクトが作成されないようにオプションをオフにします。

   Azure ホストおよびユニット テストのチェック ボックスをオフにし、認証をなしに設定します。

   ![][10]

プロジェクトの作成が完了すると、ソリューションは次の例に示すようになります。

   ![][4]

## .NET クライアント ライブラリのインストールおよび他のパッケージの更新

1. ソリューション エクスプローラーでソリューションの **[NuGet パッケージの管理]** を右クリックします。
2. **[更新]**、**[安定版のみ]**、**[すべて更新]** を指定します。

   ![][11]

3. 追加パッケージのインストールを受け入れて、すべての依存関係もインストールされるようにします。

4. 次に、Azure Search .NET クライアント ライブラリをインストールします。検索を正しく指定してください。そうしないと、パッケージを簡単に検索できません。**[NuGet パッケージの管理]** を再び右クリックします。

5. **[オンライン]**、**[nuget.org]**、**[安定版パッケージのみ]** の順に指定し、*azure.search* を検索します。**[インストール]** をクリックして、ライブラリをインストールします。

   ![][12]

## System.Configuration へのアセンブリ参照の追加

**DataIndexer** は **System.Configuration** を使用して app.config の構成設定を読み取ります。

1. **[DataIndexer]** を右クリックし、**[追加]**、**[参照]**、**[フレームワーク]**、**[System.Configuration]** の順に選択します。チェック ボックスをオンにします。
2. **[OK]** をクリックします。

## 構成ファイルの更新

各プロジェクトには、サービス名と API キーを指定する構成ファイルが含まれています。

1. **DataIndexer** で、App.config を次の例に置き換え、[SERVICE NAME] および [SERVICE KEY] を実際のサービスに対して有効な値で更新します。サービス名は完全な URL ではないことに注意してください。たとえば、Search サービス エンドポイントが **https://mysearchsrv.search.microsoft.net* である場合、App.config に入力するサービス名は *mysearchsrv* です。

		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
		    <appSettings>
		      <add key="SearchServiceName" value="[SEARCH SERVICE]" />
		      <add key="SearchServiceApiKey" value="[SEARCH SERVICE API KEY]" />
		    </appSettings>
		</configuration>

2. **SimpleSearchMVCApp** で、Web.config を次の例に置き換え、[SERVICE NAME] および [SERVICE KEY] を実際のサービスに対して有効な値で再び更新します。

		<?xml version="1.0" encoding="utf-8"?>
		<!--
			For more information on how to configure your ASP.NET application, please visit
			http://go.microsoft.com/fwlink/?LinkId=152368
			-->
		<configuration>
			<configSections>
				<!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
				<section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=5.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
			</configSections>
			<connectionStrings>
				<add name="DefaultConnection" providerName="System.Data.SqlClient" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-SimpleMVCApp-20150303114355;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-SimpleMVCApp-20150303114355.mdf" />
			</connectionStrings>
			<appSettings>
				<add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
				<add key="SearchServiceApiKey" value="[API KEY]" />

				<add key="webpages:Version" value="2.0.0.0" />
				<add key="webpages:Enabled" value="false" />
				<add key="PreserveLoginUrl" value="true" />
				<add key="ClientValidationEnabled" value="true" />
				<add key="UnobtrusiveJavaScriptEnabled" value="true" />
			</appSettings>
			<system.web>
				<httpRuntime targetFramework="4.5" />
				<compilation debug="true" targetFramework="4.5" />
				<authentication mode="Forms">
					<forms loginUrl="~/Account/Login" timeout="2880" />
				</authentication>
				<pages>
					<namespaces>
						<add namespace="System.Web.Helpers" />
						<add namespace="System.Web.Mvc" />
						<add namespace="System.Web.Mvc.Ajax" />
						<add namespace="System.Web.Mvc.Html" />
						<add namespace="System.Web.Optimization" />
						<add namespace="System.Web.Routing" />
						<add namespace="System.Web.WebPages" />
					</namespaces>
				</pages>
				<profile defaultProvider="DefaultProfileProvider">
					<providers>
						<add name="DefaultProfileProvider" type="System.Web.Providers.DefaultProfileProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
					</providers>
				</profile>
				<membership defaultProvider="DefaultMembershipProvider">
					<providers>
						<add name="DefaultMembershipProvider" type="System.Web.Providers.DefaultMembershipProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" enablePasswordRetrieval="false" enablePasswordReset="true" requiresQuestionAndAnswer="false" requiresUniqueEmail="false" maxInvalidPasswordAttempts="5" minRequiredPasswordLength="6" minRequiredNonalphanumericCharacters="0" passwordAttemptWindow="10" applicationName="/" />
					</providers>
				</membership>
				<roleManager defaultProvider="DefaultRoleProvider">
					<providers>
						<add name="DefaultRoleProvider" type="System.Web.Providers.DefaultRoleProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
					</providers>
				</roleManager>
				<!--
								If you are deploying to a cloud environment that has multiple web server instances,
								you should change session state mode from "InProc" to "Custom". In addition,
								change the connection string named "DefaultConnection" to connect to an instance
								of SQL Server (including SQL Azure and SQL  Compact) instead of to SQL Server Express.
					-->
				<sessionState mode="InProc" customProvider="DefaultSessionProvider">
					<providers>
						<add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
					</providers>
				</sessionState>
			</system.web>
			<system.webServer>
				<validation validateIntegratedModeConfiguration="false" />
				<handlers>
					<remove name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" />
					<remove name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" />
					<remove name="ExtensionlessUrlHandler-Integrated-4.0" />
					<add name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness32" responseBufferLimit="0" />
					<add name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness64" responseBufferLimit="0" />
					<add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
					<remove name="OPTIONSVerbHandler" />
					<remove name="TRACEVerbHandler" />
				</handlers>
			</system.webServer>
			<entityFramework>
				<defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
					<parameters>
						<parameter value="v12.0" />
					</parameters>
				</defaultConnectionFactory>
			</entityFramework>
			<runtime>
				<assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
					<dependentAssembly>
						<assemblyIdentity name="Newtonsoft.Json" publicKeyToken="30ad4fe6b2a6aeed" culture="neutral" />
						<bindingRedirect oldVersion="0.0.0.0-7.0.0.0" newVersion="7.0.0.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="WebGrease" publicKeyToken="31bf3856ad364e35" culture="neutral" />
						<bindingRedirect oldVersion="0.0.0.0-1.6.5135.21930" newVersion="1.6.5135.21930" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="Antlr3.Runtime" publicKeyToken="eb42632606e9261f" culture="neutral" />
						<bindingRedirect oldVersion="0.0.0.0-3.5.0.2" newVersion="3.5.0.2" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.Helpers" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.WebPages" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.Mvc" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-5.2.3.0" newVersion="5.2.3.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.WebPages.Razor" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
					</dependentAssembly>
				</assemblyBinding>
			</runtime>
		</configuration>


## DataIndexer の変更

このプログラムは、app.config での指定に従って Search サービスに接続し、インデックスを作成して、Azure SQL Database に格納されている USGS データセットを読み込むコンソール アプリケーションです。このチュートリアルのこの部分にはインデクサーを使用します。

このプログラムを実行する前に、**Program.cs** を置き換える必要があります。これはインデックスおよびインデクサーの作成、データの読み込み、メッセージの書き込みに使用されます。

### Program.cs の更新

1. ソリューション エクスプローラーで、**DataIndexer** の **Program.cs** を開きます。
2. Program.cs の内容を次のコードに置き換えます。

	    using System;
	    using System.Configuration;
	    using System.Threading;
	    using Microsoft.Azure.Search;
	    using Microsoft.Azure.Search.Models;
	
	    namespace DataIndexer
	    {
	        class Program
	        {
	            private const string GeoNamesIndex = "geonames";
	            private const string UsgsDataSource = "usgs-datasource";
	            private const string UsgsIndexer = "usgs-indexer";
	
	            private static SearchServiceClient _searchClient;
	            private static SearchIndexClient _indexClient;
	
	            // This Sample shows how to delete, create, upload documents and query an index
	            static void Main(string[] args)
	            {
	                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
	                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
	
	                // Create an HTTP reference to the catalog index
	                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
	                _indexClient = _searchClient.Indexes.GetClient(GeoNamesIndex);
	
	                Console.WriteLine("{0}", "Deleting index, data source, and indexer...\n");
	                if (DeleteIndexingResources())
	                {
	                    Console.WriteLine("{0}", "Creating index...\n");
	                    CreateIndex();
	                    Console.WriteLine("{0}", "Sync documents from Azure SQL...\n");
	                    SyncDataFromAzureSQL();
	                }
	                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
	                Console.ReadKey();
	            }
	
	            private static bool DeleteIndexingResources()
	            {
	                // Delete the index, data source, and indexer.
	                try
	                {
	                    _searchClient.Indexes.Delete(GeoNamesIndex);
	                    _searchClient.DataSources.Delete(UsgsDataSource);
	                    _searchClient.Indexers.Delete(UsgsIndexer);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error deleting indexing resources: {0}\r\n", ex.Message);
	                    Console.WriteLine("Did you remember to add your SearchServiceName and SearchServiceApiKey to the app.config?\r\n");
	                    return false;
	                }
	
	                return true;
	            }
	
	            private static void CreateIndex()
	            {
	                // Create the Azure Search index based on the included schema
	                try
	                {
	                    var definition = new Index()
	                    {
	                        Name = GeoNamesIndex,
	                        Fields = new[] 
	                        { 
	                            new Field("FEATURE_ID",     DataType.String)         { IsKey = true,  IsSearchable = false, IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
	                            new Field("FEATURE_NAME",   DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("FEATURE_CLASS",  DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("STATE_ALPHA",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("STATE_NUMERIC",  DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("COUNTY_NAME",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("COUNTY_NUMERIC", DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("ELEV_IN_M",      DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("ELEV_IN_FT",     DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("MAP_NAME",       DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("DESCRIPTION",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
	                            new Field("HISTORY",        DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
	                            new Field("DATE_CREATED",   DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("DATE_EDITED",    DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true}
	                        }
	                    };
	
	                    _searchClient.Indexes.Create(definition);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error creating index: {0}\r\n", ex.Message);
	                }
	
	            }
	
	            private static void SyncDataFromAzureSQL()
	            {
	                // This will use the Azure Search Indexer to synchronize data from Azure SQL to Azure Search
	                Console.WriteLine("{0}", "Creating Data Source...\n");
	                var dataSource =
	                    new DataSource()
	                    {
	                        Name = UsgsDataSource,
	                        Description = "USGS Dataset",
	                        Type = DataSourceType.AzureSql,
	                        Credentials = new DataSourceCredentials("Server=tcp:azs-playground.database.windows.net,1433;Database=usgs;User ID=reader;Password=EdrERBt3j6mZDP;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;"),
	                        Container = new DataContainer("GeoNamesRI")
	                    };
	
	                try
	                {
	                    _searchClient.DataSources.Create(dataSource);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error creating data source: {0}", ex.Message);
	                    return;
	                }
	
	                Console.WriteLine("{0}", "Creating Indexer and syncing data...\n");
	
	                var indexer =
	                    new Indexer()
	                    {
	                        Name = UsgsIndexer,
	                        Description = "USGS data indexer",
	                        DataSourceName = dataSource.Name,
	                        TargetIndexName = GeoNamesIndex
	                    };
	
	                try
	                {
	                    _searchClient.Indexers.Create(indexer);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error creating and running indexer: {0}", ex.Message);
	                    return;
	                }
	
	                bool running = true;
	                Console.WriteLine("{0}", "Synchronization running...\n");
	                while (running)
	                {
	                    IndexerExecutionInfo status = null;
	
	                    try
	                    {
	                        status = _searchClient.Indexers.GetStatus(indexer.Name);
	                    }
	                    catch (Exception ex)
	                    {
	                        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
	                        return;
	                    }
	
	                    IndexerExecutionResult lastResult = status.LastResult;
	                    if (lastResult != null)
	                    {
	                        switch (lastResult.Status)
	                        {
	                            case IndexerExecutionStatus.InProgress:
	                                Console.WriteLine("{0}", "Synchronization running...\n");
	                                Thread.Sleep(1000);
	                                break;
	
	                            case IndexerExecutionStatus.Success:
	                                running = false;
	                                Console.WriteLine("Synchronized {0} rows...\n", lastResult.ItemCount);
	                                break;
	
	                            default:
	                                running = false;
	                                Console.WriteLine("Synchronization failed: {0}\n", lastResult.ErrorMessage);
	                                break;
	                        }
	                    }
	                }
	            }
	        }
	    }


## DataIndexer のビルドと実行

1. **DataIndexer** プロジェクトを右クリックします。
2. プロジェクトをビルドします。
3. **F5** キーを押して実行します。

コンソール ウィンドウに次のようなメッセージが表示されます。

![][6]

ポータルには、新しい **geonames** インデックスが表示されます。ポータルが最新の状態になるまでに数分かかることがあるので、しばらく待ってから結果を確認してください。

![][7]

## SimpleSearchMVCApp の変更

**SimpleSearchMVC** は、IIS Express でローカルに実行する Web アプリです。検索ボックスを提供し、検索結果を表形式で表示します。

このプログラムを実行する前に、3 つの変更を行います。

- **HomeController.cs** を置き換えます。これは、ユーザー入力を受け付けるために使用されます。この例では、検索語句は *q* という名前の変数に格納されます。

- **index.cshtml** を置き換えます。これは、検索語句の入力を提供して検索結果を表示する Web ページです。

- **FeatureSearch.cs** を追加します。これは、検索クライアントを作成して検索を実行するクラスです。

### HomeController.cs の更新

既定のコードを以下のコードに置き換えます。

	    using Microsoft.Azure.Search.Models;
	    using System;
	    using System.Collections.Generic;
	    using System.Linq;
	    using System.Web;
	    using System.Web.Mvc;
	
	    namespace SimpleSearchMVCApp.Controllers
	    {
	        public class HomeController : Controller
	        {
	            //
	            // GET: /Home/
	            private FeaturesSearch _featuresSearch = new FeaturesSearch();
	
	            public ActionResult Index()
	            {
	                return View();
	            }
	
	            public ActionResult Search(string q = "")
	            {
	                // If blank search, assume they want to search everything
	                if (string.IsNullOrWhiteSpace(q))
	                    q = "*";
	
	                return new JsonResult
	                {
	                    JsonRequestBehavior = JsonRequestBehavior.AllowGet,
	                    Data = _featuresSearch.Search(q).Results
	                };
	            }
	        }
	    }


### Index.cshtml の更新

既定のコードを以下のコードに置き換えます。

	@{
	    ViewBag.Title = "Azure Search - Feature Search";
	}

	<script src="http://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.2.min.js"></script>
	<script type="text/javascript">

	    $(function () {
	        // Execute search if user clicks enter
	        $("#q").keyup(function (event) {
	            if (event.keyCode == 13) {
	                Search();
	            }
	        });
	    });

	    function Search() {
	        // We will post to the MVC controller and parse the full results on the client side
	        // You may wish to do additional pre-processing on the data before sending it back to the client
	        var q = $("#q").val();

	        $.post('/home/search',
	        {
	            q: q
	        },
	        function (data) {
	            var searchResultsHTML = "<tr><td>FEATURE NAME</td><td>FEATURE CLASS</td>";
	            searchResultsHTML += "<td>STATE ALPHA</td><td>COUNTY_NAME</td>";
	            searchResultsHTML += "<td>Elevation (m)</td><td>Elevation (ft)</td><td>MAP NAME</td>";
	            searchResultsHTML += "<td>DESCRIPTION</td><td>HISTORY</td><td>DATE CREATED</td>";
	            searchResultsHTML += "<td>DATE EDITED</td></tr>";
	            for (var i = 0; i < data.length; i++) {
	                searchResultsHTML += "<td>" + data[i].Document.FEATURE_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.FEATURE_CLASS + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.STATE_ALPHA + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.COUNTY_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_M + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_FT + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.MAP_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.DESCRIPTION + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.HISTORY + "</td>";
	                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_CREATED) + "</td>";
	                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_EDITED) + "</td></tr>";
	            }

	            $("#searchResults").html(searchResultsHTML);

	        });

	        function parseJsonDate(jsonDateString) {
	            if (jsonDateString != null)
	                return new Date(parseInt(jsonDateString.replace('/Date(', '')));
	            else
	                return "";
	        }
	    };

	</script>
	<h2>USGS Search for Rhode Island</h2>

	<div class="container">
	    <input type="search" name="q" id="q" autocomplete="off" size="100" /> <button onclick="Search();">Search</button>
	</div>
	<br />
	<div class="container">
	    <div class="row">
	        <table id="searchResults" border="1"></table>
	    </div>
	</div>


### FeaturesSearch.cs の追加

アプリケーションに検索機能を提供するクラスを追加します。

1. ソリューション エクスプローラーで、**SimpleSearchMVCApp** を右クリックし、**[追加]**、**[新しい項目]**、**[コード]**、**[クラス]** の順に選択します。
2. クラスの名前を **FeaturesSearch** に設定します。
3. 既定のコードを以下のコードに置き換えます。


	    using System;
	    using System.Configuration;
	    using Microsoft.Azure.Search;
	    using Microsoft.Azure.Search.Models;
	
	    namespace SimpleSearchMVCApp
	    {
	        public class FeaturesSearch
	        {
	            private static SearchServiceClient _searchClient;
	            private static SearchIndexClient _indexClient;
	
	            public static string errorMessage;
	
	            static FeaturesSearch()
	            {
	                try
	                {
	                    string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
	                    string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
	
	                    // Create an HTTP reference to the catalog index
	                    _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
	                    _indexClient = _searchClient.Indexes.GetClient("geonames");
	                }
	                catch (Exception e)
	                {
	                    errorMessage = e.Message.ToString();
	                }
	            }
	
	            public DocumentSearchResult Search(string searchText)
	            {
	                // Execute search based on query string
	                try
	                {
	                    SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
	                    return _indexClient.Documents.Search(searchText, sp);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error querying index: {0}\r\n", ex.Message.ToString());
	                }
	                return null;
	            }
	
	        }
	    }


### スタートアップ プロジェクトとしての SimpleSearchMVCApp の設定

**SimpleSearchMVCApp** プロジェクトを右クリックし、スタートアップ プロジェクトとして設定します。

## SimpleSearchMVCApp のビルドと実行

このプログラムをビルドして実行すると、既定のブラウザーで Web ページが表示され、Azure Search サービスのインデックスに格納されている USGS データにアクセスするための検索ボックスが提供されます。

![][8]

### USGS データの検索

USGS データ セットには、ロードアイランド州に関連するレコードが含まれています。検索ボックスが空の状態で **[Search]** をクリックすると、既定で、上位 50 のエントリが取得されます。

検索語句を入力すると、検索を絞り込む条件が検索エンジンに提供されます。地域の名前を入力してみてください。*Roger Williams* はロードアイランド州の最初の州知事でした。多数の公園、建造物、および学校に彼の名前が付けられています。

![][9]

また、次のようなクエリを試し、語句や演算子を追加または削除して検索の範囲を変更してみてください。

- Pawtucket または Pembroke
- goose +cape -neck


## 次のステップ

これは、.NET と USGS データセットに基づく最初の Azure Search チュートリアルです。カスタム ソリューションで使用できる他の検索機能を紹介できるように、時間をかけてこのチュートリアルを拡張する予定です。

Azure Search についての知識が既にある場合は、このサンプルを基にして、サジェスター (先行入力またはオートコンプリート クエリ)、フィルター、ファセット ナビゲーションなどを試すことができます。また、件数を追加してドキュメントを一括処理することで検索結果の表示を改善し、ユーザーが結果をページ移動できるようにすることもできます。

Azure Search を初めて使用する場合は、 他のチュートリアルも試して、作成できるものについての理解を深めることをお勧めします。他のリソースについては、[ドキュメントのページ](https://azure.microsoft.com/documentation/services/search/)を参照してください。[ビデオとチュートリアルの一覧](search-video-demo-tutorial-list.md)のリンクから、さらに多くの情報にアクセスすることもできます。

<!--Image references-->
[1]: ./media/search-get-started-dotnet/create-search-portal-1.PNG
[2]: ./media/search-get-started-dotnet/create-search-portal-2.PNG
[3]: ./media/search-get-started-dotnet/create-search-portal-3.PNG

[4]: ./media/search-get-started-dotnet/AzSearch-DotNet-VSSolutionExplorer.png

[6]: ./media/search-get-started-dotnet/consolemessages.png
[7]: ./media/search-get-started-dotnet/portalindexstatus.png
[8]: ./media/search-get-started-dotnet/usgssearchbox.png
[9]: ./media/search-get-started-dotnet/rogerwilliamsschool.png

[10]: ./media/search-get-started-dotnet/AzSearch-DotNet-MVCOptions.PNG
[11]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-1.PNG
[12]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-2.PNG

<!---HONumber=AcomDC_0211_2016-->