---
title: ラベル付きデータセットを作成して探索する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning のラベル付けプロジェクトからデータ ラベルをエクスポートして、機械学習タスクに使用する方法について説明します。
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.custom: data4ml
ms.date: 10/21/2021
ms.openlocfilehash: 53abb99aef3a04f3c975cd0fbde4d1097e01269b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560319"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>ラベル付き Azure Machine Learning データセットを作成して探索する

この記事では、Azure Machine Learning データのラベル付けプロジェクトからデータ ラベルをエクスポートし、データ探索用の Pandas データフレーム、または画像変換用の Torchvision データセットなどの一般的な形式に読み込む方法について説明します。 

## <a name="what-are-datasets-with-labels"></a>ラベル付きデータセットとは 

ラベル付きの Azure Machine Learning データセットは、ラベル付きデータセットと呼ばれています。 ラベル付きデータセットというこれらの特定の種類のデータセットは、Azure Machine Learning データ ラベル付けプロジェクトの出力としてのみ作成されます。 [画像のラベル付け](how-to-create-image-labeling-projects.md)や[テキストのラベル付け](how-to-create-text-labeling-projects.md)のためのデータ ラベル付けプロジェクトを作成します。 Machine Learning では、画像の分類 (複数ラベルまたは多クラス) のほか、境界ボックスと組み合わせたオブジェクトの識別でデータ ラベル付けプロジェクトをサポートしています。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro)、または [Azure Machine Learning Studio](https://ml.azure.com/) へのアクセス。
    * [azure-contrib-dataset](/python/api/azureml-contrib-dataset/) パッケージをインストールする
* Machine Learning ワークスペース。 [Azure Machine Learning ワークスペースを作成する](how-to-manage-workspace.md)方法に関するページを参照してください。
* Azure Machine Learning データのラベル付けプロジェクトへのアクセス。 ラベル付けプロジェクトがない場合は、最初に[画像のラベル付け](how-to-create-image-labeling-projects.md)や[テキストのラベル付け](how-to-create-text-labeling-projects.md)のためのものを作成します。

## <a name="export-data-labels"></a>データ ラベルをエクスポートする 

データのラベル付けプロジェクトを完了したら、ラベル付けプロジェクトからラベル データをエクスポートできます。 そうすることで、データとそのラベルの両方への参照をキャプチャし、[COCO 形式](http://cocodataset.org/#format-data)で、または Azure Machine Learning データセットとしてエクスポートすることができます。 ラベル付けプロジェクトの **[プロジェクトの詳細]** ページにある **[エクスポート]** ボタンを使用します。

### <a name="coco"></a>COCO 

 COCO ファイルは、Azure Machine Learning ワークスペースの既定の BLOB ストアにある *export/coco* 内のフォルダーに作成されます。 
 
>[!NOTE]
>物体検出プロジェクトでは、COCO ファイルのエクスポートされた "bbox": [x,y,width,height]" 値が正規化されます。 これらは 1 にスケーリングされます。 例: 640 x 480 ピクセルのイメージでは、(10, 10) の位置にある境界ボックス (幅 30 ピクセル、高さ 60 ピクセル) に次のような注釈が付けられます: (0.015625. 0.02083, 0.046875, 0.125)。 座標は正規化されているため、すべてのイメージの "幅" と "高さ" が "0.0" として表示されます。 実際の幅と高さは、OpenCV や Pillow (PIL) などの Python ライブラリを使用して取得できます。

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning のデータセット

エクスポートした Azure Machine Learning データセットには、Azure Machine Learning Studio の **[データセット]** セクションでアクセスできます。 また、データセットの **[詳細]** ページには、Python からラベルにアクセスするためのサンプル コードも用意されています。

![エクスポートされたデータセット](./media/how-to-create-labeling-projects/exported-dataset.png)

ラベル付きデータを Azure Machine Learning データセットにエクスポートした後、AutoML を使用して、ラベル付きデータでトレーニングされた Computer Vision モデルを作成できます。 詳細については、「[Python を使用して Computer Vision モデルをトレーニングする AutoML を設定する (プレビュー)](how-to-auto-train-image-models.md)」を参照してください

## <a name="explore-labeled-datasets"></a>ラベル付きデータセットを探索する

ラベル付きデータセットを Pandas データフレームまたは Torchvision データセットに読み込んで、データ探索用の人気のオープンソース ライブラリのほか、画像変換とトレーニング用に PyTorch で提供されているライブラリを活用できます。

### <a name="pandas-dataframe"></a>Pandas データフレーム

ラベル付きデータセットは、`azureml-contrib-dataset` クラスの [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) メソッドを使用して Pandas データフレーム に読み込むことができます。 次のシェル コマンドを使用して、このクラスをインストールします。 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>azureml.contrib 名前空間は、サービスの改善に伴って頻繁に変更されます。 そのため、この名前空間内のものはすべて Microsoft によって完全にサポートされているものではなく、プレビューとして見なされる必要があります。

Azure Machine Learning には、Pandas データフレームに変換する場合にファイル ストリームに対して次のファイル処理オプションが用意されています。
* ダウンロード:データ ファイルをローカル パスにダウンロードします。
* マウント: データ ファイルをマウント ポイントにマウントします。 マウントは、Azure Machine Learning ノートブック VM や Azure Machine Learning コンピューティングなど、Linux ベースのコンピューティングでのみ機能します。

次のコードでは、`animal_labels` データセットは、以前にワークスペースに保存されたラベル付けプロジェクトからの出力です。

```Python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Torchvision データセット

`azureml-contrib-dataset` クラスの [to_torchvision()](/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset#to-torchvision--) メソッドを使用して、ラベル付きデータセットを Torchvision データセットに読み込むことができます。 このメソッドを使用するには、[PyTorch](https://pytorch.org/) がインストールされている必要があります。 

次のコードでは、`animal_labels` データセットは、以前にワークスペースに保存されたラベル付けプロジェクトからの出力です。

```python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

from torchvision.transforms import functional as F

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>次のステップ
