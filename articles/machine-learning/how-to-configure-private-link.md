---
title: プライベート エンドポイントを構成する
titleSuffix: Azure Machine Learning
description: プライベート エンドポイントを使用して、仮想ネットワークから Azure Machine Learning ワークスペースに安全にアクセスします。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.openlocfilehash: 134eeff68debe0faa799d5fb5bfa4b477deef7f0
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562563"
---
# <a name="configure-a-private-endpoint-for-an-azure-machine-learning-workspace"></a>Azure Machine Learning ワークスペース用にプライベート エンドポイントを構成する

このドキュメントでは、Azure Machine Learning ワークスペース用にプライベート エンドポイントを構成する方法について説明します。 Azure Machine Learning 用の仮想ネットワークの作成については、[仮想ネットワークの分離とプライバシーの概要](how-to-network-security-overview.md)に関する記事をご覧ください。

Azure Private Link では、プライベート エンドポイントを使用してワークスペースに接続できます。 プライベート エンドポイントは、仮想ネットワーク内にある一組のプライベート IP アドレスです。 これにより、ワークスペースへのアクセスが、プライベート IP アドレスでのみ行われるように制限できます。 プライベート エンドポイントを使用すると、データ流出のリスクを軽減できます。 プライベート エンドポイントの詳細については、[Azure Private Link](../private-link/private-link-overview.md) に関する記事を参照してください。

> [!WARNING]
> プライベート エンドポイントを使用してワークスペースをセキュリティで保護しても、エンドツーエンドのセキュリティは保証されません。 ソリューションの個々のコンポーネントのすべてをセキュリティで保護する必要があります。 たとえば、ワークスペースでプライベート エンドポイントを使用していても、Azure ストレージ アカウントが VNet の内側にない場合、ワークスペースとストレージの間のトラフィックでは、セキュリティのために VNet は使用されません。
>
> Azure Machine Learning で使用されるリソースのセキュリティ保護の詳細については、次の記事を参照してください。
>
> * [仮想ネットワークの分離とプライバシーの概要](how-to-network-security-overview.md)。
> * [ワークスペース リソースの保護](how-to-secure-workspace-vnet.md)。
> * [トレーニング環境の保護](how-to-secure-training-vnet.md)。
> * [推論環境の保護](how-to-secure-inferencing-vnet.md)。
> * [VNet で Azure Machine Learning Studio を使用する](how-to-enable-studio-virtual-network.md)。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [cli-version-info](../../includes/machine-learning-cli-version-1-only.md)]

* プライベート エンドポイントを作成するには、既存の仮想ネットワークが必要です。 
* プライベート エンドポイントを追加する前に、[プライベート エンドポイントのネットワーク ポリシーを無効にします](../private-link/disable-private-endpoint-network-policy.md)。

## <a name="limitations"></a>制限事項

* プライベート エンドポイントで保護されたワークスペースのパブリック アクセスを有効にしていて、パブリック インターネット経由で Azure Machine Learning スタジオを使用する場合、デザイナーなどの一部の機能から自分のデータにアクセスできないことがあります。 この問題は、データが VNet の背後でセキュリティ保護されているサービスに格納されている場合に発生します。 たとえば、Azure Storage アカウントです。
* Mozilla Firefox を使用している場合、ワークスペースのプライベート エンドポイントにアクセスしようとしたときに問題が発生することがあります。 この問題は、Mozilla の DNS over HTTPS に関連している可能性があります。 回避策として、Microsoft Edge または Google Chrome を使用することをお勧めします。
* プライベート エンドポイントを使用しても、ワークスペースの削除やコンピューティング リソースの管理などの Azure コントロール プレーン (管理操作) には影響しません。 たとえば、コンピューティング先の作成、更新、削除などです。 これらの操作は、通常どおりパブリック インターネット経由で実行されます。 Azure Machine Learning Studio を使用するなどのデータ プレーン操作、API (公開されたパイプラインを含む)、または SDK では、プライベート エンドポイントが使用されます。
* プライベート エンドポイントを使用してワークスペースにコンピューティング インスタンスまたはコンピューティング クラスターを作成するときは、コンピューティング インスタンスとコンピューティング クラスターがワークスペースと同じ Azure リージョンに存在する必要があります。
* プライベート エンドポイントを使用して Azure Kubernetes Service クラスターを作成するとき、またはそれをワークスペースにアタッチするときは、クラスターがワークスペースと同じリージョンに存在する必要があります。
* 複数のプライベート エンドポイントで 1 つのワークスペースを使用するときは (プレビュー)、プライベート エンドポイントの 1 つが、次の依存関係サービスと同じ VNet 内に存在する必要があります。

    * ワークスペース用の既定のストレージを提供する Azure ストレージ アカウント
    * ワークスペース用の Azure Key Vault
    * ワークスペース用の Azure Container Registry。

    たとえば、1 つの VNet ("サービス" VNet) には、依存関係サービスとワークスペース用のプライベート エンドポイントが含まれます。 この構成により、ワークスペースはサービスと通信できます。 別の VNet ("クライアント") には、ワークスペース用のプライベート エンドポイントのみが含まれ、クライアント開発用コンピューターとワークスペースの間の通信にのみ使用できます。

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>プライベート エンドポイントを使用するワークスペースを作成する

プライベート エンドポイントを使用してワークスペースを作成するには、次のいずれかの方法を使用します。 いずれの方法でも __既存の仮想ネットワークが必要です__。

> [!TIP]
> ワークスペース、プライベート エンドポイント、仮想ネットワークを同時に作成する場合は、「[Azure Resource Manager テンプレートを使用して Azure Machine Learning のワークスペースを作成する](how-to-create-workspace-template.md)」を参照してください。

# <a name="python"></a>[Python](#tab/python)

Azure Machine Learning Python SDK には、[PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) クラスが用意されています。これを [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) で使用すると、プライベート エンドポイントを使用してワークスペースを作成できます。 このクラスには、既存の仮想ネットワークが必要です。

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[機械学習のための Azure CLI 拡張機能 1.0 ](reference-azure-machine-learning-cli.md)には、[az ml workspace create](/cli/azure/ml/workspace#az_ml_workspace_create) コマンドが用意されています。 このコマンドの次のパラメーターは、プライベート ネットワークを使用してワークスペースを作成するために使用できますが、既存の仮想ネットワークが必要です。

* `--pe-name`:作成するプライベート エンドポイントの名前。
* `--pe-auto-approval`:ワークスペースへのプライベート エンドポイント接続を自動的に承認するかどうか。
* `--pe-resource-group`:プライベート エンドポイントを作成するリソース グループ。 仮想ネットワークが含まれているグループと同じグループでなければなりません。
* `--pe-vnet-name`:プライベート エンドポイントを作成する既存の仮想ネットワーク。
* `--pe-subnet-name`:プライベート エンドポイントを作成するサブネットの名前。 既定値は `default` です。

これらのパラメーターは、create コマンドのその他の必須パラメーターの他に追加するものです。 たとえば、次のコマンドは、既存のリソース グループと VNet を使用して、米国西部リージョンに新しいワークスペースを作成します。

```azurecli
az ml workspace create -r myresourcegroup \
    -l westus \
    -n myworkspace \
    --pe-name myprivateendpoint \
    --pe-auto-approval \
    --pe-resource-group myresourcegroup \
    --pe-vnet-name myvnet \
    --pe-subnet-name mysubnet
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure Machine Learning スタジオの __[ネットワーク]__ タブで、プライベート エンドポイントを構成できます。 ただし、既存の仮想ネットワークが必要です。 詳細については、[ポータルでのワークスペースの作成](how-to-manage-workspace.md)に関するページを参照してください。

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>ワークスペースにプライベート エンドポイントを追加する

次のいずれかの方法を使用して、既存のワークスペースにプライベート エンドポイントを追加します。

> [!WARNING]
>
> このワークスペースに関連付けられている既存のコンピューティング先があり、それらがプライベート エンドポイントが作成されたのと同じ仮想ネットワークの背後にない場合、それらは機能しません。

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

この例で使用されているクラスとメソッドの詳細については、[PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) と [Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-) を参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[機械学習のための Azure CLI 拡張機能 1.0 ](reference-azure-machine-learning-cli.md)には、[az ml workspace private-endpoint add](/cli/azure/ml(v1)/workspace/private-endpoint#az_ml_workspace_private_endpoint_add) コマンドが用意されています。

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval --pe-vnet-name myvnet
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

ポータルの Azure Machine Learning ワークスペースから、 __[プライベート エンドポイント接続]__ を選択し、 __[+ プライベート エンドポイント]__ を選択します。 新しいプライベート エンドポイントを作成するには、フィールドを使用します。

* __[リージョン]__ を選択する場合は、ご使用の仮想ネットワークと同じリージョンを選択します。 
* __[リソースの種類]__ を選択する場合は、__Microsoft.MachineLearningServices/workspaces__ を使用します。 
* __[リソース]__ を実際のワークスペース名に設定します。

最後に、 __[作成]__ を選択してプライベート エンドポイントを作成します。

---

## <a name="remove-a-private-endpoint"></a>プライベート エンドポイントを削除する

ワークスペースからプライベート エンドポイントを削除するには、次のいずれかの方法を使用します。

# <a name="python"></a>[Python](#tab/python)

プライベート エンドポイントを削除するには、[Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) を使用します。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[機械学習のための Azure CLI 拡張機能 1.0 ](reference-azure-machine-learning-cli.md)には、[az ml workspace private-endpoint delete](/cli/azure/ml(v1)/workspace/private-endpoint#az_ml_workspace_private_endpoint_delete) コマンドが用意されています。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

ポータルの Azure Machine Learning ワークスペースから、 __[プライベート エンドポイント接続]__ を選択し、削除するエンドポイントを選択します。 最後に、 __[削除]__ を選択します。

---

## <a name="securely-connect-to-your-workspace"></a>ワークスペースに安全に接続する

[!INCLUDE [machine-learning-connect-secure-workspace](../../includes/machine-learning-connect-secure-workspace.md)]

## <a name="multiple-private-endpoints-preview"></a>複数のプライベート エンドポイント (プレビュー)

プレビュー機能として、Azure Machine Learning では 1 つのワークスペースに複数のプライベート エンドポイントを使用することがサポートされています。 複数のプライベート エンドポイントは、異なる環境を分離しておくときによく使用されます。 複数のプライベート エンドポイントを使用することで有効になるシナリオを次に示します。

* 別の VNet 内のクライアント開発環境。
* 別の VNet 内の Azure Kubernetes Service (AKS) クラスター。
* 別の VNet 内の他の Azure サービス。 たとえば、Azure Synapse と Azure Data Factory で、1 つの Microsoft マネージド仮想ネットワークを使用できます。 どの場合も、ワークスペース用のプライベート エンドポイントを、それらのサービスによって使用されるマネージド VNet に追加できます。 これらのサービスでマネージド仮想ネットワークを使用する方法の詳細については、次の記事を参照してください。

    * [Synapse のマネージド プライベート エンドポイント](../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md)
    * [Azure Data Factory のマネージド仮想ネットワーク](../data-factory/managed-virtual-network-private-endpoint.md)。

    > [!IMPORTANT]
    > [Synapse のデータ流出防止](../synapse-analytics/security/workspace-data-exfiltration-protection.md)は、Azure Machine Learning ではサポートされていません。

> [!IMPORTANT]
> ワークスペース用のプライベート エンドポイントが含まれる各 VNet は、ワークスペースで使用される Azure ストレージ アカウント、Azure Key Vault、Azure Container Registry にアクセスできる必要があります。 たとえば、各 VNet にサービス用のプライベート エンドポイントを作成します。

複数のエンドポイントを追加するには、「[ワークスペースにプライベート エンドポイントを追加する](#add-a-private-endpoint-to-a-workspace)」セクションで説明されているのと同じ手順を使用します。

### <a name="scenario-isolated-clients"></a>シナリオ: 分離されたクライアント

Azure Machine Learning によって使用されているコンピューティング リソースに直接アクセスできないように、開発クライアントを分離したい場合は、次の手順を使用します。

> [!NOTE]
> これらの手順では、ワークスペース、Azure ストレージ アカウント、Azure Key Vault、Azure Container Registry が既にあるものとします。 既存の VNet に、これらの各サービスのプライベート エンドポイントがあります。

1. クライアント用に別の VNet を作成します。 この VNet には、クライアントとして機能する Azure Virtual Machines や、VNet に接続するためにオンプレミスのクライアントによって使用される VPN Gateway が含まれている場合があります。
1. ワークスペースによって使用される Azure ストレージ アカウント、Azure Key Vault、Azure Container Registry 用に、新しいプライベート エンドポイントを追加します。 これらのプライベート エンドポイントは、クライアント VNet に存在する必要があります。
1. ワークスペースによって使用される追加のストレージがある場合は、そのストレージ用に新しいプライベート エンドポイントを追加します。 プライベート エンドポイントは、クライアント VNet に存在し、プライベート DNS ゾーンの統合が有効にされている必要があります。
1. ワークスペースに新しいプライベート エンドポイントを追加します。 このプライベート エンドポイントは、クライアント VNet に存在し、プライベート DNS ゾーンの統合が有効にされている必要があります。
1. 記事「[仮想ネットワークでスタジオを使用する](how-to-enable-studio-virtual-network.md#datastore-azure-storage-account)」の手順を使用して、スタジオがストレージ アカウントにアクセスできるようにします。

次の図はこの構成を示したものです。 __ワークロード__ VNet には、トレーニングとデプロイのためにワークスペースによって作成されたコンピューティングが含まれます。 __クライアント__ VNet には、クライアントまたはクライアントの ExpressRoute と VPN の接続が含まれます。 どちらの VNet にも、ワークスペース、Azure ストレージ アカウント、Azure Key Vault、Azure Container Registry 用のプライベート エンドポイントが含まれます。

:::image type="content" source="./media/how-to-configure-private-link/multiple-private-endpoint-workspace-client.png" alt-text="分離されたクライアント VNet の図":::

### <a name="scenario-isolated-azure-kubernetes-service"></a>シナリオ: 分離された Azure Kubernetes Service

ワークスペースによって使用される分離された Azure Kubernetes Service を作成する場合は、次の手順を使用します。

> [!NOTE]
> これらの手順では、ワークスペース、Azure ストレージ アカウント、Azure Key Vault、Azure Container Registry が既にあるものとします。 既存の VNet に、これらの各サービスのプライベート エンドポイントがあります。

1. Azure Kubernetes Service インスタンスを作成します。 作成の間に、AKS によって AKS クラスターが含まれる VNet が作成されます。
1. ワークスペースによって使用される Azure ストレージ アカウント、Azure Key Vault、Azure Container Registry 用に、新しいプライベート エンドポイントを追加します。 これらのプライベート エンドポイントは、クライアント VNet に存在する必要があります。
1. ワークスペースによって使用される他のストレージがある場合は、そのストレージ用に新しいプライベート エンドポイントを追加します。 プライベート エンドポイントは、クライアント VNet に存在し、プライベート DNS ゾーンの統合が有効にされている必要があります。
1. ワークスペースに新しいプライベート エンドポイントを追加します。 このプライベート エンドポイントは、クライアント VNet に存在し、プライベート DNS ゾーンの統合が有効にされている必要があります。
1. AKS クラスターを Azure Machine Learning ワークスペースにアタッチします。 詳細については、「[Azure Kubernetes Service クラスターを作成してアタッチする](how-to-create-attach-kubernetes.md#attach-an-existing-aks-cluster)」を参照してください。

:::image type="content" source="./media/how-to-configure-private-link/multiple-private-endpoint-workspace-aks.png" alt-text="分離された AKS VNet の図":::

## <a name="enable-public-access"></a>パブリック アクセスを有効にする

状況によっては、VNet ではなくパブリック エンドポイント経由で、セキュリティで保護された自分のワークスペースにだれかが接続できるようにすることが必要になる場合があります。 プライベート エンドポイントを使用してワークスペースを構成した後で、必要に応じてワークスペースへのパブリック アクセスを有効にすることができます。 これを実行しても、プライベート エンドポイントは削除されません。 VNet の背後にあるコンポーネント間のすべての通信は、引き続きセキュリティで保護されます。 これにより、VNet 経由のプライベート アクセスに加えて、そのワークスペースのみへのパブリック アクセスが可能になります。

> [!WARNING]
> パブリック エンドポイント経由で接続する場合:
> * __Studio の一部の機能では、データへのアクセスに失敗します__。 この問題は、_データが VNet の背後でセキュリティ保護されているサービスに格納されている場合_ に発生します。 たとえば、Azure Storage アカウントです。 
> * コンピューティング インスタンスでの Jupyter、JupyterLab、RStudio の使用 (ノートブックの実行を含む) は __サポートされていません__。

プライベート エンドポイントが有効なワークスペースへのパブリック アクセスを有効にするには、次の手順を使用します。

# <a name="python"></a>[Python](#tab/python)

プライベート エンドポイントを削除するには、[Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) を使用します。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
ws.update(allow_public_access_when_behind_vnet=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[機械学習のための Azure CLI 拡張機能 1.0 ](reference-azure-machine-learning-cli.md)には、[az ml workspace update](/cli/azure/ml/workspace#az_ml_workspace_update) コマンドが用意されています。 ワークスペースへのパブリック アクセスを有効にするには、パラメーター `--allow-public-access true` を追加します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

現在、ポータルを使用してこの機能を有効にする方法はありません。

---

## <a name="next-steps"></a>次の手順

* Azure Machine Learning ワークスペースのセキュリティ保護の詳細については、「[仮想ネットワークの分離とプライバシーの概要](how-to-network-security-overview.md)」を参照してください。

* 仮想ネットワークでカスタム DNS ソリューションの使用を計画している場合は、「[カスタム DNS サーバーでワークスペースを使用する方法](how-to-custom-dns.md)」を参照してください。