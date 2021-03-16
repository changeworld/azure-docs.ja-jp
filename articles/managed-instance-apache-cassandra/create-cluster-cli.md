---
title: クイックスタート - CLI を使用して Azure Managed Instance for Apache Cassandra クラスターを作成する
description: Azure CLI を使用して Azure Managed Instance for Apache Cassandra クラスターを作成するには、このクイックスタートをご利用ください。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 86fa7e2e45dacb86b6601b699dca46b1b909fd08
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102424701"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>クイックスタート: Azure CLI を使用して Azure Managed Instance for Apache Cassandra クラスターを作成する (プレビュー)

Azure Managed Instance for Apache Cassandra は、マネージドなオープンソースの Apache Cassandra データセンターのデプロイ操作とスケーリング操作を自動化します。 このサービスは、ハイブリッド シナリオの促進と継続的保守管理の削減に役立ちます。

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra は現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

このクイックスタートでは、Azure CLI コマンドを使用して、Azure Managed Instance for Apache Cassandra クラスターを作成する方法を紹介します。 また、データセンターの作成のほか、データセンター内のノードのスケールアップとスケールダウンについても取り上げます。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* この記事には、Azure CLI バージョン 2.12.1 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

* セルフホステッドまたはオンプレミス環境に接続された [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)。 オンプレミス環境を Azure に接続する方法の詳細については、「[オンプレミス ネットワークの Azure への接続](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)」の記事を参照してください。

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。


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

1. その仮想ネットワークとサブネットに、マネージド インスタンスで必要となるいくつかの特殊なアクセス許可を適用します。 `az role assignment create` コマンドを使用します。`<subscription ID>`、`<resource group name>`、`<VNet name>`、`<subnet name>` は、適切な値に置き換えてください。

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > 前のコマンドの `assignee` 値と `role` 値は、それぞれ固定されたサービス プリンシパルとロール識別子です。

1. 次に、新しく作成した仮想ネットワークにクラスターを作成します。 次のコマンドを実行します。`delegatedManagementSubnetId` 変数の値には必ず、前のコマンドで取得した `Resource ID` の値を使用してください。

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='<Resource_ID>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. 最後に、ノード数を 3 としてクラスターのデータセンターを作成します。

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId='<Resource_ID>'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedSubnetId \
      --node-count 3 
   ```

1. データセンターの作成後、データセンター内のノードをスケールアップまたはスケールダウンしたい場合は、次のコマンドを実行します。 `node-count` パラメーターの値は、必要な値に変更してください。

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId= '<Resource_ID>'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>クラスターへの接続

Azure Managed Instance for Apache Cassandra によって作成されるノードには、パブリック IP アドレスが割り当てられていません。 新しく作成した Cassandra クラスターに接続するには、仮想ネットワーク内に別のリソースを作成する必要があります。 たとえばアプリケーション リソースや、Apache のオープンソース クエリ ツール [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) がインストールされている仮想マシン リソースです。 Ubuntu 仮想マシンは、[Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/)を使用してデプロイできます。 デプロイ後、SSH を使用してそのマシンに接続し、CQLSH をインストールします。次のコマンドはその例です。

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

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、`az group delete` コマンドを使用して、リソース グループとマネージド インスタンス、およびすべての関連リソースを削除できます。

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure CLI を使用して、Azure Managed Instance for Apache Cassandra クラスターを作成する方法について学習しました。 これで、クラスターの操作を開始できます。

> [!div class="nextstepaction"]
> [Azure Databricks でマネージド Apache Spark クラスターをデプロイする](deploy-cluster-databricks.md)
