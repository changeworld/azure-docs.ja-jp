---
title: Microsoft Sentinel でセキュリティ ハンティングにノートブックを使用する
description: この記事では、Microsoft Sentinel 検出機能でノートブックを使用する方法について説明します。
services: sentinel
author: batamig
ms.author: bagol
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: ace7df5954d288bb46430c84ccde8d2822f08115
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521227"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Jupyter のノートブックを使用してセキュリティの脅威を検出する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft Sentinel の基盤となるのは、ハイ パフォーマンス クエリと動的スキーマを併用して大規模なデータ ボリュームまでスケーリングするデータ ストアです。 Azure portal とすべての Microsoft Sentinel ツールでは、このデータ ストアにアクセスする際に、共通の API が使用されます。

同じ API を [Jupyter](https://jupyter.org/) Notebook や Python などの外部ツールでも使用できます。 ポータルでは多くの一般的なタスクを実行できますが、Jupyter では、データに対して、より幅広い処理が可能です。 高度なプログラミング性と膨大なライブラリ コレクションを組み合わせて、機械学習、視覚化、データ解析を行えます。 こうした特性から、Jupyter は、セキュリティの調査や検出にうってつけのツールとなっています。

たとえば、次の目的にノートブックを使用します。

- Microsoft Sentinel で追加設定なしでは提供されていない、一部の Python 機械学習機能などの **分析を実行する**
- Microsoft Sentinel で追加設定なしでは提供されていない、カスタム タイムラインやプロセス ツリーなどの **データ視覚化を作成する**
- オンプレミスのデータ セットなど、Microsoft Sentinel の外部にある **データ ソースを統合する**

Jupyter のエクスペリエンスが Azure portal に統合されているため、簡単にノートブックを作成して実行し、データを解析できます。 *Kqlmagic* ライブラリは、Microsoft Sentinel から [KQL](https://kusto.azurewebsites.net/docs/kusto/query/index.html) クエリを取得して、ノートブック内で直接実行できるようにする接着剤の働きをします。

Microsoft Sentinel には、Microsoft のセキュリティ アナリストが開発した複数のノートブックがパッケージされています。

- これらのノートブックの中には、特定のシナリオ向けに作成され、そのまま使用できるようになっているものがあります。
- 一方、技術や機能を説明するサンプルとして作成されたものもあります。これをコピーまたは改造して、独自のノートブック内で使用することができます。

[Microsoft Sentinel GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel-Notebooks/)からインポートできるノートブックもあります。

## <a name="notebook-components"></a>ノートブックのコンポーネント

ノートブックは、次の 2 つのコンポーネントから構成されています。

- **ブラウザーベースのインターフェイス**。クエリやコードを入力して実行すると、実行結果が表示されます。
- **"*カーネル*"** 。コード自体を解析して実行する役割を果たします。

Microsoft Sentinel ノートブックのカーネルは、Azure 仮想マシン (VM) で実行されます。 お使いのノートブックに複雑な機械学習モデルが含まれており、より強力な仮想マシンを使用する場合は、いくつかそのためのライセンス オプションがあります。

Microsoft Sentinel ノートブックでは、*pandas*、*matplotlib*、*bokeh* など、多くの一般的な Python ライブラリが使用されています。 次の分野に対応したその他の多数の Python パッケージから選択することもできます。

- 視覚化とグラフィックス
- データ処理と解析
- 統計と数値計算
- 機械学習とディープ ラーニング

ノートブックのセルに複雑なコードを繰り返し入力しなければならない状況を避けるために、ほとんどの Python ノートブックは "*パッケージ*" と呼ばれるサードパーティのライブラリに依存します。 ノートブックでパッケージを使用するには、パッケージをインストールしてインポートする必要があります。 Azure ML コンピューティングには、一般的なパッケージのほとんどがプレインストールされています。 パッケージ、またはパッケージの関連部分 (モジュール、ファイル、関数、クラスなど) をインポートしていることを確認します。

Microsoft Sentinel のノートブックでは、[MSTICPy](https://github.com/Microsoft/msticpy/) という名前の Python パッケージを使用します。これは、データの取得、分析、強化、視覚化のためのサイバーセキュリティ ツールのコレクションです。 

MSTICPy ツールは、特に検出や調査用のノートブックの作成に役立つように設計されており、新機能の開発や改良が鋭意進められています。 詳細については、次を参照してください。

- [MSTIC Jupyter と Python のセキュリティ ツールに関するドキュメント](https://msticpy.readthedocs.io/)
- [チュートリアル: Microsoft Sentinel での Jupyter Notebook と MSTICPy の概要](notebook-get-started.md)
- [Microsoft Sentinel での Jupyter Notebook と MSTICPy の詳細な構成](notebooks-msticpy-advanced.md)

[Microsoft Sentinel GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel-Notebooks/)は、将来 Microsoft が作成した Microsoft Sentinel ノートブックやコミュニティから投稿された Azure Sentinel ノートブックを保管するための場所です。

## <a name="manage-access-to-microsoft-sentinel-notebooks"></a>Microsoft Sentinel ノートブックへのアクセスを管理する

Microsoft Sentinel で Jupyter のノートブックを使用するには、まず、ユーザーの役割に応じた適切なアクセス許可が必要です。

Microsoft Sentinel のノートブックは JupyterLab や Jupyter クラシックで使用できる一方で、Microsoft Sentinel では、ノートブックは [Azure Machine Learning](../machine-learning/overview-what-is-azure-machine-learning.md) (Azure ML) プラットフォーム上で実行されます。 Microsoft Sentinel でノートブックを実行するには、Microsoft Sentinel ワークスペースと [Azure ML ワークスペース](../machine-learning/concept-workspace.md)の両方に対する適切なアクセス権が必要です。

|権限  |説明  |
|---------|---------|
|**Microsoft Sentinel のアクセス許可**     |   他の Microsoft Sentinel リソースと同様に、[Microsoft Sentinel Notebooks] ブレードでノートブックにアクセスするには、Microsoft Sentinel 閲覧者、Microsoft Sentinel レスポンダー、または Microsoft Sentinel 共同作成者のロールが必要です。 <br><br>詳細については、「[Microsoft Sentinel のアクセス許可](roles.md)」を参照してください。|
|**Azure Machine Learning のアクセス許可**     | Azure Machine Learning ワークスペースは Azure リソースの 1 つです。 他の Azure リソースと同様に、新しい Azure Machine Learning ワークスペースが作成されるときに、既定のロールが提供されます。 ワークスペースにユーザーを追加し、これらの組み込みロールのいずれかに割り当てることができます。 詳細については、[Azure Machine Learning の既定のロール](../machine-learning/how-to-assign-roles.md)に関するページと、「[Azure 組み込みロール](../role-based-access-control/built-in-roles.md)」を参照してください。 <br><br>   **重要**: ロールのアクセス権のスコープは、Azure の複数のレベルで指定できます。 たとえば、ワークスペースへの所有者アクセス権を持つユーザーであっても、そのワークスペースが含まれるリソース グループへの所有者アクセス権を持っていないことがあります。 詳細については、「[Azure RBAC のしくみ](../role-based-access-control/overview.md)」を参照してください。 <br><br>Azure ML ワークスペースの所有者である場合は、ワークスペースのロールを追加および削除し、ロールをユーザーに割り当てることができます。 詳細については、次を参照してください。<br>    - [Azure portal](../role-based-access-control/role-assignments-portal.md)<br>    - [PowerShell](../role-based-access-control/role-assignments-powershell.md)<br>    - [Azure CLI](../role-based-access-control/role-assignments-cli.md)<br>   - [REST API](../role-based-access-control/role-assignments-rest.md)<br>    - [Azure Resource Manager テンプレート](../role-based-access-control/role-assignments-template.md)<br> - [Azure Machine Learning CLI ](../machine-learning/how-to-assign-roles.md#manage-workspace-access)<br><br>組み込みロールが十分ではない場合は、カスタム ロールを作成することもできます。 カスタム ロールには、そのワークスペース内のリソースの読み取り、書き込み、削除、コンピューティングのアクセス許可を与えることができます。 ロールは、特定のワークスペース レベル、特定のリソース グループ レベル、または特定のサブスクリプション レベルで使用できるようにすることができます。 詳細については、「[カスタム ロールの作成](../machine-learning/how-to-assign-roles.md#create-custom-role)」を参照してください。 |
|     |         |

## <a name="create-an-azure-ml-workspace-from-microsoft-sentinel"></a>Microsoft Sentinel から Azure ML ワークスペースを作成する

この手順では、Microsoft Sentinel から Microsoft Sentinel ノートブック用の Azure ML ワークスペースを作成する方法を説明します。

**ワークスペースを作成するには**:

1. Azure portal から **[Microsoft Sentinel]**  >  **[脅威の管理]**  >  **[Notebooks]** に移動し、 **[Create a new AML workspace]\(新しい AML ワークスペースの作成\)** を選択します。

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

1. **[ネットワーク]** タブで、ワークスペースの接続に、パブリック エンドポイントを使用するか、自分で構成するプライベート エンドポイントを使用するかを選択します。 お使いの Microsoft Sentinel ワークスペースにパブリック エンドポイントがある場合は、ネットワーク通信の潜在的な問題を回避するため、パブリック エンドポイントを Azure ML ワークスペースに使用することをお勧めします。 完了したら、 **[確認および作成]** を選択します。

1. **[確認および作成]** タブで、情報を見直して正しいことを確認した後、 **[作成]** を選択してワークスペースのデプロイを開始します。 次に例を示します。

    :::image type="content" source="media/notebooks/machine-learning-create-last-step.png" alt-text="Microsoft Sentinel で Machine Learning ワークスペースを確認して作成します。":::

    クラウドへのワークスペースの作成には数分かかる場合があります。 この間、ワークスペースの **[概要]** ページには現在のデプロイ状態が表示され、デプロイが完了すると更新されます。

1. デプロイが完了したら、Microsoft Sentinel の **[Notebooks]** に戻り、新しい Azure ML ワークスペースからノートブックを起動できます。

    複数のノートブックがある場合は、ノートブックを起動するときに使用する既定の AML ワークスペースを選択してください。 次に例を示します。

    :::image type="content" source="media/notebooks/default-machine-learning.png" alt-text="ノートブックの既定の AML ワークスペースを選択します。":::


## <a name="launch-a-notebook-in-your-azure-ml-workspace"></a>Azure ML ワークスペースでノートブックを起動する

AML ワークスペースを作成した後、Microsoft Sentinel から Azure ML ワークスペースでノートブックの起動を開始します。

> [!NOTE]
> ノートブックは、GitHub の組み込みの静的ノートブック レンダラーなどを使用して、静的なドキュメントとして表示できます。 ただし、ノートブックでコードを実行するには、ノートブックを Jupyter カーネルと呼ばれるバックエンド プロセスにアタッチする必要があります。 カーネルによってコードが実行され、コードで作成されるすべての変数とオブジェクトが保持されます。 ブラウザーがこのデータのビューアーです。
>
> Azure ML では、カーネルは Azure ML コンピューティングと呼ばれる仮想マシン上で実行されます。 コンピューティング インスタンスでは、一度に多数のノートブックの実行をサポートできます。
>

**Microsoft Sentinel からノートブックを起動するには**:

1. Azure portal から **[Microsoft Sentinel]**  >  **[Threat management]\(脅威の管理\)**  >  **[Notebooks]** に移動します。ここで、Microsoft Sentinel によって提供されるノートブックを確認できます。

    > [!TIP]
    > **[ノートブック]** ページの上部にある **[Guides & Feedback]\(ガイドとフィードバック\)** を選択すると、右側のペインにさらに多くのリソースとガイダンスが表示されます。

1. ノートブックを選択すると、その説明、必要なデータの種類、データ ソースが表示されます。

    使用するノートブックが見つかったら、 **[Save notebook]\(ノートブックの保存\)** を選択して、自分のワークスペースに複製します。

    必要に応じて名前を編集します。 ワークスペースにノートブックが既に存在する場合は、既存のノートブックを上書します。または、新しいノートブックを作成します。

    :::image type="content" source="media/notebooks/save-notebook.png" alt-text="ノートブックを保存して、自分のワークスペースに複製します。":::

1. ノートブックを保存すると、 **[Save notebook]\(ノートブックの保存\)** ボタンが **[Launch notebook]\(ノートブックの起動\)** に変わります。 **[Launch notebook]\(ノートブックの起動\)** を選択し、AML ワークスペースでそれを開きます。

    次に例を示します。

    :::image type="content" source="media/notebooks/sentinel-notebooks-on-machine-learning.png" alt-text="AML ワークスペースでノートブックを起動します。":::

1. ページの上部で、ノートブック サーバーに使用する **[Compute]\(コンピューティング\)** インスタンスを選択します。

    コンピューティング インスタンスがない場合は、[新しく作成します](../machine-learning/how-to-create-manage-compute-instance.md?tabs=#use-the-script-in-the-studio)。 コンピューティング インスタンスが停止している場合は、開始してください。 詳細については、[Azure Machine Learning スタジオでのノートブックの実行](../machine-learning/how-to-run-jupyter-notebooks.md)に関する記事を参照してください。

    作成したコンピューティング インスタンスを表示および使用できるのは自分のみです。 ユーザー ファイルは VM とは別に格納され、ワークスペース内のすべてのコンピューティング インスタンス間で共有されます。

    > [!TIP]
    > ノートブックをテストするために新しいコンピューティング インスタンスを作成する場合は、**汎用** カテゴリのコンピューティング インスタンスを作成します。
    >
    > また、カーネルは Azure ML のウィンドウの右上にも表示されます。 必要なカーネルが選択されていない場合は、ドロップダウン リストから別のバージョンを選択します。
    >

1. ノートブック サーバーを作成して起動したら、ノートブックのセルの実行を開始できます。 各セルで、 **[実行]** アイコンを選択してノートブックのコードを実行します。

    詳細については、「[コマンド モードのショートカット](../machine-learning/how-to-run-jupyter-notebooks.md)」を参照してください。

1. ノートブックがハングする場合、または最初からやり直したい場合は、カーネルを再起動して、ノートブックのセルを最初から再実行します。 **[Kernel operations]\(カーネルの操作\)**  >  **[Restart kernel]\(カーネルの再起動\)** を選択します。 次に例を示します。

    :::image type="content" source="media/notebooks/sentinel-notebooks-restart-kernel.png" alt-text="ノートブック カーネルを再起動します。":::

    > [!IMPORTANT]
    > カーネルを再起動すると、すべての変数とその他の状態がワイプされます。 再起動後に、初期化と認証のセルを再実行する必要があります。
    >

## <a name="run-code-in-your-notebook"></a>ノートブックでコードを実行する

ノートブックは次のようになっています。

- **Markdown** セルにはテキスト (HTML を含む) と静的イメージがあります。
- **コード** セルにはコードが含まれます。 コード セルを選択した後、セルの左側にある **[再生]** アイコンを選択するか、**SHIFT + ENTER** キーを押して、セル内のコードを実行します。

> [!IMPORTANT]
> ノートブックのコード セルは必ず順番に実行してください。 セルをスキップすると、エラーが発生する可能性があります。
>

たとえば、ノートブックで次のコード セルを実行します。

```python
# This is your first code cell. This cell contains basic Python code.

# You can run a code cell by selecting it and then selecting
# the Play button to the left of the cell, or by pressing SHIFT+ENTER.
# Code output displays below the code.

print("Congratulations, you just ran this code cell")

y = 2 + 2

print("2 + 2 =", y)

```

上のサンプル コードでは、こちらの出力が生成されます。

```python
Congratulations, you just ran this code cell

2 + 2 = 4
```

ノートブックのコード セル内で設定した変数はセル間で保持されるため、セルをチェーンでつなぐことができます。 たとえば、次のコード セルでは、前のセルの `y` の値が使用されます。

```python
# Note that output from the last line of a cell is automatically
# sent to the output cell, without needing the print() function.

y + 2
```

出力は次のようになります。

```output
6
```

## <a name="download-all-microsoft-sentinel-notebooks"></a>Microsoft Sentinel のすべてのノートブックをダウンロードする

このセクションでは、Microsoft Sentinel ノートブックから、Git を使用して [Microsoft Sentinel GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel-Notebooks/)で使用できるすべてのノートブックを、Azure ML ワークスペースに直接ダウンロードする方法について説明します。

Azure ML ワークスペースに Microsoft Sentinel のノートブックを格納しておくと、簡単に更新された状態に保つことができます。

1. Microsoft Sentinel のノートブックから、次のコードを空のセルに入力し、セルを実行します。

    ```python
    !git clone https://github.com/Azure/Azure-Sentinel-Notebooks.git azure-sentinel-nb
    ```

    GitHub リポジトリのコピーが Azure ML ワークスペースのユーザー フォルダーの **azure-Sentinel-nb** ディレクトリに作成されます。

1. 目的のノートブックをこのフォルダーから作業ディレクトリにコピーします。

1. GitHub で最近加えられた変更が反映されるようノートブックを更新するには、次のように実行します。

    ```python
    !cd azure-sentinel-nb && git pull
    ```

## <a name="troubleshooting"></a>トラブルシューティング

通常、ノートブックではシームレスにカーネルが作成またはアタッチされ、手動で変更を加える必要は一切ありません。 エラーが発生した、またはノートブックが実行されていないと思われる場合は、カーネルのバージョンと状態の確認が必要になることがあります。

ノートブックで問題が発生する場合は、[Azure Machine Learning ノートブックのトラブルシューティング](../machine-learning/how-to-run-jupyter-notebooks.md#troubleshooting)に関する記事を参照してください。

### <a name="force-caching-for-user-accounts-and-credentials-between-notebook-runs"></a>ノートブックの実行間でユーザー アカウントと資格情報のキャッシュを強制する

既定では、ユーザー アカウントと資格情報は、同じセッションであっても、ノートブックの実行間でキャッシュされません。

**セッションの実行中にキャッシュを強制するには、次のようにします**。

1. Azure CLI を使用して認証します。 空のノートブック セルに、次のコードを入力して実行します。

    ```python
    !az login
    ```

    次のような出力が表示されます。

    ```python
    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the 9-digit device code to authenticate.
    ```

1. 出力から 9 文字のトークンを選択してコピーし、`devicelogin` の URL を選択して指定されたページに移動します。 

1. ダイアログにトークンを貼り付け、プロンプトが表示されたらサインインを続行します。

    サインインが正常に完了すると、次の出力が表示されます。

    ```python
    Subscription <subscription ID> 'Sample subscription' can be accessed from tenants <tenant ID>(default) and <tenant ID>. To select a specific tenant when accessing this subscription, use 'az login --tenant TENANT_ID'.

> [!NOTE]
> The following tenants don't contain accessible subscriptions. Use 'az login --allow-no-subscriptions' to have tenant level access.
>
> ```
> <tenant ID> 'foo'
><tenant ID> 'bar'
>[
> {
>    "cloudName": "AzureApp",
>    "homeTenantId": "<tenant ID>",
>    "id": "<ID>",
>    "isDefault": true,
>    "managedByTenants": [
>    ....
>```
>
### Error: *Runtime dependency of PyGObject is missing*

If the *Runtime dependency of PyGObject is missing* error appears when you load a query provider, try troubleshooting using the following steps:

1. Proceed to the cell with the following code and run it:

    ```python
    qry_prov = QueryProvider("AzureSentinel")
    ```

    Python の依存関係 (`pygobject`) がないことを示す、次のような警告メッセージが表示されます。

    ```output
    Runtime dependency of PyGObject is missing.

    Depends on your Linux distribution, you can install it by running code similar to the following:
    sudo apt install python3-gi python3-gi-cairo gir1.2-secret-1

    If necessary, see PyGObject's documentation: https://pygobject.readthedocs.io/en/latest/getting_started.html

    Traceback (most recent call last):
      File "/anaconda/envs/azureml_py36/lib/python3.6/site-packages/msal_extensions/libsecret.py", line 21, in <module>
    import gi  # https://github.com/AzureAD/microsoft-authentication-extensions-for-python/wiki/Encryption-on-Linux
    ModuleNotFoundError: No module named 'gi'
    ```

1. Microsoft Sentinel のノートブックの GitHub リポジトリにある [aml-compute-setup.sh](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/HowTos/aml-compute-setup.sh) スクリプトを使用して、コンピューティング インスタンスのすべてのノートブックと Anaconda 環境に `pygobject` を自動的にインストールします。

> [!TIP]
> また、ノートブックから次のコードを実行して、この警告を修正することもできます。
>
> ```python
> !conda install --yes -c conda-forge pygobject
> ```
>


## <a name="next-steps"></a>次のステップ

ノートブック エクスペリエンスを Azure Synapse のビッグ データの分析と統合します。 詳細については、[ノートブックと Azure Synapse の統合 (パブリック プレビュー)](notebooks-with-synapse.md) に関する記事を参照してください。

[Microsoft Sentinel GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel-Notebooks)では、独自のノートブックを開発するときに役立つツール、図、コード サンプルとなるよう意図されたノートブックが他に共有されています。

既存のノートブックに関するフィードバック、提案、機能の要求、ノートブックの投稿、バグ レポート、改善や追加などがあれば、ぜひお送りください。 問題またはフォークを作成して、投稿をアップロードするには、[Microsoft Sentinel GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel)にアクセスしてください。

- [**Azure Log Analytics での資格情報のスキャン**](https://www.youtube.com/watch?v=OWjXee8o04M)や **ガイド付き調査 - プロセス アラート** など、いくつかのノートブック テンプレートを調べて、脅威のハンティングと調査でのノートブックの使用についてさらに **説明します**。

    Microsoft Sentinel > **[Notebooks]**  >  **[テンプレート]** タブで、ノートブック テンプレートをさらに検索します。

- [Microsoft Sentinel GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel-Notebooks)で、ノートブックを **さらに検索します**。

  - [`Sample-Notebooks`](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) ディレクトリにはデータと共に保存されるサンプル ノートブックが含まれており、目的の出力を表示するために使用できます。

  - [`HowTos`](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) ディレクトリには、既定の Python バージョンの設定や、ノートブックからの Microsoft Sentinel ブックマークの作成などの概念を説明するノートブックが含まれています。

詳細については、次を参照してください。

- [チュートリアル: Microsoft Sentinel での Jupyter Notebook と MSTICPy の概要](notebook-get-started.md)
- [チュートリアル: Microsoft Sentinel ノートブック - はじめに](https://www.youtube.com/results?search_query=azazure+sentinel+notebooks) (ビデオ)
- [チュートリアル: Azure ML スタジオ内から Jupyter ノートブックを編集して実行する](https://www.youtube.com/watch?v=AAj-Fz0uCNk) (ビデオ)
- [ウェビナー: Microsoft Sentinel ノートブックの基礎](https://www.youtube.com/watch?v=rewdNeX6H94)
- [脅威を事前に検出する](hunting.md)
- [ブックマークを使用して検出中に関心のある情報を保存する](bookmarks.md)
- [Jupyter、msticpy、および Microsoft Sentinel](https://msticpy.readthedocs.io/en/latest/getting_started/JupyterAndAzureSentinel.html)
