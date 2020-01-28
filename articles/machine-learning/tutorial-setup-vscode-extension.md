---
title: チュートリアル:Visual Studio Code の Azure Machine Learning 拡張機能を設定する
titleSuffix: Azure Machine Learning
description: Visual Studio Code の Azure Machine Learning 拡張機能を設定する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 01/16/2019
ms.openlocfilehash: 4000fcc80d507d3b1e871d7f3288fc3b77693c76
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157463"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>Visual Studio Code の Azure Machine Learning 拡張機能を設定する

Visual Studio Code の Azure Machine Learning 拡張機能を使用して、スクリプトをインストールして実行する方法について説明します。

このチュートリアルでは、以下のタスクについて学習します。

> [!div class="checklist"]
> * Visual Studio Code の Azure Machine Learning 拡張機能をインストールする
> * Visual Studio Code から Azure アカウントにサインインする
> * Azure Machine Learning 拡張機能を使用してサンプル スクリプトを実行する

## <a name="prerequisites"></a>前提条件

- Azure のサブスクリプション。 お持ちでない場合は、[無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) にサインアップしてお試しください。
- 軽量なクロスプラットフォーム コード エディターである [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview) をインストールします。 

## <a name="install-the-extension"></a>拡張機能をインストールする

1. Visual Studio Code を開きます。
1. **アクティビティ バー**の**拡張機能**アイコンを選択して、拡張機能ビューを開きます。
1. 拡張機能ビューで、「Azure Machine Learning」を検索します。
1. **[インストール]** を選択します。

> [!NOTE]
> または、[インストーラーを直接ダウンロード](https://aka.ms/vscodetoolsforai)して、Visual Studio Marketplace 経由で Azure Machine Learning 拡張機能をインストールすることもできます。 

このチュートリアルの残りの手順は、拡張機能の**バージョン 0.6.8** を使用してテストされています。

## <a name="sign-in-to-your-azure-account"></a>Azure アカウントにサインインする

Azure でリソースをプロビジョニングし、ワークロードを実行するには、Azure アカウントの資格情報でサインインする必要があります。 アカウント管理を支援するために、Azure Machine Learning では Azure アカウント拡張機能が自動的にインストールされます。 Azure アカウント拡張機能の詳細については、[こちらのサイト](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)を参照してしてください。

1. メニュー バーから **[表示]、[コマンド パレット]** の順に選択して、コマンド パレットを開きます。 
1. 「Azure: Sign In」コマンドをテキスト ボックスに入力して、サインイン プロセスを開始します。

## <a name="run-a-script-in-azure"></a>Azure でスクリプトを実行する

これで、ご自分のアカウント資格情報を使用して Azure にサインインできました。次に、このセクションの手順に従い、拡張機能を使用して機械学習モデルをトレーニングする方法を説明します。

1. [VS Code Tools for AI リポジトリ](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip)をダウンロードし、お使いのコンピューターの任意の場所に解凍します。
1. Visual Studio Code で `mnist-vscode-docs-sample` ディレクトリを開きます。
1. アクティビティ バーの **Azure** アイコンを選択します。
1. Azure Machine Learning ビューの上部にある**実験の実行**アイコンを選択します。

    > [!div class="mx-imgBorder"]
    > ![実験の実行](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. コマンド パレットが展開されたら、画面の指示に従います。

    1. Azure サブスクリプションを選択します。
    1. **[Create a new Azure ML workspace]\(新しい Azure ML ワークスペースを作成する\)** を選択します
    1. ジョブの種類として **[TensorFlow Single-Node Training]\(TensorFlow 単一ノード トレーニング\)** を選択します。
    1. トレーニングするスクリプトとして「`train.py`」を入力します。 これは、手書き数字の画像を分類する機械学習モデルのコードを含むファイルです。
    1. 実行するための要件として、次のパッケージを指定します。

        ```text
        pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
        ```

1. この時点で、次のような構成ファイルがテキスト エディターに表示されます。 構成には、トレーニング ジョブを実行するために必要な情報が含まれています。ここには、モデルをトレーニングするためのコードや、前の手順で指定した Python の依存関係を含むファイルなどが指定されています。

    ```json
    {
        "workspace": "WS12191742",
        "resourceGroup": "WS12191742-rg2",
        "location": "South Central US",
        "experiment": "WS12191742-exp2",
        "compute": {
            "name": "WS12191742-com2",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS12191742-com2-rc1",
            "condaDependencies": [
                "python=3.6.2",
                "tensorflow=1.15.0"
            ],
            "pipDependencies": [
                "azureml-defaults"
            ]
        }
    }
    ```

1. Azure で実験を実行するには、 **[Submit experiment]\(実験を送信する\)** を選択します。 これにより、`train.py` と構成ファイルが Azure Machine Learning ワークスペースに送信されます。 その後、Azure のコンピューティング リソース上でトレーニング ジョブが開始されます。
1. 数分後、トレーニング済みの TensorFlow モデルが格納された、`output` という名前のディレクトリがローカルに作成されます。

## <a name="next-steps"></a>次のステップ

* [チュートリアル:Visual Studio Code の Azure Machine Learning 拡張機能を使用して、画像分類 TensorFlow モデルをトレーニングおよびデプロイする](tutorial-train-deploy-image-classification-model-vscode.md)。
