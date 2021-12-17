---
title: クイックスタート - CLI を使用して Azure Managed Instance for Apache Cassandra クラスターを作成する
description: Azure CLI を使用して Azure Managed Instance for Apache Cassandra クラスターを作成するには、このクイックスタートをご利用ください。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d439078615a091642e15b3895f0468b846a964fb
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704635"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli"></a>クイックスタート: Azure CLI を使用して Azure Managed Instance for Apache Cassandra クラスターを作成する

Azure Managed Instance for Apache Cassandra では、マネージド オープンソースの Apache Cassandra データセンターに対して、デプロイとスケーリングの自動化された操作を提供します。 このサービスは、ハイブリッド シナリオの促進と継続的保守管理の削減に役立ちます。

このクイックスタートでは、Azure CLI コマンドを使用して、Azure Managed Instance for Apache Cassandra クラスターを作成する方法を紹介します。 また、データセンターの作成のほか、データセンター内のノードのスケールアップとスケールダウンについても取り上げます。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* セルフホステッドまたはオンプレミス環境に接続された [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)。 オンプレミス環境を Azure に接続する方法の詳細については、「[オンプレミス ネットワークの Azure への接続](/azure/architecture/reference-architectures/hybrid-networking/)」の記事を参照してください。

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

> [!IMPORTANT]
> この記事では、Azure CLI バージョン 2.30.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>マネージド インスタンス クラスターを作成する

1. [Azure ポータル](https://portal.azure.com/)

1. Azure CLI でサブスクリプション ID を設定します。

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. 次に、仮想ネットワークと専用サブネットをご使用のリソース グループに作成します。

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```

   > [!NOTE]
   > Azure Managed Instance for Apache Cassandra をデプロイするには、インターネットへのアクセスが必要です。 インターネットへのアクセスが制限されている環境では、デプロイは失敗します。 Managed Cassandra が適切に機能するために必要な、次の重要な Azure サービスへのアクセスが VNet 内でブロックされていないことを確認します。
   > - Azure Storage
   > - Azure KeyVault
   > - Azure 仮想マシン スケール セット
   > - Azure 監視
   > - Azure Active Directory
   > - Azure Security

1. その仮想ネットワークに、マネージド インスタンスで必要となるいくつかの特殊なアクセス許可を適用します。 `az role assignment create` コマンドを使用します。`<subscriptionID>`、`<resourceGroupName>`、`<vnetName>` は、適切な値に置き換えてください。

   ```azurecli-interactive
   az role assignment create \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   ```

   > [!NOTE]
   > 前のコマンドの `assignee` と `role` の値は固定値です。これらの値は、コマンドに記載されているとおりに入力してください。 そうしないと、クラスターの作成時にエラーが発生します。 このコマンドの実行中にエラーが発生した場合は、実行するためのアクセス許可がない可能性があります。管理者に連絡してアクセス許可を求めてください。

1. 次に、[az managed-cassandra cluster create](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create) コマンドを使用して、新しく作成した仮想ネットワークにクラスターを作成します。 `delegatedManagementSubnetId` 変数の値を使用して次のコマンドを実行します。

   > [!NOTE]
   > 以下で指定する `delegatedManagementSubnetId` 変数の値は、上記のコマンドで指定した `--scope` の値とまったく同じです。

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
     --cluster-name $clusterName \
     --resource-group $resourceGroupName \
     --location $location \
     --delegated-management-subnet-id $delegatedManagementSubnetId \
     --initial-cassandra-admin-password $initialCassandraAdminPassword \
     --debug
   ```

1. 最後に [az managed-cassandra datacenter create](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create) コマンドを使用し、各ノードに 4 つの P30 ディスクが接続された Standard D8s v4 VM SKU という 3 つのノードでクラスターのデータセンターを作成します。

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   virtualMachineSKU='Standard_D8s_v4'
   noOfDisksPerNode=4
     
   az managed-cassandra datacenter create \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName \
     --data-center-location $dataCenterLocation \
     --delegated-subnet-id $delegatedManagementSubnetId \
     --node-count 3 \
     --sku $virtualMachineSKU \
     --disk-capacity $noOfDisksPerNode \
     --availability-zone false
   ```

   > [!NOTE]
   > `--sku` の値は、次の使用可能な SKU から選択できます。
   >
   > - Standard_E8s_v4
   > - Standard_E16s_v4 
   > - Standard_E20s_v4
   > - Standard_E32s_v4 
   > - Standard_DS13_v2
   > - Standard_DS14_v2
   > - Standard_D8s_v4
   > - Standard_D16s_v4
   > - Standard_D32s_v4 
   > 
   > `--availability-zone` が `false` に設定されていることにも注意してください。 可用性ゾーンを有効にするには、これを `true` に設定します。 

   > [!WARNING]
   > 可用性ゾーンは一部のリージョンでサポートされていません。 可用性ゾーンがサポートされていないリージョンを選択すると、デプロイは失敗します。 サポートされているリージョンについては、[こちら](../availability-zones/az-overview.md#azure-regions-with-availability-zones)を参照してください。 可用性ゾーンの正常なデプロイは、特定のリージョン内のすべてのゾーンでコンピューティング リソースが使用可能であることにも左右されます。 選択した SKU、または容量が一部のゾーンで使用できない場合、デプロイは失敗する可能性があります。 

1. データセンターの作成後、データセンター内のノードをスケールアップまたはスケールダウンしたい場合は、[az managed-cassandra datacenter update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update) コマンドを実行します。 `node-count` パラメーターの値は、必要な値に変更してください。

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter update \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName \
     --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>クラスターへの接続

Azure Managed Instance for Apache Cassandra によって作成されるノードには、パブリック IP アドレスが割り当てられていません。 新しく作成した Cassandra クラスターに接続するには、仮想ネットワーク内に別のリソースを作成する必要があります。 たとえばアプリケーション リソースや、Apache のオープンソース クエリ ツール [CQLSH](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html) がインストールされている仮想マシン リソースです。 Ubuntu 仮想マシンは、[Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/vm-simple-linux/)を使用してデプロイできます。 デプロイ後、SSH を使用してそのマシンに接続し、CQLSH をインストールします。次のコマンドはその例です。

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
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="troubleshooting"></a>トラブルシューティング

Azure CLI を使用して Virtual Network にアクセス許可を適用するときにエラー ("*Cannot find user or service principal in graph database for 'e5007d2c-4b13-4a74-9b6a-605d99f03501' ('e5007d2c-4b13-4a74-9b6a-605d99f03501' に対するユーザーまたはサービス プリンシパルがグラフ データベース内で見つかりません)* " など) が発生した場合、Azure portal から同じアクセス許可を手動で適用できます。 この方法については[こちら](add-service-principal.md)を参照してください。

> [!NOTE] 
> Azure Cosmos DB のロールの割り当ては、デプロイの目的にのみ使用されます。 Azure Managed Instance for Apache Cassandra には、Azure Cosmos DB に対するバックエンドの依存関係はありません。  

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、`az group delete` コマンドを使用して、リソース グループとマネージド インスタンス、およびすべての関連リソースを削除できます。

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure CLI を使用して、Azure Managed Instance for Apache Cassandra クラスターを作成する方法について学習しました。 これで、クラスターの操作を開始できます。

> [!div class="nextstepaction"]
> [Azure Databricks でマネージド Apache Spark クラスターをデプロイする](deploy-cluster-databricks.md)