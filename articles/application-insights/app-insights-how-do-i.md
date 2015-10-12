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
	ms.date="09/23/2015" 
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
 
## アプリケーション バージョン フィルター

新しいバージョンのアプリを発行するときは、異なるバージョンのテレメトリを区別する必要があります。

アプリケーション バージョン プロパティを設定することで、[検索](app-insights-diagnostic-search.md)および[メトリック エクスプローラー](app-insights-metrics-explorer.md)の結果をフィルター処理できます。


![](./media/app-insights-how-do-i/050-filter.png)

アプリケーション バージョン プロパティを設定するには複数の方法があります。

* 直接設定します。

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`

* その行を[テレメトリ初期化子](app-insights-api-custom-events-metrics.md#telemetry-initializers)にラップして、すべての TelemetryClient インスタンスが一貫して設定されるようにします。

* [ASP.NET] `BuildInfo.config`でバージョンを設定します。Web モジュールは BuildLabel ノードからバージョンを取得します。このファイルをプロジェクトに追加し、ソリューション エクスプローラーで [常にコピーする] プロパティを設定します。

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

<!---HONumber=Oct15_HO1-->