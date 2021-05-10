---
title: チュートリアル:Jupyter Notebook の概要 (Python)
titleSuffix: Azure Machine Learning
description: Jupyter Notebook チュートリアルに向けてセットアップします。 ワークスペースを作成し、ノートブックをワークスペースに複製して、ノートブックを実行するコンピューティング インスタンスを作成します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python, contperf-fy21q3
adobe-target: true
ms.openlocfilehash: ed7a95ea83cacc0eaccfa23cd87403fb9be615ee
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210671"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>チュートリアル:Jupyter Notebook で Azure Machine Learning の作業を開始する

このチュートリアルでは、[マネージド クラウドベースのワークステーション (コンピューティング インスタンス)](concept-compute-instance.md) で Jupyter Notebook を使用して、Azure Machine Learning の使用を開始する手順を実行します。 このチュートリアルは、他のすべての Jupyter Notebook チュートリアルの前段階となるものです。

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * 他の Jupyter Notebook チュートリアルで使用する [Azure Machine Learning ワークスペース](concept-workspace.md)を作成します。
> * チュートリアル ノートブックをワークスペース内のフォルダーに複製します。
> * クラウドベースのコンピューティング インスタンスを作成します。Azure Machine Learning Python SDK のインストールと構成があらかじめ済んでいる環境が得られます。

このチュートリアルでは、ご利用のワークスペースからコンピューティング リソースに対して Jupyter ノートブックを実行する準備を行います。  

Azure Machine Learning の始め方としては、バッチ ジョブを送信する方法もお勧めです。  「[チュートリアル: 個人の開発環境で Azure Machine Learning の使用を開始する](tutorial-1st-experiment-sdk-setup-local.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

## <a name="create-a-workspace"></a>ワークスペースの作成

Azure Machine Learning ワークスペースは、機械学習モデルを実験、トレーニング、およびデプロイするために使用する、クラウドでの基本的なリソースです。 ワークスペースは、Azure サブスクリプションとリソース グループを、サービス内の簡単に使用できるオブジェクトに結び付けます。

既に Azure Machine Learning ワークスペースがある場合は、「[ノートブック フォルダーを複製する](#clone)」に進んでください。  

[ワークスペースを作成する方法](how-to-manage-workspace.md)は多数あります。  このチュートリアルでは、Azure リソースを管理するための Web ベースのコンソールである Azure portal を使用してワークスペースを作成します。

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> お使いの *ワークスペース* と *サブスクリプション* をメモしておきます。 この情報は、適切な場所に実験を作成するために必要になります。

## <a name="run-a-notebook-in-your-workspace"></a><a name="azure"></a>ワークスペースでノートブックを実行する

Azure Machine Learning では、インストール不要であらかじめ構成されたエクスペリエンスを実現するために、お使いのワークスペースにクラウド ノートブック サーバーが含まれています。 お使いの環境、パッケージ、および依存関係を制御したい場合は、[独自の環境](tutorial-1st-experiment-sdk-setup-local.md)を使用してください。

 次のビデオに従うか、詳細な手順を使用して、ワークスペースでチュートリアル ノートブックを複製して実行します。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a><a name="clone"></a> ノートブック フォルダーを複製する

次の実験の設定を完了し、Azure Machine Learning スタジオで手順を実行します。 この統合インターフェイスには、あらゆるスキル レベルのデータ サイエンス実務者がデータ サイエンス シナリオを実行するための機械学習ツールが含まれています。

1. [Azure Machine Learning Studio](https://ml.azure.com/) にサインインします。

1. お使いのサブスクリプションと、作成したワークスペースを選択します。

1. 左側で **[ノートブック]** を選択します。

1. 上部で **[サンプル]** タブを選択します。

1. **Python** フォルダーを開きます。

1. バージョン番号が付いたフォルダーを開きます。 この番号は、Python SDK の現在のリリースを表します。

1. **tutorials** フォルダーの右側にある **[...]** ボタンを選択し、 **[Clone]\(複製\)** を選択します。

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Clone チュートリアル フォルダーを示すスクリーンショット。":::

1. フォルダーの一覧には、ワークスペースにアクセスする各ユーザーが表示されます。 自分のフォルダーを選択して **tutorials** フォルダーをそこに複製します。

### <a name="open-the-cloned-notebook"></a><a name="open"></a>複製されたノートブックを開く

1. **[User files]\(ユーザー ファイル\)** セクションに複製された **tutorials** フォルダーを開きます。

    > [!IMPORTANT]
    > **samples** フォルダー内のノートブックを表示できますが、そこからノートブックを実行することはできません。 ノートブックを実行するには、必ず **[User Files]\(ユーザー ファイル\)** セクションにあるノートブックの複製バージョンを開いてください。
    
1. **tutorials/image-classification-mnist-data** フォルダーの **img-classification-part1-training.ipynb** ファイルを選択します。

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="tutorials フォルダーを開いたところを示すスクリーンショット。":::

1. 上部のバーで、ノートブックの実行に使用するコンピューティング インスタンスを選択します。 これらの仮想マシン (VM) には、[Azure Machine Learning を実行するために必要なすべてのもの](concept-compute-instance.md#contents)があらかじめ構成されています。

1. VM が見つからない場合は、 **[追加]** を選択してコンピューティング インスタンス VM を作成します。

    1. VM を作成する場合は、以下のルールに従います。
 
        + 名前は必須であり、フィールドを空にすることはできません。
        + 名前は、ワークスペースまたはコンピューティング インスタンスの Azure リージョン内のすべての既存のコンピューティング インスタンス全体で一意である必要があります (大文字小文字は区別されません)。 選択した名前が一意でない場合は、アラートが表示されます。
        + 有効な文字は、大文字、小文字、数字 (0 から 9)、およびダッシュ (-) です。
        + 名前は 3 から 24 文字の長さでなければなりません。
        + 名前の先頭は、数字やダッシュではなく、文字である必要があります。
        + ダッシュを使用する場合は、その後に少なくとも 1 文字を続ける必要があります。 たとえば、Test-、test-0、test-01 は無効であり、test-a0、test-0a は有効な例です。

    1. 利用可能な選択肢から VM のサイズを選択します。 このチュートリアルでは、既定の VM を選択するのが適切です。

    1. **[作成]** を選択します。 VM の設定には約 5 分かかる可能性があります。

1. VM が使用可能になると、上部のツール バーに表示されます。 これで、ツール バーの **[Run all]\(すべて実行\)** を使用するか、ノートブックのコード セルで **Shift + Enter** キーを使用して、ノートブックを実行できるようになりました。

カスタム ウィジェットがある場合、または Jupyter か JupyterLab を使用する場合は、右端にある **[Jupyter]** ドロップダウン リストを選択します。 その後、 **[Jupyter]** または **[JupyterLab]** を選択します。 新しいブラウザー ウィンドウが開きます。

## <a name="next-steps"></a>次のステップ

開発環境の設定が完了したので、Jupyter Notebook でのモデルのトレーニングに進みます。

> [!div class="nextstepaction"]
> [チュートリアル:MNIST データと scikit-learn を使用して画像の分類モデルをトレーニングする](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> 他のチュートリアルに取り組む予定がない場合は、不使用時のコストを抑えるために、クラウド ノートブック サーバー VM を停止します。

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
