---
title: AutoML 実験での特徴量化
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で提供されている特徴量化の設定と、自動 ML 実験における特徴エンジニアリングのサポートについて説明します。
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/28/2020
ms.custom: seodec18
ms.openlocfilehash: 93e18a95e30c21a44f9ca7df92925323930a9ce8
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84122308"
---
# <a name="featurization-with-automated-machine-learning"></a>自動機械学習による特徴量化

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

このガイドでは、特徴量化に関してどのような設定があるか、また、[自動機械学習の実験](concept-automated-ml.md)を行ううえで、それらをどのようにカスタマイズすればよいかについて説明します。

特徴エンジニアリングは、データのドメイン知識を使用して、ML アルゴリズムの学習向上に寄与する特徴を構築するプロセスです。 Azure Machine Learning では、特徴エンジニアリングを容易にするために、データのスケーリングと正規化の手法が適用されます。 自動機械学習 (AutoML) の実験では、これらの手法と特徴エンジニアリングが、まとめて特徴量化と呼ばれています。

この記事は、AutoML の実験を構成する方法についての知識が既にある読者を対象としています。 詳細については、次の記事を参照してください。

* コード ファーストのエクスペリエンス: [Python SDK で自動 ML の実験を構成する](how-to-configure-auto-train.md)
* コーディングなしの (または少しのコードを使用した) エクスペリエンス: [Azure Machine Learning Studio を使用して自動機械学習モデルを作成、確認、デプロイする](how-to-use-automated-ml-for-ml-models.md)

## <a name="configure-featurization"></a>特徴量化を構成する

自動機械学習におけるあらゆる実験では、[自動スケーリングと正規化の手法](#featurization)が既定でデータに適用されます。 スケーリングと正規化の手法は、さまざまなスケールの特徴に反応する "*特定*" のアルゴリズムを支援する特徴量化の一種です。 ただし、**欠損値の補完、エンコード**、**変換**などの特徴量化も追加で有効にできます。

> [!NOTE]
> 自動化された機械学習の特徴付け手順 (機能の正規化、欠損データの処理、テキストから数値への変換など) は、基になるモデルの一部になります。 このモデルを予測に使用する場合、トレーニング中に適用されたのと同じ特徴付けの手順がご自分の入力データに自動的に適用されます。

SDK を使用して構成された実験では、`featurization` 設定を有効または無効にできるほか、実験に使用する特徴量化ステップを自分で指定することもできます。 Azure Machine Learning Studio をご使用の場合、特徴量化を有効にする方法については、[こちらの手順](how-to-use-automated-ml-for-ml-models.md#customize-featurization)でご覧ください。

次の表は、[AutoMLConfig クラス](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)の `featurization` に使用できる設定を示したものです。 

|特徴付けの構成 | 説明|
------------- | ------------- |
|**`"featurization": 'auto'`**| 前処理の一環として、[データ ガードレールと特徴付けの手順](#featurization)が自動的に実行されることを示します。 **既定の設定**です。|
|**`"featurization": 'off'`**| 特徴量化手順が自動的には実行されないことを示します。|
|**`"featurization":`&nbsp;`'FeaturizationConfig'`**| カスタマイズされた特徴付け手順を使用する必要があることを示します。 [特徴付けをカスタマイズする方法の詳細](#customize-featurization)。|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>自動特徴付け

以下の表は、データに対して自動的に適用される手法の一覧です。 SDK または Studio を使用して構成された実験に適用されます。 この動作を無効にするには、`AutoMLConfig` オブジェクトで `"featurization": 'off'` を設定します。

> [!NOTE]
> 自動 ML で作成されたモデルを [ONNX モデル](concept-onnx.md) にエクスポートする予定がある場合、* で示された特徴付けオプションのみが ONNX 形式でサポートされます。 [モデルの ONNX への変換](concept-automated-ml.md#use-with-onnx)の詳細についてご確認ください。 

|特徴量化&nbsp;ステップ| 説明 |
| ------------- | ------------- |
|**高カーディナリティまたは差異なしの特徴の削除*** |これらをトレーニング セットおよび検証セットから削除します。まったく値が存在しない特徴、すべての行の値が同じである特徴、高いカーディナリティ (ハッシュ、ID、GUID など) の特徴が含まれます。|
|**欠損値の補完*** |数値特徴の場合、その列の平均値で補完します。<br/><br/>カテゴリ特徴の場合、出現回数が最も多い値で補完します。|
|**その他の特徴の生成*** |DateTime の特徴:年、月、日、曜日、年の通算日、四半期、年の通算週、時間、分、秒。<br/><br/>テキストの特徴:ユニグラム、バイグラム、文字トライグラムに基づく期間の頻度。|
|**変換とエンコード***|一意の値がほとんどない数値特徴は、カテゴリ特徴に変換されます。<br/><br/>カーディナリティの低いカテゴリ型の場合、ワンホット エンコードが実行されます。カーディナリティが高い場合は、ワンホット ハッシュ エンコードです。|
|**ワードの埋め込み**|事前トレーニングされたモデルを使用してテキスト トークンのベクトルをセンテンス ベクトルに変換するテキスト特性化機能です。 ドキュメント内の各ワードの埋め込みベクトルは、ドキュメント特徴ベクトルを生成するためにまとめて集約されます。|
|**ターゲット エンコード**|カテゴリ特徴の場合、回帰の問題について各カテゴリを平均ターゲット値にマップします。分類の問題については、各クラスのクラス確率にマップします。 マッピングの過剰適合および疎データ カテゴリによって発生するノイズを削減するために、頻度ベースの重み付けと k フォールド クロス検証が適用されます。|
|**テキスト ターゲット エンコード**|テキスト入力の場合、bag-of-words を使用するスタック線形モデルは、各クラスの確率を生成するために使用されます。|
|**証拠の重み (WoE)**|ターゲット列に対するカテゴリ列の相関関係のメジャーとして、WoE を計算します。 それは、クラス内およびクラス外の確率に対する比率の対数として計算されます。 このステップでは、クラスごとに 1 つの数値特徴列を出力し、明示的に欠損値と外れ値の処理を補完する必要がなくなります。|
|**クラスターの距離**|k-means クラスタリング モデルを、すべての数値列を対象にトレーニングします。  k 個の新しい特徴 (クラスターごとに 1 つの新しい数値特徴) を出力します。各クラスターの重心までの各サンプルの距離を含みます。|

## <a name="data-guardrails"></a>データ ガードレール

データ ガードレールは、データの潜在的な問題 (欠損値、[クラスの不均衡](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)など) を特定して、より優れた結果を得るための是正措置を取る上で役立ちます。 

データ ガードレールは次の場合に適用されます。 

* **SDK での実験**: `AutoMLConfig` オブジェクトで `"featurization": 'auto'` パラメーターまたは `validation=auto` パラメーターが指定されているとき。
* **Studio での実験**: *[自動特徴量化]* が有効になっているとき。  

実験に関するデータ ガードレールは、次のようにして確認できます。

* Python SDK で実験を送信する際に `show_output=True` を設定する。

* Studio の場合、自動 ML 実行の **[データ ガードレール]** タブを確認する。

### <a name="data-guardrail-states"></a>データ ガードレールの状態

データ　ガードレールには、3 つの状態のいずれかが表示されます。"**成功**"、"**完了**"、または "**通知済み**" です。

|State| 説明 |
|----|---- |
|**成功**| データの問題は検出されませんでした。ユーザーの操作は不要です。 |
|**完了**| データに変更が適用されました。 変更が期待どおりの結果と一致するように自動 ML が行った修正アクションをレビューするようお勧めします。 |
|**通知済み**| 修復できないデータの問題が検出されました。 改訂して問題を修正するようお勧めします。| 

次の表は、現在サポートされているデータ ガードレールと、実験を送信するときにユーザーに表示される可能性がある関連ステータスを示しています。

ガードレール|Status|トリガー&nbsp;の&nbsp;条件
---|---|---
**欠損特徴量値の補完** |*成功* <br><br><br> *完了*| トレーニング データで、不足している機能の値が検出されませんでした。 [不足している値の補完](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options)の詳細を確認してください。 <br><br> 不足している機能の値が、トレーニング データで検出され、補完されました。
**高カーディナリティの特徴量の処理** |*成功* <br><br><br> *完了*| 入力が分析され、高カーディナリティ機能は検出されませんでした。 [高カーディナリティ機能の検出](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options)の詳細を確認してください。 <br><br> 高カーディナリティ機能が入力で検出され、処理されました。
**検証分割処理** |*完了*| 検証構成は "自動" に設定されており、トレーニング データに含まれているのは **20,000 行未満**でした。 <br> トレーニング済みモデルの各イテレーションは、クロス検証によって検証されました。 [検証データ](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data)の詳細を確認してください。 <br><br> 検証構成が "自動" に設定されており、トレーニング データに含まれているのは **20,000 行超**でした。 <br> 入力データはトレーニング データセットと検証データセットに分割され、モデルが検証されます。
**クラス均衡の検出** |*成功* <br><br><br><br><br> *通知済み* | 入力が分析され、すべてのクラスがトレーニング データ内で均衡が取られます。 サンプルの数と比率によって測定され、データセット内で各クラスに適正な表現がある場合、データセットは均衡が取れていると見なされます。 <br><br><br> 入力で、不均衡なクラスが検出されました。 モデルのバイアスを修正するには、バランスの問題を修正します。 [不均衡なデータ](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)の詳細を確認してください。
**メモリの問題の検出** |*成功* <br><br><br><br> *完了* |<br> 選択した {期間、ラグ、ローリング ウィンドウ} の値が分析され、潜在的なメモリ不足の問題は検出されませんでした。 時系列[予測構成](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment)の詳細を確認してください。 <br><br><br>選択した {期間、ラグ、ローリング ウィンドウ} の値が分析され、潜在的に実験がメモリ不足に陥ることがあります。 ラグまたはローリング ウィンドウの構成がオフになっています。
**頻度の検出** |*成功* <br><br><br><br> *完了* |<br> 時系列が分析され、すべてのデータ ポイントが検出済みの頻度でアラインされます。 <br> <br> 時系列が分析され、検出された頻度にアラインしないデータ ポイントが検出されました。 このようなデータ ポイントはデータセットから削除されました。 [時系列予測のデータの準備](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)の詳細を確認してください。

## <a name="customize-featurization"></a>特徴量化のカスタマイズ

ML モデルのトレーニングに使用したデータと特徴から適切な予測が得られるよう、特徴量化の設定をカスタマイズすることができます。 

特徴量化をカスタマイズするには、`AutoMLConfig` オブジェクトで  `"featurization": FeaturizationConfig` を指定します。 Azure Machine Learning Studio を実験にご使用の場合、こちらでその[方法](how-to-use-automated-ml-for-ml-models.md#customize-featurization)をご覧ください。

サポートされているカスタマイズは次のとおりです。

|カスタマイズ|定義|
|--|--|
|**列の目的の更新**|指定した列の特徴の種類をオーバーライドします。|
|**トランスフォーマー パラメーターの更新** |指定したトランスフォーマーのパラメーターを更新します。 現在、Imputer (平均値、最頻値、中央値) と HashOneHotEncoder がサポートされています。|
|**列の削除** |特徴付けされない列です。|
|**ブロック トランスフォーマー**| 特徴付けプロセスで使用されるトランスフォーマーをブロックします。|

API 呼び出しを使用して、FeaturizationConfig オブジェクトを作成します。
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```
## <a name="next-steps"></a>次のステップ

* 自動 ML の実験を設定する方法を学習する。

    * コーディング エクスペリエンスをご希望の場合: [Azure Machine Learning SDK を使用して自動 ML の実験を構成する](how-to-configure-auto-train.md)。
    * コーディングなしの (または少しのコードを使用した) エクスペリエンスをご希望の場合: [Azure Machine Learning Studio で自動機械学習の実験を作成する](how-to-use-automated-ml-for-ml-models.md)。

* [モデルをデプロイする方法と場所](how-to-deploy-and-where.md)についてさらに詳しく学習する。

* [自動機械学習を使用して回帰モデルをトレーニングする方法](tutorial-auto-train-models.md)または[リモート リソースに対して自動機械学習を使用してトレーニングする方法](how-to-auto-train-remote.md)についてさらに詳しく学習する。
