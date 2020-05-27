---
title: ワークスペースで Jupyter ノートブックを実行する方法
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Studio のワークスペースから離れずに Jupyter ノートブックを実行する方法について説明します。
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 96e37afd8bf7d59eef4a4c0c831f535faa36d34d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681446"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>ワークスペースで Jupyter ノートブックを実行する方法
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning Studio のワークスペースで Jupyter ノートブックを直接実行する方法について説明します。 [Jupyter](https://jupyter.org/) または [JupyterLab](https://jupyterlab.readthedocs.io) を起動できますが、ワークスペースから離れずにノートブックを編集して実行することもできます。

次のことを実行できます。

* ワークスペースで Jupyter ノートブックを作成する
* ノートブックから実験を実行する
* ノートブック環境を変更する
* ノートブックの実行に使用されるコンピューティング インスタンスの詳細を検索する

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://aka.ms/AMLFree) を作成してください。
* Machine Learning ワークスペース。 [Azure Machine Learning ワークスペースを作成する](how-to-manage-workspace.md)方法に関するページを参照してください。

## <a name="create-notebooks"></a><a name="create"></a> ノートブックを作成する

Azure Machine Learning ワークスペースで、新しい Jupyter ノートブックを作成して作業を開始します。 新しく作成されたノートブックは、既定のワークスペース ストレージに格納されます。 このノートブックは、ワークスペースにアクセスできるすべてのユーザーと共有できます。 

新しいノートブックを作成するには: 

1. [Azure Machine Learning Studio](https://ml.azure.com) で、ワークスペースを開きます。
1. 左側にある **[Notebooks]\(ノートブック\)** を選択します。 
1. **[My files]\(マイ ファイル\)** セクションの **[User files]\(ユーザー ファイル\)** リストの上にある **[新しいファイルの作成]** アイコンを選択します。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="[新しいファイルの作成]":::

1. ファイルに名前を付けます。 
1. Jupyter Notebook Files の場合は、ファイルの種類として **[Python Notebook]\(Python ノートブック\)** を選択します。
1. ファイル ディレクトリを選択します。
1. **［作成］** を選択します

> [!TIP]
> テキスト ファイルを作成することもできます。  ファイルの種類として **[テキスト]** を選択し、拡張子を名前に追加します (たとえば、myfile.py、myfile.txt)。  

[Notebooks]\(ノートブック\) ページの上部にあるツールを使用して、ノートブックを含むフォルダーとファイルをアップロードすることもできます。  ノートブックとほとんどのテキスト ファイルの種類はプレビュー セクションに表示されます。  他のほとんどのファイルの種類ではプレビューを使用できません。

### <a name="clone-samples"></a>サンプルを複製する

ワークスペースには **Samples** フォルダーがあり、SDK を探索するために役立ち、独自の機械学習プロジェクトの例として利用できるように設計されたノートブックが格納されています。  これらのノートブックをワークスペース ストレージ コンテナーに作成したフォルダーに複製することができます。  

例については、「[Tutorial:初めての ML 実験を作成する](tutorial-1st-experiment-sdk-setup.md#azure)」を参照してください。

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a> Git のファイルを使用し、ファイルのバージョン管理を行う

ターミナル ウィンドウを使用してすべての Git 操作にアクセスできます。 すべての Git ファイルとフォルダーは、ワークスペース ファイル システムに格納されます。

> [!NOTE]
> すべての Jupyter 環境で表示されるように、 **~/cloudfiles/code/Users** フォルダー以下の任意の場所にファイルとフォルダーを追加します。

ターミナルにアクセスするには:

1. [Azure Machine Learning Studio](https://ml.azure.com) で、ワークスペースを開きます。
1. 左側にある **[Notebooks]\(ノートブック\)** を選択します。
1. 左側の **[User files]\(ユーザー ファイル\)** セクションにある任意のノートブックを選択します。  ノートブックがない場合は、まず[ノートブックを作成](#create)します
1. **[コンピューティング]** ターゲットを選択するか、新しいターゲットを作成し、それが実行されるまで待ちます。
1. **[Open terminal]\(ターミナルを開く\)** アイコンを選択します。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="ターミナルを開きます":::

1. アイコンが表示されない場合は、コンピューティング ターゲットの右側にある **[...]** を選択し、 **[Open terminal]\(ターミナルを開く\)** を選択します。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="[...] の [Open terminal]\(ターミナルを開く\)":::


詳細については、[Git リポジトリをワークスペース ファイル システムに複製する](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)方法に関する記事を参照してください。


### <a name="share-notebooks-and-other-files"></a>ノートブックとその他のファイルを共有する

ノートブックまたはファイルを共有するには、URL をコピーして貼り付けます。  ワークスペースの他のユーザーのみがこの URL にアクセスできます。  詳細については、[ワークスペースへのアクセスの許可](how-to-assign-roles.md)に関する記事を参照してください。

## <a name="edit-a-notebook"></a>ノートブックを編集する

ノートブックを編集するには、ワークスペースの **[User files]\(ユーザー ファイル\)** セクションにあるノートブックを開きます。 編集するセルをクリックします。 

コンピューティング インスタンスの実行が実行中の場合、任意の Python ノートブックで [Intellisense](https://code.visualstudio.com/docs/editor/intellisense) によるコード補完を使用することもできます。

Notebook ツールバーから Jupyter または JupyterLab を起動することもできます。  Azure Machine Learning では、Jupyter または JupyterLab からの更新プログラムの提供やバグの修正は行われません。これは、Microsoft サポートの範囲に含まれないオープン ソース製品であるためです。

### <a name="useful-keyboard-shortcuts"></a>便利なキーボード ショートカット

|[キーボード]  |アクション  |
|---------|---------|
|Shift + Enter     |  セルを実行する       |
|Ctrl + M (Windows)     |  ノートブックのタブ トラップを有効または無効にします。       |
|Ctrl + Shift + M (Mac と Linux)     |    ノートブックのタブ トラップを有効または無効にします。     |
|Tab (タブ トラップが有効な場合) | '\t' 文字 (インデント) を追加します
|Tab (タブ トラップが無効な場合) | フォーカスを次のフォーカス可能な項目に変更します (セルの削除ボタン、実行ボタンなど)

## <a name="delete-a-notebook"></a>ノートブックを削除する

**Samples** ノートブックを削除することは "*できません*"。  これらのノートブックは Studio の一部であり、新しい SDK が発行されるたびに更新されます。  

**ユーザー ファイル** ノートブックは、次のいずれかの方法で削除 "*できます*"。

* Studio で、フォルダーまたはファイルの末尾にある **[...]** を選択します。  必ずサポートされているブラウザー (Microsoft Edge、Chrome、または Firefox) を使用してください。
* 任意の Notebook ツールバーから [ **[Open terminal]\(ターミナルを開く\)** ](#terminal) を選択して、コンピューティング インスタンスのターミナル ウィンドウにアクセスします。
* Jupyter または JupyterLab で付属のツールを使用します。

## <a name="run-an-experiment"></a>実験を実行する

ノートブックから実験を実行するには、まず、実行中の[コンピューティング インスタンス](concept-compute-instance.md)に接続します。 コンピューティング インスタンスがない場合は、次の手順に従って作成します。 

1. Notebook ツールバーの **[+]** を選択します。 
2. コンピューティングに名前を付け、 **[仮想マシン サイズ]** を選択します。 
3. **［作成］** を選択します
4. コンピューティング インスタンスがノートブックに自動的に接続され、セルを実行できるようになります。

作成したコンピューティング インスタンスを表示および使用できるのは自分のみです。  **ユーザー ファイル**は VM とは別に格納され、ワークスペース内のすべてのコンピューティング インスタンス間で共有されます。

### <a name="view-logs-and-output"></a>ログと出力を表示する

[Notebook ウィジェット](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)を使用して、実行の進行状況とログを表示します。 ウィジェットは非同期であり、トレーニングが終了するまで更新情報が表示されます。 Azure Machine Learning ウィジェットは、Jupyter および JupterLab でもサポートされています。

## <a name="change-the-notebook-environment"></a>ノートブック環境を変更する

Notebook ツールバーを使用すると、ノートブックを実行する環境を変更できます。  

このようなアクションを実行しても、ノートブックの状態またはノートブック内の変数の値は変更されません。

|アクション  |結果  |
|---------|---------| --------|
|カーネルを停止する     |  任意の実行中のセルを停止します。 セルを実行すると、カーネルが自動的に再起動されます。 |
|別のワークスペース セクションに移動する     |     実行中のセルは停止されます。 |

これらのアクションを実行すると、ノートブックの状態はリセットされ、ノートブック内のすべての変数がリセットされます。

|アクション  |結果  |
|---------|---------| --------|
| カーネルを変更する | Notebook に新しいカーネルが使用されます |
| コンピューティングを切り替える    |     Notebook には新しいコンピューティングが自動的に使用されます。 |
| コンピューティングをリセットする | セルを実行しようとすると再起動します |
| コンピューティングを停止する     |    セルは実行されません  |
| Jupyter または JupyterLab でノートブックを開く     |    新しいタブでノートブックが開きます。  |

### <a name="add-new-kernels"></a>新しいカーネルを追加する

Notebook によって、接続されたコンピューティング インスタンスにインストールされているすべての Jupyter カーネルが自動的に検出されます。  コンピューティング インスタンスにカーネルを追加するには:

1. Notebook ツールバーの [ **[Open terminal]\(ターミナルを開く\)** ](#terminal) を選択します。
1. ターミナル ウィンドウを使用して、新しい環境を作成します。
1. 環境をアクティブにします。  `newenv` を作成した後の例を次に示します。

    ```shell
    source activate newenv
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

任意の[使用可能な Jupyter カーネル](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels)をインストールできます。

### <a name="status-indicators"></a>状態インジケーター

**[コンピューティング]** ドロップダウンの横のインジケーターには、その状態が表示されます。  状態は、ドロップダウン リストにも表示されます。  

|Color |コンピューティング状態 |
|---------|---------| 
| [緑] | コンピューターは実行中 |
| [赤] |コンピューティングの失敗 | 
| Black | コンピューティングの停止 |
|  淡い青 |コンピューティングの作成、開始、再起動、設定 |
|  グレー |コンピューティングの削除、停止 |

**[カーネル]** ドロップダウンの横にあるインジケーターには、その状態が表示されます。

|Color |カーネルの状態 |
|---------|---------|
|  [緑] |カーネル接続済み、アイドル、ビジー|
|  グレー |カーネルが接続されていません |

## <a name="find-compute-details"></a>コンピューティングの詳細を確認する 

コンピューティング インスタンスの詳細については、[Studio](https://ml.azure.com) の**コンピューティング**に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [最初の実験を実行する](tutorial-1st-experiment-sdk-train.md)
* [スナップショットを使用してファイル ストレージをバックアップする](../storage/files/storage-snapshots-files.md)
