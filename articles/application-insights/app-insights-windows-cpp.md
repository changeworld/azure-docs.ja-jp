<properties 
	pageTitle="C++ アプリ向けの Application Insights" 
	description="Application Insights を使用して C++ アプリの使用状況とパフォーマンスを分析します。" 
	services="application-insights" 
	documentationCenter="cpp" 
	authors="crystk" 
	manager="jakubo"/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="universal" 
    ms.devlang="na" 
    ms.topic="article" 
	ms.date="06/03/2015" 
    ms.author="crystk"/>

# C++ アプリ向けの Application Insights

Visual Studio Application Insights を使用すると、使用状況、イベント、およびクラッシュに関してモバイル アプリケーションを監視できます。

## 必要条件

必要なものは次のとおりです。

* [Microsoft Azure](http://azure.com) のサブスクリプションWindows、XBox Live、またはその他の Microsoft クラウド サービスで所有している Microsoft アカウントを使用してサインインします。
* Visual Studio 2015 以降。

## Application Insights リソースの作成

[Azure ポータル][portal]で、Application Insights の新しいリソースを作成します。Windows Phone または Windows ストアのオプションを選択します。

![[新規]、[開発者向けサービス]、[Application Insights] の順にクリックする](./media/app-insights-windows-cpp/01-universal.png)

表示されるブレードには、アプリケーションに関するパフォーマンスと使用状況データが表示されます。次に Azure にログインするときにこのブレードに戻るには、スタート画面でそのタイルを見つけてください。あるいは、[参照] ボタンをクリックして探します。

####  インストルメンテーション キーをコピーします。

これはリソースを識別するキーです。データをリソースに送信するために SDK の後の手順でインストールします。

![[プロパティ] をクリックし、キーを選択して、Ctrl キーを押しながら C キーを押す](./media/app-insights-windows-cpp/02-props-asp.png)

## <a name="sdk"></a>アプリケーションに SDK をインストールする


1. Visual Studio で、デスクトップ アプリ プロジェクトの NuGet パッケージを編集します。

    ![プロジェクトを右クリックし、[Nuget パッケージの管理] を選択する](./media/app-insights-windows-cpp/03-nuget.png)

2. C++ Apps 向け Application Insights SDK をインストールします。

    ![**[オンライン]**、**[プレリリースを含める]** の順に選択し、"Application Insights" を検索する](./media/app-insights-windows-cpp/04-ai-nuget.png)

3. リリースおよびデバッグのプロジェクト設定で、次のようにします。
  - $(SolutionDir)packages\ApplicationInsights-CPP.1.0.0-Beta\src\inc をプロジェクト プロパティ -> VC++ ディレクトリ -> Include ディレクトリに追加する
  - $(SolutionDir)packages\ApplicationInsights.1.0.0-Beta\lib\native<PLATFORM TYPE>\release\AppInsights_Win10-UAP をプロジェクト プロパティ -> VC++ ディレクトリ -> Library ディレクトリに追加する

4. ApplicationInsights.winmd を $(SolutionDir)packages\ApplicationInsights.1.0.0-Beta\lib\native<PLATFORM TYPE>\release\ApplicationInsights からのプロジェクトへのリファレンスとして追加する
5. AppInsights_Win10-UAP.dll を $(SolutionDir)packages\ApplicationInsights.1.0.0-Beta\lib\native<PLATFORM TYPE>\release\AppInsights_Win10-UAP から追加する。プロパティに移動し、内容を YES に設定します。これにより、dll がビルド ディレクトリにコピーされます。

## SDK の使用

SDK を初期化し、テレメトリの追跡を開始します。

1. App.xaml.h で、次のようにします。 
  - 追加: `ApplicationInsights::CX::SessionTracking^ m_session;`
2. App.xaml.cpp で、次のようにします。
  - 追加: `using namespace ApplicationInsights::CX;`

  - App:App() で、次のようにします。
	
     `// this will do automatic session tracking and automatic page view collection` `m_session = ref new ApplicationInsights::CX::SessionTracking();`

  - ルート フレームを作成したら (通常、App::OnLaunched の末尾)、m_session を初期化します。
	
    ```
    String^ iKey = L"<YOUR INSTRUMENTATION KEY>";
    m_session->Initialize(this, rootFrame, iKey);
	```

3. アプリケーションのどこかで追跡を使用するには、テレメトリ クライアントのインスタンスを宣言します。


```

    using namespace ApplicationInsights::CX;
    TelemetryClient^ tc = ref new TelemetryClient(L"<YOUR INSTRUMENTATION KEY>");
	tc->TrackTrace(L"This is my first trace");
    tc->TrackEvent(L"I'M ON PAGE 1");
    tc->TrackMetric(L"Test Metric", 5.03);
```


## <a name="run"></a> プロジェクトの実行

アプリケーションを実行し、テレメトリを生成します。開発用コンピューターでデバッグ モードで実行するか、発行してユーザーに実行させることができます。

## Application Insights でデータを表示する

http://portal.azure.com に戻り、Application Insights リソースを参照します。

[検索] をクリックして [[診断検索]][diagnostic] を開きます。ここには、最初のイベントが表示されます。何も表示されない場合は 1 ～ 2 分待機し、[更新] をクリックします。

![[診断検索] をクリックする](./media/app-insights-windows-cpp/21-search.png)

アプリケーションを使用すると、データが [概要] ブレードに表示されます。

![[概要] ブレード](./media/app-insights-windows-cpp/22-oview.png)

詳細情報を表示するグラフをクリックします。たとえば、クラッシュの場合は、次のようになります。

![クラッシュのグラフをクリックする](./media/app-insights-windows-cpp/23-crashes.png)


## <a name="usage"></a>次のステップ

[アプリの使用状況の追跡][track]

[API を使用してカスタム イベントおよびメトリックを送信する][api]

[診断検索][diagnostic]

[メトリックス エクスプローラー][metrics]

[トラブルシューティング][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md

 

<!---HONumber=62-->