---
title: Azure portal を使用したクイック スタート
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service の利用を開始します。 Azure portal を使用して、機械学習モデルの実験、トレーニング、およびデプロイを行うために使用される、クラウドでの基礎ブロックとなるワークスペースを作成します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 01/18/2019
ms.custom: seodec18
ms.openlocfilehash: 9d441ed408f9c9348ba091178cf6ce84d67abb3a
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672935"
---
# <a name="quickstart-use-the-azure-portal-to-get-started-with-azure-machine-learning"></a>クイック スタート:Azure portal を使用した Azure Machine Learning の基本操作

Azure portal を使用して Azure Machine Learning ワークスペースを作成します。 このワークスペースが、Machine Learning で機械学習モデルの実験、トレーニング、およびデプロイを行うために使用される、クラウドでの基礎ブロックとなります。 このクイック スタートではクラウド リソースを使用するため、インストールは不要です。 代わりに独自の Jupyter ノートブック サーバーを構成するには、[Python を使用して Azure Machine Learning の利用を開始する](quickstart-create-workspace-with-python.md)」を参照してください。  
 
このクイック スタートでは、以下のアクションを実行します。

* Azure サブスクリプションでワークスペースを作成します。
* Jupyter のノートブックで Python を試し、数回の繰り返しにわたる値をログに記録する。
* ログに記録した値をワークスペースで表示する。

以下の Azure リソースは、リージョンで利用可能になると、ワークスペースに自動的に追加されます。

  - [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
  - [Azure Storage](https://azure.microsoft.com/services/storage/)
  - [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
  - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

作成するリソースは、Machine Learning サービスに関連したその他のチュートリアルおよびハウツー記事の前提条件として使用できます。 他の Azure サービスと同様に、Machine Learning に関連付けられている特定のリソースにも制限があります。 たとえば、コンピューティング クラスターのサイズなどです。 詳細については、[既定の制限とクォータを増やす方法](how-to-manage-quotas.md)に関するページを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](http://aka.ms/AMLFree) を今日からお試しいただけます。


## <a name="create-a-workspace"></a>ワークスペースの作成 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]


## <a name="use-the-workspace"></a>ワークスペースの使用

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]



それでは、機械学習スクリプトの管理にワークスペースがいかに役立つか学習しましょう。 このセクションでは、次の手順を実行します。

* Azure Notebooks でノートブックを開く。
* ログに記録される値を作成するコードを実行する。
* ログに記録した値をワークスペースで表示する。

この例は、スクリプトで生成された情報の追跡にワークスペースがいかに役立つかを示しています。 

### <a name="open-a-notebook"></a>ノートブックを開く 

[Azure Notebooks](https://notebooks.azure.com) では、Jupyter ノートブック向けの無料のクラウド プラットフォームが提供されます。Machine Learning の実行に必要なすべてのものが事前構成されています。 ワークスペースからこのプラットフォームを起動して、Azure Machine Learning service のワークスペースを使い始めることができます。

1. ワークスペース ページで、**[Azure Machine Learning サービス ワークスペースを探索する]** を選択します。

 ![ワークスペースの探索](./media/quickstart-get-started/explore_aml.png)

1. Azure Notebooks で最初の実験を試すために、**[Azure Notebooks を開きます]** を選択します。  Azure Notebooks は、Jupyter ノートブックをクラウドで無料で実行できる独立したサービスです。  このサービスへのリンクを使用すると、Azure Notebooks で作成したライブラリに、ワークスペースへの接続方法に関する情報が追加されます。

 ![[Open Azure Notebooks]\(Azure Notebooks を開く\)](./media/quickstart-get-started/explore_ws.png)

1. Azure Notebooks にサインインします。  必ず Azure portal にサインインしたのと同じアカウントでサインインしてください。 組織によっては、サインインするには[管理者の同意](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent)が必要な場合があります。

1. サインイン後、新しいタブが開き、`Clone Library` プロンプトが表示されます。 このライブラリを複製すると、ノートブックとその他のファイルのセットが Azure Notebooks アカウントに読み込まれます。  これらのファイルは、Azure Machine Learning の機能を調べるのに役立ちます。

1. ワークスペース情報を他ユーザーと共有しないように、**[パブリック]** チェック ボックスをオフにします。

1. **[複製]** を選択します。

 ![ライブラリを複製する](./media/quickstart-get-started/clone.png)

1. プロジェクトの状態が [停止済み] と表示されたら、**[Run on Free Compute]\(無料のコンピューティングで実行\)** をクリックして無料のノートブック サーバーを使用します。

    ![無料のコンピューティングでプロジェクトを実行する](./media/quickstart-get-started/run-project.png)

### <a name="run-the-notebook"></a>ノートブックを実行する

このプロジェクトのファイルの一覧には `config.json` ファイルが含まれています。 この構成ファイルには、Azure portal で作成したワークスペースに関する情報が含まれています。  このファイルを使用すると、コードからワークスペースに接続して情報を追加できます。

1. **01.run-experiment.ipynb** を選択してノートブックを開きます。

1. ステータス領域に、カーネルが開始されるのを待機していることを示すメッセージが表示されます。  カーネルの準備が整うと、このメッセージは消えます。

    ![カーネルが起動するのを待機する](./media/quickstart-get-started/wait-for-kernel.png)

1. カーネルが起動したら、**Shift + Enter** キーを使用してセルを 1 つずつ実行します。 または、**[セル]** > **[すべて実行]** を選択してノートブック全体を実行します。 セルの横にアスタリスク __*__ が表示されている場合、そのセルはまだ実行中です。 そのセルのコードが完了すると、数値が表示されます。 

1. ノートブックの手順に従って、Azure サブスクリプションを認証します。

ノートブック内のセルをすべて実行し終えると、記録された値をワークスペースで確認できるようになります。

## <a name="view-logged-values"></a>ログに記録された値を表示する

1. `run` セルからの出力には、ワークスペースで実験結果を表示するための Azure portal へのリンクが含まれています。 

    ![実験を表示する](./media/quickstart-get-started/view_exp.png)

1. 実行に関する情報をワークスペースで表示するために、**[Link to Azure Portal]\(Azure portal へのリンク\)** をクリックします。  このリンクにより、Azure portal のワークスペースが開きます。

1. ログに記録された値のプロットが自動的に作成された、ワークスペースに表示されます。 同じ name パラメーターで複数の値をログに記録するときは常に、プロットが自動的に生成されます。

   ![履歴を表示する](./media/quickstart-get-started/web-results.png)

Pi を概算するコードではランダムな値が使用されるため、実際のプロットには異なる値が表示されます。  

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

リソース グループは保持しつつ、いずれかのワークスペースを削除することもできます。 ワークスペースのプロパティを表示し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

実験してモデルをデプロイするために必要なリソースを作成しました。 ノートブックでいくつかのコードも実行しました。 クラウドのワークスペースで、そのコードの実行履歴を確認しました。

ワークフローを詳しく体験するには、Machine Learning チュートリアルに従って、モデルをトレーニングし、デプロイします。  

> [!div class="nextstepaction"]
> [チュートリアル:画像分類モデルをトレーニングする](tutorial-train-models-with-aml.md)
