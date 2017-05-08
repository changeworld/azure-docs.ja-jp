---
title: "Web アプリを監視する | Microsoft Docs"
description: "Web アプリの監視を設定する方法を説明します"
services: App-Service
keywords: 
author: btardif
ms.author: byvinyal
ms.date: 04/04/2017
ms.topic: article
ms.service: app-service-web
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 8d9b4a4fa3b62659fc7e2aa1c6329fdc5e01fe39
ms.lasthandoff: 04/21/2017

---
# <a name="monitor-app-service"></a>App Service を監視する
このチュートリアルでは、アプリを監視し、組み込みのプラットフォーム ツールを使用して問題を発生時に解決する方法について説明します。

このドキュメントの各セクションでは、特定の機能を取り上げます。 これらの機能を一緒に使うと、次のことが可能です。
- アプリで発生した問題を特定する。
- コードまたはプラットフォームによって問題が引き起こされるタイミングを特定する。
- コードにおける問題の原因を絞り込む。
- 問題をデバッグおよび修正する。

## <a name="before-you-begin"></a>開始する前に
- 以下に示す手順を実行するには、監視する Web アプリが必要です。 
    - 「[SQL Database を使用して Azure で ASP.NET アプリを作成する](app-service-web-tutorial-dotnet-sqldatabase.md)」チュートリアルで説明している手順に従って、アプリケーションを作成できます。

- アプリケーションの**リモート デバッグ**を試す場合は、Visual Studio が必要です。 
    - まだ Visual Studio 2017 をインストールしていない場合は、[無料](https://www.visualstudio.com/downloads/)の Visual Studio 2017 Community エディションをダウンロードして使用できます。 
    - Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。

## <a name="metrics"></a> 手順 1 - メトリックの表示
**メトリック**は以下のものを把握するうえで役立ちます。 
- アプリケーションの健全性
- アプリのパフォーマンス
- リソース消費

アプリケーションの問題を調査する際には、まずメトリックを確認することをお勧めします。 Azure Portal では、**Azure Monitor** を使用して、アプリのメトリックの視覚的な調査を簡単に行うことができます。

メトリックは、アプリの複数の重要な集計全体にわたる履歴ビューを提供します。 App Service でホストしているアプリについては、Web アプリと App Service プランの両方を監視する必要があります。

> [!NOTE]
> App Service プランは、アプリをホストするために使用される物理リソースのコレクションを表しています。 App Service プランに割り当てられたすべてのアプリケーションは、プランで定義されたリソースを共有します。これにより、複数のアプリをホストする際にコストを節約できます。
>
> App Service プランには、次の定義があります。
> * リージョン: 北ヨーロッパ、米国東部、東南アジアなど
> * インスタンス サイズ: S、M、L など
> * スケール カウント: インスタンス数 1、2、3 など
> * SKU: Free、Shared、Basic、Standard、Premium など

Web アプリのメトリックを確認するには、監視対象のアプリの **[概要]** ブレードに移動します。 ここでは、アプリのメトリックのグラフを **[監視] タイル**として表示できます。 編集するタイルをクリックし、表示するメトリックと時間の範囲を構成します。 

既定では、リソースのブレードは、過去 1 時間のアプリケーションの要求とエラーを示すビューを提供します。
![アプリの監視](media/app-service-web-tutorial-monitoring/app-service-monitor.png)

この例からわかるように、多くの **HTTP サーバー エラー**が発生しているアプリケーションがあります。 この多数のエラーが、このアプリケーションの調査に欠かせない最初の情報です。

> [!TIP]
> Azure Monitor の詳細については、次のリンク先を参照してください。
> - [Azure Monitor の使用](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Azure メトリック](..\monitoring-and-diagnostics\monitoring-overview-metrics.md)
> - [Azure Monitor のサポートされるメトリック](..\monitoring-and-diagnostics\monitoring-supported-metrics.md#microsoftwebsites-including-functions)
> - [Azure のダッシュ ボード](..\azure-portal\azure-portal-dashboards.md)

## <a name="alerts"></a> 手順 2 - アラートの構成
アプリが特定の状態になったときにトリガーされるように**アラート**を構成できます。

「[手順 1 - メトリックの表示](#metrics)」で、アプリケーションに多数のエラーがあることを確認しました。 

エラー発生時に自動的に通知されるようにアラートを構成してみましょう。 この場合は、HTTP 50X エラーの数が特定のしきい値を超えるたびに電子メールが送信されるようにします。

アラートを作成するには、**[監視]** > **[アラート]** の順に移動し、**[[+] アラートの追加]** をクリックします。

![アラート](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts.png)

アラート構成の値を指定します。
- **[リソース]:** アラートで監視するサイト。 
- **[名前]:** アラートの名前。この場合は *High HTTP 50X*。
- **[説明]:** このアラートで監視するものの説明 (プレーン テキスト)。
- **[アラート対象]:** アラートではメトリックかイベントを監視できます。この例ではメトリックを監視します。
- **[メトリック]:** 監視対象のメトリック。この場合は "*HTTP サーバー エラー*"。
- **[条件]:** アラートを送信するタイミング。この場合は、"*より大きい*" というオプションを選びます。
- **[しきい値]:** 監視する値。この場合は *400* です。
- **[期間]:** アラートはメトリックの平均値に基づいて動作します。 期間を短くすると、アラートが発生しやすくなります。 この場合は、"*5 分*" にしています。 
- **電子メールの所有者と共同作成者:** この場合は "*有効*" にしています。

これでアラートが作成されました。アプリが構成済みのしきい値を超えるたびに電子メールが送信されます。 アクティブなアラートは Azure Portal でも確認できます。

![トリガーされたアラート](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts-triggered.png)


> [!TIP]
> Azure アラートの詳細については、次のリンク先を参照してください。
> - [Microsoft Azure のアラートの概要](..\monitoring-and-diagnostics\monitoring-overview-alerts.md)
> - [メトリックでの操作](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [メトリック アラートの作成](..\monitoring-and-diagnostics\insights-alerts-portal.md)

## <a name="companion"></a> 手順 3 - App Service Companion
**App Service Companion** は、アプリを監視する便利な手段とネイティブ操作環境をモバイル デバイス (iOS または Android) に提供します。

App Service Companion を使用すると、次のことができます。
- アプリケーション メトリックを確認する
- アプリケーションのアラートと推奨事項を確認して対処する
- 基本的なトラブルシューティングを実行する (アプリの参照、起動、停止、再起動)
- 重大なイベントのプッシュ通知を受け取る

![App Service Companion](media/app-service-web-tutorial-monitoring/app-service-companion.png)

[![App Service Companion (App Store)](media/app-service-web-tutorial-monitoring/app-service-companion-appStore.png)](https://itunes.apple.com/app/azure-app-service-companion/id1146659260)
[![App Service Companion (Google Play)](media/app-service-web-tutorial-monitoring/app-service-companion-googlePlay.png)](https://play.google.com/store/apps/details?id=azureApps.AzureApps)

App Service Companion は、[App Store](https://itunes.apple.com/app/azure-app-service-companion/id1146659260) または [Google Play](https://play.google.com/store/apps/details?id=azureApps.AzureApps) からインストールできます。

## <a name="diagnose"></a> 手順 4 - 問題の診断と解決
**[問題の診断と解決]** は、アプリケーションの問題をプラットフォームの問題と区別するのに役立ちます。 これには、Web アプリを正常な状態に戻すのに役立つ可能性がある対応策も提示されます。
 
![問題の診断と解決](media/app-service-web-tutorial-monitoring/app-service-monitor-diagnosis.png)

前の手順の例を引き続き利用します。このアプリケーションには、可用性の問題があることがわかります。 一方、プラットフォームの可用性は 100% のままです。

アプリに問題があり、プラットフォームは稼働し続けているときは、アプリケーションの問題に対処すべきであることがはっきりわかります。

## <a name="logging"></a> 手順 5 - ログ記録
アプリケーションの問題であると絞り込めたので、アプリケーションとサーバーのログで詳細を確認できます。

ログ記録では、Web アプリの**アプリケーション診断**と **Web サーバー診断**の両方のログを収集できます。

### <a name="application-diagnostics"></a>アプリケーション診断
アプリケーション診断では、実行時にアプリケーションによって生成されたトレースをキャプチャできます。 

アプリケーションにトレースを追加すると、問題をデバッグし、突き止める力が大幅に向上します。

ASP.NET では、[System.Diagnostics.Trace クラス](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx)を使用して、ログ インフラストラクチャによってキャプチャされるイベントを生成することで、アプリケーション トレースを記録できます。 トレースの重要度を指定して、簡単にフィルター処理できるようにすることもできます。

```csharp
public ActionResult Delete(Guid? id)
{
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id);
    if (id == null)
    {
        System.Diagnostics.Trace.TraceError("/Todos/Delete/ failed, ID is null");
        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
    }
    Todo todo = db.Todoes.Find(id);
    if (todo == null)
    {
        System.Diagnostics.Trace.TraceWarning("/Todos/Delete/ failed, ID: " + id + " could not be found");
        return HttpNotFound();
    }
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id + "completed successfully");
    return View(todo);
}
```
アプリケーションのログ記録を有効にするには、**[監視]** > **[診断ログ]** の順に移動し、アプリケーションのログ記録をオンに切り替えます。

![アプリの監視](media/app-service-web-tutorial-monitoring/app-service-monitor-applogs.png)

アプリケーション ログは、Web アプリのファイル システムに格納するか、Blob Storage にプッシュすることができます。 運用環境では、Blob Storage を使用することをお勧めします。

> [!IMPORTANT]
> ログ記録を有効にすると、アプリケーションのパフォーマンスとリソースの使用率が影響を受けます。 運用環境では、エラー ログをお勧めします。 問題を調査するときにのみ、詳細ログ記録を有効にします。

 ### <a name="web-server-diagnostics"></a>Web サーバー診断
Web サーバーのログは、アプリがインストルメント化されていない場合でも生成されます。 App Service では、次の 3 種類のサーバー ログを収集できます。

- **Web サーバーのログ記録** 
    - [W3C 拡張ログ ファイル形式](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)を使用した、HTTP トランザクションに関する情報。 
    - 全体的なサイト メトリック、たとえば、サイトで処理された要求の数や、特定の IP アドレスからの要求の数を特定するときに便利です。
- **詳細なエラー ログ記録** 
    - 障害 (状態コード 400 以上) を示す HTTP 状態コードの詳細なエラー情報。 
    - [エラーのログ記録の詳細については、こちらをご覧ください。](https://www.iis.net/learn/troubleshoot/diagnosing-http-errors/how-to-use-http-detailed-errors-in-iis)
- **失敗した要求トレース** 
    - 要求の処理に使用された IIS コンポーネントのトレースや各コンポーネントにかかった時間など、失敗した要求の詳細情報。 
    - 失敗した要求ログは、特定の HTTP エラーの原因を特定する場合に便利です。
    - [失敗した要求トレースの詳細については、こちらをご覧ください。](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing/troubleshooting-failed-requests-using-tracing-in-iis)

Web サーバーのログ記録を有効にするには:
- **[監視]** > **[診断ログ]** に移動します。 
- トグルを使用して、さまざまな種類の Web サーバー診断を有効にします。

![アプリの監視](media/app-service-web-tutorial-monitoring/app-service-monitor-serverlogs.png)

> [!IMPORTANT]
> ログ記録を有効にすると、アプリケーションのパフォーマンスとリソースの使用率が影響を受けます。 運用環境では、エラー ログをお勧めします。問題を調査するときにのみ、詳細ログ記録を有効にします。

### <a name="accessing-logs"></a>ログへのアクセス
Blob Storage に格納されているログにアクセスするには、Azure Storage エクスプローラーを使用します。 Web アプリのファイル システムに格納されているログについては、FTP を使用して次のパスにアクセスします。

- **アプリケーション ログ** - `%HOME%/LogFiles/Application/`。
    - このフォルダーには、アプリケーション ログによって生成された情報を含む 1 つ以上のテキスト ファイルが格納されます。
- **失敗した要求トレース** - `%HOME%/LogFiles/W3SVC#########/`。 
    - このフォルダーには、1 つの XSL ファイルと 1 つ以上の XML ファイルが格納されます。 
- **詳細なエラー ログ** - `%HOME%/LogFiles/DetailedErrors/`。 
    - このフォルダーには、アプリによって生成された HTTP エラーに関する詳細な情報を記録した 1 つ以上の .htm ファイルが格納されます。
- **Web サーバー ログ** - `%HOME%/LogFiles/http/RawLogs`。 
    - このフォルダーには、W3C 拡張ログ ファイル形式を使用して形式が設定された 1 つ以上のテキスト ファイルが格納されます。

## <a name="streaming"></a> 手順 6 - ログ ストリーミング
ログのストリーミングは、FTP による[ログ アクセス](#Accessing-Logs)に比べて時間が節約されるため、アプリケーションをデバッグする際に便利です。

App Service では、生成された**アプリケーション ログ**と **Web サーバー ログ**をストリーミングできます。 

> [!TIP]
> ログをストリーミングする前に、「[ログ記録](#logging)」セクションの説明に従って、ログの収集を有効にしていることを確認してください。

ログをストリーミングするには、**[監視]**> **[ログ ストリーム]** に移動します。 探している情報に応じて、**[アプリケーション ログ]** または **[Web サーバー ログ]** を選択します。 ここからバッファーを一時停止、再開、およびクリアすることもできます。

![ストリーミング ログ](media/app-service-web-tutorial-monitoring/app-service-monitor-logstream.png)

> [!TIP]
> ログは、アプリでトラフィックが発生したときにのみ生成されます。ログの詳細度を上げて、取得するイベントや情報を増やすこともできます。

## <a name="remote"></a> 手順 7 - リモート デバッグ
アプリケーションの問題の原因を突き止めたら、**リモート デバッグ**を使ってコードを調べます。

リモート デバッグを使用すると、クラウドで実行中の Web アプリにデバッガーをアタッチできます。 ローカルで実行中のアプリと同じように、ブレークポイントの設定、メモリの直接操作、コードのステップ実行、さらにコード パスの変更を実行できます。

クラウドで実行中の Web アプリにデバッガーをアタッチするには:

- Visual Studio 2017 を使用して、デバッグするアプリのソリューションを開きます。 
- ローカルでの開発と同じように、ブレークポイントをいくつか設定します。
- **Cloud Explorer** を開きます (ctrl + /、ctrl + x)。
- 必要に応じて Azure の資格情報でログインします。
- デバッグするアプリを検索します。
- **[操作]** ウィンドウから**[デバッガーのアタッチ]** を選択します。

![リモート デバッグ](media/app-service-web-tutorial-monitoring/app-service-monitor-vsdebug.png)

Visual Studio により、リモート デバッグ用にアプリケーションが構成され、アプリに移動するブラウザー ウィンドウが開きます。 アプリを参照して、ブレークポイントをトリガーしたり、コードをステップ実行したりします。

> [!WARNING]
> 運用環境におけるデバッグ モードの実行はお勧めできません。 運用環境のアプリが複数のサーバー インスタンスにスケールアウトされていない場合、デバッグを行うと、Web サーバーが他の要求に応答できなくなります。 運用環境の問題のトラブルシューティングを行う場合は、[ログ](#logging)と [Application Insights](#insights) を構成することをお勧めします。



## <a name="explorer"></a> 手順 8 - プロセス エクスプローラー
アプリケーションが複数のインスタンスにスケールアウトされている場合は、**プロセス エクスプローラー**を使うとインスタンス固有の問題の特定に役立ちます。

**プロセス エクスプローラー**では、次のことができます。

- App Service プランの異なるインスタンスのプロセスをすべて列挙する。
- 各プロセスに関連付けられているハンドルとモジュールをドリルダウンおよび表示する。 
- ランナウェイ プロセスの特定に役立つ、プロセス レベルの CPU、ワーキング セット、およびスレッドの数を表示する。
- 開いているファイル ハンドルを見つけ、特定のプロセス インスタンスを強制終了する。

プロセス エクスプローラーには、**[監視]** > **[プロセス エクスプローラー]** からアクセスできます。

![プロセス エクスプローラー](media/app-service-web-tutorial-monitoring/app-service-monitor-processexplorer.png)


## <a name="insights"></a> 手順 9 - Application Insights
**Application Insights** は、アプリのアプリケーション プロファイルと高度な監視機能を提供します。 

Application Insights を使用して、Web アプリの例外とパフォーマンスの問題を検出して診断します。

**[監視]** > **[Application Insights]** で、Web アプリに対して Application Insights を有効にできます。 

> [!NOTE]
> データの収集を開始するために、Application Insights サイト拡張機能をインストールするよう求められる場合があります。 サイト拡張機能をインストールすると、アプリケーションが再起動します。

![Application Insights](media/app-service-web-tutorial-monitoring/app-service-monitor-appinsights.png)

Application Insights は豊富な機能セットを備えています。詳細については、「[次のステップ](#next)」セクションのリンク先を参照してください。

## <a name="next"></a> 次のステップ

 - [Application Insights とは何か?](..\application-insights\app-insights-overview.md)
 - [Application Insights を使用した Azure Web アプリのパフォーマンスの監視](..\application-insights\app-insights-azure-web-apps.md)
 - [Application Insights を使用した Web サイトの可用性と応答性の監視](..\application-insights\app-insights-monitor-web-app-availability.md)
