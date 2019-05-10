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
ms.openlocfilehash: cd88fd85ce6d18287c700a54e42b6237a42ea5c9
ms.sourcegitcommit: eea74d11a6d6ea6d187e90e368e70e46b76cd2aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035369"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Jupyter Notebook を使用して Azure Machine Learning service を探索する

作業を容易にするために、Azure Machine Learning service の探索に使用できる一連の Jupyter Python Notebook を開発しました。 

このサイトのドキュメントでサービスの使用方法を確認し、これらのノートブックを使用して、それらを自分の状況に合わせてカスタマイズします。 

以下のいずれかのパスを使用して、これらのサンプル ノートブックと共にノートブック サーバーを稼働します。  サーバーを稼働したら、**tutorials** フォルダーでチュートリアル ノートブックを見つけます。または、**how-to-use-azureml** フォルダーでさまざまな機能を確認します。

## <a name="a-managed-cloud-notebook-server"></a>マネージド クラウド ノートブック サーバー

独自のクラウド ベースのノートブック サーバーで簡単に開始できます。 このクラウド リソースを作成すると、サンプル ノートブックと [Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk) が既にインストールされて構成されています。  

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* これらのサンプルは、ノートブックの Web ページで使用できます。

## <a name="a-data-science-virtual-machine-dsvm"></a>A Data Science Virtual Machine (DSVM)

DSVM には [Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk) とノートブック サーバーが既にインストールされて構成されています。 

[DSVM を作成](how-to-configure-environment.md#dsvm)した後、DSVM でこれらの手順を使用してノートブックを実行します。

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="your-own-jupyter-notebook-server"></a>独自の Jupyter Notebook サーバー

次の手順を使用して、コンピューターにローカルの Jupyter Notebook サーバーを作成します。

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

この設定の各手順では、クイックスタートとチュートリアルのノートブックを実行するために必要なパッケージをインストールします。  他のサンプル ノートブックでは、さらに別のコンポーネントのインストールが必要になることがあります。  これらのコンポーネントの詳細については、「[Install the Azure Machine Learning SDK for Python (Azure Machine Learning SDK for Python をインストールする)](https://docs.microsoft.com/python/api/overview/azure/ml/install)」を参照してください。

## <a name="azure-notebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com/) には、サンプル ノートブックと [Azure Machine Learning SDK for Python](https://aka.ms/aml-sdk) が既にインストールされて構成されています。 インストールと今後の更新プログラムは、Azure サービスを介して自動的に管理されます。

Azure Notebooks を使い始めるには、[Azure portal](https://portal.azure.com) を使用します。  ワークスペースを開き、**[概要]** セクションの **[Azure Notebooks で開始する]** を選択します。

## <a name="next-steps"></a>次の手順

+ この GitHub リポジトリ (https://aka.ms/aml-notebooks) で、Azure Machine Learning service のサンプル ノートブックの詳細を確認します

以下のチュートリアルを試します:
+ [MNIST を使用した画像分類モデルのトレーニングとデプロイ](tutorial-train-models-with-aml.md)

+ [NYC タクシー データ セットを使用して回帰モデルをトレーニングするためのデータの準備と自動化された機械学習](tutorial-data-prep.md)