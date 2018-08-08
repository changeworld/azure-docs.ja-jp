---
title: Azure Functions を監視する
description: Azure Application Insights を Azure Functions とともに使用して、関数の実行を監視する方法を説明します。
services: functions
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/15/2017
ms.author: glenga
ms.openlocfilehash: ba820c594b5afb34c050c74de30300b0dfc8c3a6
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344057"
---
# <a name="monitor-azure-functions"></a>Azure Functions を監視する

## <a name="overview"></a>概要 


  [Azure Functions](functions-overview.md) には、関数を監視するための [Azure Application Insights](../application-insights/app-insights-overview.md) とのビルトイン統合機能が用意されています。 この記事では、Functions がテレメトリ データを Application Insights に送信するように構成する方法を示します。

![Application Insights メトリックス エクスプローラー](media/functions-monitoring/metrics-explorer.png)

Functions には、[Application Insights を使用しないビルトイン監視機能もあります](#monitoring-without-application-insights)。 Application Insights ではより多くのデータや優れたデータ分析方法が提供されるため、Application Insights の使用をお勧めします。

## <a name="application-insights-pricing-and-limits"></a>Application Insights の価格と制限

Function App との Application Insights 統合は無料でお試しいただくことができます。 ただし、1 日に無料で処理できるデータ量には上限があり、テスト中にこの上限に達する場合があります。 1 日あたりの上限に近づいた場合は、ポータルと電子メール通知でお知らせします。  しかし、これらのアラートに気が付かず、上限に達してしまった場合は、新しいログが Application Insights クエリに表示されません。 不要なトラブルシューティングに時間を費やさずにすむように、上限には気を付けてください。 詳細については、「[Application Insights での価格とデータ ボリュームの管理](../application-insights/app-insights-pricing.md)」を参照してください。

## <a name="enable-app-insights-integration"></a>App Insights 統合を有効にする

関数アプリでデータを Application Insights に送信するには、Application Insights リソースのインストルメンテーション キーについて知っておく必要があります。 キーは、APPINSIGHTS_INSTRUMENTATIONKEY という名前のアプリ設定で指定する必要があります。

この接続は、[Azure Portal](https://portal.azure.com) で次の方法で設定できます。

* [新しい関数アプリに対して自動的に設定する](#new-function-app)
* [App Insights リソースを手動で接続する](#manually-connect-an-app-insights-resource)

### <a name="new-function-app"></a>新しい関数アプリ

1. 関数アプリの **[作成]** ページに移動します。

1. **[Application Insights]** スイッチを **[オン]** に設定します。

2. **Application Insights の場所**を選択します。

   データを格納する [Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies/)で、関数アプリのリージョンに最も近いリージョンを選択してください。

   ![関数アプリの作成時に Application Insights を有効にする](media/functions-monitoring/enable-ai-new-function-app.png)

3. 必要な他の情報を入力します。

1. **[作成]** を選択します。

次に、[組み込みログを無効](#disable-built-in-logging)にします。

### <a name="manually-connect-an-app-insights-resource"></a>App Insights リソースを手動で接続する 

1. Application Insights リソースを作成します。 アプリケーションの種類を **[全般]** に設定します。

   ![Application Insights リソースを作成して [全般] を指定する](media/functions-monitoring/ai-general.png)

2. Application Insights リソースの **[要点]** ページからインストルメンテーション キーをコピーします。 表示されているキー値の末尾にカーソルを合わせて、**[コピーするにはクリックします]** ボタンを表示します。

   ![Application Insights のインストルメンテーション キーをコピーする](media/functions-monitoring/copy-ai-key.png)

1. 関数アプリの **[アプリケーション設定]** ページで、**[新しい文字列の追加]** をクリックして[アプリの設定を追加](functions-how-to-use-azure-function-app-settings.md#settings)します。 新しい設定の名前を APPINSIGHTS_INSTRUMENTATIONKEY にして、コピーしたインストルメンテーション キーを貼り付けます。

   ![インストルメンテーション キーをアプリ設定に追加する](media/functions-monitoring/add-ai-key.png)

1. **[Save]** をクリックします。

## <a name="disable-built-in-logging"></a>組み込みログを無効にする

Application Insights を有効にする場合は、[Azure Storage を使用する組み込みログ](#logging-to-storage)を無効にすることをお勧めします。 組み込みログは軽量のワークロードには便利ですが、高負荷の実稼働環境での使用には向きません。 実稼働環境の監視には、Application Insights をお勧めします。 組み込みログを実稼働環境で使用すると、Azure Storage での調整のためにログ レコードが不完全になる場合があります。

組み込みログを無効にするには、`AzureWebJobsDashboard` アプリ設定を削除します。 Azure Portal でアプリ設定を削除する方法については、[関数アプリの管理方法](functions-how-to-use-azure-function-app-settings.md#settings)に関するページで「**アプリケーションの設定**」セクションを参照してください。 アプリ設定を削除する前に、同じ関数アプリの既存の関数によって、Azure Storage のトリガーまたはバインドにその設定が使用されていないことを確認します。

## <a name="view-telemetry-in-monitor-tab"></a>[監視] タブにテレメトリを表示する

前のセクションで示したように Application Insights 統合を設定したら、**[監視]** タブにテレメトリ データを表示できます。

1. 関数アプリのページで、Application Insights が構成されてから少なくとも 1 回実行された関数を選択し、**[監視]** タブを選択します。

   ![[監視] タブを選択する](media/functions-monitoring/monitor-tab.png)

2. 関数呼び出しの一覧が表示されるまで、**[更新]** を一定間隔で選択します。

   テレメトリ クライアントではサーバーに送信するデータをバッチ処理するため、一覧が表示されるには最大で 5 分かかる場合があります  (この遅延は、[Live Metrics Stream](../application-insights/app-insights-live-stream.md) には適用されません。 このサービスは、ページの読み込み時に Functions ホストに接続するため、ログがページに直接ストリーム配信されます)。

   ![呼び出しリスト](media/functions-monitoring/monitor-tab-ai-invocations.png)

2. 特定の関数呼び出しのログを表示するには、その呼び出しの **[日付]** 列のリンクを選択します。

   ![呼び出しの詳細のリンク](media/functions-monitoring/invocation-details-link-ai.png)

   その呼び出しのログ出力は、新しいページに表示されます。

   ![呼び出しの詳細](media/functions-monitoring/invocation-details-ai.png)

両方のページ (呼び出しの一覧と詳細) が、データを取得する Application Insights Analytics クエリにリンクしています。

![Application Insights で実行する](media/functions-monitoring/run-in-ai.png)

![Application Insights Analytics 呼び出しの一覧](media/functions-monitoring/ai-analytics-invocation-list.png)

これらのクエリで表示される呼び出しの一覧は、過去 30 日間、最大 20 行 (`where timestamp > ago(30d) | take 20`) に制限されています。また、呼び出しの詳細の一覧には、過去 30 日間のデータが、無制限で表示されます。

詳細については、後述の「[テレメトリをクエリする](#query-telemetry-data)」を参照してください。

## <a name="view-telemetry-in-app-insights"></a>App Insights でテレメトリを表示する

Azure Portal 上で関数アプリから Application Insights を開くには、関数アプリの **[概要]** ページの **[構成済みの機能]** セクションで **[Application Insights]** リンクを選択します。

![[概要] ページの [Application Insights] リンク](media/functions-monitoring/ai-link.png)


Application Insights の使用方法については、「[Application Insights のドキュメント](https://docs.microsoft.com/azure/application-insights/)」をご覧ください。 このセクションでは、Application Insights でデータを表示する方法の例をいくつか示します。 Application Insights をすでに使い慣れている場合は、[テレメトリ データの構成とカスタマイズに関するセクション](#configure-categories-and-log-levels)に直接進んでかまいません。

[メトリックス エクスプローラー](../application-insights/app-insights-metrics-explorer.md)では、グラフを作成したり、関数呼び出しの数、実行時間、成功率などのメトリックに基づいて警告を表示したりできます。

![メトリックス エクスプローラー](media/functions-monitoring/metrics-explorer.png)

[[失敗]](../application-insights/app-insights-asp-net-exceptions.md) タブでは、グラフを作成したり、関数の失敗やサーバーの例外に基づいて警告を表示したりできます。 **[操作名]** は関数名です。 依存関係に関する[カスタム テレメトリ](#custom-telemetry-in-c-functions)を実装している場合を除き、依存関係のエラーは表示されません。

![エラー](media/functions-monitoring/failures.png)

[[パフォーマンス]](../application-insights/app-insights-performance-counters.md) タブでは、パフォーマンスの問題を分析できます。

![[パフォーマンス]](media/functions-monitoring/performance.png)

**[サーバー]** タブには、リソース使用率とサーバーあたりのスループットが表示されます。 このデータは、関数が原因で基本リソースの処理が遅延している場合のデバッグで役立つことがあります。 サーバーは、**クラウド ロール インスタンス**と呼ばれます。

![サーバー](media/functions-monitoring/servers.png)

[[Live Metrics Stream]](../application-insights/app-insights-live-stream.md) タブには、リアルタイムで作成されたメトリック データが表示されます。

![ライブ ストリーム](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>テレメトリをクエリする

[Application Insights Analytics](../application-insights/app-insights-analytics.md) では、データベース内のテーブルの形式ですべてのテレメトリ データにアクセスできます。 Analytics では、データを抽出、操作、視覚化するためのクエリ言語が用意されています。

![Analytics を選択する](media/functions-monitoring/select-analytics.png)

![分析の例](media/functions-monitoring/analytics-traces.png)

クエリの例を次に示します。 ここでは、直近 30 分間の worker あたりの要求数の分布を示します。

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

使用可能なテーブルは、左側のペインの **[スキーマ]** タブに表示されます。 次のテーブルで、関数呼び出しによって生成されたデータを確認できます。

* **traces** - ランタイムや関数コードによって作成されたログ。
* **requests** - 関数呼び出しごとの要求。
* **exceptions** - ランタイムによってスローされた例外。
* **customMetrics** - 呼び出しの成功数と失敗数、成功率、時間。
* **customEvents** - ランタイムによって追跡されたイベント。たとえば、関数をトリガーする HTTP 要求など。
* **performanceCounters** - 関数が実行されているサーバーのパフォーマンスに関する情報。

その他のテーブルには、可用性テストと、クライアントとブラウザーのテレメトリがあります。 カスタム テレメトリを実装して、テーブルにデータを追加できます。

各テーブルでは、関数固有のデータの一部が `customDimensions` フィールドに保存されます。  たとえば、次のクエリでは、ログ レベルが `Error` のすべてのトレースが取得されます。

```
traces 
| where customDimensions.LogLevel == "Error"
```

ランタイムにより、`customDimensions.LogLevel` と `customDimensions.Category` が提供されます。 関数コードで記述したログにフィールドを追加できます。 この記事の後半にある「[構造化ログ](#structured-logging)」をご覧ください。

## <a name="configure-categories-and-log-levels"></a>カテゴリとログ レベルを構成する

Application Insights はカスタム構成なしで使用できますが、既定の構成ではデータ量が多くなる可能性があります。 Visual Studio Azure サブスクリプションを使っている場合、Application Insights のデータ上限に達する可能性があります。 この記事の残りの部分では、関数から Application Insights に送信するデータを構成し、カスタマイズする方法を説明します。

### <a name="categories"></a>Categories

Azure Functions のロガーでは、すべてのログに*カテゴリ*があります。 カテゴリは、ランタイム コードや関数コードのどの部分にログが記述されているかを示します。 

Functions のランタイムでは、先頭が "Host" のカテゴリを持つログが作成されます。 たとえば、"Function started"、"Function executed"、"Function completed" のログのカテゴリは "Host.Executor" になります。 

ご使用の関数コードでログを記述する場合、カテゴリは "Function" になります。

### <a name="log-levels"></a>ログ レベル

Azure Functions ロガーでは、すべてのログに*ログ レベル*も含まれています。 [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) は列挙型で、整数コードは次のような相対的な重要度を示します。

|LogLevel    |コード|
|------------|---|
|Trace       | 0 |
|デバッグ       | 1 |
|情報 | 2 |
|警告     | 3 |
|エラー       | 4 |
|重大    | 5 |
|なし        | 6 |

ログ レベル `None` については、次のセクションで説明します。 

### <a name="configure-logging-in-hostjson"></a>Host.json でログを構成する

*Host.json* ファイルでは、関数アプリから Application Insights に送信するログの量を設定します。 カテゴリごとに、送信する最小のログ レベルを指定します。 次に例を示します。

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

この例では、次のルールを設定します。

1. カテゴリが "Host.Results" または "Function" のログの場合は、`Error` 以上のレベルのみを Application Insights に送信する。 `Warning` 以下のレベルのログは無視する。
2. カテゴリ Host.Aggregator があるログでは、すべてのログを Application Insights に送信します。 `Trace` ログ レベルは、一部のロガーで `Verbose` と呼ばれるものと同じですが、*host.json* ファイルでは `Trace` を使用します。
3. その他すべてのログについては、`Information` 以上のレベルのみを Application Insights に送信する。

*host.json* 内のカテゴリ値により、先頭の値が同一のすべてのカテゴリについてログを制御する。 たとえば、*host.json* 内の "Host" は、"Host.General"、"Host.Executor"、"Host.Results" などのログを制御します。

*host.json* に先頭の文字列が同一のカテゴリが複数含まれる場合は、同一の部分がより長いものが最初に一致する。 たとえば、"Host.Aggregator" 以外のランタイムからのすべてを `Error` レベルで記録し、"Host.Aggregator" のログを `Information` レベルで記録するとします。

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

あるカテゴリのすべてのログを抑制するには、ログ レベル `None` を使用します。 そのカテゴリのログは書き込まれず、その上のログ レベルはありません。

次のセクションでは、ランタイムが作成するログの主なカテゴリについて説明します。 

### <a name="category-hostresults"></a>カテゴリ Host.Results

これらのログは、Application Insights では "requests" として示されます。 それらは、関数の成功または失敗を示します。

![要求回数グラフ](media/functions-monitoring/requests-chart.png)

これらすべてのログは `Information` レベルで書き込まれるため、`Warning` 以上でフィルターすると、このデータは表示されなくなります。

### <a name="category-hostaggregator"></a>カテゴリ Host.Aggregator

これらのログには、[構成可能な](#configure-the-aggregator)期間の関数呼び出しの回数と平均回数が記録されます。 既定の期間は、30 秒か 1,000 回のどちらか早い方です。 

ログは、Application Insights の **customMetrics** テーブルで利用できます。 例としては、実行回数、成功率、時間などがあります。

![customMetrics クエリ](media/functions-monitoring/custom-metrics-query.png)

これらすべてのログは `Information` レベルで書き込まれるため、`Warning` 以上でフィルターすると、このデータは表示されなくなります。

### <a name="other-categories"></a>その他のカテゴリ

すでに示されているカテゴリ以外のカテゴリのすべてのログは、Application Insights の **traces** テーブルで利用できます。

![traces クエリ](media/functions-monitoring/analytics-traces.png)

"Host" で始まるカテゴリのすべてのログは、Functions ランタイムによって書き込まれます。 "Function started" と "Function completed" のログのカテゴリは "Host.Executor" となります。 正常に実行された場合、これらのログは `Information` レベルとなり、例外は `Error` レベルで記録されます。 ランタイムでは、`Warning` レベルのログも作成されます。たとえば、"queue messages sent to the poison queue" などです。

ご使用の関数コードが書き込んだログのカテゴリは "Function" となり、どのログ レベルにもできます。

## <a name="configure-the-aggregator"></a>アグリゲーターを構成する

前のセクションで述べたように、ランタイムでは期間内の関数実行についてデータが集計されます。 既定の期間は、30 秒か 1,000 回実行のどちらか早い方です。 *host.json* ファイル内でこの設定を構成できます。  次に例を示します。

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>サンプリングを構成する

Application Insights には、負荷がピークのときにテレメトリ データが生成されすぎないようにする[サンプリング](../application-insights/app-insights-sampling.md)機能が備わっています。 Application Insights では、テレメトリ項目の数が特定の割合を超えると、受信した項目の一部がランダムに無視され始めます。 1 秒あたりの項目の最大数に対する既定の設定は 5 です。 *host.json* でサンプリングを構成できます。  次に例を示します。

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

## <a name="write-logs-in-c-functions"></a>C# 関数でログを書き込む

Application Insights で traces として表示されるログを、ご使用の関数コードで書き込むことができます。

### <a name="ilogger"></a>ILogger

関数では、`TraceWriter` パラメーターではなく [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) パラメーターを使用します。 `TraceWriter` を使って作成されたログは Application Insights に送られますが、`ILogger` では[構造化ログ](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)を記録することができます。

`ILogger` オブジェクトで、`Log<level>` [ILogger 上の拡張メソッド](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions#Methods_)を呼び出し、ログを作成します。 たとえば、次のコードでは、カテゴリが "Function" の `Information` ログが書き込まれます。

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>構造化ログ

ログ メッセージで使用するパラメーターは、プレースホルダーの名前ではなく順序によって決定されます。 たとえば、次のコードがあるとします。

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

同じメッセージ文字列を維持しながらパラメーターの順序を逆にすると、結果のメッセージ テキストではそれらの値が誤った場所に配置されます。

プレースホルダーはこのように処理されるため、構造化ログを実行できます。 Application Insights では、メッセージ文字列だけでなく、パラメーターの名前と値のペアも保存します。 そのため、メッセージ引数はクエリ可能なフィールドになります。

たとえば、前の例のようなロガー メソッド呼び出しでは、フィールド `customDimensions.prop__rowKey` をクエリできます。 `prop__` プレフィックスを追加して、ランタイムが追加したフィールドと、ご使用の関数コードが追加したフィールドとの間に競合が起こらないようにします。

フィールド `customDimensions.prop__{OriginalFormat}` を参照することで、元のメッセージ文字列をクエリすることもできます。  

`customDimensions` データの JSON 表現の例を次に示します。

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="logging-custom-metrics"></a>カスタム メトリックのログ記録  

C# スクリプト関数では、`ILogger` 上の `LogMetric` 拡張メソッドを使用して、Application Insights でのカスタム メトリックを作成できます。 メソッド呼び出しの例を次に示します。

```csharp
logger.LogMetric("TestMetric", 1234); 
```

[.NET 用 Application Insights API ](#custom-telemetry-in-c-functions) を使用して `TrackMetric` を呼び出す代わりに、このコードを使用できます。

## <a name="write-logs-in-javascript-functions"></a>JavaScript 関数でログを書き込む

Node.js 関数では、`context.log` を使用してログを書き込みます。 構造化ログは使用できません。

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="logging-custom-metrics"></a>カスタム メトリックのログ記録  

Node.js 関数では、`context.log.metric` メソッドを使用して、Application Insights でカスタム メトリックを作成できます。 メソッド呼び出しの例を次に示します。

```javascript
context.log.metric("TestMetric", 1234); 
```

[Application Insights 用 Node.js SDK ](#custom-telemetry-in-javascript-functions) を使用して `trackMetric` を呼び出す代わりに、このコードを使用できます。

## <a name="custom-telemetry-in-c-functions"></a>C# 関数でのカスタム テレメトリ

[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) NuGet パッケージを使用して、カスタム テレメトリ データを Application Insights に送信できます。

[カスタム テレメトリ API](../application-insights/app-insights-api-custom-events-metrics.md) を使用する C# コードの例を次に示します。 この例は .NET クラス ライブラリ用ですが、Application Insights のコードは C# スクリプト用と同じです。

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
            
            return name == null
                ? req.CreateResponse(HttpStatusCode.BadRequest, 
                    "Please pass a name on the query string or in the request body")
                : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
        }
        
        // This correllates all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

関数呼び出しの要求が重複するため、`TrackRequest` や `StartOperation<RequestTelemetry>` を呼び出さないでください。  Functions ランタイムでは、要求が自動的に追跡されます。

`telemetryClient.Context.Operation.Id` は設定しないでください。 これはグローバル設定です。多くの関数が同時に実行されていると、この設定により正しくない相関関係が発生します。 代わりに、新しいテレメトリ インスタンス (`DependencyTelemetry`、`EventTelemetry`) を作成し、その `Context` プロパティを変更してください。 その後、テレメトリ インスタンスを、`TelemetryClient` (`TrackDependency()`、`TrackEvent()`) の対応する `Track` メソッドに渡します。 これにより、現在の関数呼び出しについては、必ず正しい相関関係の詳細がテレメトリで確保されます。

## <a name="custom-telemetry-in-javascript-functions"></a>JavaScript 関数でのカスタム テレメトリ

[Application Insights Node.js SDK](https://www.npmjs.com/package/applicationinsights) は、現在はベータ版です。 カスタム テレメトリを Application Insights に送信するサンプル コードをいくつか次に示します。

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

`tagOverrides` パラメーターにより、関数の呼び出し ID に `operation_Id` を設定します。 この設定により、指定された関数呼び出しの自動生成されたカスタム テレメトリをすべて関連付けることができます。

## <a name="known-issues"></a>既知の問題

### <a name="dependencies"></a>依存関係

他のサービスに対する関数の依存関係は自動的には表示されませんが、依存関係を表示するようにカスタム コードを記述することができます。 [C# カスタム テレメトリ セクション](#custom-telemetry-in-c-functions)にあるサンプル コードで方法を示します。 このサンプル コードでは、次のような Application Insights の*アプリケーション マップ*が作成されます。

![アプリケーション マップ](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>レポートに関する問題

Functions での Application Insights 統合に関する問題をレポートしたり、提案や要求を行ったりするには、[GitHub で問題を作成します](https://github.com/Azure/Azure-Functions/issues/new)。

## <a name="monitoring-without-application-insights"></a>Application Insights を使用しない監視

関数の監視には Application Insights の使用をお勧めします。Application Insights ではより多くのデータと優れたデータ分析方法が提供されるためです。 ただし、必要に応じて、Azure Storage を使用する組み込みログ システムを使用し続けることもできます。

### <a name="logging-to-storage"></a>ストレージへのログ記録

組み込みログは、`AzureWebJobsDashboard` アプリ設定の接続文字列で指定されたストレージ アカウントを使用します。 関数アプリ ページで、関数を選択し、**[監視]** タブを選択して、クラシック ビューで表示し続けるように選択します。

![クラシック ビューに切り替える](media/functions-monitoring/switch-to-classic-view.png)

 関数の実行の一覧が表示されます。 関数実行を選択して、時間、入力データ、エラー、および関連するログ ファイルを確認します。

前に Application Insights を有効にしていたが、組み込みログに戻る場合は、Application Insights を手動で無効にして、**[監視]** タブを選択します。Application Insights 統合を無効にするには、APPINSIGHTS_INSTRUMENTATIONKEY アプリ設定を削除します。

**[監視]** タブに Application Insights データが表示されていても、[組み込みログを無効](#disable-built-in-logging)にしていない場合は、ファイル システムにログ データを表示できます。 ストレージ リソースで [ファイル] に移動し、関数のファイル サービスを選んでから、`LogFiles > Application > Functions > Function > your_function` に移動してログ ファイルを表示します。

### <a name="real-time-monitoring"></a>リアルタイム監視

[Azure コマンド ライン インターフェイス (CLI) 2.0](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/overview) を使用して、ローカル ワークステーションのコマンド ライン セッションにログ ファイルをストリーミングできます。  

Azure CLI 2.0 では、次のコマンドを使用して、サインイン、ご使用のサブスクリプションの選択、ログ ファイルのストリーミングを行います。

```
az login
az account list
az account set <subscriptionNameOrId>
az webapp log tail --resource-group <resource group name> --name <function app name>
```

Azure PowerShell では、次のコマンドを使用して、ご使用の Azure アカウントの追加、サブスクリプションの選択、ログ ファイルのストリーミングを行います。

```
PS C:\> Add-AzureAccount
PS C:\> Get-AzureSubscription
PS C:\> Get-AzureSubscription -SubscriptionName "<subscription name>" | Select-AzureSubscription
PS C:\> Get-AzureWebSiteLog -Name <function app name> -Tail
```

詳細については、[Azure App Service の Web アプリの診断ログの有効化](../app-service/web-sites-enable-diagnostic-log.md#streamlogs)に関するページをご覧ください。

### <a name="viewing-log-files-locally"></a>ログ ファイルをローカルに表示する

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="next-steps"></a>次の手順

詳細については、次のリソースを参照してください。

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core のログ記録](/aspnet/core/fundamentals/logging/)
