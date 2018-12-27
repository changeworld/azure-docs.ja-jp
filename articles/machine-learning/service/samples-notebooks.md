---
title: Jupyter ノートブックの例
titleSuffix: Azure Machine Learning service
description: Python 内で Azure Machine Learning service を探索するための Jupyter Notebook の例を検索し、使用します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 0d74f731d0a7eca25238344e36838dc6c806c788
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434530"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Jupyter Notebook を使用して Azure Machine Learning service を探索する


作業を容易にするために、Azure Machine Learning service の探索に使用できる一連の Jupyter Python Notebook を開発しました。 

このサイトのドキュメントでサービスの使用方法を確認し、これらのノートブックを使用して、それらを自分の状況に合わせてカスタマイズします。 

## <a name="prerequisite"></a>前提条件

ワークスペースを作成して Azure Notebooks を起動するには、[Azure Machine Learning Python のクイック スタート](quickstart-get-started.md)を完了してください。

## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Azure Notebooks を試す:クラウド上の無料の Jupyter Notebook

Azure Notebooks の利用を開始するのは簡単です。 Azure Notebooks には [Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk) が既にインストールされて構成されています。 インストールと今後の更新プログラムは、Azure サービスを介して自動的に管理されます。
  
+ **コア チュートリアル ノートブック**を実行するには:
  1. [Azure Notebooks](https://notebooks.azure.com/) に移動します。
    
  1. 前提条件のクイック スタートで作成した **Getting Started** ライブラリ内で **tutorials** フォルダーを探します。
    
  1. 実行するノートブックを開きます。
    
+ **その他のノートブック**を実行するには:

  1. [サンプル ノートブックを Azure Notebooks にインポート](https://aka.ms/aml-clone-azure-notebooks)します。

  1. 次のいずれかの方法を使用して、ワークスペース構成ファイルをライブラリに追加します。
     + **config.json** ファイルを **Getting Started** ライブラリから新しい複製ライブラリにコピーします。

     + [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) 内のコードを使用して新しいワークスペースを作成します。
    
  1. 実行するノートブックを開きます。     


## <a name="use-a-data-science-virtual-machine-dsvm"></a>Data Science Virtual Machine (DSVM) を使用する

DSVM には [Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk) とノートブック サーバーが既にインストールされて構成されています。 次の手順を使用してノートブックを実行します。

1. [DSVM を作成](how-to-configure-environment.md#dsvm)します。

1. [GitHub リポジトリ](https://aka.ms/aml-notebooks)を複製します。

1. 次のいずれかの方法を使用して、ワークスペース構成ファイルをライブラリに追加します。
    * 前提条件のクイック スタートを使用して作成した **aml_config\config.json** ファイルを複製されたディレクトリにコピーします。

    * [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) 内のコードを使用して新しいワークスペースを作成します。

1. 複製したディレクトリから、Notebook サーバーを起動します。

## <a name="use-your-own-jupyter-notebook-server"></a>自分の Jupyter Notebook サーバーを使用する

次の手順を使用して、コンピューターにローカルの Jupyter Notebook サーバーを作成します。

1. Azure Machine Learning SDK をインストールする前提条件のクイック スタートを完了したことを確認します。

1. [GitHub リポジトリ](https://aka.ms/aml-notebooks)を複製します。

1. 次のいずれかの方法を使用して、ワークスペース構成ファイルをライブラリに追加します。
    * 前提条件のクイック スタートを使用して作成した **aml_config\config.json** ファイルを複製されたディレクトリにコピーします。
    
    * [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) 内のコードを使用して新しいワークスペースを作成します。

1. 複製したディレクトリから、Notebook サーバーを起動します。

1. ノートブックが格納されているフォルダーに移動します。

1. ノートブックを開きます。

<a name="auto"></a>

## <a name="automated-ml-setup"></a>自動化された ML セットアップ 

**これらの手順は、`automated-machine-learning` フォルダー内のノートブックにのみ適用されます。**

上記のオプションのどれでも使用できますが、次の手順に従って、環境のインストールとワークスペースの作成を同時に行うこともできます。 

1. [Mini-conda](https://conda.io/miniconda.html) をインストールします。 3.7 またはそれ以降を選択します。 プロンプトに従ってインストールします。 
   >[!NOTE]
   >4.4.10 以降のバージョンであれば、既存の conda を使用できます。 `conda -V` を使用してバージョンを表示します。 `conda update conda` コマンドを使用して conda バージョンを更新することができます。 mini-conda を明示的にインストールする必要はありません。

1. [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) からサンプルのノートブックを zip ファイルとしてダウンロードし、内容をローカル ディレクトリに抽出します。 自動化された機械学習のノートブックは、`how-to-use-azureml/automated-machine-learning` フォルダーにあります。

1. 新しい Conda 環境を設定します。 
   1. ローカル コンピューターに Conda プロンプトを開きます。
   
   1. ローカル コンピューターに抽出したファイルに移動します。
   
   1. `automated-machine-learning` フォルダーを開きます。
   
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

1. 自動化された機械学習のフォルダーを開き、`configuration.ipynb` ノートブックを開きます。 

1. ノートブック内のセルを実行して、Machine Learning Services リソース プロバイダーを登録し、ワークスペースを作成します。

これで、ローカル コンピューターに保存されたノートブックを開いて実行する準備が整いました。


## <a name="next-steps"></a>次の手順

[Azure Machine Learning service 用の GitHub ノートブック リポジトリ](https://aka.ms/aml-notebooks)

以下のチュートリアルを試します:
+ [MNIST を使用した画像分類モデルのトレーニングとデプロイ](tutorial-train-models-with-aml.md)

+ [NYC タクシー データ セットを使用して回帰モデルをトレーニングするためのデータの準備と自動化された機械学習](tutorial-data-prep.md)
