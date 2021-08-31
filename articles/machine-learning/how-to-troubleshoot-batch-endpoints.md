---
title: バッチ エンドポイントのトラブルシューティング (プレビュー)
titleSuffix: Azure Machine Learning
description: バッチ エンドポイントの問題解決に役立つヒント。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting, devplatv2
ms.reviewer: laobri
ms.author: tracych
author: tracych
ms.date: 05/05/2021
ms.openlocfilehash: 9fbbc2af3681b6f91ec3c1e0f6adfd46a8598c17
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114448315"
---
# <a name="troubleshooting-batch-endpoints-preview"></a>バッチ エンドポイントのトラブルシューティング (プレビュー)

[バッチ エンドポイント](how-to-use-batch-endpoint.md) (プレビュー) を使用したバッチ スコアリングでよく発生するエラーをトラブルシューティングまたは回避する方法について説明します。

 [!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

次の表には、バッチ エンドポイントを開発、利用する過程で遭遇する可能性のある一般的な問題と解決策が示されています。

| 問題 | 考えられる解決策 |
|--|--|
| コードの構成または環境がない。 | MLflow 以外のモデルを使用する場合は、スコアリング スクリプトと環境の定義を必ず指定してください。 ノー コード デプロイがサポートされるのは MLflow モデルのみです。 詳細については、[MLflow と Azure Machine Learning を使用して ML モデルを追跡する](how-to-use-mlflow.md)方法に関するページを参照してください|
| 既存のバッチ エンドポイントのモデル、コード、環境、コンピューティングの更新に失敗する。 | 新しい名前で新しいバッチ エンドポイントを作成します。 既存のバッチ エンドポイントに関してこれらのアセットを更新する機能は、まだサポートされていません。 |
| リソースが見つかりませんでした。 | CLI コマンドで `--type batch` を使用していることを確認します。 この引数を指定しなかった場合、既定の `online` タイプが使用されます。|
| サポートされない入力データ。 | バッチ エンドポイントに渡すことができる入力データの形式は、1) 登録データ、2) クラウドのデータ、3) ローカルのデータの 3 つです。 適切な形式を使用していることを確認してください。 詳細については、「[バッチ エンドポイント (プレビュー) を使用したバッチ スコアリング](how-to-use-batch-endpoint.md)」を参照してください|
| 指定されたエンドポイント名が存在するか、削除中である。 | 新しい名前で新しいバッチ エンドポイントを作成します。 `endpoint delete` コマンドは、エンドポイントを削除対象としてマークします。 同じ名前を再利用して、同じリージョンに新しいエンドポイントを作成することはできません。 |
| 出力が既に存在する。 | 独自の出力場所を構成した場合、エンドポイントを呼び出すたびに、必ず新しい出力を指定してください。 |

##  <a name="scoring-script-requirements"></a>スコアリング スクリプトの要件

MLflow 以外のモデルを使用している場合、スコアリング スクリプトを指定する必要があります。 スコアリング スクリプトには 2 つの関数が必要です。

- `init()`:この関数は、後で推論するためのコストのかかる準備、または一般的な準備を行うときに使用します。 たとえば、これを使って、モデルをグローバル オブジェクトに読み込みます。 この関数は、プロセスの開始時に 1 回だけ呼び出されます。
-  `run(mini_batch)`: この関数は、`mini_batch` インスタンスごとに実行されます。
    -  `mini_batch`: `mini_batch` の値は、ファイル パスのリストです。
    -  `response`: `run()` メソッドは、Pandas の DataFrame または配列を返す必要があります。 その返された要素は、共通の出力ファイルに追加されます。 返された出力要素はそれぞれ、入力ミニバッチ内で成功した 1 つの入力要素の実行を示します。 1 つの入力を実行出力結果にマップできるだけの十分なデータが実行結果に含まれていることを確認してください。 実行の出力は出力ファイルに書き込まれますが、順序どおりの書き込みは保証されません。出力でいずれかのキーを使って、正しい入力にマップする必要があります。

:::code language="python" source="~/azureml-examples-cli-preview/cli/endpoints/batch/mnist/code/digit_identification.py" :::

## <a name="understanding-logs-of-a-batch-scoring-job"></a>バッチ スコアリング ジョブのログについて

### <a name="get-logs"></a>ログを取得する

Azure CLI または REST を使用してバッチ エンドポイントを呼び出した後、バッチ スコアリング ジョブが非同期的に実行されます。 バッチ スコアリング ジョブのログは、2 とおりの方法で取得できます。

方法 1: ログをローカル コンソールにストリーム配信する

次のコマンドを実行すると、システムによって生成されたログをコンソールにストリーム配信できます。 ストリーム配信されるのは、`azureml-logs` フォルダー内のログだけです。

```bash
az ml job stream -name <job_name>
```

方法 2: スタジオでログを表示する 

スタジオで実行するためのリンクを取得するには、次を実行します。 

```azurecli
az ml job show --name <job_name> --query interaction_endpoints.Studio.endpoint -o tsv
```

1. スタジオで、上のコマンドから返された値を使用してジョブを開きます。 
1. **[batchscoring]\(バッチ スコアリング\)** を選択します
1. **[出力 + ログ]** タブを開きます 
1. 確認するログを選択します

### <a name="understand-log-structure"></a>ログの構造について

最上位のログ フォルダーには、`azureml-logs` と `logs` の 2 つのフォルダーがあります。 

`~/azureml-logs/70_driver_log.txt` ファイルには、スコアリング スクリプトを起動するコントローラーからの情報が含まれています。  

バッチ スコアリング ジョブには分散型の性質があるため、複数の異なるソースからのログが存在します。 ただし、概要情報を提供する、結合されたファイルが 2 つ作成されます。 

- `~/logs/job_progress_overview.txt`: このファイルでは、これまでに作成されたミニバッチ (タスクとも呼ばれます) の数とこれまでに処理されたミニバッチの数に関する概要が示されます。 ミニバッチが終了すると、ログにジョブの結果が記録されます。 ジョブが失敗した場合は、エラー メッセージのほか、どこからトラブルシューティングすればよいかが示されます。

- `~/logs/sys/master_role.txt`:このファイルでは、実行中のジョブのプリンシパル ノード (オーケストレーターとも呼ばれます) が示されます。 このログは、タスクの作成、進行状況の監視、実行結果に関する情報を提供します。

スクリプトのエラーを簡潔に理解するために、次のものがあります。

- `~/logs/user/error.txt`:このファイルは、スクリプトのエラーの概要を作成しようとします。

スクリプトのエラーの詳細については、次のものがあります。

- `~/logs/user/error/`: このファイルは、エントリ スクリプトの読み込み中および実行中にスローされた例外の詳細なスタック トレースを格納します。

各ノードによってスコアリング スクリプトがどのように実行されたかを十分に理解する必要がある場合は、ノードごとの各プロセス ログを確認してください。 プロセス ログは、ワーカー ノード別にグループ化されて `sys/node` フォルダーにあります。

- `~/logs/sys/node/<ip_address>/<process_name>.txt`:このファイルは、各ミニバッチがワーカーによって収集または完了される際に、その詳細情報を提供します。 各ミニバッチについて、次の情報が記録されます。

    - ワーカー プロセスの IP アドレスと PID。 
    - 項目の総数、正常に処理された項目の数、および失敗した項目の数。
    - 開始時刻、期間、処理時間、および実行メソッドの時間。

また、各ノードのリソース使用率の定期的チェックの結果を確認することもできます。 ログ ファイルとセットアップ ファイルは次のフォルダーにあります。

- `~/logs/perf`:秒単位でチェック間隔を変更するには、`--resource_monitor_interval` を設定します。 既定の間隔は `600` で、これは約 10 分です。 監視を停止するには、値を `0` に設定します。 各 `<ip_address>` フォルダーには次のものが含まれます。

    - `os/`:ノードで実行されているすべてのプロセスに関する情報。 1 回のチェックでオペレーティング システムのコマンドが実行され、その結果がファイルに保存されます。 Linux では、コマンドは `ps`です。
        - `%Y%m%d%H`:サブフォルダー名は、time to hour です。
            - `processes_%M`:ファイルは、チェック時間の分で終了します。
    - `node_disk_usage.csv`:ノードの詳細なディスク使用量。
    - `node_resource_usage.csv`:ノードのリソース使用状況の概要。
    - `processes_resource_usage.csv`:各プロセスのリソース使用状況の概要。

### <a name="how-to-log-in-scoring-script"></a>スコアリング スクリプトにログインする方法

スコアリング スクリプトで Python ログを使用できます。 ログは、`logs/user/stdout/<node_id>/processNNN.stdout.txt` に保存されます。 

```python
import argparse
import logging

# Get logging_level
arg_parser = argparse.ArgumentParser(description="Argument parser.")
arg_parser.add_argument("--logging_level", type=str, help="logging level")
args, unknown_args = arg_parser.parse_known_args()
print(args.logging_level)

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.logging_level.upper())
logger.info("Info log statement")
logger.debug("Debug log statement")
```