<properties 
	pageTitle="既にライブの Java Web アプリ向けの Application Insights" 
	description="サーバーで既に実行中の Web アプリケーションの監視を開始する" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2015" 
	ms.author="awills"/>
 
# 既にライブの Java Web アプリ向けの Application Insights

*Application Insights はプレビュー段階です。*

J2EE サーバーで既に実行されている Web アプリケーションがある場合、[Appliction Insight](app-insights-overview.md) を使用すると、コードの変更やプロジェクトの再コンパイルなしで監視を開始できます。この方法では、サーバーに送信される HTTP 要求、未処理の例外、パフォーマンス カウンターに関する情報が取得されます。

使用を開始するには、[Microsoft Azure](https://azure.com) のサブスクリプションが必要です。

> [AZURE.NOTE]このページの手順では、実行時の Web アプリに SDK を追加します。これは、ソース コードの更新やリビルドを実行したくない場合に便利です。ただし、できれば、それよりも [SDK をソース コードに追加](app-insights-java-get-started.md)することをお勧めします。追加すると、ユーザーの利用状況を追跡するためのコードの記述などで選択の幅が広がります。

## 1\.Application Insights のインストルメンテーション キーを取得する

1. [Microsoft Azure ポータル](https://portal.azure.com)にログインします。
2. 新しい Application Insights リソースを作成します。

    ![[+] をクリックし、[Application Insights] を選択します](./media/app-insights-java-live/01-create.png)
3. アプリケーションの種類を [Java Web アプリケーション] に設定します。

    ![名前を入力し、[Java Web アプリケーション] を選択した後、[作成] をクリックします](./media/app-insights-java-live/02-create.png)
4. 新しいリソースのインストルメンテーション キーを見つけます。このキーは、後でコード プロジェクトに貼り付けます。

    ![新しいリソース概要で、[プロパティ] をクリックし、インストルメンテーション キーをコピーします](./media/app-insights-java-live/03-key.png)

## 2\.SDK のダウンロード

1. [Application Insights SDK for Java](https://azuredownloads.blob.core.windows.net/applicationinsights/sdk.html) をダウンロードします。 
2. サーバーで、プロジェクトのバイナリの読み込み元のディレクトリに SDK の内容を展開します。Tomcat を使用している場合、この場所は通常 `webapps<your_app_name>\WEB-INF\lib` になります。


## 3\.Application Insights の xml ファイルを追加する

SDK を追加したフォルダーに ApplicationInsights.xml を作成します。そのファイルに次の XML を入力します。

インストルメンテーション キーについては、Azure ポータルで入手したキーを使用してください。

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>


* インストルメンテーション キーは、テレメトリのすべての項目と共に送信されます。インストルメンテーション キーを受け取った Application Insights は、リソース内にこのキーを表示します。
* HTTP 要求コンポーネントはオプションです。このコンポーネントは、要求と応答時間に関するテレメトリをポータルに自動的に送信します。
* イベントの関連付けは、HTTP 要求コンポーネントに対する追加の操作です。この操作では、サーバーで受信した各要求に識別子を割り当てた後、この識別子をテレメトリのすべての項目に "Operation.Id" プロパティとして追加します。これにより、[診断検索](app-insights-diagnostic-search.md)でフィルターを設定して、テレメトリを各要求に関連付けることができます。


## 4\.HTTP フィルターを追加する

プロジェクトの web.xml ファイルを見つけて開きます。アプリケーション フィルターが構成されている web-app ノードの下に次のコード スニペットをマージします。

最も正確な結果を得るためには、他のすべてのフィルターの前にこのフィルターをマップする必要があります。

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

## 5\.Web アプリを再起動する

## 6\.Application Insights でのテレメトリを表示する

[Microsoft Azure ポータル](https://portal.azure.com)の Application Insights リソースに戻ります。

HTTP 要求データが概要ブレードに表示されます (表示されない場合は、数秒待ってから [最新の情報に更新] をクリックします)。

![サンプル データ](./media/app-insights-java-live/5-results.png)
 

任意のグラフをクリックして、より詳細なメトリックを表示します。

![](./media/app-insights-java-live/6-barchart.png)

 

要求のプロパティを表示すると、その要求に関連付けられているテレメトリ イベント (要求や例外など) が表示されます。
 
![](./media/app-insights-java-live/7-instance.png)


[メトリックの詳細についてはこちらをご覧ください。](app-insights-metrics-explorer.md)



## 次のステップ

* [Web ページにテレメトリを追加](app-insights-web-track-usage.md)して、ページ ビューやユーザー メトリックを監視します。
* [Web テストを設定](app-insights-monitor-web-app-availability.md)して、アプリケーションが動作していて応答できることを確認します。
* [ログ トレースをキャプチャする](app-insights-java-trace-logs.md)
* 問題の診断に役立つ情報を得るには、[イベントおよびログを検索](app-insights-diagnostic-search.md)します。


 

<!---HONumber=AcomDC_1203_2015-->