---
title: チュートリアル:初めての ML 実験を作成する
titleSuffix: Azure Machine Learning
description: このチュートリアルでは、Jupyter Notebook で実行されている Azure Machine Learning Python SDK を初めて使う場合について説明します。  パート 1 では、実験と ML モデルを管理するワークスペースを作成します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/25/2019
ms.openlocfilehash: 125cde85d3caa6fd1a33045e85c5383f83a669bf
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982536"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>チュートリアル:Python SDK で初めての ML 実験を作成する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

このチュートリアルでは、Jupyter ノートブックで実行されている Azure Machine Learning Python SDK の使用を開始するためのエンドツーエンドの手順を完了します。 このチュートリアルは、**2 部構成のチュートリアル シリーズのパート 1** です。Python 環境の設定と構成のほか、実験と機械学習モデルを管理するためのワークスペースの作成について説明します。 [**パート 2**](tutorial-1st-experiment-sdk-train.md) では、これを基に、複数の機械学習モデルをトレーニングし、Azure Machine Learning Studio と SDK の両方を使用したモデル管理プロセスを紹介します。

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * 次のチュートリアルで使用する [Azure Machine Learning ワークスペース](concept-workspace.md)を作成します。
> * チュートリアル ノートブックをワークスペース内のフォルダーに複製します。
> * Azure Machine Learning Python SDK がインストールされ、事前構成されたクラウドベースのコンピューティング インスタンスを作成します。


Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

## <a name="create-a-workspace"></a>ワークスペースの作成

Azure Machine Learning ワークスペースは、機械学習モデルを実験、トレーニング、およびデプロイするために使用する、クラウドでの基本的なリソースです。 ワークスペースは、Azure サブスクリプションとリソース グループを、サービス内の簡単に使用できるオブジェクトに結び付けます。 

ワークスペースを作成するには、Azure リソースを管理するための Web ベースのコンソールである Azure portal を使用します。 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> お使いの**ワークスペース**と**サブスクリプション**をメモしておきます。 これらは、適切な場所に実験を作成するために必要になります。 

## <a name="azure"></a>ワークスペースでノートブックを実行する

このチュートリアルでは、インストール不要であらかじめ構成されているエクスペリエンスを実現するために、お使いのワークスペースでクラウド ノートブック サーバーを使用します。 お使いの環境、パッケージ、および依存関係を制御したい場合は、[独自の環境](how-to-configure-environment.md#local)を使用してください。

次のビデオに従って、または後で示す詳細な手順を使用して、ワークスペースでチュートリアルを複製して実行します。 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]



### <a name="clone-a-notebook-folder"></a>ノートブック フォルダーを複製する

Azure Machine Learning Studio で、次の実験の設定を完了し、ステップを実行します。Azure Machine Learning Studio は、あらゆるスキル レベルのデータ サイエンス実務者がデータ サイエンス シナリオを実行するための機械学習ツールを含む統合インターフェイスです。

1. [Azure Machine Learning Studio](https://ml.azure.com/) にサインインします。

1. お使いのサブスクリプションと、作成したワークスペースを選択します。

1. 左側にある **[ノートブック]** を選択します。

1. **Samples** フォルダーを開きます。

1. **Python** フォルダーを開きます。

1. バージョン番号が付いたフォルダーを開きます。  この番号は、Python SDK の現在のリリースを表します。

1. **tutorials** フォルダーの右側にある **[...]** を選択し、 **[Clone]\(複製\)** を選択します。

    ![フォルダーを複製する](./media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. フォルダーの一覧には、ワークスペースにアクセスする各ユーザーが表示されます。  自分のフォルダーを選択して **tutorials** フォルダーをそこに複製します。

### <a name="a-nameopenopen-the-cloned-notebook"></a><a name="open">複製されたノートブックを開く

1. **[User Files]\(ユーザー ファイル\)** の下の自分のフォルダーを開き、複製された **tutorials** フォルダーを開きます。

    ![tutorials フォルダーを開く](./media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > **samples** フォルダー内のノートブックを表示できますが、そこからノートブックを実行することはできません。  ノートブックを実行するには、必ず **[User Files]\(ユーザー ファイル\)** セクションにあるノートブックの複製バージョンを開いてください。
    
1. **tutorials** フォルダー内の **tutorial-1st-experiment-sdk-train.ipynb** ファイルを選択します。

1. 上部のバーで、ノートブックの実行に使用するコンピューティング インスタンスを選択します。 これらの VM には、[Azure Machine Learning を実行するために必要なすべてのもの](concept-compute-instance.md#contents)があらかじめ構成されています。 対象のワークスペースの任意のユーザーが作成した VM を選択できます。 

1. VM が見つからない場合は、 **[追加]** を選択してコンピューティング インスタンス VM を作成します。 

    1. VM を作成するときは名前を指定します。  名前は 2 文字から 16 文字にする必要があります。 有効な文字は、英字、数字、および - 文字です。また、名前は、Azure サブスクリプション全体で一意である必要があります。

    1.  利用可能な選択肢から仮想マシンのサイズを選択します。

    1. **[作成]** を選択します。 VM の設定には約 5 分かかる可能性があります。

1. VM が使用可能になると、上部のツール バーに表示されます。  これで、ツール バーの **[Run all]\(すべて実行\)** を使用するか、ノートブックのコード セルで **Shift + Enter** キーを使用して、ノートブックを実行できるようになりました。

カスタム ウィジェットがある場合、または Jupyter/JupyterLab を使用する場合は、右端にある **[Jupyter]** ドロップダウンを選択し、 **[Jupyter]** または **[JupyterLab]** を選択します。 新しいブラウザー ウィンドウが開きます。

> [!NOTE]
> コンピューティング インスタンス (プレビュー) は現在、**米国中北部**、**米国東部 2**、**北ヨーロッパ**、または**英国南部**リージョンのワークスペースでのみ使用できます。他のリージョンは近日中にサポートされる予定です。
>ワークスペースが他のリージョンにある場合は、代わりに [Notebook VM](concept-compute-instance.md#notebookvm) を引き続き作成して使用することができます。  ノートブックを実行するには、Notebook VM またはコンピューティング インスタンスを使用できます。 VM は、停止しない限り実行し続けて、コストが発生します。 Studio の **[Compute]\(コンピューティング\)** タブからそれを手動で停止できます。または、Azure VM に対してスケジュールされた自動シャットダウンを有効にする方法についての[ブログ記事](https://techcommunity.microsoft.com/t5/educator-developer-blog/azure-virtual-machine-auto-shutdown/ba-p/379342)を参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のタスクを完了しました。

* Azure Machine Learning ワークスペースを作成しました。
* お使いのワークスペース内にクラウド ノートブック サーバーを作成して構成しました。

チュートリアルの**パート 2** では、`tutorial-1st-experiment-sdk-train.ipynb` のコードを実行して機械学習モデルをトレーニングします。 

> [!div class="nextstepaction"]
> [チュートリアル:最初のモデルをトレーニングする](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> このチュートリアルのパート 2 や他のチュートリアルに取り組む予定がない場合は、不使用時のコストを抑えるために、[クラウド ノートブック サーバー VM を停止](tutorial-1st-experiment-sdk-train.md#clean-up-resources)してください。


