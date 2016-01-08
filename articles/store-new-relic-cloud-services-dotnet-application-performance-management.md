<properties 
	pageTitle="Azure での New Relic の使用 | Microsoft Azure" 
	description="New Relic サービスを使用して Azure アプリケーションを管理および監視する方法について説明します。" 
	services="" 
	documentationCenter=".net" 
	authors="nickfloyd" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="nickfloyd@newrelic.com"/>



# Azure の New Relic によるアプリケーション パフォーマンス管理

このガイドでは、Azure でホストされるアプリケーションに New Relic による世界クラスのパフォーマンスの監視を追加する方法について説明します。アプリケーションに New Relic をすぐに追加できる簡単なプロセスについて説明し、New Relic の機能をいくつか紹介します。New Relic の使い方の詳細については、「[New Relic の使用](#using-new-relic)」を参照してください。

## New Relic とは

New Relic は、実稼働アプリケーションを監視してそのパフォーマンスと信頼性を詳しく把握できるようにする開発者向けのツールです。パフォーマンス上の問題を特定して診断する時間を短縮できるように設計されており、その問題の解決に必要な情報をすぐに確認できます。

New Relic は、Web トランザクションの読み込み時間とスループットを、サーバーとユーザーのブラウザーの両方から追跡します。データベースでの処理にかかった時間の表示、時間がかかったクエリと Web 要求の分析、アップタイムの監視と通知、アプリケーションの例外の追跡など、多数の処理を実行します。

## Azure ストアの New Relic 特別料金


New Relic Standard は、Azure ユーザーであれば無料でご利用いただけます。New Relic Pro は、Azure Cloud Services のインスタンス サイズに基づいて提供されます。

料金情報については、[Microsoft Azure ストアの New Relic のページ](http://azure.microsoft.com/marketplace/partners/newrelic/newrelic)を参照してください。

> [AZURE.NOTE]コンピューティング インスタンス 10 個までについてのみ、料金を表示しています。10 個を超える場合は、ボリューム ディスカウント料金について New Relic (sales@newrelic.com) までお問い合わせください。

Azure ユーザーは、New Relic エージェントのデプロイ時に、New Relic Pro の 2 週間の試用サブスクリプションをご利用になれます。

## Azure ストアを使用して New Relic にサインアップする

New Relic は、Azure の Web ロールおよび worker ロールとシームレスに統合されます。

Azure ストアから New Relic に直接サインアップするには、次の簡単な 3 つの手順を実行します。

### 手順 1.Azure ストアからサインアップする

1. [Microsoft Azure 管理ポータル](https://manage.windowsazure.com)にログインします。
2. 管理ポータルの下のウィンドウの **[新規]** をクリックします。
3. **[ストア]** をクリックします。
4. **[アドインの選択]** ダイアログで、**[New Relic]** をクリックし、**[次へ]** をクリックします。
5. **[個人用サービス]** ダイアログで、希望の New Relic プランを選択します。
6. 該当する場合は、プロモーション コードを入力します。
7. Azure の設定における New Relic サービスの表示名を入力するか、既定値 **NewRelic** を使用します。この名前は、Azure ストアの サブスクライブ項目の一覧で一意になっている必要があります。
8. リージョンの値を選択します (たとえば **[米国西部]**)。
9. **[次へ]** をクリックします。
10. **[購入の確認]** ダイアログで、プランと料金情報および法律条項を確認します。条項に同意したら、**[購入]** をクリックします。
11. **[購入]** をクリックすると、New Relic アカウントの作成プロセスが開始されます。その状態は、Azure 管理ポータルで監視できます。
12. New Relic のライセンス キーを取得するには、**[出力値]** をクリックします。 
13. 表示されたライセンス キーをコピーします。このキーは、New Relic NuGet パッケージのインストール時に入力する必要があります。

### 手順 2.NuGet パッケージをインストールします。

1. Visual Studio ソリューションを開くか、または **[ファイル]、[新規作成]、[プロジェクト]** の順にクリックして新しいソリューションを作成します。

	![Visual Studio](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png)

2. ソリューションに Azure クラウド サービス プロジェクトがまだ含まれていない場合は、ソリューション エクスプローラーでアプリケーションを右クリックし、**[Azure クラウド サービス プロジェクトの追加]** をクリックして、プロジェクトを追加します。

	![クラウド サービスを作成する](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png)

3. **[ツール]、[ライブラリ パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順にクリックし、パッケージ マネージャー コンソールを開きます。プロジェクトをパッケージ マネージャー コンソール ウィンドウの 一番上に表示される既定のプロジェクトに設定します。

	![パッケージ マネージャー コンソール](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png)

4. パッケージ マネージャーのコマンド プロンプトで「`Install-Package
   NewRelicWindowsAzure`」と入力し、**Enter** キーを押します。

	![パッケージ マネージャーでのインストール](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png)

5. ライセンス キーのダイアログで、Azure ストアから受け取ったライセンス キーを入力します。

	![ライセンス キーの入力](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png)

6. 省略可能: アプリケーション名のダイアログで、New Relic のダッシュボードに 表示されるアプリケーションの名前を入力します。または、既定のソリューション名を使用します。

	![アプリケーション名の入力](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png)

7. ソリューション エクスプローラーで、Azure クラウド サービス プロジェクトを右クリックし、**[発行]** をクリックします。

	![クラウド プロジェクトの発行](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png)


**メモ:** 初めてこのアプリケーションを Azure にデプロイする場合は、Azure の資格情報を入力するように求められます。詳細については、「[Deploying an ASP.NET Web Application to an Azure Website (ASP.NET Web アプリケーションの Azure Web サイトへのデプロイ)](app-service-web\web-sites-dotnet-get-started.md)」を参照してください。

![発行の設定](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png)

### 手順 3.New Relic でアプリケーションのパフォーマンスをチェックする

New Relic のダッシュボードを表示するには、次の手順を実行します。

1. Azure ポータルで、**[管理]** をクリックします。
2. New Relic アカウントの電子メールとパスワードを使用してサインインします。
3. New Relic のメニュー バーで、**[アプリケーション]、(アプリケーションの名前)** の順にクリックします。

	**[監視] の [概要]** ダッシュボードが自動的に表示されます。

	![New Relic の監視ダッシュボード](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png)

	**[アプリケーション]** メニューの一覧からアプリを選択すると、**[概要]** ダッシュボードに、現在のアプリ サーバーとブラウザーの情報が表示されます。

### <a id="using-new-relic"></a>New Relic の使用

[アプリケーション] メニューの一覧からアプリケーションを選択すると、[概要] ダッシュボードに、現在のアプリ サーバーとブラウザーの情報が表示されます。2 つのビューを切り替えるには、**[アプリ サーバー]** または **[ブラウザー]** をクリックします。

[](https://newrelic.com/docs/site/the-new-relic-ui#functions")標準の New Relic UI[ と](https://newrelic.com/docs/site/the-new-relic-ui#drilldown)ダッシュボードのドリルダウン機能のほかにも、アプリケーションの [概要] ダッシュボードにはさまざまな機能があります。

| 目的操作 | これを行うには、次の手順を実行します。 |
| ----------------- | ---------- |
| 選択したアプリのサーバーまたはブラウザーのダッシュボード情報を表示する | **[アプリ サーバー]** または **[ブラウザー]** をクリックします。 |
| アプリケーションの [Apdex](https://newrelic.com/docs/site/apdex) スコアのしきい値レベルを表示する | [Apdex スコア] の **[?]** アイコンをポイントします。 |
| 世界規模の Apdex の詳細を表示します。 | [概要] の**ブラウザー** ビューで、グローバル Apdex マップの任意の場所をポイントします。**ヒント:** 選択したアプリケーションの [[地理]](https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard") ダッシュボードに直接移動するには、**グローバル Apdex** のタイトルをクリックするか、グローバル Apdex マップの任意の場所をクリックします。 |
| [Web トランザクション](https://newrelic.com/docs/applications-dashboards/web-transactions) ダッシュボードを表示します。 | アプリケーションの [概要] ダッシュボードの [Web トランザクション] テーブルをクリックします。または、特定の Web トランザクションに関する詳細 ([主要トランザクション](https://newrelic.com/docs/site/key-transactions")など) を表示するには、その名前をクリックします。 |
| [エラー](https://newrelic.com/docs/site/errors) ダッシュボードを表示します。 | アプリケーションの [概要] ダッシュボードの [エラー率] グラフのタイトルをクリックします。**ヒント:** [エラー] ダッシュボードは、**[アプリケーション]**、(アプリ)、[イベント]、[エラー] の順にクリックして表示することもできます。 |


さらに、アプリのサーバーの詳細を表示する場合は、次のいずれかを実行します。

- ホストのテーブル ビューと各ホストのメトリックの詳細内訳を切り替えます。
- 個々のサーバーの名前をクリックします。
- 個々のサーバーの Apdex スコアをポイントします。
- 個々のサーバーの CPU 使用率またはメモリをクリックします。

以下に、ブラウザー ビューを選択したときのアプリケーションの [概要] ダッシュボードの例を示します。

![パッケージ マネージャー コンソール](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png)

## 次のステップ

詳細については、次に示すその他のリソースを参照してください。

 * [Azuree での .NET エージェントのインストール](https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure): New Relic の .NET エージェントのインストール手順 
 * [The New Relic User Interface (New Relic ユーザー インターフェイス)](https://newrelic.com/docs/site/the-new-relic-ui): New Relic UI の概要、ユーザー権限とプロファイルの設定、標準機能の使用、およびダッシュボードのドリルダウンの詳細
 * [Applications Overview dashboard (アプリケーションの [概要] ダッシュボード)](https://newrelic.com/docs/site/applications-overview): New Relic のアプリケーションの [概要] ダッシュボードを使用する際の機能
 * [Apdex: Measuring user satisfaction (Apdex: ユーザー満足度の測定)](https://newrelic.com/docs/site/apdex): Apdex によってエンド ユーザーのアプリケーションに対する満足度がどのように測定されるかに関する概要
 * [Real User Monitoring](https://newrelic.com/docs/features/real-user-monitoring): Real User Monitoring (RUM) を使用して、ユーザーのブラウザーで Web ページの読み込みにかかった時間、アクセス元の地域、および使用しているブラウザーに関する詳細を確認する方法の概要
 * [Finding Help (ヘルプの利用)](https://newrelic.com/docs/site/finding-help): New Relic のオンライン ヘルプ センターで利用できるリソース

<!---HONumber=Nov15_HO1-->