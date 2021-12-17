---
title: 仮想ネットワークの分離とプライバシーの概要
titleSuffix: Azure Machine Learning
description: 独立した Azure Virtual Network (VNet) を使用して、Azure Machine Learning ワークスペース リソースとコンピューティング環境を保護します。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/29/2021
ms.topic: how-to
ms.custom: devx-track-python, references_regions, contperf-fy21q1,contperf-fy21q4,FY21Q4-aml-seo-hack, security
ms.openlocfilehash: 47b3789938035dab9c9094fbc5aba0aa1fc8d8e4
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723364"
---
<!-- # Virtual network isolation and privacy overview -->
# <a name="secure-azure-machine-learning-workspace-resources-using-virtual-networks-vnets"></a>仮想ネットワーク (VNet) を使用して Azure Machine Learning ワークスペース リソースを保護する

仮想ネットワーク (VNet) を使用して Azure Machine Learning ワークスペース リソースとコンピューティング環境を保護します。 この記事では、完全な仮想ネットワークを構成する方法を示すシナリオ例を使用します。

> [!TIP]
> この記事は、Azure Machine Learning ワークフローのセキュリティ保護に関するシリーズの一部です。 このシリーズの他の記事は次のとおりです。
>
> * [ワークスペース リソースをセキュリティで保護する](how-to-secure-workspace-vnet.md)
> * [トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md)
> * [推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)
> * [スタジオの機能を有効にする](how-to-enable-studio-virtual-network.md)
> * [カスタム DNS を使用する](how-to-custom-dns.md)
> * [ファイアウォールを使用する](how-to-access-azureml-behind-firewall.md)

## <a name="prerequisites"></a>前提条件

この記事では、次のトピックについて理解していることを前提としています。
+ [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)
+ [IP ネットワーク](../virtual-network/ip-services/public-ip-addresses.md)
+ [プライベート エンドポイントを持つ Azure Machine Learning ワークスペース](how-to-configure-private-link.md)
+ [ネットワーク セキュリティ グループ (NSG)](../virtual-network/network-security-groups-overview.md)
+ [ネットワーク ファイアウォール](../firewall/overview.md)
## <a name="example-scenario"></a>シナリオ例

このセクションでは、プライベート IP アドレスを使用して Azure Machine Learning の通信をセキュリティで保護するために、一般的なネットワーク シナリオがどのように設定されるかについて説明します。

次の表は、VNet がある場合とない場合で、サービスが Azure Machine Learning ネットワークのさまざまな部分にアクセスする方法を比較したものです。

| シナリオ | ワークスペース | 関連付けられているリソース | トレーニングのコンピューティング環境 | 推論のコンピューティング環境 |
|-|-|-|-|-|-|
|**仮想ネットワークなし**| パブリック IP | パブリック IP | パブリック IP | パブリック IP |
|**パブリックワークスペース、仮想ネットワーク内の他のすべてのリソース** | パブリック IP | パブリック IP (サービス エンドポイント) <br> **または** <br> プライベート IP (プライベート エンドポイント) | プライベート IP | プライベート IP  |
|**仮想ネットワーク内のリソースをセキュリティで保護**| プライベート IP (プライベート エンドポイント) | パブリック IP (サービス エンドポイント) <br> **または** <br> プライベート IP (プライベート エンドポイント) | プライベート IP | プライベート IP  | 

* **ワークスペース** - ワークスペースのプライベート エンドポイントを作成します。 プライベート エンドポイントは、複数のプライベート IP アドレスを使用してワークスペースを VNet に接続します。
    * **パブリックアクセス** -必要に応じて、セキュリティで保護されたワークスペースのパブリックアクセスを有効にすることができます。
* **関連するリソース** - サービス エンドポイントまたはプライベート エンドポイントを使用して、Azure storage、Azure Key Vault などのワークスペース リソースに接続します。 Azure Container Services の場合は、プライベート エンドポイントを使用します。
    * **サービス エンドポイント** は、仮想ネットワークの ID を Azure サービスに提供します。 ご利用の仮想ネットワークでサービス エンドポイントを有効にしたら、仮想ネットワーク規則を追加して、Azure サービス リソースへのアクセスを仮想ネットワークに限定することができます。 サービス エンドポイントは、パブリック IP アドレスを使用します。
    * **プライベート エンドポイント** は、Azure Private Link を使用するサービスに安全に接続するネットワーク インターフェイスです。 プライベート エンドポイントでは、自分の VNet からのプライベート IP アドレスを使用して、サービスを実質的に VNet に取り込みます。
* **トレーニングのコンピューティング アクセス** - パブリック IP アドレス (プレビュー) を使用して、Azure Machine Learning コンピューティング インスタンスや Azure Machine Learning コンピューティング クラスターのようなトレーニングのコンピューティング ターゲットに安全に接続します。
* **推論のコンピューティング アクセス** - プライベート IP アドレスを使用して、Azure Kubernetes Services (AKS) コンピューティング クラスターにアクセスします。


次のセクションでは、前述のネットワーク シナリオをセキュリティで保護する方法について説明します。 ネットワークをセキュリティで保護するには、次のことを行う必要があります。

1. [**ワークスペースと関連するリソース**](#secure-the-workspace-and-associated-resources)をセキュリティで保護します。
1. [**トレーニング環境**](#secure-the-training-environment)をセキュリティで保護します。
1. [**推論環境**](#secure-the-inferencing-environment)をセキュリティで保護します。
1. 必要に応じて、[**Studio の機能を有効にします**](#optional-enable-studio-functionality)。
1. [**ファイアウォール設定**](#configure-firewall-settings)を構成します。
1. [**DNS 名前解決**](#custom-dns)を構成します。

## <a name="public-workspace-and-secured-resources"></a>パブリックワークスペースとセキュリティで保護されたリソース

仮想ネットワークですべての関連リソースを保護したまま、パブリックインターネット経由でワークスペースにアクセスする場合は、次の手順を実行します。

1. ワークスペースによって使用されるリソースを含む [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)を作成します。
1. パブリックにアクセス可能なワークスペースを作成するには、次の __いずれか__ のオプションを使用します。

    * 仮想ネットワークを使用しない __Azure Machine Learning ワークスペース__ を作成します。 詳細については、[Azure Machine Learning ワークスペースの管理](how-to-manage-workspace.md)に関するページを参照してください。
    * [Private Link が有効なワークスペース](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint)を作成し、VNet とワークスペース間の通信を有効にします。 [ワークスペースへのパブリック アクセスを有効にします。](#optional-enable-public-access)

1. __サービス エンドポイント__ または __プライベート エンドポイント__ の "_どちらか_" を使用して、次のサービスを仮想ネットワークに追加します。 また、信頼された Microsoft サービスによるこれらのサービスへのアクセスを許可します。

    | サービス | エンドポイント情報 | 信頼できる情報を許可する |
    | ----- | ----- | ----- |
    | __Azure Key Vault__| [サービス エンドポイント](../key-vault/general/overview-vnet-service-endpoints.md)</br>[プライベート エンドポイント](../key-vault/general/private-link-service.md) | [信頼された Microsoft サービスを許可して、このファイアウォールをバイパスする](how-to-secure-workspace-vnet.md#secure-azure-key-vault) |
    | __Azure Storage アカウント__ | [サービスとプライベート エンドポイント](how-to-secure-workspace-vnet.md?tabs=se#secure-azure-storage-accounts)</br>[プライベート エンドポイント](how-to-secure-workspace-vnet.md?tabs=pe#secure-azure-storage-accounts) | [信頼された Azure サービスにアクセスを許可する](../storage/common/storage-network-security.md#grant-access-to-trusted-azure-services) |
    | __Azure Container Registry__ | [プライベート エンドポイント](../container-registry/container-registry-private-link.md) | [信頼されたサービスを許可する](../container-registry/allow-access-trusted-services.md) |

1. ワークスペースの Azure Storage アカウントのプロパティで、ファイアウォール設定の許可一覧にクライアント IP アドレスを追加します。 詳細については、[ファイアウォールと仮想ネットワークの構成](../storage/common/storage-network-security.md#configuring-access-from-on-premises-networks)に関する記事を参照してください。

## <a name="secure-the-workspace-and-associated-resources"></a>ワークスペースと関連するリソースをセキュリティで保護する

ワークスペースと関連するリソースをセキュリティで保護するには、次の手順に従います。 これらの手順により、サービスが仮想ネットワークで通信できるようになります。

1. ワークスペースとその他のリソースを含む [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)を作成します。
1. [Private Link が有効なワークスペース](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint)を作成し、VNet とワークスペース間の通信を有効にします。
1. __サービス エンドポイント__ または __プライベート エンドポイント__ の "_どちらか_" を使用して、次のサービスを仮想ネットワークに追加します。 また、信頼された Microsoft サービスによるこれらのサービスへのアクセスを許可します。

    | サービス | エンドポイント情報 | 信頼できる情報を許可する |
    | ----- | ----- | ----- |
    | __Azure Key Vault__| [サービス エンドポイント](../key-vault/general/overview-vnet-service-endpoints.md)</br>[プライベート エンドポイント](../key-vault/general/private-link-service.md) | [信頼された Microsoft サービスを許可して、このファイアウォールをバイパスする](how-to-secure-workspace-vnet.md#secure-azure-key-vault) |
    | __Azure Storage アカウント__ | [サービスとプライベート エンドポイント](how-to-secure-workspace-vnet.md?tabs=se#secure-azure-storage-accounts)</br>[プライベート エンドポイント](how-to-secure-workspace-vnet.md?tabs=pe#secure-azure-storage-accounts) | [Azure リソース インスタンスからのアクセスを許可する](../storage/common/storage-network-security.md#grant-access-from-azure-resource-instances-preview)</br>**or**</br>[信頼された Azure サービスにアクセスを許可する](../storage/common/storage-network-security.md#grant-access-to-trusted-azure-services) |
    | __Azure Container Registry__ | [プライベート エンドポイント](../container-registry/container-registry-private-link.md) | [信頼されたサービスを許可する](../container-registry/allow-access-trusted-services.md) |


![ワークスペースと関連するリソースが、VNet 内のサービス エンドポイントまたはプライベート エンドポイントを介して相互に通信する方法を示すアーキテクチャ図](./media/how-to-network-security-overview/secure-workspace-resources.png)

これらの手順を完了する方法の詳細については、[Azure Machine Learning ワークスペースのセキュリティ保護](how-to-secure-workspace-vnet.md)に関するページを参照してください。 

### <a name="limitations"></a>制限事項

仮想ネットワーク内のワークスペースと関連するリソースをセキュリティで保護するには、次の制限があります。
- すべてのリソースは同じ VNet の背後に配置する必要があります。 ただし、同じ VNet 内の複数のサブネットは許容されます。

## <a name="secure-the-training-environment"></a>トレーニング環境をセキュリティで保護する

このセクションでは、Azure Machine Learning でトレーニング環境をセキュリティで保護する方法について説明します。 また、Azure Machine Learning でトレーニング ジョブがどのように完了するかについても説明するので、ネットワーク構成がどのように連携するかを理解できます。

トレーニング環境をセキュリティで保護するには、次の手順に従います。

1. トレーニング ジョブを実行するために、Azure Machine Learning [コンピューティング インスタンスとコンピューター クラスターを仮想ネットワークに](how-to-secure-training-vnet.md#compute-cluster)作成します。
1. 管理サービスがコンピューティング リソースにジョブを送信できるように、[受信通信を許可](how-to-secure-training-vnet.md#required-public-internet-access)します。 

![マネージド コンピューティング クラスターとインスタンスをセキュリティで保護する方法を示すアーキテクチャ図](./media/how-to-network-security-overview/secure-training-environment.png)

これらの手順を完了する方法の詳細については、[トレーニング環境のセキュリティ保護](how-to-secure-training-vnet.md)に関するページを参照してください。 

### <a name="example-training-job-submission"></a>トレーニング ジョブの送信例 

このセクションでは、トレーニング ジョブを送信するために、Azure Machine Learning によってサービス間でどのように通信が安全に行われるかについて説明します。 これは、すべての構成が連携して通信をセキュリティで保護するしくみを示しています。

1. クライアントは、サービス エンドポイントまたはプライベート エンドポイントで保護されているストレージ アカウントに、トレーニング スクリプトとトレーニング データをアップロードします。

1. プライベート エンドポイントを使用して、クライアントから Azure Machine Learning ワークスペースにトレーニング ジョブが送信されます。

1. Azure Batch サービスは、ワークスペースからジョブを受信します。 そして、コンピューティング リソースのパブリック ロード バランサーを介して、トレーニング ジョブをコンピューティング環境に送信します。 

1. コンピューティング リソースはジョブを受信し、トレーニングが開始されます。 コンピューティング リソースは、セキュリティで保護されたストレージ アカウントにアクセスしてトレーニング ファイルをダウンロードし、出力をアップロードします。

### <a name="limitations"></a>制限事項

- Azure コンピューティング インスタンスと Azure コンピューティング クラスターは、ワークスペースおよび関連するリソースと同じ VNet、リージョン、およびサブスクリプションにある必要があります。 

## <a name="secure-the-inferencing-environment"></a>推論環境をセキュリティで保護する

このセクションでは、推論環境をセキュリティで保護するために使用できるオプションについて説明します。 大規模な運用環境のデプロイには、Azure Kubernetes Services (AKS) クラスターを使用することをお勧めします。

仮想ネットワークでの AKS クラスターには、次の 2 つのオプションがあります。

- 既定の AKS クラスターを VNet にデプロイまたはアタッチします。
- プライベート AKS クラスターを VNet にアタッチします。

**既定の AKS クラスター** には、パブリック IP アドレスを持つコントロール プレーンがあります。 デプロイ時に既定の AKS クラスターを VNet に追加することもできますし、クラスターの作成後にそれをアタッチすることもできます。

**プライベート AKS クラスター** には、プライベート IP を介してのみアクセスできるコントロール プレーンがあります。 プライベート AKS クラスターは、クラスターの作成後にアタッチする必要があります。

既定のクラスターおよびプライベート クラスターを追加する方法の詳細については、[推論環境のセキュリティ保護](how-to-secure-inferencing-vnet.md)に関するページを参照してください。 

次のネットワーク図は、仮想ネットワークにアタッチされたプライベート AKS クラスターのある、セキュリティで保護された Azure Machine Learning ワークスペースを示しています。

![プライベート AKS クラスターを仮想ネットワークにアタッチする方法を示すアーキテクチャ図。 AKS コントロール プレーンは顧客 VNet の外側に配置されます](./media/how-to-network-security-overview/secure-inferencing-environment.png)

### <a name="limitations"></a>制限事項

- ワークスペースには、AKS クラスターと同じ VNet 内にプライベートエンドポイントが必要です。 たとえば、ワークスペースで複数のプライベートエンドポイントを使用する場合、1つのプライベートエンドポイントを AKS VNet に配置し、そのワークスペースの依存サービスを含む VNet に別のエンドポイントを配置できます。

## <a name="optional-enable-public-access"></a>省略可能:パブリック アクセスを有効にする

プライベート エンドポイントを使用して VNet の背後にあるワークスペースをセキュリティで保護しながら、パブリック インターネット経由のアクセスを許可することができます。 初期構成は、[ワークスペースと関連するリソースをセキュリティで保護する](#secure-the-workspace-and-associated-resources)場合と同じです。 

プライベートエンドポイントを使用してワークスペースをセキュリティで保護した後、次の手順を使用して、SDK または Azure Machine Learning studio を使用してクライアントをリモートで開発できるようにします。

1. ワークスペースへの[パブリック アクセスを有効にします。](how-to-configure-private-link.md#enable-public-access)
1. パブリックインターネット経由で接続するクライアントの IP アドレスとの通信を許可するように[Azure Storage ファイアウォールを構成](../storage/common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#grant-access-from-an-internet-ip-range)します。

## <a name="optional-enable-studio-functionality"></a>省略可能: Studio の機能を有効にする

[ワークスペースをセキュリティで保護する](#secure-the-workspace-and-associated-resources) > [トレーニング環境をセキュリティで保護する](#secure-the-training-environment) > [推論環境をセキュリティで保護する](#secure-the-inferencing-environment) > **Studio の機能を有効にする** > [ファイアウォール設定を構成する](#configure-firewall-settings)

ストレージが VNet 内にある場合、スタジオで完全な機能を有効にするためには、追加の構成手順を使用する必要があります。 既定では、次の機能が無効になっています。

* スタジオでのデータのプレビュー
* デザイナーでのデータの視覚化
* デザイナーでのモデルのデプロイ
* AutoML 実験の送信
* ラベル付けプロジェクトの開始

すべてのスタジオ機能を有効にするには、[仮想ネットワークで Azure Machine Learning スタジオを使用する](how-to-enable-studio-virtual-network.md)を参照してください。

### <a name="limitations"></a>制限事項

[ML によるデータのラベル付け](how-to-create-image-labeling-projects.md#use-ml-assisted-data-labeling)は、仮想ネットワークの背後にある既定のストレージ アカウントをサポートしていません。 代わりに、既定以外のストレージ アカウントを使用して、ML によるデータのラベル付けを行います。 

> [!TIP]
> 既定のストレージ アカウントでない限り、データのラベル付けに使用されるアカウントは、仮想ネットワークの背後で保護できます。 

## <a name="configure-firewall-settings"></a>ファイアウォール設定を構成する

ファイアウォールを構成して、Azure Machine Learning ワークスペース リソースとパブリック インターネット間のトラフィックを制御します。 Azure Firewall を推奨しますが、他のファイアウォール製品を使用することもできます。 

ファイアウォール設定の詳細については、[ファイアウォールの内側でのワークスペースの使用](how-to-access-azureml-behind-firewall.md)に関するページを参照してください。

## <a name="custom-dns"></a>[カスタム DNS]

仮想ネットワークにカスタム DNS ソリューションを使用する必要がある場合は、ワークスペースのホスト レコードを追加する必要があります。

必要なドメイン名と IP アドレスの詳細については、「[カスタム DNS サーバーでワークスペースを使用する方法](how-to-custom-dns.md)」を参照してください。

## <a name="next-steps"></a>次の手順

この記事は、Azure Machine Learning ワークフローのセキュリティ保護に関するシリーズの一部です。 このシリーズの他の記事は次のとおりです。

* [ワークスペース リソースをセキュリティで保護する](how-to-secure-workspace-vnet.md)
* [トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md)
* [推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)
* [スタジオの機能を有効にする](how-to-enable-studio-virtual-network.md)
* [カスタム DNS を使用する](how-to-custom-dns.md)
* [ファイアウォールを使用する](how-to-access-azureml-behind-firewall.md)