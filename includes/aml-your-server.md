---
title: インクルード ファイル
description: インクルード ファイル
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 01/25/2019
ms.openlocfilehash: ce8b117a3cbe0e3a5c4265729ccf5c0264241013
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391769"
---
1. 「[Azure Machine Learning service ワークスペースを作成する](../articles/machine-learning/service/setup-create-workspace.md#portal)」の手順を使用して、次のことを実行します。
    * Miniconda 環境を作成する
    * Azure Machine Learning SDK for Python をインストールする
    * ワークスペースの作成
    * ワークスペースの構成ファイル (**aml_config/config.json**) を記述する。

1. [GitHub リポジトリ](https://aka.ms/aml-notebooks)を複製します。

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 複製したディレクトリから、Notebook サーバーを起動します。

    ```shell
    jupyter notebook
    ```