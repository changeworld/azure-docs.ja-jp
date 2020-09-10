---
title: ML パイプラインのデバッグとトラブルシューティング
titleSuffix: Azure Machine Learning
description: Python で Azure Machine Learning パイプラインをデバッグする パイプラインの開発における陥りやすい落とし穴と、リモートからの実行前および実行中にスクリプトをデバッグするためのヒントについて説明します。 Visual Studio Code を使用して、機械学習パイプラインを対話的にデバッグする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: troubleshooting, devx-track-python
ms.openlocfilehash: 0f051e5b5711cec9fd8e72ec2b84c18f80430a0a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018061"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>機械学習パイプラインのデバッグとトラブルシューティング
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、[Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) および [Azure Machine Learning デザイナー (プレビュー)](https://docs.microsoft.com/azure/machine-learning/concept-designer) で[機械学習パイプライン](concept-ml-pipelines.md)をトラブルシューティングおよびデバッグする方法について説明します。 

## <a name="troubleshooting-tips"></a>トラブルシューティングのヒント

次の表に、パイプライン開発時の一般的な問題と、考えられる解決策を示します。

| 問題 | 考えられる解決策 |
|--|--|
| `PipelineData` ディレクトリにデータを渡せない | パイプラインがステップの出力データを想定する場所に、スクリプトでディレクトリを作成したことを確認してください。 ほとんどの場合、入力引数によって出力ディレクトリが定義されます。ディレクトリを明示的に作成してください。 出力ディレクトリを作成するには、`os.makedirs(args.output_dir, exist_ok=True)` を使用します。 この設計パターンを示すスコアリング スクリプトの例については、[こちらのチュートリアル](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script)を参照してください。 |
| 依存関係のバグ | ローカルでのテスト時には発生しなかった依存関係エラーがリモート パイプラインで発生する場合は、リモート環境の依存関係とバージョンがテスト環境のものと一致していることを確認します。 (「[環境のビルド、キャッシュ、再利用](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)」を参照してください。)|
| コンピューティング ターゲットでのあいまいなエラー | コンピューティング先を削除してから再作成してみてください。 コンピューティング先は簡単に再作成でき、いくつかの一時的な問題を解決できます。 |
| ステップを再利用しないパイプライン | ステップの再利用は既定で有効になっていますが、パイプライン ステップで無効にしていないか確認してください。 再利用が無効になっている場合は、ステップの `allow_reuse` パラメーターが `False` に設定されます。 |
| パイプラインが不必要に再実行される | 基になるデータまたはスクリプトが変更されたときにのみステップが再実行されるようにするには、各ステップのソース コード ディレクトリを分離します。 複数のステップに同じソース ディレクトリを使用すると、不要に再実行される可能性があります。 パイプライン ステップ オブジェクトで `source_directory` パラメーターを使用して、そのステップの分離されたディレクトリを指定し、複数のステップで同じ `source_directory` パスを使用しないようにします。 |


## <a name="debugging-techniques"></a>デバッグ手法

パイプラインのデバッグには 3 つの主要な手法があります。 

* ローカル コンピューター上で個々のパイプライン ステップをデバッグする
* ログと Application Insights を使用して問題の原因を分離し、診断する
* Azure で実行しているパイプラインにリモート デバッガーをアタッチする

### <a name="debug-scripts-locally"></a>スクリプトをローカルでデバッグする

パイプラインにおける最も一般的なエラーの 1 つは、ドメイン スクリプトが意図したとおりに実行されないこと、またはリモート コンピューティングのコンテキストにデバッグが困難なランタイム エラーが含まれていることです。

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

## <a name="configure-write-to-and-review-pipeline-logs"></a>パイプライン ログの構成、書き込み、および確認

スクリプトのローカルでのテストは、パイプラインの構築を開始する前に主なコード フラグメントや複雑なロジックをデバッグするための優れた方法です。ただし、特にパイプラインのステップ間の相互作用中に診断動作が生じる場合には、ある時点で実際のパイプライン自体の実行中にスクリプトをデバッグすることが必要になる可能性が高いです。 JavaScript コードをデバッグする場合と同様に、リモート実行時にオブジェクトの状態と予期される値を確認できるように、ステップ スクリプトで `print()` ステートメントを適宜使用することをお勧めします。

### <a name="logging-options-and-behavior"></a>ログ オプションと動作

次の表は、パイプラインのさまざまなデバッグ オプションに関する情報を示しています。 これは完全なリストではありません。ここに示されている Azure Machine Learning、Python、OpenCensus のオプションの他にもオプションはあります。

| ライブラリ                    | Type   | 例                                                          | 宛先                                  | リソース                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | メトリック | `run.log(name, val)`                                             | Azure Machine Learning ポータル UI             | [実験を追跡する方法](how-to-track-experiments.md)<br>[azureml.core.Run クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python の印刷とログ    | ログ    | `print(val)`<br>`logging.info(message)`                          | ドライバー ログ、Azure Machine Learning デザイナー | [実験を追跡する方法](how-to-track-experiments.md)<br><br>[Python のログ](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
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

### <a name="finding-and-reading-pipeline-log-files"></a>パイプライン ログ ファイルの確認と読み取り

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

`ParallelRunStep` からのログ記録とトレースの詳細については、「[ParallelRunStep のデバッグとトラブルシューティング](how-to-debug-parallel-run-step.md)」をご覧ください。

## <a name="logging-in-azure-machine-learning-designer-preview"></a>Azure Machine Learning デザイナー (プレビュー) でのログ記録

デザイナーで作成されたパイプラインの場合、作成ページまたはパイプラインの実行の詳細ページで、**70_driver_log** ファイルが確認できます。

### <a name="enable-logging-for-real-time-endpoints"></a>リアルタイム エンドポイントのログ記録を有効にする

デザイナーでリアルタイム エンドポイントのトラブルシューティングとデバッグを行うには、SDK を使用して Application Insight のログ記録を有効にする必要があります。 ログ記録を使用すると、モデル デプロイと使用に関する問題のトラブルシューティングとデバッグを行うことができます。 詳細については、[デプロイ済みモデルのログ記録](how-to-enable-logging.md#logging-for-deployed-models)に関する記事をご覧ください。 

### <a name="get-logs-from-the-authoring-page"></a>作成ページからログを取得する

パイプラインの実行を送信し、作成ページを表示したままにしておくと、各モジュールの実行が終了した時点で、モジュールごとに生成されたログ ファイルを確認できます。

1. 作成キャンバスでの実行が終了したモジュールを選択します。
1. モジュールの右ペインで、 **[Outputs + logs]\(出力 + ログ\)** タブにアクセスします。
1. 右ペインを展開して **70_driver_log.txt** を選択し、ブラウザーにファイルを表示します。 また、ログをローカルにダウンロードすることもできます。

    ![デザイナー上に展開された出力ペイン](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>パイプラインの実行からログを取得する

パイプラインの実行の詳細ページで特定の実行のためのログ ファイルを見つけることもできます。Studio の **[パイプライン]** または **[実験]** セクションどちらかで確認できます。

1. デザイナーで作成されたパイプラインの実行を選択します。

    ![パイプラインの実行ページ](./media/how-to-debug-pipelines/designer-pipelines.png)

1. プレビュー ペインでモジュールを選択します。
1. モジュールの右ペインで、 **[Outputs + logs]\(出力 + ログ\)** タブにアクセスします。
1. 右側のペインを展開して、ブラウザーで **70_driver_log.txt** ファイルを表示するか、またはログをローカルにダウンロードするファイルを選択します。

> [!IMPORTANT]
> パイプラインの実行の詳細ページからパイプラインを更新するには、新しいパイプライン ドラフトにパイプラインの実行を**複製する**必要があります。 パイプラインの実行は、パイプラインのスナップショットです。 ログ ファイルに似ており、変更することはできません。 

## <a name="application-insights"></a>Application Insights
この方法で OpenCensus Python ライブラリを使用する方法の詳細については、次のガイドを参照してください。[Application Insights での機械学習パイプラインのデバッグとトラブルシューティング](how-to-debug-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Visual Studio Code を使用した対話型デバッグ

場合によっては、ML パイプラインで使用される Python コードを対話的にデバッグする必要が生じることがあります。 Visual Studio Code (VS Code) と debugpy を使用すると、トレーニング環境で実行されているコードにアタッチできます。 詳細については、[VS Code での対話型デバッグのガイド](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines)を参照してください。

## <a name="next-steps"></a>次のステップ

* [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) パッケージおよび [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) パッケージについては、SDK リファレンスを参照してください。

* [デザイナーの例外とエラー コード](algorithm-module-reference/designer-error-codes.md)の一覧を参照してください。
