<properties 
	pageTitle="Java Web プロジェクトで Application Insights を使う" 
	description="Application Insights を使用して Java Web サイトのパフォーマンスと利用状況を監視します" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/26/2015" 
	ms.author="awills"/>
 
# Java Web プロジェクトで Application Insights を使う

*Application Insights はプレビュー段階です。*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Visual Studio Application Insights をプロジェクトに追加すると、パフォーマンスの問題や例外を検出して診断できます。


![サンプル データ](./media/app-insights-java-get-started/5-results.png)

さらに、アプリケーションの可用性を監視するための [Web テスト][availability]を設定したり、使用パターンを把握するための[コードを Web ページ][track]に挿入したりできます。

必要なものは次のとおりです。

* Oracle JRE 1.6 以降、または Zulu JRE 1.6 以降
* [Microsoft Azure](http://azure.microsoft.com/) のサブスクリプション([無料評価版](http://azure.microsoft.com/pricing/free-trial/)を使って作業を開始できます)。


## 1.Application Insights のインストルメンテーション キーを取得する

1. [Microsoft Azure ポータル](https://portal.azure.com)にログインします。
2. 新しい Application Insights リソースを作成します。

    ![[+] をクリックし、[Application Insights] を選択します](./media/app-insights-java-get-started/01-create.png)
3. アプリケーションの種類を [Java Web アプリケーション] に設定します。

    ![名前を入力し、[Java Web アプリケーション] を選択した後、[作成] をクリックします](./media/app-insights-java-get-started/02-create.png)
4. 新しいリソースのインストルメンテーション キーを見つけます。このキーは、後でコード プロジェクトに貼り付けます。

    ![新しいリソース概要で、[プロパティ] をクリックし、インストルメンテーション キーをコピーします](./media/app-insights-java-get-started/03-key.png)

## 2.Application Insights SDK for Java をプロジェクトに追加する

*プロジェクトに適した方法を選択してください。*

#### Eclipse で動的 Web プロジェクトを作成している場合:

[Application Insights SDK for Java プラグイン][eclipse]を使用します。

#### Maven を使用している場合:

プロジェクトが既に Maven を使用してビルドする設定になっている場合は、pom.xml ファイルに次のコード スニペットをマージします。

次に、バイナリがダウンロードされるように、プロジェクトの依存関係を更新します。

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[0.9,)</version>
      </dependency>
    </dependencies>


* *ビルド エラーまたはチェックサムの検証エラーが発生する場合は、 次のバージョンを試してください: * `<version>0.9.3</version>`

#### Gradle を使用している場合:

プロジェクトが既に Gradle を使用してビルドする設定になっている場合は、build.gradle ファイルに次のコード スニペットをマージします。

次に、バイナリがダウンロードされるように、プロジェクトの依存関係を更新します。

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '0.9.+'
      // or applicationinsights-core for bare API
    }

* *ビルド エラーまたはチェックサムの検証エラーが発生する場合は、 次のバージョンを試してください: * `version:'0.9.3'`

#### それ以外の場合:

SDK を手動で追加する:

1. [Azure Libraries for Java](http://dl.msopentech.com/lib/PackageForWindowsAzureLibrariesForJava.html) をダウンロードします。
2. Zip ファイルから次のバイナリを抽出し、プロジェクトに追加します。
 * applicationinsights-core
 * applicationinsights-web
 * commons-codec
 * commons-io
 * commons-lang
 * commons-logging
 * guava
 * httpclient
 * httpcore
 * jsr305


*`-core` コンポーネントと `-web` コンポーネントの関係について*

`applicationinsights-core` は、自動テレメトリのない最小限の API を提供します。`applicationinsights-web` は、HTTP 要求数と応答時間を追跡するメトリックを提供します。


## 3.Application Insights の xml ファイルを追加する

ApplicationInsights.xml をプロジェクトのリソース フォルダーに追加します。次の XML をファイルにコピーします。

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
* イベントの関連付けは、HTTP 要求コンポーネントに対する追加の操作です。この操作では、サーバーで受信した各要求に識別子を割り当てた後、この識別子をテレメトリのすべての項目に "Operation.Id" プロパティとして追加します。これにより、[診断検索][diagnostic]でフィルターを設定して、テレメトリを各要求に関連付けることができます。

## 4.HTTP フィルターを追加する

最後の構成手順では、HTTP 要求コンポーネントが各 Web 要求をログに記録できるようにします (単に最小限の API が必要な場合はこの手順を行う必要はありません)。

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

#### MVC 3.1 以降を使用している場合

Application Insights パッケージを含めるように次の要素を編集します。

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>

#### Struts 2 を使用している場合

次の項目を Struts 構成ファイルに追加します (通常は、struts.xml または struts-default.xml)。

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />

(既定のスタックにインターセプターが定義されている場合は、単にインターセプターをそのスタックに追加できます)。

## 5.Application Insights でのテレメトリを表示する

アプリケーションを実行します。

Microsoft Azure の Application Insights リソースに戻ります。

HTTP 要求データが概要ブレードに表示されます (表示されない場合は、数秒待ってから [最新の情報に更新] をクリックします)。

![サンプル データ](./media/app-insights-java-get-started/5-results.png)
 

任意のグラフをクリックして、より詳細なメトリックを表示します。

![](./media/app-insights-java-get-started/6-barchart.png)

 

要求のプロパティを表示すると、その要求に関連付けられているテレメトリ イベント (要求や例外など) が表示されます。
 
![](./media/app-insights-java-get-started/7-instance.png)



[メトリックの詳細についてはこちらをご覧ください。][metrics]

#### 洗練されたアドレス名の計算

Application Insights では、MVC アプリケーションの HTTP 要求の形式として、`VERB controller/action` が想定されます。


たとえば、`GET Home/Product/f9anuh81`、`GET Home/Product/2dffwrf5`、`GET Home/Product/sdf96vws` は、`GET Home/Product` にグループ化されます。

これにより、要求数や要求の平均実行時間など、要求の意味のある集計を行うことができます。

## 5.パフォーマンス カウンター

[サーバー] タイルをクリックすると、一連のパフォーマンス カウンターが表示されます。


![](./media/app-insights-java-get-started/11-perf-counters.png)

### パフォーマンス カウンター コレクションのカスタマイズ

パフォーマンス カウンターの標準セットのコレクションを無効にするには、ApplicationInsights.xml ファイルのルート ノードの下に次のスニペットを追加します。

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>

### 追加のパフォーマンス カウンターの収集

収集する追加のパフォーマンス カウンターを指定できます。

#### JMX カウンター (Java 仮想マシンによって公開されます)

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>

*	`displayName` - Application Insights ポータルに表示される名前。
*	`objectName` - JMX オブジェクトの名前。
*	`attribute` - 取得する JMX オブジェクト名の属性
*	`type` (オプション) - JMX オブジェクトの属性の型。
 *	既定値: int、long などの単純型。
 *	`composite`: パフォーマンス カウンター データは、"Attribute.Data" 形式です。
 *	`tabular`: パフォーマンス カウンター データは、テーブル行形式です。



#### Windows (64 ビット) パフォーマンス カウンター 

それぞれの [Windows パフォーマンス カウンター](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx)は、(フィールドがクラスのメンバーであるのと同様に) カテゴリのメンバーです。カテゴリについては、グローバルに設定することも、数字または名前付きインスタンスを設定することもできます。

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>

*	displayName - Application Insights ポータルに表示される名前。
*	categoryName - このパフォーマンス カウンターが関連付けられているパフォーマンス カウンター カテゴリ (パフォーマンス オブジェクト)。
*	counterName - パフォーマンス カウンターの名前。
*	instanceName - パフォーマンス カウンター カテゴリ インスタンスの名前、または空の文字列 ("") (カテゴリにインスタンスが 1 つ含まれている場合)。categoryName が Process であり、アプリが実行されている現在の JVM プロセスからパフォーマンス カウンターを収集する場合は、`"__SELF__"` を指定します。

パフォーマンス カウンターは、[メトリックス エクスプローラー][metrics]でカスタム メトリックとして表示されます。

![](./media/app-insights-java-get-started/12-custom-perfs.png)


## 6.ログ トレースをキャプチャする

Application Insights を使用すると、Log4J、Logback、またはその他のログ フレームワークのログをさまざまな側面から分析できます。ログは、HTTP 要求やその他のテレメトリに関連付けることができます。[詳細についてはこちら][javalogs]。

## 7.独自のテレメトリを送信する

SDK をインストールすると、API を使用して独自のテレメトリを送信できるようになります。

* アプリケーションのユーザーの行動を把握するには、[カスタム イベントおよびメトリックを追跡][track]します。
* 問題の診断に役立つ情報を得るには、[イベントおよびログを検索][diagnostic]します。


さらに、Application Insights のさまざまな機能をアプリケーションに関連付けることができます。

* ビューや基本的なユーザー メトリックを監視するには、[Web クライアント テレメトリを追加][usage]します。
* [Web テストを設定][availability]して、アプリケーションが動作していて応答できることを確認します。


## 疑問がある場合 問題が発生した場合

[Java のトラブルシューティング](app-insights-java-troubleshoot.md)



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md


<!--HONumber=54--> 