---
title: Azure Monitor を使用して Python アプリを監視する (プレビュー) | Microsoft Docs
description: OpenCensus Python を Azure Monitor に接続する手順について説明します
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 87c0b62cec0b61bfc52ec31233ca7c1f947fdd98
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846136"
---
# <a name="set-up-azure-monitor-for-your-python-application-preview"></a>Python アプリケーション用に Azure Monitor をセットアップする (プレビュー)

Azure Monitor は、[OpenCensus](https://opencensus.io) との統合により、Python アプリケーションの分散トレース、メトリック収集、およびログ記録をサポートします。 この記事では、OpenCensus for Python を設定し、監視データを Azure Monitor に送信するプロセスについて説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
- Python のインストール。 この記事では [Python 3.7.0](https://www.python.org/downloads/) を使用していますが、以前のバージョンでも軽微な変更で使用できる可能性があります。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Azure Monitor で Application Insights のリソースを作成する

まず、インストルメンテーション キー (ikey) を生成する Application Insights リソースを Azure Monitor で作成する必要があります。 次に、この ikey を使用して、Azure Monitor にテレメトリ データを送信する OpenCensus SDK を構成します。

1. **[リソースの作成]**  >  **[開発者ツール]**  >  **[Application Insights]** の順に選択します。

   ![Application Insights リソースを追加する](./media/opencensus-python/0001-create-resource.png)

1. 構成ボックスが表示されます。 下の表を使用して、入力フィールドに入力します。

   | 設定        | Value           | [説明]  |
   | ------------- |:-------------|:-----|
   | **Name**      | グローバルに一意の値 | 監視しているアプリを識別する名前。 |
   | **リソース グループ**     | myResourceGroup      | Application Insights データをホストする新しいリソース グループの名前 |
   | **Location** | East US | お近くの場所か、アプリがホストされている場所の近く |

1. **作成** を選択します。

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Azure Monitor 用の OpenCensus Python SDK を使用したインストルメント化

OpenCensus Azure Monitor エクスポーターをインストールします。

```console
python -m pip install opencensus-ext-azure
```

パッケージと統合の完全な一覧については、[OpenCensus パッケージ](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus)に関するページを参照してください。

> [!NOTE]
> `python -m pip install opencensus-ext-azure` コマンドは、Python インストール用に `PATH` 環境変数が設定されていることを前提としています。 この変数を構成していない場合は、Python の実行可能ファイルの場所を示す完全なディレクトリ パスを指定する必要があります。 その結果次のようなコマンドになります。`C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`

SDK では 3 つの Azure Monitor エクスポーターを使用して、さまざまな種類のテレメトリ (トレース、メトリック、ログ) を Azure Monitor に送信します。 これらのテレメトリの種類の詳細については、[データ プラットフォームの概要](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)に関するページを参照してください。 次の手順に従い、3 つのエクスポーターを使用してこれらのテレメトリの種類を送信します。

## <a name="telemetry-type-mappings"></a>テレメトリの種類のマッピング

次に、Azure Monitor に表示されるテレメトリの種類にマップされる OpenCensus のエクスポーターを示します。

![OpenCensus から Azure Monitor へのテレメトリの種類のマッピングを示すスクリーンショット](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Trace

> [!NOTE]
> OpenCensus の `Trace` は[分散トレース](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)を表します。 `AzureExporter` によって `requests` および `dependency` テレメトリが Azure Monitor に送信されます。

1. まずいくつかのトレース データをローカルで生成しましょう。 Python IDLE か任意のエディターで、次のコードを入力します。

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2. このコードを実行すると、値を入力するように繰り返し求められます。 入力ごとに値はシェルに出力され、OpenCensus Python Module が `SpanData` の対応する部分を生成します。 OpenCensus プロジェクトでは、[複数範囲のツリーとしてトレース](https://opencensus.io/core-concepts/tracing/)が定義されます。
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

3. 値の入力はデモとしても有用ですが、最終的には `SpanData` を Azure Monitor に送信するようにします。 前の手順のコードを、次のコード サンプルに基づいて変更します。

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

4. Python スクリプトを実行すると引き続き値を入力するように求められますが、値のみがシェルに出力されます。 作成された `SpanData` は Azure Monitor に送信されます。 送信されたスパン データは `dependencies` で確認できます。

5. OpenCensus のサンプリングの詳細については、[OpenCensus でのサンプリング](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)に関するページを参照してください。

6. トレース データにおけるテレメトリの相関付けの詳細については、「[OpenCensus Python におけるテレメトリの相関付け](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)」を参照してください。

### <a name="metrics"></a>メトリック

1. まず、いくつかのローカル メトリック データを生成しましょう。 ユーザーが Enter キーを押した回数を追跡する単純なメトリックを作成します。

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
2. このコードを実行すると、Enter キーを押すように繰り返し求められます。 Enter キーが押された回数を追跡するメトリックが作成されます。 各エントリに対して値がインクリメントされ、メトリック情報がコンソールに表示されます。 この情報には、メトリックが更新されたときの現在の値と現在のタイムスタンプが含まれます。

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. 値の入力はデモとしても有用ですが、最終的にはメトリック データを Azure Monitor に送信するようにします。 前の手順のコードを、次のコード サンプルに基づいて変更します。

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

4. エクスポーターは、一定の間隔でメトリック データを Azure Monitor に送信します。 既定値は 15 秒ごとです。 1 つのメトリックを追跡しているので、このメトリック データは、それに含まれる値およびタイムスタンプに関係なく、間隔ごとに送信されます。 データは `customMetrics` で確認できます。

### <a name="logs"></a>ログ

1. まず、いくつかのローカル ログ データを生成しましょう。

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2.  コードにより、値の入力を継続的に求められます。 入力したすべての値に対してログ エントリが生成されます。

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. 値の入力はデモとしても有用ですが、最終的にはログ データを Azure Monitor に送信するようにします。 前の手順のコードを、次のコード サンプルに基づいて変更します。

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

4. エクスポーターはログ データを Azure Monitor に送信します。 データは `traces` で確認できます。 

> [!NOTE]
> このコンテキストでの `traces` は `Tracing` と同じではありません。 `traces` は、`AzureLogHandler` を利用するときに Azure Monitor に表示されるテレメトリの種類を表します。 `Tracing` は OpenCensus の概念を表し、[分散トレース](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)に関連します。

5. ログ メッセージの書式を設定するには、組み込みの Python [ログ API](https://docs.python.org/3/library/logging.html#formatter-objects) で `formatters` を使用します。

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    format_str = '%(asctime)s - %(levelname)-8s - %(message)s'
    date_format = '%Y-%m-%d %H:%M:%S'
    formatter = logging.Formatter(format_str, date_format)
    # TODO: replace the all-zero GUID with your instrumentation key.
    handler = AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

6. ログにカスタム ディメンションを追加することもできます。 これらは、Azure Monitor に `customDimensions` のキーと値のペアとして表示されます。
> [!NOTE]
> この機能を使用するには、ディクショナリを引数としてログに渡す必要があります。その他のデータ構造は無視されます。 文字列の書式設定を維持するには、それらをディクショナリに格納し、引数として渡します。

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    logger.warning('action', {'key-1': 'value-1', 'key-2': 'value2'})
    ```

7. トレース コンテキスト データを使用してログを強化する方法の詳細については、OpenCensus Python [ログの統合](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation)に関するページを参照してください。

## <a name="view-your-data-with-queries"></a>クエリを使用してデータを表示する

アプリケーションから送信されたテレメトリ データは、 **[Logs(Analytics)]** (ログ (分析)) タブを使用して表示できます。

![[Logs(Analytics)] (ログ (分析)) が赤い枠線で囲まれた概要ウィンドウのスクリーンショット](./media/opencensus-python/0010-logs-query.png)

**[アクティブ]** の一覧で次のようにします。

- Azure Monitor のトレース エクスポーターで送信されたテレメトリについては、`requests` の下に受信した要求が表示されます。 送信または処理中の要求は、`dependencies` の下に表示されます。
- Azure Monitor メトリック エクスポーターを使用して送信されたテレメトリの場合、送信されたメトリックは `customMetrics` の下に表示されます。
- Azure Monitor ログ エクスポーターを使用して送信されたテレメトリの場合、ログは `traces` の下に表示されます。 例外は `exceptions` の下に表示されます。

クエリとログの使用方法の詳細については、「[Azure Monitor のログ](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)」を参照してください。

## <a name="learn-more-about-opencensus-for-python"></a>OpenCensus for Python に関する詳細情報

* [GitHub の OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [カスタマイズ](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Flask の統合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django の統合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL の統合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>次のステップ

* [アプリケーション マップ](./../../azure-monitor/app/app-map.md)
* [エンドツーエンドのパフォーマンスの監視](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>警告

* [可用性テスト](../../azure-monitor/app/monitor-web-app-availability.md): サイトが Web で表示できることを確認するためのテストを作成します。
* [スマート診断](../../azure-monitor/app/proactive-diagnostics.md): これらのテストは自動的に実行されます。セットアップするために何かをする必要はありません。 アプリの要求が失敗する割合が異常な場合に通知します。
* [メトリック アラート](../../azure-monitor/app/alerts.md): メトリックがしきい値を超えた場合に警告するようにアラートを設定 します。 メトリック アラートはカスタム メトリックで設定し、コード化してアプリに組み込むことができます。
