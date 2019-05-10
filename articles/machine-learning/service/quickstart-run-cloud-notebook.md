---
title: クイック スタート:クラウドでノートブックを実行する
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service の利用を開始します。 クラウドのマネージド ノートブック サーバーを使用してワークスペースを試してみましょう。  ワークスペースは、機械学習モデルの実験、トレーニング、およびデプロイを行うために使用する、クラウドでの基礎ブロックとなります。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 1a48f8620fb99f1cf8787dabc738d328a796d093
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510611"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>クイック スタート:クラウドベースのノートブック サーバーを使用して Azure Machine Learning の利用を開始する

クラウドベースのノートブック サーバーを作成し、それを使用します。  このクイック スタートでは、[Azure Machine Learning service ワークスペース](concept-azure-machine-learning-architecture.md)に値を記録する Python コードを実行します。 ワークスペースは、Machine Learning で機械学習モデルの実験、トレーニング、およびデプロイを行うために使用する、クラウドでの基礎ブロックとなります。 

このクイック スタートでは、Azure Machine Learning の実行に必要な Python 環境で構成されている Azure Machine Learning ワークスペースにクラウド リソースを作成する方法を示します。 独自の環境を使用する場合は、次を参照してください: 「[クイック スタート: 独自のノートブック サーバーを使用して Azure Machine Learning の利用を開始する](quickstart-run-local-notebook.md)」。  

このクイック スタートでは、以下のアクションを実行します。

* ワークスペースに新しいクラウドベースのノートブック サーバーを作成する。
* Jupyter の Web インターフェイスを起動する。
* pi を推定するコードが含まれていて、各イテレーションでのエラーがログに記録されるノートブックを開く。
* ノートブックを実行する。
* ログに記録したエラー値をワークスペースで表示します。 この例は、スクリプトで生成された情報の追跡にワークスペースがいかに役立つかを示しています。

Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

## <a name="create-a-workspace"></a>ワークスペースの作成

Azure Machine Learning service ワークスペースがある場合は、[次のセクション](#create-a-cloud-based-notebook-server)に進みます。 ワークスペースがない場合は、ここで作成します。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-a-cloud-based-notebook-server"></a>クラウドベースのノートブック サーバーを作成する

 ワークスペースで、Jupyter ノートブックの使用を開始するためのクラウド リソースを作成します。 このリソースによって、Azure Machine Learning service を実行するために必要なすべてのものが事前構成されたクラウドベースのプラットフォームが提供されます。

1. [Azure Portal](https://portal.azure.com/) でワークスペースを開きます。  ポータルでワークスペースを見つける方法がわからない場合は、[ワークスペースを探す](how-to-manage-workspace.md#view)方法に関するページを参照してください。

1. Azure portal のワークスペース ページで、左側の **[ノートブック VM]** を選択します。

1. **[+ 新規]** を選択して、ノートブック VM を作成します。

     ![新しい VM を選択する](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. VM の名前を指定します。 **[作成]** を選択します。

    > [!NOTE]
    > Notebook VM 名は 2 文字から 16 文字にする必要があります。 英字、数字、- の文字が有効です。  名前は、Azure サブスクリプション内で一意である必要もあります。

    ![新しい仮想マシンを作成する](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. 状態が **[実行中]** に変わるまで、4 から 5 分間待ちます。

## <a name="launch-jupyter-web-interface"></a>Jupyter の Web インターフェイスを起動する

VM が実行中になった後、**[ノートブック VM]** セクションを使用して、Jupyter の Web インターフェイスを起動します。

1. VM の **[URI]** 列で、**[Jupyter]** を選択します。  

    ![Jupyter ノートブック サーバーを開始する](./media/quickstart-run-cloud-notebook/start-server.png)

    リンクから、ノートブック サーバーが起動され、新しいブラウザー タブで Jupyter ノートブックの Web ページが開かれます。このリンクは、VM を作成するユーザーに対してのみ動作します。

1. Jupyter ノートブックの Web ページで、最上位のフォルダー名はユーザー名です。  このフォルダーを選択します。

1. サンプルのフォルダー名には、**samples-1.0.33.1** のようにバージョン番号が含まれています。  サンプル フォルダーを選択します。

1. **quickstart** ノートブックを選択します。

## <a name="run-the-notebook"></a>ノートブックを実行する

pi を推定し、エラーをワークスペースのログに記録するノートブックを実行します。

1. **01.run-experiment.ipynb** を選択してノートブックを開きます。

1. 最初のコード セルをクリックし、**[実行]** を選択します。

    > [!NOTE]
    > コード セルの前には、角かっこが表示されます。 角かっこが空 (__[  ]__) の場合、コードはまだ実行されていません。 コードの実行中は、アスタリスクが表示されます (__[*]__)。 コードが完了すると、番号 **[1]** が表示されます。  番号は、セルが実行された順番を示します。
    >
    > セルを実行するショートカットとして、**Shift + Enter** キーを使用します。

    ![最初のコード セルを実行する](media/quickstart-run-cloud-notebook/cell1.png)

1. 2 つ目のコード セルを実行します。 認証の手順が表示されたら、コードをコピーし、リンクに従ってサインインします。 サインインすると、ブラウザーによってこの設定が記憶されます。  

    ![認証](media/quickstart-run-cloud-notebook/authenticate.png)

1. 完了すると、セルの番号 __[2]__ が表示されます。  サインインする必要があった場合は、認証成功の状態メッセージが表示されます。   サインインする必要がなかった場合は、このセルの出力は表示されません。セルが正常に実行されたことを示すために、番号のみが表示されます。

    ![成功メッセージ](media/quickstart-run-cloud-notebook/success.png)

1. 残りのコード セルを実行します。  各セルの実行が完了すると、そのセルの番号が表示されます。 最後のセルだけで、その他の出力が表示されます。  

    最大のコード セルで、`run.log` が複数の場所で使用されていることがわかります。 各 `run.log` で、その値がワークスペースに追加されます。

## <a name="view-logged-values"></a>ログに記録された値を表示する

1. `run` セルからの出力には、ワークスペースで実験結果を表示するための Azure portal へのリンクが含まれています。

    ![実験を表示する](./media/quickstart-run-cloud-notebook/view-exp.png)

1. 実行に関する情報をワークスペースで表示するために、**[Link to Azure portal]\(Azure portal へのリンク\)** をクリックします。  このリンクにより、Azure portal のワークスペースが開きます。

1. ログに記録された値のプロットが自動的に作成された、ワークスペースに表示されます。 同じ name パラメーターで複数の値をログに記録するときは常に、プロットが自動的に生成されます。

   ![履歴を表示する](./media/quickstart-run-cloud-notebook/web-results.png)

Pi を概算するコードではランダムな値が使用されるため、実際のプロットには異なる値が表示されます。  

## <a name="clean-up-resources"></a>リソースのクリーンアップ

### <a name="stop-the-notebook-vm"></a>ノートブック VM を停止する

ノートブック VM を使用していないときは、コストを削減するために、ノートブック VM を停止します。  

1. ワークスペースで、**[ノートブック VM]** を選択します。

   ![VM サーバーを停止する](./media/quickstart-run-cloud-notebook/stop-server.png)

1. 一覧から VM を選択します。

1. **[停止]** を選択します。

1. サーバーを再び使用する準備が整ったら、**[開始]** を選択します。

### <a name="delete-everything"></a>すべてを削除する

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

リソース グループは保持しつつ、いずれかのワークスペースを削除することもできます。 ワークスペースのプロパティを表示し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、以下のタスクを完了しました。

* ノートブック VM を作成する。
* Jupyter の Web インターフェイスを起動する。
* pi を推定するコードが含まれていて、各イテレーションでのエラーがログに記録されるノートブックを開く。
* ノートブックを実行する。
* ログに記録したエラー値をワークスペースで表示します。  この例は、スクリプトで生成された情報の追跡にワークスペースがいかに役立つかを示しています。 

Jupyter Notebook の Web ページで、サンプル フォルダーにある他のノートブックを参照し、Azure Machine Learning service の詳細をご確認ください。

ワークフローを詳しく体験するには、Machine Learning チュートリアルに従って、モデルをトレーニングし、デプロイします。  

> [!div class="nextstepaction"]
> [チュートリアル:画像分類モデルをトレーニングする](tutorial-train-models-with-aml.md)
