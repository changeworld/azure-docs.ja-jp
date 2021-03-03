---
title: クラウドで Azure Percept の高度な開発を始める
description: クラウドで Jupyter Notebook と Azure Machine Learning を使用して高度な開発を始めます
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 408040ea68273a29ed9be87b60bd0cc6da736a05
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658764"
---
# <a name="getting-started-with-advanced-development-in-the-cloud-via-jupyter-notebooks-and-azure-machine-learning"></a>Jupyter Notebook と Azure Machine Learning を使用したクラウドでの高度な開発の概要

この記事では、Azure Machine Learning ワークスペースの設定、ワークスペースへの事前設定済みサンプル Jupyter Notebook のアップロード、コンピューティング インスタンスの作成、およびワークスペース内でのノートブックの各セルの実行の各プロセスについて順を追って説明します。

この[ノートブック](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb)は、Python で Azure ML の事前トレーニング済み TensorFlow モデル (MobileNetSSDV2Lite) とカスタム データセットを使用し、転移学習を実施して、ボウルを検出します。

ノートブックでは、[COCO](https://cocodataset.org/#home) を起点として目的のクラス (ボウル) のみに絞り込み、適切な形式に変換する方法を示しています。 オープンソースの [VoTT 2](https://github.com/microsoft/VoTT) ラベル付けツールを使用して、PASCAL VOC 形式で境界ボックスを作成してラベル付けすることもできます。

カスタム データセットでのモデルの再トレーニング後、モジュール ツイン更新法を使用してモデルを Azure Percept DK にデプロイできます。

その後、Azure Percept Vision SoM のライブ RTSP ストリームを表示することで、モデルの推論結果を確認できます。 モデルの再トレーニングとデプロイはどちらも、クラウドのノートブック内で実行されます。

## <a name="prerequisites"></a>前提条件

- [Azure Machine Learning のサブスクリプション](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure Percept Vision SoM を接続済みの Azure Percept DK](./overview-azure-percept-dk.md)
- [Azure Percept DK のオンボーディング エクスペリエンス](./quickstart-percept-dk-set-up.md)を完了済み

## <a name="download-azure-percept-github-repository"></a>Azure Percept GitHub リポジトリのダウンロード

1. [Azure Percept GitHub リポジトリ](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview)にアクセスします。

1. リポジトリをクローンするか、ZIP ファイルをダウンロードします。 画面の上部付近にある **[Code]\(コード\)**  ->  **[Download ZIP]\(ZIP のダウンロード\)** の順にクリックします。

    :::image type="content" source="./media/advanced-development-cloud/download-zip.png" alt-text="GitHub のダウンロード画面。":::

## <a name="set-up-azure-machine-learning-portal-and-notebook"></a>Azure Machine Learning ポータルとノートブックの設定

1. [Azure Machine Learning ポータル](https://ml.azure.com)にアクセスします。

1. ドロップダウン メニューで、使用するディレクトリ、Azure サブスクリプション、Machine Learning ワークスペースを選択して、 **[作業の開始]** をクリックします。

    :::image type="content" source="./media/advanced-development-cloud/machine-learning-portal-get-started.png" alt-text="Azure ML の作業の開始画面。":::

    Azure Machine Learning ワークスペースがまだない場合は、 **[新しいワークスペースの作成]** をクリックします。 新しいブラウザー タブで、次の手順を実行します。

    1. Azure サブスクリプションを選択します。
    1. リソース グループを選択します。
    1. ワークスペースの名前を入力します。
    1. 自分のリージョンを選択します。
    1. ワークスペースのエディションを選択します。
    1. **[確認と作成]** をクリックします。
    1. 次のページで、選択内容を確認して **[作成]** をクリックします。

        :::image type="content" source="./media/advanced-development-cloud/workspace-review-and-create.png" alt-text="Azure ML のワークスペース作成画面。":::

    ワークスペースの作成には数分かかります。 ワークスペースの作成が完了すると、Machine Learning service の概要ページで自分のリソースの横に緑色のチェック マークが付き、ページ上部に "**デプロイが完了しました**" と表示されます。

    :::image type="content" source="./media/advanced-development-cloud/workspace-creation-complete-inline.png" alt-text="ワークスペースの作成の確認。" lightbox= "./media/advanced-development-cloud/workspace-creation-complete.png":::

    ワークスペースの作成が完了したら、Azure Machine Learning ポータルのタブに戻り、 **[作業の開始]** をクリックします。

1. Machine Learning ワークスペースのホームページで、左側ペインの **[Notebooks]\(ノートブック\)** をクリックします。

    :::image type="content" source="./media/advanced-development-cloud/notebook.png" alt-text="Azure ML のホームページ。":::

1. **[My files]\(マイ ファイル\)** タブで、上向きの矢印をクリックして .ipynb ファイルをアップロードします。

    :::image type="content" source="./media/advanced-development-cloud/upload-files.png" alt-text="[ファイルのアップロード] アイコンを強調表示したホームページ。":::

1. Azure Percept の GitHub リポジトリのローカル コピーに含まれる [Transferlearningusing_SSDLiteV2 Model.ipynb ファイル](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb)を参照し、選択します。 **[開く]** をクリックします。 **[ファイルのアップロード]** ウィンドウで、 **[I trust contents from this file]\(このファイルのコンテンツを信頼する\)** の横にあるチェック ボックスをオンにして、 **[アップロード]** をクリックします。

    :::image type="content" source="./media/advanced-development-cloud/select-file.png" alt-text="ファイル選択画面。":::

1. 右上のメニュー バーで、 **[コンピューティング]** の状態を確認します。 コンピューティングが表示されていない場合は、 **[+]** アイコンをクリックして新しいコンピューティングを作成します。

    :::image type="content" source="./media/advanced-development-cloud/no-computes-found-inline.png" alt-text="[+] を強調表示したコンピューティング状態。" lightbox= "./media/advanced-development-cloud/no-computes-found.png":::

1. **[New compute instance]\(新しいコンピューティング インスタンス\)** ウィンドウで、 **[Compute name]\(コンピューティング名\)** を入力して、 **[仮想マシンの種類]** と **[仮想マシンのサイズ]** を選択します。 **Create** をクリックしてください。

    :::image type="content" source="./media/advanced-development-cloud/new-compute-instance.png" alt-text="コンピューティング インスタンスの新規作成画面。":::

    **[作成]** をクリックすると、 **[コンピューティング]** 状態に青い丸が付き、" **\<your compute name> - 作成中**" と表示されます。

    :::image type="content" source="./media/advanced-development-cloud/compute-creating.png" alt-text="コンピューティングの作成が進行中であるときのコンピューティング状態。":::

    コンピューティングの作成が完了すると、**コンピューティング** の状態に緑色の丸が付き、" **\<your compute name> - 実行中**" と表示されます。

    :::image type="content" source="./media/advanced-development-cloud/compute-running.png" alt-text="コンピューティングの作成が完了したことを示すコンピューティング状態。":::

1. コンピューティングが実行中になったら、 **[+]** アイコンの横にあるドロップダウン メニューで **[Python 3.6 - AzureML]** を選択します。

## <a name="working-with-the-notebook"></a>ノートブックの使用

これで、ノートブックを実行してカスタムのボウル検出器をトレーニングし、開発キットにデプロイする準備が整いました。 スクリプトを実行するには一部のセルに入力パラメーターを指定する必要があるため、ノートブックの各セルは 1 つずつ実行してください。 入力パラメーターが必要なセルは、ノートブックで直接編集することもできます。 セルを実行するには、セル左側にある再生アイコンをクリックします。

:::image type="content" source="./media/advanced-development-cloud/run-cell-inline.png" alt-text="セルのアイコンを強調表示したノートブック。" lightbox= "./media/advanced-development-cloud/run-cell.png":::

## <a name="next-steps"></a>次のステップ

他の Azure Machine Learning service のサンプル ノートブックについては、こちらの[リポジトリ](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml)を参照してください
