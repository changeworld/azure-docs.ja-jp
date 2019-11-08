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
ms.date: 09/26/2019
ms.openlocfilehash: e11b1c8c19db73ace069d4bfda3516cdbbceaa02
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476083"
---
1. [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) に関するページの手順を使用して、Azure Machine Learning SDK for Python をインストールします

1. [Azure Machine Learning ワークスペース](../articles/machine-learning/service/how-to-manage-workspace.md)を作成します。

1. [構成ファイル](../articles/machine-learning/service/how-to-configure-environment.md#workspace)  (**aml_config/config.json**) を記述します。

1. [GitHub リポジトリ](https://aka.ms/aml-notebooks)を複製します。

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 複製したディレクトリから、Notebook サーバーを起動します。

    ```shell
    jupyter notebook
    ```
