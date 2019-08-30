---
title: ディープ ラーニングと AI のフレームワーク
titleSuffix: Azure Data Science Virtual Machines
description: TensorFlow、PyTorch、Keras、Caffe、MXNet、Horovod、Theano、Chainer などを含む、Azure Data Science Virtual Machine 上のディープ ラーニング フレームワークおよびツール。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2019
ms.author: vijetaj
ms.openlocfilehash: 3c9510b99bbdc7db292ecdc0bf59aadc305cf0c8
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035840"
---
# <a name="deep-learning-and-ai-frameworks"></a>ディープ ラーニングと AI のフレームワーク
[Data Science Virtual Machine](https://aka.ms/dsvm) (DSVM) は、イメージや言語理解などの予測分析および認識機能を使用して人工知能 (AI) アプリケーションを構築するために役立つ多数のディープ ラーニング フレームワークをサポートしています。

DSVM で使用できるディープ ラーニング フレームワークには、次のものがあります。

+ TensorFlow
+ PyTorch
+ Keras
+ Caffe
+ Caffe2
+ Torch
+ MXNet モデル サーバー
+ MXNet
+ Horovod
+ Theano
+ Chainer
+ Deep Water
+ NVIDIA DIGITS
+ nvidia-smi
+ TensorFlow Serving
+ TensorRT
+ Microsoft Cognitive Toolkit

|DSVM 上の&nbsp;DL&nbsp;ツール&nbsp;|Windows|Linux|使用上の注意&nbsp;|
|---------|:-------------------:|:------------------:|-----|
|[TensorFlow](https://www.tensorflow.org/) | はい (Windows 2016) | はい |[Linux および Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) では Python 3.5、[Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) では Python 3.6 でインストールされます。 DSVM では、サンプルの Jupyter ノートブックが含まれています。<br/><br/>**これを実行するには**:<br/>* ターミナル: 適切な環境をアクティブ化した後、Python を実行します。 <br/> * Jupyter: [Jupyter](provision-vm.md#tools) または [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) に接続し、サンプルの保存されている TensorFlow ディレクトリを開きます。  |
|[PyTorch](https://pytorch.org/)| いいえ | はい |[Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) でインストールされます。 サンプルの Jupyter ノートブックが含まれており、サンプルは /dsvm/samples/pytorch にあります。    <br/><br/>**これを実行するには**<br/>* ターミナル: 適切な環境をアクティブ化した後、Python を実行します。<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux): 接続した後、サンプル用の PyTorch ディレクトリを開きます。  |
|[Keras](https://keras.io/)| はい | はい |API は [Linux および Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) では Python 3.5、[Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) では Python 3.6 でインストールされます。 [サンプル](https://github.com/fchollet/keras/tree/master/examples)<br/><br/>**これを実行するには**:<br/>* ターミナル: 適切な環境をアクティブ化した後、Python を実行します。 <br/> * Jupyter: GitHub の場所からサンプルをダウンロードし、[Jupyter](provision-vm.md#tools) または [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) に接続してサンプル ディレクトリを開きます。 |
|[Caffe](https://github.com/caffe2/caffe2) | いいえ |はい (Ubuntu)|Caffe は `/opt/caffe` にインストールされます。   サンプルは `/opt/caffe/examples` にあります。 <br/><br/>**これを実行するには**、X2Go を使用して VM にログインした後、新しいターミナルを起動して次のように入力します。<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>新しいブラウザー ウィンドウでサンプルのノートブックが開きます。 バイナリは、/opt/caffe/build/install/bin にインストールされています。<br/><br/>Caffe のインストールされるバージョンは Python 2.7 を必要とします。既定でアクティブ化される Python 3.5 では機能しません。 Python 2.7 に切り替えるには、`source activate root` を実行して Anaconda 環境を切り替えます。|
|[Caffe2](https://github.com/caffe2/caffe2) | いいえ |はい (Ubuntu)|Caffe2 は、[Python 2.7 (ルート) conda 環境](dsvm-languages.md#python-linux-and-windows-server-2012-edition)にインストールされています。 ソースは、`/opt/caffe2` にあります。<br/>サンプル ノートブックは、JupyterHub に含まれています。<br/><br/>**これを実行するには**:<br/>* ターミナルで、[ルート Python 環境](dsvm-languages.md#python-linux-and-windows-server-2012-edition)をアクティブ化して Python を起動し、caffe2 をインポートします。 <br/> * JupyterHub で、[JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) に接続し、サンプル ノートブックが保存されている Caffe2 ディレクトリに移動します。 一部のノートブックについては、Caffe2 ルートを Python コードで設定する必要があります。/opt/caffe2 と入力します。 |
|[Torch](http://torch.ch/) | いいえ |はい (Ubuntu)|Torch は `/dsvm/tools/torch` にインストールされます。 PyTorch は Python 2.7 (_root_) および Python 3.5 (_py35_) 環境にインストールされます。 Torch サンプルは `/dsvm/samples/torch` にあり、PyTorch サンプルは `/dsvm/samples/pytorch` にあります。 |
|[MXNet](https://mxnet.io/) | はい (Windows 2016) | はい|MXNet は、Windows では `C:\dsvm\tools\mxnet` に、Linux では `/dsvm/tools/mxnet` にインストールされます。 Python バインディングは、[Linux および Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 上の Python 3.5 および [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) 上の Python 3.6 にインストールされています。 Ubuntu にはR バインディングもインストールされています。<br/><br/>サンプル Jupyter ノートブックが含まれています。 <br/><br/>**これを実行するには**:<br/>* ターミナル: 適切な環境をアクティブ化した後、Python を実行します。 <br/> * Jupyter: [Jupyter](provision-vm.md#tools) または [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) に接続し、サンプルの保存されている mxnet ディレクトリを開きます。|
|[MXNet モデル サーバー](https://github.com/awslabs/mxnet-model-server) | いいえ | はい |MXNet および ONNX モデルの HTTP エンドポイントを作成するサーバー。 _mxnet-model-server_ は端末で利用可能 サンプルは [MXNet モデル サーバーのページ](https://github.com/awslabs/mxnet-model-server)にあります。|
|[Horovod](https://github.com/uber/horovod) | いいえ | はい (Ubuntu) |TensorFlow のための分散ディープ ラーニング フレームワーク。 Horovod は、[Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 上の Python 3.5 にインストールされています。  [サンプル](https://github.com/uber/horovod/tree/master/examples)を参照してください。<br/><br/>**これを実行するには**、ターミナルで適切な環境をアクティブ化した後、Python を実行します。 |
|[Theano](https://github.com/Theano/Theano) | いいえ | はい (Ubuntu) |Theano は Python 2.7 (_root_) および Python 3.5 (_py35_) 環境にインストールされます。<br/><br/>**これを実行するには**: <br/>* ターミナル: 必要な Python バージョン (root または py35) をアクティブ化し、python を実行した後、theano をインポートします。<br/>* Jupyter: Python 2.7 または 3.5 カーネルを選択した後、theano をインポートします。  <br/>最近の MKL バグを回避するには、まず、MKL スレッドレイヤーを設定する必要があります。<br/><br/>_export MKL_THREADING_LAYER=GNU_|
|[Chainer](https://chainer.org/) |いいえ | はい |Chainer は、[Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) にインストールされています。 ChainerRL と ChainerCV もインストールされます。 <br/><br/>サンプル ノートブックは、JupyterHub に含まれています。<br/><br/>**これを実行するには**: <br/>* ターミナル: [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 環境をアクティブ化し、_python_ を実行した後、chainer をインポートします。 <br/> * JupyterHub: [JupyterHub に接続](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)した後、Chainer ディレクトリに移動してサンプル ノートブックを見つけます。|
|[NVidia Digits](https://github.com/NVIDIA/DIGITS) | いいえ | はい (Ubuntu) |ディープ ラーニング モデルを迅速にトレーニングするための NVIDIA のディープ ラーニング システム。 DIGITS は `/dsvm/tools/DIGITS` にインストールされ、_digits_ という名前のサービスとして利用できます。  <br/><br/>**これを実行するには**: <br/>X2Go で VM にログインします。 ターミナルで、```sudo systemctl start digits``` でサービスを開始します。 <br/><br/>サービスが開始するまで 1 分ほどかかります。 Web ブラウザーを起動し、`http://localhost:5000` にアクセスします。 DIGITS ではセキュリティ保護されたログインが提供されないので、VM の外部には公開しないでください。|
|[CUDA、cuDNN、NVIDIA ドライバー](https://developer.nvidia.com/cuda-toolkit) |はい | はい | |
|nvidia-smi|はい | はい |GPU アクティビティのクエリを実行するための NVIDIA ツール。 _nvidia-smi_ はシステム パスから実行できます。 <br/><br/>コマンド プロンプト (Windows) またはターミナル (Linux) を起動し、_nvidia-smi_ を実行します。|
|[TensorFlow Serving](https://www.tensorflow.org/serving/) | いいえ | はい |TensorFlow モデルで推論を実行するサーバー。 _tensorflow_model_server_ は端末で利用可能 サンプルは[オンラインで](https://www.tensorflow.org/serving/)利用可能|
|[TensorRT](https://developer.nvidia.com/tensorrt) |  いいえ | はい (Ubuntu) |NVIDIA からの深層学習推論サーバーです。 TensorRT は _apt_ パッケージとしてインストールされています。 サンプルは[オンラインで](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples)利用可能|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|はい | はい | [Linux および Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) では Python 3.5、[Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) では Python 3.6 でインストールされます。 DSVM では、サンプルの Jupyter ノートブックが含まれています。 <br/><br/>**これを実行するには**: <br/>ターミナル: 適切な環境をアクティブ化し、Python を実行します。 <br/>Jupyter: [Jupyter](provision-vm.md#tools) または [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux) に接続し、サンプルの保存されている CNTK ディレクトリを開きます。 |
|Deep Water|いいえ | はい (Ubuntu) |H2O のためのディープ ラーニング フレームワークである Deep Water は [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) でインストールされ、`/dsvm/tools/deep_water` でも使用できます。 サンプル ノートブックは、JupyterHub に含まれています。 Deep Water では、cuDNN 5.1 と CUDA 8 が必要です。 他の深層学習フレームワークでは CUDA 9 および cuDNN 7 が使用されているため、これは既定ではライブラリ パスにありません。 Deep Water 用の CUDA 8 + cuDNN 5.1 を使用するには:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>Deep Water を使用するには:<br/>* ターミナル: [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 環境をアクティブ化した後、_python_ を実行します。 <br/>* JupyterHub: [JupyterHub に接続](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)した後、deep_water ディレクトリに移動してサンプル ノートブックを見つけます。|
