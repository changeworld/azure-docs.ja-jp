---
title: Jupyter ノートブックの例
titleSuffix: Azure Machine Learning service
description: Python 内で Azure Machine Learning service を探索するための Jupyter Notebook の例を検索し、使用します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 12da1b20c5e4e6299445b8ec8ec90eeec6711e2c
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805520"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Jupyter Notebook を使用して Azure Machine Learning service を探索する

作業を容易にするために、Azure Machine Learning service の探索に使用できる一連の Jupyter Python Notebook を開発しました。 

このサイトのドキュメントでサービスの使用方法を確認し、これらのノートブックを使用して、それらを自分の状況に合わせてカスタマイズします。 

以下のいずれかのパスを使用して、これらのサンプル ノートブックと共にノートブック サーバーを稼働します。  サーバーを稼働したら、**tutorials** フォルダーでチュートリアル ノートブックを見つけます。または、**how-to-use-azureml** フォルダーでさまざまな機能を確認します。


## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Azure Notebooks を試す:クラウド上の無料の Jupyter Notebook

Azure Notebooks の利用を開始するのは簡単です。 [Azure Notebooks](https://notebooks.azure.com/) には [Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk) が既にインストールされて構成されています。 インストールと今後の更新プログラムは、Azure サービスを介して自動的に管理されます。
  
[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]


## <a name="use-a-data-science-virtual-machine-dsvm"></a>Data Science Virtual Machine (DSVM) を使用する

DSVM には [Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk) とノートブック サーバーが既にインストールされて構成されています。 

[DSVM を作成](how-to-configure-environment.md#dsvm)した後、DSVM でこれらの手順を使用してノートブックを実行します。

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]


## <a name="use-your-own-jupyter-notebook-server"></a>自分の Jupyter Notebook サーバーを使用する

次の手順を使用して、コンピューターにローカルの Jupyter Notebook サーバーを作成します。

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

このクイック スタートの各手順では、クイック スタートとチュートリアルのノートブックを実行するために必要なパッケージをインストールします。  他のサンプル ノートブックでは、さらに別のコンポーネントのインストールが必要になることがあります。  これらのコンポーネントの詳細については、「[Install the Azure Machine Learning SDK for Python (Azure Machine Learning SDK for Python をインストールする)](https://docs.microsoft.com/python/api/overview/azure/ml/install)」を参照してください。

<a name="automated-ml-setup"></a>

## <a name="automated-machine-learning-setup"></a>自動化された機械学習の設定 

"_以下の手順は、**how-to-use-azureml/automated-machine-learning** フォルダーにあるノートブックにのみ適用されます。_"

上記のオプションのどれでも使用できますが、次の手順に従って、環境のインストールとワークスペースの作成を同時に行うこともできます。 

1. [Mini-conda](https://conda.io/miniconda.html) をインストールします。 3.7 またはそれ以降を選択します。 プロンプトに従ってインストールします。 
   >[!NOTE]
   >4.4.10 以降のバージョンであれば、既存の conda を使用できます。 `conda -V` を使用してバージョンを表示します。 `conda update conda` コマンドを使用して conda バージョンを更新することができます。 mini-conda を明示的にインストールする必要はありません。

1. [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) からサンプルのノートブックを zip ファイルとしてダウンロードし、内容をローカル ディレクトリに抽出します。 自動化された機械学習のノートブックは、`how-to-use-azureml/automated-machine-learning` フォルダーにあります。

1. 新しい Conda 環境を設定します。 
   1. ローカル コンピューターに Conda プロンプトを開きます。
   
   1. ローカル コンピューターに抽出したファイルに移動します。
   
   1. **automated-machine-learning** フォルダーを開きます。
   
   1. Windows の conda プロンプトまたはオペレーティング システムの `.sh` ファイル内で `automl_setup.cmd` を実行します。 実行には約 10 分かかることがあります。

      セットアップ スクリプトでは以下の操作を行います:
      + 新しい conda 環境を作成する
      + 必要なパッケージをインストールする
      + ウィジェットを構成する
      + Jupyter Notebook を開始する
      
   >[!NOTE]
   > スクリプトでは、conda 環境名をオプションのパラメーターとして受け取ります。 既定の conda 環境名は `azure_automl` です。 正確なコマンドは、オペレーティング システムによって異なります。 これは、新しい環境を作成したり新しいバージョンにアップグレードしたりする場合に便利です。 たとえば、"automl_setup.cmd azure_automl_sandbox" を使用して、環境名 azure_automl_sandbox を作成することができます。 
      
1. スクリプトが完了すると、ブラウザーに Jupyter Notebook のホーム ページが表示されます。

1. ノートブックを保存したパスに移動します。 

1. 自動化された機械学習のフォルダーを開き、**configuration.ipynb** ノートブックを開きます。 

1. ノートブック内のセルを実行して、Machine Learning Services リソース プロバイダーを登録し、ワークスペースを作成します。

これで、ローカル コンピューターに保存されたノートブックを開いて実行する準備が整いました。


## <a name="next-steps"></a>次の手順

[Azure Machine Learning service 用の GitHub ノートブック リポジトリ](https://aka.ms/aml-notebooks)

以下のチュートリアルを試します:
+ [MNIST を使用した画像分類モデルのトレーニングとデプロイ](tutorial-train-models-with-aml.md)

+ [NYC タクシー データ セットを使用して回帰モデルをトレーニングするためのデータの準備と自動化された機械学習](tutorial-data-prep.md)
