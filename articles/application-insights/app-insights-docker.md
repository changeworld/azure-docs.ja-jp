<properties 
	pageTitle="Application Insights で Docker ホストを監視する" 
	description="Docker ホストのパフォーマンス カウンター、イベント、例外を、コンテナー化されたアプリからのテレメトリと共に Application Insights に表示できます。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/20/2015" 
	ms.author="awills"/>
 
# Application Insights で Docker ホストを監視する

[Docker](https://www.docker.com/) ホストからのパフォーマンス カウンターおよび例外レポートを、Application Insights でグラフ化できます。[Application Insights](app-insights-overview.md) アプリをホストのコンテナーにインストールすると、ホストおよび他のイメージの総合的なパフォーマンス カウンターが表示されます。

Docker で、すべての依存関係を備えた軽量コンテナーにアプリを配布します。これらは、Docker エンジンを実行しているすべてのホスト コンピューターで実行します。

Application Insights は、Docker ホストとコンテナーのパフォーマンスとアクティビティを監視できます。

![例](./media/app-insights-docker/00.png)

もちろん、コンテナー内のアプリのソースを持っている場合は、それぞれに [Application Insights SDK をインストール](app-insights-java-live)して、詳細なパフォーマンス ログと使用状況の追跡を取得できます。

## Application Insights リソースの作成

Application Insights リソースは、データを表示して分析する場所です。Microsoft Azure ポータルでホストされます。

1.	使用を開始するには、[Microsoft Azure](https://azure.com) のサブスクリプションが必要です。使用しないサービスには払う必要がない従量課金オプションがあり、Application Insights の Free レベルを使用できます。
2.	[Azure ポータル](https://portal.azure.com)にサインインし、Application Insights の新しいリソースを作成します。アプリケーションの種類として [その他] を選択します(この選択は表示するグラフの初期選択だけに影響し、重要なものではありません)。

    ![例](./media/app-insights-docker/01-new.png)
3.	[参照]、[すべて] を使用していつでも新しい監視リソースを取得できます。ただし、普通は、ホーム ダッシュボードにアプリのタイルを配置して、ポータルにアクセスするたびに表示されるようにします。

    新しいリソースを開きます。
4.	Docker タイルを追加します。**[編集]**、**[タイルを追加]** の順に選択し、ギャラリーから Docker タイルをドラッグします。最初は空のグラフのみ表示されます。

    ![例](./media/app-insights-docker/03.png)

5. **[要点]** ドロップダウンをクリックし、インストルメンテーション キーをコピーします。これを使用して SDK にテレメトリの送信先を指示します。

すぐにまた戻ってテレメトリを見るので、ブラウザー ウィンドウはそのままにします。


## ホストへの Application Insights のインストール
 
テレメトリを表示する場所ができたので、テレメトリを収集して送信するアプリを設定します。1.Docker ホストにサインインします。2.Docker Hub で Microsoft Application Insights のイメージを検索し、ホストにプルします。3.インストルメンテーション キーを設定します。

    docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444

4. イメージを実行します。

## テレメトリの表示

Azure ポータルで Application Insights リソースに戻ります。

すぐに Docker コンテナーから到着するデータが表示されます (特に、Docker エンジンで他のコンテナーが実行している場合)。

グラフをクリックすると、詳細が表示されます。

取得できるビューのいくつかを次に示します。

### ホスト、アクティビティ、イメージ別のパフォーマンス カウンター


![例](./media/app-insights-docker/10.png)

イメージ名をクリックすると詳細が表示されます。ビューをカスタマイズするには、グラフまたはグリッド見出しをクリックするか、[グラフの追加] を使用します。

[メトリックス エクスプローラーの詳細情報](app-insights-metrics-explorer.md)。

## 個々のイベント


![例](./media/app-insights-docker/12.png)

個々のイベントを調べるには、[[検索]](app-insights-diagnostic-search.md) をクリックします。検索およびフィルターを使用して必要なイベントを探します。イベントをクリックすると詳細情報が表示されます。
 
## コンテナー名別の例外
 

![例](./media/app-insights-docker/14.png)



## Q & A

*Docker からは取得できず、Application Insights からは取得できるものは何ですか*

* コンテナーおよびイメージ別のパフォーマンス カウンターの詳細情報です。
* コンテナーとアプリ データが 1 つのダッシュボードに統合されます。
* データベース、Power BI、または他のダッシュボードに[テレメトリをエクスポート](app-insights-export-telemetry.md)してさらに分析できます。

*アプリ自体からテレメトリを取得するにはどうすればよいですか*

* Application Insights SDK をアプリにインストールします。詳細情報: [Java Web アプリ](app-insights-java-get-started.md)、[Windows Web アプリ](app-insights-asp-net.md)。

<!---HONumber=AcomDC_1125_2015-->