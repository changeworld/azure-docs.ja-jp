---
title: ParallelRunStep のデバッグとトラブルシューティング
titleSuffix: Azure Machine Learning
description: Azure Machine Learning SDK for Python で機械学習パイプラインのデバッグとトラブルシューティングを行います。 パイプラインを使用した開発によくある落とし穴と、リモートからの実行前および実行中にスクリプトをデバッグする際に役立つヒントについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 11/21/2019
ms.openlocfilehash: db17dca1a859807f4d92b4953ec0e2785a3a4160
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851977"
---
# <a name="debug-and-troubleshoot-using-parallelrun"></a>ParallelRun の使用に関するデバッグとトラブルシューティング
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、[Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) の [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) クラスのデバッグとトラブルシューティングを行う方法について説明します。

## <a name="testing-scripts-locally"></a>スクリプトのローカルでのテスト

機械学習パイプラインについては、「[スクリプトのローカルでのテスト](how-to-debug-pipelines.md#testing-scripts-locally)」セクションを参照してください。 ParallelRunStep は、ML パイプラインのステップとして実行されるため、どちらにも同じ回答が当てはまります。

## <a name="debugging-scripts-from-remote-context"></a>リモート コンテキストからのスクリプトのデバッグ

スコアリング スクリプトのローカルでのデバッグから実際のパイプラインでのデバッグに切り替えることは、大幅な変更であり、簡単ではありません。 ポータルでのログの検索については、[機械学習パイプラインのリモート コンテキストからのスクリプトのデバッグに関するセクション](how-to-debug-pipelines.md#debugging-scripts-from-remote-context)を参照してください。 そのセクションの情報は、推論のバッチ実行にも当てはまります。

たとえば、ログ ファイル `70_driver_log.txt` には次の情報も記録されます。 

* スクリプトの実行中に出力されたすべてのステートメント。
* スクリプトのスタック トレース。

バッチ推論ジョブには分散型の性質があるため、複数の異なるソースからのログが存在します。 ただし、概要情報を提供する、統合されたファイルが 2 つ作成されます。

- `~/logs/overview.txt`:このファイルでは、これまでに作成されたミニバッチ (タスクとも呼ばれます) の数とこれまでに処理されたミニバッチの数に関する概要が示されます。 最後にはジョブの結果が示されます。 ジョブが失敗した場合は、エラー メッセージのほか、どこからトラブルシューティングすればよいかが示されます。

- `~/logs/master.txt`:このファイルでは、実行中のジョブのマスター ノード (オーケストレーターとも呼ばれます) が示されます。 タスクの作成、進行状況の監視、実行結果が含まれます。

各ノードによってスコアリング スクリプトがどのように実行されたかを十分に理解する必要がある場合は、ノードごとの各プロセス ログを確認してください。 プロセス ログは、ワーカー ノード別にグループ化されて `worker` フォルダーにあります。

- `~/logs/worker/<ip_address>/Process-*.txt`:このファイルは、各ミニバッチがワーカーによって収集または完了される際に、その詳細情報を提供します。 各ミニバッチについて、次の情報が記録されます。

    - ワーカー プロセスの IP アドレスと PID。 
    - 項目の総数と正常に処理された項目の数。 
    - 実時間での開始時刻と終了時刻 (`start1` および `end1`)。 
    - 消費したプロセッサ時間での開始時刻と終了時刻 (`start2` および `end2`)。 

各ワーカーのプロセスのリソース使用量に関する情報も確認できます。 この情報は、CSV 形式で `~/logs/performance/<ip_address>/` にあります。 たとえば、リソース使用率をチェックするときは、次のファイルを確認します。

- `process_resource_monitor_<ip>_<pid>.csv`:ワーカー プロセスごとのリソース使用量。 
- `sys_resource_monitor_<ip>.csv`:ノードごとのログ。

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>リモート コンテキストからユーザー スクリプトのログを記録する方法
次の手順に従ってロガーを設定して、ポータルの **logs/users** フォルダーにログを出力できます。
1. 以下の 1 つ目のコード セクションを entry_script_helper.py ファイルに保存し、エントリ スクリプトと同じフォルダーに格納します。 このクラスは、AmlCompute 内でパスを取得します。 ローカル テストの場合は、ローカル フォルダーが返されるように `get_working_dir()` を変更できます。
2. `init()` メソッドでロガーを構成して使用します。 以下の 2 つ目のコード セクションにその例を示します。 

**entry_script_helper.py:**
```python
"""
This module provides helper features for entry script.
This file should be in Python search paths or in the same folder as the entry script.
"""
import os
import socket
import logging
import time
from multiprocessing import current_process
from azureml.core import Run


class EntryScriptHelper:
    """ A helper to provide common features for entry script."""

    LOG_CONFIGED = False

    def get_logger(self, name="EntryScript"):
        """ Return a logger.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        return logging.getLogger(name)

    def config(self, name="EntryScript", level="INFO"):
        """ Config a logger. This should be called in init() in score module.
            Config the logger one time if not configed.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        logger = logging.getLogger(name)

        formatter = logging.Formatter(
            "%(asctime)s|%(name)s|%(levelname)s|%(process)d|%(thread)d|%(funcName)s()|%(message)s"
        )
        formatter.converter = time.gmtime

        logger.setLevel(level)

        handler = logging.FileHandler(self.get_log_file_path())
        handler.setLevel(level)
        handler.setFormatter(formatter)
        logger.addHandler(handler)

        return logger

    def get_log_file_path(self):
        """ Get the log file path for users.
            Each process has its own log file, so there is not race issue among multiple processes.
        """
        ip_address = socket.gethostbyname(socket.gethostname())
        log_dir = os.path.join(self.get_log_dir(), "user", ip_address)
        os.makedirs(log_dir, exist_ok=True)
        return os.path.join(log_dir, f"{current_process().name}.txt")

    def get_log_dir(self):
        """ Return the folder for logs.
            Files and folders in it will be uploaded and show up in run detail page in the azure portal.
        """
        log_dir = os.path.join(self.get_working_dir(), "logs")
        os.makedirs(log_dir, exist_ok=True)
        return log_dir

    def get_working_dir(self):
        """ Return the working directory."""
        return os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), self.get_run().id)

    def get_temp_dir(self):
        """ Return local temp directory."""
        local_temp_dir = os.path.join(
            os.environ.get("AZ_BATCHAI_JOB_TEMP", ""), "azureml-bi", str(os.getpid())
        )
        os.makedirs(local_temp_dir, exist_ok=True)
        return local_temp_dir

    def get_run(self):
        """ Return the Run from the context."""
        return Run.get_context(allow_offline=False)

```

**ロガーを使用したサンプル エントリ スクリプト:**
```python
"""
This is a sample scoring module.

This module provides a sample which passes the input back without any change.
"""
import os
import logging
from entry_script_helper import EntryScriptHelper

LOG_NAME = "score_file_list"


def init():
    """ Init """
    EntryScriptHelper().config(LOG_NAME)
    logger = logging.getLogger(LOG_NAME)
    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    logger.info(f"{__file__}.output_folder:{output_folder}")
    logger.info("init()")
    os.makedirs(output_folder, exist_ok=True)


def run(mini_batch):
    """ Accept and return the list back."""
    logger = logging.getLogger(LOG_NAME)
    logger.info(f"{__file__}, run({mini_batch})")

    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    for file_name in mini_batch:
        with open(file_name, "r") as file:
            lines = file.readlines()
        base_name = os.path.basename(file_name)
        name = os.path.join(output_folder, base_name)
        logger.info(f"{__file__}: {name}")
        with open(name, "w") as file:
            file.write(f"ouput file {name} from {__file__}:\n")
            for line in lines:
                file.write(line)

    return mini_batch
```

## <a name="next-steps"></a>次の手順

* [azureml-contrib-pipeline-step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) パッケージと ParallelRunStep クラスの[ドキュメント](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py)のへプルについては、SDK リファレンス参照してください。

* バッチ スコアリングのためにパイプラインを使用するには、[高度なチュートリアル](tutorial-pipeline-batch-scoring-classification.md)に従ってください。
