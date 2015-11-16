<properties 
	pageTitle="Application Insights での作業" 
	description="Application Insights での FAQ。" 
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
	ms.date="10/11/2015" 
	ms.author="awills"/>

# Application Insights での作業

## 電子メールの受信

### サイトがダウンした場合の電子メール

[可用性 Web テスト](app-insights-monitor-web-app-availability.md)を設定します。

### サイトが過負荷になっている場合の電子メール

**サーバー応答時間**の[アラート](app-insights-alerts.md)を設定します。1 ～ 2 秒の間のしきい値で機能する必要があります。

![](./media/app-insights-how-do-i/030-server.png)

アプリがエラー コードを返すことによって負荷の兆候を示す場合もあります。**[失敗した要求]** でアラートを設定します。

**[サーバーの例外]** でアラートを設定する場合は、データを表示するために[追加セットアップ](app-insights-asp-net-exceptions.md)が必要になる場合があります。

### 例外での電子メール

1. [例外の監視を設定します](app-insights-asp-net-exceptions.md)
2. 例外数メトリックに[アラートを設定](app-insights-alert.md)します


### アプリのイベントでの電子メール

特定のイベントが発生したときに電子メールを受け取りたいものとします。Application Insights は直接この機能を提供しませんが、[メトリックがしきい値を超えたときにアラートを送信](app-insights-alerts.md)できます。

アラートは[カスタム メトリック](app-insights-api-custom-events-metrics.md#track-metric)には設定できますが、カスタム イベントには設定できません。イベントが発生したときにメトリックを増やすコードを記述します。

    telemetry.TrackMetric("Alarm", 10);

または:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

アラートには 2 つの状態があるため、アラートを終了するときは低い値を送信する必要があります。

    telemetry.TrackMetric("Alarm", 0.5);

アラームを表示するには[メトリック エクスプローラー](app-insights-metric-explorer.md)でグラフを作成します。

![](./media/app-insights-how-do-i/010-alarm.png)

メトリックが短時間中間値を超えたら発生するようにアラートを設定します。


![](./media/app-insights-how-do-i/020-threshold.png)

平均計算期間を最小に設定します。

メトリックがしきい値を上回ったときと下回ったときの両方で、電子メールを受け取ります。

考慮すべき点:

* アラートには、"警告" と "正常" の 2 つの状態があります。状態はメトリックを受信した場合にのみ評価されます。
* 電子メールは状態が変化したときにのみ送信されます。これは、高い値と低い値の両方のメトリックを送信する必要がある理由です。 
* アラートを評価するため、前の期間に受け取った値の平均が計算されます。これはメトリックを受信するたびに行われるので、設定した期間より頻繁に電子メールが送信される可能性があります。
* 電子メールは "警告" と "正常" の両方で送信されるので、1 回限りのイベントを 2 つの状態として考え直すことができます。たとえば、"ジョブ完了" イベントの代わりに、"ジョブ進行中" という状態を考え、その場合はジョブの開始時と終了時に電子メールを受け取ります。

### アラートの自動設定

[Use PowerShell to create new alerts (PowerShell を使用した新しいアラートの作成)](app-insights-alerts/#set-alerts-by-using-powershell)

## PowerShell を使用した Application Insights の管理

* [新しいリソースの作成に関するページ](app-insights-powershell-script-create-resource.md)
* [新しいアラートの作成に関するページ](app-insights-alerts/#set-alerts-by-using-powershell)

## アプリケーションのバージョンとスタンプ

### 開発、テスト、および運用環境の結果を区分する

* さまざまな環境に、個別の iKey を設定します。
* さまざまなスタンプ (開発、テスト、運用) のタグに、個別のプロパティ値を持つテレメトリを設定します。

[詳細情報](app-insights-separate-resources.md)
 

### ビルド番号でのフィルター処理

新しいバージョンのアプリを発行するときは、異なるビルドのテレメトリを区別する必要があります。

アプリケーション バージョン プロパティを設定することで、[検索](app-insights-diagnostic-search.md)および[メトリック エクスプローラー](app-insights-metrics-explorer.md)の結果をフィルター処理できます。


![](./media/app-insights-how-do-i/050-filter.png)

アプリケーション バージョン プロパティを設定するには複数の方法があります。

* 直接設定します。

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`

* その行を[テレメトリ初期化子](app-insights-api-custom-events-metrics.md#telemetry-initializers)にラップして、すべての TelemetryClient インスタンスが一貫して設定されるようにします。

* [ASP.NET] `BuildInfo.config` でバージョンを設定します。Web モジュールは BuildLabel ノードからバージョンを取得します。このファイルをプロジェクトに追加し、ソリューション エクスプローラーで [常にコピーする] プロパティを設定します。

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] MSBuild で自動的に BuildInfo.config を生成します。そのためには、.csproj ファイルに数行を追加します。

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
    ```

    これにより、*プロジェクト名*.BuildInfo.config という名前のファイルが生成されます。これは発行プロセスで BuildInfo.config という名前に変更されます。

    Visual Studio でビルドすると、ビルド ラベルにはプレースホルダー (AutoGen\_...) が含まれます。一方、MSBuild でビルドすると、適切なバージョン番号が設定されます。

    MSBuild がバージョン番号を生成できるようにするには、AssemblyReference.cs で `1.0.*` のようなバージョンを設定します。

## バックエンド サーバーの監視

[基本的な API の使用に関するページ](app-insights-windows-desktop.md)


## データの視覚化

#### 複数のアプリケーションのメトリックを使用したダッシュボード

* [Metric エクスプローラー](app-insights-metrics-explorer.md)でグラフをカスタマイズし、お気に入りとして保存します。Azure ダッシュボードにピン留めします。
* 

#### 他のソースや Application Insights からのデータのあるダッシュボード

* [テレメトリを Power BI にエクスポートします](app-insights-export-power-bi.md)。 

または

* SharePoint をダッシュボードとして使用して、SharePoint Web パーツにデータを表示します。[連続エクスポートと Stream Analytics を使用して SQL にエクスポートします](app-insights-code-sample-export-sql-stream-analytics.md)。PowerView を使用してデータベースを確認し、PowerView の SharePoint Web パーツを作成します。


### 複雑なフィルター処理、セグメント化、結合

* [連続エクスポートと Stream Analytics を使用して SQL にエクスポートします](app-insights-code-sample-export-sql-stream-analytics.md)。PowerView を使用してデータベースを確認します。

<a name="search-specific-users"></a>
### 匿名ユーザーまたは認証済みユーザーのフィルター処理

ユーザーがサインインしたら、[認証されたユーザー ID](app-insights-api-custom-events-metrics.md#authenticated-users) を設定できます。(自動的には設定されません。)

次に以下のことを行えます。

* 特定のユーザー ID で検索

![](./media/app-insights-how-do-i/110-search.png)

* 匿名ユーザーまたは認証済みユーザーに対するメトリックのフィルター処理

![](./media/app-insights-how-do-i/115-metrics.png)

## プロパティ名または値を変更する

フィルター (app-insights-api-filtering-sampling.md#filtering) を作成します。これにより、アプリから Application Insights にテレメトリが送信される前に、テレメトリの変更またはフィルター処理ができるようになります。

## 特定のユーザーとその使用状況を一覧表示する

[特定のユーザーのみを検索](#search-specific-users)するために、[認証されたユーザー ID](app-insights-api-custom-events-metrics/#authenticated-users) を設定できます。

ユーザーが表示するページやログインの頻度についてユーザーを一覧表示するには、2 つのオプションがあります。

* [認証されたユーザー ID を設定](app-insights-api-custom-events-metrics/#authenticated-users)し、[データベースにエクスポート](app-insights-code-sample-export-sql-stream-analytics.md)し、適切なツールを使用してユーザー データを分析します。
* ユーザー数が少ない場合は、メトリック値またはイベント名として関心のあるデータを使用し、ユーザー ID をプロパティとして設定して、カスタム イベントまたはメトリックを送信します。ページ ビューを分析するには、標準の JavaScript trackPageView 呼び出しを置き換えます。サーバー側のテレメトリを分析するには、テレメトリ初期化子を使用して、ユーザー ID をすべてのサーバー テレメトリに追加します。次に、ユーザー ID を基に、メトリックや検索結果をフィルター処理および細分化します。


## アプリから Application Insights へのトラフィックの削減

* [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) で不要なモジュール (パフォーマンス カウンター コレクターなど) を無効にします。
* SDK で、[サンプリングとフィルター処理](app-insights-api-filtering-sampling.md)を使用します。
* [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) を使用する場合は、結果を送信する前にメトリック値のバッチの集計を計算します。これに対して提供される TrackMetric() のオーバーロードを考慮します。


詳細については、[価格とクォータ](app-insights-pricing.md)に関するページを参照してください。

## 遠隔測定を無効にする

サーバーからテレメトリの収集と送信を**動的に停止および開始**するには:

```

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```



パフォーマンス カウンター、HTTP 要求、依存関係などの**選択されている標準のコレクターを無効にする**には、[ApplicationInsights.config](app-insights-api-custom-events-metrics.md) 内の該当する行を削除するか、コメント アウトします。たとえば、独自の TrackRequest データを送信する場合にこれを行います。



## システム パフォーマンス カウンターの表示

メトリックス エクスプローラーに表示できるメトリックには、一連のシステム パフォーマンス カウンターがあります。事前定義された **[サーバー]** というブレードに、それらのいくつかが表示されます。

![Application Insights リソースを開いて、[サーバー] をクリック](./media/app-insights-how-do-i/121-servers.png)

### パフォーマンス カウンターのデータが表示されない場合

* 自身のコンピューターまたは VM の**IIS サーバー**の場合。[Status Monitor をインストール](app-insights-monitor-performance-live-website-now.md)します。 
* **Azure Web サイト**の場合。まだパフォーマンス カウンターに対応していません。Azure Web サイトのコントロール パネルの標準パーツとして取得できるメトリックがいくつか用意されています。
* **UNIX サーバー**の場合。[collectd をインストールします](app-insights-java-collectd.md)。

### 表示するパフォーマンス カウンターの数を増やすには

* 最初に、[新しいグラフを追加](app-insights-metrics-explorer.md)し、提供されている基本的なセットにカウンターが含まれているかどうかを確認します。
* 含まれていない場合は、[パフォーマンス カウンター モジュールによって収集されたセットにカウンターを追加](app-insights-web-monitor-performance.md#system-performance-counters)します。

<!---HONumber=Nov15_HO2-->