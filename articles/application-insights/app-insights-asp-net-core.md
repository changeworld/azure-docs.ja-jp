---
title: Azure Application Insights for ASP.NET Core | Microsoft Docs
description: Web アプリケーションの可用性、パフォーマンス、使用状況を監視します。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: mbullwin
ms.openlocfilehash: 77c48a22f974e027b4e8858d5e38018bbf5bb54f
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights for ASP.NET Core

Azure Application Insights では、Web アプリケーションをコード レベルまで詳細に監視できます。 ユーザーは、Web アプリケーションの可用性、パフォーマンス、利用状況を簡単に監視できます。 アプリケーションのエラーを、ユーザーからの報告を待つことなく、迅速に特定して診断することもできます。

この記事では、Visual Studio でサンプルの ASP.NET Core [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) アプリケーションを作成する方法と、Azure Application Insights で監視を開始する方法について説明します。

## <a name="prerequisites"></a>前提条件

- NET Core 2.0.0 SDK 以降。
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) バージョン 15.3 以降と、ASP.NET および Web 開発のワークロード。

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Visual Studio で ASP.NET Core プロジェクトを作成する

1. **[Visual Studio 2017]** を右クリックし、管理者として起動します。
2. **[ファイル]** > **[新規作成]** > **[プロジェクト]** の順に選択します (Ctrl-Shift-N)。

   ![Visual Studio の [ファイル]、[新規作成]、[プロジェクト] メニューのスクリーンショット](./media/app-insights-asp-net-core/0001-file-new-project.png)

3. **[Visual C#]** を展開し、**[.NET Core]** > **[ASP.NET Core Web アプリケーション]** を選択します。 **[名前]** > **[ソリューション名]** を入力し、**[新しい Git リポジトリの作成]** をオンにします。

   ![Visual Studio の [ファイル]、[新規作成]、[プロジェクト] ウィザードのスクリーンショット](./media/app-insights-asp-net-core/0002-new-project-web-application.png)

4. **[.Net Core]** > **[ASP.NET Core 2.0** **Web アプリケーション]** > **[OK]** を選択します。

    ![Visual Studio の [ファイル]、[新規作成]、[プロジェクト] 選択メニューのスクリーンショット](./media/app-insights-asp-net-core/0003-dot-net-core.png)

## <a name="add-application-insights-telemetry"></a>Application Insights Telemetry の追加

1. **[プロジェクト]** > **[Application Insights Telemetry の追加...]** を選択します(別の方法として、**[接続済みサービス]** > [接続済みサービスの追加] を右クリックすることもできます)。

    ![Visual Studio の [ファイル]、[新規作成]、[プロジェクト] 選択メニューのスクリーンショット](./media/app-insights-asp-net-core/0004-add-application-insights-telemetry.png)

2. **[開始 (無料)]** を選択します。

    ![Visual Studio の [ファイル]、[新規作成]、[プロジェクト] 選択メニューのスクリーンショット](./media/app-insights-asp-net-core/0005-start-free.png)

3. 適切な **[サブスクリプション]** > **[リソース]** を選択し、1 か月あたり 1 GB を超えるコレクションを許可するかどうかを選択して、**[登録]** を選択します。

    ![Visual Studio の [ファイル]、[新規作成]、[プロジェクト] 選択メニューのスクリーンショット](./media/app-insights-asp-net-core/0006-register.png)

## <a name="changes-made-to-your-project"></a>プロジェクトに加えられた変更

Application Insights は非常に低オーバーヘッドです。 Application Insights テレメトリを追加することでプロジェクトに加えられた変更を確認するには:

**[ビュー]** > **[チーム エクスプローラー]** (Ctrl+\, Ctrl+M) > **[プロジェクト]** > **[変更]** を選択します

- 合計 4 つの変更があります。

  ![Application Insights を追加することによって変更されたファイルのスクリーンショット](./media/app-insights-asp-net-core/0007-changes.png)

- 1 つの新規ファイルが作成されています。

   **ConnectedService.json**

  ![Application Insights を追加することによって変更されたファイルのスクリーンショット](./media/app-insights-asp-net-core/0008-connectedservice-json.png)

- 3 つのファイルが変更されています。

  **appsettings.json**

   ![Application Insights を追加することによって変更されたファイルのスクリーンショット](./media/app-insights-asp-net-core/0009-appsettings-json.png)

  **ContosoDotNetCore.csproj**

   ![Application Insights を追加することによって変更されたファイルのスクリーンショット](./media/app-insights-asp-net-core/0010-contoso-netcore-csproj.png)

   **Program.cs**

   ![Application Insights を追加することによって変更されたファイルのスクリーンショット](./media/app-insights-asp-net-core/0011-program-cs.png)

## <a name="synthetic-transactions-with-powershell"></a>PowerShell を使用した代理トランザクション

アプリを起動し、リンクを手動でクリックして、テスト トラフィックを生成できます。 ただし、多くの場合は、PowerShell でシンプルな代理トランザクションを作成する方法が便利です。

1. IIS Express をクリックしてアプリを実行します ![Visual Studio IIS Express アイコンのスクリーンショット](./media/app-insights-asp-net-core/0012-iis-express.png)

2. ブラウザーのアドレス バーから URL をコピーします。 これは、http://localhost:{ランダムなポート番号} という形式になります

   ![ブラウザーの URL アドレス バーのスクリーンショット](./media/app-insights-asp-net-core/0013-copy-url.png)

3. 次の PowerShell ループを実行して、テスト アプリに対する 100 個の代理トランザクションを作成します。 **localhost:** の後のポート番号を、前の手順でコピーした URL と一致するように変更します。

   ```PS
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Application Insights ポータルを開く

前のセクションの PowerShell を実行したら、Application Insights を起動してトランザクションを表示し、データが収集されていることを確認します。 

Visual Studio のメニューで、**[プロジェクト]** > **[Application Insights]** > **[Application Insights ポータルを開く]** を選択します

   ![Application Insights の概要のスクリーンショット](./media/app-insights-asp-net-core/0014-portal-01.png)

> [!NOTE]
> 上記のスクリーンショットの例では、**[ライブ ストリーム]**、**[ページ ビューの読み込み時間]**、および **[失敗した要求]** が現在収集されていません。 次のセクションでは、これらの各設定を追加する方法について説明します。 **[ライブ ストリーム]** と **[ページ ビューの読み込み時間]** を既に収集している場合は、**[失敗した要求]** の手順のみを実行してください。

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>失敗した要求、ライブ ストリーム、およびページ ビューの読み込み時間を収集する

### <a name="failed-requests"></a>失敗した要求

**[失敗した要求]** は技術的には収集されていますが、まだ何も発生していません。 プロセスを高速化するには、既存のプロジェクトにカスタム例外を追加して、実際の例外をシミュレートします。 Visual Studio でアプリがまだ実行されている場合は、続行する前に **[デバッグの停止]** (Shift+F5) を選択します

1. **ソリューション エクスプローラー**で、**[ページ]** > **[About.cshtml]** を展開し、**[About.cshtml.cs]** を開きます。

   ![Visual Studio ソリューション エクスプローラーのスクリーンショット](./media/app-insights-asp-net-core/0015-solution-explorer-about.png)

2. ``Message=`` の下に例外を追加し、ファイルへの変更を保存します。

   ```C#
   throw new Exception("Test Exception");
   ```

   ![例外コードのスクリーンショット](./media/app-insights-asp-net-core/000016-exception.png)

### <a name="live-stream"></a>Live Stream

ASP.NET Core を使用した Application Insights のライブ ストリーム機能にアクセスするには、**Microsoft.ApplicationInsights.AspNetCore 2.2.0** NuGet パッケージに更新します。

Visual Studio から、**[プロジェクト]** > **[NuGet パッケージの管理]** > **[Microsoft.ApplicationInsights.AspNetCore]** > バージョン **[2.2.0]** > **[更新]** を選択します。

  ![NuGet パッケージ マネージャーのスクリーンショット](./media/app-insights-asp-net-core/0017-update-nuget.png)

複数の確認プロンプトが表示されるので、それらを読み、変更内容に同意する場合はそれらを受け入れます。

### <a name="page-view-load-time"></a>ページ ビューの読み込み時間

1. Visual Studio で、**[ソリューション エクスプローラー]** > **[ページ]** に移動します。2 つのファイルを変更する必要があります: **_Layout.cshtml** および **_ViewImports.cshtml**

2. **_ViewImports.cshtml** で、次のコードを追加します。

   ```C#
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```
     ![_ViewImports.cshtml へのコード変更のスクリーンショット](./media/app-insights-asp-net-core/00018-view-imports.png)

3. **Layout.cshtml** で、``</head>`` タグの前 (他のどのスクリプトよりも前) に下記の行を追加します。

    ```C#
    @Html.Raw(snippet.FullScript)
    ```
    ![layout.cshtml へのコード変更のスクリーンショット](./media/app-insights-asp-net-core/0018-layout-cshtml.png)

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>失敗した要求、ページ ビューの読み込み時間、ライブ ストリームをテストする

前の手順を完了したら、テストを実行し、すべてが動作していることを確認できます。

1. IIS Express をクリックしてアプリを実行します ![Visual Studio IIS Express アイコンのスクリーンショット](./media/app-insights-asp-net-core/0012-iis-express.png)

2. **[バージョン情報]** ページ移動して、テスト例外をトリガーします (デバッグ モードで実行している場合は、例外が Application Insights によってピックアップされる前に、Visual Studio で **[続行]**をクリックする必要があります)。

3. 前述の手順でシミュレートされた PowerShell トランザクション スクリプトを再実行します (場合によっては、スクリプト内のポート番号を調整する必要があります)。

4. Applications Insights の概要がもう開いていない場合は、Visual Studio のメニューから **[プロジェクト]** > **[Application Insights]** > **[Application Insights ポータルを開く]** を選択します。 

   > [!TIP]
   > 新しいトラフィックまだ表示されていない場合は、**[時間の範囲]** をオンにし、**[更新]** をクリックします。

   ![[概要] ウィンドウのスクリーンショット](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. [ライブ ストリーム] を選択します

   ![ライブ メトリック ストリームのスクリーンショット](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (PowerShell スクリプトがまだ実行されている場合は、ライブ メトリックが表示されます。既に停止している場合は、ライブ ストリームが開いた状態でスクリプトを再度実行します。)

## <a name="app-insights-sdk-comparison"></a>App Insights SDK の比較

Application Insights の製品グループでは、[完全な .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) と .Net Core SDK との間での機能の同等性をできるかぎり達成するべく取り組んできました。 [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) for Application Insights の 2.2.0 リリースでは、機能の差が大幅に解消されています。

両者の相違点とトレードオフについて詳しくは、[.NET と .NET Core](https://docs.microsoft.com/en-us/dotnet/standard/choosing-core-framework-server)に関する記事をご覧ください。

   | SDK の比較 | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Live Metrics**      | **+** |**-** | **+** |
   | **サーバー テレメトリ チャネル** | **+** |**-** | **+**|
   |**アダプティブ サンプリング**| **+** | **-** | **+**|
   | **SQL 依存関係呼び出し**     | **+** |**-** | **+**|
   | **パフォーマンス カウンター*** | **+** | **-**| **-**|

このコンテキストでの_パフォーマンス カウンター_は、[サーバー側のパフォーマンス カウンター](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-performance-counters) (プロセッサ、メモリ、ディスク使用率など) を参照しています。

## <a name="open-source-sdk"></a>オープンソース SDK
[コードを読んで協力してください。](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>次の手順
* [ユーザー フローの探索](app-insights-usage-flows.md): ユーザーがアプリ内をどのように移動しているかを把握します。
* [スナップショット コレクションを構成](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications)して、例外がスローされたときのソース コードと変数の状態を確認します。
* [API の使用](app-insights-api-custom-events-metrics.md) : アプリのパフォーマンスと使用の詳細を表示するための独自のイベントとメトリックスを送信します。
* [可用性テスト](app-insights-monitor-web-app-availability.md) : 世界中からアプリを常にチェックします。