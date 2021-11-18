---
title: クイックスタート - Azure portal から Azure Managed Instance for Apache Cassandra クラスターを作成する
description: このクイックスタートでは、Azure portal を使用して、Azure Managed Instance for Apache Cassandra クラスターを作成する方法について説明します。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 03b5cc8b56e7d6825ec7bee5ec61d156e960123b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704214"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal"></a>クイックスタート: Azure portal から Azure Managed Instance for Apache Cassandra クラスターを作成する

Azure Managed Instance for Apache Cassandra では、マネージド オープンソースの Apache Cassandra データセンターのデプロイとスケーリングの自動操作が提供されます。これにより、ハイブリッド シナリオが推進され、継続的なメンテナンスが削減されます。

このクイックスタートでは、Azure portal を使用して、Azure Managed Instance for Apache Cassandra クラスターを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>マネージド インスタンス クラスターを作成する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 検索バーで、**Managed Instance for Apache Cassandra** を検索し、結果を選択します。

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Managed Instance for Apache Cassandra を検索する。" lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. **[Create Managed Instance for Apache Cassandra cluster]\(Managed Instance for Apache Cassandra クラスターの作成\)** ボタンを選択します。

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="クラスターを作成する。" lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. **[Create Managed Instance for Apache Cassandra]\(Managed Instance for Apache Cassandra の作成\)** ウィンドウで、次の詳細を入力します。

   * **[サブスクリプション]** - ドロップダウン リストから Azure サブスクリプションを選びます。
   * **[リソース グループ]** - 新しいリソース グループを作成するか、既存のものを使用するかを指定します。 リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 詳細については、[Azure リソース グループの概要](../azure-resource-manager/management/overview.md)に関する記事を参照してください。
   * **[クラスター名]** - ご自分のクラスターの名前を入力します。
   * **[場所]** - ご自分のクラスターがデプロイされる場所。
   * **[Initial Cassandra admin password]\(最初の Cassandra 管理者パスワード\)** - クラスターの作成に使用されるパスワード。
   * **[Confirm Cassandra admin password]\(Cassandra 管理者パスワードの確認\)** - パスワードを再入力します。
   * **仮想ネットワーク** - 既存の仮想ネットワークとサブネットを選択するか、新しく新規作成します。 
   * **ロールの割り当て** - 仮想ネットワークでは、管理対象の Cassandra クラスターをデプロイできるようにするために特別なアクセス許可が必要です。 新しい仮想ネットワークを作成する場合、またはアクセス許可を適用せずに既存の仮想ネットワークを使用する場合、このチェックボックスをオンのままにします。 Managed Instance Cassandra クラスターが既に存在する仮想ネットワークを使用している場合、このオプションをオフにします。

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="クラスターの作成フォームに入力する。" lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

   > [!NOTE]
   > Azure Managed Instance for Apache Cassandra をデプロイするには、インターネットへのアクセスが必要です。 インターネットへのアクセスが制限されている環境では、デプロイは失敗します。 Managed Cassandra が適切に機能するために必要な、次の重要な Azure サービスへのアクセスが VNet 内でブロックされていないことを確認します。 詳細については、「[必要なアウトバウンド ネットワーク規則](network-rules.md)」を参照してください。
   > - Azure Storage
   > - Azure KeyVault
   > - Azure 仮想マシン スケール セット
   > - Azure 監視
   > - Azure Active Directory
   > - Azure Security

1. 次に、 **[データ センター]** タブを選択します。

1. 次の詳細を入力します。

   * **[データセンター名]** - ドロップダウン リストから Azure サブスクリプションを選択します。
   * **[可用性ゾーン]** - 可用性ゾーンを有効にする場合、このチェックボックスをオンにします。
   * **[SKU サイズ]** - 使用可能な仮想マシン SKU サイズから選択します。
   * **[ディスク数]** - 各 Cassandra ノードに接続する p30 ディスクの数を選択します。
   * **[ノード数]** - このデータセンターにデプロイされる Cassandra ノードの数を選択します。

   :::image type="content" source="./media/create-cluster-portal/create-datacenter-page.png" alt-text="概要を確認し、データセンターを作成します。" lightbox="./media/create-cluster-portal/create-datacenter-page.png" border="true":::

   > [!WARNING]
   > 可用性ゾーンは一部のリージョンでサポートされていません。 可用性ゾーンがサポートされていないリージョンを選択すると、デプロイは失敗します。 サポートされているリージョンについては、[こちら](../availability-zones/az-overview.md#azure-regions-with-availability-zones)を参照してください。 可用性ゾーンの正常なデプロイは、特定のリージョン内のすべてのゾーンでコンピューティング リソースが使用可能であることにも左右されます。 選択した SKU、または容量が一部のゾーンで使用できない場合、デプロイは失敗する可能性があります。 

1. 次に、 **[確認と作成]** 、 **[作成]** の順にクリックします。

   > [!NOTE]
   > クラスターの作成には、最大 15 分かかることがあります。

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="概要を確認して、クラスターを作成する。" lightbox="./media/create-cluster-portal/review-create.png" border="true":::

1. デプロイが完了したら、リソース グループを調べて、新しく作成されたマネージド インスタンス クラスターを確認します。

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="クラスターが作成された後の [概要] ページ。" lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. クラスター ノードを閲覧するには、クラスター リソースに移動し、 **[データ センター]** ウィンドウを開いて確認します。

   :::image type="content" source="./media/create-cluster-portal/datacenter-1.png" alt-text="データセンター ノードを表示します。" lightbox="./media/create-cluster-portal/datacenter-1.png" border="true":::

<!-- ## <a id="create-account"></a>Add a datacenter

1. To add another datacenter, click the add button in the **Data Center** pane:

   :::image type="content" source="./media/create-cluster-portal/add-datacenter.png" alt-text="Click on add datacenter." lightbox="./media/create-cluster-portal/add-datacenter.png" border="true":::

   > [!WARNING]
   > If you are adding a datacenter in a different region, you will need to select a different virtual network. You will also need to ensure that this virtual network has connectivity to the primary region's virtual network created above (and any other virtual networks that are hosting datacenters within the managed instance cluster). Take a look at [this article](../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks) to learn how to peer virtual networks using Azure portal. You also need to make sure you have applied the appropriate role to your virtual network before attempting to deploy a managed instance cluster, using the below CLI command.
   >
   > ```azurecli-interactive  
   >     az role assignment create \
   >     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
   >     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
   >     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   > ```

1. Fill in the appropriate fields:

   * **Datacenter name** - From the drop-down, select your Azure subscription.
   * **Availability zone** - Check this box if you want availability zones to be enabled in this datacenter.
   * **Location** - Location where your datacenter will be deployed to.
   * **SKU Size** - Choose from the available Virtual Machine SKU sizes.
   * **No. of disks** - Choose the number of p30 disks to be attached to each Cassandra node.
   * **SKU Size** - Choose the number of Cassandra nodes that will be deployed to this datacenter.
   * **Virtual Network** - Select an Exiting Virtual Network and Subnet.  

   :::image type="content" source="./media/create-cluster-portal/add-datacenter-2.png" alt-text="Add Datacenter." lightbox="./media/create-cluster-portal/add-datacenter-2.png" border="true":::

   > [!WARNING]
   > Notice that we do not allow creation of a new virtual network when adding a datacenter. You need to choose an existing virtual network, and as mentioned above, you need to ensure there is connectivity between the target subnets where datacenters will be deployed. You also need to apply the appropriate role to the VNet to allow deployment (see above).

1. When the datacenter is deployed, you should be able to view all datacenter information in the **Data Center** pane:

   :::image type="content" source="./media/create-cluster-portal/multi-datacenter.png" alt-text="View the cluster resources." lightbox="./media/create-cluster-portal/multi-datacenter.png" border="true":::

## Troubleshooting

If you encounter an error when applying permissions to your Virtual Network using Azure CLI, such as *Cannot find user or service principal in graph database for 'e5007d2c-4b13-4a74-9b6a-605d99f03501'*, you can apply the same permission manually from the Azure portal. Learn how to do this [here](add-service-principal.md).

> [!NOTE] 
> The Azure Cosmos DB role assignment is used for deployment purposes only. Azure Managed Instanced for Apache Cassandra has no backend dependencies on Azure Cosmos DB.   -->

## <a name="connecting-to-your-cluster"></a>クラスターに接続する

Azure Managed Instance for Apache Cassandra では、パブリック IP アドレスを持つノードが作成されないため、新しく作成された Cassandra クラスターに接続するには、VNet 内に別のリソースを作成する必要があります。 これは、アプリケーション、または Apache のオープンソース クエリ ツール [CQLSH](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html) がインストールされている仮想マシンにすることができます。 [テンプレート](https://azure.microsoft.com/resources/templates/vm-simple-linux/)を使用して、Ubuntu 仮想マシンをデプロイできます。 デプロイされたら、SSH を使用してマシンに接続し、次のコマンドを使用して CQLSH をインストールします。

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
initial_admin_password="Password provided when creating the cluster"
cqlsh $host 9042 -u cassandra -p $initial_admin_password --ssl
```


## <a name="clean-up-resources"></a>リソースをクリーンアップする

このマネージド インスタンス クラスターを引き続き使用しない場合は、次の手順でそれを削除します。

1. Azure portal の左側にあるメニューで、 **[リソース グループ]** を選択します。
1. 一覧から、このクイック スタートで作成したリソース グループを選択します。
1. リソース グループの **[概要]** ペインで、 **[リソース グループの削除]** を選択します。
1. 次のウィンドウで、削除するリソース グループの名前を入力し、**[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure portal を使用して、Azure Managed Instance for Apache Cassandra クラスターを作成する方法について学習しました。 これで、クラスターの操作を開始できます。

> [!div class="nextstepaction"]
> [Azure Databricks でマネージド Apache Spark クラスターをデプロイする](deploy-cluster-databricks.md)