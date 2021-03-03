---
title: ローカルで Azure Percept の高度な開発を始める
description: ローカルで VS Code と Jupyter Notebook を使用して Azure ML の機械学習の開発を始めます
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 88e71ac21177a13d30176212e97442c63272eca6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658972"
---
# <a name="getting-started-with-machine-learning-development-using-vs-code--jupyter-notebooks-on-azureml"></a>VS Code と Jupyter Notebook を使用した Azure ML での機械学習の開発の概要

この記事では、Azure Machine Learning ワークスペースの設定、コンピューティング インスタンスの作成、ローカル コンピューターでの Visual Studio Code の開発環境の設定、および VS Code 内での事前構成済みサンプル Jupyter Notebook の各セルの実行という各プロセスについて、順を追って説明します。

この[ノートブック](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb)は、Python で Azure ML の事前トレーニング済み TensorFlow モデル (MobileNetSSDV2Lite) とカスタム データセットを使用し、転移学習を実施して、ボウルを検出します。

ノートブックでは、[COCO データセット](https://cocodataset.org/#home)を利用して目的のクラス (ボウル) のみに絞り込み、適切な形式に変換する方法を示しています。 オープンソースの [VoTT 2](https://github.com/microsoft/VoTT) ラベル付けツールを使用して、PASCAL VOC 形式で境界ボックスを作成してラベル付けすることもできます。

カスタム データセットでのモデルの再トレーニング後、モジュール ツイン更新法を使用してモデルを Azure Percept DK にデプロイできます。 その後、Azure Percept Vision SoM のライブ RTSP ストリームを表示することで、モデルの推論結果を確認できます。 モデルの再トレーニングとデプロイは共に、リモート コンピューティング インスタンスを介してノートブック内で実行されます。

## <a name="prerequisites"></a>前提条件

- [Azure Machine Learning のサブスクリプション](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure Percept Vision SoM を接続済みの Azure Percept DK](./overview-azure-percept-dk.md)
- [Azure Percept DK のオンボーディング エクスペリエンス](./quickstart-percept-dk-set-up.md)を完了済み

## <a name="download-azure-percept-github-repository"></a>Azure Percept GitHub リポジトリのダウンロード

1. [Azure Percept DK GitHub リポジトリ](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview)にアクセスします。
1. リポジトリをクローンするか、ZIP ファイルをダウンロードします。 画面の上部付近にある **[Code]\(コード\)**  ->  **[Download ZIP]\(ZIP のダウンロード\)** の順にクリックします。

    :::image type="content" source="./media/advanced-development-local/download-zip.png" alt-text="GitHub のダウンロード画面。":::

## <a name="create-an-azure-machine-learning-workspace-and-remote-compute-instance"></a>Azure Machine Learning ワークスペースとリモート コンピューティング インスタンスの作成

1. [Azure Machine Learning ポータル](https://ml.azure.com)にアクセスします。
1. ドロップダウン メニューで、使用するディレクトリ、Azure サブスクリプション、Machine Learning ワークスペースを選択して、 **[作業の開始]** をクリックします。

    :::image type="content" source="./media/advanced-development-local/machine-learning-portal-get-started.png" alt-text="Azure ML の作業の開始画面。":::

    Azure Machine Learning ワークスペースがまだない場合は、 **[新しいワークスペースの作成]** をクリックします。 新しいブラウザー タブで、次の手順を実行します。

    1. Azure サブスクリプションを選択します。
    1. リソース グループを選択します。
    1. ワークスペースの名前を入力します。
    1. 自分のリージョンを選択します。
    1. ワークスペースのエディションを選択します。
    1. **[確認と作成]** をクリックします。
    1. 次のページで、選択内容を確認して **[作成]** をクリックします。

        :::image type="content" source="./media/advanced-development-local/workspace-review-and-create.png" alt-text="Azure ML のワークスペース作成画面。":::

    ワークスペースの作成には数分かかります。 ワークスペースの作成が完了すると、Machine Learning service の概要ページで自分のリソースの横に緑色のチェック マークが付き、ページ上部に "**デプロイが完了しました**" と表示されます。

    :::image type="content" source="./media/advanced-development-local/workspace-creation-complete-inline.png" alt-text="ワークスペースの作成の確認。" lightbox= "./media/advanced-development-local/workspace-creation-complete.png":::

    ワークスペースの作成が完了したら、Azure Machine Learning ポータルのタブに戻り、 **[作業の開始]** をクリックします。

1. Machine Learning ワークスペースのホームページで、左側ペインの **[Compute]\(コンピューティング\)** をクリックします。

1. 既存のコンピューティング インスタンスがない場合は、 **[新規]** をクリックして、新しい CPU または GPU コンピューティングを作成します。 **[New compute instance]\(新しいコンピューティング インスタンス\)** ウィンドウで、 **[Compute name]\(コンピューティング名\)** を入力して、 **[仮想マシンの種類]** と **[仮想マシンのサイズ]** を選択します。 **Create** をクリックしてください。

    :::image type="content" source="./media/advanced-development-local/new-compute-instance.png" alt-text="コンピューティング インスタンスの新規作成画面。":::

    **[作成]** をクリックしてからコンピューティング インスタンスが作成されるまでには、数分かかります。 コンピューティングの作成が完了すると、**コンピューティング** の状態に緑色の丸が付き、" **\<your compute name> - 実行中**" と表示されます。 このコンピューティング インスタンスでは Jupyter サーバーが実行されています。以下のチュートリアルでは、このインスタンスを使用します。

## <a name="visual-studio-code-development-environment-setup"></a>Visual Studio Code 開発環境の設定

1. 必要なツールをインストールします。

    1. オプション 1: 

        [Dev Tools Pack Installer](./dev-tools-installer.md) を使用して、次のパッケージをインストールします。

        - Visual Studio Code
        - Python 3.5、3.6、または 3.7
        - Miniconda3
        - Intel OpenVino Toolkit 2020.2

        注: Intel OpenVino Toolkit は、Dev Tools Pack Installer ではオプションのパッケージとして表示されますが、Azure Percept 開発キット (DK) の Azure Percept Vision SoM を使用するうえでは必須です。

    1. オプション 2:

        Dev Tools Pack Installer を使用しない場合は、次のパッケージの各リンクをクリックし、指定されたダウンロードおよびインストールのガイドラインに従って、パッケージを手動でインストールします。

        - [Visual Studio Code](https://code.visualstudio.com/)
        - [Python 3.5、3.6、または 3.7](https://www.python.org/)
        - [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
        - [Intel OpenVino Toolkit 2020.2](https://docs.openvinotoolkit.org/)

    注: Anaconda のフル ディストリビューションが既にインストールされている場合は、Miniconda をインストールする必要はありません。 また、Anaconda または Miniconda を使用しない場合は、Python 仮想環境を作成し、pip を使用して AI モデル開発の実行に必要なパッケージをインストールすることもできます。

1. Visual Studio Code を起動します。
1. データ サイエンス環境を設定します。

    - オプション 1 - キュレーション済みの ML パッケージが既に存在する、既存または新規の Machine Learning リモート コンピューティング インスタンスに接続します。 **本チュートリアルでは、このオプションを使用します**。

    - オプション 2 - ローカル コンピューターで conda 環境を設定します。
        1. Anaconda または Miniconda のコマンド プロンプトを開いて次のコマンドを実行し、指定のパッケージが含まれる **myenv** という名前の環境を作成します。

            `conda create -n myenv python=3.7 pandas jupyter seaborn scikit-learn keras tensorflow=1.15`

            Anaconda 環境の作成および管理の詳細については、[Anaconda のドキュメント](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)を参照してください。

1. VS Code ワークスペースを作成します。

    1. 任意の場所に、Visual Studio Code ワークスペースとして使用するフォルダーを作成します。 それに **myworkspace** という名前を付けます。
    1. Visual Studio Code で **[ファイル]**  >  **[フォルダーを開く]**  >  **[フォルダーの選択]** の順にクリックして、**myworkspace** を開きます。
    1. エクスプローラーで、Azure Percept DK の GitHub リポジトリのローカル コピーに含まれる [Transferlearningusing_SSDLiteV2 Model.ipynbb ファイル](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb)を参照し、選択します。 このノートブック ファイルを myworkspace フォルダーにコピーします。

## <a name="azure-integration-with-visual-studio-code"></a>Azure と Visual Studio Code の統合

1. まだ[無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) にサインアップしていない場合は、サインアップします。

1. 次の VS Code 用 Azure 拡張機能をインストールします。
    - [Azure Machine Learning 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)。
    - Python Insider 拡張機能。 VS Code で、 **[表示]**  ->  **[コマンド パレット]** に移動します コマンド パレットで、「**Python: Switch to Insiders Daily Channel**」と入力して選択します。
    - [Azure Account 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)。 プロンプトが表示されたら、VS Code のウィンドウを再読み込みします。
    - [Azure IoT Hub Toolkit 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)。
    - [Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。

1. Visual Studio Code 内で Azure アカウントにサインインし、Azure 上にリソースをプロビジョニングしてワークロードを実行します。
    1. Visual Studio Code のメニュー バーで、 **[表示]**  >  **[コマンド パレット]** の順に選択してコマンド パレットを開きます。
    1. コマンド パレットに「**Azure: Sign In**」と入力して、サインイン プロセスを開始します。

        :::image type="content" source="./media/advanced-development-local/transfer-learning-azure-sign-in-inline.png" alt-text="Azure サインイン画面。" lightbox= "./media/advanced-development-local/transfer-learning-azure-sign-in.png":::

    1. VS Code の左側にある Azure アイコンをクリックして、Python 拡張機能と Azure アカウントをアクティブにします。

        :::image type="content" source="./media/advanced-development-local/azure-icon.png" alt-text="VS Code の Azure アイコン。":::

    1. **myworkspace** フォルダーの Transferlearningusing_SSDLiteV2 Model_VSCode.ipynb ノートブックを開きます。
    1. コマンド パレットを開きます。 「**Python: specify local or remote Jupyter server for connections**」と入力して選択します。
    1. 選択可能な接続オプションの一覧が表示されます。 **[Azure ML Compute Instances]\(Azure ML のコンピューティング インスタンス\)** を選択します。

        :::image type="content" source="./media/advanced-development-local/azure-machine-learning-compute-instances.png" alt-text="VS Code の [Azure ML Compute Instances]\(Azure ML のコンピューティング インスタンス\) オプション。":::

    1. ガイド付きプロンプトに従って、サブスクリプション、ワークスペース、リモート コンピューティング インスタンスを選択します。 ワークスペースとリモート コンピューティング インスタンスは、このチュートリアルの前の手順で作成したものを選択します。 新しいコンピューティング インスタンスを作成することもできます。
    1. コンピューティング インスタンスを選択すると、VS Code のウィンドウを再読み込みするように求められます。 再読み込みが完了したら、 **[Python 3.6 - AzureML]** カーネルを選択します。 これで、ノートブック内の任意のセルを実行できるようになりました。 ノートブックのセルを実行すると、ノートブックとコンピューティング インスタンス間の接続がインスタンス化されます。 使用するコンピューティング インスタンスに応じて、ノートブック ツールバーが更新されます。

        :::image type="content" source="./media/advanced-development-local/kernel-inline.png" alt-text="VS Code でのカーネルの選択。" lightbox= "./media/advanced-development-local/kernel.png":::

## <a name="working-with-the-notebook"></a>ノートブックの使用

これで、VS Code 内でノートブックを実行してカスタムのボウル検出器をトレーニングして、開発キットにデプロイする準備が整いました。 スクリプトを実行するには一部のセルに入力パラメーターを指定する必要があるため、ノートブックの各セルは 1 つずつ実行してください。 入力パラメーターが必要なセルは、ノートブックで直接編集することもできます。 セルを実行するには、セル左側にある再生アイコンをクリックします。

:::image type="content" source="./media/advanced-development-local/run-cell-1.png" alt-text="セルのアイコンを強調表示したノートブック。":::

## <a name="next-steps"></a>次のステップ

他の Azure Machine Learning service のサンプル ノートブックについては、こちらの[リポジトリ](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml)を参照してください。
