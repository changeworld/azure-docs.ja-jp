---
title: Azure Sentinel でセキュリティ ハンティングにノートブックを使用する
description: この記事では、Azure Sentinel 検出機能でノートブックを使用する方法について説明します。
services: sentinel
author: batamig
ms.author: bagol
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 06/24/2021
ms.openlocfilehash: 7b1202648db3aed73ea040fb60610879a6816dd9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737567"
---
# <a name="use-jupyter-notebook-to-hunt-for-security-threats"></a>Jupyter Notebook を使用してセキュリティの脅威を検出する

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel の基盤となるのは、ハイ パフォーマンス クエリと動的スキーマを併用して大規模なデータ ボリュームまでスケーリングするデータ ストアです。 Azure portal とすべての Azure Sentinel ツールでは、このデータ ストアにアクセスする際に、共通の API が使用されます。 同じ API を [Jupyter](https://jupyter.org/) Notebook や Python などの外部ツールでも使用できます。 ポータルでは多くの一般的なタスクを実行できますが、Jupyter では、データに対して、より幅広い処理が可能です。 高度なプログラミング性と膨大なライブラリ コレクションを組み合わせて、機械学習、視覚化、データ解析を行えます。 こうした特性から、Jupyter は、セキュリティの調査や検出にうってつけのツールとなっています。

たとえば、次の目的にノートブックを使用します。

- 一部の Python 機械学習機能など、Azure Sentinel に組み込まれていない分析を実行します
- カスタム タイムラインやプロセス ツリーなど、Azure Sentinel に組み込まれていないデータ視覚化を作成します
- オンプレミスのデータ セットなど、Azure Sentinel の外部にあるデータ ソースを統合します。

Jupyter のエクスペリエンスが Azure portal に統合されているため、簡単にノートブックを作成して実行し、データを解析できます。 *Kqlmagic* ライブラリは、Azure Sentinel からクエリを取得して、ノートブック内で直接実行できるようにする接着剤の働きをします。 クエリでは、[Kusto クエリ言語](https://kusto.azurewebsites.net/docs/kusto/query/index.html)が使用されます。 Azure Sentinel には、Microsoft のセキュリティ アナリストが開発した複数のノートブックがパッケージされています。 これらのノートブックの中には、特定のシナリオ向けに作成され、そのまま使用できるようになっているものがあります。 一方、技術や機能を説明するサンプルとして作成されたものもあります。これをコピーまたは改造して、独自のノートブック内で使用することができます。 Azure Sentinel コミュニティの GitHub からインポートできるノートブックもあります。

統合された Jupyter エクスペリエンスでは、[Azure Notebooks](https://notebooks.azure.com/) を使用して、ノートブックが保存、共有、および実行されます。 これらのノートブックは、ローカル環境で実行することも (コンピューター上に Python 環境と Jupyter がある場合)、他の JupyterHub 環境 (Azure Databricks など) 内で実行することもできます。


## <a name="notebook-components"></a>ノートブックのコンポーネント

ノートブックは、次の 2 つのコンポーネントから構成されています。
- **ブラウザーベースのインターフェイス**。クエリやコードを入力して実行すると、実行結果が表示されます。
- **"*カーネル*"** 。コード自体を解析して実行する役割を果たします。

Azure Sentinel ノートブックのカーネルは、Azure 仮想マシン (VM) で実行されます。 ノートブックに複雑な機械学習モデルが含まれている場合は、より強力な仮想マシンを活用するためのライセンス オプションがいくつか存在します。

Azure Sentinel ノートブックでは、*pandas*、*matplotlib*、*bokeh* など、多くの一般的な Python ライブラリが使用されています。 次の分野に対応したその他の多数の Python パッケージから選択することもできます。

- 視覚化とグラフィックス
- データ処理と解析
- 統計と数値計算
- 機械学習とディープ ラーニング

オープンソースの Jupyter セキュリティ ツールも [msticpy](https://github.com/Microsoft/msticpy/) という名前のパッケージでリリースされています。 このパッケージは、組み込みのノートブックの多くで使用されています。 Msticpy ツールは、特に検出や調査用のノートブックの作成に役立つように設計されており、新機能の開発や改良が鋭意進められています。 詳細については、[MSTIC Jupyter および Python セキュリティ ツールのドキュメント](https://msticpy.readthedocs.io/)を参照してください。

[Azure Sentinel コミュニティの GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel)は、将来 Microsoft が作成した Azure Sentinel ノートブックやコミュニティから投稿された Azure Sentinel ノートブックを保管するための場所です。


## <a name="manage-access-to-azure-sentinel-notebooks"></a>Azure Sentinel ノートブックへのアクセスを管理する

ノートブックを使用するには、まず、ユーザーの役割に応じた適切なアクセス許可が必要です。

Azure Sentinel ノートブックは [Azure Machine Learning](../machine-learning/overview-what-is-azure-machine-learning.md) (Azure ML) プラットフォーム上で実行されるので、Azure Sentinel ワークスペースと [Azure ML ワークスペース](../machine-learning/concept-workspace.md)の両方に対する適切なアクセス権が必要です。

|権限  |説明  |
|---------|---------|
|**Azure Sentinel のアクセス許可**     |   他の Azure Sentinel リソースと同様に、[Azure Sentinel Notebooks] ブレードでノートブックにアクセスするには、Azure Sentinel 閲覧者、Azure Sentinel レスポンダー、または Azure Sentinel 共同作成者のロールが必要です。 <br><br>詳細については、「[Azure Sentinel のアクセス許可](roles.md)」を参照してください。|
|**Azure Machine Learning のアクセス許可**     | Azure Machine Learning ワークスペースは Azure リソースの 1 つです。 他の Azure リソースと同様に、新しい Azure Machine Learning ワークスペースが作成されるときに、既定のロールが提供されます。 ワークスペースにユーザーを追加し、これらの組み込みロールのいずれかに割り当てることができます。 詳細については、[Azure Machine Learning の既定のロール](../machine-learning/how-to-assign-roles.md)に関するページと、「[Azure 組み込みロール](../role-based-access-control/built-in-roles.md)」を参照してください。 <br><br>   **重要**: ロールのアクセス権のスコープは、Azure の複数のレベルで指定できます。 たとえば、ワークスペースへの所有者アクセス権を持つユーザーであっても、そのワークスペースが含まれるリソース グループへの所有者アクセス権を持っていないことがあります。 詳細については、「[Azure RBAC のしくみ](../role-based-access-control/overview.md)」を参照してください。 <br><br>Azure ML ワークスペースの所有者である場合は、ワークスペースのロールを追加および削除し、ロールをユーザーに割り当てることができます。 詳細については、次を参照してください。<br>    - [Azure portal](../role-based-access-control/role-assignments-portal.md)<br>    - [PowerShell](../role-based-access-control/role-assignments-powershell.md)<br>    - [Azure CLI](../role-based-access-control/role-assignments-cli.md)<br>   - [REST API](../role-based-access-control/role-assignments-rest.md)<br>    - [Azure Resource Manager テンプレート](../role-based-access-control/role-assignments-template.md)<br> - [Azure Machine Learning CLI ](../machine-learning/how-to-assign-roles.md#manage-workspace-access)<br><br>組み込みロールが十分ではない場合は、カスタム ロールを作成することもできます。 カスタム ロールには、そのワークスペース内のリソースの読み取り、書き込み、削除、コンピューティングのアクセス許可を与えることができます。 ロールは、特定のワークスペース レベル、特定のリソース グループ レベル、または特定のサブスクリプション レベルで使用できるようにすることができます。 詳細については、「[カスタム ロールの作成](../machine-learning/how-to-assign-roles.md#create-custom-role)」を参照してください。 |
|     |         |


## <a name="create-an-azure-ml-workspace-from-azure-sentinel"></a>Azure Sentinel から Azure ML ワークスペースを作成する

この手順では、Azure Sentinel から Azure Sentinel ノートブック用の Azure ML ワークスペースを作成する方法を説明します。

**ワークスペースを作成するには**:

1. Azure portal から **[Azure Sentinel]**  >  **[脅威の管理]**  >  **[ノートブック]** に移動し、 **[Create a new AML workspace]\(新しい AML ワークスペースの作成\)** を選択します。

1. 次の詳細を入力してから、 **[次へ]** を選択します。

    |フィールド|説明|
    |--|--|
    |**サブスクリプション**|使用する Azure サブスクリプションを選択します。|
    |**リソース グループ**|サブスクリプションの既存のリソース グループを使用するか、任意の名前を入力して新しいリソース グループを作成します。 リソース グループは、Azure ソリューションの関連するリソースを保持します。|
    |**ワークスペース名**|ワークスペースを識別する一意の名前を入力します。 名前は、リソース グループ全体で一意である必要があります。 覚えやすく、他のユーザーが作成したワークスペースと区別しやすい名前を使用します。|
    |**リージョン**|ユーザーとデータ リソースに最も近い場所を選択し、ワークスペースを作成します。|
    |**[ストレージ アカウント]**| ストレージ アカウントは、ワークスペース用の既定のデータストアとして使用されます。 新しい Azure Storage リソースを作成しても、サブスクリプションで既存のものを選択してもかまいません。|
    |**KeyVault**| キー コンテナーは、ワークスペースで必要なシークレットや他の機密情報を格納するために使用されます。 新しい Azure Key Vault リソースを作成しても、サブスクリプションで既存のものを選択してもかまいません。|
    |**Application Insights**| ワークスペースでは、デプロイされているモデルに関する監視情報を格納するために、Azure Application Insights が使用されます。 新しい Azure Application Insights リソースを作成しても、サブスクリプションで既存のものを選択してもかまいません。|
    |**コンテナー レジストリ**| コンテナー レジストリは、トレーニングとデプロイで使用される Docker イメージを登録するために使用されます。 コストを最小限に抑えるには、最初のイメージをビルドした後でのみ、新しい Azure Container Registry リソースを作成します。 または、今すぐリソースを作成するか、サブスクリプションで既存のものを選択するか、コンテナー レジストリを使用しない場合は **[なし]** を選択してもかまいません。|
    | | |

1. **[ネットワーク]** タブで、ワークスペースの接続に、パブリック エンドポイントを使用するか、自分で構成するプライベート エンドポイントを使用するかを選択します。 お使いの Azure Sentinel ワークスペースにパブリック エンドポイントがある場合は、ネットワーク通信の潜在的な問題を回避するため、パブリック エンドポイントを Azure ML ワークスペースに使用することをお勧めします。 完了したら、 **[確認および作成]** を選択します。

1. **[確認および作成]** タブで、情報を見直して正しいことを確認した後、 **[作成]** を選択してワークスペースのデプロイを開始します。 次に例を示します。

    :::image type="content" source="media/notebooks/machine-learning-create-last-step.png" alt-text="Azure Sentinel で Machine Learning ワークスペースを確認して作成します。":::

    クラウドへのワークスペースの作成には数分かかる場合があります。 この間、ワークスペースの **[概要]** ページには現在のデプロイ状態が表示され、デプロイが完了すると更新されます。

1. デプロイが完了したら、Azure Sentinel の **[ノートブック]** に戻り、新しい Azure ML ワークスペースからノートブックを起動できます。

    複数のノートブックがある場合は、ノートブックを起動するときに使用する既定の AML ワークスペースを選択してください。 次に例を示します。

    :::image type="content" source="media/notebooks/default-machine-learning.png" alt-text="ノートブックの既定の AML ワークスペースを選択します。":::


## <a name="launch-a-notebook-in-your-azure-ml-workspace"></a>Azure ML ワークスペースでノートブックを起動する

AML ワークスペースを作成した後、Azure Sentinel から Azure ML ワークスペースでノートブックの起動を開始します。

**ノートブックを起動するには**:

1. Azure portal から **[Azure Sentinel]**  >  **[Threat management]\(脅威の管理\)**  >  **[Notebooks]\(ノートブック\)** に移動します。ここで、Azure Sentinel によって提供されるノートブックを確認できます。

    > [!TIP]
    > **[ノートブック]** ページの上部にある **[Guides & Feedback]\(ガイドとフィードバック\)** を選択すると、右側のペインにさらに多くのリソースとガイダンスが表示されます。

1. ノートブックを選択すると、その説明、必要なデータの種類、データ ソースが表示されます。

    使用するノートブックが見つかったら、 **[Save notebook]\(ノートブックの保存\)** を選択して、自分のワークスペースに複製します。

    必要に応じて名前を編集します。 ワークスペースにノートブックが既に存在する場合は、既存のノートブックを上書きするか、新しいノートブックを作成するかを選択できます。

    :::image type="content" source="media/notebooks/save-notebook.png" alt-text="ノートブックを保存して、自分のワークスペースに複製します。":::

1. ノートブックを保存すると、 **[Save notebook]\(ノートブックの保存\)** ボタンが **[Launch notebook]\(ノートブックの起動\)** に変わります。 **[Launch notebook]\(ノートブックの起動\)** を選択し、AML ワークスペースでそれを開きます。

    次に例を示します。

    :::image type="content" source="media/notebooks/sentinel-notebooks-on-machine-learning.png" alt-text="AML ワークスペースでノートブックを起動します。":::

1. ページの上部で、ノートブック サーバーに使用する **[Compute]\(コンピューティング\)** インスタンスを選択します。

    コンピューティング インスタンスがない場合は、[新しく作成します](../machine-learning/how-to-create-manage-compute-instance.md?tabs=#use-the-script-in-the-studio)。 コンピューティング インスタンスが停止している場合は、開始してください。 詳細については、[Azure Machine Learning スタジオでのノートブックの実行](../machine-learning/how-to-run-jupyter-notebooks.md)に関する記事を参照してください。

    作成したコンピューティング インスタンスを表示および使用できるのは自分のみです。 ユーザー ファイルは VM とは別に格納され、ワークスペース内のすべてのコンピューティング インスタンス間で共有されます。

    > [!TIP]
    > ノートブックをテストするために新しいコンピューティング インスタンスを作成する場合は、**汎用** カテゴリのコンピューティング インスタンスを作成します。

1. ノートブック サーバーを作成して起動したら、ノートブックのセルの実行を開始できます。 各セルで、 **[実行]** アイコンを選択してノートブックのコードを実行します。

    詳細については、「[コマンド モードのショートカット](../machine-learning/how-to-run-jupyter-notebooks.md)」を参照してください。

1. ノートブック カーネルを再起動して、ノートブックのセルを最初から再実行する必要がある場合は、 **[Kernel operations]\(カーネルの操作\)**  >  **[Restart kernel]\(カーネルの再起動\)** を選択します。 次に例を示します。

    :::image type="content" source="media/notebooks/sentinel-notebooks-restart-kernel.png" alt-text="ノートブック カーネルを再起動します。":::

## <a name="troubleshooting"></a>トラブルシューティング

ノートブックで問題が発生する場合は、[Azure Machine Learning ノートブックのトラブルシューティング](../machine-learning/how-to-run-jupyter-notebooks.md#troubleshooting)に関する記事を参照してください。


## <a name="next-steps"></a>次のステップ

- [**Azure Log Analytics での資格情報のスキャン**](https://www.youtube.com/watch?v=OWjXee8o04M)や **ガイド付き調査 - プロセス アラート** など、いくつかのノートブック テンプレートを調べて、脅威のハンティングと調査でのノートブックの使用についてさらに学習します。 

    Azure Sentinel > **[ノートブック]**  >  **[テンプレート]** タブで、ノートブック テンプレートを探します。

- [Azure Sentinel の GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel-Notebooks)でさらに検索します。

    - [**Sample-Notebooks**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) ディレクトリにはデータと共に保存されるサンプル ノートブックが含まれており、目的の出力を表示するために使用できます。

    - [**HowTos**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) ディレクトリには、既定の Python バージョンの設定や、ノートブックからの Azure Sentinel ブックマークの作成などの概念を説明するノートブックが含まれています。

    詳細については、[Azure ML ノートブックと Azure Sentinel の使用開始](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb)に関するガイドを参照してください。

> [!NOTE]
> [Azure Sentinel GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel-Notebooks)で共有されているノートブックは、独自のノートブックを開発するときに使用できる便利なツール、図、コード サンプルとして用意されています。
>
> 既存のノートブックに関するフィードバック、提案、機能の要求、ノートブックの投稿、バグ レポート、改善や追加などがあれば、ぜひお送りください。 問題またはフォークを作成して、投稿をアップロードするには、[Azure Sentinel コミュニティの GitHub](https://github.com/Azure/Azure-Sentinel) にアクセスしてください。
>

詳細については、次を参照してください。

- [ウェビナー: Azure Sentinel ノートブックの基礎](https://www.youtube.com/watch?v=rewdNeX6H94)
- [チュートリアル: Azure Sentinel ノートブック - はじめに](https://www.youtube.com/results?search_query=azazure+sentinel+notebooks)
- [脅威を事前に検出する](hunting.md)
- [ブックマークを使用して検出中に関心のある情報を保存する](bookmarks.md)
- [Jupyter、msticpy および Azure Sentinel](https://msticpy.readthedocs.io/en/latest/getting_started/JupyterAndAzureSentinel.html)
- [チュートリアル: Azure ML スタジオ内から Jupyter ノートブックを編集して実行する](https://www.youtube.com/watch?v=AAj-Fz0uCNk)
