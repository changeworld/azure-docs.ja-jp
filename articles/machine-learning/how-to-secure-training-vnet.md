---
title: 仮想ネットワークを使用してトレーニング環境をセキュリティで保護する
titleSuffix: Azure Machine Learning
description: 分離された Azure Virtual Network を使用して、Azure Machine Learning トレーニング環境をセキュリティで保護します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 08/04/2021
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 913fb8f782872a2a8651d19de8c918595cdecfeb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739150"
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
> セキュリティで保護されたワークスペース、コンピューティング クラスター、コンピューティング インスタンスの作成に関するチュートリアルについては、「[チュートリアル: セキュリティで保護されたワークスペースを作成する](tutorial-create-secure-workspace.md)」を参照してください。

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

    - 仮想ネットワーク リソース上の "Microsoft.Network/virtualNetworks/*/read"。 これは ARM テンプレートのデプロイでは必要ありません。
    - サブネット リソース上の "Microsoft.Network/virtualNetworks/subnet/join/action"。

    ネットワークでの Azure RBAC の詳細については、[ネットワークの組み込みロール](../role-based-access-control/built-in-roles.md#networking)に関するページを参照してください

### <a name="azure-machine-learning-compute-clusterinstance"></a>Azure Machine Learning コンピューティング クラスター/インスタンス

* 仮想ネットワークは Azure Machine Learning ワークスペースと同じサブスクリプションにある必要があります。
* コンピューティング インスタンスまたはクラスターに使用されるサブネットには、割り当てられていない十分な IP アドレスが必要です。

    * コンピューティング クラスターは動的にスケーリングできます。 未割り当ての IP アドレスが十分でない場合、クラスターは部分的に割り当てられます。
    * コンピューティング インスタンスに必要な IP アドレスは 1 つのみです。

* 仮想ネットワークを管理するためのアクセス許可を制限するセキュリティ ポリシーまたはロックがないことを確認します。 ポリシーまたはロックを確認する場合は、仮想ネットワークのサブスクリプションとリソース グループの両方を確認します。
* 仮想ネットワークのサブスクリプションまたはリソース グループに対するセキュリティ ポリシーまたはロックで、仮想ネットワークを管理するためのアクセス許可が制限されているかどうかを確認します。 
* トラフィックを制限することで仮想ネットワークのセキュリティを保護する計画の場合は、「[必要なパブリック インターネット アクセス](#required-public-internet-access)」を参照してください。
* コンピューティング クラスターまたはインスタンスをデプロイするために使用されるサブネットを他のサービスには委任しないでください。 たとえば、ACI に委任しないでください。

### <a name="azure-databricks"></a>Azure Databricks

* 仮想ネットワークは Azure Machine Learning のワークスペースと同じサブスクリプションとリージョンになければなりません。
* 仮想ネットワークでワークスペースの Azure Storage アカウントもセキュリティで保護される場合、それらは Azure Databricks クラスターと同じ仮想ネットワークに存在する必要があります。

## <a name="limitations"></a>制限事項

### <a name="azure-machine-learning-compute-clusterinstance"></a>Azure Machine Learning コンピューティング クラスター/インスタンス

* 複数のコンピューティング インスタンスまたはクラスターを 1 つの仮想ネットワークに配置する場合は、1 つまたは複数のリソースのクォータの増加を要求する必要がある場合があります。 Machine Learning コンピューティング インスタンスまたはクラスターにより、__仮想ネットワークが含まれているリソース グループ__ に追加のネットワーク リソースが自動的に割り当てられます。 サービスにより、各コンピューティング インスタンスまたはクラスターについて次のリソースが割り当てられます。

    * 1 つのネットワーク セキュリティ グループ (NSG)。 この NSG には、コンピューティング クラスターとコンピューティング インスタンスに固有の次のルールが含まれています。

        * `BatchNodeManagement` サービス タグからポート 29876-29877 で受信 TCP トラフィックを許可します。
        * `AzureMachineLearning` サービス タグからポート 44224 で受信 TCP トラフィックを許可します。

        次のスクリーンショットは、こうしたルールの例を示しています。

        :::image type="content" source="./media/how-to-secure-training-vnet/compute-instance-cluster-network-security-group.png" alt-text="NSG のスクリーンショット":::

    * 1 つのパブリック IP アドレス。 Azure Policy によってパブリック IP の作成が禁止されている場合、クラスターやインスタンスのデプロイが失敗します。
    * 1 つのロード バランサー

    コンピューティング クラスターでは、クラスターが 0 ノードにスケールダウンするたびにこれらのリソースが削除され、スケールアップすると作成されます。

    コンピューティング インスタンスでは、これらのリソースはインスタンスが削除されるまで保持されます。 インスタンスを停止しても、リソースは削除されません。 

    > [!IMPORTANT]
    > これらのリソースは、サブスクリプションの[リソース クォータ](../azure-resource-manager/management/azure-subscription-service-limits.md)によって制限されます。 仮想ネットワーク リソース グループがロックされている場合、コンピューティング クラスターやインスタンスの削除が失敗します。 コンピューティング クラスターやインスタンスが削除されるまで、ロード バランサーを削除することはできません。 また、ネットワーク セキュリティ グループの作成を禁止する Azure ポリシーがないことも確認してください。

* ワークスペースの Azure Storage アカウントも仮想ネットワーク内にある場合は、サブネットの制限事項に関する次のガイダンスを使用します。

    * Azure Machine Learning __スタジオ__ を使用してデータを視覚化する計画の場合、またはデザイナーを使用する計画の場合、ストレージ アカウントは __コンピューティング インスタンスまたはクラスターと同じサブネット内__ にある必要があります。
    * __SDK__ を使用する計画の場合は、ストレージ アカウントが別のサブネットに含まれていてもかまいません。

    > [!NOTE]
    > [信頼された Microsoft サービスによるこのアカウントに対するアクセスを許可します] チェック ボックスをオンにするだけでは、コンピューティングからの通信の許可には不十分です。

* ワークスペースでプライベート エンドポイントを使用する場合、コンピューティング インスタンスには仮想ネットワーク内からのみアクセスできます。 カスタム DNS または hosts ファイルを使用する場合は、`<instance-name>.<region>.instances.azureml.ms` のエントリを追加します。 このエントリを、ワークスペースのプライベート エンドポイントのプライベート IP アドレスにマップします。 詳細については、[カスタム DNS](./how-to-custom-dns.md) に関するページを参照してください。
* 仮想ネットワーク サービス エンドポイント ポリシーは、コンピューティング クラスターやインスタンスのシステム ストレージ アカウントに対して機能しません。
* ストレージ インスタンスとコンピューティング インスタンスが異なるリージョンに存在する場合、断続的にタイムアウトが発生することがあります。
* コンピューティング インスタンスで Jupyter Notebook を使用する場合:

    * WebSocket 通信を無効にしないでください。 ネットワークで `*.instances.azureml.net` と `*.instances.azureml.ms` との WebSocket 通信が許可されている必要があります。
    * データと同じ仮想ネットワークおよびサブネットの背後にあるコンピューティング リソースでノートブックが実行されていることを確認する必要があります。 コンピューティング インスタンスを作成する場合は、 **[詳細設定]**  >  **[仮想ネットワークの構成]** を使用して、ネットワークとサブネットを選択します。

* __コンピューティング クラスター__ は、ワークスペースとは別のリージョンに作成できます。 この機能は __プレビュー__ 段階であり、__コンピューティング クラスター__ でのみ利用できます。コンピューティング インスタンスでは利用できません。 クラスターに別のリージョンを使用する場合は、次の制限が適用されます。

    * ワークスペースに関連付けられているリソース (ストレージなど) がクラスターとは異なる仮想ネットワークにある場合は、ネットワーク間のグローバル仮想ネットワーク ピアリングを設定します。 詳細については、「[仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)」をご覧ください。
    * プライベート エンドポイントが有効なワークスペースを使用している場合、別のリージョンでのクラスターの作成は __サポートされていません__。
    * ネットワーク待機時間とデータ転送コストが増加する可能性があります。 待機時間とコストは、クラスターの作成時や、クラスターでのジョブの実行時に発生する可能性があります。

    ワークスペースとは異なるリージョンを使用する場合、NSG ルール、ユーザー定義ルート、入力や出力要件の使用などのガイダンスは通常通り適用されます。

### <a name="azure-databricks"></a>Azure Databricks

* Azure Databricks によって使用される __databricks-private__ および __databricks-public__ サブネットに加えて、仮想ネットワーク用に作成された __既定の__ サブネットも必要です。
* Azure Databricks は、仮想ネットワークとの通信にプライベート エンドポイントを使用しません。

仮想ネットワークでの Azure Databricks の使用に関する詳細については、[Azure Virtual Network での Azure Databricks のデプロイ](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)に関するページを参照してください。

### <a name="azure-hdinsight-or-virtual-machine"></a>Azure HDInsight または仮想マシン

* Azure Machine Learning では、Ubuntu を実行する仮想マシンのみがサポートされています。

## <a name="required-public-internet-access"></a>必要なパブリック インターネット アクセス

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

ファイアウォール ソリューションの使用方法の詳細については、[Azure Machine Learning でのファイアウォールの使用](how-to-access-azureml-behind-firewall.md)に関するページを参照してください。

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>コンピューティング クラスターとインスタンス 

次のタブを使用して、コンピューティング クラスターの作成方法を選択します。

# <a name="studio"></a>[スタジオ](#tab/azure-studio)

次の手順を使用して、Azure Machine Learning スタジオでコンピューティング クラスターを作成します。

1. [Azure Machine Learning Studio](https://ml.azure.com/) にサインインし、お使いのサブスクリプションとワークスペースを選択します。
1. 左側の __[Compute]\(コンピューティング\)__ を選択し、中央の __[Compute clusters]\(コンピューティング クラスター\)__ を選択して、 __[+ 新規]__ を選択します。

    :::image type="content" source="./media/how-to-enable-virtual-network/create-compute-cluster.png" alt-text="クラスター作成画面のスクリーンショット":::

1. __[Create compute cluster]\(コンピューティング クラスターの作成\)__ ダイアログで、必要な VM サイズと構成を選択し、 __[次へ]__ を選択します。

    :::image type="content" source="./media/how-to-enable-virtual-network/create-compute-cluster-vm.png" alt-text="VM 構成の設定画面のスクリーンショット":::

1. __[Configure Settings]\(構成の設定\)__ セクションで、__コンピューティング名__、__仮想ネットワーク__、__サブネット__ を設定します。

    > [!TIP]
    > ワークスペースがプライベート エンドポイントを使用して仮想ネットワークに接続する場合、 __[仮想ネットワーク]__ 選択フィールドが灰色表示されます。

    :::image type="content" source="./media/how-to-enable-virtual-network/create-compute-cluster-config.png" alt-text="仮想ネットワーク設定のスクリーンショット":::

1. __[作成]__ を選択してコンピューティング クラスターを作成します。

# <a name="python"></a>[Python](#tab/python)

次のコードでは、`mynetwork` という名前の仮想ネットワークの `default` サブネットに新しい Machine Learning コンピューティング クラスターが作成されます。

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

作成プロセスが完了したら、実験でクラスターを使用してモデルをトレーニングします。 詳細については、[トレーニング用のコンピューティング ターゲットの選択と使用](how-to-set-up-training-targets.md)に関するページをご覧ください。

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="inbound-traffic"></a>受信トラフィック

[!INCLUDE [udr info for computes](../../includes/machine-learning-compute-user-defined-routes.md)]

Azure Machine Learning の入力と出力のトラフィック要件の詳細については、[ファイアウォールの内側でのワークスペースの使用](how-to-access-azureml-behind-firewall.md)に関するページを参照してください。

## <a name="azure-databricks"></a>Azure Databricks

仮想ネットワークでの Azure Databricks の使用に関する具体的な情報については、「[Azure Virtual Network に Azure Databricks をデプロイする](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)」を参照してください。

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>仮想マシンまたは HDInsight クラスター

このセクションでは、ワークスペースのある仮想ネットワークで仮想マシンまたは Azure HDInsight クラスターを使用する方法について説明します。

### <a name="create-the-vm-or-hdinsight-cluster"></a>VM または HDInsight クラスターを作成する

Azure portal または Azure CLI を使用して VM または HDInsight クラスターを作成し、そのクラスターを Azure の仮想ネットワークに配置します。 詳細については、次の記事を参照してください。
* [Linux VM 用の Azure 仮想ネットワークの作成と管理を行う](../virtual-machines/linux/tutorial-virtual-network.md)

* [Azure Virtual Network を使用した Azure HDInsight の拡張](../hdinsight/hdinsight-plan-virtual-network-deployment.md)

### <a name="configure-network-ports"></a>ネットワーク ポートを構成する 

Azure Machine Learning で VM またはクラスターの SSH ポートと通信できるようにするために、ネットワーク セキュリティ グループ用のソース エントリを構成します。 SSH ポートは、通常はポート 22 です。 このソースからのトラフィックを許可するには、次の操作を実行します。

1. __[ソース]__ ボックスの一覧で、 __[サービス タグ]__ を選択します。

1. __[ソース サービス タグ]__ ボックスの一覧で、 __[AzureMachineLearning]__ を選択します。

    ![仮想ネットワーク内の VM または HDInsight クラスターで実験を行うためのインバウンド規則](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. __[ソース ポート範囲]__ ボックスの一覧で、 __[*]__ を選択します。

1. __[宛先]__ ボックスの一覧で __[すべて]__ を選択します。

1. __[宛先ポート範囲]__ ボックスの一覧で __[22]__ を選択します。

1. __[プロトコル]__ で __[すべて]__ を選択します。

1. __[アクション]__ で、 __[許可]__ を選択します。

ネットワーク セキュリティ グループの既定のアウトバウンド規則を保持します。 詳細については、「[セキュリティ グループ](../virtual-network/network-security-groups-overview.md#default-security-rules)」の既定のセキュリティ規則をご覧ください。

既定のアウトバウンド規則を使用せずに仮想ネットワークのアウトバウンド アクセスを制限する場合は、「[必要なパブリック インターネット アクセス](#required-public-internet-access)」セクションを参照してください。

### <a name="attach-the-vm-or-hdinsight-cluster"></a>VM または HDInsight クラスターをアタッチする

VM または HDInsight クラスターをお客様の Azure Machine Learning のワークスペースにアタッチします。 詳細については、「[モデル トレーニング用のコンピューティング ターゲットを設定する](how-to-set-up-training-targets.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

この記事は、Azure Machine Learning ワークフローのセキュリティ保護に関するシリーズの一部です。 このシリーズの他の記事は次のとおりです。

* [Virtual Network の概要](how-to-network-security-overview.md)
* [ワークスペース リソースをセキュリティで保護する](how-to-secure-workspace-vnet.md)
* [推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)
* [スタジオの機能を有効にする](how-to-enable-studio-virtual-network.md)
* [カスタム DNS を使用する](how-to-custom-dns.md)
* [ファイアウォールを使用する](how-to-access-azureml-behind-firewall.md)