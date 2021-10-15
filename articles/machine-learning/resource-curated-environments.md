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
ms.date: 07/08/2021
ms.openlocfilehash: b66f76d80d3dad6b24eefa6fb7bbd88cfce082e0
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360025"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning のキュレーションされた環境

この記事では、Azure Machine Learning のキュレーション環境と最新のフレームワークのバージョンの一覧を示します。 キュレートされた環境は Azure Machine Learning から提供され、既定でお使いのワークスペースで利用できます。 Azure Machine Learning SDK の最新バージョンを使用したキャッシュ済みの Docker イメージでサポートされるため、実行準備コストが削減され、デプロイ時間を短縮できます。 このような環境を使用すれば、さまざまな機械学習フレームワークをすぐに使い始めることができます。

> [!NOTE]
> 環境とその依存関係の完全な一覧を取得するには、[Python SDK](how-to-use-environments.md)、[CLI](/cli/azure/ml/environment?view=azure-cli-latest&preserve-view=true#az_ml_environment_list)、または Azure Machine Learning [スタジオ](how-to-manage-environments-in-studio.md)を使用してください。 詳細については、[環境に関する記事](how-to-use-environments.md#use-a-curated-environment)を参照してください。 

## <a name="training-curated-environments"></a>キュレートされた環境のトレーニング

### <a name="pytorch"></a>PyTorch

**名前**: AzureML-pytorch-1.9-ubuntu18.04-py37-cuda11-gpu  
**説明**: AzureML Python SDK と追加の python パッケージを含む PyTorch を使用したディープ ラーニング用の環境。  

個人用ワークフローに合わせて、次の Dockerfile をカスタマイズできます。

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.1-cudnn8-ubuntu18.04:20210922.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/pytorch-1.9

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 \
    pip=20.2.4 \
    pytorch=1.9.0 \
    torchvision=0.10.0 \
    torchaudio=0.9.0 \
    cudatoolkit=11.1.1 \
    nvidia-apex=0.1.0 \
    -c anaconda -c pytorch -c conda-forge

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_PYTORCH=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'ipykernel~=6.0' \
                'azureml-core==1.34.0' \
                'azureml-defaults==1.34.0' \
                'azureml-mlflow==1.34.0' \
                'azureml-telemetry==1.34.0' \
                'tensorboard==2.4.0' \
                'tensorflow-gpu==2.4.1' \
                'onnxruntime-gpu>=1.7,<1.8' \
                'horovod[pytorch]==0.21.3' \
                'future==0.17.1' \
                'torch-tb-profiler==0.2.1'


# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

その他の利用可能な PyTorch 環境:
* AzureML-pytorch-1.8-ubuntu18.04-py37-cuda11-gpu
* AzureML-pytorch-1.7-ubuntu18.04-py37-cuda11-gpu

### <a name="lightgbm"></a>LightGBM

**名前**: AzureML-lightgbm-3.2-ubuntu18.04-py37-cpu  
**説明**: AzureML Python SDK と追加のパッケージを含む Scikit-learn、LightGBM、XGBoost、Dask を使用した機械学習用の環境。  

個人用ワークフローに合わせて、次の Dockerfile をカスタマイズできます。

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210922.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/lightgbm

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_TENSORFLOW=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'numpy>=1.10,<1.20' \
                'scipy~=1.5.0' \
                'scikit-learn~=0.24.1' \
                'xgboost~=1.4.0' \
                'lightgbm~=3.2.0' \
                'dask~=2021.6.0' \
                'distributed~=2021.6.0' \
                'dask-ml~=1.9.0' \
                'adlfs~=0.7.0' \
                'ipykernel~=6.0' \
                'azureml-core==1.34.0' \
                'azureml-defaults==1.34.0' \
                'azureml-mlflow==1.34.0' \
                'azureml-telemetry==1.34.0'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

### <a name="sklearn"></a>Sklearn
**名前**: AzureML-sklearn-0.24-ubuntu18.04-py37-cuda11-gpu  
**説明**: 回帰、クラスタリング、Scikit-learn を用いた分類などのタスクのための環境。 AzureML Python SDK と追加の python パッケージが含まれています。  

個人用ワークフローに合わせて、次の Dockerfile をカスタマイズできます。

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210922.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/sklearn-0.24.1

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'ipykernel~=6.0' \
                'azureml-core==1.34.0' \
                'azureml-defaults==1.34.0' \
                'azureml-mlflow==1.34.0' \
                'azureml-telemetry==1.34.0' \
                'scikit-learn==0.24.1'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

### <a name="tensorflow"></a>TensorFlow

**名前**: AzureML-tensorflow-2.4-ubuntu18.04-py37-cuda11-gpu  
**説明**: AzureML Python SDK と追加の python パッケージを含む Tensorflow を使用したディープ ラーニング用の環境。  

個人用ワークフローに合わせて、次の Dockerfile をカスタマイズできます。

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04:20210922.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/tensorflow-2.4

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_TENSORFLOW=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'ipykernel~=6.0' \
                'azureml-core==1.34.0' \
                'azureml-defaults==1.34.0' \
                'azureml-mlflow==1.34.0' \
                'azureml-telemetry==1.34.0' \
                'tensorboard==2.4.0' \
                'tensorflow-gpu==2.4.1' \
                'tensorflow-datasets==4.3.0' \
                'onnxruntime-gpu>=1.7,<1.8' \
                'horovod[tensorflow-gpu]==0.21.3'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="automated-ml-automl"></a>自動 ML (AutoML)

AutoML を使用する Azure ML パイプライン トレーニング ワークフローでは、コンピューティングの種類と DNN が有効になっているかどうかに基づいて、キュレーションされた環境が自動的に選択されます。 AutoML には、次のキュレーションされた環境が用意されています。

| 名前 | コンピューティングの種類 | DNN が有効か |
| --- | --- | --- |
|AzureML-AutoML | CPU | いいえ |
|AzureML-AutoML-DNN | CPU | はい |
| AzureML-AutoML-GPU | GPU | いいえ |
| AzureML-AutoML-DNN-GPU | GPU | はい |

AutoML と Azure ML パイプラインの詳細については、「[Python の Azure Machine Learning パイプラインで自動 ML を使用する](how-to-use-automlstep-in-pipelines.md)」を参照してください。

## <a name="inference-only-curated-environments-and-prebuilt-docker-images"></a>推論専用のキュレーションされた環境と事前構築済みの Docker イメージ

* すべての Docker イメージは、非ルート ユーザーとして実行されます。
* Docker イメージに `latest` タグを使用することをお勧めします。 推論用の構築済みの Docker イメージは、Microsoft Container Registry (MCR) に発行され、使用可能なタグの一覧をクエリするには、[GitHub リポジトリの指示](https://github.com/microsoft/ContainerRegistry#browsing-mcr-content)に従います。

### <a name="tensorflow"></a>TensorFlow

Framework のバージョン | CPU/GPU | 事前インストール済みパッケージ | MCR パス | キュレーション環境
 --- | --- | --- | --- | --- |
 1.15 | CPU | pandas==0.25.1 </br> numpy=1.20.1 | `mcr.microsoft.com/azureml/tensorflow-1.15-ubuntu18.04-py37-cpu-inference:latest`  | AzureML-tensorflow-1.15-ubuntu18.04-py37-cpu-inference | 
2.4 | CPU | numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/tensorflow-2.4-ubuntu18.04-py37-cpu-inference:latest` | AzureML-tensorflow-2.4-ubuntu18.04-py37-cpu-inference |
2.4 | GPU | numpy >= 1.16.0 </br> pandas~=1.1.x </br> CUDA==11.0.3 </br> CuDNN==8.0.5.39 | `mcr.microsoft.com/azureml/tensorflow-2.4-ubuntu18.04-py37-cuda11.0.3-gpu-inference:latest` | AzureML-tensorflow-2.4-ubuntu18.04-py37-cuda11.0.3-gpu-inference |

### <a name="pytorch"></a>PyTorch

Framework のバージョン | CPU/GPU | 事前インストール済みパッケージ | MCR パス | キュレーション環境
 --- | --- | --- | --- | --- |
 1.6 | CPU | numpy==1.20.1 </br> pandas==0.25.1 | `mcr.microsoft.com/azureml/pytorch-1.6-ubuntu18.04-py37-cpu-inference:latest` | AzureML-pytorch-1.6-ubuntu18.04-py37-cpu-inference |
1.7 | CPU | numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/pytorch-1.7-ubuntu18.04-py37-cpu-inference:latest` | AzureML-pytorch-1.7-ubuntu18.04-py37-cpu-inference |

### <a name="scikit-learn"></a>SciKit-Learn

Framework のバージョン | CPU/GPU | 事前インストール済みパッケージ | MCR パス | キュレーション環境
 --- | --- | --- | --- | --- |
0.24.1  | CPU | scikit-learn==0.24.1 </br> numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/sklearn-0.24.1-ubuntu18.04-py37-cpu-inference:latest` | AzureML-sklearn-0.24.1-ubuntu18.04-py37-cpu-inference |

### <a name="onnx-runtime"></a>ONNX Runtime

Framework のバージョン | CPU/GPU | 事前インストール済みパッケージ | MCR パス | キュレーション環境
 --- | --- | --- | --- | --- |
1.6 | CPU | numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/onnxruntime-1.6-ubuntu18.04-py37-cpu-inference:latest` |AzureML-onnxruntime-1.6-ubuntu18.04-py37-cpu-inference |

### <a name="xgboost"></a>XGBoost

Framework のバージョン | CPU/GPU | 事前インストール済みパッケージ | MCR パス | キュレーション環境
 --- | --- | --- | --- | --- |
0.9 | CPU | scikit-learn==0.23.2 </br> numpy==1.20.1 </br> pandas==0.25.1 | `mcr.microsoft.com/azureml/xgboost-0.9-ubuntu18.04-py37-cpu-inference:latest` | AzureML-xgboost-0.9-ubuntu18.04-py37-cpu-inference | 

### <a name="no-framework"></a>フレームワークなし

Framework のバージョン | CPU/GPU | 事前インストール済みパッケージ | MCR パス | キュレーション環境
 --- | --- | --- | --- | --- |
NA | CPU | NA | `mcr.microsoft.com/azureml/minimal-ubuntu18.04-py37-cpu-inference:latest` | AzureML-minimal-ubuntu18.04-py37-cpu-inference  |


## <a name="security"></a>セキュリティ
サポートされている環境に対するバージョン更新は、30 日間以上経過していない脆弱性に対処するために 2 週間ごとにリリースされます。 

