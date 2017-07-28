---
title: "Virtual Network - Azure を使用した HDInsight の拡張 | Microsoft Docs"
description: "Azure Virtual Network を使用して HDInsight を他のクラウド リソースまたはデータ センター内のリソースに接続する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/30/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: c0091f48518174f9aa0bc8022d1cd22694f8f8e7
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Azure Virtual Network を使用した HDInsight 機能の拡張

HDInsight で Azure Virtual Network を使用して次のシナリオを有効にする方法について説明します。

* HDInsight へのアクセスを制限する。 たとえば、インターネットからの受信トラフィックを禁止します。

* インターネット経由で公開されていない HDInsight のサービスに直接アクセスする。 たとえば、直接 Kafka ブローカーを操作したり、HBase Java API を使用したりします。

* サービスを直接 HDInsight に接続する。 たとえば、Oozie を使用して、データ センター内の SQL Server にデータをインポートまたはエクスポートします。

* 複数の HDInsight クラスターが関係するソリューションを作成する。 たとえば、Spark または Storm を使用して、Kafka に保存されたデータを分析します。

## <a name="prerequisites"></a>前提条件

* Azure CLI 2.0: 詳細については、[Azure CLI 2.0 のインストールおよび構成](https://docs.microsoft.com/cli/azure/install-az-cli2)に関するページをご覧ください。

* Azure PowerShell: 詳細については、[Azure PowerShell のインストールおよび構成](/powershell/azure/overview)に関するページをご覧ください。

> [!NOTE]
> このドキュメントには、Azure CLI および Azure PowerShell の最新バージョンが必要です。 以前のバージョンを使用している場合は、コマンドが異なる場合があります。 適切に実行するためには、前のリンクを使用して最新バージョンをインストールします。

## <a id="whatis"></a>Azure Virtual Network とは

[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) によって、ソリューションに必要なリソースを含む、セキュリティで保護された永続的なネットワークを作成できます。

仮想ネットワークで HDInsight を使用する場合の考慮事項の一覧を次に示します。

* __クラシック仮想ネットワークと Resource Manager 仮想ネットワーク__: 次の表を使用して、HDInsight クラスターのオペレーティング システムに基づいて使用するネットワークの種類を決定します。

    | HDInsight オペレーティング システム | クラシック仮想ネットワーク | Resource Manager 仮想ネットワーク |
    | ---- | ---- | ---- |
    | Linux | × | ○ |
    | Windows | ○ | × |

    互換性のない仮想ネットワークのリソースにアクセスするには、2 つのネットワークを接続します。 クラシック仮想ネットワークと Resource Manager 仮想ネットワークを接続する方法の詳細については、[新しい Vnet へのクラシック VNet の接続](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)に関する記事をご覧ください。

* __カスタム DNS__: HDInsight とローカル ネットワーク内のリソースとの間で名前解決を有効にする必要がある場合は、カスタム DNS サーバーを使用する必要があります。 インターネットに公開されているリソースにアクセスする場合には、カスタム DNS サーバーは不要です。

    カスタム DNS サーバーの使用方法の詳細については、「[VM とロール インスタンスの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)」をご覧ください。

* __強制トンネリング__: HDInsight は強制トンネリングをサポートしていません。

* __制限付きの仮想ネットワーク__: HDInsight は、送受信トラフィックを制限する仮想ネットワークにインストールできます。 使用している Azure リージョンの特定の IP アドレスへのアクセスを許可する必要があります。

    * __ネットワーク セキュリティ グループ__: ネットワーク セキュリティ グループを使用する場合は、いくつかの Azure の IP アドレスへのアクセスが制限されないようにする必要があります。 IP アドレスの一覧については、「[required IP addresses (必須 IP アドレス)](#hdinsight-ip)」のセクションをご覧ください。

        詳細については、「[Network Security Groups (ネットワーク セキュリティ グループ)](#using-network-security-groups)」のセクションをご覧ください。

    * __ユーザー定義ルート__: ユーザー定義のルートを使用する場合は、いくつかの Azure の IP アドレスにルートを定義する必要があります。 IP アドレスの一覧については、「[required IP addresses (必須 IP アドレス)](#hdinsight-ip)」のセクションをご覧ください。

        詳細については「[User-defined routes (ユーザー定義のルート)](#user-defined-routes)」のセクションをご覧ください。

    * __ネットワーク仮想アプライアンス__: 仮想アプライアンス ファイアウォールを使用する場合、ファイアウォール経由で許可する必要があるポートの一覧については、「[Virtual appliance firewall (仮想アプライアンス ファイアウォール)](#virtual-appliance-firewall)」のセクションをご覧ください。

### <a name="connect-cloud-resources-together-in-a-private-network-cloud-only"></a>プライベート ネットワーク (クラウドのみ) 内でのクラウド リソース間の接続

![クラウドのみの構成の図](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

Virtual Network を使用して Azure サービスと Azure HDInsight を連携させることで、以下のシナリオが実現できるようになります。

* **HDInsight サービスまたはジョブを呼び出す** 。

* **データを直接転送する** 。

* **複数の HDInsight サーバーを結合して単一のソリューションにする。** HDInsight クラスターにはさまざまな種類があり、それぞれ適したワークロードやテクノロジに対応しています。 複数の種類 (Storm と HBase など) を組み合わせたクラスターを作成することはできません。 仮想ネットワークを使用することで、複数のクラスターが互いに直接通信することができます。

### <a name="connect-cloud-resources-to-a-local-datacenter-network"></a>クラウド リソースのローカル データ センター ネットワークへの接続

サイト間構成では、データセンターから複数のリソースを Azure Virtual Network に接続できます。 この接続は、ハードウェア VPN デバイスを使用するか、ルーティングとリモート アクセス サービスを使用して行います。

![サイト間構成の図](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

ポイント対サイト構成では、ソフトウェア VPN を使用して、特定のリソースを Azure Virtual Network に接続できます。

![ポイント対サイト構成の図](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

Virtual Network を使用してクラウドとデータ センターをリンクすると、クラウドのみの構成と同様のシナリオを実現できます。 ただし、クラウド内のリソースの操作に制限されるのではなく、データ センターのリソースも使用できます。

* **データを直接転送する** 。 たとえば、Sqoop を使用して SQL Server に、または SQL Server からデータを転送したり、基幹業務 (LOB) アプリケーションで生成されたデータを読み取ったりします。

* **LOB アプリケーションから HDInsight サービスを呼び出す。** たとえば、HBase Java API を使用して HDInsight HBase クラスターにデータを格納したり、クラスターからデータを取得したりします。

Virtual Network の機能、利点の詳細については、「 [Azure 仮想ネットワークの概要](../virtual-network/virtual-networks-overview.md)」を参照してください。

> [!NOTE]
> HDInsight クラスターをプロビジョニングする前に、Azure Virtual Network を作成します。その後、クラスターを作成するときにネットワークを指定します。 カスタム DNS サーバーの使用を予定している場合は、それを HDInsight よりも先に仮想ネットワークに追加する必要があります。 詳細については、「 [仮想ネットワークの構成タスク](https://azure.microsoft.com/documentation/services/virtual-network/)」を参照してください。

##<a id="hdinsight-ip"></a>必須 IP アドレス

HDInsight サービスは管理されたサービスで、プロビジョニング中や実行中に Azure 管理サービスへのアクセスが必要です。 Azure 管理では、次のサービスを実行します。

* クラスターの正常性の監視
* クラスター リソースのフェールオーバーの開始
* スケーリング操作によるクラスター内のノード数の変更
* その他の管理タスク

> [!NOTE]
> これらの操作には、インターネットへのフル アクセスは必要ありません。 インターネット アクセスを制限する場合は、次の IP アドレスのポート 443 上で着信アクセスを許可します。 これにより、Azure が HDInsight を管理できるようになります。

仮想ネットワークへのアクセスを制限する場合は、管理 IP アドレスへのアクセスを許可する必要があります。 許可される IP アドレスは、HDInsight クラスターと Virtual Network が存在するリージョンに固有である必要があります。 次の表を参照して、使用しているリージョンに適した IP アドレスを見つけてください。

| Country (国) | リージョン | 許可された IP アドレス | 許可されたポート |
| ---- | ---- | ---- | ---- |
| アジア | 東アジア | 23.102.235.122</br>52.175.38.134 | 443 |
| ブラジル | ブラジル南部 | 191.235.84.104</br>191.235.87.113 | 443 |
| カナダ | カナダ東部 | 52.229.127.96</br>52.229.123.172 | 443 |
| &nbsp; | カナダ中部 | 52.228.37.66</br>52.228.45.222 | 443 |
| 中国 | 中国 (北部) | 42.159.96.170</br>139.217.2.219 | 443 |
| &nbsp; | 中国 (東部) | 42.159.198.178</br>42.159.234.157 | 443 |
| ドイツ | ドイツ中部 | 51.4.146.68</br>51.4.146.80 | 443 |
| &nbsp; | ドイツ北東部 | 51.5.150.132</br>51.5.144.101 | 443 |
| インド | インド中部 | 52.172.153.209</br>52.172.152.49 | 443 |
| 日本 | 東日本 | 13.78.125.90</br>13.78.89.60 | 443 |
| &nbsp; | 西日本 | 40.74.125.69</br>138.91.29.150 | 443 |
| 英国 | 英国西部 | 51.141.13.110</br>51.141.7.20 | 443 |
| &nbsp; | 英国南部 | 51.140.47.39</br>51.140.52.16 | 443 |
| 米国 | 米国中央部 | 13.67.223.215</br>40.86.83.253 | 443 |
| &nbsp; | 米国中西部 | 52.161.23.15</br>52.161.10.167 | 443 |
| &nbsp; | 米国西部 2 | 52.175.211.210</br>52.175.222.222 | 443 |

Azure Government に使用する IP アドレスについては、「[Azure Government Intelligence + Analytics (Azure Governemnt Intelligence + Analytics)](../azure-government/documentation-government-services-intelligenceandanalytics.md)」をご覧ください。

__使用しているリージョンが表にない場合は__、次の IP アドレスでポート __443__ にトラフィックを許可します。

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> HDInsight では、送信トラフィックの制限をサポートしていません。受信トラフィックのみをサポートしています。 HDInsight が含まれているサブネットのネットワーク セキュリティ グループ規則を定義するときは、__受信規則だけを使用します__。

> [!NOTE]
> 仮想ネットワークでカスタム DNS サーバーを使用する場合は、__168.63.129.16__ からのアクセスを許可する必要もあります。 これは、Azure の再帰的リゾルバーのアドレスです。 詳細については、「[Name resolution for VMs and Role instances (VM とロール インスタンスの名前解決)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)」をご覧ください。

## <a name="network-security-groups"></a>ネットワーク セキュリティ グループ

ネットワーク セキュリティ グループ (NSG) を使用してインターネット アクセスをブロックすると、通常はクラスターのパブリック ゲートウェイを介して公開される HDInsight サービスを利用できません。 これには Ambari と SSH が含まれます。 代わりに、クラスターのヘッド ノードの内部 IP アドレスを使用してサービスにアクセスする必要があります。

ヘッド ノードの内部 IP アドレスを検索するには、「[Internal IPs and FQDNs](#internal-ips-and-fqdns)」のセクションにあるスクリプトを使用します。

### <a name="example-secured-virtual-network"></a>例: セキュリティで保護された仮想ネットワーク

次の例では、次の IP アドレスからのポート 443 での受信トラフィックを許可するネットワーク セキュリティ グループを作成する方法を示します。

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> これらのアドレスは、示されている特定の IP アドレスがないリージョン用です。 リージョンの IP アドレスを検索するには、「[セキュリティで保護された仮想ネットワーク](#secured-virtual-networks)」のセクションの情報を参照してください。

これらの手順では、HDInsight をインストールする Virtual Network とサブネットを既に作成していることを前提としています。 「[Azure Portal を使用した仮想ネットワークの作成](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)」をご覧ください。

> [!WARNING]
> __優先順位__の高い順に、ネットワーク トラフィックに対するルールのテストが行われます。 1 つのルールがテスト条件に一致するとそのルールが適用され、その要求に関してルールのテストはそれ以上行われません。 受信トラフィックを広範囲にブロックするルールがある場合 (**すべて拒否**ルールなど)、そのルールはトラフィックを許可するルールの後に来る__必要があります__。
>
> ネットワーク セキュリティ グループ ルールの詳細については、「[What is a Network Security Group](../virtual-network/virtual-networks-nsg.md)」に関するドキュメントをご覧ください。

**例: Azure リソース管理テンプレート**

[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/)から次のリソース管理テンプレートを使用して、ネットワーク構成がセキュリティ保護された VNet で HDInsight クラスターを作成します。

[Deploy a secured Azure VNet and an HDInsight Hadoop cluster within the VNet (セキュリティで保護された Azure VNet と HDInsight Hadoop クラスターを VNet 内にデプロイする)](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

**例: Azure PowerShell**

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that HDInsight will be installed into"
# Get the Virtual Network object
$vnet = Get-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName
# Get the region the Virtual network is in.
$location = $vnet.Location
# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzureRmNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound
# Set the changes to the security group
Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
# Apply the NSG to the subnet
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

**例: Azure CLI**

1. 次のコマンドを使用して、 `hdisecure`という名前の新しいネットワーク セキュリティ グループを作成します。 **RESOURCEGROUPNAME** を、Azure Virtual Network が含まれているリソース グループに置き換えます。 **LOCATION** を、グループが作成された場所 (リージョン) に置き換えます。

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    グループが作成されたら、新しいグループに関する情報が表示されます。

2. ポート 443 上で Azure HDInsight の正常性と管理サービスからのインバウンド通信を許可する新しいネットワーク セキュリティ グループにルールを追加するには、次を使用します。 **RESOURCEGROUPNAME** を、Azure Virtual Network が含まれているリソース グループの名前に置き換えます。

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    ```

3. ルールが作成されたら、次を使用してこのネットワーク セキュリティ グループの一意の識別子を取得します。

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    このコマンドにより、次のテキストのような値が返されます。

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    期待どおりの結果が得られない場合は、コマンド内の ID を二重引用符で囲んでください。

4. 次のコマンドを使用して、ネットワーク セキュリティ グループをサブネットに適用します。 __GUID__ と __RESOURCEGROUPNAME__ の値を、前の手順で返された値に置き換えます。 __VNETNAME__ と __SUBNETNAME__ を、HDInsight クラスターの作成時に使用する仮想ネットワーク名とサブネット名に置き換えます。

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    このコマンドが完了すると、これらの手順で使用されるサブネット上のセキュリティで保護された Virtual Network に、HDInsight を正常にインストールできます。

> [!IMPORTANT]
> 上記の手順を使用すると、Azure クラウドの HDInsight 正常性および管理サービスへのアクセスのみが開きます。 それ以外の Virtual Network 外から HDInsight クラスターへのアクセスはすべてブロックされます。 仮想ネットワーク外からのアクセスを有効にする場合は、追加のネットワーク セキュリティ グループ ルールを追加する必要があります。
>
> 次の例では、インターネットからの SSH アクセスを可能にする方法を示します。
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound
> ```
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
> ```

ネットワーク セキュリティ グループの詳細については、 [ネットワーク セキュリティ グループの概要](../virtual-network/virtual-networks-nsg.md)に関する記事を参照してください。 Azure Virtual Network におけるルーティングの制御については、「[ユーザー定義のルートと IP 転送](../virtual-network/virtual-networks-udr-overview.md)」をご覧ください。

## <a name="user-defined-routes"></a>ユーザー定義のルート

ユーザー定義ルート (UDR) を使用して仮想ネットワークを保護する場合は、ご利用のリージョンの HDInsight 管理 IP アドレスのルートを追加する必要があります。 リージョン別の IP アドレスの一覧については、「[Required IP addresses (必須 IP アドレス)](#hdinsight-ip)」のセクションをご覧ください。

必要な IP アドレスへのルートは、__次ホップ__の種類を__インターネット__に設定する必要があります。 次の図は、Azure Portal 上でルートがどのように表示されるかの例を示しています。

![HDInsight で必要な IP アドレスのユーザー定義ルート](./media/hdinsight-extend-hadoop-virtual-network/user-defined-routes-portal.png)

ユーザー定義ルートの詳細については、「[user-defined routes and IP forwarding (ユーザー定義ルートと IP 転送)](../virtual-network/virtual-networks-udr-overview.md)」をご覧ください。

## <a name="virtual-appliance-firewall"></a>仮想アプライアンス ファイアウォール

仮想アプライアンスのファイアウォールを使用して仮想ネットワークを保護している場合は、次のポートの送信トラフィックを許可する必要があります。

* 53
* 443
* 1433
* 11000 ～ 11999
* 14000 ～ 14999

仮想アプライアンスのファイアウォール ルールの詳細については、「[virtual appliance scenario (仮想アプライアンス シナリオ)](../virtual-network/virtual-network-scenario-udr-gw-nva.md)」をご覧ください。

## <a name="forced-tunneling"></a>強制トンネリング

HDInsight では、強制トンネリングはサポートされていません。

## <a name="retrieve-internal-ips-and-fqdns"></a>内部 IP アドレスと FQDN の取得

仮想ネットワークを使用して HDInsight に接続するときに、クラスター内のノードに直接接続することができます。 次のスクリプトを使用して、クラスター内のノードの内部 IP アドレスと完全修飾ドメイン名 (FQDN) を判別します。

**Azure PowerShell**

```powershell
$resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

__Azure CLI__

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

> [!IMPORTANT]
> Azure CLI 2.0 の例で、`<resourcegroupname>` を仮想ネットワークを含むリソース グループの名前に置き換えます。

スクリプトは、クラスターの仮想ネットワーク インターフェイス カード (NIC) を照会することによって機能します。 NIC は、HDInsight が使用する仮想ネットワークを含むリソース グループに存在します。

## <a id="nextsteps"></a>次のステップ

次の例は、Azure Virtual Network で HDInsight を使用する方法を示しています。

* [Azure Virtual Network の HBase クラスター](hdinsight-hbase-provision-vnet.md)

* [HDInsight での Storm と HBase を使用したセンサー データの分析](hdinsight-storm-sensor-data-analysis.md)

* [HDInsight での Hadoop クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)

* [HDInsight の Hadoop での Sqoop の使用](hdinsight-use-sqoop-mac-linux.md)

Azure のかそうネットワークの詳細については、 [Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)に関するページを参照してください。


