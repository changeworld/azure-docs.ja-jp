<properties linkid="video-center-index" urlDisplayName="index" pageTitle="Use ASP.NET session state with Azure Websites" metaKeywords="azure cache service session state" description="Learn how to use the Azure Cache Service to support ASP.NET session state caching." metaCanonical="" services="cache" documentationCenter=".NET" title="How to Use ASP.NET Session State with Azure Websites" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra" />

# Azure Websites で ASP.NET セッション状態を使用する方法

著者 [Rick Anderson][Rick Anderson](更新日: 2014 年 7 月 1 日)

このトピックでは、Azure Redis Cache Service (プレビュー) を使用してセッション状態をサポートする方法について説明します。

ASP.NET Web アプリケーションでセッション状態を使用している場合は、外部セッション状態プロバイダー (Redis Cache Service または SQL Server セッション状態プロバイダー) を構成する必要があります。セッション状態を使用し、外部プロバイダーを使用しない場合は、Web アプリケーションの 1 つのインスタンスに制限されます。Redis Cache Service は、最も高速で最も簡単に実現できるキャッシュ サービスです。

キャッシュ サービス (プレビュー) を使用してセッション状態キャッシュをサポートする基本的な手順は次のとおりです。

-   [キャッシュを作成します。][キャッシュを作成します。]
-   [RedisSessionStateProvider NuGet パッケージを Web アプリケーションに追加します。][RedisSessionStateProvider NuGet パッケージを Web アプリケーションに追加します。]
-   [web.config ファイルを修正します。][web.config ファイルを修正します。]
-   [セッション オブジェクトを使用して、キャッシュされたアイテムを保存および取得します。][セッション オブジェクトを使用して、キャッシュされたアイテムを保存および取得します。]

## <span id="createcache"></span></a>キャッシュを作成する

[ここにある手順][ここにある手順]に従ってキャッシュを作成します。

## <span id="configureproject"></span></a>RedisSessionStateProvider NuGet パッケージを Web アプリケーションに追加する

NuGet `RedisSessionStateProvider` パッケージをインストールします。パッケージ マネージャー コンソールからインストールするには、**[ツール]** \> **[NuGet パッケージ マネージャー]** \> **[パッケージ マネージャー コンソール]** コマンドを使用します。

`PM> Install-Package RedisSessionStateProvider -IncludePrerelease`

**[ツール]** \> **[NuGet パッケージ マネージャー]** \> **[ソリューションの NugGet パッケージの管理]** からインストールするには、`RedisSessionStateProvider` を検索し、**[プレリリースを含める]** を必ず指定します。

詳細については、[NuGet RedisSessionStateProvider のページ][NuGet RedisSessionStateProvider のページ]および「[キャッシュ クライアントの構成][キャッシュ クライアントの構成]」を参照してください。

## <span id="configurewebconfig"></span></a>Web.Config ファイルを修正する

キャッシュに必要なアセンブリ参照の作成に加え、NuGet パッケージは *web.config* ファイルにスタブ エントリを追加します。

1.  *web.config* を開き、**sessionState** 要素を見つけます。

2.  `host`、`accessKey`、`port` (SSL ポートは 6380 とする必要があります) の値を入力し、`SSL` を `true` に設定します。これらの値は、ご利用のキャッシュ インスタンスの Azure 管理プレビュー ポータル ブレードから取得できます。詳細については、「[キャッシュに接続する][キャッシュに接続する]」を参照してください。
    次のマークアップは、*web.config* ファイルに対する変更を示しています。

<pre class="prettyprint">
 &lt;system.web&gt; &lt;customErrors mode=&quot;Off&quot; /&gt; &lt;authentication mode=&quot;None&quot; /&gt; &lt;compilation debug=&quot;true&quot; targetFramework=&quot;4.5&quot; /&gt; &lt;httpRuntime targetFramework=&quot;4.5&quot; /&gt; &lt;sessionState mode=&quot;Custom&quot; customProvider=&quot;RedisSessionProvider&quot;&gt; &lt;providers&gt; &lt;!--&lt;add name=&quot;RedisSessionProvider&quot; host = &quot;127.0.0.1&quot; [String] port = &quot;&quot; [number] accessKey = &quot;&quot; [String] ssl = &quot;false&quot; [true|false] throwOnError = &quot;true&quot; [true|false] retryTimeoutInMilliseconds = &quot;0&quot; [number] databaseId = &quot;0&quot; [number] applicationName = &quot;&quot; [String] /&gt;--&gt; &lt;add name=&quot;RedisSessionProvider&quot; type=&quot;Microsoft.Web.Redis.RedisSessionStateProvider&quot; port=&quot;6380&quot; host=&quot;movie2.redis.cache.windows.net&quot; accessKey=&quot;m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=&quot; ssl=&quot;true&quot; /&gt; &lt;!--&lt;add name=&quot;MySessionStateStore&quot; type=&quot;Microsoft.Web.Redis.RedisSessionStateProvider&quot; host=&quot;127.0.0.1&quot; accessKey=&quot;&quot; ssl=&quot;false&quot; /&gt;--&gt; &lt;/providers&gt; &lt;/sessionState&gt; &lt;/system.web&gt;
</pre>

</p>
## <span id="usesessionobject"></span></a>コードでセッション オブジェクトを使用する

最後に、ASP.NET コードでセッション オブジェクトを使用します。**Session.Add** メソッドを使用して、セッション状態にオブジェクトを追加します。このメソッドではキーと値のペアに基づいて、セッション状態キャッシュにアイテムが格納されます。

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

次のコードは、セッション状態からこの値を取得します。

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;  

Redis Cache を使用して、Web アプリケーションのオブジェクトをキャッシュすることもできます。詳細については、「[MVC movie app with Azure Redis Cache in 15 minutes (Azure Redis Cache を使用した MVC ムービー アプリを 15 分でデプロイする)][MVC movie app with Azure Redis Cache in 15 minutes (Azure Redis Cache を使用した MVC ムービー アプリを 15 分でデプロイする)]」を参照してください。
ASP.NET セッション状態の使用方法の詳細については、「[ASP.NET セッション状態の概要][ASP.NET セッション状態の概要]」を参照してください。

  [Rick Anderson]: https://twitter.com/RickAndMSFT
  [キャッシュを作成します。]: #createcache
  [RedisSessionStateProvider NuGet パッケージを Web アプリケーションに追加します。]: #configureproject
  [web.config ファイルを修正します。]: #configurewebconfig
  [セッション オブジェクトを使用して、キャッシュされたアイテムを保存および取得します。]: #usesessionobject
  [ここにある手順]: http://azure.microsoft.com/ja-jp/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache
  [NuGet RedisSessionStateProvider のページ]: http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/
  [キャッシュ クライアントの構成]: http://azure.microsoft.com/ja-jp/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet
  [キャッシュに接続する]: http://azure.microsoft.com/ja-jp/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache
  [ASP.NET セッション状態の概要]: http://msdn.microsoft.com/ja-jp/library/ms178581.aspx
