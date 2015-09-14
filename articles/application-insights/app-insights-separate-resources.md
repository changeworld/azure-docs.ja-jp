<properties 
	pageTitle="開発、テスト、および実稼働用に Application Insights リソースを分ける"
	description="開発のさまざまな段階で、アプリケーションのパフォーマンスと使用状況を監視します。"
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
	ms.date="09/02/2015"
	ms.author="awills"/>

# 開発、テスト、および実稼働用に Application Insights リソースを分ける


アプリのデバッグ、テスト、実稼働の各バーションのテレメトリの混同を避けるために、各バージョンからデータを受け取るために個別の [Application Insights][start] リソースを作成します。

アプリケーションから受け取ったデータは Microsoft Azure *リソース*に格納され、Application Insights によって処理されます。各リソースは、*インストルメンテーション キー*によって識別されます。 アプリ内で、Application Insights SDK にキーが提供され、これにより、収集したデータを適切なリソースに送信できます。キーは、コード内または ApplicationInsights.config で指定できます。SDK 内のキーを変更することで、データをさまざまなリソースに送信できます。


## Application Insights リソースの作成
  

[portal.azure.com](https://portal.azure.com) で、Application Insights リソースを追加します。

![[新規]、[Application Insights] の順にクリックする](./media/app-insights-create-new-resource/01-new.png)


* **アプリケーションの種類**に応じて、概要ブレードに表示されるものと[メトリック エクスプローラー][metrics]で使用できるプロパティが決まります。自分のアプリの種類が表示されない場合、Web ページの Web の種類のいずれかと、その他のデバイスの電話の種類のいずれかを選択します。
* **リソース グループ**は、[アクセス制御](app-insights-resources-roles-access-control.md)などのプロパティを管理するのに便利です。開発、テスト、および実稼働用に別々のリソース グループを使用できます。
* **サブスクリプション**は、Azure での支払いアカウントです。
* **場所**は、データの保存場所です。現在、これは変更できません。
* **スタート画面への追加**は、Azure ホーム ページ上のリソースに対するクイック アクセス タイルを入れます。 

リソースの作成には数秒かかります。完了したら、アラートが表示されます。

([PowerShell スクリプト](app-insights-powershell-script-create-resource.md)を作成して、リソースを自動で作成できます。)


## インストルメンテーション キーのコピー

インストルメンテーション キーは作成したリソースを識別します。

![Essentials、インストルメンテーション キーの順にクリックし、Ctrl キーを押しながら C キーを押します。](./media/app-insights-create-new-resource/02-props.png)

アプリがデータを送信する宛先のすべてのリソースのインストルメンテーション キーが必要です。


## <a name="dynamic-ikey"></a> 動的なインストルメンテーション キー

通常、SDK が ApplicationInsights.config から iKey を取得します。代わりにコードで設定すると、変更をより簡単に実行できます。

ASP.NET サービスの global.aspx.cs など、初期化メソッドでキーを設定します。

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

この例では、さまざまなリソースの ikeys が Web 構成ファイルのさまざまなバージョンに配置されます。Web 構成ファイルをスワップすると、ターゲット リソースもスワップされます。

#### Web ページ

iKey は、アプリの Web ページや、[クイック スタート ブレードから取得したスクリプト](app-insights-javascript.md)内でも使用されます。スクリプトにそのままコーディングするのではなく、サーバーの状態からこれを生成します。たとえば、ASP.NET アプリで以下の操作を行います。

*Razor の JavaScript*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...





<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-get-started.md

 

<!---HONumber=September15_HO1-->