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
ms.openlocfilehash: 2504245a14eee5308e046148f8073cf34ffa8143
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "79486008"
---
1. [Azure Machine Learning ワークスペースを作成](../articles/machine-learning/how-to-manage-workspace.md)します。

1. [GitHub リポジトリ](https://aka.ms/aml-notebooks)を複製します。

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 次のいずれかの方法を使用して、ワークスペース構成ファイルを複製されたディレクトリに追加します。

    * [Azure portal](https://ms.portal.azure.com) で、お使いのワークスペースの **[概要]** セクションから **[config.json をダウンロード]** を選択します。 

    ![config.json をダウンロードする](./media/aml-dsvm-server/download-config.png)

    * 複製したディレクトリの [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) ノートブック内のコードを使用して新しいワークスペースを作成します。

1. 複製したディレクトリから、Notebook サーバーを起動します。

    ```bash
    jupyter notebook
    ```