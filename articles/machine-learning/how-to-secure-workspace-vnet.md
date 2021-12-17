---
title: 仮想ネットワークを使用して Azure Machine Learning ワークスペースをセキュリティで保護する
titleSuffix: Azure Machine Learning
description: 分離された Azure Virtual Network を使用して、Azure Machine Learning ワークスペースおよび関連付けられているリソースを保護します。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, security
ms.openlocfilehash: 11e501615e10ccd3bfb407e12f1839d27e81e337
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562304"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>仮想ネットワークを使用して Azure Machine Learning ワークスペースをセキュリティで保護する

この記事では、仮想ネットワーク内の Azure Machine Learning ワークスペースとそれに関連付けられているリソースをセキュリティで保護する方法について説明します。

> [!TIP]
> この記事は、Azure Machine Learning ワークフローのセキュリティ保護に関するシリーズの一部です。 このシリーズの他の記事は次のとおりです。
>
> * [Virtual Network の概要](how-to-network-security-overview.md)
> * [トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md)
> * [推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)
> * [スタジオの機能を有効にする](how-to-enable-studio-virtual-network.md)
> * [カスタム DNS を使用する](how-to-custom-dns.md)
> * [ファイアウォールを使用する](how-to-access-azureml-behind-firewall.md)
>
> セキュリティで保護されたワークスペースの作成に関するチュートリアルについては、「[チュートリアル: セキュリティで保護されたワークスペースを作成する](tutorial-create-secure-workspace.md)」を参照してください。

この記事では、仮想ネットワークで次のワークスペース リソースを有効にする方法について説明します。
> [!div class="checklist"]
> - Azure Machine Learning ワークスペース
> - Azure Storage アカウント
> - Azure Machine Learning データストアとデータセット
> - Azure Key Vault
> - Azure Container Registry

## <a name="prerequisites"></a>前提条件

+ 一般的な仮想ネットワークのシナリオと全体的な仮想ネットワーク アーキテクチャについては、[ネットワーク セキュリティの概要](how-to-network-security-overview.md)に関するページを参照してください。

+ ベスト プラクティスについては、「[Azure Machine Learning のエンタープライズ セキュリティに関するベスト プラクティス](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-enterprise-security)」の記事を参照してください。

+ コンピューティング リソースで使用する既存の仮想ネットワークとサブネット。

    > [!TIP]
    > 仮想ネットワークで (モデルをデプロイするために) Azure Container Instances を使用することを計画している場合は、ワークスペースと仮想ネットワークが同じリソース グループに存在する必要があります。 それ以外の場合は、異なるグループに配置できます。

+ リソースを仮想ネットワークまたはサブネットにデプロイするには、ご利用のユーザー アカウントが、Azure ロールベースのアクセス制御 (Azure RBAC) で次のアクションへのアクセス許可を保持している必要があります。

    - 仮想ネットワーク リソース上の "Microsoft.Network/virtualNetworks/join/action"。
    - サブネット リソース上の "Microsoft.Network/virtualNetworks/subnet/join/action"。

    ネットワークでの Azure RBAC の詳細については、[ネットワークの組み込みロール](../role-based-access-control/built-in-roles.md#networking)に関するページを参照してください

### <a name="azure-container-registry"></a>Azure Container Registry

* Azure Container Registry が Premium バージョンである必要があります。 アップグレードの詳細については、「[SKU の変更](../container-registry/container-registry-skus.md#changing-tiers)」を参照してください。

* トレーニングまたは推論に使用されるストレージ アカウントとコンピューティング ターゲットと同じ仮想ネットワークとサブネット内に Azure Container Registry が存在している必要があります。

* Azure Machine Learning ワークスペースに、[Azure Machine Learning コンピューティング クラスター](how-to-create-attach-compute-cluster.md)が含まれている必要があります。

## <a name="limitations"></a>制限事項

### <a name="azure-storage-account"></a>Azure Storage アカウント

Azure Machine Learning ワークスペースと Azure ストレージ アカウントがどちらもプライベート エンドポイントを使用して VNet に接続する場合、その両方が同じサブネット内に存在する必要があります。

### <a name="azure-container-registry"></a>Azure Container Registry

ACR が仮想ネットワークの背後にある場合、Azure Machine Learning でそれを使用して Docker イメージを直接構築することはできません。 代わりに、コンピューティング クラスターを使用してイメージが構築されます。

> [!IMPORTANT]
> Docker イメージの作成に使用されるコンピューティング クラスターは、モデルのトレーニングとデプロイに使用されるパッケージ リポジトリにアクセスできる必要があります。 パブリック リポジトリへのアクセスを許可するネットワーク セキュリティ規則の追加、[プライベート Python パッケージの使用](how-to-use-private-python-packages.md)、またはパッケージが既に含まれている [カスタム Docker イメージ](how-to-train-with-custom-image.md) の使用が必要になる場合があります。

> [!WARNING]
> 仮想ネットワークと通信するために、お使いの Azure Container Registry によって使用されているのがプライベート エンドポイントの場合、Azure Machine Learning コンピューティング クラスターでマネージド ID を使用することはできません。 コンピューティング クラスターでマネージド ID を使用するには、ワークスペースの Azure Container Registry でサービス エンドポイントを使用します。

## <a name="required-public-internet-access"></a>必要なパブリック インターネット アクセス

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

ファイアウォール ソリューションの使用に関する詳細については、[Azure Machine Learning でのファイアウォールの使用](how-to-access-azureml-behind-firewall.md)に関するページを参照してください。

## <a name="secure-the-workspace-with-private-endpoint"></a>プライベート エンドポイントを使用してワークスペースをセキュリティで保護する

Azure Private Link では、プライベート エンドポイントを使用してワークスペースに接続できます。 プライベート エンドポイントは、仮想ネットワーク内にある一組のプライベート IP アドレスです。 これにより、ワークスペースへのアクセスが、プライベート IP アドレスでのみ行われるように制限できます。 プライベート エンドポイントを使用すると、データ流出のリスクを軽減できます。

ワークスペースのプライベート エンドポイントの構成の詳細については、[プライベート エンドポイントの構成方法](how-to-configure-private-link.md)に関する記事を参照してください。

> [!WARNING]
> プライベート エンドポイントを使用してワークスペースをセキュリティで保護しても、エンドツーエンドのセキュリティは保証されません。 ソリューションの個々のコンポーネントをセキュリティで保護するには、この記事の残りの手順と VNet シリーズの手順に従う必要があります。 たとえば、ワークスペースでプライベート エンドポイントを使用していても、Azure Storage アカウントが VNet の内側にない場合、ワークスペースとストレージの間のトラフィックでは、セキュリティのために VNet は使用されません。

## <a name="secure-azure-storage-accounts"></a>Azure Storage アカウントをセキュリティで保護する

Azure Machine Learning では、プライベート エンドポイントまたはサービス エンドポイントを使用するようにストレージ アカウントを構成できます。 

# <a name="private-endpoint"></a>[プライベート エンドポイント](#tab/pe)

1. Azure portal で、Azure Storage アカウントストレージ アカウントを選択します。
1. 「[Azure Storage のプライベート エンドポイントを使用する](../storage/common/storage-private-endpoints.md#creating-a-private-endpoint)」の情報を利用し、次のストレージ サブリソースのプライベート エンドポイントを追加します。

    * **BLOB**
    * **[最近使ったファイル]**
    * **キュー** - Azure Machine Learning パイプラインで [ParallelRunStep](./tutorial-pipeline-batch-scoring-classification.md) を使用する予定の場合にのみ必要です。
    * **テーブル** - Azure Machine Learning パイプラインで [ParallelRunStep](./tutorial-pipeline-batch-scoring-classification.md) を使用する予定の場合にのみ必要です。

    :::image type="content" source="./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png" alt-text="プライベート エンドポイント構成ページのスクリーンショット。BLOB オプションとファイル オプションを確認できます。":::

    > [!TIP]
    > 既定のストレージでは **ない** ストレージ アカウントを構成するとき、追加するストレージ アカウントに相当する **ターゲット サブリソース** タイプを選択します。

1. サブリソースのプライベート エンドポイントを作成した後、ストレージ アカウントの __[ネットワーク]__ の下で __[ファイアウォールと仮想ネットワーク]__ タブを選択します。
1. __[選択されたネットワーク]__ を選択し、 __[リソース インスタンス]__ の下で __[リソースの種類]__ として `Microsoft.MachineLearningServices/Workspace` を選択します。 __インスタンス名__ を使用してワークスペースを選択します。 詳細については、「[システム割り当てマネージド ID に基づく信頼されたアクセス](../storage/common/storage-network-security.md#trusted-access-based-on-system-assigned-managed-identity)」を参照してください。

    > [!TIP]
    > あるいは、 __[信頼されたサービスの一覧にある Azure サービスがこのストレージ アカウントにアクセスすることを許可します]__ を選択し、信頼されたサービスからのアクセスをもっと広範囲で許可できます。 詳細については、[Azure Storage ファイアウォールおよび仮想ネットワークの構成](../storage/common/storage-network-security.md#trusted-microsoft-services)に関する記事を参照してください。

    :::image type="content" source="./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-no-vnet.png" alt-text="プライベート エンドポイントの使用時の Azure portal 内の Azure Storage ページの [ネットワーク] 領域":::

1. __[保存]__ を選んで構成を保存します。

> [!TIP]
> プライベート エンドポイントを使用する場合は、パブリック アクセスを無効にすることもできます。 詳細については、[パブリック読み取りアクセスの禁止](../storage/blobs/anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account)に関するページをご覧ください。

# <a name="service-endpoint"></a>[サービス エンドポイント](#tab/se)

1. Azure portal で、Azure Storage アカウントストレージ アカウントを選択します。

1. ページの左側にある __[セキュリティとネットワーク]__ セクションで、 __[ネットワーク]__ を選択し、 __[ファイアウォールと仮想ネットワーク]__ タブを選択します。

1. __[選択されたネットワーク]__ を選択します。 __[仮想ネットワーク]__ の __[既存の仮想ネットワークの追加]__ リンクを選択し、ご自身のワークスペースで使用する仮想ネットワークを選択します。

    > [!IMPORTANT]
    > ストレージ アカウントは、トレーニングまたは推論に使用されるコンピューティング インスタンスまたはクラスターと同じ仮想ネットワークとサブネット内に存在する必要があります。

1. __[リソース インスタンス]__ で、 __[リソースの種類]__ として `Microsoft.MachineLearningServices/Workspace` を選択し、 __[インスタンス名]__ を使用してご自身のワークスペースを選択します。 詳細については、「[システム割り当てマネージド ID に基づく信頼されたアクセス](../storage/common/storage-network-security.md#trusted-access-based-on-system-assigned-managed-identity)」を参照してください。

    > [!TIP]
    > あるいは、 __[信頼されたサービスの一覧にある Azure サービスがこのストレージ アカウントにアクセスすることを許可します]__ を選択し、信頼されたサービスからのアクセスをもっと広範囲で許可できます。 詳細については、[Azure Storage ファイアウォールおよび仮想ネットワークの構成](../storage/common/storage-network-security.md#trusted-microsoft-services)に関する記事を参照してください。

    :::image type="content" source="./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png" alt-text="Azure portal 内の Azure Storage ページの [ネットワーク] 領域":::

1. __[保存]__ を選んで構成を保存します。

> [!TIP]
> サービス エンドポイントを使用する場合は、パブリック アクセスを無効にすることもできます。 詳細については、[パブリック読み取りアクセスの禁止](../storage/blobs/anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account)に関するページをご覧ください。

---

## <a name="secure-azure-key-vault"></a>Azure Key Vault をセキュリティで保護する

Azure Machine Learning では、関連付けられた Key Vault インスタンスを使用して、次の資格情報が格納されます。
* 関連付けられたストレージ アカウントの接続文字列
* Azure コンテナー リポジトリ インスタンスへのパスワード
* データ ストアへの接続文字列

Azure キー コンテナーは、プライベート エンドポイントまたはサービス エンドポイントのいずれかを使用するように構成できます。 仮想ネットワークの内側で、Azure Machine Learning 実験機能を Azure Key Vault で使用するには、次の手順を使用します。

> [!TIP]
> プライベート エンドポイントとサービスエンド ポイント、どちらを使用する場合でも、キー コンテナーは、ワークスペースのプライベート エンドポイントと同じネットワーク内にある必要があります。

# <a name="private-endpoint"></a>[プライベート エンドポイント](#tab/pe)

Azure Key Vault でプライベート エンドポイントを使用する方法については、[Azure Private Link への Key Vault の統合](../key-vault/general/private-link-service.md#establish-a-private-link-connection-to-key-vault-using-the-azure-portal)に関するページをご覧ください。


# <a name="service-endpoint"></a>[サービス エンドポイント](#tab/se)

1. ワークスペースに関連付けられている Key Vault に移動します。

1. __[Key Vault]__ ページの左側のウィンドウで、 __[ネットワーク]__ を選択します。

1. __[ファイアウォールと仮想ネットワーク]__ タブで、次の操作を行います。
    1. __[許可するアクセス元]__ で __[プライベート エンドポイントと選択されたネットワーク]__ を選択します。
    1. __[仮想ネットワーク]__ で、 __[既存の仮想ネットワークを追加]__ を選択して、実験のコンピューティングが存在する仮想ネットワークを追加します。
    1. __[Allow trusted Microsoft services to bypass this firewall]\(信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する\)__ で、 __[はい]__ を選択します。

    :::image type="content" source="./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png" alt-text="[Key Vault] ウィンドウの [ファイアウォールと仮想ネットワーク] セクション":::

詳細については、「[Azure Key Vault のネットワーク設定を構成する](../key-vault/general/how-to-azure-key-vault-network-security.md)」を参照してください。

---

## <a name="enable-azure-container-registry-acr"></a>Azure Container Registry (ACR) の有効化

> [!TIP]
> ワークスペースの作成時に既存の Azure Container Registry を使用しなかった場合、それが存在しない可能性があります。 既定では、必要になるまで、ワークスペースによって ACR インスタンスが作成されることはありません。 強制的に作成するには、このセクションの手順を使用する前に、ワークスペースを使用してモデルをトレーニングまたはデプロイします。

プライベート エンドポイントを使用するように Azure Container Registry を構成できます。 次の手順を使用して、仮想ネットワーク内にあるときに ACR を使用するようにワークスペースを構成します。

1. 次の方法のいずれかを使用して、お使いのワークスペースの Azure Container Registry の名前を見つけます。

    __Azure Portal__

    ワークスペースの [概要] セクションの Azure Container Registry への __[レジストリ]__ 値のリンク。

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="ワークスペースの Azure Container Registry" border="true":::

    __Azure CLI__

    [Azure CLI 用の Machine Learning 拡張機能をインストールしている](reference-azure-machine-learning-cli.md)場合は、`az ml workspace show` コマンドを使用して、ワークスペースの情報を表示できます。

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    このコマンドでは `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` のような値が返されます。 文字列の最後の部分がワークスペースの Azure Container Registry の名前です。

1. 仮想ネットワークへのアクセスを制限するには、[Azure Container Registry へのプライベート接続](../container-registry/container-registry-private-link.md)に関するページの手順に従います。 仮想ネットワークを追加するときに、Azure Machine Learning リソースの仮想ネットワークとサブネットを選択します。

1. ワークスペースの ACR を構成し、[信頼されたサービスによるアクセスを許可](../container-registry/allow-access-trusted-services.md)します。

1. Azure Machine Learning コンピューティング クラスターを作成する。 これは、ACR が VNet の背後にある場合に Docker イメージをビルドするために使用されます。 詳細については、[コンピューティング クラスターの作成](how-to-create-attach-compute-cluster.md)に関するページを参照してください。

1. Azure Machine Learning Python SDK を使用して、コンピューティング インスタンスを使用して Docker イメージをビルドするようにワークスペースを構成します。 次のコード スニペットは、ビルド コンピューティングを設定するためにワークスペースを更新する方法を示しています。 `mycomputecluster` を、使用するクラスターの名前に置き換えてください。

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = '')
    ```

    > [!IMPORTANT]
    > ストレージ アカウント、コンピューティング クラスター、および Azure Container Registry のすべてが、仮想ネットワークと同じサブネット内に存在する必要があります。
    
    詳細については、[update()](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) メソッド リファレンスを参照してください。

> [!TIP]
> ACR が VNet の背後にある場合は、その ACR への[パブリック アクセスを無効](../container-registry/container-registry-access-selected-networks.md#disable-public-network-access)にすることもできます。

## <a name="datastores-and-datasets"></a>データストアとデータセット
次の表に、検証をスキップする必要があるサービスの一覧を示します。

| サービス | 検証をスキップする必要があるか |
| ----- |:-----:|
| Azure BLOB ストレージ | はい |
| Azure ファイル共有 | はい |
| Azure Data Lake Store Gen1 | いいえ |
| Azure Data Lake Store Gen2 | いいえ |
| Azure SQL Database | はい |
| PostgreSQL | はい |

> [!NOTE]
> Azure Data Lake Store Gen1 および Azure Data Lake Store Gen2 では、既定で検証がスキップされるため、何の操作も必要ありません。

次のコード サンプルでは、新しい Azure BLOB データストアが作成され、`skip_validation=True` が設定されます。

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>データセットを使用する

次のデータセットの種類では、データセットの検証をスキップする構文は似ています。
- 区切りファイル
- JSON 
- Parquet
- SQL
- ファイル

次のコードでは、新しい JSON データセットが作成され、`validate=False` が設定されます。

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 
```

## <a name="securely-connect-to-your-workspace"></a>ワークスペースに安全に接続する

[!INCLUDE [machine-learning-connect-secure-workspace](../../includes/machine-learning-connect-secure-workspace.md)]

## <a name="workspace-diagnostics"></a>ワークスペース診断

[!INCLUDE [machine-learning-workspace-diagnostics](../../includes/machine-learning-workspace-diagnostics.md)]

## <a name="next-steps"></a>次のステップ

この記事は、Azure Machine Learning ワークフローのセキュリティ保護に関するシリーズの一部です。 このシリーズの他の記事は次のとおりです。

* [Virtual Network の概要](how-to-network-security-overview.md)
* [トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md)
* [推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)
* [スタジオの機能を有効にする](how-to-enable-studio-virtual-network.md)
* [カスタム DNS を使用する](how-to-custom-dns.md)
* [ファイアウォールを使用する](how-to-access-azureml-behind-firewall.md)