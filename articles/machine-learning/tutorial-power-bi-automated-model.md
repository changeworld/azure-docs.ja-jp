---
title: チュートリアル:自動 ML を使用して予測モデルを作成する (パート 1/2)
titleSuffix: Azure Machine Learning
description: Microsoft Power BI で最良のモデルを使用して結果を予測できるように、自動機械学習モデルを構築およびデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 4d760f5017c20ac88d3329bb79937c971d4fba75
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2021
ms.locfileid: "112459426"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-by-using-automated-machine-learning-part-1-of-2"></a>チュートリアル:Power BI 統合 - 自動機械学習を使用して予測モデルを作成する (パート 1/2)

このチュートリアルのパート 1 では、予測機械学習モデルをトレーニングおよびデプロイします。 Azure Machine Learning スタジオで自動機械学習 (ML) を使用します。  パート 2 では、Microsoft Power BI でパフォーマンスの最も良いモデルを使用して結果を予測します。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * Azure Machine Learning コンピューティング クラスターを作成する。
> * データセットを作成します。
> * 自動機械学習の実行を作成する。
> * リアルタイム スコアリング エンドポイントに最良のモデルをデプロイする。


Power BI で使用するモデルは、3 とおりの方法で作成してデプロイすることができます。  この記事では、"オプション C: スタジオで自動機械学習を使用してモデルをトレーニングおよびデプロイする" について説明します。  このオプションは、コーディング不要の作成エクスペリエンスです。 これでは、データ準備とモデルのトレーニングが完全に自動化されます。 

ただし、代わりに、その他のオプションのいずれかを使用することもできます。

* [オプション A: Jupyter Notebook を使用してモデルをトレーニングおよびデプロイする](tutorial-power-bi-custom-model.md)。 コード優先のこの作成エクスペリエンスでは、Azure Machine Learning スタジオでホストされている Jupyter Notebook を使用します。
* [オプション B: Azure Machine Learning デザイナーを使用してモデルをトレーニングおよびデプロイする](tutorial-power-bi-designer-model.md)。 わずかなコードを記述するだけのこの作成エクスペリエンスでは、ドラッグ アンド ドロップ ユーザー インターフェイスを使用します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 まだサブスクリプションをお持ちでない場合は、[無料試用版](https://azure.microsoft.com/free/)を使用できます。 
- Azure Machine Learning ワークスペース。 ワークスペースがまだない場合は、「[Azure Machine Learning ワークスペースを作成して管理する](./how-to-manage-workspace.md#create-a-workspace)」を参照してください。

## <a name="create-a-compute-cluster"></a>コンピューティング クラスターを作成する

自動機械学習は、多数の機械学習モデルを自動的にトレーニングして、"最良" のアルゴリズムとパラメーターを見つけ出すものです。 モデル トレーニングの実行は、Azure Machine Learning によってコンピューティング クラスター上で並列処理されます。

開始するには、[Azure Machine Learning スタジオ](https://ml.azure.com)の左側のメニューにある **[コンピューティング]** を選択します。 **[Compute clusters]\(コンピューティング クラスター\)** タブを開きます。次に、 **[新規]** を選択します。

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="コンピューティング クラスターの作成方法を示すスクリーンショット。":::

**[Create compute cluster]\(コンピューティング クラスターの作成\)** ページで次の作業を行います。

1. VM サイズを選択してください。 このチュートリアルでは、**Standard_D11_v2** マシンで十分です。
1. **[次へ]** を選択します。
1. 有効なコンピューティング名を入力します。
1. **[ノードの最小数]** は `0` のままにします。
1. **[ノードの最大数]** は `4` に変更します。
1. **［作成］** を選択します

ご自分のクラスターの状態が **[作成中]** に変わります。

>[!NOTE]
> 新しいクラスターのノードは 0 個であるため、コンピューティング コストは発生しません。 自動機械学習ジョブが実行されたときにのみコストが発生します。 アイドル時間が 120 秒を超えると再び、クラスターが自動的に 0 に縮小されます。


## <a name="create-a-dataset"></a>データセットを作成する

このチュートリアルでは、[Diabetes データセット](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)を使用します。 このデータセットは [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) から入手できます。

データセットを作成するために、左側のメニューで **[データセット]** を選択します。 次に **[データセットの作成]** を選択します。 次のオプションが表示されます。

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="新しいデータセットを作成する方法を示すスクリーンショット。":::

**[Open Datasets から]** を選択します。 その後、 **[Create dataset from Open Datasets]\(Open Datasets からのデータセットの作成\)** ページで次の作業を行います。

1. 検索バーを使用して、*diabetes* を見つけます。
1. **[Sample: Diabetes]** を選択します。
1. **[次へ]** を選択します。
1. データセットに *diabetes* という名前を付けます。
1. **［作成］** を選択します

データを探索するために、データセットを選択し、 **[探索]** を選択します。

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="データセットの探索方法を示すスクリーンショット。":::

このデータには、10 個のベースライン入力変数が含まれています (年齢、性、肥満度指数、平均血圧、血清に関する 6 つの測定値など)。 また、**Y** という名前のターゲット変数も 1 つあります。このターゲット変数は、調査開始から 1 年経過時の糖尿病の進行を表す定量的測定値です。

## <a name="create-an-automated-machine-learning-run"></a>自動機械学習の実行を作成する

[Azure Machine Learning スタジオ](https://ml.azure.com)の左側のメニューにある **[自動 ML]** を選択します。 次に、 **[New automated ML run]\(新しい自動 ML の実行\)** を選択します。

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="新しい自動機械学習の実行を作成する方法を示すスクリーンショット。":::

次に、先ほど作成した **diabetes** データセットを選択します。 その後、 **[次へ]** を選択します。

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="データセットを選択する方法を示すスクリーンショット。":::
 
**[Configure run]\(実行の構成\)** ページで、次を実行します。

1. **[実験名]** の **[新規作成]** を選択します。
1. 実験の名前を指定します。
1. **[Target column]\(ターゲット列\)** フィールドで **[Y]** を選択します。
1. **[コンピューティング クラスターを選択する]** フィールドで、先ほど作成したコンピューティング クラスターを選択します。 

完成したフォームは次のようになります。

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="自動機械学習の構成方法を示すスクリーンショット。":::

最後に、機械学習タスクを選択します。 この場合、タスクは **[回帰]** です。

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="タスクの構成方法を示すスクリーンショット。":::

**[完了]** を選択します。

> [!IMPORTANT]
> 自動機械学習で 100 個のモデルのトレーニングを終えるまでに約 30 分かかります。

## <a name="deploy-the-best-model"></a>最適なモデルをデプロイする

自動機械学習が完了したら、 **[モデル]** タブを選択して、試行した機械学習モデルをすべて確認できます。モデルは、パフォーマンス順に表示されます。先頭に表示されるのが、パフォーマンスの最も良いモデルです。 最良のモデルを選択すると、 **[デプロイ]** ボタンが有効になります。

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="モデルの一覧を示すスクリーンショット。":::

**[デプロイ]** を選択して、 **[Deploy a model]\(モデルのデプロイ\)** ウィンドウを開きます。

1. モデル サービスに *diabetes-model* という名前を付けます。
1. **[Azure Container Service]** を選択します。
1. **[デプロイ]** を選択します。

モデルが正常にデプロイされたことを示すメッセージが表示されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、自動機械学習を使用して機械学習モデルをトレーニングおよびデプロイする方法を見てきました。 次のチュートリアルでは、このモデルを Power BI で利用 (スコア付け) する方法について説明します。

> [!div class="nextstepaction"]
> [チュートリアル: Power BI でモデルを利用する](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
