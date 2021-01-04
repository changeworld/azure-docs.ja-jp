---
title: チュートリアル:自動 ML を使用して予測モデルを作成する (パート 1/2)
titleSuffix: Azure Machine Learning
description: Microsoft Power BI で最良のモデルを使用して結果を予測できるように、自動 ML モデルを構築、デプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 897f493edf6ccdebb25c201e8e4f9babfb0754c5
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370879"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-using-automated-machine-learning-part-1-of-2"></a>チュートリアル:Power BI 統合 - 自動機械学習を使用して予測モデルを作成する (パート 1/2)

このチュートリアルのパート 1 では、Azure Machine Learning スタジオで自動機械学習を使用して予測機械学習モデルをトレーニング、デプロイします。  パート 2 では、Microsoft Power BI でパフォーマンスの最も良いモデルを使用して結果を予測します。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * Azure Machine Learning コンピューティング クラスターの作成
> * データセットを作成する
> * 自動 ML 実行を作成する
> * リアルタイム スコアリング エンドポイントに最良のモデルをデプロイする


Power BI で使用するモデルは、3 とおりの方法で作成、デプロイすることができます。  この記事では、オプション C (スタジオで自動 ML を使用してモデルをトレーニング、デプロイする) を取り上げます。  このオプションで紹介するのは、データ準備とモデルのトレーニングを完全に自動化する、コーディング不要の作成エクスペリエンスです。 

その他、次の方法を使用できます。

* [オプション A: Notebooks を使用してモデルをトレーニング、デプロイする](tutorial-power-bi-custom-model.md) - Azure Machine Learning スタジオにホストされた Jupyter Notebook を使用した、コード優先の作成エクスペリエンス。
* [オプション B: デザイナーを使用してモデルをトレーニング、デプロイする](tutorial-power-bi-designer-model.md) - デザイナーを使用してわずかなコードを記述するだけの作成エクスペリエンス (ドラッグ アンド ドロップ ユーザー インターフェイス)。

## <a name="prerequisites"></a>[前提条件]

- Azure サブスクリプション ([無料試用版を利用できます](https://aka.ms/AMLFree))。 
- Azure Machine Learning ワークスペース。 まだワークスペースをお持ちでない場合は、[Azure Machine Learning ワークスペースの作成方法](./how-to-manage-workspace.md#create-a-workspace)に関する手順に従ってください。

## <a name="create-compute-cluster"></a>コンピューティング クラスターを作成する

自動 ML は、多種多様な機械学習モデルを自動的にトレーニングして、"最良" のアルゴリズムとパラメーターを見つけ出すものです。 モデル トレーニングの実行は、Azure Machine Learning によってコンピューティング クラスター上で並列処理されます。

[Azure Machine Learning スタジオ](https://ml.azure.com)の左側のメニューから **[Compute]\(コンピューティング\)** を選択し、 **[Compute Clusters]\(コンピューティング クラスター\)** タブを選択します。 **[新規]** を選択します。

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="コンピューティング クラスターの作成方法を示すスクリーンショット":::

**[Create compute cluster]\(コンピューティング クラスターの作成\)** 画面で次の作業を行います。

1. VM サイズを選択します (このチュートリアルの用途であれば、`Standard_D11_v2` マシンをお勧めします)。
1. **[次へ]** を選択します
1. 有効なコンピューティング名を入力します
1. **[Minimum number of nodes]\(最小ノード数\)** は 0 のままにします
1. **[最大ノード数]** は 4 に変更します
1. **[作成]**

クラスターの状態が **[作成中]** に変化したことがわかります。

>[!NOTE]
> クラスターが作成されてもノード数は 0 です。つまり、コンピューティング コストは発生しません。 自動 ML ジョブが実行されたときに初めてコストが発生します。 アイドル時間が 120 秒を超えると再び、クラスターが自動的に 0 に縮小されます。


## <a name="create-dataset"></a>データセットを作成する

このチュートリアルでは、[Diabetes (糖尿病) データセット](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)を使用します。これは [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) から入手できます。

データセットを作成するには、左側のメニューの **[データセット]** を選択し、 **[データセットの作成]** を選択します。すると、次のオプションが表示されます。

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="新しいデータセットを作成する方法を示すスクリーンショット":::

**[Open Datasets から]** を選択し、 **[Open Datasets からのデータセットの作成]** 画面で次の作業を行います。

1. 検索バーを使用して *diabetes* を検索します
1. **[Sample: Diabetes]\(サンプル: Diabetes\)** を選択します
1. **[次へ]** を選択します
1. データセットの名前として「*diabetes*」を入力します
1. **[作成]**

[データセット]、 **[Explore]\(参照\)** の順に選択してデータを参照することができます。

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="データセットの参照方法を示すスクリーンショット":::

このデータには、10 個のベースライン入力変数 (年齢、性、肥満度指数、平均血圧、および血清に関する 6 つの測定値) のほか、**Y** という名前のターゲット変数 (調査開始から 1 年経過時の糖尿病の進行を表す定量的測定値) が 1 つ含まれています。

## <a name="create-automated-ml-run"></a>自動 ML 実行を作成する

[Azure Machine Learning スタジオ](https://ml.azure.com)の左側のメニューから **[自動 ML]** を選択し、 **[New Automated ML Run]\(新しい自動 ML の実行\)** を選択します。

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="新しい自動 ML の実行を作成する方法を示すスクリーンショット":::

次に、先ほど作成した **diabetes** データセットを選択し、 **[次へ]** を選択します。

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="データセットを選択する方法を示すスクリーンショット":::
 
**[Configure run]\(実行の構成\)** 画面で次の作業を行います。

1. **[実験名]** の **[新規作成]** を選択します
1. 実験の名前を入力します
1. [Target column]\(ターゲット列\) フィールドで **[Y]** を選択します
1. **[コンピューティング クラスターを選択する]** フィールドで、先ほど作成したコンピューティング クラスターを選択します。 

完成したフォームは次のようになります。

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="自動 ML の構成方法を示すスクリーンショット":::

最後に、実行する機械学習タスク ( **[回帰]** ) を選択する必要があります。

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="タスクの構成方法を示すスクリーンショット":::

**[完了]** を選択します。

> [!IMPORTANT]
> 自動 ML で 100 種類のモデルのトレーニングを終えるまでに約 30 分かかります。

## <a name="deploy-the-best-model"></a>最適なモデルをデプロイする

自動 ML の実行が完了したら、 **[モデル]** タブを選択して、試行した各種の機械学習モデルをすべて一覧表示できます。モデルは、パフォーマンス順に表示されます。先頭に表示されるのが、パフォーマンスの最も良いモデルです。 最良のモデルを選択すると、 **[デプロイ]** ボタンが有効になります。

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="モデルの一覧を示すスクリーンショット":::

**[デプロイ]** を選択すると、 **[モデルのデプロイ]** 画面が表示されます。

1. モデル サービスに名前を付けます。ここでは、**diabetes-model** を使用します
1. **[Azure Container Service]** を選択します
1. **[デプロイ]** を選択します

モデルが正常にデプロイされたことを示すメッセージが表示されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、自動 ML を使用して機械学習モデルをトレーニング、デプロイする方法を見てきました。 次のチュートリアルでは、このモデルを Power BI から利用 (スコアリング) する方法を紹介します。

> [!div class="nextstepaction"]
> [チュートリアル: Power BI でモデルを利用する](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
