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
ms.openlocfilehash: 8d21e41ad487ad17598f2320fab5eebae02309e8
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021738"
---
1. 「[Azure Machine Learning service ワークスペースを作成する](../articles/machine-learning/service/setup-create-workspace.md#portal)」にある手順を使用して、次のことを行います。
    * Miniconda 環境を作成する
    * Azure Machine Learning SDK for Python をインストールする
    * ワークスペースの作成
    * ワークスペースの構成ファイル (**aml_config/config.json**) を記述する。
    
1. [GitHub リポジトリ](https://aka.ms/aml-notebooks)を複製します。

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 次のいずれかの方法を使用して、ワークスペース構成ファイルを追加します。
    * 手順 1 で作成した **aml_config/config.json** ファイルを複製されたディレクトリにコピーします。

    * [Azure portal](https://ms.portal.azure.com) で、お使いのワークスペースの **[概要]** セクションから **[config.json をダウンロード]** を選択します。 

    ![config.json をダウンロードする](./media/aml-dsvm-server/download-config.png)

    * [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) 内のコードを使用して新しいワークスペースを作成します。

1. 複製したディレクトリから、Notebook サーバーを起動します。
    
    ```shell
    jupyter notebook
    ```