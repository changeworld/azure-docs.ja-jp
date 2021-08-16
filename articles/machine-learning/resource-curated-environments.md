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
ms.openlocfilehash: 1bea195d6f08832415a16531212d176ada7402e6
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110612577"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning のキュレーションされた環境

この記事では、Azure Machine Learning のキュレーション環境の一覧を示します。 キュレートされた環境は Azure Machine Learning から提供され、既定でお使いのワークスペースで利用できます。 Azure Machine Learning SDK の最新バージョンを使用したキャッシュ済みの Docker イメージでサポートされるため、実行準備コストが削減され、デプロイ時間を短縮できます。 このような環境を使用すれば、さまざまな機械学習フレームワークをすぐに使い始めることができます。

> [!NOTE]
> このリストは、2021 年 4 月時点の情報で更新されています。 環境とその依存関係の最新の一覧は、Python SDK または CLI を使用して取得してください。 詳細については、[環境に関する記事](./how-to-use-environments.md#use-a-curated-environment)を参照してください。 この新しいセットのリリース後、以前のキュレーションされた環境は非表示になりますが、引き続き使用することができます。 

## <a name="pytorch"></a>PyTorch
- AzureML-pytorch-1.7-ubuntu18.04-py37-cuda11-gpu
     - Azure ML SDK および追加の python パッケージを含む PyTorch を使用したディープ ラーニング用の環境。
     - PyTorch のバージョン: 1.7
     - Python バージョン 3.7
     - 基本イメージ: mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - CUDA のバージョン: 11.0.3
     - OpenMPI バージョン: 4.1.0
     - Ubuntu バージョン: 18.04

## <a name="sklearn"></a>Sklearn
- AzureML-sklearn-0.24-ubuntu18.04-py37-cuda11-gpu
     - 回帰、クラスタリング、Scikit-learn を用いた分類などのタスクのための環境。 Azure ML SDK と追加の python パッケージが含まれています。
     - Scikit-learn のバージョン: 24.1
     - Python バージョン 3.7
     - 基本イメージ: mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - CUDA のバージョン: 11.0.3
     - OpenMPI バージョン: 4.1.0
     - Ubuntu バージョン: 18.04

## <a name="tensorflow"></a>TensorFlow
- AzureML-tensorflow-2.4-ubuntu18.04-py37-cuda11-gpu
     - Azure ML SDK および追加の python パッケージを含む Tensorflow を使用したディープ ラーニング用の環境。
     - Tensorflow のバージョン: 2.4
     - Python バージョン 3.7
     - 基本イメージ: mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04
     - CUDA のバージョン: 11.0.3
     - OpenMPI バージョン: 4.1.0
     - Ubuntu バージョン: 18.04

## <a name="inference-only-curated-environments-and-prebuilt-docker-images"></a>推論専用のキュレーションされた環境と事前構築済みの Docker イメージ
- 事前構築済みの Docker イメージの推論専用キュレーション環境と MCR パスについては、[推論用の事前構築済みの Docker イメージ](concept-prebuilt-docker-images-inference.md#list-of-prebuilt-docker-images-for-inference)に関するページを参照してください。
