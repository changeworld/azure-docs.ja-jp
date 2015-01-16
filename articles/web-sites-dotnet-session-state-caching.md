<properties urlDisplayName="index" pageTitle="Azure Websites での ASP.NET セッション状態の使用" metaKeywords="azure cache service session state" description="Azure Cache Service を使用して ASP.NET のセッション状態のキャッシュをサポートする方法について説明します。" metaCanonical="" services="cache" documentationCenter=".NET" title="How to Use ASP.NET Session State with Azure Websites" authors="riande"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="riande" />


# Azure Websites で ASP.NET セッション状態を使用する方法

*著者: [Rick Anderson](https://twitter.com/RickAndMSFT)(更新日: 2014 年 7 月 1 日)。*

このトピックでは、Azure Redis Cache Service (プレビュー) を使用してセッション状態をサポートする方法について説明します。

ASP.NET Web アプリケーションでセッション状態を使用している場合は、外部セッション状態プロバイダー (Redis Cache Service または SQL Server セッション状態プロバイダー) を構成する必要があります。セッション状態を使用し、外部プロバイダーを使用しない場合は、Web アプリケーションの 1 つのインスタンスに制限されます。Redis Cache Service は、最も高速で最も簡単に実現できるキャッシュ サービスです。

キャッシュ サービス (プレビュー) を使用してセッション状態キャッシュをサポートする基本的な手順は次のとおりです。

* [キャッシュを作成します。](#createcache)
* [RedisSessionStateProvider NuGet パッケージを Web アプリケーションに追加します。](#configureproject)
* [web.config ファイルを修正します。](#configurewebconfig)
* [セッション オブジェクトを使用して、キャッシュされたアイテムを保存および取得します。](#usesessionobject)

<h2><a id="createcache"></a>キャッシュを作成する</h2>
[ここにある手順](http://azure.microsoft.com/ja-jp/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache)に従ってキャッシュを作成します。

<h2><a id="configureproject"></a>RedisSessionStateProvider NuGet パッケージを Web アプリケーションに追加します。</h2>
`RedisSessionStateProvider` NuGet パッケージをインストールします。パッケージ マネージャー コンソール (**[ツール]**、**[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択) からインストールするには、次のコマンドを使用します。

  `PM> Install-Package RedisSessionStateProvider -IncludePrerelease`
  
**[ツール]**、**[NuGet パッケージ マネージャー]**、**[ソリューションの NugGet パッケージの管理]** の順に選択してインストールするには、`RedisSessionStateProvider` を検索し、**[プレリリースを含める]** を必ず指定します。

詳細については、[NuGet RedisSessionStateProvider のページ](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ )および[キャッシュ クライアントの構成に関するセクション](http://azure.microsoft.com/ja-jp/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet)を参照してください。

<h2><a id="configurewebconfig"></a>Web.Config ファイルを修正する</h2>
キャッシュに必要なアセンブリ参照の作成に加え、NuGet パッケージは *web.config* ファイルにスタブ エントリを追加します。 

1. *web.config* を開き、**sessionState** 要素を見つけます。

1. `host`、`accessKey`、`port` (SSL ポートは 6380 とする必要があります) の値を入力し、`SSL` を `true` に設定します。これらの値は、ご利用のキャッシュ インスタンスの Azure 管理プレビュー ポータル ブレードから取得できます。詳細については、「[キャッシュに接続する](http://azure.microsoft.com/ja-jp/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache)」を参照してください。
次のマークアップは、*web.config* ファイルに対する変更を示しています。


  <pre class="prettyprint">  
    <system.web>
    <customErrors mode="Off" />
    <authentication mode="None" />
    <compilation debug="true" targetFramework="4.5" />
    <httpRuntime targetFramework="4.5" />
  <sessionState mode="Custom" customProvider="RedisSessionProvider">
      <providers>  
          <!--<add name="RedisSessionProvider" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
          />-->
         <add name="RedisSessionProvider" 
              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
              <mark>port="6380"
              host="movie2.redis.cache.windows.net" 
              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
              ssl="true"</mark> />
      <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />-->
      </providers>
    </sessionState>
  </system.web></pre>


<h2><a id="usesessionobject"></a>コードでセッション オブジェクトを使用する</h2>
最後に、ASP.NET コードでセッション オブジェクトを使用します。**Session.Add** メソッドを使用して、セッション状態にオブジェクトを追加します。このメソッドではキーと値のペアに基づいて、セッション状態キャッシュにアイテムが格納されます。

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

次のコードは、セッション状態からこの値を取得します。

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;	

Redis Cache を使用して、Web アプリケーションのオブジェクトをキャッシュすることもできます。詳細については、「[MVC movie app with Azure Redis Cache in 15 minutes (Azure Redis Cache を使用した MVC ムービー アプリを 15 分でデプロイする)](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/)」を参照してください。
ASP.NET セッション状態の詳しい使用方法については、「[ASP.NET セッション状態の概要][]」を参照してください。

  
  
  [最新バージョンのインストール]: http://www.windowsazure.com/ja-jp/downloads/?sdk=net  
  [ASP.NET セッション状態の概要]: http://msdn.microsoft.com/ja-jp/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
