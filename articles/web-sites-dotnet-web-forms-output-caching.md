<properties linkid="video-center-detail" urlDisplayName="詳細" pageTitle="ビデオ センターの詳細" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Azure の Web サイトで ASP.NET Web フォームの出力キャッシュを使用する方法" authors="jroth" solutions="" manager="" editor="" />



# Azure の Web サイトで ASP.NET Web フォームの出力キャッシュを使用する方法

ここでは、Azure のキャッシュ サービス (プレビュー) を使用して、ASP.NET Web フォームの ASP.NET ページ出力キャッシュをサポートする方法を説明します。ページ出力キャッシュは、指定した期間だけキャッシュ内のレンダリング ページを直接返す機能です。この機能は、更新頻度の低いページに頻繁にアクセスする場合に役立ちます。ASP.NET MVC アプリケーションはページ出力キャッシュをサポートしていないことに注意してください。

キャッシュ サービス (プレビュー) は、Web サイトの外部に分散キャッシュ サービスを提供します。これにより、Web サイトのすべてのインスタンスがキャッシュ内の同じレンダリング ページにアクセスできます。

キャッシュ サービス (プレビュー) を使用してページ出力キャッシュにアクセスする基本手順は次のとおりです。

* [キャッシュを作成します。](#createcache)
* [Azure のキャッシュを使用するように ASP.NET プロジェクトを構成します。](#configureproject)
* [web.config ファイルを修正します。](#configurewebconfig)
* [出力キャッシュを使用して、キャッシュされたページを一時的に返します。](#useoutputcaching)

<h2><a id="createcache"></a>キャッシュを作成する</h2>
1. Azure 管理ポータルの下部にある **[新規]** アイコンをクリックします。

	![[新規] アイコン][NewIcon]

2. **[データ サービス]**、**[キャッシュ]** の順に選択し、**[簡易作成]** をクリックします。

	![新規キャッシュ ダイアログ][NewCacheDialog]

3. キャッシュの一意な名前を **[エンドポイント]** ボックスに入力します。キャッシュに関するその他のプロパティを設定し、**[新しいキャッシュの作成]** をクリックします。

4. 管理ポータルで **[キャッシュ]** アイコンを選択します。すべてのキャッシュ サービス エンドポイントが表示されます。

	![キャッシュ アイコン][CacheIcon]

5. いずれかのキャッシュ サービス エンドポイントを選択すると、そのプロパティが表示されます。この後のセクションでは、**[ダッシュボード]** タブを使用して ASP.NET プロジェクトのキャッシュを構成します。

<h2><a id="configureproject"></a>ASP.NET プロジェクトを構成する</h2>
1. まず、**Azure SDK for .NET** の[最新バージョンをインストール][]していることを確認します。

2. Visual Studio の**ソリューション エクスプローラー**で目的の ASP.NET プロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します (WebMatrix を使用している場合は、ツール バーの **[NuGet]** をクリックします)。

3. **[オンライン検索]** ボックスに「**WindowsAzure.Caching**」と入力します。

	![NuGet ダイアログ][NuGetDialog]

4. **[Azure Caching]** パッケージを選択し、**[インストール]** をクリックします。

<h2><a id="configurewebconfig"></a>Web.Config ファイルを修正する</h2>
NuGet パッケージは、キャッシュに必要なアセンブリ参照を作成するほか、web.config ファイルにスタブ エントリを追加します。キャッシュ サービスを使用して ASP.NET ページ出力キャッシュにアクセスするには、web.config を一部修正する必要があります。

1. ASP.NET プロジェクトの **web.config** ファイルを開きます。

2. **caching** 要素と **outputCache** 要素が既に存在する場合は、それらをコメント アウト (または削除) します。

3. Azure Caching NuGet パッケージによって追加された **caching** 要素をコメント解除します。次のスクリーン ショットのようになります。

	![OutputConfig][OutputConfig]

4. 次に、**dataCacheClients** セクションを探します。**securityProperties** 子要素をコメント解除します。

	![CacheConfig][CacheConfig]

5. **autoDiscover** 要素で、**identifier** 属性にキャッシュのエンドポイント URL を設定します。エンドポイント URL を確認するには、Azure の管理ポータルでキャッシュのプロパティを参照します。**[ダッシュボード]** タブを開き、**[概要]** セクションの **[エンドポイント URL]** の値をコピーします。

	![エンドポイント URL][EndpointURL]

6. **messageSecurity** 要素で、**authorizationInfo** 属性にキャッシュのアクセス キーを設定します。アクセス キーを確認するには、Azure の管理ポータルで該当するキャッシュを選択します。下部のバーにある **[キーの管理]** アイコンをクリックします。**[プライマリ アクセス キー]** ボックスの横にあるコピー ボタンをクリックします。

	![キーの管理][ManageKeys]

<h2><a id="useoutputcaching"></a>出力キャッシュを使用する</h2>
最後に、出力キャッシュを使用する ASP.NET Web フォーム アプリケーションでページを構成します。そのためには、.ASPX ソースの先頭に **OutputCache** 属性を追加します。次に例を示します。

	<%@ OutputCache Duration="45" VaryByParam="*" %>

上の例では、ページを 45 秒間キャッシュに格納します。**VaryByParam** が "*" に設定されているので、別のクエリ パラメーターが渡された場合でも、キャッシュ内のこのページ出力は変更されません。一方、次の例では、"UserId" パラメーターの値ごとに別のバージョンのページがキャッシュされます。

	<%@ OutputCache Duration="45" VaryByParam="UserId" %>	

  
  
  
[最新バージョンをインストール]: http://www.windowsazure.com/ja-jp/downloads/?sdk=net
[NewIcon]: ./media/web-sites-web-forms-output-caching/CacheScreenshot_NewButton.PNG
[NewCacheDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CreateOptions.PNG
[CacheIcon]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheIcon.PNG
[NuGetDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG
[OutputConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG
[CacheConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG
[EndpointURL]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG
[ManageKeys]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG
  
  
  

