<properties linkid="develop-dotnet-aspnet-mvc-4-mobile-website" urlDisplayName="ASP.NET MVC 5 mobile website" pageTitle=".NET ASP.NET MVC 5 mobile website - Azure tutorials" metaKeywords="Azure tutorial, Azure web app tutorial, Azure mobile app, Azure ASP.NET MVC 5,,ASP.NET MVC" description="A tutorial that teaches you how to deploy a web application to an Azure website using mobile features in ASP.NET MVC 5 web application." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy an ASP.NET MVC Mobile Web Application on Azure Websites" authors="cephalin,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="cephalin,riande" />

# ASP.NET MVC 5 モバイル Web アプリケーションを Azure Web サイトに展開する

このチュートリアルでは、モバイル対応の ASP.NET MVC 5 Web アプリケーションを作成し、Windows Azure に展開する方法について基本を説明します。このチュートリアルを実行するには、[Visual Studio Express 2013 for Web][Visual Studio Express 2013 for Web] が必要です。または Visual Studio のプロフェッショナル エディションを既にお持ちの場合はそれを使用することもできます。

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

### 作成するアプリケーション:

このチュートリアルでは、[スタート プロジェクト][スタート プロジェクト]に用意されている単純な会議一覧アプリケーションにモバイル機能を追加します。次のスクリーンショットは、完成した アプリケーションの ASP.NET セッションを Internet Explorer 11 F12 開発ツールのブラウザー エミュレーターで表示したものです。

![][0]

Internet Explorer 11 F12 開発者ツールおよび [Fiddler ツール][Fiddler ツール] を使用してアプリケーションのデバッグに役立てることができます。

### 学習内容

ここでは次の内容について学習します。

-   Visual Studio 2013 を使用して、Web アプリケーションを Windows Azure Web サイトに直接発行する方法。
-   ASP.NET MVC 5 テンプレートで CSS Bootstrap フレームワークを使用して、
    モバイル デバイス上での表示品質を高める方法
-   iPhone や Android などの特定のモバイル ブラウザーに対応したモバイル専用のビューを作成する方法
-   レスポンシブ ビュー (さまざまなデバイスの異なる
    ブラウザーに対応するビュー) の作成方法

## 開発環境を設定する

.NET Framework 対応の Azure SDK をインストールして、開発環境を設定します。

1.  Azure SDK for .NET をインストールするには、次のリンクをクリックします。Visual Studio 2013 をまだインストールしていない場合は、次のリンクをクリックするとインストールされます。このチュートリアルには Visual Studio 2013 が必要です。[Visual Studio 2013 用の Azure SDK][Visual Studio 2013 用の Azure SDK]
2.  Web Platform Installer のウィンドウで、**[インストール]** をクリックし、インストールの手順を進めます。

    ![Web Platform Installer - Azure SDK for .NET][Web Platform Installer - Azure SDK for .NET]

モバイル ブラウザー エミュレーターも必要です。次のいずれでも動作します。

-   [Internet Explorer 11 F12 developer tools][Internet Explorer 11 F12 developer tools] のブラウザー エミュレーター (このチュートリアルではすべてのスクリーンショットでこのエミュレーターを使用しています)。
    Windows Phone 8、Windows Phone 7、Apple iPad のエージェント文字列のプリセットが搭載されています。
-   [Google Chrome DevTools][Google Chrome DevTools] のブラウザー エミュレーター。多数の Android デバイスに加え、Apple iPhone、Apple iPad、Amazon Kindle Fire 用のプリセットが含まれています。タッチイベントのエミュレーションにも対応します
-   [Opera Mobile Emulator][Opera Mobile Emulator]

次のトピック用に、C# のソース コード を使用した Visual Studio プロジェクトが用意されています。

-   [スタート プロジェクトのダウンロード][スタート プロジェクト]
-   [完成したプロジェクトのダウンロード][完成したプロジェクトのダウンロード]

## このチュートリアルの手順

-   [Windows Azure Web サイトにスタート プロジェクトをデプロイする][Windows Azure Web サイトにスタート プロジェクトをデプロイする]
-   [ブートストラップ CSS フレームワーク][ブートストラップ CSS フレームワーク]
-   [ビュー、レイアウト、および部分ビューをオーバーライドする][ビュー、レイアウト、および部分ビューをオーバーライドする]
-   [スピーカー一覧を強化する][スピーカー一覧を強化する]
-   [タグ一覧を強化する][タグ一覧を強化する]
-   [日付一覧を強化する][日付一覧を強化する]
-   [SessionsTable ビューを強化する][SessionsTable ビューを強化する]
-   [SessionByCode ビューを強化する][SessionByCode ビューを強化する]

### <a name="bkmk_DeployStarterProject"></a>Windows Azure Web サイトにスタート プロジェクトをデプロイする

1.  会議一覧アプリケーションの[スタート プロジェクト][スタート プロジェクト]をダウンロードします。

2.  Windows エクスプローラーで MvcMobileStarterBeta.zip ファイルを右クリックし、*[プロパティ]* をクリックします。

3.  **[MvcMobileRTMStarter.zip のプロパティ]** ダイアログ ボックスで、**[ブロックの解除]** をクリックします (ブロックを解除すると、Web からダウンロードした *.zip* ファイルを使用する際に表示される セキュリティに関する警告を回避できます)。

4.  *Mvc5Mobile.zip* ファイルを右クリックし、**[すべて展開]** をクリックしてファイルを解凍します。

5.  Visual Studio で *Mvc5Mobile.sln* ファイルを開きます。

6.  ソリューション エクスプローラで目的のプロジェクトを右クリックし、**[発行]** をクリックします。

    ![][1]

7.  [Web の発行] で **[Windows Azure の Web サイト]** をクリックします。

    ![][2]

8.  **[サインイン]** をクリックします。

    ![][3]

9.  Windows Azure のユーザー名を入力し、**[続行]** をクリックします。

    ![][4]

10. パスワードを入力し、**[サインイン]** をクリックします。

    ![][5]

11. サインインが完了すると、[既存の Web サイトを選択] ダイアログボックスが表示されます。**[新規]** をクリックします。

    ![][6]

12. **[サイト名]** フィールドに、一意のサイト名のプレフィックスを指定します。完全修飾サイト名は、*\<prefix\>*.azurewebsites.net となります。**[リージョン]** フィールドで、リージョンを選択します。**[作成]** をクリックします。

    ![][7]

13. [Web の発行] ダイアログに、新しい Web サイトのサイト設定が入力されます。**[発行]** をクリックします。

    ![][8]

    Visual Studio で Windows Azure Web サイトへの発行が完了すると、デスクトップ ブラウザーが開き、ライブ Web サイトが表示されます。

14. モバイル ブラウザー エミュレーターを起動し、会議アプリケーション (*<prefix>*.azurewebsites.net) の URL をエミュレーターにコピーし、右上のボタンをクリックして **[Browse by tag]** をクリックします。Internet Explorer 11 を既定のブラウザーとして使用している場合は、`F12` キーを押してから `Ctrl+8` キーを押した後、ブラウザー プロファイルを **Windows Phone** に変更します。 下の図は、縦モードの *AllTags* ビューを示しています (**[Browse by tag]** をクリックした後)

    ![][9]

> [WACOM.NOTE] Visual Studio の中からでも MVC 5 アプリケーションをデバッグできますが、Web サイトを Windows Azure に再度発行すると、モバイル ブラウザーまたはブラウザー エミュレーターからライブ サイトを直接確認できます。

モバイル デバイス上でも読みやすい表示になっています。 ブートストラップ CSS フレームワークによって適用された視覚効果も一部表示されています。 **ASP.NET** リンクをクリックします。

![][10]

ASP.NET タグ ビューは、画面に合わせてズームされています。この処理は、ブートストラップによって 自動的に実行されます。ただし、モバイル ブラウザーに合わせて、このビューをさらに調整することもできます。たとえば、**[Date]** 列は読みにくくなっています。 このチュートリアルでは、後ほど、*AllTags* ビューを モバイル対応に変更します。

## <a name="bkmk_bootstrap"></a>ブートストラップ CSS フレームワーク

MVC 5 の新機能の 1 つに、ブートストラップの標準サポートがあります。 ブートストラップによって、さまざまなビューの表示品質が瞬時に向上することは先ほど確認したとおりです。 たとえば、ブラウザーの幅が小さくなったときに、上部のナビゲーション バーを自動的に折りたたむことができます。 デスクトップ ブラウザーで、ブラウザー ウィンドウのサイズを変更して、ナビゲーション バーの 外観と操作性がどのように変わるかを確認してください。これが、ブートストラップに組み込まれたレスポンシブ Web デザインです。

ブートストラップを使用しない場合の Web アプリケーションの外観を表示するには、*App\_Start\\BundleConfig.cs* を開き、*bootstrap.js* と *bootstrap.css* を含む行をコ ント アウトします。次のコードは、変更後の`RegisterBundles` メソッドにある最後の 2 つのステートメントを示しています。

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

`Ctrl+F5` キーを押してアプリケーションを実行します。

折りたたみ可能なナビゲーション バーが、通常の 順不同一覧として表示されます。**[Browse by tag]** を再度クリックし、**ASP.NET** をクリックします。 モバイル エミュレーター ビューが画面に合わせてズームされないため、テーブルの右側を表示するには横方向にスクロールする必要があります。

![][11]

変更を元に戻し、モバイル ブラウザーをリフレッシュして、モバイル対応画面が復元されたことを確認します。

ブートストラップは、ASP.NET MVC 5 固有の機能ではなく、あらゆる Web アプリケーションでこの機能を 活用できます。しかし ASP.NET MVC 5 プロジェクト テンプレートにはブートストラップが組み込まれているため、MVC 5 Web アプリケーションでは、この機能を既定で利用できます。

ブートストラップの詳細については、[ブートストラップ][ブートストラップ]のサイトを参照してください。

次のセクションでは、モバイル ブラウザー専用ビューを 作成する方法について説明します。

## <a name="bkmk_overrideviews"></a>ビュー、レイアウト、および部分ビューをオーバーライドする

モバイル ブラウザー全般、個々のモバイル ブラウザー、または特定のブラウザーの (レイアウトと部分ビューを含む) 任意のビューをオーバーライドできます。 モバイル専用ビューを用意するには、ビュー
 ファイルをコピーして *.Mobile* をファイル名に追加します。たとえば、モバイル *インデックス* ビューを作成するには、*Views\\Home\\Index.cshtml* をコピーして名前を *Views\\Home\\Index.Mobile.cshtml* に変更します。

このセクションでは、モバイル専用のレイアウト ファイルを作成します。

最初に、*Views\\Shared\\\_Layout.cshtml* をコピーして名前を *Views\\Shared\\\_Layout.Mobile.cshtml* に変更します。*\_Layout.Mobile.cshtml* を開き、タイトルを「**MVC5 Application**」から「**MVC5 Application (Mobile)**」に変更します。

ナビゲーション バーの各 `Html.ActionLink` 呼び出しで、各 *ActionLink* リンクの「Browse by」を削除します。次のコードは、モバイル レイアウト ファイルの完成した `<ul class="nav navbar-nav">` タグです。

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

*Views\\Home\\AllTags.cshtml* ファイルをコピーして名前を *Views\\Home\\AllTags.Mobile.cshtml* に変更します。新しいファイルを開き、次のように `<h2>` 要素を「Tags」から「Tags (M)」に変更します。

    <h2>Tags (M)</h2>

デスクトップ ブラウザー、およびモバイル ブラウザー エミュレーターを使用してタグ ページに移動します。 モバイル ブラウザー エミュレーターでは、先ほど変更した 2 か所 (*\_Layout.Mobile.cshtml* と *AllTags.Mobile.cshtml* で変更した各タイトル) が変更されていることがわかります。

![][12]

対照的に、デスクトップでの表示は変更されていません (*\_Layout.cshtml* と*AllTags.cshtml*)。

![][13]

## <a name="bkmk_browserviews"></a>ブラウザー専用のビューを作成する

モバイル専用のビューやデスクトップ専用のビューに加え、 個別のブラウザーに対してビューを作成できます。たとえば、iPhone ブラウザーや Android ブラウザー専用のビューを作成できます。このセクションでは、iPhone ブラウザーと iPhone バージョンの *AllTags* ビュー用のレイアウトを作成します。

*Global.asax* ファイルを開き、`Application_Start` メソッドの最終行として次のコードを追加します。

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

このコードでは、"iPhone" という表示モードを定義し、受信された各要求をその定義に対して照合します。受信された要求が 定義した条件に一致する場合 (つまり、ユーザー エージェントに "iPhone" という文字列が含まれている場合)、"iPhone" というサフィックスが含まれる名前のビューが ASP.NET MVC によって検索されます。

> [WACOM.NOTE] iPhone や Android など、モバイル ブラウザー専用の表示モードを追加する場合、最初の引数を `0` に設定して (リストの一番上に挿入)、ブラウザー専用のモードがモバイル テンプレート (\*.Mobile.cshtml) よりも優先されるようにします。逆にモバイル テンプレートをリストの一番上に配置すると、目的の表示モードに優先して選択されます (最初に一致したエントリが選択されますが、モバイル テンプレートはすべてのモバイル ブラウザーで一致します)。

コードで、`DefaultDisplayMode` を右クリックし、**[解決]**、`using System.Web.WebPages;` の順にクリックします。 `DisplayModeProvider` 型と `DefaultDisplayMode` 型が定義されている `System.Web.WebPages` 名前空間に参照が追加されます。

![][14]

別の方法として、単純にファイルの `using` セクションに、次の行を手動で追加することもできます。

    using System.Web.WebPages;

変更を保存します。 *Views\\Shared\\\_Layout.Mobile.cshtml* ファイルを *Views\\Shared\\\_Layout.iPhone.cshtml* にコピーします。新しいファイルを開き、タイトルを「`MVC5 Application (Mobile)`」から「 `MVC5 Application (iPhone)`」に変更します。

*Views\\Home\\AllTags.Mobile.cshtml* ファイルを *Views\\Home\\AllTags.iPhone.cshtml* にコピーします。新しいファイルで、`<h2>` 要素を「Tags (M)」から「Tags (iPhone)」に変更します。

アプリケーションを実行します。モバイル ブラウザー エミュレーターを実行し、ユーザー エージェントが "iPhone" に設定されていることを確認して、*AllTags* ビューにアクセスします。 Internet Explorer 11 F12 開発者ツールのエミュレーターを使用している場合は、エミュレーションを次のとおりに構成します。

-   [ブラウザー プロファイル] = **[Windows Phone]**
-   [ユーザー エージェント文字列] = **[カスタム]**
-   [カスタム文字列] = 「**Apple-iPhone5C1/1001.525**」

次のスクリーンショットは、Internet Explorer 11 F12 開発者ツールのエミュレーターで、カスタムのユーザー エージェント文字列 (iPhone 5C のユーザー エージェント文字列) を使用して *AllTags* ビューを描画した画面を示しています。

![][15]

モバイル ブラウザーで **[Speakers]** リンクをタップします。モバイル ビュー(*AllSpeakers.Mobile.cshtml*) がないため、既定のスピーカー表示(*AllSpeakers.cshtml*) がモバイル レイアウト ビュー (*\_Layout.Mobile.cshtml*) を使用して描画されます。下図に示すように、**MVC5 Application (Mobile)** が *\_Layout.Mobile.cshtml* に定義されています。

![][16]

*Views\\\_ViewStart.cshtml* ファイルで次のように`RequireConsistentDisplayMode` を `true` に設定すると、モバイル レイアウト内で既定の (非モバイル) ビューの描画をグローバルに無効化できます。

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

`RequireConsistentDisplayMode` が `true` に設定されていると、モバイル レイアウト (*\_Layout.Mobile.cshtml*) はモバイル ビューだけに使用されます (つまり、ビュー ファイルの名前が ***ViewName**.Mobile.cshtml* という形式の場合です)。モバイル レイアウトが非モバイル ビューでうまく動作しない場合は、 `RequireConsistentDisplayMode` を `true` に設定します。 次のスクリーンショットは、`RequireConsistentDisplayMode` が `true` に設定されている場合の *[Speakers]* ページの表示を示しています (文字列 "Mobile" は上部のナビゲーション バーに表示されません)。

![][17]

ビュー ファイルで `RequireConsistentDisplayMode` を `false` に設定すると、特定のビューの一貫表示モードを無効化できます。 次のマークアップは、*Views\\Home\\AllSpeakers.cshtml* ファイルで `RequireConsistentDisplayMode` を`false` に設定します。

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

このセクションでは、モバイル レイアウトとビューの作成方法および iPhone などの特定のデバイス専用のレイアウトとビューの作成方法を説明しました。 しかし、ブートストラップ CSS フレームワークの最大の利点は レスポンシブ レイアウトの使用により、単一のスタイルシートを デスクトップ、携帯電話、タブレットの各ブラウザーに適用し、一貫した外観と操作性を 実現できることです。次のセクションでは、ブートストラップを利用した モバイル対応ビューの作成方法を説明します。

## <a name="bkmk_Improvespeakerslist"></a>スピーカー一覧を強化する

いま見たように、*スピーカー* ビューは読み取れますが、リンクが小さく、モバイル デバイスではタップが困難です。 このセクションでは、 *AllSpeakers* ビューをモバイル対応に設定し、大きくてタップしやすいリンクが表示され、スピーカーをすばやく見つけることのできる検索ボックスを表示します。

ブートストラップの [リンクされたリスト グループ][リンクされたリスト グループ]のスタイリングを使用して、*スピーカー* ビューを強化します。*Views\\Home\\AllSpeakers.cshtml* で、Razor ファイルの内容を次のコードに置き換えます。

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

`<div>` タグ内の `class="list-group"` 属性によって、ブートストラップのリストのスタイリングが適用され、`class="input-group-item"` 属性によって、ブートストラップのリスト項目のスタイリングが各リンクに適用されます。

モバイル ブラウザーの表示を更新します。更新されたビューは次のようになります。

![][18]

ブートストラップの[リンクされたリスト グループ][リンクされたリスト グループ]のスタイリングを使用すると、各 リンクのボックス全体がクリック可能になり、ユーザー エクスペリエンスが大幅に向上します。 デスクトップ ビューに切り替えると、外観と操作性が一貫していることがわかります。

![][19]

モバイル ブラウザー ビューは強化されましたが、スピーカーの長い一覧をスクロールするのは困難です。ブートストラップは、検索フィルターを標準で提供していませんが、数行のコードによって追加できます。まずビューに検索ボックスを追加した後、JavaScript コードを関連付けてフィルター機能を設定します。次に示すように、*Views\\Home\\AllSpeakers.cshtml* で、\<form\> タグを \<h2\> タグの直後に追加します。

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

`<form>` タグと `<input>` タグの両方にブートストラップ のスタイルが適用されていることに注意してください。`<span>` 要素によって、ブートストラップの [glyphicon][glyphicon] が 検索ボックスに追加されます。

*Scripts* フォルダーで、*filter.js* という JavaScript ファイルを追加します。ファイルを開き、次のコードを貼り付けます。

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

さらに、登録済みのバンドルに filter.js を含める必要があります。 *App\_Start\\BundleConfig.cs* を開き、最初のバンドルを変更します。 次のように、最初の (**jquery** バンドルの) `bundles.Add` ステートメントを変更して、 *Scripts\\filter.js* を含めます。

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

**jquery** バンドルは、既定の *\_Layout* ビューによって既に描画されています。後で、同じ JavaScript コードを利用して、フィルター機能を他のリスト ビューに適用できます。

モバイル ブラウザーを情報に更新し、*AllSpeakers* ビューに移動します。 検索ボックスに、「sc」と入力します。入力した検索文字列に従って、スピーカー一覧がフィルタリングされます。

![][20]

## <a name="bkmk_improvetags"></a>タグ一覧を強化する

*スピーカー* ビューと同様に、*タグ* ビューも読み取れますが、リンク が小さく、モバイル デバイスではタップが困難です。*タグ* ビューも*スピーカー* ビューと同じように修正できます。上記のコード 変更を使用している場合、*Views\\Home\\AllTags.cshtml* で次の`Html.ActionLink` メソッド構文を使用することを除き、手順は同じです。

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

デスクトップ ブラウザーを更新すると、次のように表示されます。

![][21]

モバイル ブラウザーを更新すると、次のように表示されます。

![][22]

> [WACOM.NOTE] モバイル ブラウザーで元の一覧書式設定が残り、ブートストラップのスタイリングが正常に機能していないように見えますが、これは先ほどモバイル専用のビューを作成したためです。ここではブートストラップ CSS フレームワークを使用してレスポンシブ Web デザインを作成するため、これらのモバイル専用のビューとモバイル専用のレイアウト ビューを削除してください。その後、モバイル ブラウザーを更新すると、ブートストラップのスタイリングが表示されます。

## <a name="bkmk_improvedates"></a>日付一覧を強化する

*スピーカー* ビューおよび *タグ* ビューと同じように *日付* ビューもモバイル デバイスで使いやすいように強化できます。上記のコードの変更を使用している場合、*Views\\Home\\AllDates.cshtml* で `Html.ActionLink` メソッドの構文を使用することを除き、手順は同じです。

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

モバイル ブラウザー ビューを更新すると、次のように表示されます。

![][23]

*日付* ビューはさらに、date-time 値を date ごとに整理して表示品質を高めることができます。これには、ブートストラップの [パネル][パネル] スタイリングを使用します。 *Views\\Home\\AllDates.cshtml* ファイルの内容を 次のコードで置き換えます。

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

このコードでは、一覧の日付ごとに別個の `<div class="panel panel-primary">` タグを作成し、これまでと同じそれぞれのリンクに対して[リンクされたリスト グループ][リンクされたリスト グループ]を使用します。 このコードを実行すると、モバイル ブラウザーの表示は次のようになります。

![][24]

デスクトップ ブラウザーに切り替えます。モバイル ブラウザーと一貫性を保った画面が表示されます。

![][25]

## <a name="bkmk_improvesessionstable"></a>SessionsTable ビューを強化する

このセクションでは、*SessionsTable* ビューがより適切に モバイルに対応するように調整します。変更箇所は、これまでに作成した他のビューよりも広範囲にわたります。

モバイル ブラウザーで、**[Tag]** をタップして検索ボックスに「`asp`」と入力します。

![][26]

**[ASP.NET]** リンクをタップします。

![][27]

ご覧のように、現在、デスクトップ ブラウザーで適切に表示できるように設計された表形式で表示されています。しかし、モバイル ブラウザーで読み取るのは少し困難です。 これを修正するために、*Views\\Home\\SessionsTable.cshtml* を開き、ファイルの内容を次のコードに置き換えます。

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

このコードでは、次の 3 つの処理を行います。

-   ブートストラップの [カスタムのリンクされたリスト グループ][カスタムのリンクされたリスト グループ]
    を使用して、セッション情報を縦 1 列に並べ、すべての情報をモバイル ブラウザーで読み取れるようにします (list-group-item-text などのクラスを使用します)。
-   レイアウトに[グリッド システム][グリッド システム]を適用して、
    デスクトップ
     ブラウザーではセッション項目を横並びに表示し、モバイル ブラウザーでは縦に表示します (col-md-4 クラスを使用します)。
-   [レスポンシブ ユーティリティ][レスポンシブ ユーティリティ]を使用して、
    モバイル ブラウザーで表示したときに、セッション タグを非表示にします (hidden-xs クラスを使用します)。

タイトル リンクをタップして、それぞれのセッションに移動することもできます。次の図では、コードの変更が反映されています。

![][0]

適用したブートストラップのグリッド システムによって、モバイル ブラウザーのセッションが自動的に縦並びに配置されます。また、タグが非表示になります。デスクトップ ブラウザーに切り替えます。

![][28]

デスクトップ ブラウザーでは、タグが表示されています。また、適用したブートストラップのグリッド システムによって、セッション項目が 2 列に表示されています。 ブラウザーを広げると、配置が 3 列表示に変わります。

## <a name="bkmk_improvesessionbycode"></a>SessionByCode ビューを強化する

最後に、*SessionByCode* ビューをモバイル対応に修正します。

モバイル ブラウザーで、**[Tag]** をタップして検索ボックスに「`asp`」と入力します。

![][26]

**[ASP.NET]** リンクをタップします。ASP.NET タグのセッションが表示されます。

![][0]

**[Building a Single Page Application with ASP.NET and AngularJS]** リンクをタップします。

![][29]

既定のデスクトップ ビューでも問題はありませんが、ブートストラップの GUI コンポーネントを使用して簡単に表示品質を高めることができます。 *Views\\Home\\SessionByCode.cshtml* を開き、内容を次のマークアップと置き換えます。

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

モバイル ブラウザーの表示を更新します。次の図では、行ったコードの変更が反映されています。

![][30]

## まとめ

このチュートリアルでは、ASP.NET MVC 5 を使用して モバイル対応の Web アプリケーションを開発する方法を学習しました。学習した内容は次のとおりです。

-   ASP.NET MVC 5 アプリケーションの Windows Azure Web サイトへのデプロイ
-   ブートストラップを使用した MVC 5 アプリケーションでのレスポンシブな Web レイアウトの作成
-   ビュー全般や個別のビューのレイアウト、ビュー、および部分ビューのオーバーライド
-   `RequireConsistentDisplayMode` プロパティを使用した、レイアウトや部分的なオーバーライドの実行の制御
-   iPhone ブラウザーなど、特定のブラウザー専用のビューの作成
-   Razor コードでのブートストラップのスタイリングの適用

## 関連項目

-   [ブートストラップ][ブートストラップ]のサイト
-   [ブートストラップの公式ブログ][ブートストラップの公式ブログ]
-   [Tutorial Republic による Twitter Bootstrap Tutorial][Tutorial Republic による Twitter Bootstrap Tutorial]
-   [The Bootstrap Playground][The Bootstrap Playground]
-   [W3C 勧告: モバイル Web アプリケーションのベスト プラクティス][W3C 勧告: モバイル Web アプリケーションのベスト プラクティス]
-   [W3C のメディア クエリに関する勧告候補][W3C のメディア クエリに関する勧告候補]



  [Visual Studio Express 2013 for Web]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [スタート プロジェクト]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
  [0]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png

  [Visual Studio 2013 用の Azure SDK]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
  [Web Platform Installer - Azure SDK for .NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
  [Internet Explorer 11 F12 developer tools]: http://msdn.microsoft.com/ja-jp/library/ie/dn255001.aspx
  [Google Chrome DevTools]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
  [Opera Mobile Emulator]: http://www.opera.com/developer/tools/mobile/
  [完成したプロジェクトのダウンロード]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
  [Windows Azure Web サイトにスタート プロジェクトをデプロイする]: #bkmk_DeployStarterProject
  [ブートストラップ CSS フレームワーク]: #bkmk_bootstrap
  [ビュー、レイアウト、および部分ビューをオーバーライドする]: #bkmk_overrideviews
  [スピーカー一覧を強化する]: #bkmk_Improvespeakerslist
  [タグ一覧を強化する]: #bkmk_improvetags
  [日付一覧を強化する]: #bkmk_improvedates
  [SessionsTable ビューを強化する]: #bkmk_improvesessionstable
  [SessionByCode ビューを強化する]: #bkmk_improvesessionbycode
  [1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png
  [2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png
  [3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png
  [4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png
  [5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png
  [6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png
  [7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png
  [8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png
  [9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png
  [10]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png
  [11]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png
  [ブートストラップ]: http://getbootstrap.com/
  [12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png
  [13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png
  [14]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png
  [15]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png
  [16]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png
  [17]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png
  [リンクされたリスト グループ]: http://getbootstrap.com/components/#list-group-linked
  [18]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png
  [19]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png
  [glyphicon]: http://getbootstrap.com/components/#glyphicons
  [20]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png
  [21]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png
  [22]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png
  [23]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png
  [パネル]: http://getbootstrap.com/components/#panels
  [24]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png
  [25]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png
  [26]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png
  [27]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png
  [カスタムのリンクされたリスト グループ]: http://getbootstrap.com/components/#list-group-custom-content
  [グリッド システム]: http://getbootstrap.com/css/#grid
  [レスポンシブ ユーティリティ]: http://getbootstrap.com/css/#responsive-utilities
  [28]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png
  [29]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png
  [30]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png
  [ブートストラップの公式ブログ]: http://blog.getbootstrap.com/
  [Tutorial Republic による Twitter Bootstrap Tutorial]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
  [The Bootstrap Playground]: http://www.bootply.com/
  [W3C 勧告: モバイル Web アプリケーションのベスト プラクティス]: http://www.w3.org/TR/mwabp/
  [W3C のメディア クエリに関する勧告候補]: http://www.w3.org/TR/css3-mediaqueries/
