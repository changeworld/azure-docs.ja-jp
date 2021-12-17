---
title: Microsoft Sentinel ノートブックと Azure Synapse の統合を使用した大規模なセキュリティ分析
description: この記事では、Azure Synapse Analytics で Microsoft Sentinel ノートブックを使用してビッグ データのクエリを実行する方法について説明します。
services: sentinel
author: batamig
ms.author: bagol
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 0d298bfc2414c21efa8db869fae7bd9ab5f66632
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712626"
---
# <a name="large-scale-security-analytics-using-microsoft-sentinel-notebooks-and-azure-synapse-integration-public-preview"></a>Microsoft Sentinel ノートブックと Azure Synapse の統合を使用した大規模なセキュリティ分析 (パブリック プレビュー)

> [!IMPORTANT]
> Microsoft Sentinel ノートブックと Azure Synapse Analytics の統合は、現在プレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

Microsoft Sentinel ノートブックと Azure Synapse Analytics を統合すると、大規模なセキュリティ分析が可能になります。

Microsoft Sentinel でデータのクエリと分析を行うための主要なツールおよびソリューションとして KQL と Log Analytics がありますが、Azure Synapse には、組み込みのデータ レイク アクセスと Apache Spark 分散処理エンジンによるビッグ データ分析用の特別な機能が用意されています。

Azure Synapse との統合により、次が提供されます。

- コスト最適化されたフルマネージド Azure Synapse Apache Spark コンピューティング プールを使用した **セキュリティ ビッグ データ分析**。

- **コスト効率の高いデータ レイク アクセス**。Azure Data Lake Storage Gen2 を介して履歴データに対する分析を構築します。これは、Azure Blob Storage の上に構築されたビッグ データ分析専用の一連の機能です。

- **データ ソースを統合する柔軟性**。複数のソースと形式からセキュリティ操作ワークフローに統合します。

- Python と組み合わせて Spark フレームワークを使用するための **Python ベースの API である PySpark**。Python に既に慣れていれば、新しいプログラミング言語を学習する必要性が少なくなります。

たとえば、Azure Synapse でノートブックを使用して、ネットワーク ファイアウォール ログから異常な動作を見つけて、潜在的なネットワーク ビーコンを検出したり、Log Analytics ワークスペースから収集されたデータに基づいて、機械学習モデルをトレーニングして構築したりする必要がある場合があります。

## <a name="prerequisites"></a>前提条件

### <a name="understand-microsoft-sentinel-notebooks"></a>Microsoft Sentinel ノートブックを理解する

この記事の手順を行う前に、Microsoft Sentinel ノートブック全般について学習することをお勧めします。

始めに、「[Jupyter Notebook を使用してセキュリティの脅威を検出する](notebooks.md)」および「[チュートリアル: Microsoft Sentinel での Jupyter Notebook と MSTICPy の概要](notebook-get-started.md)」を参照してください。

### <a name="required-roles-and-permissions"></a>必要なロールとアクセス許可

Microsoft Sentinel ノートブックで Azure Synapse を使用するには、次のロールとアクセス許可が必要です。

|Type  |詳細  |
|---------|---------|
|**Microsoft Sentinel**     |- Microsoft Sentinel からノートブックを保存して起動するための **Microsoft Sentinel 共同作成者** ロール         |
|**Azure Machine Learning**     |- 必要に応じて、新しい Azure Machine Learning ワークスペースを作成するための、リソース グループ レベルの **所有者** または **共同作成者** ロール。 <br>- Microsoft Sentinel ノートブックを実行する Azure Machine Learning ワークスペースへの **共同作成者** ロール。    <br><br>詳細については、「[Azure Machine Learning ワークスペースへのアクセスの管理](../machine-learning/how-to-assign-roles.md)」を参照してください。     |
|**Azure Synapse Analytics**     | - 新しい Azure Synapse ワークスペースを作成するための、リソース グループレベルの **所有者** ロール。<br>- クエリを実行するための Azure Synapse ワークスペースへの **共同作成者** ロール。 <br>- Synapse Studio への Azure Synapse Analytics **共同作成者** ロール   <br><br>詳細については、「[Synapse で一般的なタスクを実行するために必要なロールについて理解する](../synapse-analytics/security/synapse-workspace-understand-what-role-you-need.md)」を参照してください。     |
|**Azure Data Lake Storage Gen2**     | - Log Analytics ワークスペースからデータをエクスポートするための、Azure Log Analytics **共同作成者** ロール<br>- データ レイクからのデータのクエリを実行すための、Azure Blob Storage 共同作成者ロール  <br><br>詳細については、[Azure ロールの割り当て](../storage/blobs/assign-azure-role-data-access.md?tabs=portal)に関するページを参照してください。|
|     |         |

### <a name="connect-to-azure-ml-and-synapse-workspaces"></a>Azure ML および Synapse ワークスペースに接続する

Azure Synapse で Microsoft Sentinel ノートブックを使用するには、最初に Azure Machine Learning ワークスペースと Azure Synapse ワークスペースの両方に接続する必要があります。

**Azure Machine Learning ワークスペースを作成または接続するには**:

Azure Machine Learning ワークスペースは、Microsoft Sentinel でノートブックを使用するための基本要件です。

まだ接続していない場合は、「[Jupyter のノートブックを使用してセキュリティの脅威を検出する](notebooks.md)」を参照してください。

**新しい Azure Synapse ワークスペースを作成するには**:

Microsoft Sentinel の **[ノートブック]** ページの上部で、 **[Azure Synapse の構成]** を選択し、次に **[新しい Azure Synapse ワークスペースの作成]** を選択します。

> [!NOTE]
> Azure Data Lake Storage Gen2 は、すべての Azure Synapse ワークスペースが付属する組み込みのデータ レイクです。 Microsoft Sentinel ワークスペースと同じリージョンにある新しいデータ レイクを選択するか、作成してください。 これは、この記事で後述するように、データをエクスポートするときに必要です。
>

詳細については、[Azure Synapse のドキュメント](../synapse-analytics/quickstart-create-workspace.md)を参照してください。


## <a name="configure-your-azure-synapse-analytics-integration"></a>Azure Synapse Analytics 統合を構成する

Microsoft Sentinel には、組み込みの「**Azure Synapse - Azure ML と Azure Synapse Analytics の構成**」ノートブックが用意されており、Azure Synapse と統合するために必要な構成がガイドされます。

> [!NOTE]
> Azure Synapse 統合の構成は、1 回だけの手順であり、このノートブックは Microsoft Sentinel ワークスペースに対して 1 回実行する必要があるだけです。
>

**Azure Synapse - Azure ML と Azure Synapse Analytics ノートブックの構成を実行するには**:

1. Microsoft Sentinel **[ノートブック]** ページで、 **[テンプレート]** タブを選択し、検索バーに「**Synapse**」と入力してノートブックを検索します。

1. **[Azure Synapse - Azure ML と Azure Synapse Analytics の構成]** を見つけて選択し、右下にある **[ノートブック テンプレートの複製]** を選択します。

    **[ノートブックの複製]** ウィンドウで、必要に応じてノートブック名を変更し、[Azure Machine Learning ワークスペース](#connect-to-azure-ml-and-synapse-workspaces)を選択して、 **[保存]** を選択します。

1. ノートブックがデプロイされたら、 **[ノートブックの起動]** を選択し、それを開きます。

    Microsoft Sentinel 内で、ノートブックが Azure ML ワークスペースに開きます。 詳細については、「[Azure ML ワークスペースでノートブックを起動する](notebooks.md#launch-a-notebook-in-your-azure-ml-workspace)」を参照してください。

1. ノートブックの最初の手順のセルを実行して、必要な Python ライブラリと関数を読み込み、Azure リソースに対して認証します。

1. 手順 4「**Azure Synapse Spark プールを構成する**」のセルを実行して、ビッグ データ クエリの実行時に使用する新しい [Azure Synapse Apache Spark プール](../synapse-analytics/spark/apache-spark-pool-configurations.md)を作成します。

1. 手順 5「**Azure ML ワークスペースとリンクされたサービスを構成する**」のセルを実行して、Azure ML ワークスペースが Azure Synapse ワークスペースと通信できるようします。 詳細については、「[Azure Synapse Analytics と Azure Machine Learning ワークスペースをリンクして Apache Spark プールをアタッチする](../machine-learning/how-to-link-synapse-ml-workspaces.md)」を参照してください。

1. 手順 6「**Azure Log Analytics から Azure Data Lake Storage Gen2 にデータをエクスポートする**」のセルを実行して、Azure Log Analytics から Azure Data Lake Storage へ、クエリに使用するデータをエクスポートします。

データが Azure Data Lake Storage に格納されたら、Azure Synapse を使用してビッグ データ クエリの実行を開始する準備が整います。 詳細については、[Azure Monitor での Log Analytics データ エクスポート](../azure-monitor/logs/logs-data-export.md?tabs=portal)に関するページを参照してください。

## <a name="hunt-on-historical-data-at-scale"></a>大規模な履歴データの検索

Microsoft Sentinel には、組み込みの「**Azure Synapse - Apache Spark を使用して潜在的なネットワーク ビーコンを検出する**」ノートブックが用意されています。 このノートブックを実際のサンプル セキュリティ シナリオのテンプレートとして使用して、Microsoft Sentinel と Azure Synapse によるビッグ データのハンティングを開始します。

**Microsoft Sentinel と Azure Synapse を使用して潜在的なネットワーク ビーコンを検出するには**:

1. Microsoft Sentinel **[ノートブック]** ページで、 **[テンプレート]** タブを選択し、検索バーに「**Synapse**」と入力してノートブックを検索します。

1. **[Azure Synapse - Apache Spark を使用して潜在的なネットワーク ビーコンを検出する]** を見つけて選択し、右下にある **[ノートブック テンプレートの複製]** を選択します。

    **[ノートブックの複製]** ウィンドウで、必要に応じてノートブック名を変更し、[Azure Machine Learning ワークスペース](#connect-to-azure-ml-and-synapse-workspaces)を選択して、 **[保存]** を選択します。

1. ノートブックがデプロイされたら、 **[ノートブックの起動]** を選択し、それを開きます。

    Microsoft Sentinel 内から、ノートブックがご利用の Azure ML ワークスペースに開きます。 詳細については、「[Azure ML ワークスペースでノートブックを起動する](notebooks.md#launch-a-notebook-in-your-azure-ml-workspace)」を参照してください。

1. ノートブックの最初の手順のセルを実行して、必要な Python ライブラリと関数を読み込み、Azure リソースに対して認証します。

1. **[Spark セッションの開始]** というラベルの付いたセルに到達したら、セルを実行して Azure Synapse セッションの使用を開始し、Azure ML コンピューティングを使用する代わりに、データ準備タスクとデータ ラングリング タスクのコンピューティングとして Apache Spark プールを使用します。

1. 後続のセルを実行して、現在 Azure Data Lake Storage に格納されているデータに対してクエリを構成して実行します。 たとえば、特定の時間範囲のデータを含めるように、[ルックバック期間を更新](#define-your-data-lookback-period)します。

1. クエリが完了したら、Azure Data Lake Storage から結果をエクスポートし、Log Analytics ワークスペースに戻します。

    「**ADLS から結果をエクスポートする**」手順に示すように、次のコードで、クエリ結果を 1 つの JSON ファイルとして保存します。 ディレクトリ名を定義し、セルを実行します。

    ```python
    %%synapse
    dir_name = "<dir-name>"  # specify desired directory name
    new_path = adls_path + dir_name
    csl_beacon_pd = csl_beacon_df.coalesce(1).write.format("json").save(new_path)
    ```

1. データをエクスポートした後、Spark セッションを停止できます。 Spark セッションを停止した後、後続のクエリは、ページの上部にある **[コンピューティング]** フィールドに示されている既定の Azure ML コンピューティングを使用して実行されます。

    **[Spark セッションの停止]** 手順のセルを実行します。

    ```python
    %synapse stop
    ```

1. Azure Data Lake Storage のクエリ結果を含む JSON ファイルをローカル ファイル システムにエクスポートします。

    「**ADLS からローカル ファイルシステムに結果をエクスポートする**」、「**ADLS からファイルをダウンロードする**」、および「**結果を表示する**」手順のコードを使用して、JSON ファイルをローカルに保存して表示します。

1. 結果をローカルに保存した後は、追加のデータで結果を強化し、視覚化を実行できます。 たとえば、「**Azure Synapse - Apache Spark を使用して潜在的なネットワーク ビーコンを検出する**」ノートブックでは、次のアクションを実行するための追加の手順が提供されます。

    - IP アドレス GeoLocation、WhoIs、その他の脅威インテリジェンス データによって結果を補強し、異常なネットワーク動作のより完全な全体像を取得します。
    - リモート ネットワーク接続や他のイベントの分布を確認しながら、MSTICPy の視覚化を実行して場所をマップします。

    詳細な調査のために、結果を Microsoft Sentinel に書き戻すことができます。 たとえば、結果からカスタム インシデント、ウォッチリスト、またはハンティング ブックマークを作成できます。

    > [!TIP]
    > これらの手順は、潜在的なネットワーク ビーコンを検出したり、それらをテンプレートとして使用して、組織のニーズに合わせてそれらを変更したりするときに使用します。
    >

## <a name="manage-your-azure-synapse-session-from-microsoft-sentinel"></a>Microsoft Sentinel から Azure Synapse セッションを管理する

Azure Synapse セッション内でない場合、Microsoft Sentinel は既定で、 **[ノートブック]** ページの上部にある **[コンピューティング]** フィールドで選択されている Azure ML コンピューティングに設定されています。

次のコードを使用します。これは、ここまたは、「**Azure Synapse - Apache Spark を使用して潜在的なネットワーク ビーコンを検出する**」ノートブックからコピーして、Azure Synapse セッションを開始および停止できます。

### <a name="start-an-azure-synapse-session-from-within-microsoft-sentinel"></a>Azure Synapse セッション内から Microsoft Sentinel セッションを開始する

次のコードを実行します。

```python
%synapse start -w $amlworkspace -s $subscription_id -r $resource_group -c $synapse_spark_compute
```

以降のすべてのコード セルを `%%synapse` で開始し、開始した Synapse セッションを使用します。

たとえば、次のように入力します。

```python
%%synapse

# Primary storage info
account_name = '<storage account name>' # fill in your primary account name
container_name = '<container name>' # fill in your container name
subscription_id = '<subscription if>' # fill in your subscription id
resource_group = '<resource group>' # fill in your resource groups for ADLS
workspace_name = '<Microsoft Sentinel/log analytics workspace name>' # fill in your workspace name
device_vendor = "Fortinet"  # Replace your desired network vendor from commonsecuritylogs

# Datetime and lookback parameters
end_date = "<enter date in the format yyyy-MM-dd e.g.2021-09-17>"  # fill in your input date
lookback_days = 21 # fill in lookback days if you want to run it on historical data. make sure you have historical data available in ADLS
```

### <a name="define-your-data-lookback-period"></a>データ のルックバック期間を定義する

このサンプル ノートブックのビッグ データ クエリは、`end-date` パラメーターまたはより長い時間範囲を使用して、定義済みの日付からデータに対して実行できます。

たとえば、次のように入力します。

- 特定の日付のデータに関心がある場合は、現在の日付として 2021 年 11 月 15 日を指定すると、クエリが 2021 年 11 月 15 日のデータに対してのみ実行されます。 

- クエリにより長い時間スコープを定義するには、現在の日付に加えて、ルックバック パラメーターを定義します。 たとえば、`lookback_days` パラメーターが `21` 日に設定され、`end_date` パラメーターが `2021-11-17` に設定されている場合、クエリでは、2021 年 11 月 17 日からさかのぼって 21 日間のデータが確認されます。

「**Azure Synapse - Apache Spark を使用して潜在的なネットワーク ビーコンを検出する**」ノートブックで、このコードは **データ準備手順** で見つかります。

たとえば、次のように入力します。

```python
# Datetime and lookback parameters
end_date = "2021-11-17>"  # fill in your input date
lookback_days = "21" # fill in lookback days if you want to run it on historical data. Make sure you have historical data available in ADLS
```

上記の例では、クエリが 2021 年 10 月 28 日から 11 月 17 日までのデータに対して実行されます。

### <a name="stop-an-azure-synapse-session-from-within-microsoft-sentinel"></a>Microsoft Sentinel 内から Azure Synapse セッションを停止する

次のコードを実行します。

```python
%synapse stop
```

### <a name="switch-azure-synapse-workspaces-in-microsoft-sentinel"></a>Microsoft Sentinel で Azure Synapse ワークスペースを切り替える

現在サインインしているものと異なる Synapse ワークスペースを管理または選択するには、次のいずれかの方法を使用します。

- **Azure ML と新しい Azure Synapse ワークスペースの間にリンクされたサービスを既に作成している場合**:

    1. 次のコード セルに `linkservice` パラメーターの名前を入力し、セルと後続のセルを再実行します。

        ```python
        amlworkspace = "<aml workspace name>"  # fill in your AML workspace name
        subscription_id = "<subscription id>" # fill in your subscription id
        resource_group = '<resource group of AML workspace>' # fill in your resource groups for AML workspace
        linkedservice = '<linked service name>' # fill in your linked service created to connect to synapse workspace
        ```

    1. リンクされたサービスに登録され、アタッチされている Azure Synapse Spark プールの名前を指定してください。

        ```python
        synapse_spark_compute = "<synapse spark compute>"
        ```

- **Azure ML と Azure Synapse ワークスペースの間にリンクされたサービスがまだない場合は**、「**Azure Synapse – Azure ML と Azure Synapse Analytics の構成**」ノートブックを実行して、リンクされたサービスを構成してから、「**Azure Synapse - Apache Spark を使用して潜在的なネットワーク ビーコンを検出する**」ノートブックを実行してください。

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [Jupyter のノートブックを使用してセキュリティの脅威を検出する](notebooks.md)
- [チュートリアル: Microsoft Sentinel での Jupyter Notebook と MSTICPy の概要](notebook-get-started.md)
- [Azure Synapse Analytics と Azure Machine Learning ワークスペースをリンクして Apache Spark プールをアタッチする (プレビュー)](../machine-learning/how-to-link-synapse-ml-workspaces.md)