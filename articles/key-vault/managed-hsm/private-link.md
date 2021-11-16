---
title: プライベート エンドポイントを使用して Azure Key Vault マネージド HSM を構成する
description: Azure Key Vault マネージド HSM を Azure Private Link サービスと統合する方法を確認します
author: mbaldwin
ms.author: mbaldwin
ms.date: 06/21/2021
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3e7ccb16efe21503087dcac035e2ca8f832ef7b7
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370349"
---
# <a name="integrate-managed-hsm-with-azure-private-link"></a>マネージド HSM と Azure Private Link を統合する

Azure Private Link サービスを使用すると、自分の仮想ネットワーク内のプライベート エンドポイント経由で、Azure サービス (マネージド HSM、Azure Storage、Azure Cosmos DB など) と、Azure でホストされている顧客またはパートナー サービスにアクセスできます。

Azure プライベート エンドポイントは、Azure Private Link を使用するサービスにプライベートかつ安全に接続するためのネットワーク インターフェイスです。 プライベート エンドポイントは、ご自分の VNet からのプライベート IP アドレスを使用して、サービスを実質的に VNet に取り込みます。 サービスへのすべてのトラフィックをプライベート エンドポイント経由でルーティングできるため、ゲートウェイ、NAT デバイス、ExpressRoute または VPN 接続、パブリック IP アドレスは必要ありません。 仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを経由して、パブリック インターネットからの公開を排除します。 最高レベルの細分性でアクセスを制御しながら Azure リソースのインスタンスに接続できます。

詳細については、「[Azure Private Link とは](../../private-link/private-link-overview.md)」を参照してください。

> [!NOTE]
> マネージド HSM は現在、IP ルールと[仮想ネットワーク サービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)に対応していません。 
> 
## <a name="prerequisites"></a>前提条件

マネージド HSM を Azure Private Link と統合するには、次のものが必要です。

- マネージド HSM。 詳細については、「[Azure CLI を使用してマネージド HSM をプロビジョニングしてアクティブにする](quick-create-cli.md)」を参照してください。
- Azure 仮想ネットワーク。
- 仮想ネットワーク内のサブネット。
- マネージド HSM と仮想ネットワークの両方に対する所有者または共同作成者のアクセス許可。
- Azure CLI バージョン 2.25.0 以降。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

プライベート エンドポイントと仮想ネットワークは、同じリージョンに存在する必要があります。 ポータルを使用してプライベート エンドポイントのリージョンを選択すると、自動的にフィルター処理が行われ、そのリージョン内にある仮想ネットワークのみが表示されます。 HSM は、別のリージョンに配置することができます。

プライベート エンドポイントは、ご自分の仮想ネットワーク内のプライベート IP アドレスを使用します。


## <a name="establish-a-private-link-connection-to-managed-hsm-using-cli-initial-setup"></a>CLI を使用してマネージド HSM へのプライベート リンク接続を確立する (初期セットアップ)

```azurecli
az login                                                                   # Login to Azure CLI
az account set --subscription {SUBSCRIPTION ID}                            # Select your Azure Subscription
az group create -n {RESOURCE GROUP} -l {REGION}                            # Create a new Resource Group
az provider register -n Microsoft.KeyVault                                 # Register KeyVault as a provider
az keyvault update-hsm --hsm-name {HSM NAME} -g {RG} --default-action deny # Turn on firewall

az network vnet create -g {RG} -n {vNet NAME} --location {REGION}           # Create a Virtual Network

    # Create a Subnet
az network vnet subnet create -g {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}

    # Disable Virtual Network Policies
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true

    # Create a Private DNS Zone
az network private-dns zone create --resource-group {RG} --name privatelink.managedhsm.azure.net

    # Link the Private DNS Zone to the Virtual Network
az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.managedhsm.azure.net --name {dnsZoneLinkName} --registration-enabled true

```

### <a name="allow-trusted-services-to-access-managed-hsm"></a>信頼されたサービスによるマネージド HSM へのアクセスを許可する

ファイアウォールが有効になっていると、プライベート エンドポイント接続を使用していない場所 (パブリック インターネットや Azure サービスを含む) から HSM へのアクセスがすべて拒否されます。 Microsoft サービスがマネージド HSM 内でキーにアクセスできるようにする場合は、`--bypass AzureServices` オプションを使用します。 個々のエンティティ (Azure Storage アカウントや Azure SQL Server など) がキーにアクセスできるようにするには、特定のロールの割り当てが必要です。 

> [!NOTE]
> 特定の信頼されたサービスの使用シナリオだけがサポートされます。 詳細については、[信頼されたサービスの使用シナリオの一覧](../general/overview-vnet-service-endpoints.md#trusted-services)に関するページを参照してください。

```azurecli
az keyvault update-hsm --hsm-name {HSM NAME} -g {RG} --default-action deny --bypass AzureServices
```

### <a name="create-a-private-endpoint-automatically-approve"></a>プライベート エンドポイントを作成する (自動的に承認する) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/managedHSMs/{HSM NAME}" --group-id managedhsm --connection-name {Private Link Connection Name} --location {AZURE REGION}
```

> [!NOTE]
> この HSM を削除すると、プライベート エンドポイントが動作を停止します。 後でこの HSM を復旧する (削除を取り消す) 場合は、新しいプライベート エンドポイントを作成し直す必要があります。

### <a name="create-a-private-endpoint-manually-request-approval"></a>プライベート エンドポイントを作成する (承認を手動で要求する) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/managedHSMs/{HSM NAME}" --group-id managedhsm --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```

### <a name="manage-private-link-connections"></a>プライベート リンク接続を管理する

```azurecli
# Show Connection Status
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}

# Approve a Private Link Connection Request
az keyvault private-endpoint-connection approve --description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --hsm-name {HSM NAME} –-name {PRIVATE LINK CONNECTION NAME}

# Deny a Private Link Connection Request
az keyvault private-endpoint-connection reject --description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --hsm-name {HSM NAME} –-name {PRIVATE LINK CONNECTION NAME}

# Delete a Private Link Connection Request
az keyvault private-endpoint-connection delete --resource-group {RG} --hsm-name {HSM NAME} --name {PRIVATE LINK CONNECTION NAME}
```

### <a name="add-private-dns-records"></a>プライベート DNS レコードを追加する
```azurecli
# Determine the Private Endpoint IP address
az network private-endpoint show -g {RG} -n {PE NAME}      # look for the property networkInterfaces then id; the value must be placed on {PE NIC} below.
az network nic show --ids {PE NIC}                         # look for the property ipConfigurations then privateIpAddress; the value must be placed on {NIC IP} below.

# https://docs.microsoft.com/en-us/azure/dns/private-dns-getstarted-cli#create-an-additional-dns-record
az network private-dns zone list -g {RG}
az network private-dns record-set a add-record -g {RG} -z "privatelink.managedhsm.azure.net" -n {HSM NAME} -a {NIC IP}
az network private-dns record-set list -g {RG} -z "privatelink.managedhsm.azure.net"

# From home/public network, you wil get a public IP. If inside a vnet with private zone, nslookup will resolve to the private ip.
nslookup {HSM NAME}.managedhsm.azure.net
nslookup {HSM NAME}.privatelink.managedhsm.azure.net
```

---

## <a name="validate-that-the-private-link-connection-works"></a>プライベート リンク接続が機能することを検証する

プライベート エンドポイント リソースの同一サブネット内にあるリソースが、プライベート IP アドレスを使用して自分の HSM に接続していること、そしてそれらがプライベート DNS ゾーンに正しく統合されていることを検証する必要があります。

最初に、[Azure portal での Windows 仮想マシンの作成](../../virtual-machines/windows/quick-create-portal.md)に関するページの手順に従って、仮想マシンを作成します。

[ネットワーク] タブで、次の操作を行います。

1. 仮想ネットワークとサブネットを指定します。 新しい仮想ネットワークを作成することも、既存のものを選択することもできます。 既存のものを選択する場合は、リージョンが一致することを確認します。
1. パブリック IP リソースを指定します。
1. [NIC ネットワーク セキュリティ グループ] で [なし] を選択します。
1. [負荷分散] で [いいえ] を選択します。

コマンド ラインを開き、次のコマンドを実行します。

```console
nslookup <your-HSM-name>.managedhsm.azure.net
```

ns lookup コマンドを実行して、パブリック エンドポイントを介してマネージド HSM の IP アドレスを解決すると、次のような結果が表示されます。

```console
c:\ >nslookup <your-hsm-name>.managedhsm.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-hsm-name>.managedhsm.azure.net
```

ns lookup コマンドを実行して、プライベート エンドポイントを介してマネージド HSM の IP アドレスを解決すると、次のような結果が表示されます。

```console
c:\ >nslookup your_hsm_name.managedhsm.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-hsm-name>.managed.azure.net
          <your-hsm-name>.privatelink.managedhsm.azure.net
```

## <a name="troubleshooting-guide"></a>トラブルシューティング ガイド

* プライベート エンドポイントが承認済みの状態であることを確認します。 
    1. ```az keyvault private-endpoint-connections show``` サブコマンドを使用して、プライベート エンドポイント接続の状態を確認します。
    2. 接続状態が承認済みで、プロビジョニングの状態が成功であることを確認します。 
    3. 仮想ネットワークが、使用しているものと一致していることを確認します。

* プライベート DNS ゾーン リソースがあることを調べて確認します。 
    1. プライベート DNS ゾーン リソースの名前は、正確に privatelink.managedhsm.azure.net である必要があります。 
    2. これを設定する方法については、次のリンクを参照してください。 [プライベート DNS ゾーン](../../dns/private-dns-privatednszone.md)
    
* プライベート DNS ゾーンが仮想ネットワークにリンクされていることを確認します。 パブリック IP アドレスがまだ返される場合は、これが問題である可能性があります。 
    1. プライベート DNS ゾーンが仮想ネットワークにリンクされていない場合、仮想ネットワークから送信された DNS クエリでは、HSM のパブリック IP アドレスが返されます。 
    2. Azure portal でプライベート DNS ゾーン リソースに移動し、仮想ネットワーク リンクのオプションをクリックします。 
    4. HSM への呼び出しを実行する仮想ネットワークが、一覧に表示されている必要があります。 
    5. ない場合は追加します。 
    6. 詳細な手順については、[プライベート DNS ゾーンへの仮想ネットワークのリンク](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)に関するドキュメントを参照してください

* HSM の A レコードがプライベート DNS ゾーンから欠落していないことを確認します。 
    1. [プライベート DNS ゾーン] ページに移動します。 
    2. [概要] をクリックし、HSM の簡易名の A レコードがあることを確認します。 サフィックスは指定しないでください。
    3. スペルを確認し、A レコードを作成または修正します。 TTL には 3600 (1 時間) を使用できます。 
    4. 指定したプライベート IP アドレスが正しいことを確認します。 
    
* A レコードの IP アドレスが正しいことを確認します。 
    1. Azure portal でプライベート エンドポイント リソースを開くことにより、IP アドレスを確認できます。
    2. Azure portal 内で、Microsoft.Network/privateEndpoints リソースに移動します
    3. [概要] ページで [ネットワーク インターフェイス] を探し、そのリンクをクリックします。 
    4. このリンクでは NIC リソースの概要が表示され、それにはプライベート IP アドレスのプロパティが含まれます。 
    5. これが、A レコードで指定されている正しい IP アドレスであることを確認します。

## <a name="limitations-and-design-considerations"></a>制限事項と設計に関する考慮事項

> [!NOTE]
> プライベート エンドポイントが有効なマネージド HSM 数のサブスクリプションあたりの上限は、調整可能です。 以下に記載した上限は、あくまで既定値です。 サブスクリプションの上限の引き上げを要求する場合は、Azure サポート チケットを作成してください。 いただいたご依頼は、ケースごとの事情を考慮して承認します。

**価格**: 価格情報については、[Azure Private Link の価格](https://azure.microsoft.com/pricing/details/private-link/)に関するページを参照してください。

**マネージド HSM あたりのプライベート エンドポイントの最大数**: 64。

**プライベート エンドポイントのあるマネージド HSM のサブスクリプションあたりの既定の数**: 400。

詳細については、[Azure Private Link サービスの制限事項](../../private-link/private-link-service-overview.md#limitations)に関するセクションを参照してください。

## <a name="next-steps"></a>次の手順

- [Azure Private Link](../../private-link/private-link-service-overview.md) の詳細
- [マネージド HSM](overview.md) の詳細
