---
title: キュレーションされた環境
titleSuffix: Azure Machine Learning
description: Azure Machine Learning のキュレーションされた環境について説明します。これは、一連の事前構成済みの環境であり、実験とデプロイの準備時間の短縮に役立ちます。
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 4/2/2021
ms.openlocfilehash: 16d5cbc1d48b4559ac34314582b9c01b7c6bb58c
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166651"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning のキュレーションされた環境

この記事では、Azure Machine Learning のキュレーション環境の一覧を示します。 キュレートされた環境は Azure Machine Learning から提供され、既定でお使いのワークスペースで利用できます。 Azure Machine Learning SDK の最新バージョンを使用したキャッシュ済みの Docker イメージでサポートされるため、実行準備コストが削減され、デプロイ時間を短縮できます。 このような環境を使用すれば、さまざまな機械学習フレームワークをすぐに使い始めることができます。

> [!NOTE]
> このリストは、2021 年 4 月時点の情報で更新されています。 環境とその依存関係の最新の一覧は、Python SDK または CLI を使用して取得してください。 詳細については、[環境に関する記事](./how-to-use-environments.md#use-a-curated-environment)を参照してください。 この新しいセットのリリース後、以前のキュレーションされた環境は非表示になりますが、引き続き使用することができます。 

## <a name="pytorch"></a>PyTorch
- AzureML-Pytorch1.7-Cuda11-OpenMpi4.1.0-py36
     - Azure ML SDK および追加の python パッケージを含む PyTorch を使用したディープ ラーニング用の環境。
     - PyTorch のバージョン: 1.7
     - Python のバージョン: 3.6.9
     - 基本イメージ: mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - CUDA のバージョン: 11.0.3
     - OpenMPI: 4.1.0

## <a name="scikit"></a>Scikit
- AzureML-Scikit-learn0.20.4-Cuda11-OpenMpi4.1.0-py36
     - 回帰、クラスタリング、Scikit-learn を用いた分類などのタスクのための環境。 Azure ML SDK と追加の python パッケージが含まれています。
     - Scikit-learn のバージョン: 20.4
     - Python のバージョン: 3.6.9
     - 基本イメージ: mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - CUDA のバージョン: 11.0.3
     - OpenMPI: 4.1.0

## <a name="tensorflow"></a>TensorFlow
- AzureML-TensorFlow2.4-Cuda11-OpenMpi4.1.0-py36
     - Azure ML SDK および追加の python パッケージを含む Tensorflow を使用したディープ ラーニング用の環境。
     - Tensorflow のバージョン: 2.4
     - Python のバージョン: 3.6.9
     - 基本イメージ: mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - CUDA のバージョン: 11.0.3
     - OpenMPI: 4.1.0
