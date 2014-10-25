<properties linkid="develop-net-how-to-guides-new-relic-app" urlDisplayName="New Relic App Performance Management" pageTitle="New Relic App Performance Management on Azure" metaKeywords="new relic Azure, performance azure" description="Learn how to use New Relic's performance monitoring on Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="New Relic Application Performance Management on Azure Websites" authors="new relic" solutions="" manager="dwrede" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="new relic"></tags>

# Azure Web サイトの New Relic によるアプリケーション パフォーマンス管理

このガイドでは、アプリケーションに New Relic による世界クラスのパフォーマンスの監視を Azure Web サイトに追加する方法について説明します。アプリケーションに New Relic をすぐに追加できる簡単なプロセスについて説明し、New Relic の機能をいくつか紹介します。New Relic の使い方の詳細については、「[New Relic の使用][New Relic の使用]」を参照してください。

## New Relic とは

New Relic は、実稼働アプリケーションを監視してそのパフォーマンスと信頼性を詳しく把握できるようにする開発者向けのツールです。パフォーマンス上の問題を特定して診断する時間を短縮できるように設計されており、その問題の解決に必要な情報をすぐに確認できます。

New Relic は、Web トランザクションの読み込み時間とスループットを、サーバーとユーザーのブラウザーの両方から追跡します。データベースでの処理にかかった時間の表示、時間がかかったクエリと Web 要求の分析、アップタイムの監視と通知、アプリケーションの例外の追跡など、多数の処理を実行します。

## Azure ストアの New Relic 特別料金

New Relic Standard は、Azure ユーザーであれば無料でご利用いただけます。
New Relic Pro は、使用している Web サイト モード (および占有モードを使用している場合はインスタンス サイズ) に基づいて複数のパッケージで提供されます。

料金情報については、[Windows Azure ストアの New Relic のページ][Windows Azure ストアの New Relic のページ]を参照してください。

<div class="dev-callout"> 
<strong>注: </strong>
<p>コンピューティング インスタンス 10 個までについてのみ、料金を表示しています。10 を超える場合は、ボリューム ディスカウント料金について New Relic (sales@newrelic.com) までお問い合わせください。</p>
</div>

Azure ユーザーは、New Relic エージェントのデプロイ時に、New Relic Pro の 2 週間の試用サブスクリプションをご利用になれます。

## Azure ストアを使用して New Relic にサインアップする

New Relic は、Azure の Web ロール、Worker ロール、および Web サイトとシームレスに統合されます。

Azure ストアから New Relic に直接サインアップするには、次の簡単な 4 つの手順を実行します。

### 手順 1. Azure ストアからサインアップする

1.  [Azure 管理ポータル][Azure 管理ポータル]にログインします。
2.  管理ポータルの下のウィンドウの **[新規]** をクリックします。
3.  **[ストア]** をクリックします。
4.  **[アドインの選択]** ダイアログで、**[New Relic]** をクリックし、**[次へ]** をクリックします。
5.  **[個人用サービス]** ダイアログで、希望の New Relic プランを選択します。
6.  Azure の設定における New Relic サービスの表示名を入力するか、
    既定値 **NewRelic** を使用します。この名前は、Azure ストアの
    サブスクライブ項目の一覧で一意になっている必要があります。
7.  リージョンの値を選択します (たとえば **[米国西部]**)。
8.  **[次へ]** をクリックします。
9.  **[購入の確認]** ダイアログで、プランと料金情報および法律条項を
    確認します。条項に同意したら、**[購入]** をクリックします。
10. **[購入]** をクリックすると、New Relic アカウントの作成プロセスが開始されます。その状態は、Azure 管理ポータルで監視できます。
11. New Relic ライセンス キーを取得するには、前の手順で作成したアドオンをクリックし、**[出力値]** をクリックします。
12. 表示されたライセンス キーをコピーします。このキーは、New Relic NuGet パッケージのインストール時に入力する必要があります。

### 手順 2. New Relic パッケージをインストールする

New Relic Web サイト エージェントは、NuGet パッケージとして配布され、Visual Studio または WebMatrix を使用して Web サイトに追加できます。Azure Web サイトで Visual Studio や WebMatrix を使用する方法については、「[ASP.NET Web アプリケーションを Azure の Web サイトにデプロイする][ASP.NET Web アプリケーションを Azure の Web サイトにデプロイする]または「[Microsoft WebMatrix を使用して Web サイトを開発してデプロイする][Microsoft WebMatrix を使用して Web サイトを開発してデプロイする]」を参照してください。

使用している開発環境に応じて次の手順を実行します。

**Visual Studio**

1.  Visual Studio の Web サイト ソリューションを開きます。

2.  **[ツール]、[ライブラリ パッケージ マネージャー]、\[パッケージ マネージャー コンソール]** の順にクリックし、パッケージ マネージャー コンソールを開きます。プロジェクトをパッケージ マネージャーコンソール ウィンドウの一番上に表示される既定のプロジェクトに設定します。

    ![パッケージ マネージャー コンソール][パッケージ マネージャー コンソール]

3.  パッケージ マネージャーのコマンド プロンプトで、次のコマンドを使用してパッケージをインストールします。

        Install-Package NewRelic.Azure.WebSites

4.  ライセンス キーのダイアログで、Azure ストアから受け取ったライセンス キーを入力します。

    ![ライセンス キーの入力][ライセンス キーの入力]

<!--5. Optional: At the application name prompt, enter your app's name as it will    appear in New Relic's dashboard. Or, use your solution name as the default.      ![enter application name](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget08.png)-->

**WebMatrix**

1.  WebMatrix を使用して Web サイトを開きます。

2.  リボンの **[ホーム]** タブで、**[NuGet]** を選択します。

    ![[ホーム] タブの NuGet ボタン][]

3.  NuGet ギャラリーで、ソースを **NuGet 公式パッケージのソース**に設定し、NewRelic.Azure.WebSites を探します。

    ![NuGet ギャラリーで NewRelic.Azure.WebSites を検索][NuGet ギャラリーで NewRelic.Azure.WebSites を検索]

4.  **[Azure の Web サイトの New Relic]** エントリを選択し、**[インストール]** をクリックします。

5.  パッケージをインストールすると、サイトに **newrelic** というフォルダーが追加されます。このフォルダーを展開し、**newrelic.config** ファイルを開きます。このファイルで、値 **REPLACE\_WITH\_LICENSE\_KEY** を、Azure ストアから受け取ったライセンス キーに置き換えます。

    ![newrelic フォルダーを展開して newrelic.conf を選択][newrelic フォルダーを展開して newrelic.conf を選択]

    ライセンス キー情報を追加したら、**newrelic.config** ファイルへの変更を保存します。

### 手順 3. Web サイトを構成して、アプリケーションを発行する

前の手順でアプリケーションに追加した New Relic パッケージの構成は、Azure Web サイトに**アプリケーション設定**を追加することで行います。これらの設定を追加するには、次の手順を実行します。

1.  [Azure の管理ポータル][Azure 管理ポータル]にサインインして、Web サイトにアクセスします。

2.  Web サイトから、**[構成]** を選択します。**[開発者分析]** セクションで、**[アドオン]** または **[カスタム]** を選択します。どちらの方法でも、出力は同じです。ただし、必要とされる入力はわずかに異なります。**[アドオン]** では、現在の New-Relic ライセンスが一覧に表示され、その中から 1 つを選択します。これに対して **[カスタム]** では、手動でライセンス キーを指定する必要があります。

    **[アドオン]** を選択した場合は、**[アドオンの選択]** フィールドで New-Relic ライセンスを選択します。

    ![アドオン フィールドの画像][アドオン フィールドの画像]

    **[カスタム]** を選択した場合は、**[プロバイダー]** で New-Relic を選択し、**[プロバイダー キー]** フィールドにライセンスを入力します。

    ![カスタム フィールドの画像][カスタム フィールドの画像]

3.  **[開発者分析]** でライセンスを指定したら、**[保存]** をクリックします。保存が完了すると、ページの **[アプリケーション設定]** セクションに次の値が追加され、New-Relic がサポートされます。

    <table border="1">
    <thead>
    <tr>
    <td>
    キー

    </td>
    <td>
    値

    </td>
    </tr>
    </thead>
    <tbody>
    <tr>
    <td>
    COR\\\_ENABLE\\\_PROFILING

    </td>
    <td>
    1

    </td>
    </tr>
    <tr>
    <td>
    COR\\\_PROFILER

    </td>
    <td>
    {71DA0A04-7777-4EC6-9643-7D28B46A8A41}

    </td>
    </tr>
    <tr>
    <td>
    COR\\\_PROFILER\\\_PATH

    </td>
    <td>
    C:\\Home\\site\\wwwroot\\newrelic\\NewRelic.Profiler.dll

    </td>
    </tr>
    <tr>
    <td>
    NEWRELIC\\\_HOME

    </td>
    <td>
    C:\\Home\\site\\wwwroot\\newrelic

    </td>
    </tr>
    <tr>
    <td>
    NEWRELIC\\\_LICENSEKEY

    </td>
    <td>
    ライセンス キー

    </td>
    </tr>
    </tbody>
    </table>

    <div class="dev-callout"> 
<strong>注</strong> 
<p>新しい<strong>アプリケーション設定</strong>が反映されるまで最大 30 秒かかる場合があります。設定をすぐに強制適用するには、Web サイトを再起動します。</p> 
</div>

4.  Visual Studio または WebMatrix を使用して、アプリケーションを発行します。

### 手順 4. New Relic でアプリケーションのパフォーマンスをチェックする

New Relic のダッシュボードを表示するには、次の手順を実行します。

1.  Azure ポータルで、**[管理]** をクリックします。
2.  New Relic アカウントの電子メールとパスワードを使用してサインインします。
3.  New Relic のメニュー バーで、**[アプリケーション]、(アプリケーションの名前)** の順にクリックします。

    **[監視] の [概要]** ダッシュボードが自動的に表示されます。

    ![New Relic の監視ダッシュボード][New Relic の監視ダッシュボード]

    **[アプリケーション]** メニューの一覧からアプリを選択すると、**[概要]** ダッシュボードに、現在のアプリ サーバーとブラウザーの情報が表示されます。

### <span id="using-new-relic"></span></a>New Relic の使用

[アプリケーション] メニューの一覧からアプリケーションを選択すると、[概要] ダッシュボードに、現在のアプリ サーバーとブラウザーの情報が表示されます。2 つのビューを切り替えるには、**[アプリ サーバー]** または **[ブラウザー]** をクリックします。

<a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">標準の New Relic UI</a> と <a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">ダッシュボードのドリルダウン</a>機能のほかにも、アプリケーションの [概要] ダッシュボードにはさまざまな機能があります。

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>目的操作</strong></th>
<th align="left"><strong>これを行うには、次の手順を実行します。</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">選択したアプリケーションのサーバーまたはブラウザーのダッシュボード情報を表示する</td>
<td align="left"><strong>[アプリ サーバー]</strong> または <strong>[ブラウザー]</strong> をクリックします。</td>
</tr>
<tr class="even">
<td align="left">アプリケーションの <a href="https://newrelic.com/docs/site/apdex">Apdex</a> スコアのしきい値レベルを表示する</td>
<td align="left">[Apdex スコア] の <strong>[?] <strong>アイコン</strong></strong>をポイントします。</td>
</tr>
<tr class="odd">
<td align="left">世界規模の Apdex の詳細を表示する</td>
<td align="left">[概要] の<strong>ブラウザー</strong> ビューで、グローバル Apdex マップの任意の場所をポイントします。<br /><strong>ヒント:</strong> 選択したアプリケーションの <a href="https://newrelic.com/docs/site/geography">[地理]</a> ダッシュボードに直接移動するには、<strong>グローバル Apdex</strong> のタイトルをクリックするか、グローバル Apdex マップの任意の場所をクリックします。</td>
</tr>
<tr class="even">
<td align="left"><a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard">[Web トランザクション]</a> ダッシュボードを表示する</td>
<td align="left">アプリケーションの [概要] ダッシュボードの [Web トランザクション] テーブルをクリックします。または、特定の Web トランザクションに関する詳細 (<a href="https://newrelic.com/docs/site/key-transactions">主要トランザクション</a>など) を表示するには、その名前をクリックします。</td>
</tr>
<tr class="odd">
<td align="left"><a href="https://newrelic.com/docs/site/errors">[エラー]</a> ダッシュボードを表示する</td>
<td align="left">アプリケーションの [概要] ダッシュボードの [エラー率] グラフのタイトルをクリックします。<br /><strong>ヒント:</strong> [エラー] ダッシュボードは、<strong>[アプリケーション]</strong>、(アプリ)、[イベント]、[エラー] の順にクリックして表示することもできます。</td>
</tr>
<tr class="even">
<td align="left">アプリケーションのサーバーの詳細を表示する</td>
<td align="left"><p>次のいずれかを実行します。</p>
<p></p>
<ul>
<li>ホストのテーブル ビューと各ホストのメトリックの詳細内訳を切り替えます。</li>
<li>個々のサーバーの名前をクリックします。</li>
<li>個々のサーバーの Apdex スコアをポイントします。</li>
<li>個々のサーバーの CPU 使用率またはメモリをクリックします。</li>
</ul>
</p>
</p></td>
</tr>
</tbody>
</table>

以下に、ブラウザー ビューを選択したときのアプリケーションの [概要] ダッシュボードの例を示します。

![パッケージ マネージャー コンソール][1]

## 次のステップ

詳細については、次に示すその他のリソースを参照してください。

-   [Installing the .NET Agent for Azure Web Sites (Azure の Web サイト用の .NET エージェントのインストール)][Installing the .NET Agent for Azure Web Sites (Azure の Web サイト用の .NET エージェントのインストール)]: New Relic の .NET エージェントのインストール手順
-   「[The New Relic User Interface (New Relic ユーザー インターフェイス)][The New Relic User Interface (New Relic ユーザー インターフェイス)]」:
    New Relic UI の概要、ユーザー権限とプロファイルの設定、標準機能の使用、およびダッシュボードのドリルダウンの詳細
-   「[Applications Overview (アプリケーションの概要)」][Applications Overview (アプリケーションの概要)」]: New Relic のアプリケーションの [概要] ダッシュボードを使用する際の機能
-   「[Apdex][Apdex]」: Apdex によってエンド ユーザーのアプリケーションに対する満足度がどのように測定されるかに関する概要
-   「[Real User Monitoring][Real User Monitoring]」: RUM を使用して、ユーザーのブラウザーで Web ページの
    読み込みにかかった時間、アクセス元の地域、および使用しているブラウザーに関する詳細を確認する方法の概要
-   「[Finding Help (ヘルプの利用)][Finding Help (ヘルプの利用)]」: New Relic のオンライン ヘルプ センターで利用できるリソース

  [New Relic の使用]: #using-new-relic
  [Windows Azure ストアの New Relic のページ]: http://www.windowsazure.com/ja-jp/gallery/store/new-relic/new-relic/
  [Azure 管理ポータル]: https://manage.windowsazure.com
  [ASP.NET Web アプリケーションを Azure の Web サイトにデプロイする]: http://www.windowsazure.com/ja-jp/develop/net/tutorials/get-started/
  [Microsoft WebMatrix を使用して Web サイトを開発してデプロイする]: http://www.windowsazure.com/ja-jp/develop/net/tutorials/website-with-webmatrix/
  [パッケージ マネージャー コンソール]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget04.png
  [ライセンス キーの入力]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
  [ホーム タブの NuGet ボタン]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
  [NuGet ギャラリーで NewRelic.Azure.WebSites を検索]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png
  [newrelic フォルダーを展開して newrelic.conf を選択]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
  [アドオン フィールドの画像]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
  [カスタム フィールドの画像]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png
  [New Relic の監視ダッシュボード]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app.png
  []: https://newrelic.com/docs/site/the-new-relic-ui#functions
  [と]: https://newrelic.com/docs/site/the-new-relic-ui#drilldown
  [Apdex]: https://newrelic.com/docs/site/apdex
  [地理]: https://newrelic.com/docs/site/geography
  [Web トランザクション]: https://docs.newrelic.com/docs/applications-menu/transactions-dashboard
  [主要トランザクション]: https://newrelic.com/docs/site/key-transactions
  [エラー]: https://newrelic.com/docs/site/errors
  [1]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app_browser.png
  [Installing the .NET Agent for Azure Web Sites (Azure の Web サイト用の .NET エージェントのインストール)]: https://newrelic.com/docs/dotnet/azure-web-sites-beta#manual_install
  [The New Relic User Interface (New Relic ユーザー インターフェイス)]: https://newrelic.com/docs/site/the-new-relic-ui
  [Applications Overview (アプリケーションの概要)」]: https://newrelic.com/docs/site/applications-overview
  [Real User Monitoring]: https://newrelic.com/docs/features/real-user-monitoring
  [Finding Help (ヘルプの利用)]: https://newrelic.com/docs/site/finding-help
