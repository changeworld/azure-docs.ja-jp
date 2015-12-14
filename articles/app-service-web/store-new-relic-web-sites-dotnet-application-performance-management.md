<properties 
	pageTitle="Azure App Service の .NET Web アプリに対する New Relic によるパフォーマンス管理" 
	description="New Relic のパフォーマンス監視を、Azure App Service で実行する ASP.NET アプリケーションに対して使用する方法について説明します。" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/30/2015" 
	ms.author="stepsic"/>



# Azure App Service の .NET Web アプリに対する New Relic によるパフォーマンス管理

このガイドでは、[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) の Web アプリに、New Relic による世界クラスのパフォーマンス監視を追加する方法について説明します。アプリケーションに New Relic をすぐに追加できる簡単なプロセスについて説明し、New Relic の機能をいくつか紹介します。New Relic の使い方の詳細については、「[New Relic の使用](#using-new-relic)」を参照してください。

## New Relic とは

New Relic は、実稼働アプリケーションを監視してそのパフォーマンスと信頼性を詳しく把握できるようにする開発者向けのツールです。パフォーマンス上の問題を特定して診断する時間を短縮できるように設計されており、その問題の解決に必要な情報をすぐに確認できます。

New Relic は、Web トランザクションの読み込み時間とスループットを、サーバーとユーザーのブラウザーの両方から追跡します。データベースでの処理にかかった時間の表示、時間がかかったクエリと Web 要求の分析、アップタイムの監視と通知、アプリケーションの例外の追跡など、多数の処理を実行します。

## Azure ストアの New Relic 特別料金

New Relic Standard は、Azure ユーザーであれば無料でご利用いただけます。New Relic Pro は、使用している Web サイト モード (および占有モードを使用している場合はインスタンス サイズ) に基づいて複数のパッケージで提供されます。

料金情報については、[Windows Azure Marketplace の New Relic のページ](/marketplace/partners/newrelic/newrelic)を参照してください。

> [AZURE.NOTE]コンピューティング インスタンス 10 個までについてのみ、料金を表示しています。10 個を超える場合は、ボリューム ディスカウント料金について New Relic (sales@newrelic.com) までお問い合わせください。

Azure ユーザーは、New Relic エージェントのデプロイ時に、New Relic Pro の 2 週間の試用サブスクリプションをご利用になれます。

Azure Marketplace を使用して New Relic にサインアップする
--
New Relic は、Azure の Web ロール、Worker ロール、および Azure App Service とシームレスに統合されます。

Azure Marketplace から New Relic に直接サインアップするには、次の簡単な 4 つの手順を実行します。

## 手順 1.New Relic アカウントを作成する

1. [Azure ポータル](https://portal.azure.com)にログインし、隅にある **[新規]** をクリックします。
3. **[開発者向けサービス]** > **[New Relic APM]** の順にクリックします。
4. 次を指定して New Relic アカウントを構成し、**[作成]** をクリックします。
	- **名前**
	- **料金レベル**
	- **リソース グループ**
	- **サブスクリプション**
	- **場所**
	- **法律条項**

11. **[作成]** をクリックすると、New Relic アカウントの作成プロセスが開始されます。**[通知]** ボタンをクリックすると、ステータスを監視できます。作成されたら、New Relic アカウントのブレードが表示されます。

12. New Relic ライセンス キーを取得するには、ブレードの上部にある **[Essentials]** パネルを参照します。Web アプリケーション インスタンスは、このライセンス キーを、Web アプリケーションと New Relic アカウントを統合する際に、アプリケーション設定に自動的に登録します。

## 手順 2. Web アプリ用に New Relic の統合を構成する

1. [Azure ポータル](https://portal.azure.com)の Web アプリのブレードを開きます。
2. ブレードの上部にある [...] メニューで、**[タイルの追加]** をクリックします。
3. **[監視]** タブで **[アプリケーションの概要]** を選択し、Web アプリのブレード上のタイルを表示する場所にドラックします。
4. [完了] をクリックして、タイルの追加を完了します。
5. **[アプリケーションの監視]** タイルをクリックし、**[New Relic]** を選択します。
6. 前の手順で作成したアカウントを選択し、**[OK]** をクリックします。 

	![](./media/store-new-relic-web-sites-dotnet-application-performance-management/configure-new-relic-integration.png)

	保存操作が完了したら、Web アプリのブレードで **[すべての設定]** をクリックし、次に **[アプリケーションの設定]** をクリックします。New Relic をサポートする **NEWRELIC\_LICENSEKEY** の設定が、ブレードの **[アプリ設定]** セクションに追加されて表示されます。

	>[AZURE.NOTE]新しいアプリ設定が有効になるまで、最大 30 秒かかる場合があります。設定をすぐに強制適用するには、Web アプリを再起動します。

## 手順 3. ASP.NET Web アプリを発行する

Visual Studio を使用して Web アプリを発行します。Web アプリが既に発行済みの場合は、もう一度発行します。これにより Web アプリのインスタンスに、New Relic の監視を有効にするために必要な New Relic NuGet パッケージが追加されます。

## 手順 4.New Relic でアプリケーションのパフォーマンスをチェックする

New Relic のダッシュボードを表示するには、次の手順を実行します。

2. [Azure ポータル](https://portal.azure.com)の Web アプリのブレードを開きます。
3. **[アプリケーション監視]** > **[アプリケーション名]** > **[New Relic で表示]** をクリックします。

	![](./media/store-new-relic-web-sites-dotnet-application-performance-management/view-new-relic-data.png)

3. アカウントの初回の使用時には、アカウント情報を構成します。
3. New Relic のメニュー バーで、**[アプリケーション]、(アプリケーションの名前)** の順にクリックします。

	**[監視] の [概要]** ダッシュボードが自動的に表示されます。

	![New Relic の監視ダッシュボード](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app.png)

	**[アプリケーション]** メニューの一覧からアプリを選択すると、**[概要]** ダッシュボードに、現在のアプリ サーバーとブラウザーの情報が表示されます。

### <a id="using-new-relic"></a>New Relic の使用

[アプリケーション] メニューの一覧からアプリケーションを選択すると、[概要] ダッシュボードに、現在のアプリ サーバーとブラウザーの情報が表示されます。2 つのビューを切り替えるには、**[アプリ サーバー]** または **[ブラウザー]** をクリックします。

<a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">標準の New Relic UI</a> と<a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">ダッシュボードのドリルダウン</a>機能のほかにも、アプリケーションの [概要] ダッシュボードにはさまざまな機能があります。

<table border="1">
  <thead>
    <tr>
      <th><b>目的の操作</b></th>
      <th><b>実行する手順</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>選択したアプリケーションのサーバーまたはブラウザーのダッシュボード情報を表示する</td>
       <td><b>[アプリ サーバー]</b> または <b>[ブラウザー]</b> をクリックします。</td>
    </tr>
     <tr>
       <td>アプリケーションの <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> スコアのしきい値レベルを表示する</td>
       <td>[Apdex スコア] の <b>[?]<b> アイコン</b></b>をポイントします。</td>
    </tr>
    <tr>
       <td>世界規模の Apdex の詳細を表示する</td>
       <td>[概要] の<b>ブラウザー</b> ビューで、グローバル Apdex マップの任意の場所をポイントします。<br /><b>ヒント:</b> 選択したアプリケーションの <a href="https://newrelic.com/docs/site/geography" target="_blank">[地理]</a> ダッシュボードに直接移動するには、<b>グローバル Apdex</b> のタイトルをクリックするか、グローバル Apdex マップの任意の場所をクリックします。</td>
    </tr>
    <tr>
       <td><a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard" target="_blank">[Web トランザクション]</a> ダッシュボードを表示する</td>
       <td>アプリケーションの [概要] ダッシュボードの [Web トランザクション] テーブルをクリックします。または、特定の Web トランザクションに関する詳細 (<a href="https://newrelic.com/docs/site/key-transactions" target="_blank">主要トランザクション</a>など) を表示するには、その名前をクリックします。</td>
    </tr>
    <tr>
       <td><a href="https://newrelic.com/docs/site/errors" target="_blank">[エラー]</a> ダッシュボードを表示する</td>
       <td>アプリケーションの [概要] ダッシュボードの [エラー率] グラフのタイトルをクリックします。<br /><b>ヒント:</b> [エラー] ダッシュボードは、<b>[アプリケーション]</b>、(アプリ)、[イベント]、[エラー] の順にクリックして表示することもできます。</td>
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
  </tbody>
</table>

以下に、ブラウザー ビューを選択したときのアプリケーションの [概要] ダッシュボードの例を示します。

![パッケージ マネージャー コンソール](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app_browser.png)

## 次のステップ

詳細については、次に示すその他のリソースを参照してください。

 * [Azure Web Apps (Azure Web アプリ)](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-websites#manual): New Relic の .NET エージェントのインストール手順 
 * [The New Relic User Interface (New Relic ユーザー インターフェイス)](https://newrelic.com/docs/site/the-new-relic-ui): New Relic UI の概要、ユーザー権限とプロファイルの設定、標準機能の使用、およびダッシュボードのドリルダウンの詳細
 * [Applications Overview dashboard (アプリケーションの [概要] ダッシュボード)](https://newrelic.com/docs/site/applications-overview): New Relic のアプリケーションの [概要] ダッシュボードを使用する際の機能
 * [Apdex: Measuring user satisfaction (Apdex: ユーザー満足度の測定)](https://newrelic.com/docs/site/apdex): Apdex によってエンド ユーザーのアプリケーションに対する満足度がどのように測定されるかに関する概要
 * [Real User Monitoring](https://newrelic.com/docs/features/real-user-monitoring): Real User Monitoring (RUM) を使用して、ユーザーのブラウザーで Web ページの読み込みにかかった時間、アクセス元の地域、および使用しているブラウザーに関する詳細を確認する方法の概要
 * [Finding Help (ヘルプの利用)](https://newrelic.com/docs/site/finding-help): New Relic のオンライン ヘルプ センターで利用できるリソース

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページをご覧ください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容
* Websites から App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。


[vswebsite]: web-sites-dotnet-get-started.md

[wmnugetbutton]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
[wmnugetgallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png

[newrelicconf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
[vslicensekey]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
[add-on]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
[custom]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png
 

<!---HONumber=AcomDC_1203_2015-->