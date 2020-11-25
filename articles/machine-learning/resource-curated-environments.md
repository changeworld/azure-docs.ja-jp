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
ms.date: 11/16/2020
ms.openlocfilehash: 0e6817e03c5e5363ad925367b0632e26fc2da4a2
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701400"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning のキュレーションされた環境

この記事では、Azure Machine Learning のキュレーションされた環境、およびそれらにプレインストールされているパッケージとチャネルの一覧を示します。 キュレートされた環境は Azure Machine Learning から提供され、既定でお使いのワークスペースで利用できます。 キャッシュされた Docker イメージでサポートされるため、実行準備コストが削減され、デプロイ時間を短縮できます。 このような環境を使用すれば、さまざまな機械学習フレームワークをすぐに使い始めることができます。

> [!NOTE]
> このリストは、2020 年 9 月時点の情報で更新されています。 Python SDK を使用して、更新された最新のリストを取得します。 詳細については、[環境に関する記事](./how-to-use-environments.md#use-a-curated-environment)を参照してください。

## <a name="azureml-automl"></a>AzureML-AutoML

**パッケージ チャネル:**

* anaconda
* conda-forge
* pytorch

**Conda パッケージ:**

* Python
* numpy
* scikit-learn
* pandas
* py-xgboost
* fbprophet
* 祝日
* setuptools-git
* psutil

**Pip パッケージ:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-defaults
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inference-schema
* py-cpuinfo

## <a name="azureml-automl-dnn"></a>AzureML-AutoML-DNN

**パッケージ チャネル:**

* anaconda
* conda-forge
* pytorch

**Conda パッケージ:**

* Python
* numpy
* scikit-learn
* pandas
* py-xgboost
* fbprophet
* 祝日
* setuptools-git
* pytorch
* cudatoolkit
* psutil

**Pip パッケージ:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-defaults
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inference-schema
* pytorch-transformers
* spacy
* en_core_web_sm
* py-cpuinfo

## <a name="azureml-automl-dnn-gpu"></a>AzureML-AutoML-DNN-GPU

**パッケージ チャネル:**

* anaconda
* conda-forge
* pytorch

**Conda パッケージ:**

* Python
* numpy
* scikit-learn
* pandas
* fbprophet
* 祝日
* setuptools-git
* pytorch
* cudatoolkit
* psutil

**Pip パッケージ:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-defaults
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inference-schema
* horovod
* pytorch-transformers
* spacy
* en_core_web_sm
* py-cpuinfo

## <a name="azureml-automl-dnn-vision-gpu"></a>AzureML-AutoML-DNN-Vision-GPU

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-dataset-runtime
* azureml-contrib-dataset
* azureml-telemetry
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-defaults
* azureml-interpret
* azureml-explain-model
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-contrib-automl-dnn-vision

## <a name="azureml-automl-gpu"></a>AzureML-AutoML-GPU

**パッケージ チャネル:**

* anaconda
* conda-forge
* pytorch

**Conda パッケージ:**

* Python
* numpy
* scikit-learn
* pandas
* fbprophet
* 祝日
* setuptools-git
* psutil

**Pip パッケージ:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-defaults
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inference-schema
* py-cpuinfo

## <a name="azureml-chainer-510-cpu"></a>AzureML-Chainer-5.1.0-CPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* chainer
* mpi4py

## <a name="azureml-chainer-510-gpu"></a>AzureML-Chainer-5.1.0-GPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* chainer
* cupy-cuda90
* mpi4py

## <a name="azureml-dask-cpu"></a>AzureML-Dask-CPU

**パッケージ チャネル:**

* conda-forge
* pytorch
* 既定値

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* adlfs
* azureml-core
* azureml-dataset-runtime
* dask[complete]
* dask-ml[complete]
* distributed
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* ノートブック
* pyarrow

## <a name="azureml-dask-gpu"></a>AzureML-Dask-GPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python
* matplotlib

**Pip パッケージ:**

* azureml-defaults
* adlfs
* azureml-core
* dask[complete]
* dask-ml[complete]
* distributed
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* ノートブック
* pyarrow

## <a name="azureml-hyperdrive-forecastdnn"></a>AzureML-Hyperdrive-ForecastDNN

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-defaults
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-contrib-automl-dnn-forecasting

## <a name="azureml-minimal"></a>AzureML-Minimal

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults

## <a name="azureml-pyspark-mmlspark-015"></a>AzureML-PySpark-MmlSpark-0.15

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core

## <a name="azureml-pytorch-10-cpu"></a>AzureML-PyTorch-1.0-CPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-10-gpu"></a>AzureML-PyTorch-1.0-GPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-11-cpu"></a>AzureML-PyTorch-1.1-CPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* TensorBoard
* future

## <a name="azureml-pytorch-11-gpu"></a>AzureML-PyTorch-1.1-GPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* TensorBoard
* future

## <a name="azureml-pytorch-12-cpu"></a>AzureML-PyTorch-1.2-CPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* TensorBoard
* future

## <a name="azureml-pytorch-12-gpu"></a>AzureML-PyTorch-1.2-GPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* TensorBoard
* future

## <a name="azureml-pytorch-13-cpu"></a>AzureML-PyTorch-1.3-CPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* TensorBoard
* future

## <a name="azureml-pytorch-13-gpu"></a>AzureML-PyTorch-1.3-GPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* TensorBoard
* future

## <a name="azureml-pytorch-14-cpu"></a>AzureML-PyTorch-1.4-CPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* TensorBoard
* future

## <a name="azureml-pytorch-14-gpu"></a>AzureML-PyTorch-1.4-GPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* TensorBoard
* future

## <a name="azureml-pytorch-15-cpu"></a>AzureML-PyTorch-1.5-CPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* TensorBoard
* future

## <a name="azureml-pytorch-15-gpu"></a>AzureML-PyTorch-1.5-GPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* TensorBoard
* future

## <a name="azureml-pytorch-16-cpu"></a>AzureML-PyTorch-1.6-CPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* TensorBoard
* future

## <a name="azureml-pytorch-16-gpu"></a>AzureML-PyTorch-1.6-GPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* torch
* torchvision
* mkl
* horovod
* TensorBoard
* future

## <a name="azureml-scikit-learn-0203"></a>AzureML-Scikit-learn-0.20.3

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* scikit-learn
* scipy
* joblib

## <a name="azureml-tensorflow-110-cpu"></a>AzureML-TensorFlow-1.10-CPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-110-gpu"></a>AzureML-TensorFlow-1.10-GPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-112-cpu"></a>AzureML-TensorFlow-1.12-CPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-112-gpu"></a>AzureML-TensorFlow-1.12-GPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-113-cpu"></a>AzureML-TensorFlow-1.13-CPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-113-gpu"></a>AzureML-TensorFlow-1.13-GPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-20-cpu"></a>AzureML-TensorFlow-2.0-CPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-20-gpu"></a>AzureML-TensorFlow-2.0-GPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-21-cpu"></a>AzureML-TensorFlow-2.1-CPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-21-gpu"></a>AzureML-TensorFlow-2.1-GPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-22-cpu"></a>AzureML-TensorFlow-2.2-CPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-22-gpu"></a>AzureML-TensorFlow-2.2-GPU

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tutorial"></a>AzureML-Tutorial

**パッケージ チャネル:**

* anaconda
* conda-forge

**Conda パッケージ:**

* Python
* pandas
* numpy
* tqdm
* scikit-learn
* matplotlib

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* azureml-widgets
* azureml-pipeline-core
* azureml-pipeline-steps
* azureml-opendatasets
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-train
* azureml-sdk
* azureml-interpret
* azureml-tensorboard
* azureml-mlflow
* mlflow
* sklearn-pandas

## <a name="azureml-vowpalwabbit-880"></a>AzureML-VowpalWabbit-8.8.0

**パッケージ チャネル:**

* conda-forge

**Conda パッケージ:**

* Python

**Pip パッケージ:**

* azureml-core
* azureml-defaults
* azureml-dataset-runtime[fuse,pandas]
