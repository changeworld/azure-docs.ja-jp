---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: jbeauregardb
manager: vravikumar
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: jbeauregardb
ms.openlocfilehash: 6671cd542ece13385c99a7c3b5787d37703894af
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292406"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python](https://www.python.org/downloads/) 2.7 または 3.6 以降。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../create-communication-resource.md)。
- Azure portal で、[Application Insights リソース](../../../azure-monitor/app/create-new-resource.md)を作成します。

## <a name="setting-up"></a>設定

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

1. ターミナルまたはコマンド ウィンドウを開き、自分のアプリ用に新しいディレクトリを作成し、そこに移動します。

   ```console
   mkdir application-insights-quickstart && cd application-insights-quickstart
   ```

1. テキスト エディターを使用して、プロジェクトのルート ディレクトリに **application-insights-quickstart.py** というファイルを作成し、基本的な例外処理など、プログラムの構造を追加します。 このクイックスタートのソース コードはすべて、以降のセクションでこのファイルに追加することになります。

   ```python
    import os
    from opentelemetry import trace
    from opentelemetry.sdk.trace import TracerProvider
    from opentelemetry.sdk.trace.export import BatchSpanProcessor
    from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

    from azure.monitor.opentelemetry.exporter import AzureMonitorTraceExporter

   try:
      print("Azure Communication Services - Access Tokens Quickstart")
      # Quickstart code goes here
   except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>パッケージをインストールする

引き続きアプリケーション ディレクトリで、Python 用の Azure Communication Services ID SDK パッケージと Azure Monitor 用の Microsoft Opentelemetry Exporter をインストールします。

```console
pip install azure-communication-identity
pip install azure-monitor-opentelemetry-exporter --pre
```

## <a name="setting-up-the-telemetry-tracer-with-communication-identity-sdk-calls"></a>Communication ID SDK 呼び出しを使用したテレメトリ トレーサーの設定

接続文字列を使用して `CommunicationIdentityClient` をインスタンス化します。 次のコードは、 `COMMUNICATION_SERVICES_CONNECTION_STRING` という名前の環境変数からリソースの接続文字列を取得します。 [リソースの接続文字列を管理する](../create-communication-resource.md#store-your-connection-string)方法について確認してください。

このコードを `try` ブロック内に追加します。

```python
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

まず、Azure Monitor での要求のトレースを可能にするスパンを作成するために、`AzureMonitorTraceExporter` オブジェクトのインスタンスを作成する必要があります。 Application Insights リソースの接続文字列を指定する必要があります。

```python
exporter = AzureMonitorTraceExporter.from_connection_string(
    "<APPLICATION-INSIGHTS-RESOURCE-CONNECTION-STRING>"
)
```

このエクスポーターにより、要求トレースを可能にするための次のインスタンスを作成できます。 `AzureMonitorTraceExporter` の作成の後に、次のコードを追加します。

```python
    trace.set_tracer_provider(TracerProvider())
    tracer = trace.get_tracer(__name__)
    span_processor = BatchSpanProcessor(exporter)
    trace.get_tracer_provider().add_span_processor(span_processor)
```
トレーサーが初期化されたら、要求のトレースを担当するスパンを作成できます。

```python
with tracer.start_as_current_span(name="MyIdentityApplication"):
    user = identity_client.create_user()
```

## <a name="run-the-code"></a>コードの実行

コンソール プロンプトから、**application-insights-quickstart.py** ファイルが格納されているディレクトリに移動し、次の `python` コマンドを実行してアプリを実行します。

```console
python application-insights-quickstart.py
```