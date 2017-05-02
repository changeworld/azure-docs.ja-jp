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
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 63bfc6922de224f56186003991f4a51d8f99ed35
ms.lasthandoff: 04/15/2017

---
# <a name="monitor-app-service"></a>App Service を監視する
このチュートリアルでは、組み込みのプラットフォーム ツールを使用して、App Service でホストされているアプリを監視および診断する方法について説明します。 

## <a name="in-this-tutorial"></a>このチュートリアルの内容

1. [プロセス エクスプローラー](#explorer)
    - App Service プランのインスタンスで実行中のアプリに関する詳細情報を入手します。
1. [App Service のメトリック](#metrics) 
   - 組み込みのグラフを使用してアプリを監視する方法について説明します。
   - ニーズに合わせてグラフを構成します。
   - カスタム グラフをピン留めして、カスタム ダッシュボードを作成します。
1. [アラートの構成](#alerts)
    - アプリと App Service プランの両方のアラートを構成する方法について説明します。
1. [App Service Companion](#Companion)
    - モバイル デバイスを使用して、アプリの監視およびトラブルシューティングを行います。
1. [ログの構成](#logging)
    - サーバーとアプリケーションの両方のログを収集する方法について説明します。
    - ログを格納するさまざまな場所とそのアクセス方法について説明します。
1. [ログ ストリーミング](#streaming)
    - ログ ストリーミングを使用して、アプリケーションと wc3 の出力されたログを確認します。
1. [リモート デバッグ](#remote)
    - Visual Studio を使用して、App Service で実行中のプロジェクトをリモートでデバッグします。
1. [問題の診断と解決](#diagnose)
    - アプリケーションの問題を特定し、その解決方法に関する情報を入手します。
1. [Application Insights](#insights)
    - アプリの高度なプロファイルと監視

## <a name="before-you-begin"></a>開始する前に

- 以下に示す手順を実行するには、監視する Web アプリが必要です。 
    - 「[SQL Database を使用して Azure で ASP.NET アプリを作成する](app-service-web-tutorial-dotnet-sqldatabase.md)」チュートリアルで説明している手順に従って、アプリケーションを作成できます。

- アプリケーションの**リモート デバッグ**を試す場合は、Visual Studio が必要です。 
    - まだ Visual Studio 2017 をインストールしていない場合は、[無料](https://www.visualstudio.com/downloads/)の Visual Studio 2017 Community エディションをダウンロードして使用できます。 
    - Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。

## <a name="explorer"></a> 手順 1 - プロセス エクスプローラー

プロセス エクスプローラーは、App Service プランの内部動作に関する詳細情報を入手できるツールです。

**プロセス エクスプローラー**では、次のことができます。

- App Service プランの異なるインスタンスのプロセスをすべて列挙する。
- 各プロセスに関連付けられているハンドルとモジュールをドリルダウンおよび表示する。 
- ランナウェイ プロセスの特定に役立つ、プロセス レベルの CPU、ワーキング セット、およびスレッドの数を表示する。
- 開いているファイル ハンドルを見つけ、特定のプロセス インスタンスを強制終了する。

プロセス エクスプローラーには、**[監視]** > **[プロセス エクスプローラー]** からアクセスできます。

![プロセス エクスプローラー](media/app-service-web-tutorial-monitoring/app-service-monitor-processexplorer.png)

## <a name="metrics"></a> 手順 2 - App Service のメトリックの表示
**メトリック**からは、Web アプリ自体に加え、Web アプリとユーザーおよびプラットフォームとのやり取りに関する詳細情報が得られます。

メトリックを使用すると、次の情報を把握できます。
- アプリで使用しているリソースの数
- アプリのトラフィック量
- 要求/失敗の全体的な数
- 受信データ/送信データの量

App Service でアプリをホストしている場合は、Web アプリと App Service プランを監視する必要があります。

- **アプリ**のメトリックからは、HTTP 要求/エラーと平均応答時間に関する情報が得られます。
- **App Service プラン**のメトリックからは、リソースの利用状況に関する情報が得られます。

Azure Portal では、**Azure Monitor** を使用して、アプリのメトリックの視覚的な調査を簡単に行うことができます。

- 監視するアプリの **[概要]** ブレードに移動します。

![アプリの監視](media/app-service-web-tutorial-monitoring/app-service-monitor.png)

- アプリのメトリックは、**[監視] タイル**として表示できます。
- 編集するタイルをクリックし、表示するメトリックと時間の範囲を構成します。

![グラフの構成](media/app-service-web-tutorial-monitoring/app-service-monitor-configure.png)

- カスタム グラフをダッシュボードにピン留めして、簡単にアクセスしたり、すばやく参照したりできるようにすることができます。

![グラフのピン留め](media/app-service-web-tutorial-monitoring/app-service-monitor-pin.png)

> [!TIP]
> Azure Monitor の詳細については、次のリンク先を参照してください。
> - [Azure Monitor の使用](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Azure メトリック](..\monitoring-and-diagnostics\monitoring-overview-metrics.md)
> - [Azure Monitor のサポートされるメトリック](..\monitoring-and-diagnostics\monitoring-supported-metrics.md#microsoftwebsites-including-functions)
> - [Azure Portal のダッシュボード](..\azure-portal\azure-portal-dashboards.md)

## <a name="alerts"></a> 手順 3 - アラートの構成

**アラート**を使用すると、アプリケーションの監視を自動化できます。

アラートを使用して、アプリに影響する興味深い状況が検出されたときに通知を受け取ります。

アラートを作成するには:
- 監視するアプリの **[概要]** ブレードに移動します。
- メニューの **[監視]** > **[アラート]** に移動します。
- **[+ アラートの追加]** を選択します。
- 必要に応じてアラートを構成します。

![アラート](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts.png)

> [!TIP]
> Azure アラートの詳細については、次のリンク先を参照してください。
> - [Microsoft Azure のアラートの概要](..\monitoring-and-diagnostics\monitoring-overview-alerts.md)
> - [メトリックでの操作](..\monitoring-and-diagnostics\monitoring-overview.md)

## <a name="companion"></a> 手順 4 - App Service Companion
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

## <a name="logging"></a> 手順 5 - ログ記録
ログ記録では、Web アプリの**アプリケーション診断**と **Web サーバー診断**の両方のログを収集できます。

診断ログを使用して、アプリケーションの動作やエラーの条件の把握、アプリケーションの問題のトラブルシューティングを行います。

### <a name="application-diagnostics"></a>アプリケーション診断
アプリケーション診断では、実行時にアプリケーションによって生成されたトレースをキャプチャできます。 

アプリケーション ログを有効にするには:

- **[監視]** > **[診断ログ]** に移動します。 
- トグルを使用して、アプリケーション ログを有効にします。

アプリケーション ログは、Web アプリのファイル システムに格納するか、Blob Storage にプッシュすることができます。

> [!TIP]
> 運用環境では、Blob Storage を使用することをお勧めします。

![アプリの監視](media/app-service-web-tutorial-monitoring/app-service-monitor-applogs.png)

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

> [!IMPORTANT]
> ログ記録を有効にすると、アプリケーションのパフォーマンスとリソースの使用率が影響を受けます。 運用環境では、エラー ログをお勧めします。 問題を調査するときにのみ、詳細ログ記録を有効にします。

 ### <a name="web-server-diagnostics"></a>Web サーバー診断
App Service では、次の 3 種類のサーバー ログを収集できます。

- **Web サーバーのログ記録** 
    - [W3C 拡張ログ ファイル形式](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)を使用した、HTTP トランザクションに関する情報。 
    - 全体的なサイト メトリック、たとえば、サイトで処理された要求の数や、特定の IP アドレスからの要求の数を特定するときに便利です。
- **詳細なエラー ログ記録** 
    - 障害 (状態コード 400 以上) を示す HTTP 状態コードの詳細なエラー情報。 
- **失敗した要求トレース** 
    - 要求の処理に使用された IIS コンポーネントのトレースや各コンポーネントにかかった時間など、失敗した要求の詳細情報。 
    - 失敗した要求ログは、特定の HTTP エラーの原因を特定する場合に便利です。

Web サーバーのログ記録を有効にするには:
- **[監視]** > **[診断ログ]** に移動します。 
- トグルを使用して、さまざまな種類の Web サーバー診断を有効にします。

![アプリの監視](media/app-service-web-tutorial-monitoring/app-service-monitor-serverlogs.png)

> [!IMPORTANT]
> ログ記録を有効にすると、アプリケーションのパフォーマンスとリソースの使用率が影響を受けます。 運用環境では、エラー ログをお勧めします。問題を調査するときにのみ、詳細ログ記録を有効にします。

### <a name="accessing-logs"></a>ログへのアクセス
Blob Storage に格納されているログにアクセスするには、Azure Storage エクスプローラーを使用します。

Web アプリのファイル システムに格納されているログについては、FTP を使用して次のパスにアクセスします。

- **アプリケーション ログ** : /LogFiles/Application/。 
    - このフォルダーには、アプリケーション ログによって生成された情報を含む 1 つ以上のテキスト ファイルが格納されます。
- **失敗した要求トレース** : /LogFiles/W3SVC#########/。 
    - このフォルダーには、1 つの XSL ファイルと 1 つ以上の XML ファイルが格納されます。 
- **詳細なエラー ログ** : /LogFiles/DetailedErrors/。 
    - このフォルダーには、アプリによって生成された HTTP エラーに関する詳細な情報を記録した 1 つ以上の .htm ファイルが格納されます。
- **Web サーバー ログ** : /LogFiles/http/RawLogs。 
    - このフォルダーには、W3C 拡張ログ ファイル形式を使用して形式が設定された 1 つ以上のテキスト ファイルが格納されます。

## <a name="streaming"></a> 手順 6 - ログ ストリーミング
App Service では、生成された**アプリケーション ログ**と **Web サーバー ログ**をストリーミングできます。 

ログのストリーミングは、FTP などのログ アクセス方法に比べて時間が節約されるため、アプリケーションをデバッグする際に便利です。

> [!TIP]
> ログをストリーミングする前に、「[ログ記録](#logging)」セクションの説明に従って、ログの収集を有効にしていることを確認してください。

ログをストリーミングするには:
- **[監視]**> **[ログ ストリーム]** に移動します。
- 探している情報に応じて、**[アプリケーション ログ]** または **[Web サーバー ログ]** を選択します。
- ここからバッファーを一時停止、再開、およびクリアすることもできます。

![ストリーミング ログ](media/app-service-web-tutorial-monitoring/app-service-monitor-logstream.png)

> [!TIP]
> ログは、アプリでトラフィックが発生したときにのみ生成されます。ログの詳細度を上げて、取得するイベントや情報を増やすこともできます。

## <a name="remote"></a> 手順 7 - リモート デバッグ
**リモート デバッグ**を使用すると、クラウドで実行中の Web アプリにデバッガーをアタッチできます。 ローカルで実行中のアプリと同じように、ブレークポイントの設定、メモリの直接操作、コードのステップ実行、さらにコード パスの変更を実行できます。

アプリケーションの問題を見つけて修正するには、リモート デバッグと診断ログを使用します。

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

## <a name="diagnose"></a> 手順 8 - 問題の診断と解決
**問題の診断と解決**は、Web アプリの過去 24 時間のアクティビティをスキャンする組み込みツールです。 UX では、特定したすべての問題の概要ビューが表示されます。

この機能を使用すると、アプリケーションの問題とプラットフォームの問題を切り分け、Web アプリを正常な状態に戻すのに役立つ対応策を見つけることができます。

![問題の診断と解決](media/app-service-web-tutorial-monitoring/app-service-monitor-diagnosis.png)

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
