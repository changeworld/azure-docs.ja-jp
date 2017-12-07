---
title: "Azure Application Insights スナップショット デバッガーのトラブルシューティング ガイド | Microsoft Docs"
description: "Azure Application Insights スナップショット デバッガーについてよく寄せられる質問。"
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 
ms.service: application-insights
ms.workload: 
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mbullwin
ms.openlocfilehash: 5d6a2fe4c3ee373172f5324b6c7a39e4f94f4652
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2017
---
# <a name="snapshot-debugger-troubleshooting-guide"></a>スナップショット デバッガー: トラブルシューティング ガイド

Application Insights スナップショット デバッガーでは、ライブ Web アプリケーションからデバッグのスナップショットを自動的に収集することができます。 スナップショットには、例外がスローされたときのソース コードと変数の状態が表示されます。 Application Insights スナップショット デバッガーの の設定や機能にご不明な点がある場合は、この記事に書かれている、デバッガ―の仕組みや、一般的なトラブルシューティング シナリオの解決策を参考にしてください。 

## <a name="how-does-application-insights-snapshot-debugger-work"></a>Application Insights スナップショット デバッガーの仕組み

Application Insights スナップショット デバッガーは、Application Insights テレメトリ パイプライン (ITelemetryProcessor のインスタンス) の一部で、スナップショット コレクターはコード内でスローされた例外 (AppDomain.FirstChanceException) と Application Insights の例外テレメトリ パイプラインによって追跡される例外の両方を監視します。 スナップショット コレクターをプロジェクトに追加し、Application Insights テレメトリ パイプラインで例外が検出されると、'AppInsightsSnapshotCollectorLogs' という名前の Application Insights カスタム イベントと、Custom Data 内の 'SnapshotCollectorEnabled' が送信されます。 同時に、収集されたスナップショット データ ファイルを Application Insights にアップロードする、'MinidumpUploader.exe' という名前のプロセスが開始されます。  'MinidumpUploader.exe' プロセスが開始されると、'UploaderStart' という名前のカスタム イベントが送信されます。 この手順の後、スナップショット コレクターは、標準の監視動作を開始します。

スナップショット コレクターは、Application Insights 例外テレメトリを監視する間、構成に定義されたパラメーター (ThresholdForSnapshotting、MaximumSnapshotsRequired MaximumCollectionPlanSize、ProblemCounterResetInterval など) を使用して、スナップショットを収集するタイミングを決定します。 すべての規則に該当する場合、コレクターは同じ場所でスローされた、次に発生した例外のスナップショットを要求します。 同時に、'AppInsightsSnapshotCollectorLogs' および 'RequestSnapshots' という名前の Application Insights カスタム イベントが送信されます。 'Release' コードはコンパイラによって最適化されるため、ローカル変数が収集されたスナップショットに表示されない場合があります。 スナップショットを要求したスナップショット コレクターは、例外をスローしたメソッドを非最適化します。 この間、'AppInsightsSnapshotCollectorLogs' とカスタム データ内の 'ProductionBreakpointsDeOptimizeMethod' という名前の Application Insights カスタム イベントが送信されます。  次の例外のスナップショットを収集する場合、ローカル変数は使用可能になります。 スナップショットの収集後は、パフォーマンスのためにコードは再最適化されます。 

> [!NOTE]
> 非最適化するには、Application Insights のサイト拡張機能をインストールする必要があります。

特定の例外についてスナップショットが要求されると、スナップショット コレクターはアプリケーションの例外処理パイプライン (AppDomain.FirstChanceException) の監視を開始します。 例外が再度発生した場合、コレクターは ('AppInsightsSnapshotCollectorLogs' という名前の Application Insights のカスタム イベントおよびカスタム データの' SnapshotStart') のスナップショットを開始します。 続いて、実行中のプロセスのシャドウ コピーが作成されます (ページ テーブルが複製されます)。 これには、通常、10 ～ 20 ミリ秒かかります。 この後、'AppInsightsSnapshotCollectorLogs' という名前の Application Insights カスタム イベントと、カスタム データの 'RequestSnapshots' が送信されます。 分岐プロセスが作成された場合、実行中のアプリケーションのページング メモリと同じ量だけ合計ページング メモリが増分します (ワーキング セットはこれより大幅に小さくなります)。 アプリケーション プロセスは正常に実行され、シャドウ コピー されたプロセスのメモリがディスクにダンプされ、Application Insights にアップロードされます。 スナップショットがアップロードされた後、'UploadSnapshotFinish' という名前の Application Insights カスタム イベントが送信されます。

## <a name="is-the-snapshot-collector-working-properly"></a>スナップショット コレクターは正常に動作していますか。

### <a name="how-to-find-snapshot-collector-logs"></a>スナップショット コレクターのログを検索する方法
[スナップショット コレクター NuGet パッケージ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) バージョン 1.1.0 以降がインストールされている場合は、スナップショット コレクターのログは Application Insight アカウントに送信されます *ProvideAnonymousTelemetry* (既定値は true) が false に設定されていないことを確認してください。

* Azure ポータルで Application Insights のリソースを参照します。
* [概要] セクションで *[検索]* をクリックしてします。
* 検索ボックスに、次の文字列を入力します。
    ```
    AppInsightsSnapshotCollectorLogs OR AppInsightsSnapshotUploaderLogs OR UploadSnapshotFinish OR UploaderStart OR UploaderStop
    ```
* 注意: 必要に応じて *[時間の範囲]* を変更してください。

![スナップショット コレクター ログの検索のスクリーン ショット](./media/app-insights-troubleshoot-snapshot-debugger/001.png)


### <a name="examine-snapshot-collector-logs"></a>スナップショット コレクター ログの調査
スナップショット コレクター ログを検索する場合、対象となる時間範囲に 'UploadSnapshotFinish' がある必要があります。 スナップショットを開く [Open Debug Snapshot]\(デバッグ スナップショットを開く\) が表示されない場合は、電子メールで snapshothelp@microsoft.com までお問い合わせください。その際、電子メールに Application Insights のインストルメンテーション キーを記載してください。

![スナップショット コレクター ログの調査のスクリーン ショット](./media/app-insights-troubleshoot-snapshot-debugger/005.png)

## <a name="i-cannot-find-a-snapshot-to-open"></a>開くスナップショットが見つかりません
次の手順で問題が解決しない場合は、電子メールで snapshothelp@microsoft.com までお問い合わせください。その際、電子メールに Application Insights のインストルメンテーション キーを記載してください。

### <a name="step-1-make-sure-your-application-is-sending-telemetry-data-and-exception-data-to-application-insights"></a>手順 1: アプリケーションが Application Insights にテレメトリ データと例外データを送信していることを確認します。
Application Insights のリソースに移動し、アプリケーションから送信されたデータがあることを確認してください。

### <a name="step-2-make-sure-snapshot-collector-is-added-correctly-to-your-applications-application-insights-telemetry-pipeline"></a>手順 2: スナップショット コレクターが、アプリケーションの Application Insights Telemetry パイプラインに正しく追加されていることを確認します。
「スナップショット コレクター ログの検索」手順でログが見つからない場合は、スナップショット コレクターはプロジェクトに正しく追加されているため、この手順は省略できます。

スナップショット コレクター ログがない場合は、以下を確認します。
* クラシック ASP.NET アプリケーションの *ApplicationInsights.config* ファイルの *<Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">* 行を調べます。

* ASP.NET Core アプリケーションの場合、*IServiceCollection* サービスに *ITelemetryProcessorFactory* と *SnapshotCollectorTelemetryProcessor* が追加されていることを確認します。

* また、公開したアプリケーションで、正しいインストルメンテーション キーが使用されていることを確認します。

* スナップショット コレクターでは 1 つのアプリケーションでの複数のインストルメンテーション キーの使用をサポートしていないため、観察した最初の例外のインストルメンテーション キーにスナップショットを送信します。

* コードで *InstrmentationKey* を手動で設定した場合は、*InstrumentationKey* から *ApplicationInsights.config* 要素を更新します。

### <a name="step-3-make-sure-the-minidump-uploader-is-started"></a>手順 3: ミニダンプ アップローダーが起動していることを確認します。
スナップショット コレクター ログ内で *UploaderStart* を検索します (検索文字列ボックスに 「UploaderStart」と入力します)。 スナップショット コレクターが最初の例外を監視したイベントがあるはずです。 このイベントが存在しない場合は、その他のログで詳細を確認します。 この問題を解決する方法の 1 つとして考えられるのが、アプリケーションの再起動です。

### <a name="step-4-make-sure-snapshot-collector-expressed-its-intent-to-collect-snapshots"></a>手順 4: スナップショット コレクターでスナップショットの収集が設定されていることを確認します。
スナップショット コレクター ログ内で *RequestSnapshots* を検索します (検索文字列ボックスに 「```RequestSnapshots```」と入力します)。  見つからない場合は、設定を調べてください。たとえば *ThresholdForSnapshotting* は、スナップショットの収集を開始する前に発生することが考えられる特定の例外の数を示します。

### <a name="step-5-make-sure-that-snapshot-is-not-disabled-due-to-memory-protection"></a>手順 5: メモリ保護のためのスナップショットが無効になっていないことを確認します。
アプリケーションのパフォーマンスを維持するために、メモリ バッファーが十分ある場合のみ、スナップショットが取得されます。 スナップショット コレクター ログで、'CannotSnapshotDueToMemoryUsage' を検索します。 イベントのカスタム データに詳細な理由が記載されています。 アプリケーションが Azure Web アプリで実行されている場合は、制限が厳しい場合があります。 メモリの一定の規則に一致すると、Azure Web アプリは、アプリを再起動します。 この問題を解決するには、メモリ容量のより大きいコンピューターに、サービス プランを拡張してください。

### <a name="step-6-make-sure-snapshots-were-captured"></a>手順 6: スナップショットがキャプチャされていることを確認してください。
スナップショット コレクター ログで ```RequestSnapshots``` を検索します。  存在しない場合は、設定を確認します。たとえば ```ThresholdForSnapshotting``` は、スナップショットを収集する前に発生した特定の例外の数を示します。

### <a name="step-7-make-sure-snapshots-are-uploaded-correctly"></a>手順 7: はスナップショットが正常にアップロードされていることを確認します。
スナップショット コレクター ログで ```UploadSnapshotFinish``` を検索します。  見つからない場合は、電子メールで snapshothelp@microsoft.com までお問い合わせください。その際、電子メールに Application Insights のインストルメンテーション キーを記載してください。 このイベントが存在する場合は、ログを開いて、Custom Data にある 'SnapshotId' 値をコピーします。 次に、この値を検索します。 これにより、スナップショットに対応する例外が検出されます。 次に、例外をクリックし、デバッグ スナップショットを開きます。 (対応する例外がない場合は、量が多いことを理由に例外テレメトリがサンプリングされている場合があります。他の snapshotId で検索してください。)

![SnapshotId の検索のスクリーン ショット](./media/app-insights-troubleshoot-snapshot-debugger/002.png)

![例外を開く操作のスクリーン ショット](./media/app-insights-troubleshoot-snapshot-debugger/004b.png)

![デバッグ スナップショットを開く操作のスクリーンショット](./media/app-insights-troubleshoot-snapshot-debugger/003.png)

## <a name="snapshot-view-local-variables-are-not-complete"></a>スナップショット ビューのローカル変数が完全でありません

ローカル変数の一部が見つかりません。 アプリケーションがリリース コードを実行している場合、コンパイラによる最適化のために、いくつかの変数が省略されています。 For example:

```csharp
    const int a = 1; // a will be discarded by compiler and the value 1 will be inline.
    Random rand = new Random();
    int b = rand.Next() % 300; // b will be discarded and the value will be directly put to the 'FindNthPrimeNumber' call stack.
    long primeNumber = FindNthPrimeNumber(b);
```

それ以外の場合は、バグがある可能性があります。 電子メールで snapshothelp@microsoft.com までお問い合わせください。その際、電子メールに Application Insights のインストルメンテーション キーを記載してください。

## <a name="snapshot-view-cannot-obtain-value-of-the-local-variable-or-argument"></a>スナップショットの表示: ローカル変数または引数の値を取得できません
[Application Insights のサイト拡張機能](https://www.siteextensions.net/packages/Microsoft.ApplicationInsights.AzureWebSites/)がインストールされていることを確認してください。 問題が解決しない場合は、電子メールで snapshothelp@microsoft.com までお問い合わせください。その際、電子メールに Application Insights のインストルメンテーション キーを記載してください。
