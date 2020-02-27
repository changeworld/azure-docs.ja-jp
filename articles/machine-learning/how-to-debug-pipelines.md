---
title: 機械学習パイプラインのデバッグとトラブルシューティング
titleSuffix: Azure Machine Learning
description: Azure Machine Learning SDK for Python で機械学習パイプラインのデバッグとトラブルシューティングを行います。 パイプラインの開発における陥りやすい落とし穴と、リモートからの実行前および実行中にスクリプトをデバッグするためのヒントについて説明します。 Visual Studio Code を使用して、機械学習パイプラインを対話的にデバッグする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 12/12/2019
ms.openlocfilehash: 0080b64e16b979b32aa5a91f9ee497e5f9ec47fb
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485371"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>機械学習パイプラインのデバッグとトラブルシューティング
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、[Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) および [Azure Machine Learning デザイナー (プレビュー)](https://docs.microsoft.com/azure/machine-learning/concept-designer) で[機械学習パイプライン](concept-ml-pipelines.md)をデバッグしてトラブルシューティングする方法について説明します。 次の方法に関する情報を提供します。

* Azure Machine Learning SDK を使用してデバッグする
* Azure Machine Learning デザイナーを使用してデバッグする
* Application Insights を使用してデバッグする
* Visual Studio Code (VS Code) と Python Tools for Visual Studio (PTVSD) を使用して対話形式でデバッグする

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK でのデバッグとトラブルシューティング
以降のセクションでは、パイプラインの構築時に陥りやすい落とし穴と、パイプラインで実行されているコードをデバッグするためのさまざまな方法の概要について説明します。 パイプラインが予期したとおりに実行されない場合は、次のヒントを参考にしてください。

### <a name="testing-scripts-locally"></a>スクリプトのローカルでのテスト

パイプラインにおける最も一般的なエラーの 1 つは、アタッチされたスクリプト (データ クレンジング スクリプト、スコアリング スクリプトなど) が意図したとおりに実行されないこと、またはスクリプトに Azure Machine Learning Studio のワークスペースでデバッグが困難なリモート コンピューティング コンテキストの実行時エラーが含まれていることです。 

パイプライン自体をローカル環境で実行することはできませんが、ローカル コンピューター上の分離環境でスクリプトを実行すると、コンピューティングと環境のビルド プロセスを待つ必要がないため、デバッグが速くなります。 これを行うには、いくつかの開発作業が必要です。

* データがクラウド データストアにある場合は、データをダウンロードして、スクリプトで使用できるようにする必要があります。 小さなデータのサンプルの使用は、実行時間を短縮して、スクリプトの動作に関するフィードバックをすぐに得るために有効な方法です
* 中間パイプラインの手順をシミュレートしようとしている場合は、特定のスクリプトが前の手順から想定しているオブジェクトの種類を手動で構築しなければならない場合があります
* また、独自の環境を定義し、リモート コンピューティング環境で定義されている依存関係をレプリケートする必要もあります

ローカル環境で実行するスクリプトのセットアップが完了すると、次のようなデバッグ タスクの実行がはるかに容易になります。

* カスタム デバッグ構成のアタッチ
* 実行の停止とオブジェクトの状態の確認
* 実行時まで確認できない型や論理エラーの把握

> [!TIP] 
> スクリプトが想定どおりに実行されることを確認したら、次の手順として、複数ステップのパイプラインで実行する前に、単一ステップのパイプラインでスクリプトを実行することをお勧めします。

### <a name="debugging-scripts-from-remote-context"></a>リモート コンテキストからのスクリプトのデバッグ

スクリプトのローカルでのテストは、パイプラインの構築を開始する前に主なコード フラグメントや複雑なロジックをデバッグするための優れた方法です。ただし、特にパイプラインのステップ間の相互作用中に診断動作が生じる場合には、ある時点で実際のパイプライン自体の実行中にスクリプトをデバッグすることが必要になる可能性が高いです。 JavaScript コードをデバッグする場合と同様に、リモート実行時にオブジェクトの状態と予期される値を確認できるように、ステップ スクリプトで `print()` ステートメントを適宜使用することをお勧めします。

ログ ファイル `70_driver_log.txt` には、以下が含まれています。 

* スクリプトの実行中に出力されたすべてのステートメント
* スクリプトのスタック トレース 

ポータルでこのログ ファイルとその他のログ ファイルを見つけるには、まずワークスペースでパイプラインの実行をクリックします。

![パイプラインの実行の一覧ページ](./media/how-to-debug-pipelines/pipelinerun-01.png)

パイプラインの実行の詳細ページに移動します。

![パイプラインの実行の詳細ページ](./media/how-to-debug-pipelines/pipelinerun-02.png)

特定のステップのモジュールをクリックします。 **[ログ]** タブに移動します。その他のログには、環境のイメージ ビルド プロセスやステップ準備スクリプトに関する情報が含まれます。

![パイプラインの実行の詳細ページの [ログ] タブ](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> "*発行されたパイプライン*" の実行は、ワークスペースの **[エンドポイント]** タブにあります。 "*発行されていないパイプライン*" の実行は、 **[Experiments]\(実験\)** または **[パイプライン]** にあります。

### <a name="troubleshooting-tips"></a>トラブルシューティングのヒント

次の表に、パイプライン開発時の一般的な問題と、考えられる解決策を示します。

| 問題 | 考えられる解決策 |
|--|--|
| `PipelineData` ディレクトリにデータを渡せない | パイプラインがステップの出力データを想定する場所に、スクリプトでディレクトリを作成したことを確認してください。 ほとんどの場合、入力引数によって出力ディレクトリが定義されます。ディレクトリを明示的に作成してください。 出力ディレクトリを作成するには、`os.makedirs(args.output_dir, exist_ok=True)` を使用します。 この設計パターンを示すスコアリング スクリプトの例については、[こちらのチュートリアル](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script)を参照してください。 |
| 依存関係のバグ | スクリプトをローカルで開発してテスト済みであるのに、パイプラインのリモート コンピューティングでの実行時に依存関係の問題が見つかった場合は、コンピューティング環境の依存関係とバージョンがテスト環境と一致していることを確認してください。 |
| コンピューティング ターゲットでのあいまいなエラー | コンピューティング ターゲットを削除して再作成すると、コンピューティング ターゲットでの特定の問題を解決できます。 |
| ステップを再利用しないパイプライン | ステップの再利用は既定で有効になっていますが、パイプライン ステップで無効にしていないか確認してください。 再利用が無効になっている場合は、ステップの `allow_reuse` パラメーターが `False` に設定されます。 |
| パイプラインが不必要に再実行される | 基になるデータまたはスクリプトが変更されたときにのみステップが再実行されるようにするには、各ステップのディレクトリを分離します。 複数のステップに同じソース ディレクトリを使用すると、不要に再実行される可能性があります。 パイプライン ステップ オブジェクトで `source_directory` パラメーターを使用して、そのステップの分離されたディレクトリを指定し、複数のステップで同じ `source_directory` パスを使用しないようにします。 |

### <a name="logging-options-and-behavior"></a>ログ オプションと動作

次の表は、パイプラインのさまざまなデバッグ オプションに関する情報を示しています。 これは完全なリストではありません。ここに示されている Azure Machine Learning、Python、OpenCensus のオプションの他にもオプションはあります。

| ライブラリ                    | Type   | 例                                                          | 宛先                                  | リソース                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | メトリック | `run.log(name, val)`                                             | Azure Machine Learning ポータル UI             | [実験を追跡する方法](how-to-track-experiments.md#available-metrics-to-track)<br>[azureml.core.Run クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python の印刷とログ    | ログ    | `print(val)`<br>`logging.info(message)`                          | ドライバー ログ、Azure Machine Learning デザイナー | [実験を追跡する方法](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Python のログ](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | ログ    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights - トレース                | [Application Insights でパイプラインをデバッグする](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor エクスポーター](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python ログのクックブック](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>ログ オプションの例

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Azure Machine Learning デザイナー (プレビュー) でのデバッグとトラブルシューティング

このセクションでは、デザイナーでパイプラインをトラブルシューティングする方法の概要について説明します。
デザイナーで作成されたパイプラインの場合、作成ページまたはパイプラインの実行の詳細ページに**ログ ファイル**があります。

### <a name="access-logs-from-the-authoring-page"></a>作成ページからログにアクセスする

パイプラインの実行を送信し、作成ページを表示したままにした場合、モジュールごとに生成されたログ ファイルを確認できます。

1. 作成キャンバスで任意のモジュールを選択します。
1. プロパティ ペインで、 **[ログ]** タブに移動します。
1. ログ ファイル `70_driver_log.txt` を選択します

    ![作成ページのモジュールのログ](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>パイプラインの実行からログにアクセスする

パイプラインの実行の詳細ページの **[パイプライン]** または **[Experiments]\(実験\)** セクションにも特定の実行のログ ファイルがあります。

1. デザイナーで作成されたパイプラインの実行を選択します。
    ![パイプラインの実行ページ](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. プレビュー ペインで任意のモジュールを選択します。
1. プロパティ ペインで、 **[ログ]** タブに移動します。
1. ログ ファイル `70_driver_log.txt` を選択します

## <a name="debug-and-troubleshoot-in-application-insights"></a>Application Insights のデバッグとトラブルシューティング
この方法で OpenCensus Python ライブラリを使用する方法の詳細については、次のガイドを参照してください。[Application Insights での機械学習パイプラインのデバッグとトラブルシューティング](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Visual Studio Code でのデバッグとトラブルシューティング

場合によっては、ML パイプラインで使用される Python コードを対話的にデバッグする必要が生じることがあります。 Visual Studio Code (VS Code) と Python Tools for Visual Studio (PTVSD) を使用することによって、トレーニング環境で実行されるコードにアタッチできます。

### <a name="prerequisites"></a>前提条件

* __Azure Virtual Network__ を使用するように構成された __Azure Machine Learning ワークスペース__。
* パイプライン ステップの一部として Python スクリプトを使用する __Azure Machine Learning パイプライン__。 たとえば、PythonScriptStep です。
* Azure Machine Learning コンピューティング クラスター。これは __仮想ネットワーク内__ にあり、__パイプラインによってトレーニングのために使用されます__。
* __仮想ネットワーク内__ に存在する __開発環境__。 開発環境は、次のいずれかになります。

    * 仮想ネットワーク内の Azure 仮想マシン
    * 仮想ネットワーク内の Notebook VM のコンピューティング インスタンス
    * 仮想プライベート ネットワーク (VPN) によって仮想ネットワークに接続されたクライアント コンピューター。

Azure Machine Learning で Azure Virtual Network を使用する方法の詳細については、「[Azure Virtual Network 内で Azure ML の実験と推論のジョブを安全に実行する](how-to-enable-virtual-network.md)」を参照してください。

### <a name="how-it-works"></a>しくみ

ML パイプライン ステップでは、Python スクリプトが実行されます。 これらのスクリプトは、次の操作を実行するために変更されています。
    
1. それらが実行されるホストの IP アドレスをログに記録します。 この IP アドレスを使用して、デバッガーをスクリプトに接続します。

2. PTVSD デバッグ コンポーネントを起動し、デバッガーが接続するのを待ちます。

3. 開発環境から、トレーニング プロセスによって作成されたログを監視して、スクリプトが実行されている IP アドレスを確認します。

4. `launch.json` ファイルを使用して、デバッガーを接続するための IP アドレスを VS Code に示します。

5. デバッガーをアタッチし、スクリプトを対話的にステップ実行します。

### <a name="configure-python-scripts"></a>Python スクリプトを構成する

デバッグを有効にするには、ML パイプライン ステップで使用される Python スクリプトに次の変更を加えます。

1. 次の import ステートメントを追加します。

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. 次の引数を追加します。 これらの引数を使用すると、必要に応じてデバッガーを有効にし、デバッガーをアタッチするためのタイムアウトを設定できます。

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. 次のステートメントを追加します。 これらのステートメントは現在の実行コンテキストを読み込んで、コードが実行されているノードの IP アドレスをログに記録できるようにします。

    ```python
    global run
    run = Run.get_context()
    ```

1. PTVSD を起動してデバッガーがアタッチされるまで待機する `if` ステートメントを追加します。 タイムアウト前にデバッガーがアタッチされない場合、スクリプトは通常どおり続行されます。

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        ip = socket.gethostbyname(socket.gethostname())
        print(f'ip_address: {ip}')
        ptvsd.enable_attach(address=('0.0.0.0', 5678),
                            redirect_output=True)
        # Wait for the timeout for debugger to attach
        ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
        print(f'Debugger attached = {ptvsd.is_attached()}')
    ```

次の Python の例は、デバッグを有効にする基本的な `train.py` ファイルを示しています。

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import ptvsd
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    ip = socket.gethostbyname(socket.gethostname())
    print(f'ip_address: {ip}')
    ptvsd.enable_attach(address=('0.0.0.0', 5678),
                        redirect_output=True)
    # Wait for the timeout for debugger to attach
    ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
    print(f'Debugger attached = {ptvsd.is_attached()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>ML パイプラインを構成する

PTVSD を起動して実行コンテキストを取得するために必要な Python パッケージを提供するには、[環境]()を作成して `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']` を設定します。 使用しているものと一致するように SDK のバージョンを変更します。 次のコード スニペットでは、環境を作成する方法を示します。

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['ptvsd', 'azureml-sdk==1.0.83'])
```

「[Python スクリプトを構成する](#configure-python-scripts)」セクションでは、ML パイプライン ステップで使用されるスクリプトに 2 つの新しい引数を追加しました。 次のコード スニペットは、これらの引数を使用して、コンポーネントのデバッグを有効にし、タイムアウトを設定する方法を示しています。 また、`runconfig=run_config` を設定して、前の手順で作成した環境を使用する方法も示しています。

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300],
                         compute_target=aml_compute, 
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

パイプラインを実行すると、各ステップで子実行が作成されます。 デバッグが有効になっている場合は、変更後のスクリプトによって、子実行に対して `70_driver_log.txt` に次のテキストのような情報がログに記録されます。

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

`ip_address` の値を保存します。 次のセクションで使用します。

> [!TIP]
> IP アドレスは、このパイプライン ステップの子実行に対する実行ログからも見つけることができます。 この情報を表示する方法の詳細については、「[Azure ML の実験の実行とメトリックを監視する](how-to-track-experiments.md)」を参照してください。

### <a name="configure-development-environment"></a>開発環境の設定

1. VS Code 開発環境に Python Tools for Visual Studio (PTVSD) をインストールするには、次のコマンドを使用します。

    ```
    python -m pip install --upgrade ptvsd
    ```

    VS Code で PTVSD を使用する方法の詳細については、「[Remote Debugging (リモート デバッグ)](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)」を参照してください。

1. デバッガーを実行している Azure Machine Learning コンピューティングと通信するように VS Code を構成するには、新しいデバッグ構成を作成します。

    1. VS Code から __[デバッグ]__ メニューを選択し、 __[構成を開く]__ を選択します。 __launch.json__ という名前のファイルが開きます。

    1. __launch.json__ ファイルで、`"configurations": [` を含む行を見つけ、その後に次のテキストを挿入します。 `"host": "10.3.0.5"` エントリを、前のセクションのログで返された IP アドレスに変更します。 `"localRoot": "${workspaceFolder}/code/step"` エントリを、デバッグ対象のスクリプトのコピーが格納されているローカル ディレクトリに変更します。

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "10.3.0.5",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > 構成セクションに既に他のエントリがある場合は、挿入したコードの後にコンマ (,) を追加します。

        > [!TIP]
        > ベスト プラクティスとして、スクリプトのリソースを別のディレクトリに保持することをお勧めします。`localRoot` のサンプル値が `/code/step1` を参照しているのはこのためです。
        >
        > 複数のスクリプトをデバッグする場合は、別々のディレクトリで、スクリプトごとに個別の構成セクションを作成します。

    1. __launch.json__ ファイルを保存します。

### <a name="connect-the-debugger"></a>デバッガーを接続する

1. VS Code を開き、スクリプトのローカル コピーを開きます。
2. アタッチしたときにスクリプトを停止するブレークポイントを設定します。
3. 子プロセスでスクリプトが実行されていて、`Timeout for debug connection` がログに表示されている間に、F5 キーを使用するか、 __[デバッグ]__ を選択します。 メッセージが表示されたら、 __[Azure Machine Learning Compute: remote debug]\(Azure Machine Learning コンピューティング: リモート デバッグ\)__ 構成を選択します。 サイドバーからデバッグ アイコンを選択し、[デバッグ] ドロップダウン メニューから __[Azure Machine Learning: remote debug]\(Azure Machine Learning: リモートデバッグ\)__ エントリを選択し、緑色の矢印を使用してデバッガーをアタッチすることもできます。

    この時点で、VS Code は計算ノードの PTVSD に接続し、前に設定したブレークポイントで停止します。 これで、実行時のようにコードをステップ実行したり、変数を表示したりできます。

    > [!NOTE]
    > ログに `Debugger attached = False` を示すエントリが表示された場合、タイムアウトに達しており、スクリプトはデバッガーなしで続行されます。 `Timeout for debug connection` メッセージの後、タイムアウトに達する前に、パイプラインをもう一度送信してデバッガーを接続します。

## <a name="next-steps"></a>次のステップ

* [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) パッケージおよび [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) パッケージについては、SDK リファレンスを参照してください。

* [デザイナーの例外とエラー コード](algorithm-module-reference/designer-error-codes.md)の一覧を参照してください。
