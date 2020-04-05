---
title: OpenCensus Python を使用した Azure Application Insights における受信要求の追跡 | Microsoft Docs
description: Python アプリの要求呼び出しを OpenCensus Python で監視します。
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669949"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>OpenCensus Python を使用した受信要求の追跡

受信要求データは、OpenCensus Python とそのさまざまな統合を使用して収集されます。 一般的な Web フレームワーク `django`、`flask` および `pyramid` 上に構築された Web アプリケーションに送信された受信要求データを追跡します。 その後、データは Azure Monitor の Application Insights に `requests` テレメトリとして送信されます。

まず、最新の [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md) を使用して Python アプリケーションをインストルメント化します。

## <a name="tracking-django-applications"></a>Django アプリケーションの追跡

1. `opencensus-ext-django`PyPI[ から ](https://pypi.org/project/opencensus-ext-django/) をダウンロードしてインストールし、`django` ミドルウェアでアプリケーションをインストルメント化します。 `django` アプリケーションに送信された受信要求が追跡されます。

2. `opencensus.ext.django.middleware.OpencensusMiddleware` ファイルの `settings.py` に `MIDDLEWARE` を含めます。

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. `settings.py` の `OPENCENSUS` に AzureExporter が正しく構成されていることを確認します。

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
        }
    }
    ```

4. また、追跡したくない要求については、`settings.py` の `BLACKLIST_PATHS` に URL を追加することもできます。

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Flask アプリケーションの追跡

1. `opencensus-ext-flask`PyPI[ から ](https://pypi.org/project/opencensus-ext-flask/) をダウンロードしてインストールし、`flask` ミドルウェアでアプリケーションをインストルメント化します。 `flask` アプリケーションに送信された受信要求が追跡されます。

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. コードで直接 `flask` ミドルウェアを構成できます。 追跡したくない URL からの要求については、その URL を `BLACKLIST_PATHS` に追加します。

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Pyramid アプリケーションの追跡

1. `opencensus-ext-django`PyPI[ から ](https://pypi.org/project/opencensus-ext-pyramid/) をダウンロードしてインストールし、`pyramid` tween でアプリケーションをインストルメント化します。 `pyramid` アプリケーションに送信された受信要求が追跡されます。

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. コードで直接 `pyramid` tween を構成できます。 追跡したくない URL からの要求については、その URL を `BLACKLIST_PATHS` に追加します。

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>次のステップ

* [アプリケーション マップ](../../azure-monitor/app/app-map.md)
* [可用性](../../azure-monitor/app/monitor-web-app-availability.md)
* [検索](../../azure-monitor/app/diagnostic-search.md)
* [Log (Analytics) のクエリ](../../azure-monitor/log-query/log-query-overview.md)
* [トランザクションの診断](../../azure-monitor/app/transaction-diagnostics.md)
