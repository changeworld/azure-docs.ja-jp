<properties linkid="video-center-index" urlDisplayName="索引" pageTitle="ビデオ センターの索引" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Azure の Web サイトで ASP.NET セッション状態を使用する方法" authors=""  solutions="" writer="jroth" manager="" editor=""  />




# Azure の Web サイトで ASP.NET セッション状態を使用する方法

ここでは、Azure のキャッシュ サービス (プレビュー) を使用して、ASP.NET セッション状態キャッシュをサポートする方法を説明します。

外部プロバイダーがない場合、セッション状態は、そのサイトをホストしている Web サーバー上のインプロセスに格納されます。Azure の Web サイトでは、インプロセスのセッション状態には 2 つの問題があります。まず、複数のインスタンスを持つサイトで、1 つのインスタンスに保存されているセッション状態は他のインスタンスからアクセスできません。ユーザー要求はどのインスタンスに割り当てられるかわらかないので、そのインスタンスにセッション情報があるとは限りません。次に、構成を変更すると、まったく別のサーバー上で Web サイトが実行されるようになります。

キャッシュ サービス (プレビュー) は、Web サイトの外部に分散キャッシュ サービスを提供します。これにより、インプロセス セッション状態の問題を解決できます。セッション状態の詳しい使用方法については、「[ASP.NET セッション状態の概要][]」を参照してください。

キャッシュ サービス (プレビュー) を使用してセッション状態キャッシュをサポートする基本的な手順は次のとおりです。

* [キャッシュを作成します。](#createcache)
* [Azure のキャッシュを使用するように ASP.NET プロジェクトを構成します。](#configureproject)
* [web.config ファイルを修正します。](#configurewebconfig)
* [セッション オブジェクトを使用して、キャッシュされたアイテムを保存および取得します。](#usesessionobject)

<h2><a id="createcache"></a>キャッシュを作成する</h2>
1. Azure の管理ポータルの下部にある **[新規]** アイコンをクリックします。

	![NewIcon][NewIcon]

2. **[データ サービス]**、**[キャッシュ]** の順に選択し、**[簡易作成]** をクリックします。

	![NewCacheDialog][NewCacheDialog]

3. キャッシュの一意な名前を **[エンドポイント]** ボックスに入力します。キャッシュに関するその他のプロパティを設定し、**[新しいキャッシュの作成]** をクリックします。

4. 管理ポータルで **[キャッシュ]** アイコンを選択します。すべてのキャッシュ サービス エンドポイントが表示されます。

	![CacheIcon][CacheIcon]

5. いずれかのキャッシュ サービス エンドポイントを選択すると、そのプロパティが表示されます。この後のセクションでは、**[ダッシュボード]** タブを使用して ASP.NET プロジェクトのキャッシュを構成します。

<h2><a id="configureproject"></a>ASP.NET プロジェクトを構成する</h2>
1. まず、**Azure SDK for .NET** の[最新バージョンをインストール][]していることを確認します。

2. Visual Studio の**ソリューション エクスプローラー**で目的の ASP.NET プロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します (WebMatrix を使用している場合は、ツール バーの [**NuGet**] をクリックします)。

3. **[オンライン検索]** ボックスに、「**WindowsAzure.Caching**」と入力します。

	![NuGetDialog][NuGetDialog]

4. **[Azure Caching]** パッケージを選択し、**[インストール]** をクリックします。

<h2><a id="configurewebconfig"></a>Web.Config ファイルを修正する</h2>
キャッシュに必要なアセンブリ参照の作成に加え、NuGet パッケージは web.config ファイルにスタブ エントリを追加します。キャッシュ サービスを使用してセッション状態にアクセスするには、web.config を一部修正する必要があります。

1. ASP.NET プロジェクトの **web.config** を開きます。

2. **sessionState** 要素が既に存在する場合は、これをコメント アウト (または削除) します。

3. Azure Caching NuGet パッケージによって追加された **sessionState** 要素をコメント解除します。次のスクリーン ショットのようになります。

	![SessionStateConfig][SessionStateConfig]

4. 次に、**dataCacheClients** セクションを探します。**securityProperties** 子要素をコメント解除します。

	![CacheConfig][CacheConfig]

5. **autoDiscover** 要素で、**identifier** 属性にキャッシュのエンドポイント URL を設定します。エンドポイント URL を確認するには、Azure の管理ポータルでキャッシュのプロパティを参照します。**[ダッシュボード]** タブを開き、**[概要]** セクションの **[エンドポイント URL]** の値をコピーします。

	![EndpointURL][EndpointURL]

6. **messageSecurity** 要素で、**authorizationInfo** 属性にキャッシュのアクセス キーを設定します。アクセス キーを確認するには、Azure の管理ポータルで該当するキャッシュを選択します。下部のバーにある **[キーの管理]** アイコンをクリックします。**[プライマリ アクセス キー]** ボックスの横にあるコピー ボタンをクリックします。

	![ManageKeys][ManageKeys]

<h2><a id="usesessionobject"></a>コードでセッション オブジェクトを使用する</h2>
最後に、ASP.NET コードでセッション オブジェクトを使用します。**Session.Add** メソッドを使用して、セッション状態にオブジェクトを追加します。このメソッドではキーと値のペアに基づいて、セッション状態キャッシュにアイテムが格納されます。

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

次のコードは、セッション状態からこの値を取得します。

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;	

ASP.NET セッション状態の詳しい使用方法については、「[ASP.NET セッション状態の概要][]」を参照してください。

  
  
  
  [最新バージョンをインストール]: http://www.windowsazure.com/ja-jp/downloads/?sdk=net  
  [ASP.NET セッション状態の概要]: http://msdn.microsoft.com/ja-jp/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png

