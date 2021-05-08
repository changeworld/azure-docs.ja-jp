---
title: Visual Studio Code でコンピューティング インスタンスに接続する (プレビュー)
titleSuffix: Azure Machine Learning
description: Visual Studio Code で Azure Machine Learning コンピューティング インスタンスに接続して、対話型 Jupyter Notebook とリモート開発のワークロードを実行する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 04/08/2021
ms.openlocfilehash: 14f0d15d48193267c224f3497c24651ca3249b0b
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028592"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Visual Studio Code で Azure Machine Learning コンピューティング インスタンスに接続する (プレビュー)

この記事では、Visual Studio Code を使用して Azure Machine Learning コンピューティング インスタンスに接続する方法について説明します。

[Azure Machine Learning コンピューティング インスタンス](concept-compute-instance.md)は、データ サイエンティスト向けのクラウドベースのフルマネージド ワークステーションであり、IT 管理者用の管理およびエンタープライズ対応機能を備えています。

Visual Studio Code からコンピューティング インスタンスに接続するには、次の 2 つの方法があります。

* コンピューティング インスタンスを削除します。 このオプションを使用すると、機械学習プロジェクトを構築するためのフル機能を備えた開発環境を用意できます。
* リモート Jupyter Notebook サーバー。 このオプションを使用すると、コンピューティング インスタンスをリモート Jupyter Notebook サーバーとして設定できます。

## <a name="configure-a-remote-compute-instance"></a>リモート コンピューティング インスタンスを構成する

開発用にリモート コンピューティング インスタンスを構成するには、いくつかの前提条件を満たす必要があります。

* Visual Studio Code の Azure Machine Learning 拡張機能 詳細については、[Visual Studio Code の Azure Machine Learning 拡張機能セットアップ ガイド](tutorial-setup-vscode-extension.md)を参照してください。
* Azure Machine Learning ワークスペース。 まだ用意していない場合は、[Visual Studio Code の Azure Machine Learning 拡張機能を使用して新しいワークスペースを作成](how-to-manage-resources-vscode.md#create-a-workspace)します。
* Azure Machine Learning コンピューティング インスタンス。 用意していない場合は、[Visual Studio Code の Azure Machine Learning 拡張機能を使用して新しいコンピューティング インスタンスを作成](how-to-manage-resources-vscode.md#create-compute-instance)します。

リモート コンピューティング インスタンスに接続するには:

# <a name="vs-code"></a>[VS Code](#tab/extension)

### <a name="azure-machine-learning-extension"></a>Azure Machine Learning 拡張機能

1. VS Code で、Azure Machine Learning 拡張機能を起動します。
1. 拡張機能の **[Compute instances]\(コンピューティング インスタンス\)** ノードを展開します。
1. 接続するコンピューティング インスタンスを右クリックし、 **[Connect to Compute Instance]\(コンピューティング インスタンスへの接続\)** を選択します。

:::image type="content" source="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png" alt-text="コンピューティング インスタンスへの接続: Visual Studio Code Azure ML 拡張機能" lightbox="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png":::

### <a name="command-palette"></a>コマンド パレット

1. VS Code で **[表示] > [コマンド パレット]** を選択し、コマンド パレットを開きます。
1. ボックス **[Azure ML: Connect to Compute Instance]\(Azure ML: コンピューティング インスタンスへの接続\)** に入力します。
1. サブスクリプションを選択します。
1. ワークスペースを選択します。
1. コンピューティング インスタンスを選択するか、新規に作成します。

# <a name="studio"></a>[スタジオ](#tab/studio)

[ml.azure.com](https://ml.azure.com) に移動します

> [!IMPORTANT]
> Visual Studio Code からリモート コンピューティング インスタンスに接続するには、Azure Machine Learning スタジオにログインしているアカウントが Visual Studio Code で使用しているものと同じであることを確認します。

### <a name="compute"></a>Compute

1. **[コンピューティング]** タブを選択します
1. *[アプリケーション URI]* 列で、接続するコンピューティング インスタンスの **[VS コード]** を選択します。

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png" alt-text="コンピューティング インスタンスへの接続: VS Code Azure ML スタジオ" lightbox="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png":::

### <a name="notebook"></a>ノートブック

1. **[ノートブック]** タブを選択します
1. *[ノートブック]* タブで、編集するファイルを選択します。
1. **[エディター] > [Edit in VS Code (preview)]\(VS Code で編集 (プレビュー)\)** を選択します。

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png" alt-text="コンピューティング インスタンスへの接続: VS Code Azure ML Notebook" lightbox="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png":::

---

リモート コンピューティング インスタンスの新しいウィンドウが起動します。 リモート コンピューティング インスタンスに接続しようとすると、次のタスクが実行されます。

1. 承認。 接続しようとしているユーザーに、そのコンピューティング インスタンスを使用する権限があるかどうかを確認するためのチェックが行われます。
1. VS Code Remote Server がコンピューティング インスタンスにインストールされます。
1. リアルタイムの対話のために WebSocket 接続が確立されます。

接続は、確立されると維持されます。 トークンはセッションの開始時に発行され、コンピューティング インスタンスとの接続を維持するために自動的に更新されます。

リモート コンピューティング インスタンスに接続した後、エディターを使用して次のことを行います。

* [リモート コンピューティング インスタンスまたはファイル共有でファイルを作成および管理する](https://code.visualstudio.com/docs/editor/codebasics)。
* [VS Code 統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を使用して、[リモート コンピューティング インスタンス上でコマンドとアプリケーションを実行する](how-to-access-terminal.md)。
* [スクリプトとアプリケーションをデバッグする](https://code.visualstudio.com/Docs/editor/debugging)
* [VS Code を使用して Git リポジトリを管理する](concept-train-model-git-integration.md)

## <a name="configure-compute-instance-as-remote-notebook-server"></a>コンピューティング インスタンスをリモート ノートブック サーバーとして構成する

コンピューティング インスタンスをリモート Jupyter Notebook サーバーとして構成するには、いくつかの前提条件が必要です。

* Visual Studio Code の Azure Machine Learning 拡張機能 詳細については、[Visual Studio Code の Azure Machine Learning 拡張機能セットアップ ガイド](tutorial-setup-vscode-extension.md)を参照してください。
* Azure Machine Learning ワークスペース。 まだ用意していない場合は、[Visual Studio Code の Azure Machine Learning 拡張機能を使用して新しいワークスペースを作成](how-to-manage-resources-vscode.md#create-a-workspace)します。

コンピューティング インスタンスに接続するには、次のようにします。

1. Visual Studio Code で Jupyter Notebook を開きます。
1. 統合ノートブックのエクスペリエンスが読み込まれたら、**Jupyter Server** を選択します。

    > [!div class="mx-imgBorder"]
    > ![Azure Machine Learning のリモート Jupyter Notebook サーバー ドロップダウンの表示](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    または、次のようにコマンド パレットを使用することもできます。

    1. メニュー バーから **[表示]、[コマンド パレット]** の順に選択して、コマンド パレットを開きます。
    1. テキストボックスに「`Azure ML: Connect to Compute instance Jupyter server`」と入力します。

1. Jupyter サーバー オプションの一覧から [`Azure ML Compute Instances`] を選択します。
1. サブスクリプションの一覧からご利用のサブスクリプションを選択します。 既定の Azure Machine Learning ワークスペースをあらかじめ構成済みである場合、この手順はスキップされます。
1. ワークスペースを選択します。
1. 一覧から自分のコンピューティング インスタンスを選択します。 それがない場合は、 **[Create new Azure ML Compute Instance]\(新しい Azure ML コンピューティング インスタンスを作成する\)** を選択し、プロンプトに従って作成します。
1. 変更を有効にするには、Visual Studio Code を再度読み込む必要があります。
1. Jupyter Notebook を開き、セルを実行します。

> [!IMPORTANT]
> 接続を確立するために、セルを実行する **必要があります**。

この時点では、ご利用の Jupyter Notebook でセルを実行し続けることができます。

> [!TIP]
> Jupyter のようなコード セルを含む Python スクリプト ファイル (.py) を操作することもできます。 詳細については、[Visual Studio Code Python Interactive のドキュメント](https://code.visualstudio.com/docs/python/jupyter-support-py)を参照してください。

## <a name="next-steps"></a>次のステップ

Visual Studio Code Remote の設定が完了したので、Visual Studio Code からコンピューティング インスタンスをリモート コンピューティングとして使用して、[自分のコードを対話形式でデバッグ](how-to-debug-visual-studio-code.md)することができます。

[チュートリアル:最初の ML モデルをトレーニングする)](tutorial-1st-experiment-sdk-train.md)」では、統合ノートブックでコンピューティング インスタンスを使用する方法を示しています。
