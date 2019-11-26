---
title: Linux 上で Azure Files で使用するポイント対サイト (P2S) VPN を構成する | Microsoft Docs
description: Linux 上で Azure Files で使用するポイント対サイト (P2S) VPN を構成する方法
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b988435fc6928d52321cb427e2412e7ca81680d2
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2019
ms.locfileid: "73126468"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Linux 上で Azure Files で使用するポイント対サイト (P2S) VPN を構成する
ポイント対サイト (P2S) VPN 接続を使用すると、ポート 445 を開くことなく、Azure の外部から SMB 経由で Azure ファイル共有をマウントできます。 ポイント対サイト VPN 接続は、Azure と個々のクライアントの間の VPN 接続です。 Azure Files で P2S VPN 接続を使用するには、接続したいクライアントごとに P2S VPN 接続を構成する必要があります。 オンプレミス ネットワークから Azure ファイル共有に接続する必要のある多数のクライアントが存在する場合は、クライアントごとのポイント対サイト接続の代わりにサイト間 (S2S) VPN 接続を使用できます。 詳細については、「[Azure Files で使用するサイト間 VPN を構成する](storage-files-configure-s2s-vpn.md)」を参照してください。

このハウツー記事を読み進める前に、Azure Files で使用可能なネットワーク オプションの完全な説明について [Azure Files のネットワークの概要](storage-files-networking-overview.md)に関するページを参照することを強くお勧めします。

この記事では、Azure ファイル共有をオンプレミスに直接マウントするために、Linux 上でポイント対サイト VPN を構成する手順について詳細に説明します。 Azure File Sync のトラフィックを VPN 経由でルーティングすることを検討している場合は、[Azure File Sync のプロキシとファイアウォールの設定の構成](storage-sync-files-firewall-and-proxy.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件
- 最新バージョンの Azure CLI。 Azure CLI をインストールする方法の詳細については、[Azure PowerShell CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページを参照し、オペレーティング システムを選択してください。 Linux 上で Azure PowerShell モジュールを使用することは可能ですが、下の手順は Azure CLI 用に提供されています。

- オンプレミスにマウントする Azure ファイル共有。 ポイント対サイト VPN には [Standard](storage-how-to-create-file-share.md) または [Premium Azure ファイル共有](storage-how-to-create-premium-fileshare.md)のどちらかを使用できます。

## <a name="install-required-software"></a>必要なソフトウェアのインストール
Azure 仮想ネットワーク ゲートウェイは、IPsec と OpenVPN を含むいくつかの VPN プロトコルを使用して VPN 接続を提供できます。 このガイドでは IPsec を使用する方法を示し、Linux 上で strongSwan パッケージを使用してサポートを提供します。 

> Ubuntu 18.10 で検証されています。

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>仮想ネットワークをデプロイする 
ポイント対サイト VPN 経由でオンプレミスから Azure ファイル共有やその他の Azure リソースにアクセスするには、仮想ネットワーク (VNet) を作成する必要があります。 自動的に作成される P2S VPN 接続は、オンプレミスの Linux コンピューターとこの Azure 仮想ネットワークの間のブリッジです。

次のスクリプトは、ストレージ アカウントのサービス エンドポイント用に 1 つ、ストレージ アカウントのプライベート エンドポイント用に 1 つ (これは、変化する可能性があるストレージ アカウントのパブリック IP のカスタム ルーティングを作成することなく、オンプレミスのストレージ アカウントにアクセスするために必要です)、および VPN サービスを提供する仮想ネットワーク ゲートウェイ用に 1 つの 3 つのサブネットを含む Azure 仮想ネットワークを作成します。 

忘れずに、`<region>`、`<resource-group>`、および `<desired-vnet-name>` を実際の環境の適切な値に置き換えてください。

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="restrict-the-storage-account-to-the-virtual-network"></a>ストレージ アカウントを仮想ネットワークに制限する
既定では、ストレージ アカウントを作成すると、要求を認証する手段 (Active Directory ID やストレージ アカウント キーの使用など) を持っている限り、世界中のどこからでもそのアカウントにアクセスできます。 このストレージ アカウントへのアクセスを今作成した仮想ネットワークに制限するには、仮想ネットワーク内のアクセスを許可し、その他のすべてのアクセスを拒否するネットワーク ルール セットを作成する必要があります。

ストレージ アカウントを仮想ネットワークに制限するには、サービス エンドポイントを使用する必要があります。 サービス エンドポイントは、パブリック DNS/パブリック IP に仮想ネットワーク内からしかアクセスできないようにするためのネットワーク構造です。 パブリック IP アドレスは同じままであることが保証されないため、最終的にはストレージ アカウントのサービス エンドポイントではなく、プライベート エンドポイントを使用する必要がありますが、サービス エンドポイントも公開されない限り、ストレージ アカウントを制限することはできません。

`<storage-account-name>` は、アクセスするストレージ アカウントに必ず置き換えてください。

```bash
storageAccountName="<storage-account-name>"

az storage account network-rule add \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --subnet $serviceEndpointSubnet > /dev/null

az storage account update \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" > /dev/null
```

## <a name="create-a-private-endpoint-preview"></a>プライベート エンドポイントを作成する (プレビュー)
ストレージ アカウントのプライベート エンドポイントを作成すると、そのストレージ アカウントに仮想ネットワークの IP アドレス空間内の IP アドレスが割り当てられます。 このプライベート IP アドレスを使用してオンプレミスから Azure ファイル共有をマウントすると、VPN のインストールにより自動的に定義されたルーティング規則によって、マウント要求が VPN 経由でストレージ アカウントにルーティングされます。 

```bash
zoneName="privatelink.file.core.windows.net"

storageAccount=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "id" | tr -d '"')

az resource update \
    --ids $privateEndpointSubnet \
    --set properties.privateEndpointNetworkPolicies=Disabled > /dev/null

az network private-endpoint create \
    --resource-group $resourceGroupName \
    --name "$storageAccountName-PrivateEndpoint" \
    --location $region \
    --subnet $privateEndpointSubnet \
    --private-connection-resource-id $storageAccount \
    --group-ids "file" \
    --connection-name "privateEndpointConnection" > /dev/null

az network private-dns zone create \
    --resource-group $resourceGroupName \
    --name $zoneName > /dev/null

az network private-dns link vnet create \
    --resource-group $resourceGroupName \
    --zone-name $zoneName \
    --name "$virtualNetworkName-link" \
    --virtual-network $virtualNetworkName \
    --registration-enabled false > /dev/null

networkInterfaceId=$(az network private-endpoint show \
    --name "$storageAccountName-PrivateEndpoint" \
    --resource-group $resourceGroupName \
    --query 'networkInterfaces[0].id' | tr -d '"')
 
storageAccountPrivateIP=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query "properties.ipConfigurations[0].properties.privateIPAddress" | tr -d '"')

fqdnQuery="properties.ipConfigurations[0].properties.privateLinkConnectionProperties.fqdns[0]"
fqdn=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query $fqdnQuery | tr -d '"')

az network private-dns record-set a create \
    --name $storageAccountName \
    --zone-name $zoneName \
    --resource-group $resourceGroupName > /dev/null
```

## <a name="create-certificates-for-vpn-authentication"></a>VPN 認証用の証明書を作成する
オンプレミスの Linux コンピューターからの VPN 接続が仮想ネットワークへのアクセスを認証されるようにするには、仮想マシン ゲートウェイに提供されるルート証明書と、そのルート証明書を使用して署名されるクライアント証明書の 2 つの証明書を作成する必要があります。 次のスクリプトは、必要な証明書を作成します。

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイをデプロイする
Azure 仮想ネットワーク ゲートウェイは、オンプレミスの Linux コンピューターが接続するサービスです。 このサービスをデプロイするには、世界中のクライアントへのゲートウェイを識別するパブリック IP と、それらのクライアントを認証するために使用される、前に作成したルート証明書の 2 つの基本的なコンポーネントが必要です。

`<desired-vpn-name-here>` は、これらのリソースに必要な名前に置き換えてください。

> [!Note]  
> Azure 仮想ネットワーク ゲートウェイのデプロイには、最大で 45 分かかる場合があります。 このリソースがデプロイされている間、この bash スクリプトは、デプロイが完了されるようにブロックします。 これは予期されることです。

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>VPN クライアントを構成する
Azure 仮想ネットワーク ゲートウェイは、オンプレミスの Linux コンピューター上で VPN 接続を初期化するために必要な構成ファイルを含むダウンロード可能なパッケージを作成します。 次のスクリプトは、作成した証明書を正しい場所に配置し、ダウンロード可能なパッケージ内の構成ファイルの正しい値を使用して `ipsec.conf` ファイルを構成します。

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Azure ファイル共有をマウントする
これでポイント対サイト VPN が設定されたので、Azure ファイル共有をマウントできます。 次の例では、共有を非永続的にマウントします。 永続的にマウントするには、[Linux での Azure ファイル共有の使用](storage-how-to-use-files-linux.md)に関するページを参照してください。 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>関連項目
- [Azure Files のネットワークの概要](storage-files-networking-overview.md)
- [Windows 上で Azure Files で使用するポイント対サイト (P2S) VPN を構成する](storage-files-configure-p2s-vpn-windows.md)
- [Azure Files で使用するサイト間 (S2S) VPN を構成する](storage-files-configure-s2s-vpn.md)