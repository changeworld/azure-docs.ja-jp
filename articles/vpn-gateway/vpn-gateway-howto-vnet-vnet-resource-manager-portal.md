---
title: "Azure 仮想ネットワークを別の VNet に接続する: ポータル | Microsoft Docs"
description: "Resource Manager と Azure Portal を使用して VNet 間の VPN ゲートウェイ接続を作成します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2017
ms.author: cherylmc
ms.openlocfilehash: b2da1c7148e27ca8dd8eb774d4201415a969fada
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2017
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-the-azure-portal"></a>Azure Portal を使用した VNet 間 VPN Gateway 接続を構成する

この記事では、仮想ネットワーク間で VPN Gateway 接続を確立する方法について説明します。 仮想ネットワークが属しているリージョンやサブスクリプションは異なっていてもかまいません。 異なるサブスクリプションの VNet を接続する場合、サブスクリプションが同じ Active Directory テナントに関連付けられている必要はありません。 

この記事の手順は、Resource Manager デプロイメント モデルに適用されます。また、この手順では Azure Portal を使用します。 また、この構成の作成には、次のリストから別のオプションを選択して、別のデプロイ ツールまたはデプロイ モデルを使用することもできます。

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (クラシック)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [異なるデプロイメント モデルの接続 - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [異なるデプロイメント モデルの接続 - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![v2v diagram](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

仮想ネットワークどうし (VNet 間) の接続は、VNet をオンプレミス サイトの場所に接続することと似ています。 どちらの接続タイプでも、VPN ゲートウェイを使用して、IPsec/IKE を使った安全なトンネルが確保されます。 VNet 間ではなく、VNet どうしのサイト間 (IPsec) 接続を作成することはできます。 どちらの接続の種類も、通信時には同じように動作します。 ただし、VNet 間接続を作成すると、一方の VNet のアドレス空間を更新した場合に、もう一方の VNet は、更新されたアドレス空間へのルーティングを自動的に認識します。 サイト間 (IPsec) 接続を作成する場合は、ローカル ネットワーク ゲートウェイのアドレス空間を手動で構成する必要があります。 複雑な構成を使用する場合は、VNet 間ではなく、IPsec 接続を作成する方がよいことがあります。 そうすることで、ローカル ネットワーク ゲートウェイに対して追加のアドレス空間を手動で指定することができます。

複数の VNet が同じリージョンに存在する場合、それらを VNet ピアリングで接続することを検討してください。 VNet ピアリングは、VPN ゲートウェイを使用しません。 詳細については、「 [VNet ピアリング](../virtual-network/virtual-network-peering-overview.md)」を参照してください。

マルチサイト構成と VNet 間通信を組み合わせることができます。 そのため、クロスプレミス接続と仮想ネットワーク間接続を組み合わせたネットワーク トポロジを確立することができます (下図参照)。

![接続について](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "接続について")

### <a name="why-connect-virtual-networks"></a>仮想ネットワークを接続する理由

仮想ネットワークを接続するのは次のような場合です。

* **リージョン間の geo 冗長性および geo プレゼンス**

  * インターネット接続エンドポイントを介さず、安全な接続を使って独自の geo レプリケーションや geo 同期をセットアップすることができます。
  * Azure Traffic Manager および Load Balancer を使用し、複数の Azure リージョンをまたぐ geo 冗長性を備えた、可用性に優れたワークロードをセットアップすることができます。 たとえば、複数の Azure リージョンにまたがる SQL AlwaysOn 可用性グループをセットアップすることができます。
* **特定のリージョン内で分離または管理境界を備えた多層アプリケーション**

  * 同じリージョン内で、分離または管理要件に基づいて相互に接続された複数の仮想ネットワークを利用し、多層アプリケーションをセットアップすることができます。

以下の手順を練習として使用する場合は、この例の設定値を使用できます。 この例では、仮想ネットワークは同じサブスクリプション内にありながら、異なるリソース グループに含まれます。 対象となる VNet がそれぞれ異なるサブスクリプションに存在する場合、ポータルで接続を作成することはできません。 [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) または [CLI](vpn-gateway-howto-vnet-vnet-cli.md) を使用できます。 VNet 間接続の詳細については、この記事の最後にある「[VNet 間接続に関してよく寄せられる質問](#faq)」を参照してください。

### <a name="values"></a>設定例

**TestVNet1 の値:**

* VNet の名前: TestVNet1
* アドレス空間: 10.11.0.0/16
* サブスクリプション: 使用するサブスクリプションを選択
* リソース グループ: TestRG1
* 場所: 米国東部
* サブネット名: FrontEnd
* サブネットのアドレス範囲: 10.11.0.0/24
* ゲートウェイ サブネット名: GatewaySubnet (ポータルではこの名前が自動入力されます)
* ゲートウェイ サブネットのアドレス範囲: 10.11.255.0/27
* DNS サーバー: ご利用の DNS サーバーの IP アドレスを使用
* 仮想ネットワーク ゲートウェイ名: TestVNet1GW
* ゲートウェイの種類: VPN
* VPN の種類: ルート ベース
* SKU: 使用するゲートウェイの SKU を選択
* パブリック IP アドレス名: TestVNet1GWIP
* 接続名: TestVNet1toTestVNet4
* 共有キー: 独自の共有キーを作成できます。 この例では、abc123 を使用します。 ここで重要なのは、VNet 間の接続を作成するときに、この値を一致させることです。

**TestVNet4 の値:**

* VNet の名前: TestVNet4
* アドレス空間: 10.41.0.0/16
* サブスクリプション: 使用するサブスクリプションを選択
* リソース グループ: TestRG4
* 場所: 米国西部
* サブネット名: FrontEnd
* サブネットのアドレス範囲: 10.41.0.0/24
* ゲートウェイ サブネット名: GatewaySubnet (ポータルではこの名前が自動入力されます)
* ゲートウェイ サブネットのアドレス範囲: 10.41.255.0/27
* DNS サーバー: ご利用の DNS サーバーの IP アドレスを使用
* 仮想ネットワーク ゲートウェイ名: TestVNet4GW
* ゲートウェイの種類: VPN
* VPN の種類: ルート ベース
* SKU: 使用するゲートウェイの SKU を選択
* パブリック IP アドレス名: TestVNet4GWIP
* 接続名: TestVNet4toTestVNet1
* 共有キー: 独自の共有キーを作成できます。 この例では、abc123 を使用します。 ここで重要なのは、VNet 間の接続を作成するときに、この値を一致させることです。

## <a name="CreatVNet"></a>1.TestVNet1 の作成と構成
既に VNet がある場合は、設定が VPN ゲートウェイの設計に適合していることを確認します。 特に、他のネットワークと重複している可能性のあるサブネットに注意してください。 サブネットの重複があると、接続が適切に動作しません。 VNet が正しい設定で構成されていたら、「 [DNS サーバーの指定](#dns) 」セクションの手順に進んでください。

### <a name="to-create-a-virtual-network"></a>仮想ネットワークを作成するには
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="subnets"></a>2.アドレス空間の追加とサブネットの作成
VNet が作成されたら、アドレス空間をさらに追加してサブネットを作成することができます。

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3.ゲートウェイ サブネットの作成
仮想ネットワークをゲートウェイに接続する前に、まず、接続先の仮想ネットワークのゲートウェイ サブネットを作成する必要があります。 将来的な構成要件も見越して十分な IP アドレスを確保するために、可能であれば、/28 または /27 の CIDR ブロックを使用してゲートウェイ サブネットを作成することをお勧めします。

練習としてこの構成を作成する場合は、ゲートウェイ サブネットの作成時に、上記の[設定例](#values)を参照してください。

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>ゲートウェイ サブネットを作成するには
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>4.DNS サーバーの指定 (省略可能)
VNet 間接続に DNS は不要です。 ただし仮想ネットワークにデプロイされたリソースで名前解決を使用する場合は、DNS サーバーを指定する必要があります。 この設定では、この仮想ネットワークの名前解決に使用する DNS サーバーを指定することができます。 この設定で、DNS サーバーは作成されません。

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="VNetGateway"></a>5.仮想ネットワーク ゲートウェイの作成
この手順では、VNet の仮想ネットワーク ゲートウェイを作成します。 選択したゲートウェイ SKU によっては、ゲートウェイの作成に 45 分以上かかる場合も少なくありません。 練習としてこの構成を作成する場合は、[設定例](#values)を参照してください。

### <a name="to-create-a-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを作成するには
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="CreateTestVNet4"></a>6.TestVNet4 の作成と構成
TestVNet1 を構成したら、前の手順を繰り返して TestVNet4 を作成します。使用する値は TestVNet4 の値に置き換えてください。 TestVNet1 の仮想ネットワーク ゲートウェイの作成が完了するまで待つ必要はありません。すぐに TestVNet4 を構成できます。 独自の値を使用する場合は、接続先とするどの VNet ともアドレス空間が重複しないようにしてください。

## <a name="TestVNet1Connection"></a>7.TestVNet1 ゲートウェイ接続の構成
TestVNet1 と TestVNet4 の仮想ネットワーク ゲートウェイの作成が両方とも完了したら、仮想ネットワーク ゲートウェイの接続を作成できます。 このセクションでは、VNet1 から VNet4 への接続を作成します。 これらの手順は、同じサブスクリプションに存在する VNet でのみ使用できます。 接続する VNet がそれぞれ異なるサブスクリプションに存在する場合は、PowerShell を使用する必要があります。 [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) に関する記事を参照してください。 ただし、VNet が同じサブスクリプション内の異なるリソース グループにある場合は、ポータルを使用して接続できます。

1. **[すべてのリソース]** から VNet の仮想ネットワーク ゲートウェイに移動します。 たとえば、**[TestVNet1GW]** に移動します。 **[TestVNet1GW]** をクリックして、仮想ネットワーク ゲートウェイのページを開きます。

  ![[接続] ページ](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "[接続] ページ")
2. **[+追加]** をクリックして **[接続の追加]** ページを開きます。

  ![接続の追加](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "接続の追加")
3. **[接続の追加]** ページで、名前フィールドに接続の名前を入力します。 たとえば、「**TestVNet1toTestVNet4**」と入力します。
4. **[接続の種類]** で、ドロップダウン リストから **[VNet 対 VNet]** を選択します。
5. この接続は指定した仮想ネットワーク ゲートウェイから作成しているため、**[最初の仮想ネットワーク ゲートウェイ]** フィールドの値は自動的に入力されます。
6. **[2 番目の仮想ネットワーク ゲートウェイ]** フィールドでは、接続先として作成する VNet の仮想ネットワーク ゲートウェイを設定します。 **[別の仮想ネットワーク ゲートウェイを選択する]** をクリックして、**[仮想ネットワーク ゲートウェイの選択]** ページを開きます。
7. このページに一覧表示されている仮想ネットワーク ゲートウェイを確認します。 自分のサブスクリプションに属している仮想ネットワーク ゲートウェイのみが表示されていることがわかります。 自分のサブスクリプションに属していない仮想ネットワーク ゲートウェイに接続する場合は、[PowerShell に関する記事](vpn-gateway-vnet-vnet-rm-ps.md)を参照してください。
8. 接続先とする仮想ネットワーク ゲートウェイをクリックします。
9. **[共有キー]** フィールドには、接続に使用する共有キーを入力します。 このキーは、自分で生成または作成できます。 サイト間接続では、オンプレミス デバイスと仮想ネットワーク ゲートウェイの接続にまったく同じキーを使用します。 Vnet 間接続の場合も、VPN デバイスではなく、別の仮想ネットワーク ゲートウェイに接続するという点を除けば、概念は同じです。
10. ページの下部にある **[OK]** をクリックして変更を保存します。

## <a name="TestVNet4Connection"></a>8.TestVNet4 ゲートウェイ接続の構成
次は、TestVNet4 から TestVNet1 への接続を作成します。 ポータルで、TestVNet4 に関連付けられている仮想ネットワーク ゲートウェイを探します。 前のセクションの手順に従います。その際、TestVNet4 から TestVNet1 への接続を作成するように値を置き換えます。 必ず同一の共有キーを使用してください。

## <a name="VerifyConnection"></a>9.接続の確認

ポータルで仮想ネットワーク ゲートウェイを探します。 仮想ネットワーク ゲートウェイのページで、**[接続]** をクリックして仮想ネットワーク ゲートウェイの接続のページを表示します。 接続が確立されると、[状態] の値が **[成功]** と **[接続済み]** に変わります。 接続をダブルクリックすると **[要点]** ページが開かれ、詳細情報が表示されます。

![成功](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "成功")

データのフローが開始されると、[データ入力] と [データ出力] に値が表示されます。

![要点](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "要点")

## <a name="to-add-additional-connections"></a>さらに接続を追加するには

さらに接続を追加する場合は、接続の作成元となる仮想ネットワーク ゲートウェイに移動し、**[接続]** をクリックします。 別の VNet 間接続を作成することも、オンプレミスの場所への IPsec サイト間接続を作成することもできます。 作成する接続の種類に合わせて、**[接続の種類]** を調整してください。 追加の接続を作成する前に、仮想ネットワークのアドレス空間が接続先のアドレス空間と重複していないことを確認してください。 サイト間接続を作成する手順については、[サイト間接続の作成](vpn-gateway-howto-site-to-site-resource-manager-portal.md)に関するページを参照してください。

## <a name="faq"></a>VNet 間接続に関してよく寄せられる質問
VNet 間接続に関するその他の情報についてよく寄せられる質問の詳細を示します。

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>次のステップ

仮想ネットワーク内のリソースへのネットワーク トラフィックを制限する方法については、「[ネットワークのセキュリティ](../virtual-network/security-overview.md)」を参照してください。

Azure がトラフィックを Azure、オンプレミス、インターネット リソースの間でどのようにルーティングするかについては、「[仮想ネットワーク トラフィックのルーティング](../virtual-network/virtual-networks-udr-overview.md)」を参照してください。
