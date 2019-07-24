---
title: Jupyter ノートブックの例
titleSuffix: Azure Machine Learning service
description: サンプルの Jupyter Notebook を検索して使用し、Azure Machine Learning service Python SDK を探索します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 05/29/2019
ms.custom: seodec18
ms.openlocfilehash: ea4d5a807c25ea0406b49dac8a83ef1a34e0e8b3
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391791"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Jupyter Notebook を使用して Azure Machine Learning service を探索する

[Azure Machine Learning Notebooks のリポジトリ](https://github.com/azure/machinelearningnotebooks)には、最新の Azure Machine Learning Python SDK のサンプルが含まれています。 これらの Juypter Notebook は、SDK の探索を支援し、独自の機械学習プロジェクトのモデルとして機能するように設計されています。

この記事では、次の環境からこのリポジトリにアクセスする方法を示します。

- [Azure Machine Learning Notebook VM](#azure-machine-learning-notebook-vm)
- [独自の Notebook サーバーを使用する](#bring-your-own-jupyter-notebook-server)
- [データ サイエンス仮想マシン](#data-science-virtual-machine)
- [Azure Notebooks](#azure-notebooks)

> [!NOTE]
> リポジトリを複製すると、**tutorials** フォルダー内にチュートリアル ノートブック、**how-to-use-azureml** フォルダー内に機能固有のノートブックがあることがわかります。

## <a name="azure-machine-learning-notebook-vm"></a>Azure Machine Learning Notebook VM

サンプルを開始する最も簡単な方法は、[クラウド ベースのノートブックに関するクイック スタート](quickstart-run-cloud-notebook.md)を完了することです。 完了すると、SDK とサンプル リポジトリが事前に読み込まれた専用の Notebook サーバーが用意されます。 ダウンロードやインストールは必要ありません。

## <a name="bring-your-own-jupyter-notebook-server"></a>独自の Jupyter Notebook サーバーを使用する

ローカル開発に独自の Notebook サーバーを使用するには、次の手順に従ってください。

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

これらの手順では、クイック スタートとチュートリアルのノートブックに必要な基本 SDK パッケージがインストールされます。 その他のサンプル ノートブックでは、追加のコンポーネントのインストールが必要になる場合があります。 詳細については、「[Install the Azure Machine Learning SDK for Python (Azure Machine Learning SDK for Python をインストールする)](https://docs.microsoft.com/python/api/overview/azure/ml/install)」を参照してください。

## <a name="data-science-virtual-machine"></a>データ サイエンス仮想マシン

データ サイエンス仮想マシン (DSVM) とは、データ サイエンスの実行専用に構築されたカスタマイズ済み VM イメージです。 [DSVM を作成する](how-to-configure-environment.md#dsvm)と、SDK と Notebook サーバーがユーザーのためにインストールされて構成されます。 ただし、その場合も、ワークスペースの作成とサンプル リポジトリの複製は必要になります。

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="azure-notebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com/) で、SDK と Notebook サーバーがユーザーのためにインストールされて構成されます。 Azure Notebooks は、ユーザーが探索できる、フル マネージド型で軽量の Notebook 環境を提供します。

Azure Notebooks のサンプル リポジトリにアクセスするには、[Azure portal](https://portal.azure.com) から Azure Machine Learning ワークスペースに移動します。 **[概要]** セクションから、 **[Azure Notebooks で開始する]** を選択します。

## <a name="next-steps"></a>次の手順

[サンプル ノートブック](https://aka.ms/aml-notebooks)を探索して、Azure Machine Learning service で実行できることを把握します。または、次のチュートリアルをお試しください。

- [MNIST を使用した画像分類モデルのトレーニングとデプロイ](tutorial-train-models-with-aml.md)

- [NYC タクシー データ セットを使用して回帰モデルをトレーニングするためのデータの準備と自動化された機械学習](tutorial-data-prep.md)