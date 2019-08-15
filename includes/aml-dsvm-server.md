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
ms.openlocfilehash: 09a3cc5a623be2ee5a9d50204f0902ca9f400a76
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857423"
---
1. [Azure Machine Learning service ワークスペースを作成する](../articles/machine-learning/service/how-to-manage-workspace.md)

1. [GitHub リポジトリ](https://aka.ms/aml-notebooks)を複製します。

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 次のいずれかの方法を使用して、ワークスペース構成ファイルを複製されたディレクトリに追加します。

    * [Azure portal](https://ms.portal.azure.com) で、お使いのワークスペースの **[概要]** セクションから **[config.json をダウンロード]** を選択します。 

    ![config.json をダウンロードする](./media/aml-dsvm-server/download-config.png)

    * 複製したディレクトリの [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) ノートブック内のコードを使用して新しいワークスペースを作成します。

1. 複製したディレクトリから、Notebook サーバーを起動します。

    ```shell
    jupyter notebook
    ```