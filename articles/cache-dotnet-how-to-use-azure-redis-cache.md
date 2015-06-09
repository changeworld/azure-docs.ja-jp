<properties
	pageTitle="Azure Redis Cache の使用方法"
	description="Azure Redis Cache を使用して Azure アプリケーションのパフォーマンスを向上させる方法を説明します "
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="cache"
	ms.workload="tbd"
	ms.tgt_pltfrm="cache-redis"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="sdanie"/>

# Azure Redis Cache の使用方法

このガイドでは、**Azure Redis Cache** の基本的な使用方法について説明します。サンプルは C# コードで記述され、.NET API を利用しています。紹介するシナリオは、**キャッシュの作成と構成**、**キャッシュ クライアントの構成**、**キャッシュでのオブジェクトの追加と削除**、**キャッシュへの ASP.NET セッション状態の格納**などです。Azure Redis Cache の使用方法の詳細については、「[次のステップ][]」を参照してください。

<a name="video"></a>
## Azure Redis Cache の概要 (ビデオ)
このビデオでは、Saurabh Pant と Scott Hanselman が Azure Redis Cache について紹介しています。(時間:7:45)  

> [AZURE.VIDEO azure-redis-cache-101-introduction-to-redis]

<a name="what-is"></a>
## Azure Redis Cache とは

Microsoft Azure Redis Cache は、広く支持されているオープン ソースの Redis Cache がベースとなっています。マイクロソフトによって管理されている、セキュリティで保護された専用の Redis cache にアクセスすることができます。Azure Redis Cache を使用して作成されたキャッシュには、Microsoft Azure 内のあらゆるアプリケーションからアクセスすることができます。

Microsoft Azure Redis Cache には、次の 2 つのレベルがあります。

-	**基本** - 単一ノード、複数のサイズ、最大 53 GB
-	**Standard** - 2 つのノード (プライマリ/レプリカ)。複数のサイズ、最大 53 GB99.9% の SLA。

各レベルは、機能と料金ごとに異なります。機能については、このガイドで後述します。料金の詳細については、「[キャッシュの料金詳細][]」を参照してください。

このガイドでは、Azure Redis Cache の基本的な概要について説明します。この概要ガイドでは扱われていない機能の詳細については、[Azure Redis Cache の概要][]を参照してください。

<a name="getting-started-cache-service"></a>
## Azure Redis Cache の使用

Azure Redis Cache の導入は簡単です。使い始めるには、キャッシュをプロビジョニングして構成します。次に、キャッシュ クライアントを構成してキャッシュにアクセスできるようにします。キャッシュ クライアントを構成すると、使い始めることができます。

-	[キャッシュの作成][]
-	[キャッシュ クライアントの構成][]

<a name="create-cache"></a>
## キャッシュの作成

キャッシュを作成するには、まず Azure 管理プレビュー ポータルにサインインし、**[新規]**、**[Redis Cache]** をクリックします。

![New cache][NewCacheMenu]

**[Redis Cache の新規作成]** ブレードで、必要なキャッシュ構成を指定します。

![Create cache][CacheCreate]

キャッシュ エンドポイントに使用するサブドメイン名を **[DNS 名]** に入力します。エンドポイントは、数字と小文字のみを含む、先頭が文字の 6 ～ 12 文字の文字列にしてください。

**[料金レベル]** を使用して、必要なキャッシュ サイズと機能を選択します。Redis Cache は、次の 2 つのレベルで利用できます。

-	**基本** - 単一ノード、複数のサイズ、最大 53 GB
-	**標準** - 2 つのノード (マスター/スレーブ)、99.9% SLA、複数のサイズ、最大 53 GB

**[サブスクリプション]** で、キャッシュに使用する Azure サブスクリプションを選択します。

>[AZURE.NOTE] アカウントにサブスクリプションが 1 つしかない場合、自動的に選択されるため、[サブスクリプション] ドロップダウン リストは表示されません。

**[リソース グループ]** で、キャッシュのリソース グループを選択または作成します。

>[AZURE.NOTE] 詳細については、「[リソース グループを使用した Azure リソースの管理][]」を参照してください。

**[位置情報]** を使用して、キャッシュのホストの地理的位置を指定します。パフォーマンスを最大限に引き出すために、キャッシュは、キャッシュ クライアント アプリケーションと同じリージョンに作成することを強くお勧めします。

新しいキャッシュ オプションを構成したら、**[作成]** をクリックします。キャッシュが作成されるまで数分かかる場合があります。状態を確認するには、スタートボードで進行状況を監視してください。キャッシュが作成されると、新しいキャッシュの状態が **実行中** になって、既定の設定で使用できるようになります。

![Cache created][CacheCreated]

作成されたキャッシュには、**[参照]** ブレードからアクセスすることができます。

![Browse blade][BrowseCaches]

**[キャッシュ]** をクリックしてキャッシュを表示します。

![Caches][Caches]

<a name="NuGet"></a>
## キャッシュ クライアントの構成

Azure Redis Cache を使用して作成されたキャッシュには、あらゆる Azure アプリケーションからアクセスすることができます。Visual Studio で開発した .NET アプリケーションであれば、**StackExchange.Redis** キャッシュ クライアントを使用できます。キャッシュ クライアント アプリケーションの構成は、NuGet パッケージを使用すると簡単です。

>[AZURE.NOTE] 詳細については、GitHub の [StackExchange.Redis][] に関するページと [StackExchange.Redis キャッシュ クライアントのドキュメント][]を参照してください。

Visual Studio で StackExchange.Redis NuGet パッケージを使用してクライアント アプリケーションを構成するには、**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

![Manage NuGet packages][NuGetMenu]

**[オンライン検索]** ボックスに「**StackExchange.Redis**」または「**StackExchange.Redis.StrongName**」と入力し、結果の中から必要なバージョンを選択して、**[インストール]** をクリックします。

>[AZURE.NOTE] 厳密な名前を持つバージョンの **StackExchange.Redis** クライアント ライブラリを希望する場合は、**[StackExchange.Redis.StrongName]** を選択してください。それ以外の場合は、**[StackExchange.Redis]** を選択します。

![StackExchange.Redis NuGet package][StackExchangeNuget]

クライアント アプリケーションから StackExchange.Redis キャッシュ クライアントを使用して Azure Redis Cache にアクセスするために必要なアセンブリ参照が NuGet パッケージによってダウンロードされ追加されます。

クライアント プロジェクトをキャッシュ用に構成できたら、以降のセクションで説明されている、キャッシュを操作するための技法を使用できます。

<a name="working-with-caches"></a>
## キャッシュの操作

このセクションの手順では、キャッシュに対する一般的なタスクを行う方法について説明します。

-	[キャッシュに接続する][]
-   [オブジェクトをキャッシュに追加する、キャッシュから削除する][]
-   [ASP.NET セッション状態をキャッシュに格納する][]

<a name="connect-to-cache"></a>
## キャッシュに接続する

プログラムでキャッシュを操作するには、キャッシュへの参照が必要です。StackExchange.Redis クライアントを使用して Azure Redis Cache にアクセスするすべてのファイルの先頭に次のコードを追加します。

    using StackExchange.Redis;

>[AZURE.NOTE] StackExchange.Redis クライアントには、.NET Framework 4 以降が必要です。

Azure Redis Cache への接続には、 `ConnectionMultiplexer` クラスを使用します。このクラスはクライアント アプリケーションの開始から終了まで共有、再利用する前提で設計されており、操作単位で作成する必要はありません。

Azure Redis Cache に接続して、接続済みの `ConnectionMultiplexer` インスタンスを取得するには、次の例のように、静的 `Connect` メソッドを呼び出して、キャッシュのエンドポイントとキーを渡します。password パラメーターには、サービス ポータルから生成された Azure キーを使用してください。

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

>[AZURE.NOTE] 警告:ソース コード内に資格情報を保存することは絶対に避けてください。このサンプルでは、単純化するためにあえてソース コード内に記述しています。「[Windows Azure Web サイト: アプリケーション文字列と接続文字列の動作][]」を参照してください。

SSL を使用しない場合は、 `ssl=false` を設定するか、または単にエンドポイントとキーを指定してください。

>[AZURE.NOTE] 既定では、新しいキャッシュに対して非 SSL ポートは無効になっています。非 SSL ポートを有効にする手順については、「[Azure Redis Cache でのキャッシュの構成][]」を参照してください。

	connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,password=...");

>[AZURE.NOTE] 高度な接続構成オプションの詳細については、[StackExchange.Redis][] の構成モデルを参照してください。

キャッシュのエンドポイントとキーは、ご利用のキャッシュ インスタンスの Azure 管理プレビュー ポータル ブレードから取得できます。

![Cache properties][CacheProperties]

![Manage keys][ManageKeys]

接続が確立されたら、 `ConnectionMultiplexer.GetDatabase` メソッドを呼び出して、Redis Cache データベースへの参照を返します。

	// connection referes to a previously configured ConnectionMultiplexer
	IDatabase cache = connection.GetDatabase();

>[AZURE.NOTE]  `GetDatabase` メソッドから返されるオブジェクトは、手付かずで受け渡しされる軽量のオブジェクトであり、保存する必要はありません。

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

	IDatabase cache = connection.GetDatabase();

	// Perform cache operations using the cache object...
	// Simple put of integral data types into the cache
	cache.StringSet("key1", "value");
	cache.StringSet("key2", 25);

	// Simple get of data types from the cache
	string key1 = cache.StringGet("key1");
	int key2 = (int)cache.StringGet("key2");

以上、Azure Redis Cache インスタンスに接続して、キャッシュ データベースへの参照を取得する方法を説明しました。今度は実際にキャッシュを使用してみましょう。



<a name="add-object"></a>
## オブジェクトをキャッシュに追加する、キャッシュから削除する

キャッシュに項目を格納する、またはキャッシュから項目を取得するには、 `StringSet` メソッドと `StringGet` メソッドを使用します。

	// If key1 exists, it is overwritten.
	cache.StringSet("key1", "value1");

	string value = cache.StringGet("key1");

>[AZURE.NOTE] Redis では、ほとんどのデータが Redis 文字列として保存されますが、これらの文字列には、さまざまなデータ型を格納することができます。シリアル化したバイナリ データもその 1 つで、.NET のオブジェクトをキャッシュに保存する際に使用することができます。

 `StringGet` を呼び出すと、オブジェクトが存在する場合はそのオブジェクトが返され、存在しない場合は null が返されます。その場合、目的のデータ ソースから値を取得してキャッシュに格納しておき、後で使用することができます。これを "キャッシュ アサイド パターン" といいます。

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

>[AZURE.NOTE] Azure Redis Cache はプリミティブ データ型に加え、.NET オブジェクトをキャッシュできますが、.NET オブジェクトをキャッシュするためには、あらかじめシリアル化しておく必要があります。この処理はアプリケーション開発者が行わなければなりません。逆にそのことでシリアライザーの選択に幅が生まれ、開発者にとってのメリットとなっています。詳細については、「[キャッシュ内で .NET オブジェクトを使用する][]」を参照してください。

<a name="specify-expiration"></a>
## キャッシュ内の項目の有効期限を指定する

キャッシュ内の項目の有効期限を指定するには、 `StringSet` の `TimeSpan` パラメーターを使用します。

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));


<a name="store-session"></a>
## ASP.NET セッション状態をキャッシュに格納する

Azure Redis Cache には、セッション状態プロバイダーが用意されています。セッション状態プロバイダーを使用すると、セッション状態を、メモリ内や SQL Server データベースにではなく、キャッシュに格納することができます。キャッシュ セッション
状態プロバイダーを使用するには、まず対象のキャッシュを構成し、Redis Cache Session State NuGet パッケージを使用して、キャッシュに必要な構成を ASP.NET アプリケーションに対して行います。

Visual Studio で Redis Cache Session State NuGet パッケージを使用してクライアント アプリケーションを構成するには、**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

![Manage NuGet packages][NuGetMenu]

**[オンライン検索]** ボックスに「**RedisSessionStateProvider**」と入力し、結果の中からそのプロバイダーを選択して、**[インストール]** をクリックします。

![Redis Cache Session State NuGet Package][SessionStateNuGet]

NuGet パッケージによって、必要なアセンブリ参照がダウンロードされて追加されます。さらに、web.config ファイルには、ASP.NET アプリケーションが Redis Cache Session State プロバイダーを使用するために必要な構成を記述した以下のセクションが追加されます。

  <sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />
      </providers>
    </sessionState>



コメント化されたセクションには、属性の例とそのサンプル設定が記述されています。

属性の構成には、プレビュー ポータルのキャッシュ ブレードからの値を使用してください。その他の値は適宜構成します。

	<sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="contoso5.redis.cache.windows.net"
		accessKey="..." ssl="true" />
      </providers>
    </sessionState>

標準の **InProc** セッション状態プロバイダーは必ずコメント アウトしてください。

    <!-- <sessionState mode="InProc" customProvider="DefaultSessionProvider">
      <providers>
        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
      </providers>
    </sessionState> -->

Azure Redis Session State プロバイダーの構成と使用の詳細については、「[Azure Redis セッション状態プロバイダー][]」を参照してください。

<a name="next-steps"></a>
## 次のステップ

これで、Azure Redis Cache の基本を学習できました。さらに複雑なキャッシュ タスクを実行する方法については、次のリンク先を参照してください。

-	[キャッシュ診断の有効化](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics)によってキャッシュの正常性を[監視](https://msdn.microsoft.com/library/azure/dn763945.aspx)できるようにします。ポータルではメトリックを表示できますが、お好みのツールを使用し、それを[ダウンロードして確認](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)することも可能です。
-	StackExchange.Redis クライアントの詳細:[StackExchange.Redis キャッシュ クライアントのドキュメント][]
	-	Azure Redis Cache は、さまざまな Redis クライアントや開発言語からアクセスできます。詳細については、[http://redis.io/clients][] および [他の言語での Azure Redis Cache の開発][]に関するページを参照してください。
	-	Azure Redis Cache は、Redsmin などのサービスと共に使用することもできます。詳細については、[Azure Redis 接続文字列を取得し、Redsmin と共に使用する方法][]に関するページを参照してください。
-	[redis][] のドキュメント、[redis のデータ型][]に関するページ、[redis のデータ型の概念に関するページ][]を参照してください。
-   MSDN リファレンス:[Azure Redis Cache][]


<!-- INTRA-TOPIC LINKS -->
[次のステップ]: #next-steps
[Azure Redis Cache の概要 (ビデオ)]: #video
[Azure Redis Cache とは]: #what-is
[Azure Cache の作成]: #create-cache
[最適なキャッシュの種類]: #choosing-cache
[Azure Cache を使用するための Visual Studio プロジェクトの準備]: #prepare-vs
[Caching を使用するようにアプリケーションを構成する]: #configure-app
[Azure Redis Cache の使用]: #getting-started-cache-service
[キャッシュの作成]: #create-cache
[キャッシュの構成]: #enable-caching
[キャッシュ クライアントの構成]: #NuGet
[キャッシュの操作]: #working-with-caches
[キャッシュに接続する]: #connect-to-cache
[オブジェクトをキャッシュに追加する、キャッシュから削除する]: #add-object
[キャッシュ内のオブジェクトの有効期限を指定する]: #specify-expiration
[ASP.NET セッション状態をキャッシュに格納する]: #store-session


<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png

[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png

[CacheCreated]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png





<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[他の言語での Azure Redis Cache の開発]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Azure Redis 接続文字列を取得し、Redsmin と共に使用する方法]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis セッション状態プロバイダー]: http://go.microsoft.com/fwlink/?LinkId=398249
[Azure 管理ポータル]: http://windows.azure.com/
[方法: プログラムを使ってキャッシュ クライアントを構成する]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Azure Cache のセッション状態プロバイダー]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: セッション状態のキャッシュ]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Azure Cache 用キャッシュ プロバイダーの出力]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[チーム ブログ]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[仮想マシンのサイズを構成する方法]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Cache の容量計画に関する考慮事項]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[方法: 宣言によって ASP.NET ページのキャッシュ可能性を設定する]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[方法: プログラムによってページのキャッシュ可能性を設定する]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Azure Redis Cache でのキャッシュの構成]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[StackExchange.Redis 構成モデル]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[キャッシュ内で .NET オブジェクトを使用する]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet パッケージ マネージャーのインストール]: http://go.microsoft.com/fwlink/?LinkId=240311
[キャッシュの料金詳細]: http://www.windowsazure.com/pricing/details/cache/
[管理ポータル]: https://manage.windowsazure.com/

[Azure Redis Cache の概要]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Azure Redis Cache への移行]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache のサンプル]: http://go.microsoft.com/fwlink/?LinkId=320840
[リソース グループを使用した Azure リソースの管理]: http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[StackExchange.Redis キャッシュ クライアントのドキュメント]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[redis のデータ型]: http://redis.io/topics/data-types
[redis のデータ型の概念に関するページ]: http://redis.io/topics/data-types-intro

[Windows Azure Web サイト: アプリケーション文字列と接続文字列の動作]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/

<!--HONumber=49-->
