---
title: ネットワークの着信トラフィックおよび送信トラフィックを構成する
titleSuffix: Azure Machine Learning
description: セキュリティで保護された Azure Machine Learning ワークスペースを使用するときに必要な受信および送信ネットワーク トラフィックを構成する方法。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 09/14/2021
ms.custom: devx-track-python
ms.openlocfilehash: 2207ac32595d3780bf662d9a4124b7cbf74ce6a7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633977"
---
# <a name="configure-inbound-and-outbound-network-traffic"></a>ネットワークの着信トラフィックおよび送信トラフィックを構成する

この記事では、仮想ネットワーク (VNet) の Azure Machine Learning ワークスペースをセキュリティで保護する場合のネットワーク通信の要件について説明します。 これには、Azure Firewall を構成して、Azure Machine Learning ワークスペースとパブリック インターネットへのアクセスを制御する方法が含まれます。 Azure Machine Learning のセキュリティ保護の詳細については、[Azure Machine Learning のエンタープライズ セキュリティ](concept-enterprise-security.md)に関するページを参照してください。

> [!NOTE]
> この記事の情報は、プライベート エンドポイントで構成される Azure Machine Learning ワークスペースに適用されます。

> [!TIP]
> この記事は、Azure Machine Learning ワークフローのセキュリティ保護に関するシリーズの一部です。 このシリーズの他の記事は次のとおりです。
>
> * [Virtual Network の概要](how-to-network-security-overview.md)
> * [ワークスペース リソースをセキュリティで保護する](how-to-secure-workspace-vnet.md)
> * [トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md)
> * [推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)
> * [スタジオの機能を有効にする](how-to-enable-studio-virtual-network.md)
> * [カスタム DNS を使用する](how-to-custom-dns.md)

## <a name="required-public-internet-access"></a>必要なパブリック インターネット アクセス

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

## <a name="azure-firewall"></a>Azure Firewall

> [!IMPORTANT]
> Azure Firewall では、"_Azure Virtual Network リソース_" にセキュリティが提供されます。 Azure Storage アカウントなど、一部の Azure サービスには、"_その特定のサービス インスタンスのパブリック エンドポイントに適用される_" 独自のファイアウォール設定があります。 このドキュメントの情報は、Azure Firewall に固有のものです。
> 
> サービス インスタンスのファイアウォール設定については、[仮想ネットワークでのスタジオの使用](how-to-enable-studio-virtual-network.md#firewall-settings)に関する記事を参照してください。

* Azure Machine Learning コンピューティング クラスターとコンピューティング インスタンスへの __受信__ トラフィックでは、[ユーザー定義のルート (UDR)](../virtual-network/virtual-networks-udr-overview.md) を使用してファイアウォールをスキップします。

* __送信__ トラフィックの場合は、__ネットワーク__ や __アプリケーション__ の規則を作成します。 

これらの規則コレクションの詳細については、「[Azure Firewall の概念をいくつか教えてください](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)」を参照してください。

### <a name="inbound-configuration"></a>受信の構成

[!INCLUDE [udr info for computes](../../includes/machine-learning-compute-user-defined-routes.md)]

### <a name="outbound-configuration"></a>送信の構成

1. 次のサービス タグ __への__、またサービス タグ __からの__ トラフィックを許可する __ネットワーク規則__ を追加します。

    | サービス タグ | プロトコル | Port |
    | ----- |:-----:|:-----:|
    | AzureActiveDirectory | TCP | * |
    | AzureMachineLearning | TCP | 443 |
    | AzureResourceManager | TCP | 443 |
    | Storage.region       | TCP | 443 |
    | AzureFrontDoor.FrontEnd</br>* Azure China では不要です。 | TCP | 443 | 
    | ContainerRegistry.region  | TCP | 443 |
    | MicrosoftContainerRegistry.region | TCP | 443 |
    | Keyvault.region | TCP | 443 |

    > [!TIP]
    > * ContainerRegistry.region は、カスタム Docker イメージにのみ必要です。 これには、Microsoft が提供する基本イメージへの小さな変更 (追加のパッケージなど) が含まれます。
    > * MicrosoftContainerRegistry.region は、"_Microsoft が提供する既定の Docker イメージ_" を使用し、"_ユーザー マネージドの依存関係を有効にする_" 計画がある場合にのみ必要です。
    > * Keyvault.region は、[hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) フラグを有効にしてワークスペースが作成された場合にのみ必要です。
    > * `region` が含まれているエントリの場合は、使用している Azure リージョンに置き換えます。 たとえば、「 `ContainerRegistry.westus` 」のように入力します。

1. 以下のホストに __アプリケーション規則__ を追加します。

    > [!NOTE]
    > これは、インターネット上のすべての Python リソースに必要なホストの完全な一覧ではなく、最も一般的に使用されているもののみを取り上げています。 たとえば、GitHub リポジトリまたはその他のホストにアクセスする必要がある場合は、そのシナリオに必要なホストを特定して追加する必要があります。

    | **ホスト名** | **目的** |
    | ---- | ---- |
    | **graph.windows.net** | Azure Machine Learning コンピューティング インスタンスまたはクラスターによって使用されます。 |
    | **anaconda.com**</br>**\*.anaconda.com** | 既定のパッケージをインストールするために使用されます。 |
    | **\*.anaconda.org** | リポジトリ データを取得するために使用されます。 |
    | **pypi.org** | 既定のインデックスからの依存関係 (存在する場合) を一覧表示するために使用されます。ユーザー設定によってこのインデックスが上書きされることはありません。 インデックスが上書きされる場合は、 **\*pythonhosted.org** も許可する必要があります。 |
    | **cloud.r-project.org** | R 開発用の CRAN パッケージをインストールするときに使用されます。 |
    | **\*pytorch.org** | PyTorch に基づくいくつかのサンプルによって使用されます。 |
    | **\*.tensorflow.org** | Tensorflow に基づくいくつかのサンプルによって使用されます。 |
    | **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | セットアップ スクリプトを通じてコンピューティング インスタンスにインストールされている VS Code サーバー ビットを取得するために使用されます。|
    | **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** | コンピューティング インスタンスにインストールされている Websocket サーバー ビットを取得するために使用されます。 Websocket サーバーは、Visual Studio Code クライアント (デスクトップ アプリケーション) から、コンピューティング インスタンスで実行されている Visual Studio Code サーバーに要求を送信するために使用されます。|
    | **dc.applicationinsights.azure.com** | Microsoft サポートとの連携時にメトリックおよび診断情報を収集するために使用されます。 |
    | **dc.applicationinsights.microsoft.com** | Microsoft サポートとの連携時にメトリックおよび診断情報を収集するために使用されます。 |
    | **dc.services.visualstudio.com** | Microsoft サポートとの連携時にメトリックおよび診断情報を収集するために使用されます。 | 
    

    __プロトコル/ポート__ には、__http、https__ の使用を選択します。

    アプリケーション規則の構成について詳しくは、[Azure Firewall のデプロイと構成](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)に関する記事をご覧ください。

1. Azure Kubernetes Service (AKS) にデプロイされたモデルへの送信トラフィックを制限するには、[Azure Kubernetes Service でのエグレス トラフィックの制限](../aks/limit-egress-traffic.md)に関するページおよび [Azure Kubernetes Service への ML モデルのデプロイ](how-to-deploy-azure-kubernetes-service.md#connectivity)に関するページを参照してください。

### <a name="azure-kubernetes-services"></a>Azure Kubernetes Services

Azure Machine Learning で Azure Kubernetes Service を使用する場合は、次のトラフィックを許可する必要があります。

* 「[Azure Kubernetes Service (AKS) でエグレス トラフィックを制限する](../aks/limit-egress-traffic.md)」で説明されている AKS の受信または送信の一般的な要件。
* mcr.microsoft.com への __送信__。
* AKS クラスターにモデルをデプロイする場合は、「[ML モデルを Kubernetes Service にデプロイする](how-to-deploy-azure-kubernetes-service.md#connectivity)」記事のガイダンスを使用してください。

## <a name="other-firewalls"></a>その他のファイアウォール

このセクションのガイダンスは一般的なもので、各ファイアウォールには独自の用語や特定の構成があります。 ご質問がある場合は、使用しているファイアウォールのドキュメントを確認してください。

正しく構成しないと、ワークスペースを使用したときにファイアウォールが原因で問題が発生する可能性があります。 いずれも Azure Machine Learning ワークスペースで使用されるさまざまなホスト名があります。 次のセクションでは、Azure Machine Learning に必要なホストの一覧を示します。

### <a name="microsoft-hosts"></a>Microsoft のホスト

次の表のホストは Microsoft によって所有されており、ワークスペースが適切に機能するために必要なサービスが提供されます。 表には、Azure Public、Azure Government、Azure China 21Vianet リージョンのホストが一覧表示されています。

**一般的な Azure ホスト**

| **次のために必須:** | **Azure Public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure portal | management.azure.com | management.azure.us | management.azure.cn |
| Azure Resource Manager | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Azure Machine Learning ホスト**

> [!IMPORTANT]
> 次の表では、`<storage>` を、使用している Azure Machine Learning ワークスペースの既定のストレージ アカウント名に置き換えてください。

| **次のために必須:** | **Azure Public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*.azureml.ms | \*.ml.azure.us | \*.ml.azure.cn |
| 統合されたノートブック | \*.notebooks.azure.net | \*.notebooks.usgovcloudapi.net |\*.notebooks.chinacloudapi.cn |
| 統合されたノートブック | \<storage\>.file.core.windows.net | \<storage\>.file.core.usgovcloudapi.net | \<storage\>.file.core.chinacloudapi.cn |
| 統合されたノートブック | \<storage\>.dfs.core.windows.net | \<storage\>.dfs.core.usgovcloudapi.net | \<storage\>.dfs.core.chinacloudapi.cn |
| 統合されたノートブック | \<storage\>.blob.core.windows.net | \<storage\>.blob.core.usgovcloudapi.net | \<storage\>.blob.core.chinacloudapi.cn |
| 統合されたノートブック | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| 統合されたノートブック | \*.aznbcontent.net |  | |

**Azure Machine Learning コンピューティング インスタンスおよびコンピューティング クラスターのホスト**

| **次のために必須:** | **Azure Public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| コンピューティング クラスター/インスタンス | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| コンピューティング インスタンス | \*.instances.azureml.net | \*.instances.azureml.us | \*.instances.azureml.cn |
| コンピューティング インスタンス | \*.instances.azureml.ms |  |  |
| Azure Storage アカウント | \*.blob.core.windows.net</br>\*.table.core.windows.net</br>\*.queue.core.windows.net | \*.blob.core.usgovcloudapi.net</br>\*.table.core.usgovcloudapi.net</br>\*.queue.core.usgovcloudapi.net | \*blob.core.chinacloudapi.cn</br>\*.table.core.chinacloudapi.cn</br>\*.queue.core.chinacloudapi.cn |
| Azure Key Vault | \*.vault.azure.net | \*.vault.usgovcloudapi.net | \*.vault.azure.cn |

> [!IMPORTANT]
> ファイアウォールでは、__TCP__ ポート __18881、443、8787__ 経由での \*.instances.azureml.ms との通信を許可する必要があります。

> [!TIP]
> Azure Key Vault の FQDN は、[hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) フラグを有効にしてワークスペースが作成された場合にのみ必要です。

**Azure Machine Learning によって管理される Docker イメージ**

| **次のために必須:** | **Azure Public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Microsoft Container Registry | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |
| Azure Machine Learning の事前構築済みイメージ | azurearctest.azurecr.io | azurearctest.azurecr.io | azurearctest.azurecr.io |

> [!TIP]
> * カスタム Docker イメージには __Azure Container Registry__ が必要です。 これには、Microsoft が提供する基本イメージへの小さな変更 (追加のパッケージなど) が含まれます。
> * __Microsoft Container Registry__ は、"_Microsoft が提供する既定の Docker イメージ_" を使用し、"_ユーザー マネージドの依存関係を有効にする_" 計画がある場合にのみ必要です。
> * フェデレーション ID を使用する予定の場合は、「[Active Directory フェデレーション サービス (AD FS) をセキュリティで保護するためのベスト プラクティス](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs)」の記事に従ってください。

また、「[受信の構成](#inbound-configuration)」のセクションの情報を使用して、`BatchNodeManagement` と `AzureMachineLearning` の IP アドレスを追加します。

AKS にデプロイされたモデルへのアクセスの制限については、[Azure Kubernetes Service でのエグレス トラフィックの制限](../aks/limit-egress-traffic.md)に関するページを参照してください。

**診断のサポート**

ワークスペースで発生した問題を Microsoft サポートで診断できるように、次のホストへの送信トラフィックを許可する必要があります。

* **dc.applicationinsights.azure.com**
* **dc.applicationinsights.microsoft.com**
* **dc.services.visualstudio.com**

これらのホストの IP アドレスの一覧については、「[Azure Monitor で使用される IP アドレス](../azure-monitor/app/ip-addresses.md)」を参照してください。

### <a name="python-hosts"></a>Python のホスト

このセクションのホストは、Python パッケージをインストールするために使用され、開発、トレーニング、デプロイ時に必要になります。 

> [!NOTE]
> これは、インターネット上のすべての Python リソースに必要なホストの完全な一覧ではなく、最も一般的に使用されているもののみを取り上げています。 たとえば、GitHub リポジトリまたはその他のホストにアクセスする必要がある場合は、そのシナリオに必要なホストを特定して追加する必要があります。

| **ホスト名** | **目的** |
| ---- | ---- |
| **anaconda.com**</br>**\*.anaconda.com** | 既定のパッケージをインストールするために使用されます。 |
| **\*.anaconda.org** | リポジトリ データを取得するために使用されます。 |
| **pypi.org** | 既定のインデックスからの依存関係 (存在する場合) を一覧表示するために使用されます。ユーザー設定によってこのインデックスが上書きされることはありません。 インデックスが上書きされる場合は、 **\*pythonhosted.org** も許可する必要があります。 |
| **\*pytorch.org** | PyTorch に基づくいくつかのサンプルによって使用されます。 |
| **\*.tensorflow.org** | Tensorflow に基づくいくつかのサンプルによって使用されます。 |

### <a name="r-hosts"></a>R のホスト

このセクションのホストは、R パッケージをインストールするために使用され、開発、トレーニング、デプロイ時に必要になります。

> [!NOTE]
> これは、インターネット上のすべての R リソースに必要なホストの完全な一覧ではなく、最も一般的に使用されているもののみを取り上げています。 たとえば、GitHub リポジトリまたはその他のホストにアクセスする必要がある場合は、そのシナリオに必要なホストを特定して追加する必要があります。

| **ホスト名** | **目的** |
| ---- | ---- |
| **cloud.r-project.org** | CRAN パッケージをインストールするときに使用されます。 |

### <a name="azure-kubernetes-services"></a>Azure Kubernetes Services

Azure Machine Learning で Azure Kubernetes Service を使用する場合は、次のトラフィックを許可する必要があります。

* 「[Azure Kubernetes Service (AKS) でエグレス トラフィックを制限する](../aks/limit-egress-traffic.md)」で説明されている AKS の受信または送信の一般的な要件。
* mcr.microsoft.com への __送信__。
* AKS クラスターにモデルをデプロイする場合は、「[ML モデルを Kubernetes Service にデプロイする](how-to-deploy-azure-kubernetes-service.md#connectivity)」記事のガイダンスを使用してください。

### <a name="visual-studio-code-hosts"></a>Visual Studio Code のホスト

このセクションのホストは、Visual Studio Code のパッケージをインストールして、Visual Studio Code と Azure Machine Learning ワークスペースのコンピューティング インスタンス間のリモート通信を確立するために使用されます。

> [!NOTE]
> これは、インターネット上のすべての Visual Studio Code リソースに必要なホストの完全な一覧ではなく、最も一般的に使用されているもののみを取り上げています。 たとえば、GitHub リポジトリまたはその他のホストにアクセスする必要がある場合は、そのシナリオに必要なホストを特定して追加する必要があります。

| **ホスト名** | **目的** |
| ---- | ---- |
|  **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | セットアップ スクリプトを通じてコンピューティング インスタンスにインストールされている VS Code サーバー ビットを取得するために使用されます。|
| **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** |コンピューティング インスタンスにインストールされている Websocket サーバー ビットを取得するために使用されます。 Websocket サーバーは、Visual Studio Code クライアント (デスクトップ アプリケーション) から、コンピューティング インスタンスで実行されている Visual Studio Code サーバーに要求を送信するために使用されます。 |

## <a name="next-steps"></a>次のステップ

この記事は、Azure Machine Learning ワークフローのセキュリティ保護に関するシリーズの一部です。 このシリーズの他の記事は次のとおりです。

* [Virtual Network の概要](how-to-network-security-overview.md)
* [ワークスペース リソースをセキュリティで保護する](how-to-secure-workspace-vnet.md)
* [トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md)
* [推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)
* [スタジオの機能を有効にする](how-to-enable-studio-virtual-network.md)
* [カスタム DNS を使用する](how-to-custom-dns.md)

Azure Firewall の構成の詳細については、[Azure portal を使用した Azure Firewall のデプロイと構成に関するチュートリアル](../firewall/tutorial-firewall-deploy-portal.md)を参照してください。
