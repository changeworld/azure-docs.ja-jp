---
title: 'クイック スタート: Azure portal で機械学習サービス ワークスペースを作成する - Azure Machine Learning'
description: Azure portal を使用して Azure Machine Learning ワークスペースを作成します。 このワークスペースが、Azure Machine Learning で機械学習モデルの実験、トレーニング、およびデプロイを行うために使用される、クラウドでの基礎ブロックとなります。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: rastala
ms.author: roastala
ms.date: 09/24/2018
ms.openlocfilehash: b81e40298eae0f0b44f37e7f8f16beaddad999a5
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456815"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning"></a>クイック スタート: Azure portal を使用した Azure Machine Learning の基本操作

このクイック スタートでは、Azure portal を使用して Azure Machine Learning ワークスペースを作成します。 このワークスペースが、Machine Learning で機械学習モデルの実験、トレーニング、およびデプロイを行うために使用される、クラウドでの基礎ブロックとなります。 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]

このクイック スタートでは次の作業を行います。

* Azure サブスクリプションでワークスペースを作成します。
* Azure notebook で Python を試し、数回の繰り返しにわたる値をログに記録する。
* ログに記録した値をワークスペースで表示する。

以下の Azure リソースは、リージョンで利用可能になると、ワークスペースに自動的に追加されます。

  - [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
  - [Azure Storage](https://azure.microsoft.com/services/storage/)
  - [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
  - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

作成するリソースは、Machine Learning サービスに関連したその他のチュートリアルおよびハウツー記事の前提条件として使用できます。 他の Azure サービスと同様に、Machine Learning に関連付けられている特定のリソースにも制限があります。 たとえば、Azure Batch AI クラスターのサイズです。 既定の制限と、クォータを増やす方法については、[この記事](how-to-manage-quotas.md)を参照してください。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。


## <a name="create-a-workspace"></a>ワークスペースの作成 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

ワークスペース ページで、`Explore your Azure Machine Learning service workspace` を選択します。

 ![ワークスペースを試す](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>ワークスペースの使用

それでは、機械学習スクリプトの管理にワークスペースがいかに役立つかご覧ください。 このセクションでは、次の作業を行います。

* Azure Notebooks でノートブックを開く。
* ログに記録される値を作成するコードを実行する。
* ログに記録した値をワークスペースで表示する。

この例は、スクリプトで生成された情報の追跡にワークスペースがいかに役立つかを示しています。 

### <a name="open-a-notebook"></a>ノートブックを開く 

Azure Notebooks では、Jupyter ノートブック向けの無料のクラウド プラットフォームが提供されます。Machine Learning の実行に必要なすべてのものが事前構成されています。  

初めての実験を試すには、[`Open Azure Notebooks`] を選択します。

 ![[Open Azure Notebooks]\(Azure Notebooks を開く\)](./media/quickstart-get-started/explore_ws.png)

組織によっては、サインインするには[管理者の同意](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent)が必要な場合があります。

サインイン後、新しいタブが開き、`Clone Library` プロンプトが表示されます。 `Clone` を選択します


### <a name="run-the-notebook"></a>ノートブックを実行する

2 つのノートブックと共に `config.json` ファイルが表示されます。 この構成ファイルには、作成したワークスペースに関する情報が含まれています。  

`01.run-experiment.ipynb` を選択してノートブックを開きます。

セルを 1 つずつ実行するには、`Shift`+`Enter` キーを使用します。 ノートブック全体を実行するには、[`Cells`] > [`Run All`] を選択します。 セルの横にアスタリスク (*) が表示されているときは実行中です。 そのセルのコードが完了すると、数値が表示されます。

サインインを要求される場合があります。 メッセージ内のコードをコピーします。 次にリンクを選択して、コードを新しいウィンドウに貼り付けます。 コードの前後にスペースが入らないようにしてください。 サインインには、Azure portal で使用したアカウントを使用します。

 ![ログイン](./media/quickstart-get-started/login.png)

ノートブックで、2 番目のセルが `config.json` から読み取り、ワークスペースに接続します。
```
ws = Workspace.from_config()
```

コードの 3 番目のセルが "my-first-experiment" という名前の実験を開始します。 ワークスペースに戻ったとき、この名前を使用して実行に関する情報を検索します。

```
experiment = Experiment(workspace_object=ws, name = "my-first-experiment")
```

ノートブックの最後のセルで、ログ ファイルに書き込まれる値に注意してください。

```
# Log final results
run.log("Final estimate: ",pi_estimate)
run.log("Final error: ",math.pi-pi_estimate)
```

この値はコードの実行後にワークスペースで参照できます。

## <a name="view-logged-values"></a>ログに記録された値を表示する

ノートブックのすべてのセルを実行したら、ポータル ページに戻ります。  

[`View Experiments`] を選択します。

![実験を表示する](./media/quickstart-get-started/view_exp.png)

`Reports` ポップアップを閉じます。

[`my-first-experiment`] を選択します。

完了した実行に関する情報を確認します。 ページを下へスクロールして、実行テーブルを見つけます。 実行番号リンクを選択します。

 ![実行履歴リンク](./media/quickstart-get-started/report.png)

ログに記録された値を基に自動的に作成されたプロットが表示されます。  

   ![履歴を表示する](./media/quickstart-get-started/plots.png)

Pi を概算するコードではランダムな値が使用されるため、実際のプロットには異なる値が表示されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

リソース グループは保持して、1 つのワークスペースを削除することもできます。 ワークスペースのプロパティを表示し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

実験してモデルをデプロイするために必要なリソースを作成しました。 ノートブックでいくつかのコードも実行しました。 クラウドのワークスペースで、そのコードの実行履歴を確認しました。

ワークフローを詳しく体験するには、Machine Learning チュートリアルに従って、モデルをトレーニングし、デプロイします。  

> [!div class="nextstepaction"]
> [チュートリアル: 画像分類モデルをトレーニングする](tutorial-train-models-with-aml.md)
