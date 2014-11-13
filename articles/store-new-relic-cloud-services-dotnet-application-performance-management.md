<properties urlDisplayName="New Relic" pageTitle="Azure での New Relic の使用 - Azure の機能ガイド" metaKeywords="" description="New Relic サービスを使用して Azure アプリケーションを管理および監視する方法について説明します。" metaCanonical="" services="" documentationCenter=".NET" title="Azure の New Relic によるアプリケーション パフォーマンス管理" authors="" solutions="" manager="dwrede" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="new="" relic" />

# Azure の New Relic によるアプリケーション パフォーマンス管理

このガイドでは、Azure でホストされるアプリケーションに New Relic による
世界クラスのパフォーマンスの監視を追加する方法について説明します。アプリケーションに New Relic を
すぐに追加できる簡単なプロセスについて説明し、New Relic の機能を
いくつか紹介します。New Relic の使い方の詳細については、「[New Relic の使用][New Relic の使用]」を参照してください。

## New Relic とは

New Relic は、実稼働アプリケーションを監視してそのパフォーマンスと信頼性を
詳しく把握できるようにする開発者向けのツールです。パフォーマンス上の
問題を特定して診断する時間を短縮できるように設計されており、
その問題の解決に必要な情報をすぐに確認できます。

New Relic は、Web トランザクションの読み込み時間とスループットを、
サーバーとユーザーのブラウザーの両方から追跡します。データベースでの処理にかかった時間の表示、
時間がかかったクエリと Web 要求の分析、アップタイムの監視と通知、アプリケーションの
例外の追跡など、多数の処理を実行します。

## Azure ストアの New Relic 特別料金

New Relic Standard は、Azure ユーザーで
あれば無料でご利用いただけます。New Relic Pro は、Azure クラウド サービスのインスタンス サイズに基づいて提供されます。

料金情報については、[Windows Azure ストアの New Relic のページ][Windows Azure ストアの New Relic のページ]を参照してください。

<div class="dev-callout"> 
<strong>注: </strong>
<p>コンピューティング インスタンス 10 個までについてのみ、料金を表示しています。10 を超える場合は、ボリューム ディスカウント料金について New Relic (sales@newrelic.com) までお問い合わせください。</p>
</div>

Azure ユーザーは、New Relic エージェントのデプロイ時に、New Relic Pro の 2 週間の試用サブスクリプションをご利用になれます。

## Azure ストアを使用して New Relic にサインアップする

New Relic は、Azure の Web ロールおよびワーカー ロールとシームレスに統合されます。

Azure ストアから New Relic に直接サインアップするには、次の簡単な 3 つの手順を実行します。

### 手順 1. Azure ストアからサインアップする

1.  [Azure 管理ポータル][Azure 管理ポータル]にログインします。
2.  管理ポータルの下のウィンドウの **[新規]** をクリックします。
3.  **[ストア]** をクリックします。
4.  **[アドインの選択]** ダイアログで、**[New Relic]** をクリックし、**[次へ]** をクリックします。
5.  **[個人用サービス]** ダイアログで、希望の New Relic プランを選択します。
6.  該当する場合は、プロモーション コードを入力します。
7.  Azure の設定における New Relic サービスの表示名を入力するか、
    既定値 **NewRelic** を使用します。この名前は、Azure ストアの
    サブスクライブ項目の一覧で一意になっている必要があります。
8.  リージョンの値を選択します (たとえば **[米国西部]**)。
9.  **[次へ]** をクリックします。
10. **[購入の確認]** ダイアログで、プランと料金情報および法律条項を
    確認します。条項に同意したら、**[購入]** をクリックします。
11. **[購入]** をクリックすると、New Relic アカウントの作成プロセスが開始されます。その状態は、Azure 管理ポータルで監視できます。
12. New Relic のライセンス キーを取得するには、**[出力値]** をクリックします。
13. 表示されたライセンス キーをコピーします。このキーは、New Relic NuGet パッケージのインストール時に入力する必要があります。

### 手順 2. NuGet パッケージをインストールする

1.  Visual Studio ソリューションを開くか、または
    **[ファイル]、[新規作成]、[プロジェクト]** の順にクリックして新しいソリューションを作成します。

    ![Visual Studio][Visual Studio]

2.  ソリューションに Azure クラウド サービス プロジェクトがまだ
    含まれていない場合は、ソリューション エクスプローラーでアプリケーションを
    右クリックし、**[Azure クラウド サービス プロジェクトの追加]** をクリックして、プロジェクトを追加します。

    ![クラウド サービスを作成する][クラウド サービスを作成する]

3.  **[ツール]、[ライブラリ パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に
    クリックし、パッケージ マネージャー コンソールを開きます。プロジェクトをパッケージ マネージャー
    コンソール ウィンドウの一番上に表示される既定のプロジェクトに設定します。

    ![パッケージ マネージャー コンソール][パッケージ マネージャー コンソール]

4.  パッケージ マネージャー コマンド プロンプトで「`Install-Package    NewRelicWindowsAzure`」と入力し、**Enter** キーを押します。

    ![パッケージ マネージャーでのインストール][パッケージ マネージャーでのインストール]

5.  ライセンス キーのダイアログで、Azure ストアから受け取ったライセンス キーを入力します。

    ![ライセンス キーの入力][ライセンス キーの入力]

6.  省略可能: アプリケーション名のダイアログで、New Relic のダッシュボードに
    表示されるアプリケーションの名前を入力します。または、既定のソリューション名を使用します。

    ![アプリケーション名の入力][アプリケーション名の入力]

7.  ソリューション エクスプローラーで、Azure クラウド サービス プロジェクトを右クリックし、**[発行]** をクリックします。

    ![クラウド プロジェクトの発行][クラウド プロジェクトの発行]

**注:** 初めてこのアプリケーションを Azure にデプロイする場合は、Azure の
資格情報を入力するように求められます。詳細については、「[Deploying an ASP.NET Web Application to a Azure Web Site (ASP.NET Web アプリケーションを Azure の Web サイトにデプロイする)][Deploying an ASP.NET Web Application to a Azure Web Site (ASP.NET Web アプリケーションを Azure の Web サイトにデプロイする)]」を参照してください。

![発行の設定][発行の設定]

### 手順 3. New Relic でアプリケーションのパフォーマンスをチェックする

New Relic のダッシュボードを表示するには、次の手順を実行します。

1.  Azure ポータルで、**[管理]** をクリックします。
2.  New Relic アカウントの電子メールとパスワードを使用してサインインします。
3.  New Relic のメニュー バーで、**[アプリケーション]、(アプリケーションの名前)** の順にクリックします。

    **[監視] の [概要]** ダッシュボードが自動的に表示されます。

    ![New Relic の監視ダッシュボード][New Relic の監視ダッシュボード]

    **[アプリケーション]** メニューの一覧からアプリを選択すると、**[概要]** ダッシュボードに、現在のアプリ サーバーとブラウザーの情報が表示されます。

### <span id="using-new-relic"></span></a>New Relic の使用

[アプリケーション] メニューの一覧からアプリケーションを選択すると、[概要] ダッシュボードに、現在のアプリ サーバーとブラウザーの情報が表示されます。2 つのビューを切り替えるには、**[アプリ サーバー]** または **[ブラウザー]** をクリックします。

[][]標準の New Relic UI[と][と]ダッシュボードのドリルダウン機能のほかにも、アプリケーションの [概要] ダッシュボードにはさまざまな機能があります。

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
<td align="left">[概要] の<strong>ブラウザー</strong> ビューで、グローバル Apdex マップの任意の場所をポイントします。<br /><strong>ヒント:</strong> 選択したアプリケーションの <a href="https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard">[地理]</a> ダッシュボードに直接移動するには、<strong>グローバル Apdex</strong> のタイトルをクリックするか、グローバル Apdex マップの任意の場所をクリックします。</td>
</tr>
<tr class="even">
<td align="left"><a href="https://newrelic.com/docs/applications-dashboards/web-transactions">[Web トランザクション]</a> ダッシュボードを表示する</td>
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

-   「[Installing the on Azure (Azure への .NET エージェントのインストール)][Installing the on Azure (Azure への .NET エージェントのインストール)]」: New Relic の .NET エージェントのインストール手順
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
  [Visual Studio]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png
  [クラウド サービスを作成する]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png
  [パッケージ マネージャー コンソール]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png
  [パッケージ マネージャーでのインストール]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png
  [ライセンス キーの入力]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png
  [アプリケーション名の入力]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png
  [クラウド プロジェクトの発行]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png
  [Deploying an ASP.NET Web Application to a Azure Web Site (ASP.NET Web アプリケーションを Azure の Web サイトにデプロイする)]: /ja-jp/develop/net/tutorials/get-started/
  [発行の設定]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png
  [New Relic の監視ダッシュボード]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png
  []: https://newrelic.com/docs/site/the-new-relic-ui#functions
  [と]: https://newrelic.com/docs/site/the-new-relic-ui#drilldown
  [Apdex]: https://newrelic.com/docs/site/apdex
  [[地理]]: https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard
  [[Web トランザクション]]: https://newrelic.com/docs/applications-dashboards/web-transactions
  [[エラー]]: https://newrelic.com/docs/site/errors
  [1]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png
  [Installing the on Azure (Azure への .NET エージェントのインストール)]: https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure
  [The New Relic User Interface (New Relic ユーザー インターフェイス)]: https://newrelic.com/docs/site/the-new-relic-ui
  [Applications Overview (アプリケーションの概要)」]: https://newrelic.com/docs/site/applications-overview
  [Real User Monitoring]: https://newrelic.com/docs/features/real-user-monitoring
  [Finding Help (ヘルプの利用)]: https://newrelic.com/docs/site/finding-help
