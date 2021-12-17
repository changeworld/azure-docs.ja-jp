---
title: Computer Vision タスクのデータを準備する
titleSuffix: Azure Machine Learning
description: 分類、オブジェクト検出、セグメント化に関する Computer Vision モデルをトレーニングするための Azure Machine Learning 自動 ML の画像データの準備
author: vadthyavath
ms.author: rvadthyavath
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: template-how-to
ms.date: 10/13/2021
ms.openlocfilehash: 7f2f3cf3c0af623c6c33e46fe9735a2af03c14c6
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007022"
---
# <a name="prepare-data-for-computer-vision-tasks-with-automated-machine-learning-preview"></a>自動機械学習による Computer Vision タスク用にデータを準備する (プレビュー)

> [!IMPORTANT]
> Azure Machine Learning の自動 ML による Computer Vision モデルのトレーニングのサポートは、試験的なパブリック プレビュー機能です。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、[Azure Machine Learning の自動機械学習](concept-automated-ml.md)で、Computer Vision モデルのトレーニング用画像データを準備する方法について説明します。 

自動機械学習を使用して Computer Vision タスクのモデルを生成するには、ラベル付き画像データを [Azure Machine Learning TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) の形式でモデル トレーニングの入力として取り込む必要があります。 

自動 ML での使用を承認されたスキーマが TabularDataset に含まれていることを確認するには、Azure Machine Learning データのラベル付けツールを使用するか、変換スクリプトを使用します。 

## <a name="prerequisites"></a>前提条件

* [Computer Vision の自動 ML 実験用に承認された JSONL ファイルのスキーマ](reference-automl-images-schema.md)について理解を深めます。

* 自動 ML で Computer Vision モデルをトレーニングするために使用するラベル付きデータ。

## <a name="azure-machine-learning-data-labeling"></a>Azure Machine Learning のラベル付け

ラベル付きデータを持っていない場合は、Azure Machine Learning の[データのラベル付けツール](how-to-create-image-labeling-projects.md)を使用して、画像に手動でラベルを付けることができます。 このツールは、トレーニングに必要なデータを承認された形式で自動的に生成します。

以下のようなデータのラベル付けタスクを作成、管理、および監視するのに役立ちます 

+ 画像分類 (複数クラスおよび複数ラベル)
+ オブジェクトの検出 (境界ボックス)
+ インスタンスのセグメント化 (ポリゴン)

データのラベル付けプロジェクトが既に存在していて、そのデータを使用する場合は、[ラベル付きデータを Azure Machine Learning TabularDataset としてエクスポート](how-to-create-image-labeling-projects.md#export-the-labels)できます。これは、Computer Vision モデルをトレーニングするための自動 ML で直接使用できます。

## <a name="use-conversion-scripts"></a>変換スクリプトを使用する

VOC や COCO など、一般的な Computer Vision のデータ形式でラベル付けしたデータがある場合は、トレーニング データと検証データ用に JSONL ファイルを生成するスクリプトを提供しています。 詳細な手順とスクリプトについては、[ノートブック](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml)を参照してください。

前述のいずれの形式にも準拠していない場合は、独自のスクリプトを使用して、[自動 ML 画像実験用 JSONL ファイルのスキーマ](reference-automl-images-schema.md)で定義されているスキーマに基づいて JSON Lines ファイルを生成できます。

データ ファイルが承認された JSONL 形式に変換された後に、Azure のストレージ アカウントにアップロードできます。 

## <a name="upload-the-jsonl-file-and-images-to-storage"></a>JSONL ファイルと画像をストレージにアップロードする

データを自動 ML トレーニングに使用するには、[データストア](how-to-access-data.md)を使用してデータを [Azure Machine Learning ワークスペース](concept-workspace.md)にアップロードします。 データストアは、データを Azure 上のストレージにアップロード/ダウンロードし、リモート コンピューティング先からデータを操作するためのメカニズムを提供します。

ワークスペースの作成時に自動的に作成される既定のデータストアに、画像や JSONL ファイルを含む親ディレクトリ全体をアップロードします。  このデータストアは、ワークスペースの作成の一環として作成された既定の Azure BLOB ストレージ コンテナーに接続します。

```python
# Retrieve default datastore that's automatically created when we setup a workspace
ds = ws.get_default_datastore()
ds.upload(src_dir='./fridgeObjects', target_path='fridgeObjects')
```
データのアップロードが完了したら、[Azure Machine Learning TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) を作成してワークスペースに登録し、Computer Vision モデルの自動 ML 実験への入力として今後使用することができます。

```python
from azureml.core import Dataset
from azureml.data import DataType

training_dataset_name = 'fridgeObjectsTrainingDataset'
# create training dataset
training_dataset = Dataset.Tabular.from_json_lines_files(path=ds.path("fridgeObjects/train_annotations.jsonl"),
                                                         set_column_types={"image_url": DataType.to_stream(ds.workspace)}
                                                        )
training_dataset = training_dataset.register( workspace=ws,name=training_dataset_name)

print("Training dataset name: " + training_dataset.name)
```

## <a name="next-steps"></a>次のステップ

* [自動機械学習を使用して Computer Vision モデルをトレーニングします](how-to-auto-train-image-models.md)。
* [自動機械学習を使用して小さなオブジェクト検出モデルをトレーニングします](how-to-use-automl-small-object-detect.md)。 