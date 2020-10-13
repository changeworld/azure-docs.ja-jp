---
title: プライベート エンドポイントを構成する
titleSuffix: Azure Machine Learning
description: Azure Private Link を使用して、仮想ネットワークから Azure Machine Learning ワークスペースに安全にアクセスします。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.openlocfilehash: 4ba7ec73ac70723e21b6acad571d62d14edd250a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828130"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Azure Machine Learning ワークスペース用に Azure Private Link を構成する

このドキュメントでは、Azure Machine Learning ワークスペースで Azure Private Link を使用する方法について説明します。 Azure Machine Learning の仮想ネットワークの作成に関する詳細については、「[仮想ネットワークの分離とプライバシーの概要](how-to-network-security-overview.md)」を参照してください。

Azure Private Link では、プライベート エンドポイントを使用してワークスペースに接続できます。 プライベート エンドポイントは、仮想ネットワーク内にある一組のプライベート IP アドレスです。 これにより、ワークスペースへのアクセスが、プライベート IP アドレスでのみ行われるように制限できます。 Private Link を使用すると、データ窃盗のリスクを軽減できます。 プライベート エンドポイントの詳細については、[Azure Private Link](/azure/private-link/private-link-overview) に関する記事を参照してください。

> [!IMPORTANT]
> Azure Private Link は、ワークスペースの削除やコンピューティング リソースの管理などの Azure コントロール プレーン (管理操作) には影響しません。 たとえば、コンピューティング先の作成、更新、削除などです。 これらの操作は、通常どおりパブリック インターネット経由で実行されます。 Azure Machine Learning Studio を使用するなどのデータ プレーン操作、API (公開されたパイプラインを含む)、または SDK では、プライベート エンドポイントが使用されます。
>
> Mozilla Firefox を使用している場合、ワークスペースのプライベート エンドポイントにアクセスしようとしたときに問題が発生することがあります。 この問題は、Mozilla の DNS over HTTPS に関連している可能性があります。 回避策として、Microsoft Edge または Google Chrome を使用することをお勧めします。

## <a name="prerequisites"></a>[前提条件]

カスタマー マネージド キーでプライベート リンクを有効にしたワークスペースを使用する場合は、サポート チケットを使用してこの機能を要求する必要があります。 詳細については、[クォータの管理と増加](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)に関するページを参照してください。

## <a name="limitations"></a>制限事項

Azure Government リージョンまたは Azure China 21Vianet リージョンでは、プライベート リンクで Azure Machine Learning ワークスペースを使用することはできません。

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>プライベート エンドポイントを使用するワークスペースを作成する

プライベート エンドポイントを使用してワークスペースを作成するには、次のいずれかの方法を使用します。

> [!TIP]
> Azure Resource Manager テンプレートを使用すると、必要に応じて新しい仮想ネットワークを作成できます。 その他の方法にはすべて、既存の仮想ネットワークが必要です。

# <a name="resource-manager-template"></a>[Resource Manager テンプレート](#tab/azure-resource-manager)

[https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) にある Azure Resource Manager テンプレートを使用すると、プライベート エンドポイントと仮想ネットワークを使用してワークスペースを簡単に作成することができます。

プライベート エンドポイントを含め、このテンプレートの使用に関する詳細については、「[Azure Resource Manager テンプレートを使用して Azure Machine Learning のワークスペースを作成します。](how-to-create-workspace-template.md)」を参照してください。

# <a name="python"></a>[Python](#tab/python)

Azure Machine Learning Python SDK には、[PrivateEndpointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) クラスが用意されています。これを [Workspace.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) で使用すると、プライベート エンドポイントを使用してワークスペースを作成できます。 このクラスには、既存の仮想ネットワークが必要です。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[機械学習のための Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)には、[az ml workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create) コマンドが用意されています。 このコマンドの次のパラメーターは、プライベート ネットワークを使用してワークスペースを作成するために使用できますが、既存の仮想ネットワークが必要です。

* `--pe-name`:作成するプライベート エンドポイントの名前。
* `--pe-auto-approval`:ワークスペースへのプライベート エンドポイント接続を自動的に承認するかどうか。
* `--pe-resource-group`:プライベート エンドポイントを作成するリソース グループ。 仮想ネットワークが含まれているグループと同じグループでなければなりません。
* `--pe-vnet-name`:プライベート エンドポイントを作成する既存の仮想ネットワーク。
* `--pe-subnet-name`:プライベート エンドポイントを作成するサブネットの名前。 既定値は `default` です。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure Machine Learning スタジオの __[ネットワーク]__ タブで、プライベート エンドポイントを構成できます。 ただし、既存の仮想ネットワークが必要です。 詳細については、[ポータルでのワークスペースの作成](how-to-manage-workspace.md)に関するページを参照してください。

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>プライベート エンドポイント経由でのワークスペースの使用

ワークスペースへの通信は仮想ネットワークからのみ許可されているため、ワークスペースを使用する開発環境はすべて仮想ネットワークのメンバーである必要があります。 たとえば、仮想ネットワーク内の仮想マシンを使用します。

> [!IMPORTANT]
> 接続の一時的な中断を回避するために、Private Link を有効にした後で、ワークスペースに接続しているコンピューターの DNS キャッシュをフラッシュすることをお勧めします。 

Azure Virtual Machines について詳しくは、「[Virtual Machines のドキュメント](/azure/virtual-machines/)」を参照してください。


## <a name="next-steps"></a>次の手順

* Azure Machine Learning ワークスペースのセキュリティ保護の詳細については、「[仮想ネットワークの分離とプライバシーの概要](how-to-network-security-overview.md)」を参照してください。

* 仮想ネットワークでカスタム DNS ソリューションの使用を計画している場合は、「[カスタム DNS サーバーでワークスペースを使用する方法](how-to-custom-dns.md)」を参照してください。
