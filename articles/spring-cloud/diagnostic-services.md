---
title: Azure Spring Cloud でログとメトリックを分析する |Microsoft Docs
description: Azure Spring Cloud で診断データを分析する方法について説明します
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: ebe438bd2dc5b4921ce733001f3c9df19bc592fe
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607860"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>診断設定でログとメトリックを分析する

Azure Spring Cloud の診断機能を使用すると、次のいずれかのサービスを使用してログとメトリックを分析できます。

* Azure Log Analytics を使用する。最初にストレージに書き込まなくても、データはすぐに書き込まれます。
* 監査や手動での検査に使用するためにストレージ アカウントに保存する。 保持期間を指定できます (日数)。
* サード パーティのサービスやカスタム分析ソリューションで取り込むためにイベント ハブにストリーム配信する。

まず、これらのサービスのいずれかを有効にしてデータを受信します。 Log Analytics の構成については、「[Azure Monitor で Log Analytics の使用を開始する](../azure-monitor/log-query/get-started-portal.md)」を確認してください。 

## <a name="configure-diagnostics-settings"></a>診断設定の構成

1. Azure portal で Azure Spring Cloud インスタンスに移動します。
1. **[診断設定]** オプションを選択し、 **[診断設定の追加]** を選択します。
1. 設定の名前を入力してから、ログを送信する場所を選択します。 次の 3 つのオプションを自由に組み合わせて選択できます。
    * **ストレージ アカウントへのアーカイブ**
    * **イベント ハブへのストリーム**
    * **Log Analytics への送信**

1. 監視するログ カテゴリとメトリック カテゴリを選択してから、保持期間 (日数) を指定します。 保持期間はストレージ アカウントにのみ適用されます。
1. **[保存]** を選択します。

> [!NOTE]
> ログまたはメトリックが生成されてから、ストレージ アカウント、イベント ハブ、または Log Analytics に表示されるまでに、最大 15 分のギャップがある場合があります。

## <a name="view-the-logs"></a>ログを表示する

### <a name="use-log-analytics"></a>Log Analytics の使用

1. Azure portal の左ウィンドウで、 **[Log Analytics]** を選択します。
1. 診断設定を追加したときに選択した Log Analytics ワークスペースを選択します。
1. **[ログ検索]** ウィンドウを開くには、 **[ログ]** を選択します。
1. **[ログ]** 検索ボックスに、次のような単純なクエリを入力します。

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. 検索結果を表示するには、 **[実行]** を選択します。
1. フィルター条件を設定することで、特定のアプリケーションまたはインスタンスのログを検索できます。

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Log Analytics で使用されるクエリ言語の詳細については、「[Azure Monitor ログ クエリ](../azure-monitor/log-query/query-language.md)」を参照してください。

### <a name="use-your-storage-account"></a>ストレージ アカウントを使用する 

1. Azure portal の左ウィンドウで、 **[ストレージ アカウント]** を選択します。

1. 診断設定を追加したときに選択したストレージ アカウントを選択します。
1. **[BLOB コンテナー]** ウィンドウを開くには、 **[BLOB]** を選択します。
1. アプリケーション ログを確認するには、**insights-logs-applicationconsole** という名前のコンテナーを検索します。
1. アプリケーション メトリックを確認するには、**insights-metrics-pt1m** という名前のコンテナーを検索します。

ストレージ アカウントへの診断情報の送信の詳細については、「[Azure Storage への診断データの保存と表示](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage)」を参照してください。

### <a name="use-your-event-hub"></a>イベント ハブを使用する

1. Azure portal の左ウィンドウで、 **[イベント ハブ]** を選択します。

1. 診断設定を追加したときに選択したイベント ハブを検索し、選択します。
1. **[イベント ハブの一覧]** ウィンドウを開くには、 **[イベント ハブ]** を選択します。
1. アプリケーション ログを確認するには、**insights-logs-applicationconsole** という名前のイベント ハブを検索します。
1. アプリケーション メトリックを確認するには、**insights-metrics-pt1m** という名前のイベント ハブを検索します。

イベント ハブへの診断情報の送信の詳細については、「[Event Hubs を利用してホット パスの Azure Diagnostics データをストリーム配信する](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)」を参照してください。

## <a name="analyze-the-logs"></a>ログの分析

Azure Log Analytics には Kusto が用意されているので、ログをクエリして分析することができます。 Kusto を使用したログのクエリの簡単な概要については、[Log Analytics チュートリアル](../azure-monitor/log-query/get-started-portal.md)を確認してください。

アプリケーション ログには、アプリケーションの正常性やパフォーマンスなどに関する重要な情報が記載されています。 次のセクションでは、アプリケーションの現在の状態と過去の状態を理解するのに役立つ単純なクエリを示します。

### <a name="show-application-logs-from-azure-spring-cloud"></a>Azure Spring Cloud からアプリケーション ログを表示する

Azure Spring Cloud からアプリケーション ログの一覧 (時間順に並べ替え、最新のログを最初に表示) を確認するには、次のクエリを実行します。

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>エラーまたは例外を含むログ エントリを表示する

エラーまたは例外を示す並べ替えられていないログ エントリを確認するには、次のクエリを実行します。

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

このクエリを使用してエラーを検出するか、クエリ用語を変更して特定のエラー コードまたは例外を検索します。 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>過去 1 時間にアプリケーションから報告されたエラーと例外の数を表示する

過去 1 時間にアプリケーションによってログに記録されたエラーと例外の数を表示する円グラフを作成するには、次のクエリを実行します。

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>アプリケーション ログのクエリについての詳細情報

Azure Monitor では、Log Analytics を使用したアプリケーション ログのクエリが広範にサポートされています。 このサービスについて詳しくは、「[Azure Monitor でログ クエリの使用を開始する](../azure-monitor/log-query/get-started-queries.md)」を参照してください。 アプリケーション ログを分析するクエリの作成の詳細については、「[Azure Monitor のログ クエリの概要](../azure-monitor/log-query/log-query-overview.md)」を参照してください。
