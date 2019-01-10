---
title: Application Insights Profiler に関する問題のトラブルシューティング | Microsoft Docs
description: このページには、Application Insights Profiler の有効化または使用で問題が発生している開発者に役立つ、トラブルシューティングの手順と情報が含まれています。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: d424316cb8287dcf2a9ff04cf8e6889d5bada2cd
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082142"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Application Insights Profiler の有効化または表示に関する問題のトラブルシューティング

## <a id="troubleshooting"></a>一般的なトラブルシューティング

### <a name="profiles-are-only-uploaded-if-there-are-requests-to-your-application-while-the-profiler-is-running"></a>プロファイラーの実行中にアプリケーションへの要求がある場合しか、プロファイルがアップロードされない
Application Insights Profiler は、1 時間ごとに 2 分間、または **[Application Insights Profiler を構成する]** ページの [**[今すぐプロファイル]**](profiler-settings.md ?toc=/azure/azure-monitor/toc.json) ボタンが押されたときに、プロファイラー データを収集します。 ただし、プロファイリング データがアップロードされるのは、プロファイラーの実行中に発生した要求に添付できる場合のみです。 

プロファイラーは、トレース メッセージとカスタム イベントを Application Insights リソースに書き込みます。 これらのイベントを使用すると、プロファイラーがどのように実行されているかを確認できます。 プロファイラーが実行されていてトレースをキャプチャしていると思われても、それらが [パフォーマンス] ページに表示されていない場合は、プロファイラーがどのように実行されているかを確認できます。

1. プロファイラーから Application Insights リソースに送信されたトレース メッセージとカスタム イベントを検索します。 次の検索文字列を使用すると、関連するデータを検索することができます。

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    下のスクリーン ショットの 2 つの異なる AI リソースからの 2 つの検索の例を、次に示します。 
    
    * 左側は、プロファイラーの実行中に要求を取得していないアプリケーションからのものです。 アクティビティがないためにアップロードが取り消されたというメッセージを確認できます。 

    * 右側の例では、プロファイラーが開始され、プロファイラーの実行中に発生した要求が検出されたときにカスタム イベントを送信したことを確認できます。 ServiceProfilerSample カスタム イベントが表示される場合は、プロファイラーがトレースを要求に添付したことを意味し、Application Insights のパフォーマンス ページからトレースを表示することができます。

    * テレメトリがまったく表示されない場合、プロファイラーは実行されていません。 このドキュメントの後半にある、特定のアプリの種類に関するトラブルシューティングのセクションを参照する必要がある場合があります。  

     ![プロファイラーのテレメトリを検索する][profiler-search-telemetry]

1. プロファイラーが実行された期間中に要求がある場合は、プロファイラーが有効になっているアプリケーションの一部分で要求が処理されることを確認します。 アプリケーションが複数のコンポーネントで構成されていることもありますが、Profiler は、コンポーネントのすべてではなく一部に対してのみ有効になっています。 [Application Insights Profiler を構成する] ページに、トレースをアップロード済みのコンポーネントが表示されます。

### <a name="other-things-to-check"></a>チェックすべきその他の項目:
* アプリが .Net Framework 4.6 で動作していること。
* Web アプリが ASP.NET Core アプリケーションの場合は、ASP.NET Core 2.0 以降が実行されている必要があります。
* 表示しようとしているデータが 2 週間以上前のものである場合は、時間フィルターを絞り込んで、もう一度試します。 トレースは 7 日後に削除されます。
* プロキシまたはファイアウォールが https://gateway.azureserviceprofiler.net へのアクセスをブロックしていないことを確認します。

### <a id="double-counting"></a>並列スレッドの二重カウント

スタック ビューアーに表示される合計時間メトリックが、要求期間よりも長くなる場合があります。

この状況は、ある要求に 2 つ以上のスレッドが関連付けられ、それらが並行して動作している場合に発生することがあります。 この場合、スレッド時間全体は経過時間よりも長くなります。 あるスレッドが、他のスレッドが完了するのを待機している可能性があります。 ビューアーはこのソリューションを検出して不要な待機を省略しようとしますが、重大になる可能性がある情報を省略するのではなく、誤って多すぎる情報を表示します。

トレースに並列スレッドがある場合は、要求のクリティカル パスを特定できるように、どのスレッドが待機しているかを確認してください。 通常は、すぐに待機状態になるスレッドが他のスレッドを待機しています。 他のスレッドに集中し、待機中のスレッドの時間は無視してください。

### <a name="error-report-in-the-profile-viewer"></a>プロファイル ビューアーのエラーの報告
ポータルでサポート チケットを送信してください。 エラー メッセージの関連付け ID を必ず含めてください。

## <a name="troubleshooting-profiler-on-app-services"></a>App Services でのプロファイラーのトラブルシューティング
### <a name="for-the-profiler-to-work-properly"></a>プロファイラーを正常に動作させるためには:
* Web アプリ サービス プランは Basic レベル以上である必要があります。
* Web アプリで Application Insights が有効になっている必要があります。
* Web アプリは、**APPINSIGHTS_INSTRUMENTATIONKEY** 設定が、Application Insights SDK で使用されているインストルメンテーション キーと同じキーで構成されている必要があります。
* Web アプリには、**APPINSIGHTS_PROFILERFEATURE_VERSION** アプリ設定が定義され、1.0.0 に設定されている必要があります。
* Web アプリには **DiagnosticServices_EXTENSION_VERSION** アプリ設定が定義され、3 以下の値が設定されている必要があります。
* **ApplicationInsightsProfiler3** Web ジョブが実行されている必要があります。 [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/) に移動し、[ツール] メニューの下の **WebJobs ダッシュボード**を開くと、Web ジョブを確認できます。 下のスクリーンショットのように、ApplicationInsightsProfiler2 リンクをクリックすることで、ログなどの Web ジョブの詳細を確認できます。

    Web ジョブの詳細を表示するためにクリックする必要があるリンクを、次に示します。 

    ![profiler-webjob]

    詳細が表示されるページを次に示します。 プロファイラーが動作していない理由がわからない場合は、ログをダウンロードしてチームに送信することができます。
    
    ![profiler-webjob-log]

### <a name="manual-installation"></a>手動のインストール

Profiler を構成すると、Web アプリの設定に対して更新が行われます。 環境に応じて、更新を手動で適用することもできます。 1 つの例として、アプリケーションが PowerApps 用の Web Apps 環境で実行されている場合があります。

1. **[Web App Control] \(Web アプリ コントロール)** ウィンドウで、**[設定]** を開きます。
1. **.Net Framework バージョン**を **v4.6** に設定します。
1. **[Always On]** を**オン**に設定します。
1. **APPINSIGHTS_INSTRUMENTATIONKEY** アプリ設定を追加し、その値を SDK によって使用されているものと同じインストルメンテーション キーに設定します。
1. **APPINSIGHTS_PROFILERFEATURE_VERSION** アプリ設定を追加し、値を 1.0.0 に設定します。
1. **DiagnosticServices_EXTENSION_VERSION** アプリ設定を追加し、値を 3 以下に設定します。

### <a name="too-many-active-profiling-sessions"></a>アクティブなプロファイリング セッションが多すぎる

現在、同じサービス プランで実行されている最大 4 つの Azure Web アプリとデプロイ スロットで Profiler を有効にできます。 1 つの App Service プランで実行されるより多くの Web アプリがある場合は、プロファイラーによって Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException がスローされる可能性があります。 プロファイラーは、Web アプリごとに個別に実行して、各アプリの ETW セッションの開始を試みます。 ただし、一度にアクティブにできる ETW セッションの数には制限があります。 Profiler Web ジョブが報告しているアクティブなプロファイリング セッションが多すぎる場合は、一部の Web アプリを別のサービス プランに移動します。

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>配置エラー:ディレクトリが空ではありません 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Profiler が有効になっている Web Apps リソースに Web アプリを再デプロイしている場合は、次のようなメッセージが表示されることがあります。

*ディレクトリが空ではありません 'D:\\home\\site\\wwwroot\\App_Data\\jobs'*

このエラーは、スクリプトまたは Azure DevOps デプロイ パイプラインから Web 配置を実行した場合に発生します。 解決するには、次の追加デプロイ パラメーターを、Web 配置タスクに追加します。

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

こうしたパラメーターにより、Application Insights Profiler で使用されたフォルダーが削除され、再デプロイ プロセスのブロックが解除されます。 これらは、現在実行されている Profiler インスタンスに影響を与えません。

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Application Insights Profiler が実行されているかどうかを確認するにはどうすればよいですか。

Profiler は、Web アプリ内の継続的な Web ジョブとして実行されます。 Web アプリのリソースは、[Azure Portal](https://portal.azure.com) で開くことができます。 **[WebJobs]** ウィンドウで、**ApplicationInsightsProfiler** の状態を確認してください。 実行されていない場合は、**[ログ]** を開いて詳細情報を入手します。

## <a name="troubleshooting-problems-with-profiler-and-wad"></a>Profiler と WAD に関する問題のトラブルシューティング

WAD によってプロファイラーが正しく構成されていることを確認するためにチェックすべき項目は、次の 3 つの項目です。 1 つ目として、デプロイされている WAD 構成の概念が期待どおりであることをチェックする必要があります。 2 つ目として、WAD がプロファイラーのコマンドラインで適切な iKey を渡すことをチェックする必要があります。 3 つ目として、プロファイラーが実行されたときにエラーが発生しているかどうかを確認するには、プロファイラーのログ ファイルをチェックできます。 

WAD の構成に使用された設定を確認するには、VM にログインし、次の場所にあるログ ファイルを開く必要があります。 
```
c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.logs  
```
このファイルで、"WadCfg" の文字列を検索することができ、WAD を構成するために VM に渡された設定が検索されます。 プロファイラーのシンクで使用される iKey が正しいことをチェックできます。

2 つ目として、プロファイラーを起動するために使用するコマンドラインをチェックできます。 次のファイルには、プロファイラーを起動するための引数が含まれています。
```
D:\ProgramData\ApplicationInsightsProfiler\config.json
```
プロファイラーのコマンドラインの ikey が正しいことをチェックします。 

3 つ目として、上記の config.json ファイルで検出されたパスを使用して、プロファイラーのログ ファイルをチェックします。 これにより、プロファイラーが使用している設定、およびプロファイラーのステータスとエラー メッセージを示すデバッグ情報が表示されます。 アプリケーションが要求を受信中にプロファイラーが実行中の場合は、次のようなメッセージが表示されます:"iKey からアクティビティが検出されました"。 トレースがアップロード中の場合は、次のようなメッセージが表示されます:"トレースのアップロードを開始します"。 


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








