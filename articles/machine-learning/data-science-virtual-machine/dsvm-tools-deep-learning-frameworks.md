---
title: ディープ ラーニングと AI のフレームワーク
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine で使用できるディープ ラーニングのフレームワークとツール。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 05/12/2021
ms.openlocfilehash: 3272c2584d03c6f13d2ffb85eb0b37bf3c29000a
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110081624"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Azure Data Science VM 用のディープ ラーニングと AI のフレームワーク
以下の一覧では、DSVM でのディープ ラーニング フレームワークを示します。


## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA、cuDNN、NVIDIA ドライバー](https://developer.nvidia.com/cuda-toolkit)

| カテゴリ | 値 |
|--|--|
| サポートされるバージョン | 11 |
| サポートされている DSVM エディション | Windows Server 2019<br>Ubuntu 18.04 |
| DSVM での構成/インストール方法 | _nvidia-smi_ はシステム パスから実行できます。 |
| 実行方法 | コマンド プロンプト (Windows) またはターミナル (Linux) を開いて、_nvidia-smi_ を実行します。 |
## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

| カテゴリ | 値 |
| ------------- | ------------- |
| サポートされるバージョン | 0.21.3|
| サポートされている DSVM エディション      | Ubuntu 18.04 |
| DSVM での構成/インストール方法  | Horovod は、Python 3.5 にインストールされます |
| 実行方法      | ターミナルで適切な環境をアクティブ化した後、Python を実行します。 |


## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia システム管理インターフェイス (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

| カテゴリ | 値 |
|--|--|
| サポートされるバージョン |  |
| サポートされている DSVM エディション | Windows Server 2019<br>Ubuntu 18.04 |
| 何を目的としていますか? | GPU アクティビティをクエリするための NVIDIA ツール |
| DSVM での構成/インストール方法 | `nvidia-smi` はシステム パス上にあります。 |
| 実行方法 | **GPU を備えた** 仮想マシンで、コマンド プロンプト (Windows の場合) またはターミナル (Linux の場合) を開き、`nvidia-smi` を実行します。 |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

| カテゴリ | 値 |
|--|--|
| サポートされるバージョン | 1.8.1 (Ubuntu 18.04、Windows 2019) |
| サポートされている DSVM エディション | Windows Server 2019<br>Ubuntu 18.04 |
| DSVM での構成/インストール方法 | Python、conda 環境 'py38_pytorch' にインストールされます |
| 実行方法 | ターミナル: 適切な環境をアクティブ化した後、Python を実行します。<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): 接続した後、サンプル用の PyTorch ディレクトリを開きます。 |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

| カテゴリ | 値 |
|--|--|
| サポートされるバージョン | 2.4 |
| サポートされている DSVM エディション | Windows Server 2019<br>Ubuntu 18.04 |
| DSVM での構成/インストール方法 | Python、conda 環境 'py38_tensorflow' にインストールされます |
| 実行方法 | ターミナル: 適切な環境をアクティブ化した後、Python を実行します。 <br/> * Jupyter: [Jupyter](provision-vm.md) または [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine) に接続し、サンプル用の TensorFlow ディレクトリを開きます。 |
