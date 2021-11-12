---
title: クイックスタート - Azure Managed Instance for Apache Cassandra を使用してハイブリッド クラスターを構成する
description: このクイックスタートでは、Azure Managed Instance for Apache Cassandra を使用してハイブリッド クラスターを構成する方法について説明します。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 31b6f0459f2fb9b04646667abfda039760e5fcc0
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892440"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra"></a>クイックスタート: Azure Managed Instance for Apache Cassandra を使用してハイブリッド クラスターを構成する

Azure Managed Instance for Apache Cassandra では、マネージド オープンソースの Apache Cassandra データセンターに対して、デプロイとスケーリングの自動化された操作を提供します。 このサービスは、ハイブリッド シナリオの促進と継続的保守管理の削減に役立ちます。

このクイックスタートでは、Azure CLI のコマンドを使用してハイブリッド クラスターを構成する方法について説明します。 既にオンプレミスまたはセルフホステッド環境にデータセンターがある場合は、Azure Managed Instance for Apache Cassandra を使用して、そのクラスターに他のデータセンターを追加して管理することができます。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* この記事では、Azure CLI バージョン 2.30.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

* セルフホステッドまたはオンプレミス環境に接続された [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)。 オンプレミス環境を Azure に接続する方法の詳細については、「[オンプレミス ネットワークの Azure への接続](/azure/architecture/reference-architectures/hybrid-networking/)」の記事を参照してください。

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>ハイブリッド クラスターを構成する

1. [Azure portal](https://portal.azure.com/) にサインインし、仮想ネットワーク リソースに移動します。

1. **[サブネット]** タブを開いて新しいサブネットを作成します。 **[サブネットの追加]** フォームにあるフィールドの詳細については、[仮想ネットワーク](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)に関する記事を参照してください。

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="仮想ネットワークに新しいサブネットを追加する。" lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

   > [!NOTE]
   > Azure Managed Instance for Apache Cassandra をデプロイするには、インターネットへのアクセスが必要です。 インターネットへのアクセスが制限されている環境では、デプロイは失敗します。 Managed Cassandra が適切に機能するために必要な、次の重要な Azure サービスへのアクセスが VNet 内でブロックされていないことを確認します。 IP アドレスとポートの依存関係の広範な一覧を[こちら](network-rules.md)で確認することもできます。 
   > - Azure Storage
   > - Azure KeyVault
   > - Azure 仮想マシン スケール セット
   > - Azure 監視
   > - Azure Active Directory
   > - Azure Security

1. 次に Azure CLI を使用して、Cassandra Managed Instance に必要ないくつかの特殊なアクセス許可を VNet とサブネットに適用します。 `az role assignment create` コマンドを使用します。`<subscriptionID>`、`<resourceGroupName>`、`<vnetName>` は、適切な値に置き換えてください。

   ```azurecli-interactive
   az role assignment create \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   ```

   > [!NOTE]
   > 前のコマンドの `assignee` 値と `role` 値は、それぞれ固定されたサービス プリンシパルとロール識別子です。

1. 次に、ハイブリッド クラスターのリソースを構成します。 既にクラスターはあるので、ここでのクラスター名は、既存のクラスターの名前を識別するための論理リソースにすぎません。 以下のスクリプトで `clusterName` および `clusterNameOverride` 変数を定義する際は、必ず既存のクラスターの名前を使用してください。 
 
   また、少なくとも、既存のデータセンターのシード ノードと、ノード間の暗号化に必要な gossip 証明書も必要です。 Azure Managed Instance for Apache Cassandra では、データセンター間の通信にノード間暗号化が必要になります。 既存のクラスターにノード間暗号化が実装されていない場合は、それを実装する必要があります。[こちら](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/configuration/secureSSLNodeToNode.html)のドキュメントを参照してください。 証明書の場所へのパスを指定する必要があります。 各証明書は、PEM 形式にする必要があります (例: `-----BEGIN CERTIFICATE-----\n...PEM format 1...\n-----END CERTIFICATE-----`)。 一般に、証明書を実装するには、次の 2 つの方法があります。

   1. 自己署名証明書。 これは、各ノードのプライベートおよびパブリック (CA なし) 証明書を意味します。この場合は、すべてのパブリック証明書が必要になります。

   1. CA によって署名された証明書。 これは、自己署名 CA でも、パブリック CA でもかまいません。 この場合は、ルート CA 証明書 ([Preparing SSL certificates for production](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/configuration/secureSSLCertWithCA.html) の手順を参照) と、すべての中継局 (該当する場合) が必要になります。

   必要に応じて、クライアントからノードへの証明書も実装している場合 ([こちら](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/configuration/secureSSLClientToNode.html)を参照)、ハイブリッド クラスターの作成時に同じ形式でそれらを指定する必要もあります。 以下のサンプルを参照してください。

   > [!NOTE]
   > 以下で指定する `delegatedManagementSubnetId` 変数の値は、上記のコマンドで指定した `--scope` の値とまったく同じです。

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster-legal-name'
   clusterNameOverride='cassandra-hybrid-cluster-illegal-name'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>'
    
   # You can override the cluster name if the original name is not legal for an Azure resource:
   # overrideClusterName='ClusterNameIllegalForAzureResource'
   # the default cassandra version will be v3.11
    
   az managed-cassandra cluster create \
     --cluster-name $clusterName \
     --resource-group $resourceGroupName \
     --location $location \
     --delegated-management-subnet-id $delegatedManagementSubnetId \
     --external-seed-nodes 10.52.221.2 10.52.221.3 10.52.221.4 \
     --external-gossip-certificates /usr/csuser/clouddrive/rootCa.pem /usr/csuser/clouddrive/gossipKeyStore.crt_signed
     # optional - add your existing datacenter's client-to-node certificates (if implemented):
     # --client-certificates /usr/csuser/clouddrive/rootCa.pem /usr/csuser/clouddrive/nodeKeyStore.crt_signed 
   ```

   > [!NOTE]
   > クラスターにノード間およびクライアントからノードへの暗号化が既に存在する場合は、既存のクライアントおよび gossip SSL 証明書が保持されている場所がわかっている必要があります。 わからない場合は、`keytool -list -keystore <keystore-path> -rfc -storepass <password>` を実行して証明書を出力することができます。 

1. クラスター リソースが作成されたら、次のコマンドを実行してクラスターのセットアップの詳細を取得します。

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
   ```

1. 前のコマンドを実行すると、マネージド インスタンス環境に関する情報が返されます。 gossip 証明書は、既存のデータセンター内のノードの信頼ストアにインストールするために必要となります。 次のスクリーンショットは、前のコマンドの出力と証明書の形式を示しています。

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="クラスターから証明書の詳細を取得する。" lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

   > [!NOTE]
   > 上記のコマンドから返される証明書には、テキストとして表される改行が含まれています (例: `\r\n`)。 各証明書を既存のデータセンターの信頼ストアにインポートする前に、それをファイルにコピーして書式設定する必要があります。

   > [!TIP]
   > 上記のスクリーン ショットに示されている `gossipCertificates` 配列値をファイルにコピーして、以下の bash スクリプト (お使いのプラットフォーム用の [jq をダウンロードしてインストールする](https://stedolan.github.io/jq/download/)必要があります) を使用して、証明書の書式を設定し、それぞれに個別の pem ファイルを作成します。
   >
   > ```bash
   > readarray -t cert_array < <(jq -c '.[]' gossipCertificates.txt)
   > # iterate through the certs array, format each cert, write to a numbered file.
   > num=0
   > filename=""
   > for item in "${cert_array[@]}"; do
   >   let num=num+1
   >   filename="cert$num.pem"
   >   cert=$(jq '.pem' <<< $item)
   >   echo -e $cert >> $filename
   >   sed -e '1d' -e '$d' -i $filename
   > done
   > ```

1. 次に、ハイブリッド クラスターに新しいデータセンターを作成します。 変数の値は、実際のクラスターの情報に置き換えてください。

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName \
     --data-center-location $dataCenterLocation \
     --delegated-subnet-id $delegatedManagementSubnetId \
     --node-count 9 
   ```

1. 新しいデータセンターが作成されたら、データセンターの表示コマンドを実行して、その詳細を表示します。

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName 
   ```

1. 前のコマンドによって、新しいデータセンターのシード ノードが出力されます。 

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="データセンターの詳細を取得する。" lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

1. ここで、新しいデータセンターのシード ノードを [cassandra.yaml](https://docs.datastax.com/en/cassandra-oss/3.0/cassandra/configuration/configCassandra_yaml.html) ファイル内の既存のデータセンターの[シード　ノード構成](https://docs.datastax.com/en/cassandra-oss/3.0/cassandra/configuration/configCassandra_yaml.html#configCassandra_yaml__seed_provider)に追加します。 さらに、先ほど収集したマネージド インスタンスの gossip 証明書を、証明書ごとに `keytool` コマンドを使用して、既存のクラスター内の各ノードの信頼ストアにインストールします。

   ```bash
   keytool -importcert -keystore generic-server-truststore.jks -alias CassandraMI -file cert1.pem -noprompt -keypass myPass -storepass truststorePass
   ```

   > [!NOTE]
   > さらにデータセンターを追加したい場合は、上記の手順を繰り返します。ただし必要なのはシード ノードだけです。 

1. 最後に、次の CQL クエリを使用して、各キースペースのレプリケーション戦略を更新し、クラスター全体のすべてのデータセンターを含めます。

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'on-premise-dc': 3, 'managed-instance-dc': 3};
   ```

   さらに、パスワード テーブルも更新する必要があります。

   ```bash
   ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'on-premise-dc': 3, 'managed-instance-dc': 3}
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

このクイックスタートでは、Azure CLI と Azure Managed Instance for Apache Cassandra を使用してハイブリッド クラスターを作成する方法を説明しました。 これで、クラスターの操作を開始できます。

> [!div class="nextstepaction"]
> [Azure CLI を利用して Azure Managed Instance for Apache Cassandra リソースを管理する](manage-resources-cli.md)
