---
title: .NET アプリ向け Azure Application Insights スナップショット デバッガー | Microsoft Docs
description: 例外が運用 .NET アプリでスローされるときにデバッグ スナップショットが自動的に収集される
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2018
ms.reviewer: pharring
ms.author: mbullwin
ms.openlocfilehash: b180c7e8d26acc86aa1d1982ace92efafa85f9ef
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37115505"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>.NET アプリでの例外でのデバッグ スナップショット

例外が発生したとき、実行中の Web アプリケーションからデバッグ スナップショットを自動的に収集できます。 スナップショットには、例外がスローされたときのソース コードと変数の状態が表示されます。 [Azure Application Insights](app-insights-overview.md) のスナップショット デバッガー (プレビュー) により、Web アプリの例外テレメトリが監視されます。 運用環境の問題の診断に必要な情報を入手できるように、スローされる上位の例外に関するスナップショットが収集されます。 [スナップショット コレクター NuGet パッケージ](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)をアプリケーションに含め、必要に応じて、[ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) にコレクション パラメーターを構成します。スナップショットが、Application Insights ポータルの[例外](app-insights-asp-net-exceptions.md)に表示されます。

ポータルで [Debug Snapshots (デバッグ スナップショット)] を表示して、コール スタックを表示し、各呼び出しスタック フレームで変数を確認できます。 ソース コードによるデバッグ エクスペリエンスをさらに向上させるには、Visual Studio 2017 Enterprise でスナップショットを開きます。このためには、[Visual Studio 向けのスナップショット デバッガー拡張機能をダウンロード](https://aka.ms/snapshotdebugger)します。 Visual Studio では、例外を待たずに[スナップポイントを設定し、対話形式でスナップショットを取得](https://aka.ms/snappoint)できます。

スナップショット コレクションは次のアプリケーションで使用できます。
* .NET Framework 4.5 以降を実行している .NET Framework アプリケーションと ASP.NET アプリケーション。
* Windows 上で動作している .NET core 2.0 アプリケーションと ASP.NET Core 2.0 アプリケーション。

次の環境がサポートされています。
* Azure App Service。
* OS ファミリ 4 以降を実行している Azure クラウド サービス。
* Windows Server 2012 R2 以降を実行している Azure Service Fabric サービス。
* Windows Server 2012 R2 以降を実行している Azure Virtual Machines。
* Windows Server 2012 R2 以降を実行しているオンプレミスの仮想マシンまたは物理マシン。

> [!NOTE]
> クライアント アプリケーション (たとえば、WPF、Windows フォーム、または UWP) はサポートされません。

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>ASP.NET アプリケーションのスナップショット コレクションの構成

1. まだ有効にしていない場合は、[Web アプリで Application Insights を有効](app-insights-asp-net.md)にします。

2. [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet パッケージをアプリに含めます。

3. パッケージが [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) に追加した既定のオプションを確認します。

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>1</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. スナップショットは、Application Insights にレポートされる例外についてのみ収集されます。 場合によっては (たとえば、.NET プラットフォームの古いバージョン)、[例外コレクションを構成](app-insights-asp-net-exceptions.md#exceptions)して、ポータルでスナップショット付きの例外を表示する必要があります。


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>ASP.NET Core 2.0 アプリケーションのスナップショット コレクションの構成

1. まだ有効にしていない場合は、[ASP.NET Core Web アプリで Application Insights を有効](app-insights-asp-net-core.md)にします。

    > [!NOTE]
    > お使いのアプリケーションが、2.1.1 以降のバージョンの Microsoft.ApplicationInsights.AspNetCore パッケージを参照していることを確認します。

2. [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet パッケージをアプリに含めます。

3. アプリケーションの `Startup` クラスを変更し、スナップショット コレクターのテレメトリ プロセッサを追加および構成します。

    次の using ステートメントを `Startup.cs` に追加します。

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   次の `SnapshotCollectorTelemetryProcessorFactory`クラスを `Startup` クラスに追加します。

   ```csharp
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }
       ...
    ```
    `SnapshotCollectorConfiguration` と `SnapshotCollectorTelemetryProcessorFactory` サービスをスタートアップ パイプラインに追加します。

    ```csharp
       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. スナップショット コレクターを構成するには、appsettings.json に SnapshotCollectorConfiguration セクションを追加します。 例: 

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>その他の .NET アプリケーションのスナップショット コレクションの構成

1. アプリケーションがまだ Application Insights を使用してインストルメント化されていない場合、まず[Application Insights を有効にし、インストルメンテーション キーを設定](app-insights-windows-desktop.md)します。

2. [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet パッケージをアプリに追加します。

3. スナップショットは、Application Insights にレポートされる例外についてのみ収集されます。 例外をレポートするようにコードを変更する必要があります。 例外処理コードは、アプリケーションの構造によって異なります。次に例を示します。
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="grant-permissions"></a>アクセス許可を付与する

Azure サブスクリプションの所有者は、スナップショットを検査できます。 他のユーザーは、所有者から権限を付与してもらう必要があります。

権限を付与するには、スナップショットを検査する予定のユーザーに `Application Insights Snapshot Debugger` のロールを割り当てます。 このロールは、Application Insights のターゲット リソース、リソース グループ、またはサブスクリプションに関するサブスクリプション所有者が、個々のユーザーまたはグループに割り当てることができます。

1. Azure Portal で Application Insights のリソースを参照します。
1. **[アクセス制御 (IAM)]** をクリックします。
1. **[+ 追加]** をクリックします。
1. **[ロール]** ボックスの一覧の **[Application Insights スナップショット デバッガー]** を選択します。
1. 追加するユーザーの名前を探して入力します。
1. **[保存]** ボタンをクリックして、ユーザーをロールに追加します。


> [!IMPORTANT]
> スナップショットは、変数とパラメーターの値に個人情報などの機密情報を含んでいる可能性があります。

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Application Insights ポータルのデバッグ スナップショット

特定の例外または問題 ID のスナップショットが使用できるようになると、**[デバッグ スナップショットを開く]** ボタンが Application Insights ポータルの [[例外]](app-insights-asp-net-exceptions.md) に表示されます。

![例外の [Debug Snapshot (デバッグ スナップショット)] ボタンを開く](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

デバッグ スナップショット ビューには、コール スタックと変数ウィンドウが表示されます。 コール スタック ウィンドウでコール スタックのフレームを選択すると、変数ウィンドウでその関数呼び出しのローカル変数とパラメーターを表示できます。

![ポータルでのデバッグ スナップショットの表示](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

スナップショットには機密情報が含まれている可能性があるため、既定では非表示になっています。 スナップショットを表示するには、`Application Insights Snapshot Debugger` のロールを割り当てられている必要があります。

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Visual Studio 2017 Enterprise でのデバッグ スナップショット
1. **[Download Snapshot]\(スナップショットのダウンロード\)** をクリックして `.diagsession` ファイルをダウンロードします。このファイルは Visual Studio 2017 Enterprise で開くことができます。

2. `.diagsession` ファイルを開くには、まず [Visual Studio 用のスナップショット デバッガー拡張機能をダウンロードしてインストール](https://aka.ms/snapshotdebugger)する必要があります。

3. スナップショット ファイルを開くと、Visual Studio の[ミニダンプ デバッグ] ページが表示されます。 **[Debug Managed Code]\(マネージ コードをデバッグする\)** をクリックして、スナップショットのデバッグを開始します。 例外がスローされたコード行がスナップショットに表示され、プロセスの現在の状態をデバッグできます。

    ![Visual Studio でのデバッグ スナップショットの表示](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

ダウンロードしたスナップショットには、Web アプリケーション サーバーで検出されたすべてのシンボル ファイルが含まれます。 これらのシンボル ファイルは、スナップショット データをソース コードと関連付けるために必要です。 App Service アプリの場合は、Web アプリを公開するときにシンボル デプロイを有効にしてください。

## <a name="how-snapshots-work"></a>スナップショットのしくみ

Snapshot Collector は、[Application Insights Telemetry Processor](app-insights-configuration-with-applicationinsights-config.md#telemetry-processors-aspnet) として実装されています。 アプリケーションが実行されると、Snapshot Collector Telemetry Processor がアプリケーションのテレメトリ パイプラインに追加されます。
アプリケーションが [TrackException](app-insights-asp-net-exceptions.md#exceptions) を呼び出すたびに、Snapshot Collector はスローされる例外の種類とスロー方法から問題 ID を計算します。
アプリケーションが TrackException を呼び出すたびに、該当する問題 ID のカウンターが増分されます。 カウンターが `ThresholdForSnapshotting` 値に達すると、問題 ID が収集計画に追加されます。

[AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) イベントにサブスクライブすることで例外がスローされるので、Snapshot Collector は例外も監視します。 そのイベントが発生すると、例外の問題 ID が計算され、収集計画の問題 ID と比較されます。
一致する ID があれば、実行中のプロセスのスナップショットが作成されます。 スナップショットには一意の識別子が割り当てられ、例外にはその識別子を使用してスタンプされます。 FirstChanceException ハンドラーが戻った後、スローされた例外は通常どおり処理されます。 最終的に、例外は TrackException メソッドに再び到達し、スナップショット識別子と共に Application Insights に報告されます。

メイン プロセスは引き続き実行され、ユーザーへのトラフィックが処理されます。中断をほとんど発生しません。 その間、スナップショットは Snapshot Uploader プロセスに渡されます。 Snapshot Uploader からミニダンプが作成され、関連するシンボル (.pdb) ファイルと共に Application Insights にアップロードされます。

> [!TIP]
> - プロセスのスナップショットは、実行中のプロセスの一時停止された複製です。
> - スナップショットの作成には約 10 から 20 ミリ秒かかります。
> - `ThresholdForSnapshotting` の既定値は 1 です。 これは最小値でもあります。 そのため、スナップショットが作成される前に、アプリは同じ例外を  **2 回**トリガーする必要があります。
> - Visual Studio でデバッグ中にスナップショットを生成する場合は、`IsEnabledInDeveloperMode` を true に設定します。
> - スナップショットの作成速度は `SnapshotsPerTenMinutesLimit` 設定によって制限されます。 既定では、10 分ごとに 1 つのスナップショットが上限です。
> - 1 日あたり 50 枚を超えるスナップショットをアップロードすることはできません。

## <a name="current-limitations"></a>現時点での制限事項

### <a name="publish-symbols"></a>シンボルの公開
スナップショット デバッガーでは、Visual Studio で変数をデコードし、デバッグ エクスペリエンスを提供するために、運用サーバーにシンボル ファイルが必要です。 Visual Studio 2017 の 15.2 リリースでは、App Service に公開する際に、既定でリリース ビルドのシンボルを公開します。 以前のバージョンでは、シンボルがリリース モードで公開されるように、発行プロファイルの `.pubxml` ファイルに次の行を追加する必要があります。

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Azure Compute や他の種類の場合、シンボル ファイルがメイン アプリケーション .dll (通常は `wwwroot/bin`) の同じフォルダーにあるか、現在のパスで使用できる必要があります。

### <a name="optimized-builds"></a>最適化されたビルド
場合によっては、JIT コンパイラによって適用される最適化のために、リリース ビルドでローカル変数を表示できないことがあります。
ただし、Azure App Services では、Snapshot Collector は収集計画の一部であるスロー方法を非最適化する可能性があります。

> [!TIP]
> Application Insights サイト拡張機能を App Service にインストールして、非最適化のサポートを得ます。

## <a name="troubleshooting"></a>トラブルシューティング

次のヒントは、スナップショット デバッガーの問題のトラブルシューティングに役立ちます。

### <a name="use-the-snapshot-health-check"></a>スナップショットの正常性チェックを使用する
いくつかの一般的な問題により、[デバッグ スナップショットを開く] が表示されません。 古い Snapshot Collector を使用します (たとえば、1 日のアップロード上限に達した場合)。そうしないと、スナップショットのアップロードに時間がかかることがあります。 一般的な問題のトラブルシューティングには、Snapshot Health Check を使用します。

エンドツーエンドのトレース ビューの例外ウィンドウには、Snapshot Health Check に接続できるリンクが表示されます。

![スナップショットの正常性チェックを入力する](./media/app-insights-snapshot-debugger/enter-snapshot-health-check.png)

インタラクティブでチャットのようなインターフェイスでは、一般的な問題が検索され、問題の解決が案内されます。

![正常性チェック](./media/app-insights-snapshot-debugger/healthcheck.png)

それでも問題が解決しない場合は、以下の手動トラブルシューティング手順を参照してください。

### <a name="verify-the-instrumentation-key"></a>インストルメンテーション キーの確認

公開したアプリケーションで、正しいインストルメンテーション キーを使用していることを確認します。 通常、インストルメンテーション キーは、ApplicationInsights.config ファイルから読み取られます。 値が、ポータルに表示される Application Insights リソースのインストルメンテーション キーと同じであることを確認します。

### <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>最新バージョンの NuGet にアップグレードする

Visual Studio の NuGet Package Manager を使用して、Microsoft.ApplicationInsights.SnapshotCollector の最新バージョンを使用していることを確認します。 リリース ノートについては https://github.com/Microsoft/ApplicationInsights-Home/issues/167 を参照してください。

### <a name="check-the-uploader-logs"></a>アップローダー ログの確認

スナップショットの作成後、ミニダンプ ファイル (.dmp) がディスク上に作成されます。 個別アップローダー プロセスでは、そのミニダンプ ファイルを作成し、これを関連する PDB と共に Application Insights のスナップショット デバッガーのストレージにアップロードします。 ミニダンプは、正常にアップロードされた後、ディスクから削除されます。 アップローダー プロセスのログ ファイルは、ディスク上に保持されます。 App Service 環境では、これらのログは `D:\Home\LogFiles` にあります。 App Service の Kudu 管理サイトを使用すると、これらのログ ファイルを検索できます。

1. Azure Portal で App Service アプリケーションを開きます。
2. **[高度なツール]** をクリックするか、**Kudu** を検索します。
3. **[Go]** をクリックします。
4. **[Debug console]**(デバッグ コンソール) ドロップダウン リスト ボックスで、**[CMD]** を選択します。
5. **[LogFiles]** をクリックします。

`Uploader_` または `SnapshotUploader_`で始まる名前で拡張子が `.log` であるファイルが 1 つ以上表示されます。 適切なアイコンをクリックしてログ ファイルをダウンロードするか、またはブラウザーでログ ファイルを開きます。
ファイル名には、App Service インスタンスを識別する一意のサフィックスが含まれます。 App Service のインスタンスが 1 つ以上のコンピューターでホストされている場合は、コンピューターごとに個別のログ ファイルがあります。 アップローダーは、新しいミニダンプ ファイルを検出すると、ログ ファイルに記録します。 スナップショットとアップロードの成功の例を次に示します。

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> 上記の例は、Microsoft.ApplicationInsights.SnapshotCollector NuGet パッケージのバージョン 1.2.0 です。 以前のバージョンのアップローダー プロセスは `MinidumpUploader.exe` で、ログはこれほど詳しくありません。

インストルメンテーション キーは、前の例では、`c12a605e73c44346a984e00000000000` です。 この値は、アプリケーションのインストルメンテーション キーと一致する必要があります。
ミニダンプは ID `139e411a23934dc0b9ea08a626db16c5` を持つスナップショットに関連付けられています。 この ID は、Application Insights Analytics で関連する例外テレメトリを検索するために後で使用できます。

アップローダーは、約 15 分ごとに 1 回新しい PDB をスキャンします。 次に例を示します。

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

App Service にホストされて_いない_アプリケーションでは、アップローダー ログは、ミニダンプと同じフォルダー `%TEMP%\Dumps\<ikey>` にあります (`<ikey>` はインストルメンテーション キー)。

### <a name="troubleshooting-cloud-services"></a>クラウド サービスのトラブルシューティング
クラウド サービスのロールでは、既定の一時フォルダーが minidump ファイルを保持するためには小さすぎる場合があり、スナップショットが失われる可能性があります。
必要な領域は、アプリケーションの合計ワーキング セットと同時実行スナップショット数によって異なります。
32 ビット ASP.NET web ロールのワーキング セットは、通常は 200 MB から 500 MB です。
少なくとも 2 つの同時実行スナップショットを許可します。
たとえば、アプリケーションが 1 GB の合計ワーキング セットを使用する場合は、スナップショットを格納するために少なくとも 2 GB のディスク領域があることを確認する必要があります。
次の手順に従って、スナップショット専用のローカル リソースを持つクラウド サービス ロールを構成します。

1. クラウド サービス定義 (.csdef) ファイルを編集して、新しいローカル リソースをクラウド サービスに追加します。 次の例では、サイズが 5 GB の `SnapshotStore` というリソースを定義します。
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. ロールの起動コードを変更して、`SnapshotStore` ローカル リソースを指す環境変数を追加します。 worker ロールの場合は、コードをロールの `OnStart` メソッドに追加する必要があります。
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Web ロール (ASP.NET) の場合は、コードを Web アプリケーションの `Application_Start` メソッドに追加する必要があります。
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. ロールの ApplicationInsights.config ファイルを更新して、`SnapshotCollector` によって使用される一時フォルダーの場所をオーバーライドします。
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Application Insights 検索を使用してスナップショット付きの例外を検索する

スナップショットが作成されている場合、例外がスローされるとスナップショット ID がタグ付けされます。 Application Insights に例外テレメトリがレポートされると、そのスナップショット ID はカスタム プロパティとして含まれます。 Application Insights の **[検索]** を使用すると、`ai.snapshot.id` カスタム プロパティを使用してすべてのテレメトリを見つけることができます。

1. Azure Portal の Application Insights のリソースを参照します。
2. **[Search (検索)]** をクリックします。
3. [検索] テキスト ボックスに `ai.snapshot.id` と入力し、Enter キーを押します。

![ポータルでスナップショット ID を使用してテレメトリを検索](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

この検索に結果が返されない場合、アプリケーションが選択した時間範囲に Application Insights にスナップショットをレポートしなかったということです。

アップローダー ログから特定のスナップショット ID を検索するには、[検索] ボックスにその ID を入力します。 既知のスナップショットのテレメトリを検索できない場合、次の手順に従います。

1. インストルメンテーション キーを確認することにより、正しい Application Insights のリソースを探していることを再確認します。

2. アップローダー ログからのタイムスタンプを使用して、その時間範囲をカバーするように検索の時間範囲フィルターを調整します。

それでもそのスナップショット ID を持つ例外が表示されない場合、その例外テレメトリが Application Insights にレポートされなかったということです。 このような状況は、スナップショットを取得した後かつ例外テレメトリにレポートする前にアプリケーションがクラッシュした場合に発生する可能性があります。 この場合、`Diagnose and solve problems` で App Service を確認し、予期しない再起動またはハンドルされない例外があったかどうかを確認します。

### <a name="edit-network-proxy-or-firewall-rules"></a>ネットワーク プロキシまたはファイアウォール規則を編集する

アプリケーションがプロキシまたはファイアウォールを介してインターネットに接続する場合は、アプリケーションがスナップショット デバッガーサービスと通信できるように規則を編集する必要があります。 こちらの[スナップショット デバッガーで使用される IP アドレスとポートの一覧](app-insights-ip-addresses.md#snapshot-debugger)を参照してください。

## <a name="next-steps"></a>次の手順

* [コードでスナップポイントを設定](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications)し、例外を待たずにスナップショットを取得します。
* [Web アプリの例外の診断](app-insights-asp-net-exceptions.md)に関する記事では、Application Insights に表示される例外を増やす方法を説明しています。
* [スマート検出](app-insights-proactive-diagnostics.md)は、パフォーマンスの異常を自動的に検出します。
