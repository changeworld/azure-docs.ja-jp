---
title: Azure Functions での Python アプリに対するメモリ プロファイル
description: Python アプリのメモリ使用量をプロファイルし、メモリのボトルネックを特定する方法について説明します。
ms.topic: how-to
author: hazhzeng
ms.author: hazeng
ms.date: 3/22/2021
ms.custom: devx-track-python
ms.openlocfilehash: f47451809a2fa2b7d22805e6415056f19c4c1d88
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258811"
---
# <a name="profile-python-apps-memory-usage-in-azure-functions"></a>Azure Functions で Python アプリのメモリ使用量をプロファイルする

開発中、またはローカルの Python 関数アプリ プロジェクトを Azure にデプロイした後に、関数内で発生する可能性のあるメモリのボトルネックを分析することをお勧めします。 このようなボトルネックによって関数のパフォーマンスが低下し、エラーを招く可能性があります。 次の手順では、[memory-profiler](https://pypi.org/project/memory-profiler) Python パッケージを使用する方法について説明します。これにより、関数の実行時のメモリ使用量分析が 1 行ずつ行われます。

> [!NOTE]
> メモリ プロファイルは、開発環境のメモリ フットプリント分析のみを目的としています。 運用環境の関数アプリにはメモリ プロファイラーを適用しないでください。

## <a name="prerequisites"></a>前提条件

Python 関数アプリの開発を開始する前に、次の要件を満たしておく必要があります。

* [Python 3.6.x 以上](https://www.python.org/downloads/release/python-374/)。 Azure Functions でサポートされている Python バージョンの全一覧を確認するには、[Python 開発者ガイド](functions-reference-python.md#python-version)を参照してください。

* [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.x。

* [サポートされているプラットフォーム](https://code.visualstudio.com/docs/supporting/requirements#_platforms)のいずれかにインストールされた [Visual Studio Code](https://code.visualstudio.com/)。

* 有効な Azure サブスクリプション

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="memory-profiling-process"></a>メモリ プロファイルのプロセス

1. requirements.txt に `memory-profiler` を追加して、パッケージがデプロイにバンドルされるようにします。 ローカル コンピューターで開発している場合は、[Python 仮想環境をアクティブ化](create-first-function-cli-python.md#create-venv)し、`pip install -r requirements.txt` によってパッケージを解決することができます。

2. 関数スクリプト (通常は \_\_init\_\_.py) で、`main()` 関数の上に次の行を追加します。 これにより、ルート ロガーによって子ロガー名が報告され、メモリ プロファイル ログがプレフィックス `memory_profiler_logs` によって識別できるようになります。

    ```python
    import logging
    import memory_profiler
    root_logger = logging.getLogger()
    root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
    profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

3. Apply the following decorator above any functions that need memory profiling. This does not work directly on the trigger entrypoint `main()` method. You need to create subfunctions and decorate them. Also, due to a memory-profiler known issue, when applying to an async coroutine, the coroutine return value will always be None.

    ```python
    @memory_profiler.profile(stream=profiler_logstream)

4. Test the memory profiler on your local machine by using azure Functions Core Tools command `func host start`. This should generate a memory usage report with file name, line of code, memory usage, memory increment, and the line content in it.

5. To check the memory profiling logs on an existing function app instance in Azure, you can query the memory profiling logs in recent invocations by pasting the following Kusto queries in Application Insights, Logs.

:::image type="content" source="media/python-memory-profiler-reference/application-insights-query.png" alt-text="Query memory usage of a Python app in Application Insights":::

```text
traces
| where timestamp > ago(1d)
| where message startswith_cs "memory_profiler_logs:"
| parse message with "memory_profiler_logs: " LineNumber "  " TotalMem_MiB "  " IncreMem_MiB "  " Occurences "  " Contents
| union (
    traces
    | where timestamp > ago(1d)
    | where message startswith_cs "memory_profiler_logs: Filename: "
    | parse message with "memory_profiler_logs: Filename: " FileName
    | project timestamp, FileName, itemId
)
| project timestamp, LineNumber=iff(FileName != "", FileName, LineNumber), TotalMem_MiB, IncreMem_MiB, Occurences, Contents, RequestId=itemId
| order by timestamp asc
```

## <a name="example"></a>例

次に示すのは、それぞれ "HttpTriggerAsync" と "HttpTriggerSync" という名前の非同期および同期の HTTP トリガーでメモリ プロファイルを実行する例です。 作成するのは、単に Microsoft のホーム ページに GET 要求を送信する Python 関数アプリです。

### <a name="create-a-python-function-app"></a>Python 関数アプリを作成する

Python 関数アプリは Azure Functions で指定された[フォルダー構造](functions-reference-python.md#folder-structure)に従っている必要があります。 プロジェクトをスキャフォールディングするには、次のコマンドを実行して Azure Functions Core Tools を使用することをお勧めします。

```bash
func init PythonMemoryProfilingDemo --python
cd PythonMemoryProfilingDemo
func new -l python -t HttpTrigger -n HttpTriggerAsync -a anonymous
func new -l python -t HttpTrigger -n HttpTriggerSync -a anonymous
```

### <a name="update-file-contents"></a>ファイルの内容を更新する

requirements.txt には、プロジェクトで使用されるパッケージを定義します。 Azure Functions SDK と memory-profiler に加えて、非同期 HTTP 要求用の `aiohttp` と同期 HTTP 呼び出し用の `requests` を挿入します。

```text
# requirements.txt

azure-functions
memory-profiler
aiohttp
requests
```

さらに、非同期 HTTP トリガー `HttpTriggerAsync/__init__.py` を再生成して、メモリ プロファイラー、ルート ロガー形式、ロガー ストリーミング バインドを構成することも必要です。

```python
# HttpTriggerAsync/__init__.py

import azure.functions as func
import aiohttp
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

async def main(req: func.HttpRequest) -> func.HttpResponse:
    await get_microsoft_page_async('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Is Loaded",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
async def get_microsoft_page_async(url: str):
    async with aiohttp.ClientSession() as client:
        async with client.get(url) as response:
            await response.text()
    # @memory_profiler.profile does not support return for coroutines.
    # All returns become None in the parent functions.
    # GitHub Issue: https://github.com/pythonprofilers/memory_profiler/issues/289
```

同期 HTTP トリガーについては、次の `HttpTriggerSync/__init__.py` コード セクションを参照してください。

```python
# HttpTriggerSync/__init__.py

import azure.functions as func
import requests
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

def main(req: func.HttpRequest) -> func.HttpResponse:
    content = profile_get_request('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Response Size: {len(content)}",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
def profile_get_request(url: str):
    response = requests.get(url)
    return response.content
```

### <a name="profile-python-function-app-in-local-development-environment"></a>ローカル開発環境で Python 関数アプリをプロファイルする

上記のすべての変更を行ったら、さらにいくつかの手順を行って、Azure Functions ランタイムの Python 仮想環境を初期化します。

1. 好みに応じて、Windows PowerShell または任意の Linux シェルを開きます。
2. Windows では `py -m venv .venv`、Linux では `python3 -m venv .venv` を使用して、Python 仮想環境を作成します。
3. Windows PowerShell では `.venv\Scripts\Activate.ps1`、Linux シェルでは `source .venv/bin/activate` を使用して、Python 仮想環境をアクティブ化します。
4. `pip install requirements.txt` を使用して Python の依存関係を復元します。
5. Azure Functions Core Tools `func host start` を使用して、Azure Functions ランタイムをローカルで起動します。
6. GET 要求を `https://localhost:7071/api/HttpTriggerAsync` または `https://localhost:7071/api/HttpTriggerSync` に送信します。
7. Azure Functions Core Tools で、下のセクションのようなメモリ プロファイル レポートが表示されます。

    ```text
    Filename: <ProjectRoot>\HttpTriggerAsync\__init__.py
    Line #    Mem usage    Increment  Occurences   Line Contents
    ============================================================
        19     45.1 MiB     45.1 MiB           1   @memory_profiler.profile
        20                                         async def get_microsoft_page_async(url: str):
        21     45.1 MiB      0.0 MiB           1       async with aiohttp.ClientSession() as client:
        22     46.6 MiB      1.5 MiB          10           async with client.get(url) as response:
        23     47.6 MiB      1.0 MiB           4               await response.text()
    ```

## <a name="next-steps"></a>次のステップ

Python による Azure Functions 開発の詳細については、次のリソースを参照してください。

* [Azure Functions の Python 開発者向けガイド](functions-reference-python.md)
* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions 開発者向けリファレンス](functions-reference.md)
