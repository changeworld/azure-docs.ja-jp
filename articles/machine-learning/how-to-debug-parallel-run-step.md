---
title: ParallelRunStep のトラブルシューティング
titleSuffix: Azure Machine Learning
description: 機械学習パイプラインで ParallelRunStep を使用してエラーが発生した場合のトラブルシューティング方法に関するヒント。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: pansav
author: psavdekar
ms.date: 10/21/2021
ms.openlocfilehash: 47ad08328588ca3b380298b6e796cc05d3af29fe
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564330"
---
# <a name="troubleshooting-the-parallelrunstep"></a>ParallelRunStep のトラブルシューティング

この記事では、[Azure Machine Learning SDK](/python/api/overview/azure/ml/intro) の [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) クラスを使用してエラーが発生した場合のトラブルシューティングの方法について説明します。

パイプラインのトラブルシューティングに関する一般的なヒントについては、「[機械学習パイプラインのトラブルシューティング](how-to-debug-pipelines.md)」を参照してください。

## <a name="testing-scripts-locally"></a>スクリプトのローカルでのテスト

 ParallelRunStep は、ML パイプラインのステップとして実行されます。 最初のステップとして[ご使用のスクリプトをローカルでテストする](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines)ことをお勧めします。

## <a name="entry-script-requirements"></a>エントリ スクリプトの要件

`ParallelRunStep` のエントリ スクリプトには、`run()` 関数が "*含まれている必要があります*"。また、オプションで `init()` 関数が含まれています。
- `init()`: この関数は、後の処理のためのコストのかかる準備、または一般的な準備を行うときに使用します。 たとえば、これを使って、モデルをグローバル オブジェクトに読み込みます。 この関数は、プロセスの開始時に 1 回だけ呼び出されます。
    > [!NOTE]
    > `init` メソッドが出力ディレクトリを作成する場合は、`parents=True` および `exist_ok=True` を指定します。 `init` メソッドは、ジョブが実行されているすべてのノードの各ワーカー プロセスから呼び出されます。
-  `run(mini_batch)`:この関数は、`mini_batch` インスタンスごとに実行されます。
    -  `mini_batch`: `ParallelRunStep` は run メソッドを呼び出して、そのメソッドに、リストまたは Pandas `DataFrame` のいずれかを引数として渡します。 mini_batch のエントリはそれぞれ、ファイル パス (入力が `FileDataset` の場合) または Pandas `DataFrame` (入力が `TabularDataset` の場合) になります。
    -  `response`: run() メソッドは、Pandas `DataFrame` または配列を返します。 append_row output_action の場合、これらの返される要素は、共通の出力ファイルに追加されます。 summary_only の場合、要素のコンテンツは無視されます。 すべての出力アクションについて、返される出力要素はそれぞれ、入力ミニバッチ内で成功した 1 つの入力要素の実行を示します。 入力を実行出力結果にマップできるだけの十分なデータが実行結果に含まれていることを確認してください。 実行の出力は出力ファイルに書き込まれますが、順序どおりの書き込みは保証されません。出力でいずれかのキーを使って、入力にマップする必要があります。
        > [!NOTE]
        > 1 つの入力要素に対して 1 つの出力要素が必要です。

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

推論スクリプトと同じディレクトリに他のファイルまたはフォルダーがある場合、現在の作業ディレクトリを特定することでそれらを参照することができます。

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>ParallelRunConfig のパラメーター

`ParallelRunConfig` は、Azure Machine Learning パイプライン内にある `ParallelRunStep` インスタンスの主要な構成です。 これは、お使いのスクリプトをラップし、必要なパラメーターを構成するときに使用します。たとえば、次のようなエントリです。
- `entry_script`:複数のノードで並列で実行されるローカル ファイル パスとしてのユーザー スクリプト。 `source_directory` が存在する場合は、相対パスを使用します。 それ以外の場合は、マシンでアクセス可能な任意のパスを使用します。
- `mini_batch_size`:1 つの `run()` 呼び出しに渡されたミニバッチのサイズ (省略可能。既定値は、`FileDataset` の場合は `10` ファイルで、`TabularDataset` の場合は `1MB` です。)
    - `FileDataset` の場合、これはファイル数を示し、最小値は `1` です。 複数のファイルを 1 つのミニバッチに結合できます。
    - `TabularDataset` の場合は、データのサイズです。 サンプル値は、`1024`、`1024KB`、`10MB`、および `1GB` です。 推奨値は `1MB` です。 `TabularDataset` のミニバッチは、ファイル境界を超えません。 たとえば、さまざまなサイズの .csv ファイルがある場合、ファイルの最小サイズは 100 KB で、最大サイズは 10 MB です。 `mini_batch_size = 1MB` を設定すると、1 MB より小さいファイルは 1 つのミニバッチとして処理されます。 1 MB を超えるファイルは、複数のミニバッチに分割されます。
        > [!NOTE]
        > SQL でサポートされる TabularDataset は、パーティション分割できません。
        > 1 つの parquet ファイルと 1 つの行グループの TabularDataset をパーティション分割することはできません。

- `error_threshold`:処理中に無視する必要のあるエラーの数。`TabularDataset` の場合はレコード エラー数、`FileDataset` の場合はファイル エラー数を示します。 入力全体に対するエラーの数がこの値を超えると、ジョブは中止されます。 エラーのしきい値は入力全体を対象としています。`run()` メソッドに送信された個々のミニバッチを対象にしているものではありません。 範囲は `[-1, int.max]` です。 `-1` 部分は、処理中にすべてのエラーを無視することを示します。
- `output_action`:次のいずれかの値が、出力がどのように編成されるかを示しています。
    - `summary_only`:ユーザー スクリプトによって出力が格納されます。 `ParallelRunStep` では、エラーしきい値の計算にのみ出力が使用されます。
    - `append_row`:すべての入力について、出力フォルダーにファイルが 1 つだけ作成され、行で区切られたすべての出力が追加されます。
- `append_row_file_name`:append_row output_action の出力ファイル名をカスタマイズします (省略可能。既定値は `parallel_run_step.txt` です)。
- `source_directory`:コンピューティング ターゲットで実行されるすべてのファイルを含むフォルダーへのパス (省略可能)。
- `compute_target`:サポートされるのは `AmlCompute` のみです。
- `node_count`:ユーザー スクリプトの実行に使用されるコンピューティング ノードの数。
- `process_count_per_node`: エントリ スクリプトを並列で実行するノードあたりのワーカー プロセスの数。 GPU マシンの場合、既定値は 1 です。 CPU マシンの場合、既定値はノードあたりのコア数です。 ワーカー プロセスが、取得したミニ バッチを渡すことで `run()` を繰り返し呼び出します。 ジョブ内のワーカー プロセスの総数は `process_count_per_node * node_count` で、これにより並列で実行する `run()` の最大数が決定します。
- `environment`:Python 環境定義。 既存の Python 環境が使用されるように、または一時的な環境が設定されるように構成できます。 定義で、必要なアプリケーションの依存関係を設定することもできます (省略可能)。
- `logging_level`:ログの詳細。 値は詳細度が低い順に `WARNING`、`INFO`、`DEBUG` です。 (省略可能。既定値は `INFO` です)
- `run_invocation_timeout`:`run()` メソッド呼び出しのタイムアウト (秒単位)。 (省略可能、既定値は `60` です)
- `run_max_try`:ミニバッチに対する `run()` の最大試行回数。 例外がスローされた場合、`run()` は失敗します。`run_invocation_timeout` に到達した場合は何も返されません (省略可能。既定値は `3` です)。

`mini_batch_size`、`node_count`、`process_count_per_node`、`logging_level`、`run_invocation_timeout`、`run_max_try` を `PipelineParameter` として指定すると、パイプラインの実行を再送信するときに、パラメーターの値を微調整できます。 この例では、`mini_batch_size` と `Process_count_per_node` に `PipelineParameter` を使用し、別の実行を再送信するときにこれらの値を変更します。

#### <a name="cuda-devices-visibility"></a>CUDA デバイスの可視性
GPU を搭載したコンピューティング ターゲットの場合、ワーカー プロセスで環境変数 `CUDA_VISIBLE_DEVICES` が設定されます。 AmlCompute では、GPU デバイスの総数は環境変数 `AZ_BATCHAI_GPU_COUNT_FOUND` で確認でき、これは自動的に設定されます。 各ワーカー プロセスに専用の GPU を設定する場合は、`process_count_per_node` をマシン上の GPU デバイスの数と同じに設定します。 各ワーカー プロセスが一意なインデックスを `CUDA_VISIBLE_DEVICES` に割り当てます。 ワーカー プロセスが何らかの理由で停止した場合、次に開始されるワーカー プロセスが、解放された GPU インデックスを使用します。

GPU デバイスの総数が `process_count_per_node` より少ない場合、ワーカー プロセスには GPU インデックスがすべてが使用されるまで割り当てられます。

GPU デバイスの合計を 2 と想定して `process_count_per_node = 4` を例とすると、プロセス 0 とプロセス 1 のインデックスが 0 と 1 になります。 プロセス 2 と 3 には環境変数がありません。 この環境変数を GPU の割り当てに使用するライブラリの場合、プロセス 2 と 3 には GPU がなく、GPU デバイスを取得しようとしません。 プロセス 0 は、停止すると GPU インデックス 0 を解放します。 次のプロセス (プロセス 4) には、GPU インデックス 0 が割り当てられます。

詳細については、「[CUDA Pro Tip: Control GPU Visibility with CUDA_VISIBLE_DEVICES](https://developer.nvidia.com/blog/cuda-pro-tip-control-gpu-visibility-cuda_visible_devices/)(CUDA Pro ヒント: CUDA_VISIBLE_DEVICES を使用した GPU 可視性の制御)」を参照してください。

### <a name="parameters-for-creating-the-parallelrunstep"></a>ParallelRunStep を作成するためのパラメーター

スクリプト、環境構成、およびパラメーターを使用して、ParallelRunStep を作成します。 お使いの推論スクリプトの実行の対象としてご自身のワークスペースに関連付けたコンピューティング ターゲットを指定します。 `ParallelRunStep` を使用して、バッチ推論パイプラインのステップを作成します。このステップでは、次のすべてのパラメーターが使用されます。
- `name`:ステップの名前。3 文字以上 32 文字以内で、一意の名前にする必要があります。また、正規表現 ^\[a-z\]([-a-z0-9]*[a-z0-9])?$ を使用できます。
- `parallel_run_config`:`ParallelRunConfig` オブジェクト (前述にて定義)。
- `inputs`:並列処理のためにパーティション分割される 1 つ以上の single 型の Azure Machine Learning データセット。
- `side_inputs`:パーティション分割する必要のないサイド入力として使用される 1 つ以上の参照データまたはデータセット。
- `output`: 出力データが補完されるディレクトリ パスを表す `OutputFileDatasetConfig` オブジェクト。
- `arguments`:ユーザー スクリプトに渡された引数の一覧。 それらを実際のエントリ スクリプト内で取得するには、unknown_args を使用します (省略可能)。
- `allow_reuse`:同じ設定/入力で実行されたときに、ステップで前の結果を再利用するかどうか。 このパラメーターが `False` の場合、パイプラインの実行中、このステップに対して必ず新しい実行が生成されます (省略可能。既定値は `True` です)。

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-scripts-from-remote-context"></a>リモート コンテキストからのスクリプトのデバッグ

スコアリング スクリプトのローカルでのデバッグから実際のパイプラインでのデバッグに切り替えることは、大幅な変更であり、簡単ではありません。 ポータルでのログの検索については、[機械学習パイプラインのリモート コンテキストからのスクリプトのデバッグに関するセクション](how-to-debug-pipelines.md)を参照してください。 そのセクションの情報は、ParallelRunStep にも適用されます。

たとえば、ログファイル `70_driver_log.txt` には、ParallelRunStep コードを起動するコントローラーからの情報が含まれています。

ParallelRunStep ジョブには分散型の性質があるため、複数の異なるソースからのログが存在します。 ただし、概要情報を提供する、統合されたファイルが 2 つ作成されます。

- `~/logs/job_progress_overview.txt`:このファイルでは、これまでに作成されたミニバッチ (タスクとも呼ばれます) の数とこれまでに処理されたミニバッチの数に関する概要が示されます。 最後にはジョブの結果が示されます。 ジョブが失敗した場合は、エラー メッセージのほか、どこからトラブルシューティングすればよいかが示されます。

- `~/logs/sys/master_role.txt`:このファイルでは、実行中のジョブのプリンシパル ノード (オーケストレーターとも呼ばれます) が示されます。 タスクの作成、進行状況の監視、実行結果が含まれます。

EntryScript ヘルパーおよび PRINT ステートメントを使用したエントリ スクリプトから生成されたログは、次のファイルにあります。

- `~/logs/user/entry_script_log/<node_id>/<process_name>.log.txt`:これらのファイルは、EntryScript ヘルパーを使用して entry_script から書き込まれたログです。

- `~/logs/user/stdout/<node_id>/<process_name>.stdout.txt`: これらのファイルは、entry_script の stdout (PRINT ステートメントなど) のログです。

- `~/logs/user/stderr/<node_id>/<process_name>.stderr.txt`:これらのファイルは、entry_script の stderr のログです。

スクリプトのエラーを簡潔に理解するために、次のものがあります。

- `~/logs/user/error.txt`:このファイルは、スクリプトのエラーの概要を作成しようとします。

スクリプトのエラーの詳細については、次のものがあります。

- `~/logs/user/error/`:エントリ スクリプトの読み込み中および実行中にスローされた例外の詳細なスタック トレースを格納します。

各ノードによってスコアリング スクリプトがどのように実行されたかを十分に理解する必要がある場合は、ノードごとの各プロセス ログを確認してください。 プロセス ログは、ワーカー ノード別にグループ化されて `sys/node` フォルダーにあります。

- `~/logs/sys/node/<node_id>/<process_name>.txt`:このファイルは、各ミニバッチがワーカーによって収集または完了される際に、その詳細情報を提供します。 各ミニバッチについて、次の情報が記録されます。

    - ワーカー プロセスの IP アドレスと PID。
    - 項目の合計数、正常に処理された項目数、および失敗した項目数。
    - 開始時刻、期間、処理時間、および実行メソッドの時間。

また、各ノードのリソース使用率の定期的チェックの結果を表示することもできます。 ログ ファイルとセットアップ ファイルは次のフォルダーにあります。

- `~/logs/perf`:秒単位でチェック間隔を変更するには、`--resource_monitor_interval` を設定します。 既定の間隔は `600` で、これは約 10 分です。 監視を停止するには、値を `0` に設定します。 各 `<node_id>` フォルダーには次のものが含まれます。

    - `os/`:ノードで実行されているすべてのプロセスに関する情報。 1 回のチェックでオペレーティング システムのコマンドが実行され、その結果がファイルに保存されます。 Linux では、コマンドは `ps`です。 Windows では、`tasklist` を使用します。
        - `%Y%m%d%H`:サブフォルダー名は、time to hour です。
            - `processes_%M`:ファイルは、チェック時間の分で終了します。
    - `node_disk_usage.csv`:ノードの詳細なディスク使用量。
    - `node_resource_usage.csv`:ノードのリソース使用状況の概要。
    - `processes_resource_usage.csv`:各プロセスのリソース使用状況の概要。

## <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>リモート コンテキストからユーザー スクリプトのログを記録する方法

ParallelRunStep は、process_count_per_node に基づいて、1 つのノードで複数のプロセスを実行できます。 ノード上の各プロセスのログを整理し、PRINT および LOG ステートメントを組み合わせるには、次のように ParallelRunStep ロガーを使用することをお勧めします。 EntryScript からロガーを取得して、ポータルの **logs/user** フォルダーにログが表示されるようにします。

**ロガーを使用したサンプル エントリ スクリプト:**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """Init once in a worker process."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """Call once for a mini batch. Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

## <a name="where-does-the-message-from-python-logging-sink-to"></a>Python の `logging` からのメッセージはどこにシンクされますか。
ParallelRunStep ではルート ロガーにハンドラーが設定され、これによってメッセージは `logs/user/stdout/<node_id>/processNNN.stdout.txt` にシンクされます。

`logging` の既定値は `WARNING` レベルです。 既定では、`INFO` や `DEBUG` のような `WARNING` より低いレベルは表示されません。

## <a name="how-could-i-write-to-a-file-to-show-up-in-the-portal"></a>ポータルに表示するファイルに書き込むにはどうすればよいですか。
`logs` フォルダー内のファイルはアップロードされ、ポータルに表示されます。
下のようにフォルダー `logs/user/entry_script_log/<node_id>` を取得し、書き込むファイル パスを作成できます。

```python
from pathlib import Path
from azureml_user.parallel_run import EntryScript

def init():
    """Init once in a worker process."""
    entry_script = EntryScript()
    log_dir = entry_script.log_dir
    log_dir = Path(entry_script.log_dir)  # logs/user/entry_script_log/<node_id>/.
    log_dir.mkdir(parents=True, exist_ok=True) # Create the folder if not existing.

    proc_name = entry_script.agent_name  # The process name in pattern "processNNN".
    fil_path = log_dir / f"{proc_name}_<file_name>" # Avoid conflicting among worker processes with proc_name.
```

## <a name="how-to-handle-log-in-new-processes"></a>新しいプロセス内でログを処理する方法
[`subprocess`](https://docs.python.org/3/library/subprocess.html) モジュールを使用して、ご自身のエントリ スクリプト内で新しいプロセスを生成し、その入力/出力/エラー パイプに接続して、リターン コードを取得することができます。

推奨されるアプローチは、[`run()`](https://docs.python.org/3/library/subprocess.html#subprocess.run) 関数を使用して `capture_output=True` を指定することです。 エラーは `logs/user/error/<node_id>/<process_name>.txt` に表示されます。

`Popen()` を使用する場合は、次のように、stdout/stderr をファイルにリダイレクトする必要があります。
```python
from pathlib import Path
from subprocess import Popen

from azureml_user.parallel_run import EntryScript


def init():
    """Show how to redirect stdout/stderr to files in logs/user/entry_script_log/<node_id>/."""
    entry_script = EntryScript()
    proc_name = entry_script.agent_name  # The process name in pattern "processNNN".
    log_dir = Path(entry_script.log_dir)  # logs/user/entry_script_log/<node_id>/.
    log_dir.mkdir(parents=True, exist_ok=True) # Create the folder if not existing.
    stdout_file = str(log_dir / f"{proc_name}_demo_stdout.txt")
    stderr_file = str(log_dir / f"{proc_name}_demo_stderr.txt")
    proc = Popen(
        ["...")],
        stdout=open(stdout_file, "w"),
        stderr=open(stderr_file, "w"),
        # ...
    )

```

> [!NOTE]
> ワーカー プロセスによって、"system" コードとエントリ スクリプト コードが同じプロセスで実行されます。
>
> `stdout` および `stderr` が指定されていない場合、ワーカー プロセスの設定は、ご自身のエントリ スクリプト内で `Popen()` によって作成されたサブプロセスによって継承されます。
>
> `stdout` では `logs/sys/node/<node_id>/processNNN.stdout.txt` に、`stderr` では `logs/sys/node/<node_id>/processNNN.stderr.txt` に書き込みます。


## <a name="how-do-i-write-a-file-to-the-output-directory-and-then-view-it-in-the-portal"></a>ファイルを出力ディレクトリに書き込んで、それをポータルで表示するにはどうすればよいですか。

出力ディレクトリを `EntryScript` クラスから取得して、そこに書き込みます。 書き込まれたファイルを表示するには、Azure Machine Learning ポータルの実行ビュー手順で、 **[出力 + ログ]** タブを選択します。 **[Data outputs]\(データ出力\)** リンクを選択し、ダイアログで説明されている手順を完了します。

`EntryScript` をエントリ スクリプトで、次の例のように使用します。

```python
from pathlib import Path
from azureml_user.parallel_run import EntryScript

def run(mini_batch):
    output_dir = Path(entry_script.output_dir)
    (Path(output_dir) / res1).write...
    (Path(output_dir) / res2).write...
```

## <a name="how-can-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>参照テーブルを含むファイルなどのサイド入力をすべてのワーカーに渡す方法はありますか。

ユーザーは、ParalleRunStep の side_inputs パラメーターを使用して、参照データをスクリプトに渡すことができます。 side_inputs として提供されるすべてのデータセットは、各ワーカー ノードにマウントされます。 ユーザーは引数を渡すことによって、マウントの場所を取得できます。

参照データが含まれる[データセット](/python/api/azureml-core/azureml.core.dataset.dataset)を作成し、ローカル マウント パスを指定して、それをワークスペースに登録します。 これを `ParallelRunStep` の `side_inputs` パラメーターに渡します。 また、`arguments` セクションにそのパスを追加して、マウントされたパスに簡単にアクセスすることもできます。

> [!NOTE]
> FileDatasets を使用するのは side_inputs だけにしてください。

```python
local_path = "/tmp/{}".format(str(uuid.uuid4()))
label_config = label_ds.as_named_input("labels_input").as_mount(local_path)
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

その後、次のように、推論スクリプト (init() メソッドなど) でアクセスできるようになります。

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

## <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>サービス プリンシパルの認証での入力データセットの使用方法
ユーザーは、ワークスペースで使用されるサービス プリンシパルの認証で入力データセットを渡すことができます。 ParallelRunStep でこのようなデータセットを使用する場合、ParallelRunStep 構成を構築するためにデータセットが登録されている必要があります。

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")

ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )

default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***')
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="how-to-check-progress-and-analyze-it"></a>進行状況を調べてそれを分析する方法
このセクションでは、ParallelRunStep ジョブの進行状況を調べて、予期しない動作の原因を調べる方法について説明します。

### <a name="how-to-check-job-progress"></a>ジョブの進行状況を調べる方法
StepRun の全体的な状態を調べる以外に、スケジュールまたは処理されたミニバッチの数と出力生成の進行状況を `~/logs/job_progress_overview.<timestamp>.txt` で確認できます。 ファイルは毎日ローテーションされます。タイムスタンプが最も大きいファイルで最新情報を確認できます。

### <a name="what-should-i-check-if-there-is-no-progress-for-a-while"></a>進展がしばらくの間ない場合、何を調べたらよいか
`~/logs/sys/errror` に移動して例外がないか調べます。 何もない場合は、エントリ スクリプトに時間がかかっている可能性があります。コードで進行状況情報を出力して時間のかかる部分を見つけるか、`"--profiling_module", "cProfile"` を `ParallelRunStep` の `arguments` に追加して、`<process_name>.profile` という名前のプロファイル ファイルを `~/logs/sys/node/<node_id>` フォルダーの下に生成することができます。

### <a name="when-will-a-job-stop"></a>ジョブが停止するのはいつか
キャンセルしていない場合、ジョブは次の状態で停止します。
- 完了。 すべてのミニバッチが処理されて、出力が `append_row` モードで生成されている場合。
- 失敗。 [`Parameters for ParallelRunConfig`](#parameters-for-parallelrunconfig) の `error_threshold` を超えている場合、あるいはシステム エラーがジョブ中に発生した場合。

### <a name="where-to-find-the-root-cause-of-failure"></a>エラーの根本原因をどこで探すか
`~logs/job_result.txt` の情報をたどると、原因と詳細なエラー ログが見つかります。

### <a name="will-node-failure-impact-the-job-result"></a>ノードのエラーがジョブの結果に影響するか
指定されたコンピューティング クラスターに他の使用可能なノードがある場合は、影響しません。 オーケストレーターが新しいノードを代わりとして開始し、ParallelRunStep はそうした操作に対する回復性を備えています。

### <a name="what-happens-if-init-function-in-entry-script-fails"></a>エントリ スクリプトの `init` 関数が失敗するとどうなりますか
ParallelRunStep には、ジョブのエラーをあまり長い時間遅らせずに、一時的な問題から復旧する機会を与えるために一定の回数再試行するメカニズムがあります。そのメカニズムを以下に示します。
1. ノードが起動した後、`init` がすべてのエージェントで失敗し続ける場合は、`3 * process_count_per_node` 回のエラーの後に試行を停止します。
2. ジョブの開始後、すべてのノードのすべてのエージェントで `init` が失敗し続ける場合、ジョブの実行時間が 2 分を超えて `2 * node_count * process_count_per_node` 回のエラーが発生した場合は試行を停止します。
3. すべてのエージェントが `init` で `3 * run_invocation_timeout + 30` 秒を超えてスタックしている場合、進展がない時間が長すぎるため、ジョブは失敗します。

### <a name="what-will-happen-on-outofmemory-how-can-i-check-the-cause"></a>OutOfMemory で何が起こりますか。 どうしたら原因を調べることができますか?
ParallelRunStep が、ミニバッチを処理する現在の試行をエラー状態に設定し、失敗したプロセスを再開しようと試みます。 `~logs/perf/<node_id>` を調べると、メモリを消費しているプロセスがわかります。

### <a name="why-do-i-have-a-lot-of-processnnn-files"></a>processNNN ファイルがたくさんあるのはなぜですか?
ParallelRunStep が、新しいワーカー プロセスを異常終了したもの代わりに開始し、各プロセスで `processNNN` ファイルがログとして生成されます。 ただし、ユーザー スクリプトの `init` 関数中の例外が理由でプロセスが失敗し、エラーが `3 * process_count_per_node` 回続けて繰り返す場合、新しいワーカー プロセスは開始されません。

## <a name="next-steps"></a>次のステップ

* [Azure Machine Learning パイプラインを示す Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines) に関するページを参照してください。

* [azureml-pipeline-steps](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) パッケージについては、SDK リファレンスを参照してください。

* ParallelRunConfig クラスのリファレンス [ドキュメント](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig)と ParallelRunStep クラスの[ドキュメント](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep)を参照してください。

* ParallelRunStep でパイプラインを使用するには、[高度なチュートリアル](tutorial-pipeline-batch-scoring-classification.md)に従ってください。 このチュートリアルでは、別のファイルをサイド入力として渡す方法について説明しています。
