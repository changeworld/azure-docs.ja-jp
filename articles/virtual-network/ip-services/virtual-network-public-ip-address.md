---
title: Azure パブリック IP アドレスを作成、変更、または削除する
titleSuffix: Azure Virtual Network
description: パブリック IP アドレスを管理します。 パブリック IP アドレスは、変更可能な独自の設定を備えたリソースである方法を説明します。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2021
ms.author: allensu
ms.openlocfilehash: 92f472afdb642633a093ebdf765b0d8ba968bd8a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228339"
---
# <a name="create-change-or-delete-an-azure-public-ip-address"></a>Azure パブリック IP アドレスを作成、変更、または削除する

パブリック IP アドレスとその作成、変更、削除の方法について説明します。 パブリック IP アドレスは、変更可能な独自の設定を備えたリソースです。 パブリック IP アドレスをパブリック IP アドレスをサポートする Azure リソースに割り当てると、次のことが可能になります。

- インターネットから、Azure Virtual Machines (VM)、Azure Application Gateway、Azure Load Balancer、Azure VPN Gateway などのリソースへの受信。
- 予測できる IP アドレスを使ったインターネットへの送信方向の接続。

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

ポータル、PowerShell、CLI、Resource Manager テンプレートを使用してパブリック IP アドレスを作成する方法については、以下のページを参照してください。

 * [パブリック IP アドレスの作成 - ポータル](./create-public-ip-portal.md?tabs=option-create-public-ip-standard-zones)
 * [パブリック IP アドレスの作成 - PowerShell](./create-public-ip-powershell.md?tabs=option-create-public-ip-standard-zones)
 * [パブリック IP アドレスの作成 - Azure CLI](./create-public-ip-cli.md?tabs=option-create-public-ip-standard-zones)
 * [パブリック IP アドレスの作成 - テンプレート](./create-public-ip-template.md)

>[!NOTE]
>ポータルによって 2 つのパブリック IP アドレス リソース (IPv4 と IPv6) を作成できますが、PowerShell および CLI のコマンドを使用すると、どちらか一方の IP バージョンのアドレスで 1 つのリソースが作成されます。 2 つのパブリック IP アドレス リソース (IP バージョンごとに 1 つ) を作成する場合は、コマンドを 2 回実行し、異なる名前と IP バージョンでパブリック IP アドレス リソースを指定する必要があります。

作成時のパブリック IP アドレスの特定の属性の詳細については、次の表を参照してください。

   |設定|必須|詳細|
   |---|---|---|
   |IP バージョン|はい| IPv4 または IPv6 またはその両方を選択します。 両方を選択すると、パブリック IP アドレスが 2 つ (IPv4 アドレスが 1 つと IPv6 アドレスが 1 つ) 作成されます。 [Azure VNET での IPv6](ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) に関する詳細情報。|
   |SKU|はい|SKU の導入前に作成されたパブリック IP アドレスはすべて、**Basic** SKU のパブリック IP アドレスです。 パブリック IP アドレスが作成された後に SKU を変更することはできません。 スタンドアロンの仮想マシン、可用性セット内の仮想マシン、または仮想マシン スケール セットは、Basic または Standard SKU を使用できます。 可用性セットまたはスケール セットまたはスタンドアロン VM の仮想マシン間での SKU の混在は許可されません。 **Basic** SKU: 可用性ゾーンをサポートするリージョンにパブリック IP アドレスを作成している場合、既定では **[Availability zone] \(可用性ゾーン)** 設定が *[なし]* に設定されます。 基本パブリック IP では、可用性ゾーンはサポートされていません。 **Standard** SKU: Standard SKU のパブリック IP を仮想マシンまたはロード バランサーのフロント エンドに関連付けることができます。 可用性ゾーンをサポートするリージョンにパブリック IP アドレスを作成している場合、既定では **[Availability zone] \(可用性ゾーン)** 設定が *[Zone-redundant] \(ゾーン冗長)* に設定されます。 可用性ゾーンの詳細については、 **[Availability zone] \(可用性ゾーン)** 設定を参照してください。 アドレスを標準のロード バランサーに関連付ける場合は、Standard SKU が必要です。 標準のロード バランサーの詳細については、「[Azure Load Balancer の Standard SKU](../../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。 Standard SKU のパブリック IP アドレスを仮想マシンのネットワーク インターフェイスに割り当てるときは、[ネットワーク セキュリティ グループ](../../virtual-network/network-security-groups-overview.md#network-security-groups)で、特定のトラフィックを明示的に許可する必要があります。 ネットワーク セキュリティ グループを作成して関連付け、目的のトラフィックを明示的に許可するまで、そのリソースとの通信は失敗します。|
   |サービス レベル|はい|IP アドレスがリージョンに関連付けられているか ( **[Regional]\(リージョン\)** )、複数のリージョンの "エニーキャスト" であるか ( **[Global]\(グローバル\)** ) を示します。 *"グローバル レベル"の IP は Standard IP のプレビュー機能であり、現在はリージョン間のロード バランサーにのみ使用されていることに注意してください。*|
   |名前|はい|選択したリソース グループ内で一意となる名前を使用してください。|
   |IP アドレスの割り当て|はい|**[動的]:** 動的アドレスの割り当ては、パブリック IP アドレスが Azure リソースに関連付けられ、初回起動後である場合に限られます。 動的アドレスが仮想マシンなどのリソースが停止し (割り当てが解除される)、再起動した場合、動的アドレスが変わることがあります。 仮想マシンが再起動された場合や、停止された場合、このアドレスは同じままです。 パブリック IP アドレス リソースがリソースから削除されると、動的アドレスが解放されます。<br> **静的:** 静的アドレスは、パブリック IP アドレスの作成時に割り当てられます。 パブリック IP アドレス リソースが削除されるまで、静的アドレスは解放されません。 <br>注意: **[IP バージョン]** として *[IPv6]* を選択した場合、Basic SKU での割り当て方法は "*動的*" にする必要があります。  Standard SKU のアドレスは、IPv4 と IPv6 の両方に対して "*静的*" です。 |
  |ルーティング設定 |はい| 既定では、パブリック IP アドレスのルーティングの優先順位は "Microsoft ネットワーク" に設定され、Microsoft のグローバル ワイド エリア ネットワークを通してユーザーにトラフィックを配信します。  "インターネット" を選択すると、Microsoft のネットワーク上での移動が最小限に抑えられます。その代わりに、トラフィックをコスト最適化された速度で配信するために、転送 ISP ネットワークを使用します。  パブリック IP アドレスのルーティングの優先順位は、作成後に変更することはできません。  ルーティングの優先順位の詳細については、「[ルーティングの優先順位とは (プレビュー)](routing-preference-overview.md)」を参照してください。   |
   |アイドル タイムアウト (分)|いいえ|クライアントからキープアライブ メッセージを送信しなくても TCP 接続または HTTP 接続が開いたまま維持される時間 (分)。 **[IP バージョン]** で [IPv6] を選んだ場合、この値は 4 分に設定され、変更することはできません。 |
   |DNS 名ラベル|いいえ|名前の作成先となる Azure の場所 (すべてのサブスクリプション、すべての顧客) で一意となるようにしてください。 指定した名前を使用してリソースに接続できるよう、DNS 内の名前と IP アドレスが Azure によって自動的に登録されます。 指定した名前に既定のサブネット (*location.cloudapp.azure.com* など) が付加されて、完全修飾 DNS 名が作成されます。 両方のアドレス バージョンの作成を選んだ場合は、IPv4 アドレスと IPv6 アドレスの両方に同じ DNS 名が割り当てられます。 Azure の既定の DNS には IPv4 A と IPv6 AAAA の両方の名前レコードが含まれます。 既定の DNS は、DNS 参照時に両方のレコードで応答します。 どちらのアドレス (IPv4 または IPv6) と通信するかは、クライアントが選択します。 Azure DNS サービスを使用して、パブリック IP アドレスに解決されるカスタム サフィックスを持つ DNS 名を構成することもできます。 詳細については、[Azure パブリック IP アドレスを使用した Azure DNS の使用](../../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)に関するページを参照してください。|
   |名前 ("**両方**" の IP バージョンを選択した場合のみ表示)|はい、"**両方**" の IP バージョンを選択した場合|この名前は、この表の最初の **[名前]** で入力した名前と異なっている必要があります。 IPv4 アドレスと IPv6 アドレス両方の作成を選ぶと、2 つのパブリック IP アドレス リソースが作成されて、それぞれに IP アドレスのバージョンが割り当てられます。|
   |IP アドレスの割り当て ("**両方**" の IP バージョンを選択した場合のみ表示)|はい、"**両方**" の IP バージョンを選択した場合| 上記の IP アドレスの割り当てと同じ制限があります。 |
   |サブスクリプション|はい|パブリック IP を関連付けるリソースと同じ[サブスクリプション](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)内に存在する必要があります。|
   |Resource group|はい|パブリック IP を関連付けるリソースと同じであっても異なっていても、[リソース グループ](../../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)に存在させることができます。|
   |場所|はい|パブリック IP を関連付けるリソースと同じ[場所](https://azure.microsoft.com/regions) (リージョンとも呼ばれる) に存在する必要があります。|
   |可用性ゾーン| いいえ | この設定は、サポートされている場所と IP アドレスの種類を選択した場合にのみ表示されます。 **Basic** SKU パブリック IP では、**グローバル** 階層パブリック IP は Availability Zones をサポートしません。 ゾーンなし (既定のオプション)、特定のゾーン、またはゾーン冗長を選択できます。 この選択は、特定のドメイン障害要件によって異なる場合があります。 Availability Zones がサポートされる場所の一覧と詳細情報については、[Availability Zones の概要](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページを参照してください。  

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>パブリック IP アドレスを表示、設定変更、削除する

   - **表示または一覧表示**: SKU、アドレス、関連付けを含むパブリック IP の設定を確認します。 関連付けは、ロード バランサーのフロントエンド、仮想マシン、その他の Azure リソースの場合があります。
   - **変更**: 「[パブリック IP アドレスを作成する](#create-a-public-ip-address)」の情報を使用して、設定を変更します。 設定はアイドル タイムアウト、DNS 名ラベル、割り当て方法などです。 パブリック IP SKU を Basic から Standard にアップグレードする完全なプロセスについては、[Azure パブリック IP アドレスのアップグレード](./public-ip-upgrade-portal.md)に関するページを参照してください。
   
   >[!WARNING]
   >該当する IP 構成からそのアドレスを削除し ( **[削除]** セクションを参照)、パブリック IP アドレスの割り当て方法を静的から動的に変更します。 割り当て方法を静的から動的に変更すると、パブリック IP リソースに割り当てられた IP アドレスが失われます。 静的または動的アドレスと任意の DNS 名ラベル (定義している場合) の間のマッピングは Azure パブリック DNS サーバーによって保持されますが、仮想マシンが停止 (割り当て解除) された状態にあった後に起動されると、動的 IP アドレスが変化する場合があります。 アドレスが変化しないようにするには、静的 IP アドレスを割り当ててください。
   
|操作|Azure portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|表示 | パブリック IP の **[概要]** セクション内 |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) でパブリック IP アドレス オブジェクトを取得してその設定を表示| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) で設定を表示|
|List | **[パブリック IP アドレス]** カテゴリ |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) で 1 つ以上のパブリック IP アドレス オブジェクトを取得してその設定を表示|[az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_list) でパブリック IP アドレスを一覧表示|
|変更 | 関連付け解除された IP の場合は、 **[構成]** を選択し、次の操作を行います。 </br> アイドル タイムアウトを変更します。 </br> DNS 名ラベルを付けます。 </br> IP の割り当てを静的から動的に変更します。 </br> Basic IP を Standard にアップグレードします。 |[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) で設定を更新 |[az network public-ip update](/cli/azure/network/public-ip#az_network_public_ip_update) で更新 |

   - **削除**: パブリック IP を削除するには、パブリック IP オブジェクトが IP 構成または仮想マシンのネットワーク インターフェイスに関連付けられていない必要があります。 詳細については、後の表を参照してください。

|リソース|Azure portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|[仮想マシン](./remove-public-ip-address-vm.md)|**[関連付けの解除]** を選択して NIC 構成から IP アドレスの関連付けを解除してから、 **[削除]** を選択します。|[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) で NIC 構成から IP アドレスの関連付けを解除。[Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) で削除|[az network public-ip update を "--remove" パラメーターと一緒に使用して](/cli/azure/network/public-ip#az_network_public_ip_update)、IP アドレスを NIC 構成から削除します。 [az network public-ip delete](/cli/azure/network/public-ip#az_network_public_ip_delete) を使用して、パブリック IP を削除します。 |
|ロード バランサーのフロントエンド | 未使用のパブリック IP アドレスを参照し、 **[関連付け]** を選択します。 IP を置き換える、関連するフロントエンド IP 構成を持つロード バランサーを選択します。 古い IP は、仮想マシンと同じ方法を使用して削除できます。  | [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) を使用して、新しいフロントエンド IP 構成をパブリック ロード バランサーに関連付けます。 [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) を使用して、パブリック IP を削除します。 また、[Remove-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) を使用して、フロントエンド IP 構成が複数ある場合に削除できます。 | [az network lb frontend-ip update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) を使用して、新しいフロントエンド IP 構成をパブリック ロード バランサーに関連付けます。 [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) を使用して、パブリック IP を削除します。 また、[az network lb frontend-ip delete](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_delete) を使用して、フロントエンド IP 構成が複数ある場合に削除できます。 |
|ファイアウォール|該当なし| [Deallocate](../../firewall/firewall-faq.yml#how-can-i-stop-and-start-azure-firewall) でファイアウォールの割り当てを解除し、すべての IP 構成を削除 | [az network firewall ip-config delete](/cli/azure/network/firewall/ip-config#az_network_firewall_ip_config_delete) を使用して、IP を削除します。 まず PowerShell を使用して、割り当てを解除します。 |

## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

パブリック IP を持つ仮想マシン スケール セットを使用する場合、個々の仮想マシン インスタンスに関連付けられた個別のパブリック IP オブジェクトは存在しません。 ただし、パブリック IP プレフィックス オブジェクトを[使用してインスタンス IP を生成](https://azure.microsoft.com/resources/templates/vmms-with-public-ip-prefix/)することはできます。

仮想マシン スケール セットのパブリック IP アドレスを一覧表示するには、PowerShell ([Get-AzPublicIpAddress -VirtualMachineScaleSetName](/powershell/module/az.network/get-azpublicipaddress)) または CLI ([az virtual machine scale set list-instance-public-ips](/cli/azure/vmss#az_vmss_list_instance_public_ips)) を使用できます。

詳細については、[Microsoft Azure Virtual Machine Scale Sets のネットワーク](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine)に関する記事を参照してください。

## <a name="assign-a-public-ip-address"></a>パブリック IP アドレスを割り当てる

パブリック IP アドレスを割り当てる方法については、次のリソースを参照してください。

- 作成時の [Windows](../../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシンまたは [Linux](../../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシン。 IP を[既存の仮想マシン](./virtual-network-network-interface-addresses.md#add-ip-addresses)に追加します。
- [パブリック ロード バランサー](../../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway](../../application-gateway/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN ゲートウェイを使用したサイト間接続](../../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Virtual Machine Scale Set](../../virtual-machine-scale-sets/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [NAT Gateway](../nat-gateway/tutorial-create-nat-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Bastion](../../bastion/quickstart-host-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Firewall](../../firewall/tutorial-firewall-deploy-portal-policy.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [リージョン間のロード バランサー](../../load-balancer/tutorial-cross-region-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>アクセス許可

パブリック IP アドレスを管理するには、アカウントに[ネットワーク共同作成者](../../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロールが割り当てられている必要があります。 [カスタム](../../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ロールもサポートされています。 カスタム ロールには、次の表に示す適切なアクションが割り当てられている必要があります。

| アクション                                                             | 名前                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | パブリック IP アドレスの読み取り                                          |
| Microsoft.Network/publicIPAddresses/write                          | パブリック IP アドレスの作成または更新                           |
| Microsoft.Network/publicIPAddresses/delete                         | パブリック IP アドレスの削除                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | リソースへのパブリック IP アドレスの関連付け                    |

## <a name="next-steps"></a>次のステップ

パブリック IP アドレスには、わずかですが料金が発生します。 料金については、「[IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses)」ページを参照してください。

- [PowerShell](../../virtual-network/powershell-samples.md) か [Azure CLI](../../virtual-network/cli-samples.md) のサンプル スクリプト、または Azure [Resource Manager テンプレート](../../virtual-network/template-samples.md)を利用したパブリック IP アドレスの作成
- パブリック IP アドレス用に [Azure Policy 定義](../../virtual-network/policy-reference.md)を作成して割り当てる