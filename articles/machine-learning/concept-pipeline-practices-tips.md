---
title: 機械学習パイプラインの反復処理と進化
titleSuffix: Azure Machine Learning
description: 開発期間短縮のためのパターン、習慣、ヒント
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 05/01/2020
ms.openlocfilehash: 2ea353469ed111eebb591aa6ba86c652683cc2f0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858182"
---
# <a name="iterating-and-evolving-machine-learning-pipelines"></a>機械学習パイプラインの反復処理と進化

Azure Machine Learning パイプラインでは、効率的な方法でコードをモジュール化したり、結果を再利用したり、計算リソースを最適化したりできます。 パイプライン利用時に役立つヒントと習慣をいくつか挙げます。

## <a name="how-do-you-get-started-with-pipelines"></a>パイプラインはどこから始めたら良いですか。

パイプラインが初めての場合、始めるにあたっていくつかの選択肢があります。

* 構築プロセスを読み、再現することが最も効果的な学習方法と思われる場合は、「[Azure Machine Learning SDK で機械学習パイプラインを作成して管理する](how-to-create-your-first-pipeline.md)」という記事が最適です 
* Jupyter ノートブックでインタラクティブに学習する方法をご希望の場合は、「[Azure Machine Learning Pipelines: はじめに](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-getting-started.ipynb)」ノートブックで開発されたパイプラインが適しているでしょう
* コード ファーストの状況を優先される場合は、「[Azure Machine Learning Pipelines のデモ](https://github.com/microsoft/aml-pipelines-demo)」リポジトリを複製することから始めることをお勧めします

## <a name="how-do-you-modularize-pipeline-code"></a>パイプライン コードをモジュール化するにはどうすればよいですか? 

モジュールと `ModuleStep` クラスは、ML コードをモジュール化する機会として最適です。 ただし、パイプライン手順間で移動することは 1 つの関数呼び出しよりはるかに高額になることに留意する必要があります。 たずねるべき質問は "関数とデータはこの別セクションのそれとは概念的に異なるのか" ではありません。 "この関数とデータを別々に発展させるのか?" か、 または "この計算は高額になるのか。その出力は再利用できるのか?" です。 詳細については、[Module と ModuleVersion の作成方法とそれらを ModuleStep のパイプラインで使用する方法](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb)に関するノートブックをご覧ください。

前に説明しましたが、トレーニングからデータ準備を分離することは、多くの場合、このような機会の 1 つです。 データ準備は複雑であり、時間がかかることがあるため、プロセスをパイプライン手順に分割することもあります。 その他の機会としては、たとえば、トレーニング後のテストや分析があります。 

## <a name="how-do-you-speed-pipeline-iteration"></a>パイプラインの反復処理を速める方法はありますか? 

パイプラインの反復処理を速める一般的な手法: 

- パイプラインを複製し (コピーを作り)、すぐにパイプラインを再実行する
- 起動時間を回避するため、コンピューティング インスタンスは実行状態を維持する
- 再利用を許可するようにデータと手順を構成し、変化のないデータの再計算をスキップすることをパイプラインに許可する

すぐに反復処理を行うなら、複製してパイプラインを作成し、再実行できます。 もう 1 つの便利な手法は、Compute を停止していないのであれば、新しい Compute を起動するコストが発生しないというものです。 実行結果の再利用を許可するように手順を設定する場合、実行を繰り返すとき、可能な限り (手順に変更がないとき) 結果が再利用されます。

## <a name="how-do-you-collaborate-using-ml-pipelines"></a>ML パイプラインを使用して共同作業を行うにはどうすればよいですか? 

個々のパイプラインは自然な進行方向となります。それに沿って作業を分割します。 手順間のデータと引数の流れで合意している限り、開発者ごとに異なる手順を担当できます。複数のチームで分割することもできます。 

アクティブな開発中、ワークスペースから `PipelineRun` と `StepRun` を取得し、これらのオブジェクトを利用して最終と中間の出力をダウンロードし、独自のモジュール化作品にそれらの成果物を利用できます。

## <a name="use-pipelines-to-test-techniques-in-isolation"></a>パイプラインを使用し、分離して手法をテストする

実際の ML ソリューションには通常、すべての手順で相当なカスタマイズが必要になります。 生データは多くの場合、フィルタリング、変換、拡張といった方法で準備する必要があります。 トレーニング プロセスには潜在的に、いくつかのアーキテクチャが考えられます。ディープ ラーニングの場合、レイヤーの規模やアクティブ化の関数にさまざまなバリエーションが考えられます。 一貫したアーキテクチャであっても、ハイパーパラメーター検索で勝ち取るものには大きな意味があります。

[AutoML](concept-automated-ml.md) や[自動ハイパーパラメーター検索](how-to-tune-hyperparameters.md)のようなツールに加え、パイプラインは A/B テスティング ソリューションのための重要なツールになることがあります。 パイプライン手順にいくつかのバリエーションがある場合、個々の実行を生成し、バリエーションを試すことは簡単です。 

```python
data_preparation_variants = [data1, data2, data3]
data_augmentation_variants = [aug1, aug2]
architecture_variants = [train1, train2, train3]

# Cartesian product
all_variants = np.array(np.meshgrid(data_preparation_variants,data_augmentation_variants,architecture_variants)).T.reshape(-1,3)

pipelines = [Pipeline(workspace=ws, steps=variant.tolist(), description=str(variant)) for variant in all_variants]

```

