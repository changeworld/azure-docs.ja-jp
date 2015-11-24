<properties
    pageTitle="Android アプリの分析 | Microsoft Azure"
    description="Android アプリの使用状況とパフォーマンスを分析します。"
    services="application-insights"
    documentationCenter="android"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
	ms.date="11/14/2015"
    ms.author="awills"/>

# Android アプリの分析

Visual Studio Application Insights を使用すると、使用状況、イベント、およびクラッシュに関してモバイル アプリケーションを監視できます。

> [AZURE.NOTE]クラッシュ レポートの取得、分析、配布、およびフィードバックの管理には、[HockeyApp](http://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk) をお勧めします。

## 必要条件

必要なものは次のとおりです。

* [Microsoft Azure](http://azure.com) のサブスクリプションWindows、XBox Live、またはその他の Microsoft クラウド サービスで所有している Microsoft アカウントを使用してサインインします。
* Android Studio
* Android SDK バージョン 9 以降。

## Application Insights リソースの作成

[Azure ポータル][portal]で、Application Insights の新しいリソースを作成します。Android のオプションを選択します。

![[新規]、[開発者向けサービス]、[Application Insights] の順にクリックする](./media/app-insights-android/11-new.png)

表示されるブレードには、アプリケーションに関するパフォーマンスと使用状況データが表示されます。次に Azure にログインするときにこのブレードに戻るには、スタート画面でそのタイルを見つけてください。あるいは、[参照] ボタンをクリックして探します。

## セットアップ

[セットアップ ガイド](https://github.com/Microsoft/ApplicationInsights-Android#-3-setup)に従ってください。


## SDK の使用

SDK を初期化し、テレメトリの追跡を開始します。

アプリのルート アクティビティに、次のインポートを追加します。

```java

     import com.microsoft.applicationinsights.library.ApplicationInsights;
```

アクティビティの `onCreate` コールバックに次のコードを追加します。

```java

    ApplicationInsights.setup(this.getApplicationContext(), this.getApplication());
    ApplicationInsights.start();
```

`ApplicationInsights.start()` が呼び出されると、SDK は Android のライフサイクル アクティビティと処理されていない例外の追跡を開始します。

> [AZURE.NOTE]アプリケーション ライフサイクル イベントは、Android SDK version 15 以上 (アイスクリームサンドイッチ以上) のみで収集されます。

さらに、カスタム イベント、トレース、メトリック、および処理された例外を収集できます。テレメトリを送信するには、[Application Insights API][api] のいずれかを使用します。

* その他のユーザーの操作には TrackEvent(eventName)。
* [診断ログの記録][diagnostic]には TrackTrace(logEvent)。
* Catch 句の TrackHandledException(exception)
* 特定のイベントに関連付けられていないメトリックの標準レポートをバックグラウンド タスクで送信するには TrackMetric(name, value)。

次のコードは、初期化と手動のテレメトリ収集の例です。

```java

    public class MyActivity extends Activity {

      @Override
      protected void onCreate(Bundle savedInstanceState) {

        ApplicationInsights.setup(this);
        //... other initialization code ...//
        ApplicationInsights.start();

        // track telemetry data
        TelemetryClient client = TelemetryClient.getInstance();
        HashMap<String, String> properties = new HashMap<String, String>();
        properties.put("property1", "my custom property");
        client.trackEvent("sample event", properties);
        client.trackTrace("sample trace");
        client.trackMetric("sample metric", 3);
        client.trackHandledException(new Exception("sample exception"));
      }
    }
```

## <a name="run"></a> プロジェクトの実行

テレメトリを生成するには、アプリケーションを実行します (Windows では、SHIFT キーを押しながら F10 キーを押し、OS X では CTRL キーを押しながら R キーを押します)。

## Application Insights でのデータの表示

http://portal.azure.com に戻り、Application Insights リソースを参照します。

**[検索]** をクリックして [[診断検索]][diagnostic] を開きます。ここには、最初のイベントが表示されます。何も表示されない場合は 1 ～ 2 分待機し、**[更新]** をクリックします。

![[診断検索] をクリックする](./media/app-insights-android/21-search.png)

アプリケーションを使用すると、データが [概要] ブレードに表示されます。

![[概要] ブレード](./media/app-insights-android/22-oview.png)

詳細情報を表示するグラフをクリックします。たとえば、クラッシュの場合は、次のようになります。

![クラッシュのグラフをクリックする](./media/app-insights-android/23-crashes.png)


## <a name="usage"></a>次のステップ

[アプリの使用状況の追跡][track]

[診断検索][diagnostic]

[メトリックス エクスプローラー][metrics]

[トラブルシューティング][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-api-custom-events-metrics.md

<!---HONumber=Nov15_HO4-->