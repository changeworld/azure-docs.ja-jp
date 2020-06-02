---
title: Azure Files ネットワーク エンドポイントの構成 | Microsoft Docs
description: Azure Files のネットワーク オプションの概要。
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4695164e7bcbc63b852f2f4364cdccbc8ea7d8c4
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849316"
---
# <a name="configuring-azure-files-network-endpoints"></a>Azure Files ネットワーク エンドポイントの構成
Azure Files では、Azure ファイル共有にアクセスするための次の主な 2 種類のエンドポイントが提供されます。 
- パブリック エンドポイント。パブリック IP アドレスを持ち、世界中のどこからでもアクセスできます。
- プライベート エンドポイント。仮想ネットワーク内に存在し、その仮想ネットワークのアドレス空間内からのプライベート IP アドレスを持ちます。

パブリックおよびプライベート エンドポイントは、Azure ストレージ アカウントに存在します。 ストレージ アカウントは、複数のファイル共有だけでなく、BLOB コンテナーやキューなどのその他のストレージ リソースをデプロイできるストレージの共有プールを表す管理構造です。

この記事では、Azure ファイル共有に直接アクセスするためのストレージ アカウントのエンドポイントを構成する方法について説明します。 このドキュメントに記載されている詳細のほとんどは、Azure File Sync とストレージ アカウントのパブリック エンドポイントおよびプライベート エンドポイントとの相互運用方法にも適用されますが、Azure File Sync のデプロイに対するネットワークに関する考慮事項の詳細については、[Azure File Sync プロキシとファイアウォールの設定の構成](storage-sync-files-firewall-and-proxy.md)に関する記事を参照してください。

このハウツー ガイドを読む前に、「[Azure Files のネットワークに関する考慮事項](storage-files-networking-overview.md)」を読むことをお勧めします。

## <a name="prerequisites"></a>前提条件
- この記事では、既に Azure サブスクリプションが作成されていることを前提としています。 サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。
- この記事では、オンプレミスから接続するストレージ アカウントに Azure ファイル共有が既に作成されていることを前提としています。 Azure ファイル共有を作成する方法については、「[Azure ファイル共有を作成する](storage-how-to-create-file-share.md)」をご覧ください。
- Azure PowerShell を使用する場合は、[最新バージョンをインストールしてください](https://docs.microsoft.com/powershell/azure/install-az-ps)。
- Azure CLI を使用する場合は、[最新バージョンをインストールしてください](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-a-private-endpoint"></a>プライベート エンドポイントの作成
ストレージ アカウント用のプライベート エンドポイントを作成すると、次の Azure リソースがデプロイされます。

- **プライベート エンドポイント**: ストレージ アカウントのプライベート エンドポイントを表す Azure リソース。 これは、ストレージ アカウントとネットワーク インターフェイスを接続するリソースと考えることができます。
- **ネットワーク インターフェイス (NIC)** : 指定された仮想ネットワークおよびサブネット内のプライベート IP アドレスを保持するネットワーク インターフェイス。 これは、仮想マシンをデプロイするとデプロイされるリソースとまったく同じものですが、VM に割り当てられるのではなく、プライベート エンドポイントによって所有されます。
- **プライベート DNS ゾーン**: 以前にこの仮想ネットワークに対してプライベート エンドポイントをデプロイしたことがない場合は、新しいプライベート DNS ゾーンが仮想ネットワークにデプロイされます。 この DNS ゾーン内のストレージ アカウントに対しては、DNS A レコードも作成されます。 この仮想ネットワークにプライベート エンドポイントを既にデプロイしてある場合は、ストレージ アカウントに対する新しい A レコードが、既存の DNS ゾーンに追加されます。 DNS ゾーンのデプロイは省略できますが、強くお勧めします。また、AD サービス プリンシパルまたは FileREST API を使用して Azure ファイル共有をマウントする場合は、必須です。

> [!Note]  
> この記事では、Azure パブリック リージョンを対象に、ストレージ アカウントの DNS サフィックス `core.windows.net` を使用しています。 この解説は、Azure ソブリン クラウド (Azure US Government クラウドや Azure China クラウドなど) にも当てはまります。ご利用の環境の適切なサフィックスに置き換えてください。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)
プライベート エンドポイントを作成するストレージ アカウントに移動します。 ストレージ アカウントの目次で **[プライベート エンドポイント接続]** を選択した後、 **[+ プライベート エンドポイント]** を選択して、新しいプライベート エンドポイントを作成します。 

![ストレージ アカウントの目次にある [プライベート エンドポイント接続] 項目のスクリーンショット](media/storage-files-networking-endpoints/create-private-endpoint-0.png)

表示されるウィザードには、設定するページが複数あります。

**[基本]** ブレードで、プライベート エンドポイントの目的のリソース グループ、名前、リージョンを選択します。 これらには任意の内容を指定でき、ストレージ アカウントと一致している必要はまったくありませんが、プライベート エンドポイントは、そのプライベート エンドポイントを作成しようとしている仮想ネットワークと同じリージョン内に作成する必要があります。

![[プライベート エンドポイントの作成] セクションの [基本] セクションのスクリーンショット](media/storage-files-networking-endpoints/create-private-endpoint-1.png)

**[リソース]** ブレードで、 **[マイ ディレクトリ内の Azure リソースに接続します]** のラジオ ボタンを選択します。 **[リソースの種類]** で、リソースの種類として **[Microsoft.Storage/storageAccounts]** を選択します。 **[リソース]** フィールドは、接続先の Azure ファイル共有を含むストレージ アカウントです。 これは Azure Files 向けのため、ターゲット サブリソースは **[ファイル]** です。

**[構成]** ブレードでは、プライベート エンドポイントを追加する特定の仮想ネットワークとサブネットを選択できます。 前に作成した仮想ネットワークを選択します。 上でサービス エンドポイントを追加したサブネットから個別のサブネットを選択する必要があります。 [構成] ブレードには、プライベート DNS ゾーンを作成または更新するための情報も含まれています。 既定の `privatelink.file.core.windows.net` ゾーンを使用することをお勧めします。

![[構成] セクションのスクリーンショット](media/storage-files-networking-endpoints/create-private-endpoint-2.png)

**[確認および作成]** をクリックしてプライベート エンドポイントを作成します。 

仮想ネットワーク内に仮想マシンがある場合、または[こちら](storage-files-networking-dns.md)で説明されているような DNS 転送を構成してある場合は、PowerShell、コマンド ライン、またはターミナル (Windows、Linux、または macOS で動作) から次のコマンドを実行して、プライベート エンドポイントが正しくセットアップされていることをテストできます。 `<storage-account-name>` を適切なストレージ アカウント名に置き換える必要があります。

```
nslookup <storage-account-name>.file.core.windows.net
```

すべてが正常に動作した場合は、次のような出力が表示されます。`192.168.0.5` は、仮想ネットワーク内のプライベート エンドポイントのプライベート IP アドレスです (Windows の場合に表示される出力)。

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
ストレージ アカウント用のプライベート エンドポイントを作成するには、まず、プライベート エンドポイントを追加するストレージ アカウントと仮想ネットワーク サブネットへの参照を取得する必要があります。 `<storage-account-resource-group-name>`、`<storage-account-name>`、`<vnet-resource-group-name>`、`<vnet-name>`、および `<vnet-subnet-name>` を、次のように置き換えます。

```PowerShell
$storageAccountResourceGroupName = "<storage-account-resource-group-name>"
$storageAccountName = "<storage-account-name>"
$virtualNetworkResourceGroupName = "<vnet-resource-group-name>"
$virtualNetworkName = "<vnet-name>"
$subnetName = "<vnet-subnet-name>"

# Get storage account reference, and throw error if it doesn't exist
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name $storageAccountName `
        -ErrorAction SilentlyContinue

if ($null -eq $storageAccount) {
    $errorMessage = "Storage account $storageAccountName not found "
    $errorMessage += "in resource group $storageAccountResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}

# Get virtual network reference, and throw error if it doesn't exist
$virtualNetwork = Get-AzVirtualNetwork `
        -ResourceGroupName $virtualNetworkResourceGroupName `
        -Name $virtualNetworkName `
        -ErrorAction SilentlyContinue

if ($null -eq $virtualNetwork) {
    $errorMessage = "Virtual network $virtualNetworkName not found "
    $errorMessage += "in resource group $virtualNetworkResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}

# Get reference to virtual network subnet, and throw error if it doesn't exist
$subnet = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq $subnetName }

if ($null -eq $subnet) {
    Write-Error `
            -Message "Subnet $subnetName not found in virtual network $virtualNetworkName." `
            -ErrorAction Stop
}
```

プライベート エンドポイントを作成するには、ストレージ アカウントに対するプライベート リンク サービス接続を作成する必要があります。 プライベート リンク サービス接続は、プライベート エンドポイントの作成に対する入力です。 

```PowerShell
# Disable private endpoint network policies
$subnet.PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork | Out-Null

# Create a private link service connection to the storage account.
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name "$storageAccountName-Connection" `
        -PrivateLinkServiceId $storageAccount.Id `
        -GroupId "file"

# Create a new private endpoint.
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name "$storageAccountName-PrivateEndpoint" `
        -Location $virtualNetwork.Location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -ErrorAction Stop
```

Azure プライベート DNS ゾーンを作成すると、ストレージ アカウントの元の名前 (`storageaccount.file.core.windows.net` など) を仮想ネットワーク内のプライベート IP アドレスに解決できます。 プライベート エンドポイントの作成の観点からは省略可能ですが、AD ユーザー プリンシパルを使用して Azure ファイル共有をマウントしたり、REST API 経由でアクセスしたりする場合は、明示的に必要になります。  

```PowerShell
# Get the desired storage account suffix (core.windows.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
$storageAccountSuffix = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

# For public cloud, this will generate the following DNS suffix:
# privatelink.file.core.windows.net.
$dnsZoneName = "privatelink.file.$storageAccountSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
$dnsZone = Get-AzPrivateDnsZone | `
    Where-Object { $_.Name -eq $dnsZoneName } | `
    Where-Object {
        $privateDnsLink = Get-AzPrivateDnsVirtualNetworkLink `
                -ResourceGroupName $_.ResourceGroupName `
                -ZoneName $_.Name `
                -ErrorAction SilentlyContinue
        
        $privateDnsLink.VirtualNetworkId -eq $virtualNetwork.Id
    }

if ($null -eq $dnsZone) {
    # No matching DNS zone attached to virtual network, so create new one.
    $dnsZone = New-AzPrivateDnsZone `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -Name $dnsZoneName `
            -ErrorAction Stop

    $privateDnsLink = New-AzPrivateDnsVirtualNetworkLink `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -ZoneName $dnsZoneName `
            -Name "$virtualNetworkName-DnsLink" `
            -VirtualNetworkId $virtualNetwork.Id `
            -ErrorAction Stop
}
```

これで、プライベート DNS ゾーンへの参照を用意できたので、ストレージ アカウントの A レコードを作成する必要があります。

```PowerShell
$privateEndpointIP = $privateEndpoint | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object @{ 
        Name = "NetworkInterfaces"; 
        Expression = { Get-AzNetworkInterface -ResourceId $_.Id } 
    } | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty IpConfigurations | `
    Select-Object -ExpandProperty PrivateIpAddress

$privateDnsRecordConfig = New-AzPrivateDnsRecordConfig `
        -IPv4Address $privateEndpointIP

New-AzPrivateDnsRecordSet `
        -ResourceGroupName $virtualNetworkResourceGroupName `
        -Name $storageAccountName `
        -RecordType A `
        -ZoneName $dnsZoneName `
        -Ttl 600 `
        -PrivateDnsRecords $privateDnsRecordConfig `
        -ErrorAction Stop | `
    Out-Null
```

仮想ネットワーク内に仮想マシンがある場合、または[こちら](storage-files-networking-dns.md)で説明されているような DNS 転送を構成してある場合は、次のコマンドを使用して、プライベート エンドポイントが正しくセットアップされていることをテストできます。

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

すべてが正常に動作した場合は、次のような出力が表示されます。`192.168.0.5` は、仮想ネットワーク内のプライベート エンドポイントのプライベート IP アドレスです。

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
ストレージ アカウント用のプライベート エンドポイントを作成するには、まず、プライベート エンドポイントを追加するストレージ アカウントと仮想ネットワーク サブネットへの参照を取得する必要があります。 `<storage-account-resource-group-name>`、`<storage-account-name>`、`<vnet-resource-group-name>`、`<vnet-name>`、および `<vnet-subnet-name>` を、次のように置き換えます。

```bash
storageAccountResourceGroupName="<storage-account-resource-group-name>"
storageAccountName="<storage-account-name>"
virtualNetworkResourceGroupName="<vnet-resource-group-name>"
virtualNetworkName="<vnet-name>"
subnetName="<vnet-subnet-name>"

# Get storage account ID 
storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

# Get virtual network ID
virtualNetwork=$(az network vnet show \
        --resource-group $virtualNetworkResourceGroupName \
        --name $virtualNetworkName \
        --query "id" | \
    tr -d '"')

# Get subnet ID
subnet=$(az network vnet subnet show \
        --resource-group $virtualNetworkResourceGroupName \
        --vnet-name $virtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

プライベート エンドポイントを作成するには、まず、サブネットのプライベート エンドポイント ネットワーク ポリシーが無効に設定されていることを確認する必要があります。 次に、`az network private-endpoint create` コマンドを使用して、プライベート エンドポイントを作成できます

```bash
# Disable private endpoint network policies
az network vnet subnet update \
        --ids $subnet \
        --disable-private-endpoint-network-policies \
        --output none

# Get virtual network location
region=$(az network vnet show \
        --ids $virtualNetwork \
        --query "location" | \
    tr -d '"')

# Create a private endpoint
privateEndpoint=$(az network private-endpoint create \
        --resource-group $virtualNetworkResourceGroupName \
        --name "$storageAccountName-PrivateEndpoint" \
        --location $region \
        --subnet $subnet \
        --private-connection-resource-id $storageAccount \
        --group-ids "file" \
        --connection-name "$storageAccountName-Connection" \
        --query "id" | \
    tr -d '"')
```

Azure プライベート DNS ゾーンを作成すると、ストレージ アカウントの元の名前 (`storageaccount.file.core.windows.net` など) を仮想ネットワーク内のプライベート IP アドレスに解決できます。 プライベート エンドポイントの作成の観点からは省略可能ですが、AD ユーザー プリンシパルを使用して Azure ファイル共有をマウントしたり、REST API 経由でアクセスしたりする場合は、明示的に必要になります。  

```bash
# Get the desired storage account suffix (core.windows.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
storageAccountSuffix=$(az cloud show \
        --query "suffixes.storageEndpoint" | \
    tr -d '"')

# For public cloud, this will generate the following DNS suffix:
# privatelink.file.core.windows.net.
dnsZoneName="privatelink.file.$storageAccountSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
possibleDnsZones=$(az network private-dns zone list \
        --query "[?name == '$dnsZoneName'].id" \
        --output tsv)

for possibleDnsZone in $possibleDnsZones
do
    possibleResourceGroupName=$(az resource show \
            --ids $possibleDnsZone \
            --query "resourceGroup" | \
        tr -d '"')
    
    link=$(az network private-dns link vnet list \
            --resource-group $possibleResourceGroupName \
            --zone-name $dnsZoneName \
            --query "[?virtualNetwork.id == '$virtualNetwork'].id" \
            --output tsv)
    
    if [ -z $link ]
    then
        1 > /dev/null
    else 
        dnsZoneResourceGroup=$possibleResourceGroupName
        dnsZone=$possibleDnsZone
        break
    fi  
done

if [ -z $dnsZone ]
then
    # No matching DNS zone attached to virtual network, so create a new one
    dnsZone=$(az network private-dns zone create \
            --resource-group $virtualNetworkResourceGroupName \
            --name $dnsZoneName \
            --query "id" | \
        tr -d '"')
    
    az network private-dns link vnet create \
            --resource-group $resourceGroupName \
            --zone-name $zoneName \
            --name "$virtualNetworkName-DnsLink" \
            --virtual-network $virtualNetwork \
            --registration-enabled false \
            --output none
fi
```

これで、プライベート DNS ゾーンへの参照を用意できたので、ストレージ アカウントの A レコードを作成する必要があります。

```bash
privateEndpointNIC=$(az network private-endpoint show \
        --ids $privateEndpoint \
        --query "networkInterfaces[0].id" | \
    tr -d '"')

privateEndpointIP=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[0].privateIpAddress" | \
    tr -d '"')

az network private-dns record-set a create \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --name $storageAccountName \
        --output none

az network private-dns record-set a add-record \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --record-set-name $storageAccountName \
        --ipv4-address $privateEndpointIP \
        --output none
```

仮想ネットワーク内に仮想マシンがある場合、または[こちら](storage-files-networking-dns.md)で説明されているような DNS 転送を構成してある場合は、次のコマンドを使用して、プライベート エンドポイントが正しくセットアップされていることをテストできます。

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

すべてが正常に動作した場合は、次のような出力が表示されます。`192.168.0.5` は、仮想ネットワーク内のプライベート エンドポイントのプライベート IP アドレスです。 ファイル共有を考慮するには、privatelink パスではなく、storageaccount.file.core.windows.net を引き続き使用する必要があることに注意してください。

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

## <a name="restrict-access-to-the-public-endpoint"></a>パブリック エンドポイントへのアクセスを制限する
ストレージ アカウントのファイアウォール設定を使用して、パブリック エンドポイントへのアクセスを制限できます。 一般に、ストレージ アカウントのほとんどのファイアウォール ポリシーではネットワーク アクセスが 1 つ以上の仮想ネットワークに制限されます。 ストレージ アカウントへのアクセスを仮想ネットワークに制限するには、次の 2 つの方法があります。

- [ストレージ アカウントに 1 つ以上のプライベート エンドポイントを作成](#create-a-private-endpoint)し、パブリック エンドポイントへのアクセスをすべて制限します。 これで、そのストレージ アカウント内の Azure ファイル共有にアクセスできるのは、特定の仮想ネットワーク内から送信されたトラフィックだけになります。
- パブリック エンドポイントを 1 つ以上の仮想ネットワークに制限します。 これは、"*サービス エンドポイント*" と呼ばれる仮想ネットワークの機能を使用して実現できます。 ストレージ アカウントへのトラフィックをサービス エンドポイントを使用して制限していても、ストレージ アカウントへのアクセスには、やはりパブリック IP アドレスを使用します。

### <a name="restrict-all-access-to-the-public-endpoint"></a>パブリック エンドポイントへのすべてのアクセスを制限する
パブリック エンドポイントへのすべてのアクセスが制限されている場合でも、そのプライベート エンドポイントを介してストレージ アカウントにアクセスできます。 それ以外の場合、ストレージ アカウントのパブリック エンドポイントに対する有効な要求は拒否されます。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)
パブリック エンドポイントへのすべてのアクセスを制限するストレージ アカウントに移動します。 ストレージ アカウントの目次で、 **[ファイアウォールと仮想ネットワーク]** を選択します。

ページの上部で、 **[選択されたネットワーク]** ラジオ ボタンを選択します。 これにより、パブリック エンドポイントの制限を制御するためのいくつかの設定が表示されるようになります。 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** をオンにして、Azure File Sync などの信頼できるファーストパーティの Microsoft サービスに、ストレージ アカウントへのアクセスを許可します。

![適切な制限が設定されている [ファイアウォールと仮想ネットワーク] ブレードのスクリーンショット](media/storage-files-networking-endpoints/restrict-public-endpoint-0.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
次の PowerShell コマンドを使用すると、ストレージ アカウントのパブリック エンドポイントに対するすべてのトラフィックが拒否されます。 このコマンドでは、`-Bypass` パラメーターが `AzureServices` に設定されていることに注意してください。 これにより、Azure File Sync などの信頼できるファーストパーティのサービスが、パブリック エンドポイント経由でストレージ アカウントにアクセスできるようになります。

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
次の CLI コマンドを使用すると、ストレージ アカウントのパブリック エンドポイントに対するすべてのトラフィックが拒否されます。 このコマンドでは、`-bypass` パラメーターが `AzureServices` に設定されていることに注意してください。 これにより、Azure File Sync などの信頼できるファーストパーティのサービスが、パブリック エンドポイント経由でストレージ アカウントにアクセスできるようになります。

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```
---

### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>パブリック エンドポイントへのアクセスを特定の仮想ネットワークに制限する
ストレージ アカウントを特定の仮想ネットワークに制限すると、指定した仮想ネットワーク内からパブリック エンドポイントへの要求が許可されます。 これは、"*サービス エンドポイント*" と呼ばれる仮想ネットワークの機能を使用して実現できます。 これは、プライベート エンドポイントの有無に関係なく使用できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
パブリック エンドポイントを特定の仮想ネットワークに制限するストレージ アカウントに移動します。 ストレージ アカウントの目次で、 **[ファイアウォールと仮想ネットワーク]** を選択します。 

ページの上部で、 **[選択されたネットワーク]** ラジオ ボタンを選択します。 これにより、パブリック エンドポイントの制限を制御するためのいくつかの設定が表示されるようになります。 **[+ 既存の仮想ネットワークを追加]** をクリックして、パブリック エンドポイント経由でのストレージ アカウントへのアクセスを許可する特定の仮想ネットワークを選択します。 そのためには、仮想ネットワークと、その仮想ネットワークのサブネットを選択する必要があります。 

**[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** をオンにして、Azure File Sync などの信頼できるファーストパーティの Microsoft サービスに、ストレージ アカウントへのアクセスを許可します。

![特定の仮想ネットワークがパブリック エンドポイント経由でストレージ アカウントへのアクセスを許可されている [ファイアウォールと仮想ネットワーク] ブレードのスクリーンショット](media/storage-files-networking-endpoints/restrict-public-endpoint-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
サービス エンドポイントを使用して、ストレージ アカウントのパブリック エンドポイントへのアクセスを特定の仮想ネットワークに制限するには、まず、ストレージ アカウントと仮想ネットワークに関する情報を収集する必要があります。 この情報を収集するには、`<storage-account-resource-group>`、`<storage-account-name>`、`<vnet-resource-group-name>`、`<vnet-name>`、`<subnet-name>` を指定します。

```PowerShell
$storageAccountResourceGroupName = "<storage-account-resource-group>"
$storageAccountName = "<storage-account-name>"
$restrictToVirtualNetworkResourceGroupName = "<vnet-resource-group-name>"
$restrictToVirtualNetworkName = "<vnet-name>"
$subnetName = "<subnet-name>"

$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name $storageAccountName `
        -ErrorAction Stop

$virtualNetwork = Get-AzVirtualNetwork `
        -ResourceGroupName $restrictToVirtualNetworkResourceGroupName `
        -Name $restrictToVirtualNetworkName `
        -ErrorAction Stop

$subnet = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq $subnetName }

if ($null -eq $subnet) {
    Write-Error `
            -Message "Subnet $subnetName not found in virtual network $restrictToVirtualNetworkName." `
            -ErrorAction Stop
}
```

仮想ネットワークからのトラフィックが、ストレージ アカウントのパブリック エンドポイントへのアクセスを Azure ネットワーク ファブリックによって許可されるためには、仮想ネットワークのサブネットで `Microsoft.Storage` サービス エンドポイントが公開されている必要があります。 次の PowerShell コマンドを実行すると、まだ存在しない場合は、`Microsoft.Storage` サービス エンドポイントがサブネットに追加されます。

```PowerShell
$serviceEndpoints = $subnet | `
    Select-Object -ExpandProperty ServiceEndpoints | `
    Select-Object -ExpandProperty Service

if ($serviceEndpoints -notcontains "Microsoft.Storage") {
    if ($null -eq $serviceEndpoints) {
        $serviceEndpoints = @("Microsoft.Storage")
    } elseif ($serviceEndpoints -is [string]) {
        $serviceEndpoints = @($serviceEndpoints, "Microsoft.Storage")
    } else {
        $serviceEndpoints += "Microsoft.Storage"
    }

    $virtualNetwork = $virtualNetwork | Set-AzVirtualNetworkSubnetConfig `
            -Name $subnetName `
            -AddressPrefix $subnet.AddressPrefix `
            -ServiceEndpoint $serviceEndpoints `
            -WarningAction SilentlyContinue `
            -ErrorAction Stop | `
        Set-AzVirtualNetwork `
            -ErrorAction Stop
}
```

ストレージ アカウントへのトラフィックを制限する最後のステップでは、ネットワーク ルールを作成し、ストレージ アカウントのネットワーク ルール セットに追加します。

```PowerShell
$networkRule = $storageAccount | Add-AzStorageAccountNetworkRule `
    -VirtualNetworkResourceId $subnet.Id `
    -ErrorAction Stop

$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -VirtualNetworkRule $networkRule `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
サービス エンドポイントを使用して、ストレージ アカウントのパブリック エンドポイントへのアクセスを特定の仮想ネットワークに制限するには、まず、ストレージ アカウントと仮想ネットワークに関する情報を収集する必要があります。 この情報を収集するには、`<storage-account-resource-group>`、`<storage-account-name>`、`<vnet-resource-group-name>`、`<vnet-name>`、`<subnet-name>` を指定します。

```bash
storageAccountResourceGroupName="<storage-account-resource-group>"
storageAccountName="<storage-account-name>"
restrictToVirtualNetworkResourceGroupName="<vnet-resource-group-name>"
restrictToVirtualNetworkName="<vnet-name>"
subnetName="<subnet-name>"

storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

virtualNetwork=$(az network vnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --name $restrictToVirtualNetworkName \
        --query "id" | \
    tr -d '"')

subnet=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

仮想ネットワークからのトラフィックが、ストレージ アカウントのパブリック エンドポイントへのアクセスを Azure ネットワーク ファブリックによって許可されるためには、仮想ネットワークのサブネットで `Microsoft.Storage` サービス エンドポイントが公開されている必要があります。 次の CLI コマンドを実行すると、まだ存在しない場合は、`Microsoft.Storage` サービス エンドポイントがサブネットに追加されます。

```bash
serviceEndpoints=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "serviceEndpoints[].service" \
        --output tsv)

foundStorageServiceEndpoint=false
for serviceEndpoint in $serviceEndpoints
do
    if [ $serviceEndpoint = "Microsoft.Storage" ]
    then
        foundStorageServiceEndpoint=true
    fi
done

if [ $foundStorageServiceEndpoint = false ] 
then
    serviceEndpointList=""

    for serviceEndpoint in $serviceEndpoints
    do
        serviceEndpointList+=$serviceEndpoint
        serviceEndpointList+=" "
    done
    
    serviceEndpointList+="Microsoft.Storage"

    az network vnet subnet update \
            --ids $subnet \
            --service-endpoints $serviceEndpointList \
            --output none
fi
```

ストレージ アカウントへのトラフィックを制限する最後のステップでは、ネットワーク ルールを作成し、ストレージ アカウントのネットワーク ルール セットに追加します。

```bash
az storage account network-rule add \
        --resource-group $storageAccountResourceGroupName \
        --account-name $storageAccountName \
        --subnet $subnet \
        --output none

az storage account update \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --bypass "AzureServices" \
        --default-action "Deny" \
        --output none
```

---

## <a name="see-also"></a>関連項目
- [Azure Files のネットワークに関する考慮事項](storage-files-networking-overview.md)
- [Azure Files の DNS 転送の構成](storage-files-networking-dns.md)
- [Azure Files 用のサイト間 VPN の構成](storage-files-configure-s2s-vpn.md)
