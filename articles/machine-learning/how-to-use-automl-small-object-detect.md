---
title: AutoML を使用して画像内の小さなオブジェクトを検出する
description: 小さなオブジェクト検出モデルをトレーニングするように Azure Machine Learning の自動 ML を設定します。
author: PhaniShekhar
ms.author: phmantri
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.date: 10/13/2021
ms.openlocfilehash: 7800399be259dddf2782c47696ce014442d8bf55
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234455"
---
# <a name="train-a-small-object-detection-model-with-automl-preview"></a>AutoML を使用して小さなオブジェクト検出モデルをトレーニングする (プレビュー)

> [!IMPORTANT]
> 現在、この機能はパブリック プレビュー段階にあります。 このプレビュー バージョンは、サービス レベル アグリーメントなしに提供されます。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、Azure Machine Learning の[自動 ML](concept-automated-ml.md) を使用して、高解像度画像内の小さなオブジェクトを検出するようにオブジェクト検出モデルをトレーニングする方法について説明します。

通常、オブジェクト検出用のコンピューター ビジョン モデルは、比較的大きなオブジェクトを含むデータセットに対して適切に機能します。 ただし、メモリや計算上の制約のために、これらのモデルは、高解像度画像内の小さなオブジェクトを検出するタスクではパフォーマンスが低下する傾向にあります。 高解像度画像は、通常は大きいため、モデルへの入力の前にサイズが変更されます。それにより、その機能は (最初の画像サイズに比べて) より小さなオブジェクトを検出するように制限されます。

この問題の解決に役立つように、自動 ML では、パブリック プレビューのコンピューター ビジョン機能の一部としてタイリングをサポートしています。 自動 ML のタイリング機能は、[小さなオブジェクト検出のためのタイリングの機能](https://openaccess.thecvf.com/content_CVPRW_2019/papers/UAVision/Unel_The_Power_of_Tiling_for_Small_Object_Detection_CVPRW_2019_paper.pdf)に関する記事にある概念に基づいています。

タイリング時、各画像はタイルのグリッドに分割されます。 隣接するタイルは、幅と高さのディメンションで互いに重複しています。 タイルは、次の図に示すように、元の画像からトリミングされます。 

![タイルの生成](./media/how-to-use-automl-small-object-detect/tiles-generation.png)

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 ワークスペースを作成するには、[Azure Machine Learning ワークスペース](how-to-manage-workspace.md)の作成に関するページを参照してください。

* この記事では、[コンピューター ビジョン タスク用の自動機械学習の実験](how-to-auto-train-image-models.md)を構成する方法に関するある程度の知識を前提にしています。 

## <a name="supported-models"></a>サポートされているモデル

タイリングを使用した小さなオブジェクト検出は現在、次のモデルでサポートされています。

* fasterrcnn_resnet18_fpn
* fasterrcnn_resnet50_fpn
* fasterrcnn_resnet34_fpn
* fasterrcnn_resnet101_fpn
* fasterrcnn_resnet152_fpn
* retinanet_resnet50_fpn

## <a name="enable-tiling-during-training"></a>トレーニング中にタイリングを有効にする

タイリングを有効にするには、`tile_grid_size` パラメーターを (3, 2) などの値に設定できます。ここで、3 は幅のディメンションに沿ったタイルの数であり、2 は高さのディメンションに沿ったタイルの数です。 このパラメーターが (3, 2) に設定されている場合、各画像は 3 x 2 個のタイルのグリッドに分割されます。 タイルの境界付近にあるすべてのオブジェクトがいずれかのタイルに完全に含まれるように、各タイルは隣接するタイルと重複しています。 この重複は、`tile_overlap_ratio` パラメーターによって制御できます。その既定値は 25% です。

タイリングが有効になると、画像全体とそこから生成されたタイルがモデル経由で渡されます。 これらの画像とタイルは、モデルに渡される前に、`min_size` と `max_size` のパラメーターに従ってサイズが変更されます。 この余分なデータの処理のために、計算時間が比例して増加します。 

たとえば、`tile_grid_size` パラメーターが (3, 2) である場合、計算時間はタイリングなしのときと比較して約 7 倍になります。

`tile_grid_size` の値は、ハイパーパラメーター空間で文字列として指定できます。

```python
parameter_space = {
    'model_name': choice('fasterrcnn_resnet50_fpn'),
    'tile_grid_size': choice('(3, 2)'),
    ...
}
```

`tile_grid_size` パラメーターの値は、画像のディメンションと、その画像内のオブジェクトのサイズによって異なります。 たとえば、画像内により小さなオブジェクトが存在する場合、タイルの数は多い方が役立ちます。

データセットに対してこのパラメーターの最適な値を選択するには、ハイパーパラメーター検索を使用できます。 それを行うには、ハイパーパラメーター空間でこのパラメーターの値の選択を指定できます。

```python
parameter_space = {
    'model_name': choice('fasterrcnn_resnet50_fpn'),
    'tile_grid_size': choice('(2, 1)', '(3, 2)', '(5, 3)'),
    ...
}
```
## <a name="tiling-during-inference"></a>推論中のタイリング

タイリングを使用してトレーニングされたモデルがデプロイされると、推論中にもタイリングが発生します。 自動 ML では、トレーニングの `tile_grid_size` 値を使用して、推論中にタイルを生成します。 次の図に示すように、画像全体とそれに対応するタイルがモデル経由で渡され、それらからのオブジェクトの提案がマージされて最終的な予測が出力されます。

![オブジェクトの提案のマージ](./media/how-to-use-automl-small-object-detect/tiles-merge.png)

> [!NOTE] 
> 複数のタイルから同じオブジェクトが検出される可能性があるため、このような重複を削除するために重複検出が実行されます。
>
> 重複検出は、タイルと画像からの提案に対して NMS を実行することによって行われます。 複数の提案が重複している場合は、最も高いスコアを持つものが選択され、その他は重複として破棄されます。2 つの提案は、それらの間の和集合に対する共通部分の割合 (iou) が `tile_predictions_nms_thresh` パラメーターより大きい場合に重複していると見なされます。

トレーニングではタイリングを有効にせず、推論中にのみ有効にするオプションもあります。 それを行うには、トレーニングのためにではなく、推論中にのみ `tile_grid_size` パラメーターを設定します。 

それにより、一部のデータセットでパフォーマンスが向上する可能性があり、またトレーニング時のタイリングに付随する追加コストも発生しません。 

## <a name="tiling-hyperparameters"></a>タイリング ハイパーパラメーター 

タイリング機能を制御するために使用できるパラメーターを次に示します。

| パラメーター名    | 説明   | Default |
| --------------- |-------------| -------|
| `tile_grid_size` |  各画像をタイリングするために使用するグリッド サイズ。 トレーニング中、検証中、推論中に使用できます。<br><br>文字列として渡される 2 つの整数のタプル (`'(3, 2)'` など)。<br><br> *注: このパラメーターを設定すると、すべてのタイルと画像がモデルによって処理されるため、計算時間が比例して増加します。*| 既定値はなし |
| `tile_overlap_ratio` | 各ディメンション内の隣接するタイル間の重複率を制御します。 タイルの境界付近にあるオブジェクトが大きすぎてどのタイルにも完全には収まらない場合は、オブジェクトが少なくとも 1 つのタイルに完全に収まるように、このパラメーターの値を大きくします。<br> <br>  [0, 1) の浮動小数点数である必要があります。| 0.25 |
| `tile_predictions_nms_thresh` | タイルと画像からの予測のマージ中に非最大抑制 (nms) を行うために使用する、和集合に対する共通部分の割合のしきい値。 検証中と推論中に使用できます。 最終的な予測の中にオブジェクトあたり複数のボックスが検出された場合は、このパラメーターを変更します。  <br><br> [0, 1] の浮動小数点数である必要があります。 | 0.25 |


## <a name="example-notebooks"></a>サンプルの Notebook

オブジェクト検出モデルの設定とトレーニングの詳細なコード例については、[オブジェクト検出のサンプル ノートブック](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml/image-object-detection/auto-ml-image-object-detection.ipynb)を参照してください。

>[!NOTE]
> この記事にあるすべての画像は、[MIT ライセンス契約](https://choosealicense.com/licenses/mit/)の許可される使用のセクションに従って使用可能になります。  
> Copyright © 2020 Roboflow, Inc.

## <a name="next-steps"></a>次のステップ

* [モデルをデプロイする方法と場所](how-to-deploy-and-where.md)についてさらに詳しく学習する。
* 実行のたびに提供されるパフォーマンス グラフおよびメトリックの定義と例については、「[自動化機械学習実験の結果を評価する](how-to-understand-automated-ml.md)」を参照してください。 
