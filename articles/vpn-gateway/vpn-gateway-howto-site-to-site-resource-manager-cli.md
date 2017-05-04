---
title: "オンプレミスのネットワークを Azure 仮想ネットワークに接続する: サイト間 VPN: CLI | Microsoft Docs"
description: "パブリック インターネットを経由したオンプレミスのネットワークから Azure 仮想ネットワークへの IPsec 接続を作成する手順。 これらの手順は、CLI を使用してクロスプレミスのサイト間 VPN Gateway 接続を作成する際に役立ちます。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c3563f3a3fa46d40ba02fe97b3b0ebe3c45caddd
ms.lasthandoff: 04/26/2017


---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>CLI を使用したサイト間 VPN 接続を持つ仮想ネットワークの作成

この記事では、Azure CLI を使用して、オンプレミス ネットワークから VNet へのサイト間 VPN Gateway 接続を作成する方法について説明します。 この記事の手順は、Resource Manager デプロイメント モデルに適用されます。 また、この構成の作成には、次のリストから別のオプションを選択して、別のデプロイ ツールまたはデプロイ モデルを使用することもできます。

> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager - CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [クラシック - Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [クラシック - クラシック ポータル](vpn-gateway-site-to-site-create.md)
> 
>


![クロスプレミスのサイト間 VPN Gateway 接続の図](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-connection-diagram.png)

サイト間 VPN Gateway 接続は、IPsec/IKE (IKEv1 または IKEv2) VPN トンネルを介してオンプレミス ネットワークを Azure 仮想ネットワークに接続するために使用します。 この種類の接続では、外部接続用パブリック IP アドレスが割り当てられていてるオンプレミスの VPN デバイスが必要です。 VPN Gateway の詳細については、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」を参照してください。

## <a name="before-you-begin"></a>開始する前に

構成を開始する前に、以下の条件を満たしていることを確認します。

* Resource Manager デプロイメント モデルを使用することを確認します。 [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* 互換性のある VPN デバイスおよびデバイスを構成できる人員。 互換性のある VPN デバイスとデバイスの構成の詳細については、[VPN デバイスの概要](vpn-gateway-about-vpn-devices.md)に関する記事を参照してください。
* VPN デバイスの外部接続用パブリック IPv4 アドレス。 この IP アドレスを NAT の内側に割り当てることはできません。
* オンプレミス ネットワーク構成の IP アドレス範囲を把握していない場合は、詳細な情報を把握している担当者と協力して作業を行ってください。 この構成を作成する場合は、Azure がオンプレミスの場所にルーティングする IP アドレス範囲のプレフィックスを指定する必要があります。 オンプレミス ネットワークのサブネットと接続先の仮想ネットワーク サブネットが重複しないようにしなければなりません。 
* 最新バージョンの CLI コマンド (2.0 以降)。 CLI コマンドのインストール方法については、「[Install Azure CLI 2.0 (Azure CLI 2.0 のインストール)](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

### <a name="example-values"></a>値の例

次の値を使用して、テスト環境を作成できます。また、この値を参考にしながら、この記事の例を確認していくこともできます。

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.12.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.12.0.0/24 
GatewaySubnet           = 10.12.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24 
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="Login"></a>1.Azure へのログイン

[az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```azurecli
az login
```

複数の Azure サブスクリプションを所有している場合は、アカウントのサブスクリプションが一覧表示されます。

```azurecli
Az account list --all
```

使用するサブスクリプションを指定します。

```azurecli
Az account set --subscription <replace_with_your_subscription_id>
```

## <a name="2-create-a-resource-group"></a>手順 2.リソース グループの作成

次の例では、"TestRG1" という名前のリソース グループを "eastus" の場所に作成します。 VNet を作成するリージョンにリソース グループが既にある場合は、代わりにそのリソース グループを使用できます。

```azurecli
az group create -n TestRG1 -l eastus
```

## <a name="VNet"></a>3.仮想ネットワークの作成

まだ仮想ネットワークがない場合は作成します。 仮想ネットワークを作成する際は、指定したアドレス空間がオンプレミス ネットワーク内に存在するあらゆるアドレス空間と重複していないことを確認します。 

次の例では、"TestVNet1" という名前の仮想ネットワークと "Subnet1" という名前のサブネットを作成します。

```azurecli
az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.12.0.0/16 -l eastus --subnet-name Subnet1 --subnet-prefix 10.12.0.0/24
```

## 4.<a name="gwsub"></a>ゲートウェイ サブネットを作成する

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

この構成には、ゲートウェイ サブネットも必要です。 仮想ネットワーク ゲートウェイでは、VPN ゲートウェイ サービスが使用する IP アドレスが含まれているゲートウェイ サブネットを使用します。 ゲートウェイ サブネットを作成する際は、名前を "GatewaySubnet " にする必要があります。 別の名前を付けてサブネットを作成しても、Azure はそれをゲートウェイ サブネットとして扱いません。

指定したゲートウェイ サブネットのサイズは、作成する VPN ゲートウェイの構成によって異なります。 /29 と同程度の小規模なゲートウェイ サブネットを作成することはできますが、/28 または /27 を選択してさらに多くのアドレスが含まれる大規模なサブネットを作成することをお勧めします。 大規模なゲートウェイ サブネットを使用すると、将来の構成に対応するのに十分な IP アドレスを確保できます。


```azurecli
az network vnet subnet create --address-prefix 10.12.255.0/27 -n GatewaySubnet -g TestRG1 --vnet-name TestVNet1
```

## <a name="localnet"></a>5.ローカル ネットワーク ゲートウェイを作成する

ローカル ネットワーク ゲートウェイは通常、オンプレミスの場所を指します。 サイトに Azure が参照できる名前を付け、接続を作成するオンプレミス VPN デバイスの IP アドレスを指定します。 また、VPN ゲートウェイを介して VPN デバイスにルーティングされる IP アドレスのプレフィックスも指定します。 指定するアドレスのプレフィックスは、オンプレミス ネットワークのプレフィックスです。 オンプレミス ネットワークが変わった場合は、プレフィックスを簡単に更新できます。

次の値を使用します。

* *--gateway-ip-address* は、オンプレミス VPN デバイスの IP アドレスです。 VPN デバイスを NAT の内側に配置することはできません。
* *--local-address-prefixes* は、オンプレミスのアドレス空間です。

次の例では、複数のアドレス プレフィックスを含むローカル ネットワーク ゲートウェイを追加する方法を示します。

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="PublicIP"></a>6.パブリック IP アドレスの要求

次に、仮想ネットワーク VPN ゲートウェイに割り当てるパブリック IP アドレスを要求します。 この IP アドレスに接続するように VPN デバイスを構成します。

Resource Manager デプロイメント モデルの仮想ネットワーク ゲートウェイでは、現在、動的割り当て方法を使用したパブリック IP アドレスのみをサポートしています。 ただし、これは IP アドレスが変更されるという意味ではありません。 VPN ゲートウェイの IP アドレスが変更されるのは、ゲートウェイが削除され、再度作成されたときのみです。 仮想ネットワーク ゲートウェイのパブリック IP アドレスは、VPN ゲートウェイのサイズ変更、リセット、その他の内部メンテナンス/アップグレードの際には変更されません。 

```azurecli
az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
```

## <a name="CreateGateway"></a>7.VPN ゲートウェイを作成する

仮想ネットワーク VPN ゲートウェイを作成します。 VPN ゲートウェイの作成が完了するまでに、45 分以上かかることがあります。

次の値を使用します。

* サイト間構成の *--gateway-type* は *Vpn* です。 ゲートウェイの種類は常に、実装する構成に対応するものとなります。 詳細については、「[ゲートウェイの種類](vpn-gateway-about-vpn-gateway-settings.md#gwtype)」を参照してください。
* *--vpn-type* には、*RouteBased* (ドキュメントによっては動的ゲートウェイと呼ばれます) または *PolicyBased* (ドキュメントによっては静的ゲートウェイと呼ばれます) を指定できます。 設定は、接続するデバイスの要件によって異なります。 VPN Gateway の種類の詳細については、「[VPN ゲートウェイの構成設定について](vpn-gateway-about-vpn-gateway-settings.md#vpntype)」を参照してください。
* *--sku* には、Basic、Standard、HighPerformance のいずれかを指定できます。 特定の SKU には構成の制限があります。 詳細については、「[ゲートウェイの SKU](vpn-gateway-about-vpngateways.md#gateway-skus)」を参照してください。

このコマンドを実行した後に、フィードバックや出力は表示されません。 ゲートウェイの作成には約 45 分かかります。

```azurecli
az network vnet-gateway create -n VNet1GW --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku Standard --no-wait 
```

## <a name="VPNDevice"></a>8.VPN デバイスの構成

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]
  仮想ネットワーク ゲートウェイのパブリック IP アドレスを調べるには、次のコマンドを実際の値に置き換えて使用します。 読みやすいように、出力は書式設定され、パブリック IP の一覧が表形式で表示されます。

  ```azurecli
  az network public-ip list -g TestRG1 -o table
  ```

## <a name="CreateConnection"></a>9.VPN 接続を作成する

仮想ネットワーク ゲートウェイとオンプレミス VPN デバイスとの間にサイト間 VPN 接続を作成します。 共有キーの値は VPN デバイスの構成された共有キーの値と一致する必要があるため、特に注意してください。

```azurecli
az network vpn-connection create -n VNet1toSite2 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

しばらくすると、接続が確立されます。

## <a name="toverify"></a>10.VPN 接続の確認

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)] 

別の方法で接続を確認する場合は、「[VPN Gateway 接続の確認](vpn-gateway-verify-connection-resource-manager.md)」を参照してください。

## <a name="common-tasks"></a>一般的なタスク

### <a name="to-view-local-network-gateways"></a>ローカル ネットワーク ゲートウェイを表示するには

```azurecli
az network local-gateway list --resource-group TestRG1
```

### <a name="modify"></a>ローカル ネットワーク ゲートウェイの IP アドレスのプレフィックスを変更するには
ローカル ネットワーク ゲートウェイのプレフィックスを変更する必要がある場合は、以下の手順に従います。 変更を加えるたびに、変更するプレフィックスだけでなく、プレフィックスの一覧全体を指定する必要があります。

- **指定した接続がある場合は**、次の例を使用します。 既存のプレフィックスで構成されるプレフィックスの一覧全体と追加するプレフィックスを指定します。 この例では、10.0.0.0/24 と 20.0.0.0/24 が既に存在します。 プレフィックス 30.0.0.0/24 と 40.0.0.0/24 を追加します。

  ```azurecli
  az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 -n VNet1toSite2 -g TestRG1
  ```

- **指定した接続がない場合は**、ローカル ネットワーク ゲートウェイの作成に使用するときと同じコマンドを使用します。 このコマンドを使用して、VPN デバイスのゲートウェイ IP アドレスを更新することもできます。 接続がまだない場合にのみ、このコマンドを使用します。 この例では、10.0.0.0/24、20.0.0.0/24、30.0.0.0/24、および 40.0.0.0/24 が存在します。 保持する必要があるプレフィックスだけを指定します。 この場合は、10.0.0.0/24 と 20.0.0.0/24 です。

  ```azurecli
  az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
  ```

### <a name="modifygwipaddress"></a>ローカル ネットワーク ゲートウェイの IP アドレスを変更するには

この構成の IP アドレスは、接続を作成している VPN デバイスの IP アドレスです。 VPN デバイスの IP アドレスが変更された場合は、値を変更できます。 ゲートウェイ接続がある場合でも、IP アドレスが変更される可能性があります。

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 -n Site2 -g TestRG1
```

結果を表示して、IP アドレスのプレフィックスが含まれていることを確認します。

  ```azurecli
  "localNetworkAddressSpace": { 
    "addressPrefixes": [ 
      "10.0.0.0/24", 
      "20.0.0.0/24", 
      "30.0.0.0/24" 
    ] 
  }, 
  "location": "eastus", 
  "name": "Site2", 
  "provisioningState": "Succeeded",  
  ```

### <a name="to-view-the-virtual-network-gateway-public-ip-address"></a>仮想ネットワーク ゲートウェイのパブリック IP アドレスを表示するには

仮想ネットワーク ゲートウェイのパブリック IP アドレスを検索するには、次の例を使用します。 読みやすいように、出力は書式設定され、パブリック IP の一覧が表形式で表示されます。

```azurecli
az network public-ip list -g TestRG1 -o table
```

### <a name="to-verify-the-shared-key-values"></a>共有キーの値を確認するには

共有キーの値が VPN デバイスの構成に使用したものと同じ値であることを確認します。 異なる場合は、デバイスの値を使用して接続をもう一度実行するか、返された値でデバイスを更新します。 値は一致する必要があります。

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 -g TestRG1
```

## <a name="next-steps"></a>次のステップ

*  接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) に関するページを参照してください。
* BGP の詳細については、[BGP の概要](vpn-gateway-bgp-overview.md)に関する記事と [BGP の構成方法](vpn-gateway-bgp-resource-manager-ps.md)に関する記事を参照してください。
* 強制トンネリングについては、[強制トンネリングの構成](vpn-gateway-forced-tunneling-rm.md)に関する記事を参照してください。
* ネットワーク関連の Azure CLI コマンドの一覧については、[Azure CLI](https://docs.microsoft.com/cli/azure/network) に関するページを参照してください。
