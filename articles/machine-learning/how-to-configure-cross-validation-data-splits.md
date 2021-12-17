---
title: 自動機械学習でのデータの分割とクロス検証
titleSuffix: Azure Machine Learning
description: 自動機械学習実験のためにトレーニング、検証、クロス検証、テストのデータを構成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.custom: automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 11/15/2021
ms.openlocfilehash: d8f94f4e6c7b6d94a2865d0a6a6c4454b1356210
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722147"
---
# <a name="configure-training-validation-cross-validation-and-test-data-in-automated-machine-learning"></a>自動機械学習でのトレーニング、検証、クロス検証、テストのデータを構成する

この記事では、自動機械学習 (自動 ML) の実験のために、トレーニング データと検証データの分割をクロス検証設定とあわせて構成するためのさまざまなオプションについて説明します。

Azure Machine Learning では、自動 ML を使用して複数の ML モデルを構築する場合、各子実行でそのモデルの品質メトリック (精度や加重 AUC など) を計算することによって関連モデルを検証する必要があります。 これらのメトリックは、各モデルで行われた予測を、検証データの過去の観測からの実際のラベルと比較することによって計算されます。 [検証の種類に基づいたメトリックの計算方法の詳細については、こちらを参照してください。](#metric-calculation-for-cross-validation-in-machine-learning) 

自動 ML の実験によって、モデルの検証が自動的に実行されます。 次のセクションでは、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/) で検証の設定をカスタマイズする方法について説明します。 

コードを使用しないエクスペリエンスの詳細については、[Azure Machine Learning Studio での自動機械学習の実験の作成](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件

この記事では、以下が必要です。

* Azure Machine Learning ワークスペース。 ワークスペースを作成するには、[Azure Machine Learning ワークスペース](how-to-manage-workspace.md)の作成に関するページを参照してください。

* Azure Machine Learning SDK を使用した自動機械学習の実験の設定に関する知識。 [チュートリアル](tutorial-auto-train-models.md)または[方法](how-to-configure-auto-train.md)に従って、自動化された機械学習実験の基本的な設計パターンについて確認してください。

* 機械学習の概念としてのトレーニングと検証データの分割およびクロス検証に関する知識。 概要については、以下を参照してください。

    * [機械学習におけるトレーニング、検証、およびテスト データについて](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [機械学習でのクロス検証について](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd) 

[!INCLUDE [automl-sdk-version](../../includes/machine-learning-automl-sdk-version.md)]

## <a name="default-data-splits-and-cross-validation-in-machine-learning"></a>機械学習での既定のデータ分割とクロス検証

[AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) オブジェクトを使用して、実験とトレーニングの設定を定義します。 次のコード スニペットでは、必須パラメーターのみが定義されていることに注意してください。つまり、`n_cross_validations` または `validation_data` のパラメーターは **含まれていません**。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

`validation_data` または `n_cross_validations` のいずれかのパラメーターを明示的に指定しない場合は、1 つのデータセット `training_data` で指定された行数に応じて、自動 ML によって既定の方法が適用されます。

|トレーニング&nbsp;データ&nbsp;のサイズ| 検証の方法 |
|---|-----|
|**20,000&nbsp;行&nbsp;より&nbsp;多い**| トレーニング/検証データの分割が適用されます。 既定では、初期トレーニング データ セットの 10% が検証セットとして取得されます。 次に、その検証セットがメトリックの計算に使用されます。
|**20,000&nbsp;行&nbsp;より&nbsp;少ない**| クロス検証アプローチが適用されます。 フォールドの既定の数は行数によって異なります。 <br> **データセットが 1,000 行より少ない場合は**、10 個のフォールドが使用されます。 <br> **行が 1,000 から 20,000 の間の場合は**、3 つのフォールドが使用されます。


## <a name="provide-validation-data"></a>検証データを指定する

この場合は、1 つのデータ ファイルから始めて、トレーニング データと検証データ セットに分割するか、検証セット用に個別のデータ ファイルを指定することができます。 どちらの方法でも、`AutoMLConfig` オブジェクトの `validation_data` パラメーターによって、検証セットとして使用するデータが割り当てられます。 このパラメーターは、[Azure Machine Learning データセット](how-to-create-register-datasets.md)または Pandas データフレームの形式のデータセットのみを受け入れます。   

> [!NOTE]
> `validation_data` パラメーターを使用する場合、`training_data` および `label_column_name` パラメーターも設定する必要があります。 検証パラメーターは 1 つだけ設定できます。つまり、`validation_data` または `n_cross_validations` のどちらか一方のみ (両方ではなく) を設定できます。

次のコード例では、トレーニングと検証に使用する `dataset` 内の指定されたデータの一部を明示的に定義します。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>検証セットのサイズを指定する

この場合、実験には 1 つのデータセットのみが指定されています。 つまり、`validation_data` パラメーターは **指定されず**、指定されたデータセットは `training_data` パラメーターに割り当てられます。  

`AutoMLConfig` オブジェクトでは、検証用にトレーニング データの一部を提示するように `validation_size` パラメーターを設定できます。 これは、検証セットが、指定された初期の `training_data` から自動 ML によって分割されることを意味します。 この値は 0.0 より大きく 1.0 未満である必要があります (たとえば、0.2 は、データの 20% が検証データ用に提示されることを意味します)。

> [!NOTE]
> `validation_size` パラメーターは予測のシナリオではサポートされていません。 

次のコード例をご覧ください。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="k-fold-cross-validation"></a>K フォールド クロス検証

k 分割交差検証を実行するには、`n_cross_validations` パラメーターを含めて、それを値に設定します。 このパラメーターは、同じフォールド数に基づいて、実行するクロス検証の回数を設定します。

> [!NOTE]
> `n_cross_validations` パラメーターは、ディープ ニューラル ネットワークを使用する分類のシナリオではサポートされていません。
 
次のコードでは、クロス検証のための 5 つのフォールドが定義されています。 そのため、5 つの異なるトレーニングがあり、各トレーニングには 5 分の 4 のデータが使用され、各検証には、ホールドアウト フォールドが毎回異なる 5 分の 1 のデータが使用されます。

その結果、メトリックは、5 つの検証メトリックの平均値を使用して計算されます。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```
## <a name="monte-carlo-cross-validation"></a>モンテカルロ クロス検証

モンテカルロ クロス検証を実行するには、`AutoMLConfig` オブジェクトに `validation_size` と `n_cross_validations` の両方のパラメーターを含めます。 

モンテカルロ クロス検証の場合、`validation_size` パラメーターで指定されたトレーニング データの一部が検証用に確保され、残りのデータがトレーニング用に割り当てられます。 このプロセスは `n_cross_validations` パラメーターに指定された値に基づいて繰り返されます。これにより、毎回、新しいトレーニングと検証の分割がランダムに生成されます。

> [!NOTE]
> モンテカルロ クロス検証は、予測のシナリオではサポートされていません。

次のコードでは、クロス検証用に 7 つのフォールドを使用し、検証用にトレーニング データの20% を使用することが定義されています。 そのため、7 つの異なるトレーニングがあり、各トレーニングには 80% のデータが使用され、各検証には、ホールドアウト フォールドが毎回異なる 20% のデータが使用されます。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 7
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>カスタムのクロス検証データ フォールドを指定する

独自のクロス検証 (CV) データ フォールドを指定することもできます。 分割して検証に使用する列を指定するため、これはより高度なシナリオと見なされます。  トレーニング データにカスタムの CV 分割列を含め、`cv_split_column_names` パラメーターで列名を入力して列を指定します。 各列は 1 つのクロス検証分割を表し、整数値 1 または 0 が入力されます。ここで、1 は行がトレーニングに使用されることを示し、0 は行が検証に使用されることを示します。

次のコード スニペットには、2 つの CV 分割列 'cv1' と 'cv2' がある銀行のマーケティング データが含まれています。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> `cv_split_column_names` を `training_data` および `label_column_name` と共に使用するには、Azure Machine Learning Python SDK バージョン 1.6.0 以降をアップグレードしてください。 以前のバージョンの SDK の場合は、`cv_splits_indices` の使用を参照してください。ただし、`X` と `y` のデータセット入力のみで使用されていることに注意してください。 


## <a name="metric-calculation-for-cross-validation-in-machine-learning"></a>機械学習でのクロス検証のメトリック計算

K 分割またはモンテカルロ クロス検証を使用すると、各検証のフォールドでメトリックが計算され、集計されます。 この集計操作は、スカラー メトリックの平均であり、グラフの合計です。 クロス検証中に計算されるメトリックは、すべてのフォールドに基づいているため、トレーニング セットのすべてのサンプルです。 [自動機械学習のメトリックの詳細については、こちらを参照してください。](how-to-understand-automated-ml.md)

カスタム検証セットまたは自動的に選択された検証セットを使用すると、モデルの評価メトリックは、トレーニング データではなく、その検証セットからのみ計算されます。

## <a name="provide-test-data-preview"></a>テスト データを指定する (プレビュー)

実験終了後に自動 ML によって生成される推奨モデルを評価するために、テスト データを指定することもできます。 テスト データを指定すると、推奨モデルのテストの実行結果に偏りが出ないように、トレーニングと検証とは別のものと見なされます。 [自動 ML でのトレーニング、検証、テストのデータの詳細情報。](concept-automated-ml.md#training-validation-and-test-data) 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!WARNING]
> この機能は、次の自動 ML シナリオでは使用できません
>  * [Computer Vision タスク (プレビュー)](how-to-auto-train-image-models.md)
>  * [多数モデルおよび階層型時系列予測トレーニング (プレビュー)](how-to-auto-train-forecast.md)
>  * [ディープ ラーニング ニューラル ネットワーク (DNN) が有効になっている予測タスク](how-to-auto-train-forecast.md#enable-deep-learning)
>  * [ローカル コンピューティングまたは Azure Databricks クラスターからの自動 ML 実行](how-to-configure-auto-train.md#compute-to-run-experiment)

テスト データセットは [Azure Machine Learning TabularDataset](how-to-create-register-datasets.md#tabulardataset) 形式にする必要があります。 `AutoMLConfig` オブジェクトで `test_data` と `test_size` のパラメーターを使ってテスト データセットを指定できます。  これらのパラメーターは相互に排他的であり、同時に指定したり、`cv_split_column_names` または `cv_splits_indices` と一緒に指定したりすることはできません。

`test_data` パラメーターを使い、`AutoMLConfig` オブジェクトに渡す既存のデータセットを指定します。 

```python
automl_config = AutoMLConfig(task='forecasting',
                             ...
                             # Provide an existing test dataset
                             test_data=test_dataset,
                             ...
                             forecasting_parameters=forecasting_parameters)
```

テスト データを直接指定するのではなく、トレーニングとテストの分割を使う場合は、`AutoMLConfig` の作成時に `test_size` パラメーターを使います。 このパラメーターには、0.0 超から 1.0 未満の浮動小数点値を指定し、テスト データセットに使うトレーニング データセットの割合を指定します。

```python
automl_config = AutoMLConfig(task = 'regression',
                             ...
                             # Specify train/test split
                             training_data=training_data,
                             test_size=0.2)
```

> [!Note]
> 回帰タスクの場合は、ランダム サンプリングが使われます。<br>
> 分類タスクの場合は、階層サンプリングが使われますが、階層サンプリングを使用できない場合は、フォールバックとしてランダム サンプリングが使われます。 <br>
> 現在、予測は、`test_size` パラメーターでのトレーニングとテストの分割を使ったテスト データセットの指定をサポートしていません。


`test_data` または `test_size` のパラメーターを `AutoMLConfig` に渡すと、実験の完了時に自動的にリモート テストの実行がトリガーされます。 このテストの実行には、指定したテスト データが使われ、自動 ML によって推奨される最適なモデルが評価されます。 詳細については、[テストの実行からの予測を取得する方法](how-to-configure-auto-train.md#test-models-preview)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [不均衡データとオーバーフィットを防止します](concept-manage-ml-pitfalls.md)。
* [チュートリアル:自動機械学習を使用してタクシー料金を予測する - データの分割セクション](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets)。
* [時系列予測モデルを自動トレーニングする](how-to-auto-train-forecast.md)方法。
