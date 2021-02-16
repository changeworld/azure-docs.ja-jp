---
title: ML の実験とメトリックをログに記録する
titleSuffix: Azure Machine Learning
description: ML のトレーニング実行のログ記録を有効にすると、リアルタイムの実行メトリックを監視し、エラーと警告を診断するのに役立ちます。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 9e5f64d9ef61a272da488ad70e690db4c07ddccc
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625079"
---
# <a name="enable-logging-in-ml-training-runs"></a>ML のトレーニングの実行でログ記録を有効にする


Azure Machine Learning Python SDK を使用すると、既定の Python ログ パッケージと SDK 固有の機能の両方を使用してリアルタイムの情報をログに記録できます。 ユーザーは、ポータルでローカルにログを記録し、ワークスペースにログを送信することができます。

ログは、エラーや警告を診断したり、パラメーターやモデルのパフォーマンスなどのパフォーマンス メトリックを追跡したりするのに役立ちます。 この記事では、次のシナリオでログ記録を有効にする方法について説明します。

> [!div class="checklist"]
> * 対話型のトレーニング セッション
> * ScriptRunConfig を使用したトレーニング ジョブの送信
> * Python ネイティブの `logging` 設定
> * その他のソースからのログ記録


> [!TIP]
> この記事では、モデルのトレーニング プロセスを監視する方法について説明します。 Azure Machine Learning からリソース使用状況やイベント (クォータ、トレーニング実行の完了、モデル デプロイの完了など) を監視することに関心がある場合は、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。

## <a name="data-types"></a>データ型

複数のデータ型 (スカラー値、リスト、テーブル、イメージ、ディレクトリなど) をログに記録できます。 詳細と、さまざまなデータ型の Python コード例については、[Run クラスの参照ページ](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py)を参照してください。

## <a name="interactive-logging-session"></a>対話型のログ セッション

対話型のログ セッションは、通常、ノートブック環境で使用されます。 メソッド [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-) を使用すると、対話型のログ セッションが開始されます。 セッション中にログに記録されるすべてのメトリックは、実験の実行レコードに追加されます。 メソッド [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecomplete--set-status-true-) を使用すると、セッションが終了し、実行が完了としてマークされます。

## <a name="scriptrun-logs"></a>ScriptRun ログ

このセクションでは、ScriptRunConfig を使用して構成したときに作成される実行の内部にログ記録のコードを追加する方法について説明します。 [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) クラスを使用して、反復可能な実行のためにスクリプトと環境をカプセル化できます。 このオプションを使用して、監視用の視覚的な Jupyter Notebook ウィジェットを表示することもできます。

この例では、アルファ値に対してパラメーター スイープを実行し、[run.log()](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truelog-name--value--description----) メソッドを使用して結果をキャプチャします。

1. ログ記録ロジック `train.py` を含むトレーニング スクリプトを作成します。

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. ```train.py``` スクリプトを送信して、ユーザー管理の環境内で実行します。 スクリプト フォルダー全体がトレーニング用に送信されます。

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)]


   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    `show_output` パラメーターを使用すると詳細なログ記録が有効になり、これによってトレーニング プロセスからの詳細情報と、リモート リソースまたはコンピューティング先に関する情報を見ることができます。 次のコードを使用して、実験を送信するときに詳細ログ記録を有効にします。

```python
run = exp.submit(src, show_output=True)
```

結果の実行時に `wait_for_completion` 関数で同じパラメーターを使用することもできます。

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>ネイティブの Python のログ

SDK の一部のログには、ログ レベルを DEBUG に設定するよう指示するエラーが含まれる場合があります。 ログ記録のレベルを設定するには、スクリプトに次のコードを追加します。

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>その他のログ ソース

Azure Machine Learning では、トレーニング中に、自動機械学習の実行や、ジョブを実行する Docker コンテナーなど、他のソースからの情報をログに記録することもできます。 これらのログは文書化されていませんが、問題が発生して Microsoft サポートに問い合わせた場合、サポートはトラブルシューティングの際にこれらのログを使用できる可能性があります。

Azure Machine Learning デザイナーにおけるメトリックのログ記録の詳細については、[デザイナーでメトリックをログに記録する方法](how-to-track-designer-experiments.md)に関する記事を参照してください

## <a name="example-notebooks"></a>サンプルの Notebook

次の Notebook は、この記事の概念を示しています。
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次のステップ

Azure Machine Learning の使用方法の詳細については、これらの記事を参照してください。

* [Azure Machine Learning デザイナーでメトリックをログに記録する](how-to-track-designer-experiments.md)方法を確認します。

* 最適なモデルを登録し、チュートリアルでデプロイする方法の例については、「[Azure Machine Learning で画像分類モデルをトレーニングする](tutorial-train-models-with-aml.md)」をご覧ください。
