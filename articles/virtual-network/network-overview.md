---
title: Azure における仮想ネットワークと仮想マシン
description: ネットワークは Azure における仮想マシンに関係するため、ここで説明します。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: conceptual
ms.date: 08/17/2021
ms.author: allensu
ms.openlocfilehash: d8ca819e7a63c7a0c4d507709c8495b604d19002
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257672"
---
# <a name="virtual-networks-and-virtual-machines-in-azure"></a>Azure における仮想ネットワークと仮想マシン

仮想マシン (VM) を作成する場合、[仮想ネットワーク](../virtual-network/virtual-networks-overview.md)を作成するか、既存のものを使用します。 仮想ネットワークにおける仮想マシンへの望ましいアクセス方法を決定します。 [リソースを作成する前に計画](../virtual-network/virtual-network-vnet-plan-design-arm.md)を立て、必ず[ネットワーク リソースの制限](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)を理解しておくことが大切です。

次の図では、仮想マシンが Web サーバーとアプリケーション サーバーとして表されています。 仮想マシンの各セットは、仮想ネットワーク内の別々のサブネットに割り当てられます。

:::image type="content" source="./media/network-overview/load-balancer.png" alt-text="多層マルチサブネット アプリケーションの図。" border="true":::

仮想マシンを作成する前に仮想ネットワークを作成することも、仮想マシンの作成時に仮想ネットワークを作成することもできます。 

仮想マシンとの通信をサポートする次のリソースを作成します。

- ネットワーク インターフェイス
- IP アドレス
- 仮想ネットワークとサブネット

また、次のオプション リソースを考慮してください。

- ネットワーク セキュリティ グループ
- ロード バランサー

## <a name="network-interfaces"></a>ネットワーク インターフェイス

[ネットワーク インターフェイス (NIC)](../virtual-network/virtual-network-network-interface.md) とは、仮想マシンと仮想ネットワークの間の相互接続です。 仮想マシンには少なくとも 1 つの NIC が必要です。 作成する VM のサイズに応じて、仮想マシンに複数の NIC を持たせることができます。 各仮想マシンのサイズでサポートされる NIC の数については、[VM サイズ](../virtual-machines/sizes.md)に関する記事を参照してください。

複数の NIC を持つ VM を作成し、VM のライフサイクルを通じて NIC を追加または削除することができます。 複数の NIC を使用すると、VM を異なるサブネットに接続できます。

VM に接続された各 NIC は、VM と同じ場所およびサブスクリプションに存在する必要があります。 NIC と同じ Azure の場所およびサブスクリプションに存在する VNet に各 NIC を接続する必要があります。 仮想マシンの接続先のサブネットは、作成した後で変更できます。 仮想ネットワークを変更することはできません。 VM に接続された各 NIC には、その VM が削除されるまで変更されない MAC アドレスが割り当てられます。

次の表に、ネットワーク インターフェイスの作成に使用できる方法の一覧を示します。

| Method | 説明 |
| ------ | ----------- |
| [Azure Portal](https://portal.azure.com) | Azure portal で VM を作成すると、ネットワーク インターフェイスが自動的に作成されます。 ポータルで作成されるのは、NIC を 1 つだけ備えた VM です。 複数の NIC を備えた VM を作成したい場合は、別の方法で作成する必要があります。 |
| [Azure PowerShell](../virtual-machines/windows/multiple-nics.md) | 以前作成したパブリック IP アドレスの識別子を **`-PublicIpAddressId`** パラメーターで指定して、[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) を実行します。 |
| [Azure CLI](../virtual-machines/linux/multiple-nics.md) | 以前作成したパブリック IP アドレスの識別子を指定するために、 **`--public-ip-address`** パラメーターを使用して [az network nic create](/cli/azure/network/nic) を実行します。 |
| [テンプレート](../virtual-network/template-samples.md) | テンプレートを使用したネットワーク インターフェイスのデプロイの詳細については、「[Network Interface in a Virtual Network with Public IP Address (パブリック IP アドレスを使用した仮想ネットワークのネットワーク インターフェイス)](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/nic-publicip-dns-vnet)」を使用してください。 |

## <a name="ip-addresses"></a>IP アドレス

Azure では次の種類の [IP アドレス](./ip-services/public-ip-addresses.md)をネットワーク インターフェイスに割り当てることができます。

- **パブリック IP アドレス** - インターネットのほか、仮想ネットワークに接続されていない他の Azure リソースとの (ネットワーク アドレス変換 (NAT) を使用しない) 送受信に使用します。 NIC へのパブリック IP アドレスの割り当てはオプションです。 パブリック IP アドレスには少額の費用がかかり、サブスクリプションごとに使用できる数には上限があります。
- **プライベート IP アドレス** - 仮想ネットワーク、オンプレミスのネットワーク、(NAT を使用した) インターネットでの通信に使用します。 少なくとも 1 つのプライベート IP アドレスを VM に割り当てる必要があります。 Azure での NAT の詳細については、「[Azure の送信用接続の詳細](../load-balancer/load-balancer-outbound-connections.md)」を参照してください。

パブリック IP アドレスを次に割り当てることができます。

* 仮想マシン
* パブリック ロード バランサー

プライベート IP アドレスを次に割り当てることができます。

* 仮想マシン
* 内部ロード バランサー

VM への IP アドレスの割り当てには、ネットワーク インターフェイスを使用します。

IP アドレスをリソースに指定する方法には、動的と静的の 2 種類があります。 Azure の場合、IP アドレスを指定する既定の方法は動的です。 IP アドレスは作成時には指定されません。 代わりに、IP アドレスの指定は、VM の作成時または停止された VM の起動時に行われます。 IP アドレスは、VM を停止または削除すると解放されます。

VM の IP アドレスが変わらないようにするため、割り当て方法を明示的に "静的" に設定できます。 この場合、IP アドレスが即座に割り当てられます。 VM を削除するか、その割り当て方法を "動的" に変更した場合にのみ、IP アドレスは解放されます。

次の表に、IP アドレスの作成に使用できる方法の一覧を示します。

| Method | 説明 |
| ------ | ----------- |
| [Azure Portal](./ip-services/virtual-network-deploy-static-pip-arm-portal.md) | 既定では、パブリック IP アドレスは動的です。 この IP アドレスは、VM を停止または削除するときに変わる可能性があります。 VM で常に同じパブリック IP アドレスが使用されるようにするには、静的パブリック IP アドレスを作成します。 既定では、VM の作成時にポータルによって動的プライベート IP アドレスが NIC に割り当てられます。 この IP アドレスは、VM の作成後に "静的" に変更できます。|
| [Azure PowerShell](./ip-services/virtual-network-deploy-static-pip-arm-ps.md) | **`-AllocationMethod`** パラメーターを Dynamic または Static に設定して [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) を実行します。 |
| [Azure CLI](./ip-services/virtual-network-deploy-static-pip-arm-cli.md) | **`--allocation-method`** パラメーターを Dynamic または Static に設定して [az network public-ip create](/cli/azure/network/public-ip) を実行します。 |
| [テンプレート](../virtual-network/template-samples.md) | テンプレートを使用したパブリック IP アドレスのデプロイの詳細については、「[Network Interface in a Virtual Network with Public IP Address (パブリック IP アドレスを使用した仮想ネットワークのネットワーク インターフェイス)](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/nic-publicip-dns-vnet)」を使用してください。 |

パブリック IP アドレスの作成後、それを NIC に割り当てて VM に関連付けることができます。

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="virtual-network-and-subnets"></a>仮想ネットワークとサブネット

サブネットとは、仮想ネットワーク内の IP アドレスの範囲です。 仮想ネットワークは、整理とセキュリティのために複数のサブネットに分割できます。 VM の各 NIC は、1 つの仮想ネットワーク内の 1 つのサブネットに接続されます。 仮想ネットワーク内の (同じまたは異なる) サブネットに接続された NIC は、追加の構成をしなくても互いに通信できます。

仮想ネットワークを設定するときに、使用できるアドレス空間やサブネットなど、トポロジを指定します。 仮想ネットワークが他の仮想ネットワークまたはオンプレミスのネットワークに接続されている場合は、重複しないアドレス範囲を選択します。 IP アドレスはプライベートであり、インターネットからアクセスすることはできません。 Azure の場合、任意のアドレス範囲はプライベート仮想ネットワークの IP アドレス空間の一部として扱われます。 このアドレス範囲は、仮想ネットワーク内、相互接続された仮想ネットワーク内、オンプレミスの場所からのみ到達可能です。

内部ネットワークの担当者が他にいる組織で作業する場合、アドレス空間を選択する前にその担当者と相談してください。 アドレス空間に重複がないことを確認します。 使用したい空間を彼らに連絡して、同じ IP アドレスの範囲が他で使用されないようにします。

サブネット間には、既定ではセキュリティの境界がありません。 それぞれのサブネットにある仮想マシンは通信できます。 デプロイでセキュリティの境界が必要な場合は、**ネットワーク セキュリティ グループ (NSG)** を使用して、サブネット間と VM 間のトラフィック フローを制御します。

次の表に、仮想ネットワークとサブネットの作成に使用できる方法の一覧を示します。

| メソッド | 説明 |
| ------ | ----------- |
| [Azure Portal](../virtual-network/quick-create-portal.md) | VM を作成する際、仮想ネットワークを Azure に作成させる場合、その名前は、仮想ネットワークが含まれたリソース グループ名と **`-vnet`** の組み合わせになります。 アドレス空間は 10.0.0.0/24、必要なサブネット名は **default**、サブネットのアドレス範囲は 10.0.0.0/24 です。 |
| [Azure PowerShell](../virtual-network/quick-create-powershell.md) | [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworkSubnetConfig) と [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して、サブネットと仮想ネットワークを作成します。 [Add-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Add-AzVirtualNetworkSubnetConfig) を使用して既存の仮想ネットワークにサブネットを追加することもできます。 |
| [Azure CLI](../virtual-network/quick-create-cli.md) | サブネットと仮想ネットワークは同時に作成されます。 **`--subnet-name`** パラメーターにサブネット名を指定して [az network vnet create](/cli/azure/network/vnet) を実行します。 |
| [テンプレート](../virtual-network/template-samples.md) | テンプレートを使用して仮想ネットワークとサブネットを作成する方法の詳細については、「[2 つのサブネットを含む Virtual Network](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/vnet-two-subnets)」を参照してください。 |

## <a name="network-security-groups"></a>ネットワーク セキュリティ グループ

[ネットワーク セキュリティ グループ (NSG)](../virtual-network/virtual-network-vnet-plan-design-arm.md) には、サブネット、NIC、またはその両方に対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) ルールが含まれています。 NSG は、サブネットかサブネットに接続された個々の NIC に関連付けることができます。 NSG がサブネットに関連付けられている場合、ACL ルールはそのサブネット内のすべての VM に適用されます。 NSG を直接 NIC に関連付けることによって、個々の NIC に対するトラフィックを制限できます。

NSG には受信と送信の 2 つのルール セットがあります。 ルールの優先順位は、各セット内で一意である必要があります。 

各ルールには次のプロパティがあります。

* Protocol
* 送信元と送信先のポート範囲
* アドレス プレフィックス
* トラフィックの方向
* 優先度
* アクセスの種類

すべての NSG に既定のルール一式が含まれています。 既定の規則は削除できません。 最も低い優先順位が割り当てられており、作成したルールで上書きすることはできません。

NIC に対して NSG を関連付ける場合、NSG のネットワーク アクセス ルールが、その NIC にのみ適用されます。 複数の NIC がある VM の NIC の 1 つに NSG を適用した場合、残りの NIC へのトラフィックに影響はありません。 NIC (デプロイメント モデルによっては VM) のほか、NIC または VM の宛先のサブネットに異なる NSG を関連付けられます。 優先順位はトラフィックの方向に基づいて付与されます。

仮想マシンと仮想ネットワークを計画するときは、必ず NSG を[計画](../virtual-network/virtual-network-vnet-plan-design-arm.md) してください。

次の表に、ネットワーク セキュリティ グループの作成に使用できる方法の一覧を示します。

| Method | 説明 |
| ------ | ----------- |
| [Azure Portal](../virtual-network/tutorial-filter-network-traffic.md) | Azure Portal で VM を作成する場合、NSG は自動的に作成され、ポータルで作成された NIC に関連付けられます。 NSG の名前は、VM の名前と **`-nsg`** の組み合わせです。 </br> この NSG には、1 つの受信規則が含まれています。 </br> 優先度は 1000 です。 </br> サービスは RDP に設定されます。 </br> プロトコルは TCP に設定されます。 </br> ポートは 3389 に設定されます。 </br> アクションは **許可** に設定されます。 </br> VM へのその他の受信トラフィックを許可する場合は、別のルールを作成します。 |
| [Azure PowerShell](../virtual-network/tutorial-filter-network-traffic.md) | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) を使用し、必要なルールの情報を指定します。 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) を使用して NSG を作成します。 [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) を使用して NSG をサブネット用に構成します。 [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) を使用して NSG を仮想ネットワークに追加します。 |
| [Azure CLI](../virtual-network/tutorial-filter-network-traffic-cli.md) | [az network nsg create](/cli/azure/network/nsg) を使用して、最初に NSG を作成します。 [az network nsg rule create](/cli/azure/network/nsg/rule) を使用して、その NSG にルールを追加します。 [az network vnet subnet update](/cli/azure/network/vnet/subnet) を使用して NSG をサブネットに追加します。 |
| [テンプレート](../virtual-network/template-samples.md) | テンプレートを使用したネットワーク セキュリティ グループのデプロイについては、「[Create a Network Security Group (ネットワーク セキュリティ グループの作成)](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/security-group-create)」をガイドとして使用してください。 |

## <a name="load-balancers"></a>ロード バランサー

[Azure Load Balancer](../load-balancer/load-balancer-overview.md) は、高可用性と優れたネットワーク パフォーマンスをアプリケーションに提供します。 ロード バランサーは、VM への[着信インターネット トラフィックを負荷分散](../load-balancer/components.md#frontend-ip-configurations)したり、[VNet 内の VM 間のトラフィックを負荷分散](../load-balancer/components.md#frontend-ip-configurations)するために構成できます。 ロード バランサーでは、クロスプレミス ネットワークでオンプレミスのコンピューターと VM の間でトラフィックの負荷分散を行ったり、外部トラフィックを特定の VM に転送したりすることもできます。

ロード バランサーにより、次の間の受信トラフィックと送信トラフィックがマップされます。  

* ロード バランサーのパブリック IP アドレスとポート。

* VM のプライベート IP アドレスとポート。

ロード バランサーを作成する際には、次の構成要素も考慮する必要があります。

- **フロントエンド IP 構成** – ロード バランサーには、1 つ以上のフロントエンド IP アドレスを含めることができます。 これらの IP アドレスは、受信トラフィック用として機能します。
- **バックエンド アドレス プール** - 負荷分散先の NIC に関連付けられた IP アドレスです。
- **[ポート フォワーディング](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)** - 受信トラフィックがフロントエンド IP を通過し、インバウンド NAT 規則を利用するバックエンド IP に分散される方法を定義します。
- **ロード バランサー規則** - 特定のフロントエンド IP とポートの組み合わせを、一連のバックエンド IP アドレスとポートの組み合わせにマップします。 1 つのロード バランサーで複数の負荷分散規則を使用できます。 各規則は、VM に関連付けられた、フロントエンド IP/ポートとバックエンド IP/ポートの組み合わせです。
- **[プローブ](../load-balancer/load-balancer-custom-probe-overview.md)** - VM の正常性を監視します。 プローブが応答できない場合、ロード バランサーは異常な VM への新しい接続の送信を停止します。 既存の接続への影響はなく、新しい接続が正常な VM に送信されます。
- **[アウトバウンド規則](../load-balancer/load-balancer-outbound-connections.md#outboundrules)** - アウトバウンド規則では、Standard Load Balancer のバックエンド プールによって識別されたすべての仮想マシンまたはインスタンスがフロントエンドに変換されるように、アウトバウンド ネットワーク アドレス変換 (NAT) が構成されます。

次の表に、インターネットに接続するロード バランサーの作成に使用できる方法の一覧を示します。

| Method | 説明 |
| ------ | ----------- |
| Azure portal |  [Azure Portal を使用して VM へのインターネット トラフィックを負荷分散](../load-balancer/quickstart-load-balancer-standard-public-portal.md)できます。 |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) | 以前作成したパブリック IP アドレスの識別子を指定するために、 **`-PublicIpAddress`** パラメーターを使用して [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) を実行します。 [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) を使用してバックエンド アドレス プールの構成を作成します。 [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) を使用して、作成済みのフロントエンド IP 構成に関連付けられた受信 NAT 規則を作成します。 [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) を使用して必要なプローブを作成します。 [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) を使用してロード バランサーの構成を作成します。 [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) を使用してロード バランサーを作成します。|
| [Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md) | [az network lb create](/cli/azure/network/lb) を使用してロード バランサーの初期構成を作成します。 [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) を使用して、以前作成したパブリック IP アドレスを追加します。 [az network lb address-pool create](/cli/azure/network/lb/address-pool) を使用して、バックエンド アドレス プールの構成を追加します。 [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule) を使用して NAT 規則を追加します。 [az network lb rule create](/cli/azure/network/lb/rule) を使用してロード バランサー規則を追加します。 [az network lb probe create](/cli/azure/network/lb/probe) を使用してプローブを追加します。 |
| [テンプレート](../load-balancer/quickstart-load-balancer-standard-public-template.md) | テンプレートを使用したロード バランサーのデプロイについては、[ロード バランサーでの 3 つの VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/load-balancer-standard-create) に関するページをガイドとして使用してください。 |

次の表に、内部ロード バランサーの作成に使用できる方法の一覧を示します。

| Method | 説明 |
| ------ | ----------- |
| Azure portal | [Azure portal のロード バランサーを使用して内部トラフィックを負荷分散](../load-balancer/quickstart-load-balancer-standard-internal-portal.md)できます。 |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) | ネットワーク サブネットのプライベート IP アドレスを指定するために、 **`-PrivateIpAddress`** パラメーターを使用して [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) を実行します。 [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) を使用してバックエンド アドレス プールの構成を作成します。 [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) を使用して、作成済みのフロントエンド IP 構成に関連付けられた受信 NAT 規則を作成します。 [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) を使用して必要なプローブを作成します。 [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) を使用してロード バランサーの構成を作成します。 [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) を使用してロード バランサーを作成します。|
| [Azure CLI](../load-balancer/quickstart-load-balancer-standard-internal-cli.md) | [az network lb create](/cli/azure/network/lb) コマンドを使用してロード バランサーの初期構成を作成します。 プライベート IP アドレスを定義するために、 **`--private-ip-address`** パラメーターを使用して [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) を実行します。 [az network lb address-pool create](/cli/azure/network/lb/address-pool) を使用して、バックエンド アドレス プールの構成を追加します。 [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule) を使用して NAT 規則を追加します。 [az network lb rule create](/cli/azure/network/lb/rule) を使用してロード バランサー規則を追加します。 [az network lb probe create](/cli/azure/network/lb/probe) を使用してプローブを追加します。|
| [テンプレート](../load-balancer/quickstart-load-balancer-standard-internal-template.md) | テンプレートを使用したロード バランサーのデプロイについては、[ロード バランサーでの 2 つの VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/2-vms-internal-load-balancer) に関するページをガイドとして使用してください。 |

## <a name="virtual-machines"></a>仮想マシン

仮想マシンは同一の仮想ネットワーク内に作成でき、プライベート IP アドレスを使用して互いに接続できます。 仮想マシンは、異なるサブネット内にある場合に接続できます。 接続には、ゲートウェイを構成したり、パブリック IP アドレスを使用する必要はありません。 VM を仮想ネットワークに入れるには、仮想ネットワークを作成します。 それぞれの VM を作成する場合は、仮想ネットワークとサブネットに割り当てます。 デプロイ中または起動中に、仮想マシンでネットワーク設定が取得されます。

仮想マシンには、デプロイ時に IP アドレスが割り当てられます。 複数の VM を仮想ネットワークまたはサブネットにデプロイする場合、それらの起動時に IP アドレスが割り当てられます。 静的 IP を VM に割り当てることもできます。 静的 IP を割り当てる場合、間違って別の VM に静的 IP を再使用しないよう、特定のサブネットを使うことを検討してください。

作成した VM を後になって仮想ネットワークに移行したくなった場合、構成の変更は容易ではありません。 仮想ネットワークに VM をデプロイし直します。 デプロイし直す場合、接続されたディスクを除いて VM を削除し、仮想ネットワーク内の元のディスクを使用して VM を作成し直すのが最も簡単です。

次の表に、VNet に VM を作成する際に使用できる方法の一覧を示します。

| Method | 説明 |
| ------ | ----------- |
| [Azure Portal](../virtual-machines/windows/quick-create-portal.md) | 前に述べた既定のネットワーク設定を使用して、1 つの NIC を備えた VM を作成します。 複数の NIC を備えた VM を作成するには、別の方法を使用する必要があります。 |
| [Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md) | [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) を使用して、以前作成した NIC を VM 構成に追加します。 |
| [Azure CLI](../virtual-machines/linux/create-cli-complete.md) | VM を作成し、個々のステップとして構築された仮想ネットワーク、サブネット、NIC に接続します。 |
| [テンプレート](../virtual-machines/windows/ps-template.md) | テンプレートを使用した VM のデプロイについては、「[Very simple deployment of a Windows VM (Windows VM の非常に簡単なデプロイ)](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-simple-windows)」をガイドとして使用してください。 |

## <a name="virtual-network-nat"></a>Virtual Network NAT

Virtual Network NAT (ネットワーク アドレス変換) は、仮想ネットワーク用のアウトバウンドのみのインターネット接続を簡単に行えるようにするものです。 これをサブネットに対して構成した場合、指定した静的パブリック IP アドレスがすべてのアウトバウンド接続で使用されます。 ロード バランサーや、仮想マシンに直接アタッチされたパブリック IP アドレスがなくても、アウトバウンド接続が可能となります。 NAT はフル マネージドで、高い回復性を備えています。

NAT では、アウトバウンド接続をサブネットごとに定義できます。 同じ仮想ネットワーク内にある複数のサブネットに対し、それぞれ異なる NAT を割り当てることができます。 サブネットの構成は、使用する NAT ゲートウェイ リソースを指定することで行います。 UDP と TCP のアウトバウンド フローには、それがどの仮想マシン インスタンスから送信されたものであれ、すべて NAT が使用されます。
NAT は、Standard SKU のパブリック IP アドレス リソースとパブリック IP プレフィックス リソースのどちらか、またはその両方を組み合わせたものと共に利用することができます。 パブリック IP プレフィックスを直接使用できるほか、複数の NAT ゲートウェイ リソースにプレフィックスのパブリック IP アドレスを割り振ることもできます。 すべてのトラフィックは、NAT によって、プレフィックスの IP アドレス範囲に調整されます。 デプロイの IP フィルター処理は容易です。

サブネットのすべてのアウトバウンド トラフィックは NAT によって自動的に処理され、ユーザーによる構成は不要です。 ユーザー定義ルートは必要ありません。 NAT は他のアウトバウンド シナリオよりも優先され、サブネットの既定のインターネット宛先に取って代わります。

フレキシブル オーケストレーション モードの仮想マシン スケール セットによって作成された仮想マシンには、既定のアウトバウンド アクセスがありません。 仮想マシン スケール セットのフレキシブル オーケストレーション モードに推奨されるアウトバウンド アクセス方法は、仮想ネットワーク NAT です。

NAT ゲートウェイ リソースと仮想ネットワーク NAT の詳細については、「[Azure Virtual Network NAT とは](./nat-gateway/nat-overview.md)」を参照してください。

次の表に、NAT ゲートウェイ リソースの作成に使用できる方法の一覧を示します。

| メソッド | 説明 |
| ------ | ----------- |
| [Azure Portal](./nat-gateway/tutorial-create-nat-gateway-portal.md) | 仮想ネットワーク、サブネット、パブリック IP、NAT ゲートウェイ、仮想マシンを作成して、NAT ゲートウェイ リソースをテストします。 |
| [Azure PowerShell](./nat-gateway/tutorial-create-nat-gateway-powershell.md) | NAT ゲートウェイ リソースを作成する [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway) の使用が含まれます。 仮想ネットワーク、サブネット、パブリック IP、NAT ゲートウェイ、仮想マシンを作成して、NAT ゲートウェイ リソースをテストします。 |
| [Azure CLI](./nat-gateway/tutorial-create-nat-gateway-cli.md) | NAT ゲートウェイ リソースを作成する [az network nat gateway create](/cli/azure/network/nat#az_network_nat_gateway_create) の使用が含まれます。 仮想ネットワーク、サブネット、パブリック IP、NAT ゲートウェイ、仮想マシンを作成して、NAT ゲートウェイ リソースをテストします。 |
| [テンプレート](./nat-gateway/quickstart-create-nat-gateway-template.md) | 仮想ネットワーク、サブネット、パブリック IP、NAT ゲートウェイ リソースを作成します。 |

## <a name="azure-bastion"></a>Azure Bastion 

Azure Bastion は、仮想ネットワーク内の仮想マシンにセキュリティで保護された管理接続を提供するためにデプロイされます。 Azure Bastion サービスを使用すると、仮想ネットワーク内の VM に対して安全かつシームレスに RDP と SSH を実行することができます。 Azure Bastion を使用すると、VM のパブリック IP を公開することなく接続できます。 接続は、Azure portal から直接行われ、追加のクライアント、エージェント、またはソフトウェアの一部を必要としません。 Azure Bastion は、Standard SKU のパブリック IP アドレスをサポートしています。

Azure Bastion の詳細については、「[Azure Bastion とは](../bastion/bastion-overview.md)」を参照してください。

次の表に、Azure Bastion デプロイの作成に使用できる方法の一覧を示します。

| メソッド | 説明 |
| ------ | ----------- |
| [Azure Portal](../bastion/quickstart-host-portal.md) | 仮想ネットワーク、サブネット、パブリック IP、bastion ホスト、仮想マシンを作成します。 |
| [Azure PowerShell](../bastion/bastion-create-host-powershell.md) | 仮想ネットワーク、サブネット、パブリック IP、bastion ホストを作成します。 bastion ホストを作成する [New-AzBastion](/powershell/module/az.network/new-azbastion) の使用が含まれます。 |
| [Azure CLI](../bastion/create-host-cli.md) | 仮想ネットワーク、サブネット、パブリック IP、bastion ホストを作成します。 bastion ホストを作成する [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) の使用が含まれます。 |
| [テンプレート](../virtual-network/template-samples.md) | Azure Bastion ホストをサンプル デプロイと統合するテンプレート デプロイの例については、「[クイックスタート: ARM テンプレートを使用して VM の負荷を分散するパブリック ロード バランサーを作成する](../load-balancer/quickstart-load-balancer-standard-public-template.md)」を参照してください。 |

## <a name="next-steps"></a>次のステップ
VM 用の Azure 仮想ネットワークの管理方法に関する VM 固有の手順については、[Windows](../virtual-machines/windows/tutorial-virtual-network.md) または [Linux](../virtual-machines/linux/tutorial-virtual-network.md) のチュートリアルを参照してください。

VM の負荷を分散し、[Windows](../virtual-machines/windows/tutorial-load-balancer.md) または [Linux](../virtual-machines/linux/tutorial-load-balancer.md) 用の高可用性アプリケーションを作成する方法に関するチュートリアルもあります。

- [ユーザー定義のルートと IP 転送](../virtual-network/virtual-networks-udr-overview.md)を構成する方法について確認します。
- [VNet 間の接続](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)を構成する方法について確認します。
- [ルートのトラブルシューティング](../virtual-network/diagnose-network-routing-problem.md)の方法について確認します。
- [仮想マシンのネットワーク帯域幅](../virtual-network/virtual-machine-network-throughput.md)について確認します。