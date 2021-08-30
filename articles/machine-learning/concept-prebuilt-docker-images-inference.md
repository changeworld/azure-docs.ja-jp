---
title: 事前構築済み Docker イメージ
titleSuffix: Azure Machine Learning
description: Azure Machine Learning の推論 (スコアリング) 用の事前構築済み Docker イメージ
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 05/25/2021
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt
ms.openlocfilehash: 0548b309f84f46d7e1a8c904115167d59cc6f10e
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2021
ms.locfileid: "112417399"
---
# <a name="prebuilt-docker-images-for-inference-preview"></a>推論用の事前構築済み Docker イメージ (プレビュー)

推論用の事前構築済みの Docker コンテナー イメージ [(プレビュー)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) は、Azure Machine Learning でモデルをデプロイする際に使用します。  これらのイメージは、一般的な機械学習フレームワークと Python パッケージを使用して事前に構築されています。 また、次のいずれかの方法を使用して、パッケージを拡張して他のパッケージを追加することもできます。

* [Python パッケージを追加する](how-to-prebuilt-docker-images-inference-python-extensibility.md)。
* [新しい Dockerfile のベースとして事前構築済みの推論イメージを使用する](how-to-extend-prebuilt-docker-image-inference.md)。 この方法を使用すると、**Python パッケージと apt パッケージ** の両方をインストールできます。

## <a name="why-should-i-use-prebuilt-images"></a>事前構築済みのイメージを使用する理由

* モデル デプロイの待ち時間が短縮される。
* モデル デプロイの成功率が向上する。
* モデル デプロイ時に不要なイメージのビルドを回避できる。
* イメージまたはコンテナーに必要な依存関係とアクセス権のみが付与される。 

## <a name="list-of-prebuilt-docker-images-for-inference"></a>推論用の事前構築済み Docker イメージの一覧 

* すべての Docker イメージは、非ルート ユーザーとして実行されます。

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

## <a name="next-steps"></a>次の手順

* [Python パッケージを事前構築済みのイメージに追加する](how-to-prebuilt-docker-images-inference-python-extensibility.md)。
* [新しい Dockerfile のベースとして事前構築済みのパッケージを使用する](how-to-extend-prebuilt-docker-image-inference.md)。