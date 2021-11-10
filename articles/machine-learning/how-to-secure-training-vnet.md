---
title: 仮想ネットワークを使用してトレーニング環境をセキュリティで保護する
titleSuffix: Azure Machine Learning
description: 分離された Azure Virtual Network を使用して、Azure Machine Learning トレーニング環境をセキュリティで保護します。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/05/2021
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, references_regions
ms.openlocfilehash: ec3a5feefa41a1bd0bb0448321fc87f7e11a55d9
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136281"
---
# <a name="secure-an-azure-machine-learning-training-environment-with-virtual-networks"></a>仮想ネットワークを使用して Azure Machine Learning トレーニング環境をセキュリティで保護する

この記事では、Azure Machine Learning で仮想ネットワークを使用して、トレーニング環境をセキュリティで保護する方法について説明します。

> [!TIP]
> この記事は、Azure Machine Learning ワークフローのセキュリティ保護に関するシリーズの一部です。 このシリーズの他の記事は次のとおりです。
>
> * [Virtual Network の概要](how-to-network-security-overview.md)
> * [ワークスペース リソースをセキュリティで保護する](how-to-secure-workspace-vnet.md)
> * [推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)
> * [スタジオの機能を有効にする](how-to-enable-studio-virtual-network.md)
> * [カスタム DNS を使用する](how-to-custom-dns.md)
> * [ファイアウォールを使用する](how-to-access-azureml-behind-firewall.md)
>
> セキュリティで保護されたワークスペース、コンピューティング クラスター、コンピューティング インスタンスの作成に関するチュートリアルについては、「<bpt id="p1">[</bpt>チュートリアル: セキュリティで保護されたワークスペースを作成する<ept id="p1">](tutorial-create-secure-workspace.md)</ept>」を参照してください。

この記事では、仮想ネットワークで次のトレーニング コンピューティング リソースをセキュリティで保護する方法について説明します。
> [!div class="checklist"]
> - Azure Machine Learning コンピューティング クラスター
> - Azure Machine Learning コンピューティング インスタンス
> - Azure Databricks
> - 仮想マシン
> - HDInsight クラスター

## <a name="prerequisites"></a>前提条件

+ 一般的な仮想ネットワークのシナリオと全体的な仮想ネットワーク アーキテクチャについては、[ネットワーク セキュリティの概要](how-to-network-security-overview.md)に関するページを参照してください。

+ コンピューティング リソースで使用する既存の仮想ネットワークとサブネット。

+ リソースを仮想ネットワークまたはサブネットにデプロイするには、ご利用のユーザー アカウントが、Azure ロールベースのアクセス制御 (Azure RBAC) で次のアクションへのアクセス許可を保持している必要があります。

    - 仮想ネットワーク リソース上の "Microsoft.Network/virtualNetworks/*/read"。 これは、Azure Resource Manager (ARM) テンプレートのデプロイには必要ありません
    - サブネット リソース上の "Microsoft.Network/virtualNetworks/subnet/join/action"。

    ネットワークでの Azure RBAC の詳細については、[ネットワークの組み込みロール](../role-based-access-control/built-in-roles.md#networking)に関するページを参照してください

### <a name="azure-machine-learning-compute-clusterinstance"></a>Azure Machine Learning コンピューティング クラスター/インスタンス

* 仮想ネットワークは Azure Machine Learning ワークスペースと同じサブスクリプションにある必要があります。
* コンピューティング インスタンスまたはクラスターに使用されるサブネットには、割り当てられていない十分な IP アドレスが必要です。

    * コンピューティング クラスターは動的にスケーリングできます。 未割り当ての IP アドレスが十分でない場合、クラスターは部分的に割り当てられます。
    * コンピューティング インスタンスに必要な IP アドレスは 1 つのみです。

* [パブリック IP アドレスを持たない](#no-public-ip)コンピューティング クラスターまたはインスタンス (プレビュー機能) を作成するには、ワークスペースから VNet に接続するためにプライベート エンドポイントを使用する必要があります。 詳細については、「[Azure Machine Learning ワークスペース用にプライベート エンドポイントを構成する](how-to-configure-private-link.md)」を参照してください。
* 仮想ネットワークを管理するためのアクセス許可を制限するセキュリティ ポリシーまたはロックがないことを確認します。 ポリシーまたはロックを確認する場合は、仮想ネットワークのサブスクリプションとリソース グループの両方を確認します。
* 仮想ネットワークのサブスクリプションまたはリソース グループに対するセキュリティ ポリシーまたはロックで、仮想ネットワークを管理するためのアクセス許可が制限されているかどうかを確認します。 
* トラフィックを制限することで仮想ネットワークのセキュリティを保護する計画の場合は、「<bpt id="p1">[</bpt>必要なパブリック インターネット アクセス<ept id="p1">](#required-public-internet-access)</ept>」を参照してください。
* コンピューティング クラスターまたはインスタンスをデプロイするために使用されるサブネットを他のサービスには委任しないでください。 たとえば、ACI に委任しないでください。

### <a name="azure-databricks"></a>Azure Databricks

* 仮想ネットワークは Azure Machine Learning のワークスペースと同じサブスクリプションとリージョンになければなりません。
* 仮想ネットワークでワークスペースの Azure Storage アカウントもセキュリティで保護される場合、それらは Azure Databricks クラスターと同じ仮想ネットワークに存在する必要があります。

## <a name="limitations"></a>制限事項

### <a name="azure-machine-learning-compute-clusterinstance"></a>Azure Machine Learning コンピューティング クラスター/インスタンス

* 複数のコンピューティング インスタンスまたはクラスターを 1 つの仮想ネットワークに配置する場合は、1 つまたは複数のリソースのクォータの増加を要求する必要がある場合があります。 Machine Learning コンピューティング インスタンスまたはクラスターにより、<bpt id="p1">__</bpt>仮想ネットワークが含まれているリソース グループ<ept id="p1">__</ept>に追加のネットワーク リソースが自動的に割り当てられます。 サービスにより、各コンピューティング インスタンスまたはクラスターについて次のリソースが割り当てられます。

    * 1 つのネットワーク セキュリティ グループ (NSG)。 この NSG には、コンピューティング クラスターとコンピューティング インスタンスに固有の次のルールが含まれています。

        * `BatchNodeManagement` サービス タグからのポート 29876 から 29877 での受信 TCP トラフィックを許可します。
        * `AzureMachineLearning` サービス タグからのポート 44224 での受信 TCP トラフィックを許可します。

        次のスクリーンショットは、これらのルールの例を示しています。

        <bpt id="p1">:::image type="content" source="./media/how-to-secure-training-vnet/compute-instance-cluster-network-security-group.png" alt-text="</bpt>NSG のスクリーンショット<ept id=&quot;p1&quot;>":::</ept>


        > [!TIP]
        > コンピューティング クラスターまたはインスタンスにパブリック IP アドレス (プレビュー機能) が使用されていない場合、これらの受信 NSG 規則は必要ありません。 
        
    * コンピューティング クラスターまたはインスタンスの場合、パブリック IP アドレスを削除できるようになりました (プレビュー機能)。 Azure Policy の割り当てによってパブリック IP の作成が禁止されている場合、コンピューティング クラスターまたはインスタンスのデプロイが成功します。

    * 1 つのロード バランサー

    コンピューティング クラスターでは、クラスターが 0 ノードにスケールダウンするたびにこれらのリソースが削除され、スケールアップすると作成されます。

    コンピューティング インスタンスでは、これらのリソースはインスタンスが削除されるまで保持されます。 インスタンスを停止しても、リソースは削除されません。 

    > [!IMPORTANT]
    > これらのリソースは、サブスクリプションの<bpt id="p1">[</bpt>リソース クォータ<ept id="p1">](../azure-resource-manager/management/azure-subscription-service-limits.md)</ept>によって制限されます。 仮想ネットワーク リソース グループがロックされている場合、コンピューティング クラスターやインスタンスの削除が失敗します。 コンピューティング クラスターやインスタンスが削除されるまで、ロード バランサーを削除することはできません。 また、ネットワーク セキュリティ グループの作成を禁止する Azure Policy の割り当てがないことも確認してください。

* ワークスペースの Azure Storage アカウントも仮想ネットワーク内にある場合は、サブネットの制限事項に関する次のガイダンスを使用します。

    * Azure Machine Learning <bpt id="p1">__</bpt>スタジオ<ept id="p1">__</ept>を使用してデータを視覚化する計画の場合、またはデザイナーを使用する計画の場合、ストレージ アカウントは<bpt id="p2">__</bpt>コンピューティング インスタンスまたはクラスターと同じサブネット内<ept id="p2">__</ept>にある必要があります。
    * <bpt id="p1">__</bpt>SDK<ept id="p1">__</ept> を使用する計画の場合は、ストレージ アカウントが別のサブネットに含まれていてもかまいません。

    > [!NOTE]
    > ワークスペースのリソース インスタンスを追加するか、[信頼された Microsoft サービスによるこのアカウントに対するアクセスを許可します] チェック ボックスをオンにするだけでは、コンピューティングからの通信の許可には不十分です。

* ワークスペースでプライベート エンドポイントを使用する場合、コンピューティング インスタンスには仮想ネットワーク内からのみアクセスできます。 カスタム DNS または hosts ファイルを使用する場合は、<ph id="ph1">`<instance-name>.<region>.instances.azureml.ms`</ph> のエントリを追加します。 このエントリを、ワークスペースのプライベート エンドポイントのプライベート IP アドレスにマップします。 詳細については、<bpt id="p1">[</bpt>カスタム DNS<ept id="p1">](./how-to-custom-dns.md)</ept> に関するページを参照してください。
* 仮想ネットワーク サービス エンドポイント ポリシーは、コンピューティング クラスターやインスタンスのシステム ストレージ アカウントに対して機能しません。
* ストレージ インスタンスとコンピューティング インスタンスが異なるリージョンに存在する場合、断続的にタイムアウトが発生することがあります。
* ワークスペースの Azure Container Registry から仮想ネットワークへの接続にプライベート エンドポイントが使用されている場合、コンピューティング インスタンスにマネージド ID を使用することはできません。 コンピューティング インスタンスでマネージド ID を使用するには、コンテナー レジストリを VNet にデプロイしないでください。
* コンピューティング インスタンスで Jupyter Notebook を使用する場合:

    * WebSocket 通信を無効にしないでください。 ネットワークで <ph id="ph1">`*.instances.azureml.net`</ph> と <ph id="ph2">`*.instances.azureml.ms`</ph> との WebSocket 通信が許可されている必要があります。
    * データと同じ仮想ネットワークおよびサブネットの背後にあるコンピューティング リソースでノートブックが実行されていることを確認する必要があります。 コンピューティング インスタンスを作成する場合は、 <bpt id="p1">**</bpt>[詳細設定]<ept id="p1">**</ept> <ph id="ph1"> > </ph> <bpt id="p2">**</bpt>[仮想ネットワークの構成]<ept id="p2">**</ept> を使用して、ネットワークとサブネットを選択します。

* <bpt id="p1">__</bpt>コンピューティング クラスター<ept id="p1">__</ept>は、ワークスペースとは別のリージョンに作成できます。 この機能は<bpt id="p1">__</bpt>プレビュー<ept id="p1">__</ept>段階であり、<bpt id="p2">__</bpt>コンピューティング クラスター<ept id="p2">__</ept>でのみ利用できます。コンピューティング インスタンスでは利用できません。 クラスターに別のリージョンを使用する場合は、次の制限が適用されます。

    * ワークスペースに関連付けられているリソース (ストレージなど) がクラスターとは異なる仮想ネットワークにある場合は、ネットワーク間のグローバル仮想ネットワーク ピアリングを設定します。 詳細については、「<bpt id="p1">[</bpt>仮想ネットワーク ピアリング<ept id="p1">](../virtual-network/virtual-network-peering-overview.md)</ept>」をご覧ください。
    * ネットワーク待機時間とデータ転送コストが増加する可能性があります。 待機時間とコストは、クラスターの作成時や、クラスターでのジョブの実行時に発生する可能性があります。

    ワークスペースとは異なるリージョンを使用する場合、NSG ルール、ユーザー定義ルート、入力や出力要件の使用などのガイダンスは通常通り適用されます。

    > [!WARNING]
    > __プライベート エンドポイントが有効なワークスペース__ を使用している場合、別のリージョンでのクラスターの作成は __サポートされていません__。

### <a name="azure-databricks"></a>Azure Databricks

* Azure Databricks によって使用される <bpt id="p1">__</bpt>databricks-private<ept id="p1">__</ept> および <bpt id="p2">__</bpt>databricks-public<ept id="p2">__</ept> サブネットに加えて、仮想ネットワーク用に作成された <bpt id="p3">__</bpt>既定の<ept id="p3">__</ept> サブネットも必要です。
* Azure Databricks は、仮想ネットワークとの通信にプライベート エンドポイントを使用しません。

仮想ネットワークでの Azure Databricks の使用に関する詳細については、<bpt id="p1">[</bpt>Azure Virtual Network での Azure Databricks のデプロイ<ept id="p1">](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)</ept>に関するページを参照してください。

### <a name="azure-hdinsight-or-virtual-machine"></a>Azure HDInsight または仮想マシン

* Azure Machine Learning では、Ubuntu を実行する仮想マシンのみがサポートされています。

## <a name="required-public-internet-access"></a>必要なパブリック インターネット アクセス

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

ファイアウォール ソリューションの使用方法の詳細については、<bpt id="p1">[</bpt>Azure Machine Learning でのファイアウォールの使用<ept id="p1">](how-to-access-azureml-behind-firewall.md)</ept>に関するページを参照してください。

## <a name="compute-clusters"></a><a name="compute-cluster"></a>コンピューティング クラスター

次のタブを使用して、コンピューティング クラスターの作成方法を選択します。

# <a name="studio"></a><bpt id="p1">[</bpt>スタジオ<ept id="p1">](#tab/azure-studio)</ept>

次の手順を使用して、Azure Machine Learning スタジオでコンピューティング クラスターを作成します。

1. [Azure Machine Learning Studio](https://ml.azure.com/) にサインインし、お使いのサブスクリプションとワークスペースを選択します。
1. 左側の <bpt id="p1">__</bpt>[Compute]\(コンピューティング\)<ept id="p1">__</ept> を選択し、中央の <bpt id="p2">__</bpt>[Compute clusters]\(コンピューティング クラスター\)<ept id="p2">__</ept> を選択して、 <bpt id="p3">__</bpt>[+ 新規]<ept id="p3">__</ept> を選択します。

    <bpt id="p1">:::image type="content" source="./media/how-to-enable-virtual-network/create-compute-cluster.png" alt-text="</bpt>クラスター作成画面のスクリーンショット<ept id=&quot;p1&quot;>":::</ept>

1. <bpt id="p1">__</bpt>[Create compute cluster]\(コンピューティング クラスターの作成\)<ept id="p1">__</ept> ダイアログで、必要な VM サイズと構成を選択し、 <bpt id="p2">__</bpt>[次へ]<ept id="p2">__</ept> を選択します。

    <bpt id="p1">:::image type="content" source="./media/how-to-enable-virtual-network/create-compute-cluster-vm.png" alt-text="</bpt>VM 構成の設定画面のスクリーンショット<ept id=&quot;p1&quot;>":::</ept>

1. <bpt id="p1">__</bpt>[Configure Settings]\(構成の設定\)<ept id="p1">__</ept> セクションで、<bpt id="p2">__</bpt>コンピューティング名<ept id="p2">__</ept>、<bpt id="p3">__</bpt>仮想ネットワーク<ept id="p3">__</ept>、<bpt id="p4">__</bpt>サブネット<ept id="p4">__</ept>を設定します。

    :::image type="content" source="media/how-to-enable-virtual-network/create-compute-cluster-config.png" alt-text="コンピューティング名、仮想ネットワーク、サブネットの設定を示すスクリーンショット。":::

    > [!TIP]
    > ワークスペースがプライベート エンドポイントを使用して仮想ネットワークに接続する場合、 <bpt id="p1">__</bpt>[仮想ネットワーク]<ept id="p1">__</ept> 選択フィールドが灰色表示されます。
    > 

1. <bpt id="p1">__</bpt>[作成]<ept id="p1">__</ept> を選択してコンピューティング クラスターを作成します。

# <a name="python"></a>[Python](#tab/python)

次のコードでは、<ph id="ph2">`mynetwork`</ph> という名前の仮想ネットワークの <ph id="ph1">`default`</ph> サブネットに新しい Machine Learning コンピューティング クラスターが作成されます。

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           location="westus2",
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

---

作成プロセスが完了したら、実験でクラスターを使用してモデルをトレーニングします。 詳細については、<bpt id="p1">[</bpt>トレーニング用のコンピューティング ターゲットの選択と使用<ept id="p1">](how-to-set-up-training-targets.md)</ept>に関するページをご覧ください。

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="no-public-ip-for-compute-clusters-preview"></a><a name="no-public-ip-amlcompute"></a>コンピューティング クラスターの [No public IP]\(パブリック IP なし\) (プレビュー)

**[No public IP]\(パブリック IP なし\)** を有効にすると、コンピューティング クラスターと依存関係との通信にパブリック IP は使用されません。 代わりに、Azure Private Link エコシステムとサービスまたはプライベート エンドポイントを使用して仮想ネットワーク内でのみ通信が行われ、パブリック IP の必要性がまったくなくなります。 [No public IP]\(パブリック IP なし\) を使用すると、インターネットからコンピューティング クラスター ノードへのアクセスと検出ができなくなるため、重大な脅威のベクトルを排除できます。 **[No public IP]\(パブリック IP なし\)** クラスターは、多くの企業が使用しているパブリック IP なしのポリシーに準拠するのに役立ちます。 

**[No public IP]\(パブリック IP なし\)** が有効なコンピューティング クラスターには、パブリック IP コンピューティング クラスターの場合と比較して、パブリック インターネットからの **受信通信要件はありません**。 具体的には、どちらの受信 NSG 規則 (`BatchNodeManagement`、`AzureMachineLearning`) も必要ありません。 **VirtualNetwork** のソースとあらゆるポート ソース、**VirtualNetwork** の宛先、**29876、29877** の宛先ポートからの受信は引き続き許可する必要があります。

**[No public IP]\(パブリック IP なし\)** クラスターは、Azure Machine Learning のワークスペースの場合に [Azure Private Link](how-to-configure-private-link.md) に依存しています。 また、 **[No public IP]\(パブリック IP なし\)** が有効なコンピューティング クラスターの場合、プライベート エンドポイント ネットワーク ポリシーとプライベート リンク サービス ネットワーク ポリシーを無効にする必要があります。 これらの要件は、Azure Private Link サービスとプライベート エンドポイントに由来するものであり、Azure Machine Learning 固有のものではありません。 [Private Link サービスのネットワーク ポリシーの無効化](../private-link/disable-private-link-service-network-policy.md)に関するページの指示に従って、仮想ネットワーク サブネットにパラメーター `disable-private-endpoint-network-policies` と `disable-private-link-service-network-policies` を設定します。

**送信接続** が機能するには、ユーザー定義のルートを持つ Azure Firewall などのエグレス ファイアウォールを設定する必要があります。 たとえば、[受信および送信の構成](how-to-access-azureml-behind-firewall.md)で設定されたファイアウォールを使用し、コンピューティング クラスターがデプロイされているサブネット上にルート テーブルを定義することで、トラフィックをそこにルーティングすることができます。 ルート テーブル エントリには、ファイアウォールのプライベート IP アドレスのネクスト ホップとして、0.0.0.0/0 というアドレス プレフィックスを設定することができます。

クラスターがデプロイされているサブネット内の Azure コンテナー レジストリと Azure ストレージには、サービス エンドポイントまたはプライベート エンドポイントを使用できます。

Studio で [No public IP address]\(パブリック IP アドレスなし\) コンピューティング クラスター (プレビュー機能) を作成するには、仮想ネットワーク セクションで **[No public IP]\(パブリック IP なし\)** チェックボックスをオンにします。
また、ARM テンプレートを使用して [No public IP]\(パブリック IP なし\) コンピューティング クラスターを作成することもできます。 ARM テンプレートで、enableNodePublicIP パラメーターを false に設定してください。

[!INCLUDE [no-public-ip-info](../../includes/machine-learning-no-public-ip-availibility.md)]

**トラブルシューティング**

* クラスターの作成時に "The specified subnet has PrivateLinkServiceNetworkPolicies or PrivateEndpointNetworkEndpoints enabled" (指定されたサブネットでは PrivateLinkServiceNetworkPolicies または PrivateEndpointNetworkEndpoints が有効になっています) というエラー メッセージが表示された場合は、[Private Link サービスのネットワーク ポリシーの無効化](../private-link/disable-private-link-service-network-policy.md)および[プライベート エンドポイントのネットワーク ポリシーの無効化](../private-link/disable-private-endpoint-network-policy.md)に関するページの指示を参照してください。

* ACR または Azure Storage への接続の問題があってジョブの実行が失敗した場合は、お客様がサブネットに ACR と Azure Storage のサービス エンドポイントまたはプライベート エンドポイントを追加済みであること、ACR または Azure Storage によってそのサブネットからのアクセスが許可されていることをご確認ください。

* [no public IP]\(パブリック IP なし\) クラスターを作成したことを確保するには、Studio でクラスターの詳細を表示すれば、リソースのプロパティで **[No Public IP]\(パブリック IP なし\)** プロパティが **[true]** に設定されていることがわかります。

## <a name="compute-instance"></a>コンピューティング インスタンス

仮想ネットワークにデプロイされたコンピューティング インスタンスを作成する手順については、「[Azure Machine Learning コンピューティング インスタンスを作成して管理する](how-to-create-manage-compute-instance.md)」を参照してください。

### <a name="no-public-ip-for-compute-instances-preview"></a><a name="no-public-ip"></a>コンピューティング インスタンスの [No public IP]\(パブリック IP なし\) (プレビュー)

**[No public IP]\(パブリック IP なし\)** を有効にすると、コンピューティング インスタンスと依存関係との通信にパブリック IP は使用されません。 代わりに、Azure Private Link エコシステムとサービスまたはプライベート エンドポイントを使用して仮想ネットワーク内でのみ通信が行われ、パブリック IP の必要性がまったくなくなります。 [No public IP]\(パブリック IP なし\) を使用すると、インターネットからコンピューティング インスタンス ノードへのアクセスと検出ができなくなるため、重大な脅威のベクトルを排除できます。 また、コンピューティング インスタンスによってパケット フィルター処理が行われ、仮想ネットワーク外からのトラフィックが拒否されます。 **[No public IP]\(パブリック IP なし\)** インスタンスは、Azure Machine Learning のワークスペースの場合に [Azure Private Link](how-to-configure-private-link.md) に依存しています。 

**送信接続** が機能するには、ユーザー定義のルートを持つ Azure Firewall などのエグレス ファイアウォールを設定する必要があります。 たとえば、[受信および送信の構成](how-to-access-azureml-behind-firewall.md)で設定されたファイアウォールを使用し、コンピューティング インスタンスがデプロイされているサブネット上にルート テーブルを定義することで、トラフィックをそこにルーティングすることができます。 ルート テーブル エントリには、ファイアウォールのプライベート IP アドレスのネクスト ホップとして、0.0.0.0/0 というアドレス プレフィックスを設定することができます。

**[No public IP]\(パブリック IP なし\)** が有効なコンピューティング インスタンスには、パブリック IP コンピューティング インスタンスの場合と比較して、パブリック インターネットからの **受信通信要件はありません**。 具体的には、どちらの受信 NSG 規則 (`BatchNodeManagement`、`AzureMachineLearning`) も必要ありません。 **VirtualNetwork** のソース、あらゆるポート ソース、**VirtualNetwork** の宛先、**29876、29877、44224** の宛先ポートからの受信は引き続き許可する必要があります。

また、 **[No public IP]\(パブリック IP なし\)** が有効なコンピューティング インスタンスの場合、プライベート エンドポイント ネットワーク ポリシーとプライベート リンク サービス ネットワーク ポリシーを無効にする必要があります。 これらの要件は、Azure Private Link サービスとプライベート エンドポイントに由来するものであり、Azure Machine Learning 固有のものではありません。 「[Private Link サービスのソース IP のネットワーク ポリシーを無効にする](../private-link/disable-private-link-service-network-policy.md)」の指示に従って、仮想ネットワーク サブネットにパラメーター `disable-private-endpoint-network-policies` と `disable-private-link-service-network-policies` を設定します。

Studio で [No public IP address]\(パブリック IP アドレスなし\) コンピューティング インスタンス (プレビュー機能) を作成するには、仮想ネットワーク セクションで **[No public IP]\(パブリック IP なし\)** チェックボックスをオンにします。
また、ARM テンプレートを使用して [No public IP]\(パブリック IP なし\) コンピューティング インスタンスを作成することもできます。 ARM テンプレートで、enableNodePublicIP パラメーターを false に設定してください。

次のステップ:
* [カスタム DNS を使用する](how-to-custom-dns.md)
* [ファイアウォールを使用する](how-to-access-azureml-behind-firewall.md)

[!INCLUDE [no-public-ip-info](../../includes/machine-learning-no-public-ip-availibility.md)]

## <a name="inbound-traffic"></a>受信トラフィック

[!INCLUDE [udr info for computes](../../includes/machine-learning-compute-user-defined-routes.md)]

Azure Machine Learning の入力と出力のトラフィック要件の詳細については、<bpt id="p1">[</bpt>ファイアウォールの内側でのワークスペースの使用<ept id="p1">](how-to-access-azureml-behind-firewall.md)</ept>に関するページを参照してください。

## <a name="azure-databricks"></a>Azure Databricks

仮想ネットワークでの Azure Databricks の使用に関する具体的な情報については、「<bpt id="p1">[</bpt>Azure Virtual Network に Azure Databricks をデプロイする<ept id="p1">](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)</ept>」を参照してください。

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>仮想マシンまたは HDInsight クラスター

このセクションでは、ワークスペースのある仮想ネットワークで仮想マシンまたは Azure HDInsight クラスターを使用する方法について説明します。

### <a name="create-the-vm-or-hdinsight-cluster"></a>VM または HDInsight クラスターを作成する

Azure portal または Azure CLI を使用して VM または HDInsight クラスターを作成し、そのクラスターを Azure の仮想ネットワークに配置します。 詳細については、次の記事を参照してください。
* [Linux VM 用の Azure 仮想ネットワークの作成と管理を行う](../virtual-machines/linux/tutorial-virtual-network.md)

* <bpt id="p1">[</bpt>Azure Virtual Network を使用した Azure HDInsight の拡張<ept id="p1">](../hdinsight/hdinsight-plan-virtual-network-deployment.md)</ept>

### <a name="configure-network-ports"></a>ネットワーク ポートを構成する 

Azure Machine Learning で VM またはクラスターの SSH ポートと通信できるようにするために、ネットワーク セキュリティ グループ用のソース エントリを構成します。 SSH ポートは、通常はポート 22 です。 このソースからのトラフィックを許可するには、次の操作を実行します。

1. <bpt id="p1">__</bpt>[ソース]<ept id="p1">__</ept> ボックスの一覧で、 <bpt id="p2">__</bpt>[サービス タグ]<ept id="p2">__</ept> を選択します。

1. <bpt id="p1">__</bpt>[ソース サービス タグ]<ept id="p1">__</ept> ボックスの一覧で、 <bpt id="p2">__</bpt>[AzureMachineLearning]<ept id="p2">__</ept> を選択します。

    ![仮想ネットワーク内の VM または HDInsight クラスターで実験を行うためのインバウンド規則](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. <bpt id="p1">__</bpt>[ソース ポート範囲]<ept id="p1">__</ept> ボックスの一覧で、 <bpt id="p2">__</bpt>[<ph id="ph1">*</ph>]<ept id="p2">__</ept> を選択します。

1. <bpt id="p1">__</bpt>[宛先]<ept id="p1">__</ept> ボックスの一覧で <bpt id="p2">__</bpt>[すべて]<ept id="p2">__</ept> を選択します。

1. <bpt id="p1">__</bpt>[宛先ポート範囲]<ept id="p1">__</ept> ボックスの一覧で <bpt id="p2">__</bpt>[22]<ept id="p2">__</ept> を選択します。

1. <bpt id="p1">__</bpt>[プロトコル]<ept id="p1">__</ept> で <bpt id="p2">__</bpt>[すべて]<ept id="p2">__</ept> を選択します。

1. <bpt id="p1">__</bpt>[アクション]<ept id="p1">__</ept> で、 <bpt id="p2">__</bpt>[許可]<ept id="p2">__</ept> を選択します。

ネットワーク セキュリティ グループの既定のアウトバウンド規則を保持します。 詳細については、「[セキュリティ グループ](../virtual-network/network-security-groups-overview.md#default-security-rules)」の既定のセキュリティ規則をご覧ください。

既定のアウトバウンド規則を使用せずに仮想ネットワークのアウトバウンド アクセスを制限する場合は、「<bpt id="p1">[</bpt>必要なパブリック インターネット アクセス<ept id="p1">](#required-public-internet-access)</ept>」セクションを参照してください。

### <a name="attach-the-vm-or-hdinsight-cluster"></a>VM または HDInsight クラスターをアタッチする

VM または HDInsight クラスターをお客様の Azure Machine Learning のワークスペースにアタッチします。 詳細については、「<bpt id="p1">[</bpt>モデル トレーニング用のコンピューティング ターゲットを設定する<ept id="p1">](how-to-set-up-training-targets.md)</ept>」をご覧ください。

## <a name="next-steps"></a>次のステップ

この記事は、Azure Machine Learning ワークフローのセキュリティ保護に関するシリーズの一部です。 このシリーズの他の記事は次のとおりです。

* [Virtual Network の概要](how-to-network-security-overview.md)
* [ワークスペース リソースをセキュリティで保護する](how-to-secure-workspace-vnet.md)
* [推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)
* [スタジオの機能を有効にする](how-to-enable-studio-virtual-network.md)
* [カスタム DNS を使用する](how-to-custom-dns.md)
* [ファイアウォールを使用する](how-to-access-azureml-behind-firewall.md)
