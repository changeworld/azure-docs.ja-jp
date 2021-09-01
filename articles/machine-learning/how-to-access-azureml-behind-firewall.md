---
title: ファイアウォールを使用する
titleSuffix: Azure Machine Learning
description: Azure Firewall を使用して Azure Machine Learning ワークスペースへのアクセスを制御します。 ファイアウォールの通過を許可する必要があるホストについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 08/12/2021
ms.custom: devx-track-python
ms.openlocfilehash: 790b5a3e34d36d674511507bc5e9ed452c5ba74e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745302"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>ファイアウォールの内側で Azure Machine Learning のワークスペースを使用する

この記事では、Azure Firewall を構成して、Azure Machine Learning ワークスペースとパブリック インターネットへのアクセスを制御する方法について説明します。 Azure Machine Learning のセキュリティ保護の詳細については、[Azure Machine Learning のエンタープライズ セキュリティ](concept-enterprise-security.md)に関するページを参照してください。

> [!NOTE]
> この記事の情報は、プライベート エンドポイントとサービス エンドポイントのどちらを使用するかにかかわらず、Azure Machine Learning ワークスペースに適用されます。

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

    > [!TIP]
    > * ContainerRegistry.region は、カスタム Docker イメージにのみ必要です。 これには、Microsoft が提供する基本イメージへの小さな変更 (追加のパッケージなど) が含まれます。
    > * MicrosoftContainerRegistry.region は、"_Microsoft が提供する既定の Docker イメージ_" を使用し、"_ユーザー マネージドの依存関係を有効にする_" 計画がある場合にのみ必要です。
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
    

    __プロトコル/ポート__ には、__http、https__ の使用を選択します。

    アプリケーション規則の構成について詳しくは、[Azure Firewall のデプロイと構成](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)に関する記事をご覧ください。

1. Azure Kubernetes Service (AKS) にデプロイされたモデルへの送信トラフィックを制限するには、[Azure Kubernetes Service でのエグレス トラフィックの制限](../aks/limit-egress-traffic.md)に関するページおよび [Azure Kubernetes Service への ML モデルのデプロイ](how-to-deploy-azure-kubernetes-service.md#connectivity)に関するページを参照してください。

### <a name="diagnostics-for-support"></a>サポート用の診断

Microsoft サポートを使用しているときに診断情報を収集する必要がある場合は、次の手順を使用します。

1. `AzureMonitor` タグとの間のトラフィックを許可する __ネットワーク規則__ を追加します。
1. 次のホストに __アプリケーション規則__ を追加します。 これらのホストの __Protocol:Port__ には、__http、https__ を選択します。

    + **dc.applicationinsights.azure.com**
    + **dc.applicationinsights.microsoft.com**
    + **dc.services.visualstudio.com**

    Azure Monitor ホストの IP アドレスの一覧については、「[Azure Monitor で使用される IP アドレス](../azure-monitor/app/ip-addresses.md)」を参照してください。
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
| コンピューティング クラスター/インスタンス | \*.batchai.core.windows.net | \*.batchai.core.usgovcloudapi.net |\*.batchai.ml.azure.cn |
| コンピューティング クラスター/インスタンス | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| コンピューティング インスタンス | \*.instances.azureml.net | \*.instances.azureml.us | \*.instances.azureml.cn |
| コンピューティング インスタンス | \*.instances.azureml.ms |  |  |

> [!IMPORTANT]
> ファイアウォールでは、__TCP__ ポート __18881、443、8787__ 経由での \*.instances.azureml.ms との通信を許可する必要があります。

**Azure Machine Learning によって使用される関連リソース**

| **次のために必須:** | **Azure Public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Storage アカウント | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Azure Container Registry | azurecr.io | azurecr.us | azurecr.cn |
| Microsoft Container Registry | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |
| Azure Machine Learning の事前構築済みイメージ | azurearctest.azurecr.io | azurearctest.azurecr.io | azurearctest.azurecr.io |

> [!TIP]
> * カスタム Docker イメージには __Azure Container Registry__ が必要です。 これには、Microsoft が提供する基本イメージへの小さな変更 (追加のパッケージなど) が含まれます。
> * __Microsoft Container Registry__ は、"_Microsoft が提供する既定の Docker イメージ_" を使用し、"_ユーザー マネージドの依存関係を有効にする_" 計画がある場合にのみ必要です。
> * フェデレーション ID を使用する予定の場合は、「[Active Directory フェデレーション サービス (AD FS) をセキュリティで保護するためのベスト プラクティス](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs)」の記事に従ってください。

また、「[受信の構成](#inbound-configuration)」のセクションの情報を使用して、`BatchNodeManagement` と `AzureMachineLearning` の IP アドレスを追加します。

AKS にデプロイされたモデルへのアクセスの制限については、[Azure Kubernetes Service でのエグレス トラフィックの制限](../aks/limit-egress-traffic.md)に関するページを参照してください。

> [!TIP]
> Microsoft サポートと協力して診断情報を収集する場合は、Azure Monitor ホストで使用される IP アドレスへの送信トラフィックを許可する必要があります。 Azure Monitor ホストの IP アドレスの一覧については、「[Azure Monitor で使用される IP アドレス](../azure-monitor/app/ip-addresses.md)」を参照してください。

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

### <a name="azure-kubernetes-services-hosts"></a>Azure Kubernetes Service のホスト

AKS が通信する必要があるホストの詳細については、[Azure Kubernetes Service でのエグレス トラフィックの制限](../aks/limit-egress-traffic.md)に関するページおよび [Azure Kubernetes Service への ML モデルのデプロイ](how-to-deploy-azure-kubernetes-service.md#connectivity)に関するページを参照してください。

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
