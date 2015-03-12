<properties 
	pageTitle="Azure での New Relic の使用 - Azure の機能ガイド" 
	description="New Relic サービスを使用して Azure アプリケーションを管理および監視する方法について説明します。" 
	services="" 
	documentationCenter=".net" 
	authors="stepsic-microsoft-com" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="stepsic"/>



#Azure の New Relic によるアプリケーション パフォーマンス管理

このガイドでは、Azure でホストされるアプリケーションに New Relic による世界クラスのパフォーマンスの監視を追加する方法について説明します。アプリケーションに New Relic をすぐに追加できる簡単なプロセスについて説明し、New Relic の機能をいくつか紹介します。New Relic の使い方の詳細については、「[New Relic の使用](#using-new-relic)」をご覧ください。

New Relic とは
--

New Relic は、実稼働アプリケーションを監視してそのパフォーマンスと信頼性を詳しく把握できるようにする開発者向けのツールです。パフォーマンス上の問題を特定して診断する時間を短縮できるように設計されており、その問題の解決に必要な情報をすぐに確認できます。

New Relic は、Web トランザクションの読み込み時間とスループットを、サーバーとユーザーのブラウザーの両方から追跡します。データベースでの処理にかかった時間の表示、時間がかかったクエリと Web 要求の分析、アップ タイムの監視と通知、アプリケーションの例外の追跡など、多数の処理を実行します。

Azure ストアの New Relic 特別料金
--

New Relic Standard は、Azure ユーザーであれば無料でご利用いただけます。
New Relic Pro は、Azure クラウド サービスのインスタンス サイズに基づいて提供されます

料金情報については、[Azure ストアの New Relic のページ](http://azure.microsoft.com/gallery/store/new-relic/new-relic/)をご覧ください。

> [AZURE.NOTE] コンピューティング インスタンス 10 個までについてのみ、料金を表示しています。10 を超える場合は、ボリューム ディスカウント料金について New Relic (sales@newrelic.com) までお問い合わせください。

Azure ユーザーは、New Relic エージェントのデプロイ時に、New Relic Pro の 2 週間の試用サブスクリプションをご利用になれます。

Azure ストアを使用して New Relic にサインアップする
--

New Relic は、Azure の Web ロールおよびワーカー ロールとシームレスに統合されます。

Azure ストアから New Relic に直接サインアップするには、次の簡単な 3 つの手順を実行します。

### 手順 1.Azure ストアからサインアップする

1. [Azure 管理ポータル](https://manage.windowsazure.com)にログインします。
2. 管理ポータルの下のウィンドウの **[新規]** をクリックします。
3. **[ストア]** をクリックします。
4. **[サービスの選択]** ダイアログで、**[New Relic]** をクリックし、**[次へ]** をクリックします。
5. **[個人用サービス]** ダイアログで、希望の New Relic プランを選択します。
6. 該当する場合は、プロモーション コードを入力します。
7. Azure の設定における New Relic サービスの表示名を入力するか、既定値 **NewRelic** を使用します。この名前は、Azure ストアのサブスクライブ項目の一覧で一意になっている必要があります。
8. リージョンの値を選択します (たとえば **[米国西部]**)。
9. **[次へ]** をクリックします。
10. **[購入の確認]** ダイアログで、プランと料金情報を確認し、法律条項を確認します。条項に同意したら、**[購入]** をクリックします。
11. **[購入]** をクリックすると、New Relic アカウントの作成プロセスが開始します。その状態は、Azure 管理ポータルで監視できます。
12. New Relic のライセンス キーを取得するには、**[出力値]** をクリックします。 
13. 表示されたライセンス キーをコピーします。このキーは、New Relic NuGet パッケージのインストール時に入力する必要があります。

### 手順 2.NuGet パッケージをインストールします。

1. Visual Studio ソリューションを開くか、**[ファイル]、[新規作成]、[プロジェクト]** の順にクリックして新しいソリューションを作成します。

	![Visual Studio](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png)

2. ソリューションに Azure クラウド サービス プロジェクトがまだ含まれていない場合は、ソリューション エクスプローラーでアプリを右クリックし、**[Azure クラウド サービス プロジェクトの追加]** をクリックして、プロジェクトを追加します。

	![Create cloud service](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png)

3. **[ツール]、[ライブラリ パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順にクリックし、パッケージ マネージャー コンソールを開きます。プロジェクトをパッケージ マネージャー コンソール ウィンドウの一番上に表示される既定のプロジェクトに設定します。

	![Package manager console](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png)

4. パッケージ マネージャー コマンド プロンプトで「 `Install-Package NewRelicWindowsAzure`」と入力し、**Enter** キーを押します。

	![install in package manager](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png)

5. ライセンス キーのダイアログで、Azure ストアから受け取ったライセンス キーを入力します。

	![enter license key](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png)

6. 省略可能:アプリケーション名のダイアログで、New Relic のダッシュボードに表示されるアプリケーションの名前を入力します。または、既定のソリューション名を使用します。

	![enter application name](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png)

7. ソリューション エクスプローラーで、Azure クラウド サービス プロジェクトを右クリックし、**[発行]** をクリックします。

	![public the cloud project](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png)


**注:** 初めてこのアプリを Azure にデプロイする場合は、
Azure の資格情報を入力するように求められます。詳細については、「<a href="/ja-jp/develop/net/tutorials/get-started/">Deploying an ASP.NET Web Application to a Azure Web Site (ASP.NET Web アプリケーションを Azure の Web サイトにデプロイする)</a>」をご覧ください。

![publish settings](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png)

### 手順 3.New Relic でアプリケーションのパフォーマンスをチェックする

New Relic のダッシュボードを表示するには、次の手順を実行します。

1. Azure ポータルで、**[管理]** をクリックします。
2. New Relic アカウントの電子メールとパスワードを使用してサインインします。
3. New Relic のメニュー バーで、**[アプリケーション]、(アプリケーションの名前)** の順にクリックします。

	**[監視] の [概要]** ダッシュボードが自動的に表示されます。

	![New Relic monitoring dashboard](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png)

	**[アプリケーション]** メニューの一覧からアプリを選択すると、**[概要]** ダッシュボードに、現在のアプリ サーバーとブラウザーの情報が表示されます。

### <a id="using-new-relic"></a>New Relic の使用

[アプリケーション] メニューの一覧からアプリを選択すると、[概要] ダッシュボードに、現在のアプリ サーバーとブラウザーの情報が表示されます。2 つのビューを切り替えるには、**[アプリ サーバー]** または **[ブラウザー]** をクリックします。

<a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">標準の New Relic UI</a> と<a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">ダッシュボードのドリルダウン</a>機能のほかにも、アプリケーションの [概要] ダッシュボードにはさまざまな機能があります。

<table border="1">
  <thead>
    <tr>
      <th><b>目的操作</b></th>
      <th><b>これを行うには、次の手順を実行します。</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>選択したアプリケーションのサーバーまたはブラウザーのダッシュボード情報を表示する</td>
       <td><b>[アプリ サーバー]</b> または <b>[ブラウザー]</b> をクリックします。</td>
    </tr>
€<tr>
€<td>アプリの <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> スコアのしきい値レベルを表示する</td>
       <td>[Apdex スコア] の <b>[?]<b> アイコンをポイントします。</b></b></td>
    </tr>
    <tr>
       <td>世界規模の Apdex の詳細を表示する</td>
       <td>[概要] の<b>ブラウザー</b> ビューで、グローバル Apdex マップの任意の場所をポイントします。<br /><b>ヒント:</b> 選択したアプリの <a href="https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard" target="_blank">[地理]</a> ダッシュボードに直接移動するには、<b>グローバル Apdex</b> のタイトルをクリックするか、グローバル Apdex マップの任意の場所をクリックします。</td>
    </tr>
    <tr>
       <td><a href="https://newrelic.com/docs/applications-dashboards/web-transactions" target="_blank">[Web トランザクション]</a> ダッシュボードを表示する</td>
       <td>アプリケーションの [概要] ダッシュボードの [Web トランザクション] テーブルをクリックします。または、特定の Web トランザクションに関する詳細 (<a href="https://newrelic.com/docs/site/key-transactions" target="_blank">主要トランザクション</a>など) を表示するには、その名前をクリックします。</td>
    </tr>
    <tr>
       <td><a href="https://newrelic.com/docs/site/errors" target="_blank">[エラー]</a> ダッシュボードを表示する</td>
       <td>アプリケーションの [概要] ダッシュボードの [エラー率] グラフのタイトルをクリックします。<br /><b>ヒント:</b> [エラー] ダッシュボードは、<b>[アプリケーション]</b> &gt; (アプリ) &gt; [イベント] &gt; [エラー] の順にクリックして表示することもできます。</td>
    </tr>
    <tr>
       <td>アプリケーションのサーバーの詳細を表示する</td>
       <td><p>次のいずれかを実行します。<p>
        <ul>
          <li>ホストのテーブル ビューと各ホストのメトリックの詳細内訳を切り替えます。</li>
          <li>個々のサーバーの名前をクリックします。</li>
          <li>個々のサーバーの Apdex スコアをポイントします。</li>
          <li>個々のサーバーの CPU 使用率またはメモリをクリックします。</li>
        </ul>
       </p></p></td>
    </tr>
€</tbody>
</table>

以下に、ブラウザー ビューを選択したときのアプリケーションの [概要] ダッシュボードの例を示します。

![Package manager console](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png)

## 次のステップ

詳細については、次に示すその他のリソースをご覧ください。

 * 「[Installing the on Azure (Azure への .NET エージェントのインストール)](https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure)」:New Relic の .NET エージェントのインストール手順 
 * 「[The New Relic User Interface (New Relic ユーザー インターフェイス)](https://newrelic.com/docs/site/the-new-relic-ui)」: 
New Relic UI の概要、ユーザー権限とプロファイルの設定、標準機能の使用、およびダッシュボードのドリルダウンの詳細
 * 「[Applications Overview (アプリケーションの概要)](https://newrelic.com/docs/site/applications-overview)」:New Relic のアプリケーションの [概要] ダッシュボードを使用する際の機能
 * 「[Apdex](https://newrelic.com/docs/site/apdex)」:Apdex によってエンド ユーザーのアプリケーションに対する満足度がどのように測定されるかに関する概要
 * 「[Real User Monitoring](https://newrelic.com/docs/features/real-user-monitoring)」:RUM を使用して、ユーザーのブラウザーで Web ページの読み込みにかかった時間、アクセス元の地域、使用ブラウザーに関する詳細を確認する方法の概要
 * 「[Finding Help (ヘルプの利用)](https://newrelic.com/docs/site/finding-help)」:New Relic のオンライン ヘルプ センターで利用できるリソース


<!--HONumber=42-->
