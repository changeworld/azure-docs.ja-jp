---
title: 仮想ネットワークを使用して Azure Machine Learning ワークスペースをセキュリティで保護する
titleSuffix: Azure Machine Learning
description: 分離された Azure Virtual Network を使用して、Azure Machine Learning ワークスペースおよび関連付けられているリソースを保護します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 08/04/2021
ms.topic: how-to
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, security
ms.openlocfilehash: 2ed3e7d1525c750c698e853921900e6d39feb83e
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480356"
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

## <a name="required-public-internet-access"></a>必要なパブリック インターネット アクセス

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

ファイアウォール ソリューションの使用に関する詳細については、[Azure Machine Learning でのファイアウォールの使用](how-to-access-azureml-behind-firewall.md)に関するページを参照してください。

## <a name="secure-the-workspace-with-private-endpoint"></a>プライベート エンドポイントを使用してワークスペースをセキュリティで保護する

Azure Private Link では、プライベート エンドポイントを使用してワークスペースに接続できます。 プライベート エンドポイントは、仮想ネットワーク内にある一組のプライベート IP アドレスです。 これにより、ワークスペースへのアクセスが、プライベート IP アドレスでのみ行われるように制限できます。 プライベート エンドポイントを使用すると、データ流出のリスクを軽減できます。

ワークスペースのプライベート エンドポイントの構成の詳細については、[プライベート エンドポイントの構成方法](how-to-configure-private-link.md)に関する記事を参照してください。

> [!WARNING]
> プライベート エンドポイントを使用してワークスペースをセキュリティで保護しても、エンドツーエンドのセキュリティは保証されません。 ソリューションの個々のコンポーネントをセキュリティで保護するには、この記事の残りの手順と VNet シリーズの手順に従う必要があります。 たとえば、ワークスペースでプライベート エンドポイントを使用していても、Azure Storage アカウントが VNet の内側にない場合、ワークスペースとストレージの間のトラフィックでは、セキュリティのために VNet は使用されません。

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Azure のストレージ アカウントをサービス エンドポイントで保護する

Azure Machine Learning では、サービス エンドポイントまたはプライベート エンドポイントを使用するようにストレージ アカウントを構成できます。 このセクションでは、サービス エンドポイントを使用して Azure ストレージ アカウントをセキュリティで保護する方法について説明します。 プライベート エンドポイントについては、次のセクションを参照してください。

仮想ネットワーク内のワークスペースに Azure ストレージ アカウントを使用するには、次の手順を使用します。

1. Azure portal で、ワークスペースで使用するストレージ サービスに移動します。

   [![Azure Machine Learning のワークスペースにアタッチされているストレージ](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. ストレージのサービス アカウント ページで、 __[ネットワーク]__ を選択します。

   ![Azure portal 内の Azure Storage ページの [ネットワーク] 領域](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. __[ファイアウォールと仮想ネットワーク]__ タブで、次の操作を行います。
    1. __[選択されたネットワーク]__ を選択します。
    1. __[仮想ネットワーク]__ で、 __[Add existing virtual network]\(既存の仮想ネットワークを追加\)__ というリンクを選択します。 この操作により、コンピューティングが置かれている仮想ネットワークが追加されます (手順 1 参照)。

        > [!IMPORTANT]
        > ストレージ アカウントは、トレーニングまたは推論に使用されるコンピューティング インスタンスまたはクラスターと同じ仮想ネットワークとサブネット内に存在する必要があります。

    1. __[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]__ チェック ボックスをオンにします。 この変更は、すべての Azure サービスにユーザーのストレージ アカウントへのアクセス許可を与えるものではありません。
    
        * 一部のサービスのリソースは、**ユーザーのサブスクリプションで登録されている場合に**、特定の操作のために **同じサブスクリプション内の** ストレージ アカウントにアクセスできます。 たとえば、ログの書き込みやバックアップの作成などです。
        * 一部のサービスのリソースでは、そのシステム割り当てマネージド ID に __Azure ロールを割り当てる__ ことで、ストレージ アカウントへのアクセスを明示的に許可できます。

        詳細については、[Azure Storage ファイアウォールおよび仮想ネットワークの構成](../storage/common/storage-network-security.md#trusted-microsoft-services)に関する記事を参照してください。

> [!TIP]
> サービス エンドポイントを使用する場合は、パブリック アクセスを無効にすることもできます。 詳細については、[パブリック読み取りアクセスの禁止](../storage/blobs/anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account)に関するページをご覧ください。

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Azure のストレージ アカウントをプライベート エンドポイントで保護する

Azure Machine Learning では、サービス エンドポイントまたはプライベート エンドポイントを使用するようにストレージ アカウントを構成できます。 ストレージ アカウントでプライベート エンドポイントが使用される場合、既定のストレージ アカウントにプライベート エンドポイントを 2 つ構成する必要があります。
1. **BLOB** ターゲット サブリソースのあるプライベート エンドポイント。
1. **ファイル** ターゲット サブリソースのあるプライベート エンドポイント (ファイル共有)。

> [!TIP]
> パイプラインで [ParallelRunStep](./tutorial-pipeline-batch-scoring-classification.md) を使用する予定の場合は、**キュー** と **テーブル** のターゲット サブリソースのあるプライベート エンドポイントも構成する必要があります。 ParallelRunStep は、タスクのスケジュール設定とディスパッチのために、内部でキューとテーブルを使用します。

![プライベート エンドポイント構成ページのスクリーンショット。BLOB オプションとファイル オプションを確認できます。](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

既定のストレージでは **ない** ストレージ アカウントにプライベート エンドポイントを構成するには、追加するストレージ アカウントに相当する **ターゲット サブリソース** タイプを選択します。

詳細については、「[Azure Storage のプライベート エンドポイントを使用する](../storage/common/storage-private-endpoints.md)」を参照してください。

> [!TIP]
> プライベート エンドポイントを使用する場合は、パブリック アクセスを無効にすることもできます。 詳細については、[パブリック読み取りアクセスの禁止](../storage/blobs/anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account)に関するページをご覧ください。

## <a name="secure-azure-key-vault"></a>Azure Key Vault をセキュリティで保護する

Azure Machine Learning では、関連付けられた Key Vault インスタンスを使用して、次の資格情報が格納されます。
* 関連付けられたストレージ アカウントの接続文字列
* Azure コンテナー リポジトリ インスタンスへのパスワード
* データ ストアへの接続文字列

Azure キー コンテナーは、サービス エンドポイントまたはプライベート エンドポイントのいずれかを使用するように構成できます。 仮想ネットワークの内側で、Azure Machine Learning 実験機能を Azure Key Vault で使用するには、次の手順を使用します。

1. ワークスペースに関連付けられている Key Vault に移動します。

1. __[Key Vault]__ ページの左側のウィンドウで、 __[ネットワーク]__ を選択します。

1. __[ファイアウォールと仮想ネットワーク]__ タブで、次の操作を行います。
    1. __[許可するアクセス元]__ で __[プライベート エンドポイントと選択されたネットワーク]__ を選択します。
    1. __[仮想ネットワーク]__ で、 __[既存の仮想ネットワークを追加]__ を選択して、実験のコンピューティングが存在する仮想ネットワークを追加します。
    1. __[Allow trusted Microsoft services to bypass this firewall]\(信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する\)__ で、 __[はい]__ を選択します。

   [![[キー コンテナー] ウィンドウの [ファイアウォールと仮想ネットワーク] セクション](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Azure Container Registry (ACR) の有効化

> [!TIP]
> ワークスペースの作成時に既存の Azure Container Registry を使用しなかった場合、それが存在しない可能性があります。 既定では、必要になるまで、ワークスペースによって ACR インスタンスが作成されることはありません。 強制的に作成するには、このセクションの手順を使用する前に、ワークスペースを使用してモデルをトレーニングまたはデプロイします。

Azure Container Registry は、サービス エンドポイントまたはプライベート エンドポイントのいずれかを使用するように構成できます。 次の手順を使用して、仮想ネットワーク内にあるときに ACR を使用するようにワークスペースを構成します。

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

1. 仮想ネットワークへのアクセスを制限するには、「[レジストリのネットワーク アクセスを構成する](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)」の手順に従います。 仮想ネットワークを追加するときに、Azure Machine Learning リソースの仮想ネットワークとサブネットを選択します。

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

次の方法を使用して、セキュリティで保護されたワークスペースに接続できます。

* [Azure VPN ゲートウェイ](../vpn-gateway/vpn-gateway-about-vpngateways.md) - オンプレミスのネットワークをプライベート接続を介して VNet に接続します。 接続は、パブリック インターネットを介して行われます。 使用できる VPN ゲートウェイには、次の 2 種類があります。

    * [ポイント対サイト](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md): 各クライアント コンピューターは、VPN クライアントを使用して VNet に接続します。
    * [サイト間](../vpn-gateway/tutorial-site-to-site-portal.md): VPN デバイスにより、VNet がオンプレミス ネットワークに接続されます。

* [ExpressRoute](https://azure.microsoft.com/services/expressroute/) - オンプレミスのネットワークをプライベート接続を介してクラウドに接続します。 接続は、接続プロバイダーを使用して行われます。
* [Azure Bastion](../bastion/bastion-overview.md) - このシナリオでは、VNet 内に Azure 仮想マシン (ジャンプ ボックスと呼ばれることもある) を作成します。 次に、Azure Bastion を使用して VM に接続します。 Bastion を使用すると、ローカル Web ブラウザーから RDP または SSH セッションを使用して VM に接続できます。 続いて、開発環境としてジャンプ ボックスを使用します。 これは VNet 内にあるため、ワークスペースに直接アクセスできます。 ジャンプ ボックスの使用例については、[チュートリアル: セキュリティで保護されたワークスペースの作成](tutorial-create-secure-workspace.md)に関するページを参照してください。

> [!IMPORTANT]
> __VPN ゲートウェイ__ または __ExpressRoute__ を使用する場合は、オンプレミスのリソースと VNet 内のリソース間での名前解決の仕組みを計画する必要があります。 詳細については、[カスタム DNS サーバーの使用](how-to-custom-dns.md)に関する記事を参照してください。

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
