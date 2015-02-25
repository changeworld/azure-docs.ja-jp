<properties 
	pageTitle="NET ASP.NET MVC 5 モバイル Web サイト - Azure チュートリアル" 
	description="ASP.NET MVC 5 Web アプリケーションのモバイル機能を使用して Web アプリケーションを Azure の Web サイトにデプロイする方法を示すチュートリアル。" 
	services="web-sites" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/19/2014" 
	ms.author="cephalin,riande"/>


# ASP.NET MVC 5 モバイル Web アプリケーションを Azure Web サイトに展開する

このチュートリアルでは、モバイル対応の ASP.NET MVC 5 Web アプリケーションを作成し、
Microsoft Azure にデプロイする方法について基本を説明します。このチュートリアルを実行するには、 
[Visual Studio Express 2013 for Web][Visual Studio Express 2013] が必要です。または Visual Studio の Professional Edition を既にお持ちの場合はそれを使用することもできます。

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

### 作成するアプリケーション:

このチュートリアルでは、[スタート プロジェクト][StarterProject] に用意されている単純な会議一覧アプリケーションにモバイル機能を追加します。次のスクリーンショットは、完成したアプリケーションの ASP.NET セッションを Internet Explorer 11 F12 開発ツールのブラウザー エミュレーターで表示したものです。

![][FixedSessionsByTag]

Internet Explorer 11 F12 開発者ツールおよび [Fiddler ツール][Fiddler] を使用してアプリケーションのデバッグに役立てることができます。 

### 学習内容

ここでは次の内容について学習します。:

-	Visual Studio 2013 を使用して、Web アプリケーションを Windows Azure Web サイトに直接発行する方法。
-   ASP.NET MVC 5 テンプレートで CSS Bootstrap フレームワークを使用して、モバイル デバイス上での表示品質を高める方法
-   iPhone や Android などの特定のモバイル ブラウザーに対応したモバイル専用のビューを作成する方法
-   レスポンシブ ビュー (さまざまなデバイスの異なるブラウザーに対応するビュー) の作成方法

<h2>開発環境を設定する</h2>

.NET Framework 対応の Azure SDK をインストールして、開発環境を設定します。 

1. Azure SDK for .NET をインストールするには、次のリンクをクリックします。Visual Studio 2013 をまだインストールしていない場合は、次のリンクをクリックするとインストールされます。このチュートリアルには、Visual Studio 2013 が必要です。[Azure SDK for Visual Studio 2013][AzureSDKVs2013]
1. Web Platform Installer のウィンドウで、**[インストール]** をクリックし、インストールの手順を進めます。

	![Web Platform Installer - Azure SDK for .NET][WebPIAzureSdk23NetVS13]

モバイル ブラウザー エミュレーターも必要です。次のいずれでも動作します。:

-   [Internet Explorer 11 F12 開発者ツール][EmulatorIE11] のブラウザー エミュレーター (このチュートリアルではすべてのスクリーンショットでこのエミュレーターを使用しています)。Windows Phone 8、Windows Phone 7、Apple iPad のエージェント文字列のプリセットが搭載されています。
-	[Google Chrome DevTools][EmulatorChrome] のブラウザー エミュレーター。多数の Android デバイスに加え、Apple iPhone、Apple iPad、Amazon Kindle Fire 用のプリセットが含まれています。タッチイベントのエミュレーションにも対応しています。
-   [Opera Mobile Emulator][EmulatorOpera]

次のトピック用に、C# のソース コードを使用した Visual Studio プロジェクトが用意されています。:

-   [スタート プロジェクトのダウンロード][StarterProject]
-   [完成したプロジェクトのダウンロード][CompletedProject]

<h2>このチュートリアルの手順</h2>

- [Microsoft Azure Web サイトにスタート プロジェクトをデプロイする][]
- [ブートストラップ CSS フレームワーク][]
- [ビュー、レイアウト、および部分ビューをオーバーライドする][]
- [スピーカー一覧を強化する][]
- [タグ一覧を強化する][]
- [日付一覧を強化する][]
- [SessionsTable ビューを強化する][]
- [SessionByCode ビューを強化する][]

<h3><a name="bkmk_DeployStarterProject"></a>Windows Azure Web サイトにスタート プロジェクトをデプロイする</h3>

1.	会議一覧アプリケーションの[スタート プロジェクト][StarterProject] をダウンロードします。

2. 	Windows エクスプローラーで Mvc5Mobile.zip ファイルを右クリックし、*Properties* をクリックします。

3. 	**[MvcMobileRTMStarter.zip のプロパティ]** ダイアログ ボックスで、**[ブロックの解除]** をクリックします(ブロックを解除すると、Web からダウンロードした .zip ファイルを使用する際に表示されるセキュリティに関する警告を回避できます)。

4.	*Mvc5Mobile.zip* ファイルを右クリックし、**[すべて展開]** をクリックしてファイルを解凍します。 

5. 	Visual Studio で、*Mvc5Mobile.sln* ファイルを開きます。

6.  ソリューション エクスプローラーで目的のプロジェクトを右クリックし、**[発行]** をクリックします。

	![][DeployClickPublish]

7.	[Web の発行] で **[Microsoft Azure Websites]** をクリックします。

	![][DeployClickWebSites]

8.	**[サインイン]** をクリックします。

	![][DeploySignIn]

9.	Microsoft Azure のユーザー名を入力し、**[続行]** をクリックします。

	![][DeployUsername]

10.	パスワードを入力し、**[サインイン]**をクリックします。

	![][DeployPassword]

11. サインインが完了すると、[既存の Web サイトを選択] ダイアログボックスが表示されます。**[新規]** をクリックします。

	![][DeployNewWebsite]  

12. **[サイト名]** フィールドに、一意のサイト名のプレフィックスを指定します。完全修飾サイト名は、*&lt;プレフィックス>*.azurewebsites.net となります。**[リージョン]** フィールドで、リージョンを選択します。**[作成]** をクリックします。

	![][DeploySiteSettings]

13.	[Web の発行] ダイアログに、新しい Web サイトのサイト設定が入力されます。**[発行]** をクリックします。

	![][DeployPublishSite]

	Visual Studio で Windows Azure Web サイトへの発行が完了すると、デスクトップ ブラウザーが開き、ライブ Web サイトが表示されます。

14.	モバイル ブラウザー エミュレーターを起動し、会議アプリケーション (*<prefix>*.azurewebsites.net) の URL をエミュレーターにコピーし、右上のボタンをクリックして **[Browse by tag]** をクリックします。Internet Explorer 11 を
既定のブラウザーとして使用している場合は、`F12` キー、
`Ctrl+8` キーの順に押して、ブラウザー プロファイルを **Windows Phone** に変更する必要があります。下の図は、縦モードの *AllTags* ビューを示しています (
**Browse by tag** をクリックした後)。

	![][AllTags]

>[AZURE.NOTE] Visual Studio の中からでも MVC 5 アプリケーションをデバッグできますが、Web サイトを Microsoft Azure に再度発行すると、モバイル ブラウザーまたはブラウザー エミュレーターからライブ サイトを直接確認できます。

モバイル デバイス上でも読みやすい表示になっています。ブートストラップ CSS フレームワークによって適用された視覚効果も一部表示されています。
**[ASP.NET]** リンクをクリックします。

![][SessionsByTagASP.NET]

ASP.NET タグ ビューは、画面に合わせてズームされています。この処理は、ブートストラップによって自動的に実行されます。ただし、モバイル ブラウザーに合わせて、このビューをさらに調整することもできます。たとえば、**[Date]** 列は読みにくくなっています。このチュートリアルでは、後ほど、*AllTags* ビューをモバイル対応に変更します。

<h2><a name="bkmk_bootstrap"></a>ブートストラップ CSS フレームワーク</h2>

MVC 5 の新機能の 1 つに、ブートストラップの標準サポートがあります。ブートストラップによって、さまざまなビューの表示品質が瞬時に向上することは先ほど確認したとおりです。たとえば、ブラウザーの幅が小さくなったときに、上部のナビゲーション バーを自動的に折りたたむことができます。デスクトップ ブラウザーで、ブラウザー ウィンドウのサイズを変更して、ナビゲーション バーの外観と操作性がどのように変わるかをご確認ください。これが、ブートストラップに組み込まれたレスポンシブ Web デザインです。

ブートストラップを使用しない場合の Web アプリケーションの外観を表示するには、
*App\_Start\\BundleConfig.cs* を開き、
*bootstrap.js* と *bootstrap.css* を含む行をコメント アウトします。次のコードは、変更後の `RegisterBundles` メソッドにある最後の 2 つのステートメントを示しています。:

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

`Ctrl キーを押しながら F5 キーを押して`アプリケーションを実行します。

折りたたみ可能なナビゲーション バーが、通常の順不同一覧として表示されます。**[Browse by tag]** を再度クリックし、**[ASP.NET]** をクリックします。
モバイル エミュレーター ビューが画面に合わせてズームされないため、テーブルの右側を表示するには横方向にスクロールする必要があります。

![][SessionsByTagASP.NETNoBootstrap]

変更を元に戻し、モバイル ブラウザーをリフレッシュして、モバイル対応画面が復元されたことを確認します。

ブートストラップは、ASP.NET MVC 5 固有の機能ではなく、あらゆる Web アプリケーションでこの機能を活用できます。しかし ASP.NET MVC 5 プロジェクト テンプレートには
ブートストラップが組み込まれているため、MVC 5 Web アプリケーションでは、この機能を既定で利用できます。

ブートストラップの詳細については、
[ブートストラップ][BootstrapSite] のサイトをご覧ください。

次のセクションでは、モバイル ブラウザー専用ビューを作成する方法について説明します。

<h2><a name="bkmk_overrideviews"></a>ビュー、レイアウト、および部分ビューをオーバーライドする</h2>

モバイル ブラウザー全般、個々のモバイル ブラウザー、または特定のブラウザーの (レイアウトと部分ビューを含む) 任意のビューをオーバーライドできます。モバイル専用ビューを用意するには、ビュー ファイルをコピーして *.Mobile* をファイル名に追加します。たとえば、モバイル
*Index* ビューを作成するには、*Views\\Home\\Index.cshtml* を
*Views\\Home\\Index.Mobile.cshtml* にコピーできます。

このセクションでは、モバイル専用のレイアウト ファイルを作成します。

最初に、*Views\\Shared\\\_Layout.cshtml* を
*Views\\Shared\\\_Layout.Mobile.cshtml* にコピーします。*_Layout.Mobile.cshtml* を開き、タイトルを「**MVC5 Application**」から「**MVC5 Application (Mobile)**」に変更します。

ナビゲーション バーの各 `Html.ActionLink` 呼び出しで、"Browse by" を
各 *ActionLink* リンクから削除します。次のコードは、モバイル レイアウト ファイルの完成した `<ul class="nav navbar-nav">` タグです。

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

*Views\\Home\\AllTags.cshtml* ファイルを
*Views\\Home\\AllTags.Mobile.cshtml* にコピーします。新しいファイルを開き、次のように
`<h2>` 要素を "Tags" から "Tags (M)": に変更します。

    <h2>Tags (M)</h2>

デスクトップ ブラウザー、および、モバイル ブラウザー エミュレーターを使用してタグ ページに移動します。モバイル ブラウザー エミュレーターでは、2 か所が変更されていることがわかります
(*\_Layout.Mobile.cshtml* のタイトルと
*AllTags.Mobile.cshtml のタイトル*)。

![][AllTagsMobile_LayoutMobile]

対照的に、デスクトップでの表示は変更されていません (*\_Layout.cshtml* と 
*AllTags.cshtml*) のタイトル)。

![][AllTagsMobile_LayoutMobileDesktop]

<h2><a name="bkmk_browserviews"></a>ブラウザー専用のビューを作成する</h2>

モバイル専用のビューやデスクトップ専用のビューに加え、個別のブラウザーに対してビューを作成できます。たとえば、iPhone ブラウザーや Android ブラウザー専用のビューを作成できます。このセクションでは、iPhone ブラウザーと iPhone バージョンの *AllTags* ビュー用のレイアウトを作成します。

*Global.asax* ファイルを開き、次の コードを
`Application_Start` メソッドの最終行として追加します。

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

このコードでは、"iPhone" という表示モードを定義し、受信された各要求をその定義に対して照合します。受信された要求が定義した条件と一致する場合 (つまり、ユーザー エージェントに
"iPhone という文字列が含まれる場合")、ASP.NET MVC は
"iPhone" サフィックスを含む名前のビューを探します。

>[AZURE.NOTE] iPhone や Android など、モバイル ブラウザー専用の表示モードを追加する場合、最初の引数を `0` に設定して(リストの一番上に挿入)、ブラウザー専用のモードがモバイル テンプレート (*.Mobile.cshtml) よりも優先されるようにします。逆にモバイル テンプレートをリストの一番上に配置すると、目的の表示モードに優先して選択されます (最初に一致したエントリが選択されますが、モバイル テンプレートはすべてのモバイル ブラウザーで一致します)。 

コードで、`DefaultDisplayMode` を右クリックし、**[解決]**、`using System.Web.WebPages;` の順にクリックします。これにより、
`System.Web.WebPages` 名前空間に参照が追加されます。ここでは、
`DisplayModeProvider` 型と `DefaultDisplayMode` 型が定義されています。

![][ResolveDefaultDisplayMode]

別の方法として、単純にファイルの
`using` セクションに、次の行を手動で追加することもできます。

    using System.Web.WebPages;

変更を保存します。次をコピーします。
*Views\\Shared\\\_Layout.Mobile.cshtml* file to
*Views\\Shared\\\_Layout.iPhone.cshtml*. Open the new file
and then change the title from `MVC5 Application (Mobile)` to
`MVC5 Application (iPhone)`.

*Views\\Home\\AllTags.Mobile.cshtml* ファイルを次にコピーします。
*Views\\Home\\AllTags.iPhone.cshtml*. In the new file, change
the `<h2>` element from "Tags (M)" to "Tags (iPhone)".

アプリケーションを実行します。モバイル ブラウザー エミュレーターを実行し、ユーザー エージェントが "iPhone" に設定されていることを確認して、*AllTags* ビューにアクセスします。Internet Explorer 11 F12 開発者ツールのエミュレーターを使用している場合は、エミュレーションを次のとおりに構成します。:

-   [ブラウザー プロファイル] = **[Windows Phone]**
-   [ユーザー エージェント文字列] =  **[カスタム]**
-   [カスタム文字列] = 「**Apple-iPhone5C1/1001.525**」

次のスクリーンショットは、Internet Explorer 11 F12 開発者ツールのエミュレーターで、カスタムのユーザー エージェント文字列 (iPhone 5C のユーザー エージェント文字列) を使用して *AllTags* ビューを描画した画面を示しています。

![][AllTagsIPhone_LayoutIPhone]

モバイル ブラウザーで **[Speakers]** リンクをタップします。モバイル ビュー (*AllSpeakers.Mobile.cshtml*) がないため、既定のスピーカー ビュー
(*AllSpeakers.cshtml*) は、モバイル レイアウト ビュー
(*\_Layout.Mobile.cshtml*) を使用して描画されます。次に示すように、タイトル「**MVC5 Application
(Mobile)**」は、*\_Layout.Mobile.cshtml* で定義されます。

![][AllSpeakers_LayoutMobile]

モバイル レイアウト内で既定の (非モバイル) ビューの描画をグローバルに無効化できます。これは、`RequireConsistentDisplayMode` を
次のように *Views\\\_ViewStart.cshtml* ファイルで `true` に設定することで行います。:

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

`RequireConsistentDisplayMode` を `true` に設定すると、モバイル レイアウト
(*\_Layout.Mobile.cshtml*) はモバイル ビューにのみ使用されます (ビュー ファイルが ***ViewName**.Mobile.cshtml という形式の場合*)。モバイル レイアウトが非モバイル ビューでうまく動作しない場合は、`RequireConsistentDisplayMode`` を `true` に設定します。次のスクリーンショットは、`RequireConsistentDisplayMode` が `true` に設定されている場合の *Speakers* ページの表示を示しています (文字列 "(Mobile)" は上部のナビゲーション バーに表示されません)。

![][AllSpeakers_LayoutMobileOverridden]

ビューの一貫表示モードを無効化できます。これは、
ビュー ファイルで `RequireConsistentDisplayMode` を `false` に設定することで行います。次のマークアップは、*Views\\Home\\AllSpeakers.cshtml* ファイルで
`RequireConsistentDisplayMode` を `false`: に設定します。

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

このセクションでは、モバイル レイアウトとビューの作成方法および iPhone などの特定のデバイス専用のレイアウトとビューの作成方法を説明しました。
しかし、ブートストラップ CSS フレームワークの最大の利点はレスポンシブ レイアウトの使用により、単一のスタイルシートを デスクトップ、携帯電話、タブレットの各ブラウザーに適用し、一貫した外観と操作性を 実現できることです。次のセクションでは、ブートストラップを利用したモバイル対応ビューの作成方法を説明します。

<h2><a name="bkmk_Improvespeakerslist"></a>スピーカー一覧を強化する</h2>

いま見たように、*Speakers* ビューは読み取れますが、リンクが小さく、モバイル デバイスではタップが困難です。このセクションでは、*AllSpeakers* ビューをモバイル対応に設定し、大きくてタップしやすいリンクが表示され、スピーカーをすばやく見つけることのできる検索ボックスを表示します。

ブートストラップの[リンクされたリスト グループ][]のスタイリングを使用して、*Speakers* ビューを強化します。*Views\\Home\\AllSpeakers.cshtml* で、Razor ファイルの内容を、以下のコードに置き換えます。

     @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker }, new { @class = "list-group-item" })
        }
    </div>

タグ内の `class="list-group"` 属性は
ブートストラップ リストのスタイリングに適用され、`class="input-group-item"` 属性は各リンクへのブートストラップ リスト アイテムのスタイリングに適用されます。

モバイル ブラウザーの表示を更新します。更新されたビューは次のようになります。:

![][AllSpeakersFixed]

ブートストラップの [[リンクされたリスト グループ]][] のスタイリングを使用すると、各リンクのボックス全体がクリック可能になり、ユーザー エクスペリエンスが大幅に向上します。デスクトップ ビューに切り替えると、外観と操作性が一貫していることがわかります。

![][AllSpeakersFixedDesktop]

モバイル ブラウザー ビューは強化されましたが、スピーカーの長い一覧をスクロールするのは困難です。ブートストラップは、検索フィルターを標準で提供していませんが、数行のコードによって追加できます。まずビューに検索ボックスを追加した後、JavaScript コードを関連付けてフィルター機能を設定します。次に示すように、
*Views\\Home\\AllSpeakers.cshtml* で、\<form\> タグを \<h2\> タグの直後に追加します。:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <form class="input-group">
        <span class="input-group-addon"><span class="glyphicon glyphicon-search"></span></span>
        <input type="text" class="form-control" placeholder="Search speaker">
    </form>
    <br />
    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class = "list-group-item" })
        }
    </div>

`<form>` タグと `<input>` タグの両方にブートストラップのスタイルが適用されていることにご注意ください。`<span>` 要素はブートストラップ
[glyphicon][] を検索ボックスを追加します。

*Scripts* フォルダーで、*filter.js* という JavaScript ファイルを追加します。ファイルを開き、次のコードを貼り付けます。:

    $(function () {

        // reset the search form when the page loads
        $("form").each(function () {
            this.reset();
        });

        // wire up the events to the <input> element for search/filter
        $("input").bind("keyup change", function () {
            var searchtxt = this.value.toLowerCase();
            var items = $(".list-group-item");

            // show all speakers that begin with the typed text and hide others
            for (var i = 0; i < items.length; i++) {
                var val = items[i].text.toLowerCase();
                val = val.substring(0, searchtxt.length);
                if (val == searchtxt) {
                    $(items[i]).show();
                }
                else {
                    $(items[i]).hide();
                }
            }
        });
    });

さらに、登録済みのバンドルに filter.js を含める必要があります。開く
*App_Start\BundleConfig.cs* を開き、最初のバンドルを変更します。最初の `bundles.Add` ステートメント (**jquery** バンドル用) を
*Scripts\\filter.js* を含むように、次のように変更します。

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

**jquery** バンドルは、既定の *\_Layout* ビューによって既に描画されています。後で、同じ JavaScript コードを利用して、フィルター機能を他のリスト ビューに適用できます。

モバイル ブラウザーを情報に更新し、*AllSpeakers* ビューに移動します。検索ボックスに、「"sc"」と入力します。入力した検索文字列に従って、スピーカー一覧がフィルタリングされます。

![][AllSpeakersFixedSearchBySC]

<h2><a name="bkmk_improvetags"></a>タグ一覧を強化する</h2>

*Speakers* ビューと同様に、*Tags* ビューも読み取れますが、リンクが小さく、モバイル デバイスではタップが困難です。*Tags* ビューも *Speakers* ビューと同じように修正できます。上記のコードの変更を使用している場合、*Views\\Home\\AllTags.cshtml*: で次の `Html.ActionLink` メソッド構文を使用することを除き、手順は同じです。

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

デスクトップ ブラウザーを更新すると、次のように表示されます。:

![][AllTagsFixedDesktop]

モバイル ブラウザーを更新すると、次のように表示されます。: 

![][AllTagsFixed]

>[AZURE.NOTE] モバイル ブラウザーで元の一覧書式設定が残り、ブートストラップのスタイリングが正常に機能していないように見えますが、これは先ほどモバイル専用のビューを作成したためです。ここではブートストラップ CSS フレームワークを使用してレスポンシブ Web デザインを作成するため、これらのモバイル専用のビューとモバイル専用のレイアウト ビューを削除してください。その後、モバイル ブラウザーを更新すると、ブートストラップのスタイリングが表示されます。

<h2><a name="bkmk_improvedates"></a>日付一覧を強化する</h2>

*Dates* ビューも *Speakers* ビューおよび
*Tags* ビューと同じようにモバイル デバイスで使いやすいように強化できます。前述のコードの変更を使用している場合、 *Views\\Home\\AllDates.cshtml*: で `Html.ActionLink` メソッドの構文を使用することを除き、手順は同じです。

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

モバイル ブラウザー ビューを更新すると、次のように表示されます。:

![][AllDatesFixed]

*Dates* ビューはさらに、date-time 値を date ごとに整理して表示品質を高めることができます。これには、ブートストラップの
[パネル][] スタイリングを使用します。*Views\\Home\\AllDates.cshtml* ファイルの内容を次のコードに置き換えます。:

    @model IEnumerable<DateTime>

    @{
        ViewBag.Title = "All Dates";
    }

    <h2>Dates</h2>

    @foreach (var dategroup in Model.GroupBy(x=>x.Date))
    {
        <div class="panel panel-primary">
            <div class="panel-heading">
                @dategroup.Key.ToString("ddd, MMM dd")
            </div>
            <div class="panel-body list-group">
                @foreach (var date in dategroup)
                {
                    @Html.ActionLink(date.ToString("h:mm tt"), 
                                     "SessionsByDate", 
                                     new { date }, 
                                     new { @class = "list-group-item" })
                }
            </div>
        </div>
    }

このコードでは、一覧の日付ごとに別個の `<div class="panel panel-primary">` タグを作成し、これまでと同じそれぞれのリンクに対して[リンクされたリスト グループ][]を使用します。このコードを実行すると、モバイル ブラウザーの表示は次のようになります。:

![][AllDatesFixed2]

デスクトップ ブラウザーに切り替えます。モバイル ブラウザーと一貫性を保った画面が表示されます。

![][AllDatesFixed2Desktop]

<h2><a name="bkmk_improvesessionstable"></a>SessionsTable ビューを強化する</h2>

このセクションでは、*SessionsTable* ビューがより適切にモバイルに対応するように調整します。変更箇所は、これまでに作成した他のビューよりも広範囲にわたります。

モバイル ブラウザーで、**[Tag]** をタップして検索ボックスに「`asp`」と入力します。

![][AllTagsFixedSearchByASP]

**[ASP.NET]** リンクをタップします。

![][SessionsTableTagASP.NET]

ご覧のように、現在、デスクトップ ブラウザーで適切に表示できるように設計された表形式で表示されています。しかし、モバイル ブラウザーで読み取るのは少し困難です。これを修正するために、
*Views\\Home\\SessionsTable.cshtml* を開き、ファイルの内容を次のコードに置き換えます。:

    @model IEnumerable<Mvc5Mobile.Models.Session>

    <h2>@ViewBag.Title</h2>

    <div class="container">
        <div class="row">
            @foreach (var session in Model)
            {
                <div class="col-md-4">
                    <div class="list-group">
                        @Html.ActionLink(session.Title, 
                                         "SessionByCode", 
                                         new { session.Code }, 
                                         new { @class="list-group-item active" })
                        <div class="list-group-item">
                            <div class="list-group-item-text">
                                @Html.Partial("_SpeakersLinks", session)
                            </div>
                            <div class="list-group-item-info">
                                @session.DateText
                            </div>
                            <div class="list-group-item-info small hidden-xs">
                                @Html.Partial("_TagsLinks", session)
                            </div>
                        </div>
                    </div>
                </div>
            }
        </div>
    </div>

このコードでは、次の 3 つの処理を行います。:

-   ブートストラップの[カスタムのリンクされたリスト グループ][]を使用して、セッション情報を縦 1 列に並べ、すべての情報をモバイル ブラウザーで読み取れるようにします (list-group-item-text などのクラスを使用します)。
-   レイアウトに[グリッド システム][]を適用して、デスクトップ ブラウザーではセッション項目を横並びに表示し、モバイル ブラウザーでは縦に表示します (col-md-4 クラスを使用します)。
-   [レスポンシブ ユーティリティ][]を使用して、モバイル ブラウザーで表示したときに、セッション タグを非表示にします (hidden-xs クラスを使用します)。

タイトル リンクをタップして、それぞれのセッションに移動することもできます。次の図にはコードの変更が反映されています。

![][FixedSessionsByTag]

適用したブートストラップのグリッド システムによって、モバイル ブラウザーのセッションが自動的に縦並びに配置されます。また、タグが非表示になります。デスクトップ ブラウザーに切り替えます。

![][SessionsTableFixedTagASP.NETDesktop]

デスクトップ ブラウザーでは、タグが表示されています。また、適用したブートストラップのグリッド システムによって、セッション項目が 2 列に表示されています。ブラウザーを広げると、配置が 3 列表示に変わります。

<h2><a name="bkmk_improvesessionbycode"></a>SessionByCode ビューを強化する</h2>

最後に、*SessionByCode* ビューをモバイル対応に修正します。

モバイル ブラウザーで、**[Tag]** をタップして検索ボックスに「`asp`」と入力します。

![][AllTagsFixedSearchByASP]

**[ASP.NET]** リンクをタップします。ASP.NET タグのセッションが表示されます。

![][FixedSessionsByTag]

**[Building a Single Page Application with ASP.NET and
AngularJS]** リンクをタップします。

![][SessionByCode3-644]

既定のデスクトップ ビューでも問題はありませんが、ブートストラップの GUI コンポーネントを使用して簡単に表示品質を高めることができます。

*Views\\Home\\SessionByCode.cshtml* を開き、内容を次のマークアップと置き換えます。:

    @model Mvc5Mobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h3>@Model.Title (@Model.Code)</h3>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Speakers
        </div>
        @foreach (var speaker in Model.Speakers)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class="panel-body" })
        }
    </div>

    <p>@Model.Abstract</p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Tags
        </div>
        @foreach (var tag in Model.Tags)
        {
            @Html.ActionLink(tag, 
                             "SessionsByTag", 
                             new { tag }, 
                             new { @class = "panel-body" })
        }
    </div>

新しいマークアップでは、ブートストラップのパネル スタイリングによりモバイル ビューが強化されています。 

モバイル ブラウザーの表示を更新します。次の図では、行ったコードの変更が反映されています。:

![][SessionByCodeFixed3-644]

まとめ
------------------

このチュートリアルでは、ASP.NET MVC 5 を使用してモバイル対応の Web アプリケーションを開発する方法を学習しました。内容は次のとおりです。:

-	ASP.NET MVC 5 アプリケーションの Windows Azure Web サイトへのデプロイ
-   ブートストラップを使用した MVC 5 アプリケーションでのレスポンシブな Web レイアウトの作成
-   ビュー全般や個別のビューのレイアウト、ビュー、および部分ビューのオーバーライド
-   `RequireConsistentDisplayMode` プロパティを使用した、レイアウトや部分的なオーバーライドの実行の制御
-   iPhone ブラウザーなど、特定のブラウザー専用のビューの作成
-   Razor コードでのブートストラップのスタイリングの適用

関連項目
--------

-   [レスポンシブ Web デザインの 9 つの基本原則](http://blog.froont.com/9-basic-principles-of-responsive-web-design/)
-   [ブートス トラップ][BootstrapSite]
-   [ブートストラップの公式ブログ][]
-   [Tutorial Republic による Twitter Bootstrap のチュートリアル][]
-   [The Bootstrap Playground][]
-   [W3C 勧告: モバイル Web アプリケーションのベスト プラクティス][]
-   [W3C のメディア クエリに関する勧告候補][]

<!-- Internal Links -->
[Microsoft Azure Web サイトにスタート プロジェクトをデプロイする]: #bkmk_DeployStarterProject
[ブートストラップ CSS フレームワーク]: #bkmk_bootstrap
[ビュー、レイアウト、および部分ビューをオーバーライドする]: #bkmk_overrideviews
[ブラウザー専用のビューを作成する]:#bkmk_browserviews
[スピーカー一覧を強化する]: #bkmk_Improvespeakerslist
[タグ一覧を強化する]: #bkmk_improvetags
[日付一覧を強化する]: #bkmk_improvedates
[SessionsTable ビューを強化する]: #bkmk_improvesessionstable
[SessionByCode ビューを強化する]: #bkmk_improvesessionbycode

<!-- External Links -->
[Visual Studio Express 2013]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
[AzureSDKVs2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
[Fiddler]: http://www.fiddler2.com/fiddler2/
[EmulatorIE11]: http://msdn.microsoft.com/ja-jp/library/ie/dn255001.aspx
[EmulatorChrome]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
[EmulatorOpera]: http://www.opera.com/developer/tools/mobile/
[StarterProject]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
[CompletedProject]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
[BootstrapSite]: http://getbootstrap.com/
[WebPIAzureSdk23NetVS13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
[リンクされたリスト グループ]: http://getbootstrap.com/components/#list-group-linked
[glyphicon]: http://getbootstrap.com/components/#glyphicons
[パネル]: http://getbootstrap.com/components/#panels
[カスタムのリンクされたリスト グループ]: http://getbootstrap.com/components/#list-group-custom-content
[グリッド システム]: http://getbootstrap.com/css/#grid
[レスポンシブ ユーティリティ]: http://getbootstrap.com/css/#responsive-utilities
[ブートストラップの公式ブログ]: http://blog.getbootstrap.com/
[Tutorial Republic による Twitter Bootstrap のチュートリアル]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
[The Bootstrap Playground]: http://www.bootply.com/
[W3C 勧告: モバイル Web アプリケーションのベスト プラクティス]: http://www.w3.org/TR/mwabp/
[W3C のメディア クエリに関する勧告候補]: http://www.w3.org/TR/css3-mediaqueries/

<!-- Images -->
[DeployClickPublish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png
[DeployClickWebSites]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png
[DeploySignIn]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png
[DeployUsername]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png
[DeployPassword]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png
[DeployNewWebsite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png
[DeploySiteSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png
[DeployPublishSite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png
[MobileHomePage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/mobile-home-page.png
[FixedSessionsByTag]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png
[AllTags]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png
[SessionsByTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png
[SessionsByTagASP.NETNoBootstrap]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png
[AllTagsMobile_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png
[AllTagsMobile_LayoutMobileDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png
[ResolveDefaultDisplayMode]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png
[AllTagsIPhone_LayoutIPhone]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png
[AllSpeakers_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png
[AllSpeakers_LayoutMobileOverridden]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png
[AllSpeakersFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png
[AllSpeakersFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png
[AllSpeakersFixedSearchBySC]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png
[AllTagsFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png 
[AllTagsFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png
[AllDatesFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png
[AllDatesFixed2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png
[AllDatesFixed2Desktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png
[AllTagsFixedSearchByASP]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png
[SessionsTableTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png
[SessionsTableFixedTagASP.NETDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png
[SessionByCode3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png
[SessionByCodeFixed3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png


<!--HONumber=42-->
