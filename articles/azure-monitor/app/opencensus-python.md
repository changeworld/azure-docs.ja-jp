---
title: Azure Monitor を使用して Python アプリを監視する (プレビュー) | Microsoft Docs
description: OpenCensus Python を Azure Monitor に接続する手順について説明します
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: ed61cb1bc88c48fe89c4a9390f04747749bd48c5
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329476"
---
# <a name="set-up-azure-monitor-for-your-python-application-preview"></a>Python アプリケーション用に Azure Monitor をセットアップする (プレビュー)

Azure Monitor は、[OpenCensus](https://opencensus.io) との統合により、Python アプリケーションの分散トレース、メトリック収集、およびログ記録をサポートします。 この記事では、OpenCensus for Python を設定し、監視データを Azure Monitor に取得するプロセスについて順を追って説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションが必要です。
- Python がインストールされている必要があります。この記事では [Python 3.7.0](https://www.python.org/downloads/) を使用していますが、以前のバージョンでも軽微な調整で使用できる可能性があります。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="create-application-insights-resource-in-azure-monitor"></a>Azure Monitor で Application Insights のリソースを作成する

まず、インストルメンテーション キー (ikey) を生成する Application Insights リソースを Azure Monitor で作成する必要があります。 次に、この ikey を使用して、Azure Monitor にテレメトリ データを送信する OpenCensus SDK を構成します。

1. **[リソースの作成]**  >  **[開発者ツール]**  >  **[Application Insights]** の順に選択します。

   ![Application Insights リソースの追加](./media/opencensus-python/0001-create-resource.png)

   構成ボックスが表示されたら、次の表を使用して入力フィールドに入力します。

    | 設定        | 値           | 説明  |
   | ------------- |:-------------|:-----|
   | **Name**      | グローバルに一意の値 | 監視しているアプリを識別する名前 |
   | **リソース グループ**     | myResourceGroup      | App Insights データをホストする新しいリソース グループの名前 |
   | **Location** | East US | 近くにある場所か、アプリがホストされている場所の近くを選択します。 |

2. **Create** をクリックしてください。

## <a name="instrumenting-with-opencensus-python-sdk-for-azure-monitor"></a>Azure Monitor 用の OpenCensus Python SDK を使用したインストルメント化

1. OpenCensus Azure Monitor エクスポーターをインストールします。

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` は、Python インストール用に PATH 環境変数が設定されていることを前提としています。 これを構成していない場合は、Python の実行可能ファイルの場所を示す完全なディレクトリ パスを指定する必要があります。その結果次のようなコマンドになります。`C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`

2. SDK では 3 つの Azure Monitor エクスポーターを利用して、さまざまな種類のテレメトリ (トレース、メトリック、ログ) を Azure Monitor に送信します。 それぞれの種類の詳細については、[データ プラットフォームの概要](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)に関するページを参照してください。 以下の手順に従い、3 つのエクスポーターを介した種類ごとの送信方法を確認してください。

### <a name="trace"></a>Trace

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

2. このコードを実行すると、値を入力するように繰り返し求められます。 入力ごとに値はシェルに出力され、**SpanData** の対応する部分が OpenCensus Python Module によって生成されます。 OpenCensus プロジェクトでは、[_複数範囲のツリーとしてトレース_](https://opencensus.io/core-concepts/tracing/)が定義されます。
    
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

3. これはデモとしても有用ですが、最終的には `SpanData` を Azure Monitor に送信するようにします。 前の手順のコードを、次のコード サンプルに基づいて変更します。

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        ),
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

4. Python スクリプトを実行すると引き続き値を入力するように求められますが、今度は値のみがシェルに出力されます。 作成された `SpanData` は Azure Monitor に送信されます。 送信されたスパン データは `dependencies` で確認できます。

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
2. このコードを実行すると、Enter キーを押すように繰り返し求められます。 Enter キーが押された回数を追跡するメトリックが作成されます。 押されるたびに値がインクリメントされ、メトリック情報がコンソールに表示されます。表示されるのは、現在の値と、メトリックが更新された時点の最新のタイムスタンプです。

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. これはデモとしても有用ですが、最終的にはメトリック データを Azure Monitor に送信するようにします。 前の手順のコードを、次のコード サンプルに基づいて変更します。

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
    )
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

4. エクスポーターは、一定の間隔で (既定では 15 秒ごとに) メトリック データを Azure Monitor に送信します。 1 つのメトリックを追跡しているので、このメトリック データは、それに含まれる値およびタイムスタンプに関係なく、間隔ごとに送信されます。 データは `customMetrics` で確認できます。

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

2.  コードにより、値の入力を継続的に求められます。 入力され、前述の値を含むすべての値に対してログ エントリが出力されます。

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

3. これはデモとしても有用ですが、最終的にはメトリック データを Azure Monitor に送信するようにします。 前の手順のコードを、次のコード サンプルに基づいて変更します。

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

## <a name="start-monitoring-in-the-azure-portal"></a>Azure Portal で監視を開始する

1. Azure portal で Application Insights の **[概要]** ページを再度開き、現在実行中のアプリケーションに関する詳細情報を表示できます。 **[Live Metric Stream]\(ライブ メトリック ストリーム\)** を選択します。

   ![ライブ メトリック ストリームが赤い枠線で書き込まれた概要ウィンドウのスクリーンショット](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. **[概要]** ページに戻り、 **[アプリケーション マップ]** を選択して、アプリケーション コンポーネント間の依存関係と呼び出しのタイミングを視覚的に表したレイアウトを表示します。

    ![基本的なアプリケーション マップのスクリーンショット](./media/opencensus-python/0007-application-map.png)

    1 つのメソッド呼び出しのみをトレースしていたため、このアプリケーション マップはあまり興味深い内容ではありません。 ただし、アプリケーション マップをスケーリングして、分散性がはるかに高いアプリケーションを視覚化することができます。

   ![アプリケーション マップ](media/opencensus-python/application-map.png)

3. **[パフォーマンスの調査]** を選択して詳細なパフォーマンス分析を実行し、パフォーマンス低下の根本原因を判断します。

    ![パフォーマンス ウィンドウのスクリーンショット](./media/opencensus-python/0008-performance.png)

4. **[サンプル]** を選択し、右側のウィンドウに表示されるいずれかのサンプルをクリックすると、エンドツーエンド トランザクションの詳細エクスペリエンスが起動します。 このサンプル アプリには単一のイベントのみが表示されますが、より複雑なアプリケーションでは、個々のイベントのコール スタックのレベルまでエンドツーエンドのトランザクションを探索できます。

     ![エンドツーエンドのトランザクション インターフェイスのスクリーンショット](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="view-your-data-with-queries"></a>クエリを使用してデータを表示する

1. アプリケーションから送信されたテレメトリ データは、[Logs(Analytics)] (ログ (分析)) タブを使用して表示できます。

    ![[Logs(Analytics)] (ログ (分析)) が赤い枠線で囲まれた概要ウィンドウのスクリーンショット](./media/opencensus-python/0010-logs-query.png)

2. Azure Monitor トレース エクスポーターを使用して送信されたテレメトリの場合、受信要求は `requests` に、送信要求と処理中の要求は `dependencies` に表示されます。

3. Azure Monitor メトリック エクスポーターを使用して送信されたテレメトリの場合、送信されたメトリックは `customMetrics` に表示されます。

4. Azure Monitor ログ エクスポーターを使用して送信されたテレメトリの場合、ログは `traces` に、例外は `exceptions` に表示されます。

5. クエリとログの使用方法の詳細については、「[Azure Monitor のログ](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)」を参照してください。

## <a name="opencensus-for-python"></a>OpenCensus for Python

* [GitHub の OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [カスタマイズ](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Flask の統合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django の統合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL の統合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>次の手順

* [API の概要](./../../azure-monitor/app/api-custom-events-metrics.md)
* [アプリケーション マップ](./../../azure-monitor/app/app-map.md)
* [エンドツーエンドのパフォーマンスの監視](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>アラート

* [可用性テスト](../../azure-monitor/app/monitor-web-app-availability.md): サイトが Web で表示できることを確認するためのテストを作成します。
* [スマート診断](../../azure-monitor/app/proactive-diagnostics.md): これらのテストは自動的に実行されます。セットアップするために何かをする必要はありません。 アプリの要求が失敗する割合が異常な場合に通知します。
* [メトリック アラート](../../azure-monitor/app/alerts.md): メトリックがしきい値を超えた場合に警告するようにアラートを設定 します。 メトリック アラートはカスタム メトリックで設定し、コード化してアプリに組み込むことができます。