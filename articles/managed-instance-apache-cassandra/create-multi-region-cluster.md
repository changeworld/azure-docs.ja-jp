---
title: クイックスタート - Azure Managed Instance for Apache Cassandra を使用してマルチリージョン クラスターを構成する
description: このクイックスタートでは、Azure Managed Instance for Apache Cassandra を使用してマルチリージョン クラスターを構成する方法について説明します。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fe2ab4a780e07f8d8325c2881d562a379ec16b97
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894395"
---
# <a name="quickstart-create-a-multi-region-cluster-with-azure-managed-instance-for-apache-cassandra"></a>クイックスタート: Azure Managed Instance for Apache Cassandra を使用してマルチリージョン クラスターを作成する

Azure Managed Instance for Apache Cassandra では、マネージド オープンソースの Apache Cassandra データセンターに対して、デプロイとスケーリングの自動化された操作を提供します。 このサービスは、ハイブリッド シナリオの促進と継続的保守管理の削減に役立ちます。

このクイックスタートでは、Azure CLI のコマンドを使用して Azure にマルチリージョン クラスターを構成する方法について説明します。  

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* この記事では、Azure CLI バージョン 2.30.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

* セルフホステッドまたはオンプレミス環境に接続された [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)。 オンプレミス環境を Azure に接続する方法の詳細については、「[オンプレミス ネットワークの Azure への接続](/azure/architecture/reference-architectures/hybrid-networking/)」の記事を参照してください。

## <a name="setting-up-the-network-environment"></a><a id="create-account"></a>ネットワーク環境の設定

このサービスでプロビジョニングされたデータセンターはすべて VNet インジェクションを使用して専用サブネットにデプロイする必要があります。そのため、マルチリージョン クラスターが正常に機能するように、デプロイの前に適切なネットワーク ピアリングを構成する必要があります。 米国東部と米国東部 2 の別々のリージョンに 2 つのデータセンターがあるクラスターを作成します。 まず、リージョンごとに仮想ネットワークを作成する必要があります。 

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. "cassandra-mi-multi-region" という名前のリソース グループを作成します

   ```azurecli-interactive
   az group create -l eastus2 -n cassandra-mi-multi-region
   ```

1. 専用サブネットを指定して、米国東部 2 に 1 つ目の VNet を作成します。

   ```azurecli-interactive
   az network vnet create \
     -n vnetEastUs2 \
     -l eastus2 \
     -g cassandra-mi-multi-region \
     --address-prefix 10.0.0.0/16 \
     --subnet-name dedicated-subnet
   ```

1. 同様に、専用サブネットを指定して、米国東部に 2 つ目の VNet を作成します。

   ```azurecli-interactive
    az network vnet create \
      -n vnetEastUs \
      -l eastus \
      -g cassandra-mi-multi-region \
      --address-prefix 192.168.0.0/16 \
      --subnet-name dedicated-subnet
   ```

   > [!NOTE]
   > ピアリング時にエラーが発生しないようにするために、異なる IP アドレス範囲を明示的に追加します。 

1. 次に、1 つ目の VNet を 2 つ目の VNet にピアリングする必要があります。

   ```azurecli-interactive
   az network vnet peering create \
     -g cassandra-mi-multi-region \
     -n MyVnet1ToMyVnet2 \
     --vnet-name vnetEastUs2 \
     --remote-vnet vnetEastUs \
     --allow-vnet-access \
     --allow-forwarded-traffic
   ```

1. 2 つの VNet を接続するために、2 つ目の VNet と 1 つ目の VNet の間に別のピアリングを作成します。

   ```azurecli-interactive
   az network vnet peering create \
     -g cassandra-mi-multi-region \
     -n MyVnet2ToMyVnet1 \
     --vnet-name vnetEastUs \
     --remote-vnet vnetEastUs2 \
     --allow-vnet-access \
     --allow-forwarded-traffic  
   ```

   > [!NOTE]
   > さらにリージョンを追加する場合、VNet ごとに、その VNet から他のすべての VNet へのピアリングと、他のすべての VNet からその VNet へのピアリングが必要になります。 

1. 前のコマンドの出力を確認し、"peeringState" の値が "Connected" になっていることを確認します。 これは、次のコマンドを実行して確認することもできます。

   ```azurecli-interactive
   az network vnet peering show \
     --name MyVnet1ToMyVnet2 \
     --resource-group cassandra-mi-multi-region \
     --vnet-name vnetEastUs2 \
     --query peeringState
   ``` 

1. 次に、Azure Managed Instance for Apache Cassandra に必要な、いくつかの特別なアクセス許可を両方の仮想ネットワークに適用します。 以下を実行します。このとき、`<SubscriptionID>` を実際のサブスクリプション ID に置き換えてください。

   ```azurecli-interactive
   az role assignment create \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs2

   az role assignment create     \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs
    ```
   > [!NOTE]
   > 前のコマンドの `assignee` と `role` の値は固定値です。これらの値は、コマンドに記載されているとおりに入力してください。 そうしないと、クラスターの作成時にエラーが発生します。 このコマンドの実行中にエラーが発生した場合は、実行するためのアクセス許可がない可能性があります。管理者に連絡してアクセス許可を求めてください。

## <a name="create-a-multi-region-cluster"></a><a id="create-account"></a>マルチリージョン クラスターを作成する

1. 適切なネットワークが作成された段階で、クラスター リソースをデプロイする準備が整いました (`<Subscription ID>` を実際のサブスクリプション ID に置き換えてください)。 この処理には 5 分から 10 分かかることがあります。

   ```azurecli-interactive
   resourceGroupName='cassandra-mi-multi-region'
   clusterName='test-multi-region'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs2/subnets/dedicated-subnet'
   initialCassandraAdminPassword='myPassword'
        
    az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. クラスター リソースが作成された段階で、データ センターを作成する準備が整いました。 まず、米国東部 2 にデータセンターを作成します (`<SubscriptionID>` を実際のサブスクリプション ID に置き換えてください)。 この処理には最大 10 分かかることがあります。

   ```azurecli-interactive
   resourceGroupName='cassandra-mi-multi-region'
   clusterName='test-multi-region'
   dataCenterName='dc-eastus2'
   dataCenterLocation='eastus2'
   delegatedManagementSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs2/subnets/dedicated-subnet'
        
    az managed-cassandra datacenter create \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName \
       --data-center-location $dataCenterLocation \
       --delegated-subnet-id $delegatedManagementSubnetId \
       --node-count 3
   ```

1. 次に、米国東部にデータセンターを作成します (`<SubscriptionID>` を実際のサブスクリプション ID に置き換えてください)。

   ```azurecli-interactive
   resourceGroupName='cassandra-mi-multi-region'
   clusterName='test-multi-region'
   dataCenterName='dc-eastus'
   dataCenterLocation='eastus'
   delegatedManagementSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs/subnets/dedicated-subnet'
        
    az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedManagementSubnetId \
      --node-count 3 
   ```

1. 2 つ目のデータセンターが作成されたら、ノード状態を取得して、すべての Cassandra ノードが正常に動作していることを確認します。

    ```azurecli-interactive
    resourceGroupName='cassandra-mi-multi-region'
    clusterName='test-multi-region'
    
    az managed-cassandra cluster node-status \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName
    ```

1. 最後に、CQLSH を使用して[対象のクラスターに接続](create-cluster-cli.md#connect-to-your-cluster)し、次の CQL クエリを使用して、各キースペースのレプリケーション戦略を更新し、クラスター全体のすべてのデータセンターを含めます。

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'dc-eastus2': 3, 'dc-eastus': 3};
   ```
   さらに、パスワード テーブルも更新する必要があります。

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'dc-eastus2': 3, 'dc-eastus': 3}
   ```

## <a name="troubleshooting"></a>トラブルシューティング

Azure CLI を使用して Virtual Network にアクセス許可を適用するときにエラー ("*Cannot find user or service principal in graph database for 'e5007d2c-4b13-4a74-9b6a-605d99f03501' ('e5007d2c-4b13-4a74-9b6a-605d99f03501' に対するユーザーまたはサービス プリンシパルがグラフ データベース内で見つかりません)* " など) が発生した場合、Azure portal から同じアクセス許可を手動で適用できます。 この方法については[こちら](add-service-principal.md)を参照してください。

> [!NOTE] 
> Azure Cosmos DB のロールの割り当ては、デプロイの目的にのみ使用されます。 Azure Managed Instance for Apache Cassandra には、Azure Cosmos DB に対するバックエンドの依存関係はありません。  

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このマネージド インスタンス クラスターを引き続き使用しない場合は、次の手順でそれを削除します。

1. Azure portal の左側にあるメニューで、 **[リソース グループ]** を選択します。
1. 一覧から、このクイック スタートで作成したリソース グループを選択します。
1. リソース グループの **[概要]** ペインで、 **[リソース グループの削除]** を選択します。
1. 次のウィンドウで、削除するリソース グループの名前を入力し、**[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure CLI と Azure Managed Instance for Apache Cassandra を使用してマルチリージョン クラスターを作成する方法を説明しました。 これで、クラスターの操作を開始できます。

> [!div class="nextstepaction"]
> [Azure CLI を利用して Azure Managed Instance for Apache Cassandra リソースを管理する](manage-resources-cli.md)
