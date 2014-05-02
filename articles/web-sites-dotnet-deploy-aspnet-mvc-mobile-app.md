<properties linkid="develop-dotnet-aspnet-mvc-4-mobile-website" urlDisplayName="ASP.NET MVC 4 モバイル Web サイト" pageTitle=".NET ASP.NET MVC 4 モバイル Web サイト - Azure チュートリアル" metaKeywords="Azure チュートリアル, Azure Web アプリケーション チュートリアル, Azure モバイル アプリケーション, Azure ASP.NET MVC 4, ASP.NET MVC" description="ASP.NET MVC 4 Web アプリケーションのモバイル機能を使用して Web アプリケーションを Azure の Web サイトに展開する方法を示すチュートリアル。" metaCanonical="" services="web-sites" documentationCenter=".NET" title="ASP.NET MVC モバイル Web アプリケーションを Azure の Web サイトに展開する" authors="tdykstra" solutions="" manager="" editor="" />





# ASP.NET MVC モバイル Web アプリケーションを Azure の Web サイトに展開する

***執筆者: [Rick Anderson](https://twitter.com/RickAndMSFT) 更新日: 2013 年 6 月 26 日。***

このチュートリアルでは、Azure の Web サイトに Web アプリケーションを展開する方法について基本を説明します。このチュートリアルの目的上、ASP.NET MVC 4 Web アプリケーションのモバイル機能を取り上げます。このチュートリアルの手順を実行するには、Microsoft Visual Studio 2012 を使用します。[Visual Studio Express 2012][] を使用してもかまいません。これは Microsoft Visual Studio の無料版です。

<h2>学習内容: </h2>

- ASP.NET MVC 4 テンプレートで HTML5 ビューポート属性とアダプティブ レンダリングを使用してモバイル デバイス上での表示品質を高める方法
- モバイル専用のビューを作成する方法
- ユーザーがアプリケーションのモバイル ビューとデスクトップ ビューを切り替えることのできるビュー切り替え機能を作成する方法
- Web アプリケーションを Azure に展開する方法

このチュートリアルでは、スタート プロジェクトに用意されている単純な会議一覧アプリケーションにモバイル機能を追加します。次のスクリーンショットは、完成したアプリケーションのメイン ページを Windows 7 Phone Emulator で表示したものです。

![MVC4 会議アプリケーションのメイン ページ][AppMainPage]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

<h2>開発環境の設定</h2>

.NET Framework 対応の Azure SDK をインストールして、開発環境を設定します。

1. Azure SDK for .NET をインストールするには、次のリンクをクリックします。Visual Studio 2012 をまだインストールしていない場合は、次のリンクをクリックするとインストールされます。このチュートリアルには、Visual Studio 2012 が必要です。
[Azure SDK for Visual Studio 2012]( http://go.microsoft.com/fwlink/?LinkId=254364)
1. インストール プログラムの実行または保存を求めるメッセージが表示されたら、**[実行]** をクリックします。
1. Web Platform Installer のウィンドウで、**[インストール]** をクリックし、インストールの手順を進めます。

![Web Platform Installer -  Azure SDK for .NET][WebPIAzureSdk20NetVS12]

モバイル ブラウザー エミュレーターも必要です。次のいずれでも動作します。

- [Windows 7 Phone Emulator][Win7PhoneEmulator] (このチュートリアルではスクリーンショットの大半でこのエミュレーターを使用しています)。
- ユーザー エージェント文字列を変更して iPhone をエミュレートします。How-To Geek の[このブログ記事][setuseragent]を参照してください。
- [Opera Mobile Emulator][OperaMobileEmulator]。
- [Apple Safari][AppleSafari] (ユーザー エージェントを iPhone に設定)。Safari のユーザー エージェントを "iPhone" に設定する方法については、David Alison のブログの「[How to let Safari pretend it's IE (Safari に IE のふりをさせる方法)][HowToSafari]」を参照してください。
- [FireFox][FireFox] ([User Agent Switcher][FireFoxUserAgentSwitcher] アドオンを搭載)。

このチュートリアルでは、コードを C# で示します。ただし、スタート プロジェクトと完成したプロジェクトは Visual Basic でも利用できます。このトピック用に Visual Basic と C# のソース コードを使用した Visual Studio プロジェクトが用意されています。

- [スタート プロジェクトのダウンロード][MVC4StarterProject]
- [完成したプロジェクトのダウンロード][FinishedProject]

<h2>このチュートリアルの手順</h2>

- [Azure の Web サイトの作成](#bkmk_CreateWebSite)
- [スタート プロジェクトを設定する](#bkmk_setupstarterproject)
- [ビュー、レイアウト、および部分ビューをオーバーライドする][]
- [jQuery Mobile を使用してモバイル ブラウザー インターフェイスを定義する][]
- [スピーカー一覧を強化する][]
- [モバイル スピーカー ビューを作成する][]
- [タグ一覧を強化する][]
- [日付一覧を強化する][]
- [SessionsTable ビューを強化する][]
- [SessionByCode ビューを強化する][]
- [Azure の Web サイトにアプリケーションを展開する][]

<h3><a name="bkmk_CreateWebSite"></a>Azure に Web サイトを作成する</h3>

Azure の Web サイトは、共有ホスティング環境で実行されます。つまり、他の Azure クライアントと共有する仮想マシン (VM) 上で実行されます。共有ホスティング環境は、低コストでクラウドの利用を開始できる方法です。後で Web トラフィックが増加したら、アプリケーションの規模を変更して専用 VM 上で実行するように設定してニーズを満たすことができます。もっと複雑なアーキテクチャが必要な場合は、Azure のクラウド サービスに移行できます。クラウド サービスは専用 VM 上で実行され、ユーザーのニーズに応じて構成できます。

1.	[Azure 管理ポータル][managementportal]へのログオン管理ポータルで **[新規]** をクリックします。

	![][CreateWebSite1]

2.	****[Web サイト]**、**[簡易作成] の順にクリックします。

	![][CreateWebSite2]

3.	**[新しい Web サイトを作成する]** で、アプリケーションの一意の URL として使用する文字列を **[URL]** ボックスに入力します。

	![][CreateWebSite3]

	URL 全体は、ここで入力した内容に、テキスト ボックスの下に表示されるサフィックスを追加して構成されます。図には "MyMobileMVC4WebSite" と表示されていますが、その URL が既に取得されている場合は、別の URL を選択する必要があります。自分が住んでいる**リージョン**を選択します。

4. 終了したら、ダイアログ ボックスの下部にあるチェック マークをクリックします。

管理ポータルが [Web サイト] ページに戻り、[状態] 列にサイトが作成中であることが示されます。しばらくすると (通常は 1 分未満)、サイトの作成に成功したことが [状態] 列に示されます。左側にあるナビゲーション バーでは、アカウントで所有するサイト数が [Web サイト] アイコンに表示され、データベース数が [SQL データベース] アイコンに表示されます。

![][CreateWebSite4]

<h3><a name="bkmk_setupstarterproject"></a>スタート プロジェクトを設定する</h3>

1.	[会議一覧アプリケーションのスタート プロジェクト][MVC4StarterProject]をダウンロードします。

2. 	Windows エクスプローラーで MvcMobileStarterBeta.zip ファイルを右クリックし、*[プロパティ]* をクリックします。

3. 	[MvcMobileRTMStarter.zip のプロパティ] ダイアログ ボックスで、[ブロックの解除] をクリックします (ブロックを解除すると、Web からダウンロードした .zip ファイルを使おうとしたときに表示されるセキュリティに関する警告を回避できます)。

	![[プロパティ] ダイアログ ボックス][PropertiesPopup]

4.	MvcMobile.zip ファイルを右クリックし、[すべて展開] をクリックしてファイルを解凍します。

5. 	Visual Studio で、MvcMobile.sln ファイルを開きます。

<h3>スタート プロジェクトを実行するには</h3>

1.	Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。アプリケーションがデスクトップ ブラウザーに表示されます。
2.	モバイル ブラウザー エミュレーターを起動して、会議アプリケーションの URL をエミュレーターにコピーし、[Browse by tag] リンクをクリックします。
	- Windows Phone Emulator を使用している場合は、URL バー内をクリックして Pause キーを押すと、キーボードにアクセスできます。次の図は、AllTags ビューです ([Browse by tag] を選択した後)。

	![[Browse by tag] ページ][BrowseByTagWithCallout]

モバイル デバイス上でも読みやすい表示になっています。[ASP.NET] リンクをクリックします。

![ASP.NET というタグの付いたセッションの閲覧][ASPNetPage]

ASP.NET タグ ビューはまったく整理されていません。たとえば、[Date] 列は読み取りにくくなっています。後で、モバイル ブラウザー専用バージョンの表示が読みやすくなった AllTags ビューを作成します。

<h2><a name="bkmk_overrideviews"></a>ビュー、レイアウト、および部分ビューをオーバーライドする</h2>

このセクションでは、モバイル専用のレイアウト ファイルを作成します。

ASP.NET MVC 4 の重要な新機能として、モバイル ブラウザー全般、個々のモバイル ブラウザー、または特定のブラウザー向けに (レイアウトと部分ビューを含む) 任意のビューをオーバーライドする単純なメカニズムがあります。モバイル専用ビューを用意するには、ビュー ファイルをコピーして .Mobile をファイル名に追加します。たとえば、モバイル インデックス ビューを作成するには、*Views\Home\Index.cshtml* をコピーして名前を *Views\Home\Index.Mobile.cshtml* に変更します。

最初に、*Views\Shared\\_Layout.cshtml* をコピーして名前を *Views\Shared\\_Layout.Mobile.cshtml* に変更します。*_Layout.Mobile.cshtml* を開き、タイトルを **MVC4 Conference** から **Conference (Mobile)** に変更します。

各 **Html.ActionLink** 呼び出しで、各 ActionLink リンクの「Browse by」を削除します。次のコードは、モバイル レイアウト ファイルの完成した body セクションです。

     <body>
        <div class="page">
            <div id="header">
                <div id="logindisplay"></div>
                <div id="title">
                    <h1> Conference (Mobile)</h1>
                </div>
                <div id="menucontainer">
                    <ul id="menu">
                        <li>@Html.ActionLink("Home", "Index", "Home")</li>
                        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
                        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
                        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
                    </ul>
                </div>
            </div>
            <div id="main">
                @RenderBody()
            </div>
            <div id="footer">
            </div>
        </div>
    </body>

*Views\Home\AllTags.cshtml* ファイルをコピーして名前を *Views\Home\AllTags.Mobile.cshtml* に変更します。新しいファイルを開き、次のように &lt;h2&gt; 要素を「Tags」から「Tags (M)」に変更します。

     <h2>Tags (M)</h2>

デスクトップ ブラウザー、および、モバイル ブラウザー エミュレーターを使用してタグ ページに移動します。モバイル ブラウザー エミュレーターでは、2 か所が変更されていることがわかります。

![変更されたタグ ページの表示][Overrideviews1]

対照的に、デスクトップでの表示は変更されていません。

![デスクトップのタグ ビューの表示][Overrideviews2]

<h2><a name="bkmk_usejquerymobile"></a>jQuery Mobile を使用してモバイル ブラウザー インターフェイスを定義する</h2>

このセクションでは、jQuery.Mobile.MVC NuGet パッケージをインストールします。これにより jQuery Mobile とビュー切り替えウィジェットがインストールされます。

[jQuery Mobile][jquerydocs] ライブラリは、すべての主要モバイル ブラウザーで動作するユーザー インターフェイス フレームワークです。jQuery Mobile は、CSS と JavaScript をサポートするモバイル ブラウザーに機能強化を漸進的に適用します。漸進的な機能強化により、すべてのブラウザーで Web ページの基本コンテンツを表示し、もっと強力なブラウザーおよびデバイスでは高機能の表示にすることができます。jQuery Mobile に含まれる JavaScript ファイルと CSS ファイルでは、マークアップを変更することなくモバイル ブラウザーに多くの要素が適合するようにスタイルが設定されています。

1. 先に作成した *Shared\\_Layout.Mobile.cshtml* ファイルを削除します。

2. *Views\Home\AllTags.Mobile.cshtml* ファイルの名前を *Views\Home\AllTags.Mobile.cshtml.hide* に変更します (このファイルは後でもう一度使用します)。ファイル名の拡張子が .cshtml でなくなったため、*AllTags* ビューを描画するために ASP.NET MVC ランタイムでこのファイルが使用されることはありません。

3. 次に示している手順を実行して、jQuery.Mobile.MVC NuGet パッケージをインストールします。

	a. **[ツール]** メニューの **[パッケージ マネージャー コンソール]** をポイントし、**[ライブラリ パッケージ マネージャー]** をクリックします。

		![ライブラリ パッケージ マネージャー][jquery1]
	
	b. **パッケージ マネージャー コンソール**で、「*Install-Package jQuery.Mobile.MVC -version 1.0.0*」と入力します。

		![パッケージ マネージャー コンソール][jquery2]

jQuery.Mobile.MVC NuGet パッケージは以下をインストールします。

- *App_Start\BundleMobileConfig.cs* ファイル。追加された jQuery JavaScript ファイルおよび CSS ファイルを参照するために必要です。後の指示に従って、このファイルに定義されているモバイル バンドルを参照する必要があります。
- jQuery Mobile CSS ファイル。
- ViewSwitcher コントローラー ウィジェット (*Controllers\ViewSwitcherController.cs*)。
- jQuery Mobile JavaScript ファイル。
- jQuery Mobile スタイル レイアウト ファイル (*Views\Shared\_Layout.Mobile.cshtml*)。
- ビュー切り替え部分ビュー (*MvcMobile\Views\Shared\_ViewSwitcher.cshtml*)。各ページの上部にデスクトップ ビューとモバイル ビューを相互に切り替えるリンクを表示します。
- Content\images フォルダーの .png および .gif イメージ ファイル。

*Global.asax* ファイルを開き、Application_Start メソッドの最終行として次のコードを追加します。

 	BundleMobileConfig.RegisterBundles(BundleTable.Bundles);

完成した Global.asax ファイルのコードを次に示します。

	using System; 
	using System.Web.Http; 
	using System.Web.Mvc; 
	using System.Web.Optimization; 
	using System.Web.Routing; 
	using System.Web.WebPages; 
	 
	namespace MvcMobile 
	{ 
	 
	    public class MvcApplication : System.Web.HttpApplication 
	    { 
	        protected void Application_Start() 
	        { 
	            DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone") 
	            { 
	                ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf 
	                    ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0) 
	            }); 
	            AreaRegistration.RegisterAllAreas(); 
	 
	            WebApiConfig.Register(GlobalConfiguration.Configuration); 
	            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters); 
	            RouteConfig.RegisterRoutes(RouteTable.Routes); 
	            BundleConfig.RegisterBundles(BundleTable.Bundles); 
	            BundleMobileConfig.RegisterBundles(BundleTable.Bundles); 
	        } 
	    } 
	}

*MvcMobile\Views\Shared\\_Layout.Mobile.cshtml* ファイルを開き、次のマークアップを *Html.Partial* 呼び出しの直後に追加します。

	<div data-role="header" align="center">
	    @Html.ActionLink("Home", "Index", "Home")
	    @Html.ActionLink("Date", "AllDates")
	    @Html.ActionLink("Speaker", "AllSpeakers")
	    @Html.ActionLink("Tag", "AllTags")
	</div>

完成した body セクションは次のようになります。

	<body>
	    <div data-role="page" data-theme="a">
	        @Html.Partial("_ViewSwitcher")
	        <div data-role="header" align="center">
	            @Html.ActionLink("Home", "Index", "Home")
	            @Html.ActionLink("Date", "AllDates")
	            @Html.ActionLink("Speaker", "AllSpeakers")
	            @Html.ActionLink("Tag", "AllTags")
	        </div>
	        <div data-role="header">
	            <h1>@ViewBag.Title</h1>
	        </div>
	        <div data-role="content">
	            @RenderSection("featured", false)
	            @RenderBody()
	        </div>
	    </div>
	</body>

アプリケーションをビルドし、モバイル ブラウザー エミュレーターで AllTags ビューに移動します。次のように表示されます。

![nuget により jquery をインストールした後][jquery3]

<div class="dev-callout"> 
<b>注</b> 
<p>IE または Chrome のユーザー エージェント文字列を iPhone に設定した後、F12 キーを押すと表示される開発者ツールを使用して、モバイル専用コードをデバッグできます。モバイル ブラウザーに <strong>[Home]</strong>、<strong>[Speaker]</strong>、<strong>[Tag]</strong>、および <strong>[Date]</strong> のリンクがボタンとして表示されない場合は、おそらく jQuery Mobile スクリプト ファイルと CSS ファイルへの参照が正しくありません。</p>
</div>

スタイルの変更に加えて、**[Displaying mobile view]** という文字列と、モバイル ビューからデスクトップ ビューに切り替えるリンクが表示されます。**[Desktop view]** リンクをクリックすると、デスクトップ ビューが表示されます。

<!--![Display desktop view][jquery4]-->

デスクトップ ビューには、モバイル ビューに直接戻る手段がありません。今からこの点を修正しましょう。*Views\Shared\\_Layout.cshtml* ファイルを開きます。&lt;body> 要素の直下に、ビュー切り替えウィジェットを描画する次のコードを追加します。

    @Html.Partial("_ViewSwitcher")

完成したコードを次に示します。

	<body>
	    @Html.Partial("_ViewSwitcher")
	
	    <div id="title">
	        <h1> MVC4 Conference</h1>
	    </div>

		@*項目はわかりやすいように省略されています。*@
	</body>

モバイル ブラウザーで **AllTags** ビューの表示を更新します。これでデスクトップ ビューとモバイル ビューの切り替えができるようになりました。

![モバイル ビューに移動][jquery5]

<div class="dev-callout"> 
<b>注</b> 
<p>Views\Shared\_ViewSwitcher.cshtml の末尾に次のコードを追加すると、ユーザー エージェント文字列をモバイル デバイスに設定したブラウザーを使用してビューをデバックするときに便利です。</p>

<pre>
	else 
	{ 
	     @:Not Mobile/Get 
	} 
</pre>
<p>また、Views\Shared\_Layout.cshtml ファイルに次の見出しを追加します。</p>
<pre>
	&lt;h1>Non Mobile Layout MVC4 Conference&lt;/h1>
</pre>
</div>

デスクトップ ブラウザーで AllTags ページに移動します。ビュー切り替えウィジェットはモバイル レイアウト ページにだけ追加されるため、デスクトップ ブラウザーにビュー切り替えウィジェットは表示されていません。デスクトップ ビューにビュー切り替えウィジェットを追加する方法については後述します。

![デスクトップ ビューの表示][jquery6]

<h2><a name="bkmk_Improvespeakerslist"></a> スピーカー一覧を強化する</h2>

モバイル ブラウザーで **[Speakers]** リンクをクリックします。モバイル ビュー (*AllSpeakers.Mobile.cshtml*) がないため、既定のスピーカー表示 (*AllSpeakers.cshtml*) がモバイル レイアウト ビュー ((*_Layout.Mobile.cshtml*) を使用して描画されます。

![モバイル スピーカー一覧の表示][SpeakerList1]

*Views\_ViewStart.cshtml* ファイルで次のように RequireConsistentDisplayMode を true に設定すると、モバイル レイアウト内で既定の (非モバイル) ビューの描画をグローバルに無効化できます。

![][SpeakerList2]

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModes.RequireConsistentDisplayMode = true;
    }
*RequireConsistentDisplayMode* が true に設定されていると、モバイル レイアウト (*_Layout.Mobile.cshtml*) はモバイル ビューだけに使用されます (つまり、ビュー ファイルの名前は ViewName.Mobile.cshtml という形式です)。モバイル レイアウトが非モバイル ビューでうまく動作しない場合は、*RequireConsistentDisplayMode* を true に設定します。次のスクリーンショットは、*RequireConsistentDisplayMode* が true に設定されていると、[Speakers] ページがどのように表示されるかを示しています。

![][SpeakerList4]

ビュー ファイルで *RequireConsistentDisplayMode* を false に設定すると、ビューの一貫表示モードを無効化できます。次のマークアップは、*Views\Home\AllSpeakers.cshtml* ファイルで *RequireConsistentDisplayMode* を false に設定します。

    @model IEnumerable<string>
    @{
        ViewBag.Title = "All speakers";
        DisplayModes.RequireConsistentDisplayMode = false;
    }
<h2><a name="bkmk_mobilespeakersview"></a>モバイル スピーカー ビューを作成する</h2>

いま見たように、スピーカー ビューは読み取れますが、リンクが小さく、モバイル デバイスではタップが困難です。このセクションでは、現代的なモバイル アプリケーション風のモバイル専用スピーカー ビューを作成します。大きくてタップしやすいリンクが表示され、スピーカーをすばやく見つけることのできる検索ボックスが用意されています。

1. *AllSpeakers.cshtml* をコピーして名前を *AllSpeakers.Mobile.cshtml* に変更します。*AllSpeakers.Mobile.cshtml* ファイルを開き、&lt;h2&gt; 見出し要素を削除します。
2. **&lt;ul&gt;** タグに data-role 属性を追加し、値を *listview* に設定します。他の *data-*** 属性と同様に、*data-role="listview"* もリスト項目を大きくしてタップしやすくします。完成したマークアップは次のようになります。

	    @model IEnumerable<string>
	    @{
	        ViewBag.Title = "All speakers";
	    }
	    <ul data-role="listview">
	        @foreach(var speaker in Model) {
	            <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
	        }
	    </ul>

3.	モバイル ブラウザーの表示を更新します。更新されたビューは次のようになります。

	![][MobileSpeakersView1]

4.	**&lt;ul&gt;** タグに data-filter 属性を追加し、値を true に設定します。ul のマークアップは次のようになります。

		<ul data-role="listview" data-filter="true">

次の図は、data-filter 属性によってページ上部に追加される検索フィルター ボックスを示しています。

![][MobileSpeakersView2]

次の図に示すように、検索ボックスに 1 文字入力するたびに、表示された一覧が jQuery Mobile によって絞り込まれます。

![][MobileSpeakersView3]

<h2><a name="bkmk_improvetags"></a> タグ一覧を強化する</h2>

既定のスピーカー ビューと同様に、タグ ビューも読み取れますが、リンクが小さく、モバイル デバイスではタップが困難です。このセクションでは、スピーカー ビューと同じ方法でタグ ビューを修正します。

1. *Views\Home\AllTags.Mobile.cshtml.hide* ファイルの名前を *Views\Home\AllTags.Mobile.cshtml* に変更します。名前を変更したファイルを開き、**&lt;h2&gt;** 要素を削除します。

2. 次に示すように、data-role 属性と data-filter 属性を **&lt;ul&gt;** タグに追加します。

		<ul data-role="listview" data-filter="true">
下図は文字 J で絞り込んだタグ ページを示しています。

![][TagsList1]

<h2><a name="bkmk_improvedates"></a> 日付一覧を強化する</h2>

**スピーカー** ビューおよび**タグ** ビューと同じように日付ビューもモバイル デバイスで使いやすいように強化できます。

1. *Views\Home\AllDates.Mobile.cshtml* ファイルをコピーして名前を *Views\Home\AllDates.Mobile.cshtml* に変更します。
2. 新しいファイルを開き、**&lt;h2&gt;** 要素を削除します。
3. 次のように、&lt;ul&gt; タグに *data-role="listview"* を追加します。

		<ul data-role="listview">

次の図は、data-role 属性を設定すると **[Date]** ページがどのように表示されるかを示しています。

![][DatesList1]

*Views\Home\AllDates.Mobile.cshtml* ファイルの内容を次のコードに置き換えます。

    @model IEnumerable<DateTime>
    @{
        ViewBag.Title = "All dates";
        DateTime lastDay = default(DateTime);
    }
    <ul data-role="listview">
        @foreach(var date in Model) {
            if (date.Date != lastDay) {
                lastDay = date.Date;
                <li data-role="list-divider">@date.Date.ToString("ddd, MMM dd")</li>
            }
            <li>@Html.ActionLink(date.ToString("h:mm tt"), "SessionsByDate", new { date })</li>
        }
    </ul>
このコードは、すべてのセッションを日付でグループ分けします。日が変わるごとに一覧に区切り線が挿入され、区切り線の下にそれぞれの日のセッションがすべて表示されます。このコードを実行すると、表示は次のようになります。

![][DatesList2]

<h2><a name="bkmk_improvesessionstable"></a> SessionsTable ビューを強化する</h2>

このセクションでは、セッションのモバイル専用ビューを作成します。変更箇所は、これまでに作成した他のビューよりも広範囲にわたります。

モバイル ブラウザーで、**[Speaker]** をタップして、検索ボックスに「Sc」と入力します。

![][SessionView1]

**[Scott Hanselman]** というリンクをタップします。

![][SessionView2]

おわかりのように、モバイル ブラウザーで表示を読み取ることは困難です。日付列は読み取りずらく、タグ列は表示されていません。これを修正するために、Views\*Home\SessionsTable.cshtml* をコピーして名前を *Views\Home\SessionsTable.Mobile.cshtml* に変更し、ファイルの内容を次のコードに置き換えます。

    @using MvcMobile.Models
    @model IEnumerable<Session>
	
    <ul data-role="listview">
        @foreach(var session in Model) {
            <li>
                <a href="@Url.Action("SessionByCode", new { session.Code })">
                    <h3>@session.Title</h3>
                    <p><strong>@string.Join(", ", session.Speakers)</strong></p>
                    <p>@session.DateText</p>
                </a>
            </li>
        }
    </ul>
このコードは部屋列とタグ列を削除し、タイトル、スピーカー、日付を縦 1 列に並べて、すべての情報をモバイル ブラウザーで読み取れるようにします。次の図にはコードの変更が反映されています。

![][SessionView3]

<h2><a name="bkmk_improvesessionbycode"></a> SessionByCode ビューを強化する</h2>

最後に、**SessionByCode** ビューのモバイル専用ビューを作成します。モバイル ブラウザーで、**[Speaker]** をタップして、検索ボックスに「Sc」と入力します。

![][SessionByCode1]

**[Scott Hanselman]** というリンクをタップします。Scott Hanselman のセッションが表示されます。

![][SessionByCode2]

**[An Overview of the MS Web Stack of Love]** リンクを選択します。

![][SessionByCode3]

既定のデスクトップ ビューでは問題ありませんが、強化することはできます。

*Views\Home\SessionByCode.cshtml* をコピーして名前を *Views\Home\SessionByCode.Mobile.cshtml* に変更し、*Views\Home\SessionByCode.Mobile.cshtml* ファイルの内容を次のマークアップに置き換えます。


    @model MvcMobile.Models.Session
	
    @{
        ViewBag.Title = "Session details";
    }
    <h2>@Model.Title</h2>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>
	
    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Speakers</li>
        @foreach (var speaker in Model.Speakers) {
            <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
        }
    </ul>
	
    <p>@Model.Description</p>
    <h4>Code: @Model.Code</h4>

    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Tags</li>
        @foreach (var tag in Model.Tags) {
            <li>@Html.ActionLink(tag, "SessionsByTag", new { tag })</li>
        }
    </ul>
新しいマークアップでは、**data-role** 属性を使用して、ビューのレイアウトを強化しています。

モバイル ブラウザーの表示を更新します。次の図には行ったコードの変更が反映されています。

![][SessionByCode4]

<h2><a name="bkmk_deployapplciation"></a> Azure の Web サイトにアプリケーションを展開する</h2>

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクトを右クリックし、コンテキスト メニューの **[発行]** をクリックします。

	![プロジェクトのコンテキスト メニューの [発行]][PublishVSSolution]

	**Web の発行**ウィザードが開きます。
2. **Web の発行**ウィザードの **[プロファイル]** タブで、**[インポート]** をクリックします。

	![発行設定のインポート][ImportPublishSettings]

	**[発行プロファイルのインポート]** ダイアログ ボックスが表示されます。

3. 以前に Visual Studio で Azure サブスクリプションを追加していない場合は、次の手順に従います。これらの手順で、**[Azure の Web サイトからインポート]** の下のドロップダウン リストに Web サイトが含まれるようにサブスクリプションを追加します。
	1. **[発行プロファイルのインポート]** ダイアログ ボックスで、**[Azure サブスクリプションの追加]** をクリックします。

	![Azure サブスクリプションの追加](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzAddWAsub.png)

	1. **[Azure サブスクリプションのインポート]** ダイアログ ボックスで、**[サブスクリプション ファイルのダウンロード]** をクリックします。
    
	![サブスクリプションのダウンロード](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)

	1. ブラウザー ウィンドウで、*.publishsettings* ファイルを保存します。
    
	![発行ファイルのダウンロード](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDown2.png)
    
	[WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)]
</br>
    
	1. **[Azure サブスクリプションのインポート]** ダイアログ ボックスで、**[参照]** をクリックし、*.publishsettings* ファイルに移動します。
    
	![サブスクリプションのダウンロード](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)
    
	1. **[インポート]** をクリックします。
    
	![インポート](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzImp.png)

	7. **[発行プロファイルのインポート]** ダイアログ ボックスで、**[Azure の Web サイトからインポート]** をクリックし、ドロップダウン リストから Web サイトを選択して **[OK]** をクリックします。

	![発行プロファイルのインポート][ImportPublishProfile]


	8. **[接続]** タブの **[接続の検証]** をクリックし、設定が正しいことを確認します。

	![接続の検証][ValidateConnection]

	9. 接続が検証されると、**[接続の検証]** ボタンの横に緑色のチェック マークが表示されます。
	![[接続] タブの [接続成功] アイコンと [次へ] ボタン][firsdeploy007]

	10. このページでは、既定の設定をすべてそのまま使用できます。リリース ビルド構成をデプロイしているため、デプロイ先サーバーでファイルを削除する必要はありません。**[データベース]** の下にある **UsersContext (DefaultConnection)** エントリは、DefaultConnection 文字列を使用する *UsersContext:DbContext* クラスから取得されています。
**[次へ]** をクリックします。

	![[接続] タブの [接続成功] アイコンと [次へ] ボタン][rxPWS]

	12. **[プレビュー]** タブで、**[プレビューの開始]** をクリックします。
このタブに、サーバーにコピーされるファイルの一覧が表示されます。プレビューの表示は、アプリケーションの発行に必要ではありませんが、知っておくと便利な機能です。この場合、表示されるファイルの一覧で操作を行う必要はありません。次に発行するときは、変更されたファイルだけがプレビュー一覧に示されます。

	![[プレビュー] タブの [プレビューの開始] ボタン][firsdeploy009]

	12. **[発行]** をクリックします。

	Azure サーバーにファイルをコピーする処理が開始されます。**出力**ウィンドウでは、実行された展開操作が表示され、展開が問題なく完了したことが報告されます。

	15. 自動的に既定のブラウザーが開き、展開先のサイトの URL にアクセスします。これで、作成したアプリケーションはクラウドで実行されています。

	![][DeployApplication10]

モバイル ブラウザーでサイト URL を参照することで、携帯電話エミュレーターを使用して確認した Web サイトを生でテストできます。

<!-- Internal Links -->
[ Azure の Web サイトの作成]: #bkmk_createaccount
[スタート プロジェクトを設定する]: #bbkmk_setupstarterproject
[ビュー、レイアウト、および部分ビューをオーバーライドする]: #bkmk_overrideviews
[jQuery Mobile を使用してモバイル ブラウザー インターフェイスを定義する]: #bkmk_usejquerymobile
[スピーカー一覧を強化する]: #bkmk_Improvespeakerslis
[モバイル スピーカー ビューを作成する]: #bkmk_mobilespeakersview
[タグ一覧を強化する]: #bkmk_improvetags
[日付一覧を強化する]: #bkmk_improvedates
[SessionsTable ビューを強化する]: #bkmk_improvesessionstable
[SessionByCode ビューを強化する]: #bkmk_improvesessionbycode
[Azure の Web サイトにアプリケーションを展開する]: #bkmk_deployapplciation

<!-- Images -->
[CreateWebSite1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_1.png
[CreateWebSite2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_2.png
[CreateWebSite3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_3.png
[CreateWebSite4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_4.png
[AppMainPage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/FinishedAPPMainScreen.png
[PropertiesPopup]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/propertiespopup.png
[BrowseByTagWithCallout]:./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/BrowseByTagWithCallout.png
[ASPNetPage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ASPNetPage.png
[Overrideviews1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p2m_layouttags_mobile_thumb.png
[Overrideviews2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p2_layoutTagsDesktop_thumb.png
[jquery1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-mobile-open-packagmanager.png
[jquery2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-mobile-open-install-jquey.png
[jquery3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_afternuget_thumb.png
[jquery4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_desktopviewwithmobilelink_thumb.png
[jquery5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_desktopviewwithmobilelink_thumb.png
[jquery6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p3_desktopBrowser_thumb.png
[SpeakerList1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersdesktop_thumb.png
[SpeakerList2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersconsistent_thumb.png
[SpeakerList3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_updatedspeakerview1_thumb.png
[SpeakerList4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png
[MobileSpeakersView1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_updatedspeakerview1_thumb.png
[MobileSpeakersView2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png
[MobileSpeakersView3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb.png
[TagsList1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_tags_j_thumb.png
[DatesList1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates1_thumb.png
[DatesList2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates2_thumb.png
[SessionView1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_1.png
[SessionView2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png
[SessionView3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_sessionsbyscottha_thumb.png
[SessionByCode1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_2.png
[SessionByCode2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png
[SessionByCode3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_love_thumb.png
[SessionByCode4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_love2_thumb.png
[DeployApplication1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_5.png
[DeployApplication2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_6.png
[DeployApplication3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_7.png
[DeployApplication4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_8.png
[DeployApplication5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_9.png
[DeployApplication6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_10.png
[DeployApplication7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_12.png
[DeployApplication8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_13.png
[DeployApplication9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_14.png
[DeployApplication10]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_15.png

<!-- External Links -->
[MVC4DeveloperPreview]: http://www.asp.net/mvc/mvc4
[WebDeployUpdate]: http://www.windowsazure.com/ja-jp/develop/net/
[Visual Studio Express 2012]: http://www.microsoft.com/visualstudio/eng/products/visual-studio-express-products
[MVC4StarterProject]: http://go.microsoft.com/fwlink/?LinkId=228307
[FinishedProject]: http://go.microsoft.com/fwlink/?LinkId=228306
[Win7PhoneEmulator]: http://msdn.microsoft.com/ja-jp/library/ff402530(VS.92).aspx
[OperaMobileEmulator]: http://www.opera.com/developer/tools/mobile/
[AppleSafari]: http://www.apple.com/safari/download/
[HowToSafari]: http://www.davidalison.com/2008/05/how-to-let-safari-pretend-its-ie.html
[FireFox]: http://www.bing.com/search?q=firefox+download
[FireFoxUserAgentSwitcher]: https://addons.mozilla.org/ja-jp/firefox/addon/user-agent-switcher/
[CSSMediaQuries]: http://www.w3.org/TR/css3-mediaqueries/
[jquerydocs]: http://jquerymobile.com/demos/1.0b3/#/demos/1.0b3/docs/about/intro.html
[setuseragent]: http://www.howtogeek.com/113439/how-to-change-your-browsers-user-agent-without-installing-any-extensions/
[managementportal]: https://manage.windowsazure.com
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk20NetVS12.png
[Add XSRF Protection]: #xsrf
[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishSettings.png
[ImportPublishProfile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishProfile.png
[PublishVSSolution]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/PublishVSSolution.png
[ValidateConnection]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ValidateConnection.png
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk20NetVS12.png
[firsdeploy007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-005.png
[firsdeploy009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-007.png
[rxPWS]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rxPWS.png


