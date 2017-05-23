---
title: "仮想ネットワークを使用して HDInsight 上の Kafka に接続する - Azure | Microsoft Docs"
description: "kafka-python クライアントを使用して HDInsight 上の Kafka にリモートで接続する方法について説明します。 このドキュメントの構成では、Azure Virtual Network 内で HDInsight を使用します。 リモート クライアントは、ポイント対サイト VPN ゲートウェイ経由で仮想ネットワークに接続します。"
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.custom: hdinsightactive
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/18/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 9489fdc3c5388a7510bc4411b4abb05fa72fbf4f
ms.contentlocale: ja-jp
ms.lasthandoff: 05/08/2017


---

# <a name="connect-to-kafka-on-hdinsight-preview-through-an-azure-virtual-network"></a>Azure Virtual Network 経由で HDInsight 上の Kafka に接続する

Azure Virtual Network を使用して HDInsight 上の Kafka に接続します。 Kafka クライアント (プロデューサーとコンシューマー) は、直接 HDInsight 上で、またはリモート システム上で実行できます。 リモート クライアントは、Azure Virtual Network 経由で HDInsight 上の Kafka に接続する必要があります。 このドキュメントの情報を使用して、リモート クライアントが Azure Virtual Network を使用して HDInsight に接続する方法を理解してください。

> [!IMPORTANT]
> このドキュメントで説明されている構成のいくつかは、Windows、macOS、または Linux クライアントで使用できます。 ただし、ポイント対サイトを含む例では、Windows 用の VPN クライアントのみが提供されます。
>
> さらに、例では、Python クライアント ([kafka-python](http://kafka-python.readthedocs.io/en/master/)) を使用して、HDInsight 上の Kafka との通信を検証します。

## <a name="architecture-and-planning"></a>アーキテクチャと計画

HDInsight クラスターは、Azure Virtual Network 内ではセキュリティで保護され、SSH と HTTPS トラフィックの受信のみが許可されます。 トラフィックはパブリック ゲートウェイ経由で到着し、このゲートウェイは Kafka クライアントからのトラフィックをルーティングしません。 リモート クライアントから Kafka にアクセスするには、仮想プライベート ネットワーク (VPN) ゲートウェイを提供する Azure Virtual Network を作成する必要があります。 仮想ネットワークとゲートウェイを構成したら、仮想ネットワークに HDInsight をインストールし、VPN ゲートウェイを使用して接続します。

![VPN 経由で接続されているクライアントがある Azure Virtual Network 内の HDInsight を示す図](media/hdinsight-apache-kafka-connect-vpn-gateway/hdinsight-in-virtual-network.png)

次の一覧には、HDInsight 上の Kafka を仮想ネットワークで使用するプロセスに関する情報が含まれています。

1. 仮想ネットワークを作成します。 Azure Virtual Network での HDInsight の使用に関する具体的な情報については、「[Azure Virtual Network を使用した HDInsight 機能の拡張](hdinsight-extend-hadoop-virtual-network.md)」をご覧ください。

2. (省略可能) 仮想ネットワーク内に Azure 仮想マシンを作成し、カスタム DNS サーバーをインストールします。 この DNS サーバーは、サイト間または Vnet 間構成でリモート クライアントの名前解決を有効にするために使用します。 詳細については、「[VM とクラウド サービスの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)」を参照してください。

3. 仮想ネットワークの VPN Gateway を作成します。 VPN ゲートウェイの構成の詳細については、「[VPN Gateway について](../vpn-gateway/vpn-gateway-about-vpngateways.md)」を参照してください。

4. 仮想ネットワーク内に HDInsight を作成します。 ネットワーク用のカスタム DNS サーバーを構成した場合、HDInsight は自動的にそれを使用するように構成されます。

5. (省略可能) カスタム DNS サーバーを使用せず、クライアントと仮想ネットワークの間で名前解決が行われない場合は、IP を提供するように Kafka を構成する必要があります。 詳細については、このドキュメントの 「[IP を提供するように Kafka を構成する](#configure-kafka-for-ip-advertising)」セクションを参照してください。

## <a name="create-using-powershell"></a>作成: PowerShell の使用

このセクションの手順では、[Azure PowerShell](/powershell/azure/overview) を使用して次の構成を作成します。

* Azure Virtual Network
* ポイント対サイト VPN ゲートウェイ
* Azure Storage アカウント (HDInsight で使用されます)
* HDInsight 上の Kafka

1. [ポイント対サイト接続用の自己署名ルートの操作](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)に関するドキュメントの手順に従って、ゲートウェイで必要な証明書を作成します。

2. PowerShell プロンプトを開き、次のコードを使用して Azure サブスクリプションにログインします。

    ```powershell
    Add-AzureRmAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzureRmSubscription -SubscriptionName "name of your subscription"
    ```

3. 次のコードを使用して、構成情報を含む変数を作成します。

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.5"
    $hdiType = "Kafka"
    ```

4. 次のコードを使用して、Azure リソース グループと仮想ネットワークを作成します。

    ```powershell
    # Create the resource group that contains everything
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzureRmPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzureRmVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzureRmVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]
    > このプロセスは、完了するまで数分かかる可能性があります。

5. 次のコードを使用して、Azure Storage アカウントと BLOB コンテナーを作成します。

    ```powershell
    # Create the storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -Type Standard_GRS `
        -Location $location

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzureStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. 次のコードを使用して、HDInsight クラスターを作成します。

    ```powershell
    # Create the HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

  > [!WARNING]
  > このプロセスは、完了するまで約 20 分かかります。

8. 次のコマンドレットを使用して、仮想ネットワークでの Windows VPN クライアントの URL を取得します。

    ```powershell
    Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName `
        -VirtualNetworkGatewayName $vpnName `
        -ProcessorArchitecture Amd64
    ```

    Windows VPN クライアントをダウンロードするには、返された URI を Web ブラウザーで使用します。

## <a name="configure-kafka-for-ip-advertising"></a>IP を提供するように Kafka を構成する

既定では、Zookeeper は、Kafka ブローカーのドメイン名をクライアントに返します。 仮想ネットワーク内のエンティティの名前解決を使用できないため、VPN クライアントではこの構成は機能しません。 次の手順を使用して、ドメイン名の代わりに IP アドレスを提供するように HDInsight 上の Kafka を構成します。

1. Web ブラウザーで、https://CLUSTERNAME.azurehdinsight.net に移動します。 __CLUSTERNAME__ を HDInsight クラスター上の Kafka の名前に置き換えます。

    プロンプトが表示されたら、クラスターの HTTPS ユーザー名とパスワードを入力します。 クラスターの Ambari Web UI が表示されます。

2. Kafka に関する情報を表示するには、左にある一覧から __[Kafka]__ を選択します。

    ![Kafka が強調表示されているサービスの一覧](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Kafka 構成を表示するには、上部中央の __[Configs (構成)]__ を選択します。

    ![Kafka の構成リンク](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. __kafka-env__ 構成を検索するには、右上の __[Filter (フィルター)]__フィールドに「`kafka-env`」と入力します。

    ![kafka-env の Kafka 構成](./media/hdinsight-apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. IP アドレスを提供するように Kafka を構成するには、次のテキストを __kafka-env-template__ フィールドの最後に追加します。

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Kafka がリッスンするインターフェイスを構成するには、右上の __[Filter (フィルター)]__ フィールドに「`listeners`」と入力します。

7. すべてのネットワーク インターフェイスをリッスンするように Kafka を構成するには、__[listeners (リスナー)]__ フィールドの値を `PLAINTEXT://0.0.0.0:92092`に変更します。

8. 構成を保存するには、__[Save (保存)]__ ボタンを使用します。 変更を説明するテキスト メッセージを入力します。 変更が保存されたら、__[OK]__ を保存します。

    ![構成を保存するボタン](./media/hdinsight-apache-kafka-connect-vpn-gateway/save-button.png)

9. Kafka の再起動時にエラーが発生しないようにするため、__[Service Actions (サービス アクション)__] ボタンを使用して __[Turn On Maintenance Mode (メンテナンス モードの有効化)]__ を選択します。 [OK] を選択して、この操作を完了します。

    ![[Turn On Maintenance Mode (メンテナンス モードの有効化)] が強調表示されているサービス アクション](./media/hdinsight-apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Kafka を再起動するには、__[Restart (再起動)]__ ボタンをクリックし、__[Restart All Affected (影響を受けるものをすべて再起動)]__ を選択します。 再起動を確認し、操作が完了したら __[OK]__ ボタンを使用します。

    ![[Restart All Affected (影響を受けるものをすべて再起動)] が強調表示されている [Restart (再起動)] ボタン](./media/hdinsight-apache-kafka-connect-vpn-gateway/restart-button.png)

11. メンテナンス モードを無効にするには、__[Service Actions (サービス アクション)]__ ボタンをクリックし、__[Turn Off Maintenance Mode (メンテナンス モードの無効化)]__ を選択します。 **[OK]** を選択して、この操作を完了します。

## <a name="connect-to-the-vpn-gateway"></a>VPN ゲートウェイに接続する

__Windows クライアント__から VPN ゲートウェイに接続するには、[ポイント対サイト接続の構成](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#a-nameclientcertificatea7---install-an-exported-client-certificate)に関するドキュメントの「__Azure への接続__」セクションに従います。

## <a name="remote-kafka-client"></a>リモート Kafka クライアント

クライアント コンピューターから Kafka に接続するには、Kafka ブローカーまたは Zookeeper ノード (クライアントが必要とするほう) の IP アドレスを使用する必要があります。 次の手順を使用して、Kafka ブローカーの IP アドレスを取得して、Python アプリケーションからそれらを使用します。

1. 次のスクリプトを使用して、クラスター内のノードの IP アドレスを取得します。

    ```powershell
    # Get the NICs for the HDInsight workernodes (names contain 'workernode').
    $nodes = Get-AzureRmNetworkInterface `
        -ResourceGroupName $resourceGroupName `
        | where-object {$_.Name -like "*workernode*"}

    # Loop through each node and get the IP address
    foreach($node in $nodes) {
        $node.IpConfigurations.PrivateIpAddress
    }
    ```

    このスクリプトは、`$resourceGroupName` が仮想ネットワークを含む Azure リソース グループの名前であることを前提としています。 スクリプトの出力は次のテキストのようになります。

        10.0.0.12
        10.0.0.6
        10.0.0.13
        10.0.0.5

    > [!NOTE]
    > Kafka クライアントが Kafka ブローカーではなく Zookeeper ノードを使用している場合は、PowerShell スクリプトの `*workernode*` を`*zookeepernode*` に置き換えます。

    > [!WARNING]
    > クラスターをスケーリングするか、ノードで障害が発生したために交換した場合、IP アドレスは変わる可能性があります。 現時点では、HDInsight クラスターのノードに特定の IP アドレスを事前に割り当てる方法はありません。

2. 次のコマンドを使用して、[kafka-python](http://kafka-python.readthedocs.io/) クライアントをインストールします。

        pip install kafka-python

3. データを Kafka に送信するには、次の Python コードを使用します。

  ```python
  from kafka import KafkaProducer
  # Replace the `ip_address` entries with the IP address of your worker nodes
  producer = KafkaProducer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'])
  for _ in range(50):
      producer.send('testtopic', b'test message')
  ```

    `'ip_address'` エントリは、このセクションの手順 1 で返されたアドレスに置き換えます。

    > [!NOTE]
    > このコードは、文字列 `test message` をトピック `testtopic` に送信します。 HDInsight 上の Kafka の既定の構成では、トピックが存在しない場合は作成します。

4. Kafka からメッセージを取得するには、次の Python コードを使用します。

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    `'ip_address'` エントリは、このセクションの手順 1 で返されたアドレスに置き換えます。 出力には、前の手順でプロデューサーに送信されたテスト メッセージが含まれています。

## <a name="troubleshooting"></a>トラブルシューティング

仮想ネットワークへの接続またはネットワーク経由での HDInsight の接続に問題がある場合は、[仮想ネットワーク ゲートウェイと接続のトラブルシューティング](../network-watcher/network-watcher-troubleshoot-manage-powershell.md)に関するドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

ポイント対サイト VPN ゲートウェイを使用する Azure Virtual Network の作成の詳細については、次のドキュメントを参照してください。

* [Azure Portal を使用したポイント対サイト接続の構成](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Azure PowerShell を使用したポイント対サイト接続の構成](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

HDInsight 上の Kafka の操作の詳細については、次のドキュメントを参照してください。

* [HDInsight での Kafka の使用](hdinsight-apache-kafka-get-started.md)
* [HDInsight 上の Kafka でのミラーリングの使用](hdinsight-apache-kafka-mirroring.md)

