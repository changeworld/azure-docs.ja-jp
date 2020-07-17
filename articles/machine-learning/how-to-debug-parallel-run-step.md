---
title: ParallelRunStep のデバッグとトラブルシューティング
titleSuffix: Azure Machine Learning
description: Azure Machine Learning SDK for Python で ParallelRunStep の機械学習パイプラインのデバッグとトラブルシューティングを行います。 パイプラインを使用した開発によくある落とし穴と、リモートからの実行前および実行中にスクリプトをデバッグする際に役立つヒントについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122928"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>ParallelRunStep のデバッグとトラブルシューティング
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、[Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) の [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) クラスのデバッグとトラブルシューティングを行う方法について説明します。

## <a name="testing-scripts-locally"></a>スクリプトのローカルでのテスト

機械学習パイプラインについては、「[スクリプトのローカルでのテスト](how-to-debug-pipelines.md#testing-scripts-locally)」セクションを参照してください。 ParallelRunStep は、ML パイプラインのステップとして実行されるため、どちらにも同じ回答が当てはまります。

## <a name="debugging-scripts-from-remote-context"></a>リモート コンテキストからのスクリプトのデバッグ

スコアリング スクリプトのローカルでのデバッグから実際のパイプラインでのデバッグに切り替えることは、大幅な変更であり、簡単ではありません。 ポータルでのログの検索については、[機械学習パイプラインのリモート コンテキストからのスクリプトのデバッグに関するセクション](how-to-debug-pipelines.md#debugging-scripts-from-remote-context)を参照してください。 そのセクションの情報は、並列ステップの実行にも適用されます。

たとえば、ログファイル `70_driver_log.txt` には、並列実行ステップコードを起動するコントローラーからの情報が含まれています。

並列実行ジョブには分散型の性質があるため、複数の異なるソースからのログが存在します。 ただし、概要情報を提供する、統合されたファイルが 2 つ作成されます。

- `~/logs/overview.txt`:このファイルでは、これまでに作成されたミニバッチ (タスクとも呼ばれます) の数とこれまでに処理されたミニバッチの数に関する概要が示されます。 最後にはジョブの結果が示されます。 ジョブが失敗した場合は、エラー メッセージのほか、どこからトラブルシューティングすればよいかが示されます。

- `~/logs/sys/master.txt`:このファイルでは、実行中のジョブのマスター ノード (オーケストレーターとも呼ばれます) が示されます。 タスクの作成、進行状況の監視、実行結果が含まれます。

EntryScript.logger および PRINT ステートメントを使用したエントリ スクリプトから生成されたログは、次のファイルにあります。

- `~/logs/user/<ip_address>/Process-*.txt`:このファイルには、EntryScript.logger を使用して entry_script から書き込まれたログが含まれています。 また、entry_script からの PRINT ステートメント (stdout) も含まれています。

各ノードによってスコアリング スクリプトがどのように実行されたかを十分に理解する必要がある場合は、ノードごとの各プロセス ログを確認してください。 プロセス ログは、ワーカー ノード別にグループ化されて `sys/worker` フォルダーにあります。

- `~/logs/sys/worker/<ip_address>/Process-*.txt`:このファイルは、各ミニバッチがワーカーによって収集または完了される際に、その詳細情報を提供します。 各ミニバッチについて、次の情報が記録されます。

    - ワーカー プロセスの IP アドレスと PID。 
    - 項目の合計数、正常に処理された項目数、および失敗した項目数。
    - 開始時刻、期間、処理時間、および実行メソッドの時間。

各ワーカーのプロセスのリソース使用量に関する情報も確認できます。 この情報は、CSV 形式で `~/logs/sys/perf/<ip_address>/` にあります。 1 つのノードの場合、ジョブ ファイルは `~logs/sys/perf` の下にあります。 たとえば、リソース使用率をチェックするときは、次のファイルを確認します。

- `Process-*.csv`:ワーカー プロセスごとのリソース使用量。 
- `sys.csv`:ノードごとのログ。

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>リモート コンテキストからユーザー スクリプトのログを記録する方法
次のサンプルコードに示すように、EntryScript からロガーを取得して、ポータルの **logs/user** フォルダーにログが表示されるようにすることができます。

**ロガーを使用したサンプル エントリ スクリプト:**
```python
from entry_script import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>ファイルや、参照テーブルを含むファイルなどのサイド入力を担当者全員に渡す方法はありますか。

サイド入力を含む[データセット](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) オブジェクトを作成し、ワークスペースに登録します。 その後、次のように、推論スクリプト (init() メソッドなど) でアクセスできるようになります。

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>次のステップ

* [azureml-contrib-pipeline-step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) パッケージと ParallelRunStep クラスの[ドキュメント](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py)のへプルについては、SDK リファレンス参照してください。

* 並列実行の手順でパイプラインを使用するには、[高度なチュートリアル](tutorial-pipeline-batch-scoring-classification.md)に従ってください。
