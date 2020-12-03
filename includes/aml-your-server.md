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
ms.date: 03/05/2020
ms.openlocfilehash: 6e7580abfd113a279e2223b9cc9665f6a0b86bc8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559530"
---
1. [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?view=azure-ml-py) に関するページの手順を使用して、Azure Machine Learning SDK for Python をインストールします

1. [Azure Machine Learning ワークスペース](../articles/machine-learning/how-to-manage-workspace.md)を作成します。

1. [構成ファイル](../articles/machine-learning/how-to-configure-environment.md#workspace)  (**aml_config/config.json**) を記述します。

1. [GitHub リポジトリ](https://aka.ms/aml-notebooks)を複製します。

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 複製したディレクトリから、Notebook サーバーを起動します。

    ```bash
    jupyter notebook
    ```