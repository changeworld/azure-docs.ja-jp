---
title: ".NET アプリ向け Azure Application Insights スナップショット デバッガー | Microsoft Docs"
description: "例外が運用 .NET アプリでスローされるときに自動的に収集されるデバッグ スナップショット"
services: application-insights
documentationcenter: 
author: qubitron
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/30/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 94b073df00b690f1347e246eeb7e12fdd1bd7cc1
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>.NET アプリでの例外でのデバッグ スナップショット

"*この機能はプレビュー段階にあります。*"

例外が発生したときに、実行中の Web アプリケーションからデバッグ スナップショットを自動的に収集します。 スナップショットには、例外がスローされたときのソース コードと変数の状態が表示されます。 [Application Insights](app-insights-overview.md) のスナップショット デバッガーにより、Web アプリの例外テレメトリが監視されます。 運用環境の問題の診断に必要な情報を入手できるように、スローされる上位の例外に関するスナップショットが収集されます。 [スナップショット コレクター NuGet パッケージ](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)をアプリケーションに含め、必要に応じて、[ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) にコレクション パラメーターを構成します。 スナップショットが、Application Insights ポータルの[例外](app-insights-asp-net-exceptions.md)に表示されます。

ポータルで [Debug Snapshots (デバッグ スナップショット)] を表示して、コール スタックを表示し、各呼び出しスタック フレームで変数を確認できます。 ソース コードによるデバッグ エクスペリエンスをさらに向上させるには、Visual Studio 2017 Enterprise でスナップショットを開きます。このためには、[Visual Studio 向けのスナップショット デバッガー拡張機能をダウンロード](https://aka.ms/snapshotdebugger)します。

スナップショット コレクションは、Azure Compute または Azure App Service の IIS でホストされる、.NET Framework 4.6 以降で実行される ASP.NET Web アプリで利用できます。

## <a name="configure-snapshot-collection"></a>スナップショット コレクションの構成

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
      <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
      <!-- The maximum number of examples we create for a single problem. -->
      <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
      <!-- The maximum number of problems that we can be tracking at any time. -->
      <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
      <!-- How often to reset problem counters. -->
      <ProblemCounterResetInterval>06:00:00</ProblemCounterResetInterval>
      <!-- The maximum number of snapshots allowed in one minute. -->
      <SnapshotsPerMinuteLimit>2</SnapshotsPerMinuteLimit>
      <!-- The maximum number of snapshots allowed per day. -->
      <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
    </Add>
  </TelemetryProcessors>
```

スナップショットは、Application Insights SDK に表示される例外でのみ収集されます。 場合によっては、ポータルに表示されるスナップショットを使用して例外を表示するように[例外コレクションを構成](app-insights-asp-net-exceptions.md#exceptions)する必要があります。

## <a name="debugging-snapshots-in-the-application-insights-portal"></a>Application Insights ポータルのデバッグ スナップショット

特定の例外または問題 ID のスナップショットが利用できるようになると、*[Open Debug Snapshot (デバッグ スナップショットを開く)]* リンクが Application Insights ポータルの [[例外]](app-insights-asp-net-exceptions.md) に表示されます。

![例外の [Debug Snapshot (デバッグ スナップショット)] ボタンを開く](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

デバッグ スナップショット ビューには、コール スタックと変数ウィンドウが表示されます。 コール スタック ウィンドウでコール スタックのフレームを選択することで、変数ウィンドウでその関数呼び出しのローカル変数とパラメーターを表示できます。

![ポータルでのデバッグ スナップショットの表示](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

スナップショットには機密情報を含めることができますが、既定では表示されません。 スナップショットを表示するには、サブスクリプションまたはリソースのポータルで `Application Insights Snapshot Debugger` ロールが割り当てられている必要があります。 現在、このロールは、ユーザーごとにサブスクリプションの所有者によってのみ割り当てることができます。 Azure Active Directory グループへのロールの割り当ては、現在サポートされていません。

## <a name="debugging-snapshots-with-visual-studio-2017-enterprise"></a>Visual Studio 2017 Enterprise でのデバッグ スナップショット
*[Download Snapshot (スナップショットのダウンロード)]* ボタンをクリックして `.diagsession` ファイルをダウンロードできます。このファイルは Visual Studio 2017 Enterprise で開くことができます。 `.diagsession` ファイルを開くためには、まず [Visual Studio 向けのスナップショット デバッガー拡張機能をダウンロードしてインストール](https://aka.ms/snapshotdebugger)する必要があります。

スナップショット ファイルを開くと、Visual Studio の [ミニダンプ デバッグ] ページが表示されます。このページでスナップショットのデバッグ開始するたには、*[Debug Managed Code (マネージ コードのデバッグ)]* をクリックします。 例外がスローされたコード行が表示され、プロセスの現在の状態をデバッグできます。

![Visual Studio でのデバッグ スナップショットの表示](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

ダウンロードしたスナップショットには、Web アプリケーション サーバーで検出されたすべてのシンボル ファイルが含まれます。 これらのシンボル ファイルは、スナップショット データをソース コードと関連付けるために必要です。 Azure App Service アプリの場合、Web アプリを公開するときに、シンボルのデプロイを必ず有効にしてください。

## <a name="how-snapshots-work"></a>スナップショットのしくみ

アプリケーションの開始時に、スナップショット要求についてアプリケーションを監視する個別のスナップショット アップローダー プロセスが作成されます。 スナップショットが要求されると、実行中のプロセスのシャドウ コピーが約 10 ～ 20 ミリ秒で実行されます。 次に、シャドウ プロセスが分析され、メイン プロセスがユーザーへのトラフィックの実行と提供を続ける間にスナップショットが作成されます。 次に、スナップショットの表示に必要な関連するシンボル (.pdb) ファイルと共にスナップショットが Application Insights にアップロードされます。

## <a name="current-limitations"></a>現時点での制限事項

### <a name="publishing-symbols"></a>シンボルの発行
スナップショット デバッガーでは、Visual Studio で変数をデコードし、デバッグ エクスペリエンスを提供するために、運用サーバーにシンボル ファイルが必要です。 15.2 リリースの Visual Studio 2017 は、Azure App Service に発行する際に、既定でリリース ビルドのシンボルを発行します。 以前のバージョンでは、シンボルがリリース モードで公開されるように、発行プロファイル `.pubxml` ファイルに次の行を追加する必要があります。

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Azure Compute や他の種類の場合、シンボル ファイルがメイン アプリケーション .dll (通常は `wwwroot/bin`) の同じフォルダーにあるか、現在のパスで使用できる必要があります。

### <a name="optimized-builds"></a>最適化されたビルド
場合によっては、ビルド プロセスで適用される最適により、ローカル変数がリリース ビルドに表示されません。 この制限は、NuGet パッケージの今後のリリースで修正されます。

## <a name="next-steps"></a>次のステップ

* [Web アプリの例外の診断](app-insights-asp-net-exceptions.md)に関する記事では、Application Insights に表示される例外を増やす方法を説明しています。 
* [スマート検出](app-insights-proactive-diagnostics.md)は、パフォーマンスの異常を自動的に検出します。

