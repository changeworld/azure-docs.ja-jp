---
title: Azure パブリック IP アドレスの作成、変更、削除 | Microsoft Docs
description: パブリック IP アドレスの作成、変更、削除の方法について説明します。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: 7319fea96f0592b9a20a591ad603d575e440704d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="create-change-or-delete-a-public-ip-address"></a>パブリック IP アドレスの作成、変更、削除

パブリック IP アドレスとその作成、変更、削除の方法について説明します。 パブリック IP アドレスは、変更可能な独自の設定を備えたリソースです。 パブリック IP アドレスを他の Azure リソースに割り当てることで次のことが可能となります。
- Azure Virtual Machines、Azure Virtual Machine Scale Sets、Azure VPN Gateway、Application Gateway、インターネットに接続する Azure Load Balancer などのリソースへの受信インターネット接続。 パブリック IP アドレスが割り当てられていない Azure リソースは、インターネットからの受信方向の通信を受信できません。 一部の Azure リソースは何もしなくてもパブリック IP アドレスでアクセスできますが、そうではないリソースにインターネットからアクセスするには、パブリック IP アドレスを割り当てる必要があります。
- 予測できる IP アドレスを使ったインターネットへの送信方向の接続。 たとえば、仮想マシンは、パブリック IP アドレスが割り当てられていなくてもインターネットに対して送信方向の通信を行うことができますが、そのアドレスは Azure によって予測できないパブリック アドレスに変換されたネットワーク アドレスです。 パブリック IP アドレスをリソースに割り当てると、送信方向の接続に使われる IP アドレスがわかります。 アドレスは、予測することはできますが、選ばれている割り当て方法によっては変化する可能性があります。 詳細については、「[パブリック IP アドレスの作成](#create-a-public-ip-address)」を参照してください。 Azure リソースからの送信接続の詳細については、[送信用接続の詳細](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

## <a name="before-you-begin"></a>開始する前に

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- ポータルを使用する場合は、https://portal.azure.com を開き、Azure アカウントでログインします。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/powershell) でコマンドを実行するか、お使いのコンピューターから PowerShell を実行してください。 Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 このチュートリアルには、Azure PowerShell モジュール バージョン 5.2.0 以降が必要です。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。
- Azure コマンド ライン インターフェイス (CLI) コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、お使いのコンピューターから CLI を実行してください。 このチュートリアルには、Azure CLI バージョン 2.0.26 以降が必要です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 Azure CLI をローカルで実行している場合、`az login` を実行して Azure との接続を作成することも必要です。

パブリック IP アドレスには、わずかですが料金が発生します。 料金については、「[IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses)」ページを参照してください。 

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

1. Azure Portal 上部に *[リソースの検索]* というテキストが表示されたボックスがあります。そこに「*パブリック IP アドレス*」と入力します。 検索結果に **[パブリック IP アドレス]** が表示されたら、それをクリックします。
2. 表示された **[パブリック IP アドレス]** ブレードの **[+ 追加]** をクリックします。
3. 表示された **[パブリック IP アドレスの作成]** ブレードで以下の設定の値を入力するか選択し、**[作成]** をクリックします。

    |Setting|必須|詳細|
    |---|---|---|
    |SKU|[はい]|SKU の導入前に作成されたパブリック IP アドレスはすべて、**Basic** SKU のパブリック IP アドレスです。  パブリック IP アドレスが作成された後に SKU を変更することはできません。 スタンドアロンの仮想マシン、可用性セット内の仮想マシン、または仮想マシン スケール セットは、Basic または Standard SKU を使用できます。  可用性セットまたはスケール セット内の仮想マシン間での SKU の混在は許可されません。 **Basic** SKU: 可用性ゾーンをサポートするリージョンにパブリック IP アドレスを作成している場合、既定では **[Availability zone] \(可用性ゾーン)** 設定が *[なし]* に設定されます。 パブリック IP アドレス用に特定のゾーンを保証するには、可用性ゾーンを選択できます。 **Standard** SKU: Standard SKU のパブリック IP を仮想マシンまたはロード バランサーのフロント エンドに関連付けることができます。 可用性ゾーンをサポートするリージョンにパブリック IP アドレスを作成している場合、既定では **[Availability zone] \(可用性ゾーン)** 設定が *[Zone-redundant] \(ゾーン冗長)* に設定されます。 可用性ゾーンの詳細については、**[Availability zone] \(可用性ゾーン)** 設定を参照してください。 アドレスを標準のロード バランサーに関連付ける場合は、Standard SKU が必要です。 標準のロード バランサーの詳細については、「[Azure Load Balancer の Standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。 標準 SKU のパブリック IP アドレスを仮想マシンのネットワーク インターフェイスに割り当てるときは、[ネットワーク セキュリティ グループ](security-overview.md#network-security-groups)で、特定のトラフィックを明示的に許可する必要があります。 ネットワーク セキュリティ グループを作成して関連付け、目的のトラフィックを明示的に許可するまで、そのリソースとの通信は失敗します。|
    |Name|[はい]|選択したリソース グループ内で一意となる名前を使用してください。|
    |IP バージョン|[はい]| IPv4 または IPv6 を選びます。 パブリック IPv4 アドレスは複数の Azure リソースに割り当てることができますが、IPv6 のパブリック IP アドレスはインターネットに接続するロード バランサーにのみ割り当てることができます。 ロード バランサーは、複数の Azure 仮想マシンに IPv6 トラフィックを負荷分散できます。 詳細については、[仮想マシンへの IPv6 トラフィックの負荷分散](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。 **Standard SKU** を選択した場合、*IPv6* を選択するオプションはありません。 **Standard SKU** を使用している場合は IPv4 アドレスしか作成できません。|
    |IP アドレスの割り当て|[はい]|**[動的]:** 動的アドレスは、仮想マシンに接続されたネットワーク インターフェイスにパブリック IP アドレスが関連付けられ、仮想マシンが初めて起動された後でのみ割り当てられます。 ネットワーク インターフェイスの接続先の仮想マシンが停止 (割り当て解除) されると、動的アドレスが変化する場合があります。 仮想マシンが再起動された場合や、停止された (ただし、割り当て解除されない) 場合、このアドレスは同じままです。 **[静的]:** 静的アドレスは、パブリック IP アドレスの作成時に割り当てられます。 仮想マシンが停止 (割り当て解除) された状態になっても、静的アドレスは変化しません。 このアドレスは、ネットワーク インターフェイスが削除された場合にのみ解放されます。 ネットワーク インターフェイスの作成後に、割り当て方法を変更することができます。 **[IP バージョン]** として *[IPv6]* を選択した場合、割り当て方法は *[動的]* です。 **[SKU]** として *[Standard]* を選択した場合、割り当て方法は *[静的]* です。|
    |アイドル タイムアウト (分)|いいえ |クライアントからキープアライブ メッセージを送信しなくても TCP 接続または HTTP 接続が開いたまま維持される時間 (分)。 **[IP バージョン]** で [IPv6] を選んだ場合、この値を変更することはできません。 |
    |DNS 名ラベル|いいえ |名前の作成先となる Azure の場所 (すべてのサブスクリプション、すべての顧客) で一意となるようにしてください。 指定した名前を使用してリソースに接続できるよう、DNS 内の名前と IP アドレスが Azure によって自動的に登録されます。 指定した名前に既定のサブネット (*location.cloudapp.azure.com* など、<location> は選択した場所) が付加されて、完全修飾 DNS 名が作成されます。 両方のアドレス バージョンの作成を選んだ場合は、IPv4 アドレスと IPv6 アドレスの両方に同じ DNS 名が割り当てられます。 Azure の既定の DNS には IPv4 A と IPv6 AAAA の両方の名前レコードが含まれており、DNS 名が参照されると両方のレコードで応答します。 どちらのアドレス (IPv4 または IPv6) と通信するかは、クライアントが選択します。 代わりに、既定のサフィックスを持つ DNS 名ラベルを使用することで、Azure DNS サービスを使用して、パブリック IP アドレスに解決されるカスタム サフィックスを持つ DNS 名を構成することもできます。 詳細については、[Azure パブリック IP アドレスを使用した Azure DNS の使用](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)に関するページを参照してください。|
    |IPv6 アドレスを作成します/IPv4 アドレスを作成します|いいえ | IPv6 または IPv4 のどちらが表示されるかは、**[IP バージョン]** の選択に依存します。 たとえば、**[IP バージョン]** で **[IPv4]** を選んだ場合、ここには **IPv6** が表示されます。 **[SKU]** として *[Standard]* を選択した場合、IPv6 アドレスを作成するオプションはありません。
    |名前 (**[IPv6 アドレスを作成します]/[IPv4 アドレスを作成します]** チェック ボックスをオンにした場合にのみ表示されます)|はい (**[IPv6 アドレスを作成します]/[IPv4 アドレスを作成します]** チェック ボックスをオンにした場合)。|この名前は、この表の最初の **[名前]** で入力した名前と異なっている必要があります。 IPv4 アドレスと IPv6 アドレス両方の作成を選ぶと、2 つのパブリック IP アドレス リソースが作成されて、それぞれに IP アドレスのバージョンが割り当てられます。|
    |IP アドレスの割り当て (**[IPv6 アドレスを作成します]/[IPv4 アドレスを作成します]** チェック ボックスをオンにした場合にのみ表示されます)|はい (**[IPv6 アドレスを作成します]/[IPv4 アドレスを作成します]** チェック ボックスをオンにした場合)。|チェック ボックスに **[IPv4 アドレスを作成します]** と表示されている場合は、割り当て方法を選ぶことができます。 チェック ボックスに **[IPv6 アドレスを作成します]** と表示されている場合は、**[動的]** が必須なので、割り当て方法を選ぶことはできません。|
    |[サブスクリプション]|[はい]|パブリック IP アドレスを関連付けるリソースと同じ[サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)内に存在する必要があります。|
    |リソース グループ|[はい]|所属する[リソース グループ](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)は、パブリック IP アドレスを関連付けるリソースと同じであっても異なっていてもかまいません。|
    |場所|[はい]|パブリック IP アドレスを関連付けるリソースと同じ[場所](https://azure.microsoft.com/regions) (リージョンとも呼ばれる) に存在する必要があります。|
    |可用性ゾーン| いいえ  | この設定は、サポートされている場所を選択した場合にのみ表示されます。 サポートされている場所の一覧については、「[Availability zones overview (可用性ゾーンの概要)](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。 **Basic** SKU を選択した場合は、*[なし]* が自動的に選択されます。 特定のゾーンを保証したい場合は、特定のゾーンを選択できます。 どちらの選択肢もゾーン冗長ではありません。 **Standard** SKU を選択した場合は、[Zone-redundant] \(ゾーン冗長) が自動的に選択され、データ パスのゾーン障害からの復元性が高くなります。 特定のゾーンを保証したい場合は (これは、ゾーン障害からの復元性が高くありません)、特定のゾーンを選択できます。
  

**コマンド**

ポータルでは 2 つのパブリック IP アドレス リソース (IPv4 と IPv6) を作成できますが、次の CLI および PowerShell のコマンドでは、どちらか一方の IP バージョンのアドレスで 1 つのリソースが作成されます。 2 つのパブリック IP アドレス リソース (IP バージョンごとに 1 つ) を作成したい場合は、コマンドを 2 回実行し、異なる名前とバージョンでパブリック IP アドレス リソースを指定する必要があります。 

|ツール|コマンド|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>パブリック IP アドレスを表示、設定変更、削除する

1. Azure Portal 上部に *[リソースの検索]* というテキストが表示されたボックスがあります。そこに「*パブリック IP アドレス*」と入力します。 検索結果に **[パブリック IP アドレス]** が表示されたら、それをクリックします。
2. 表示された **[パブリック IP アドレス]** ブレードで、表示、設定変更、削除の対象となるパブリック IP アドレスの名前をクリックします。
3. 表示されたパブリック IP アドレスのブレードで、実行する操作 (パブリック IP アドレスの表示、削除、変更) に応じて次のいずれかを実行します。
    - **表示**: ブレードの **[概要]** セクションに、関連付けられているネットワーク インターフェイス (アドレスがネットワーク インターフェイスに関連付けられている場合) など、パブリック IP アドレスの主要な設定が表示されます。 ポータルでは、アドレスのバージョン (IPv4 または IPv6) は表示されません。 バージョン情報を表示するには、PowerShell または CLI のコマンドを使って、パブリック IP アドレスを表示します。 IP アドレスのバージョンが IPv6 の場合、割り当てられたアドレスはポータル、PowerShell、または CLI では表示されません。 
    - **削除**: パブリック IP アドレスを削除するには、ブレードの **[概要]** セクションで **[削除]** をクリックします。 現在 IP 構成に関連付けられているアドレスは削除できません。 アドレスが構成に関連付けられている場合は、**[関連付け解除]** をクリックすると、IP 構成からアドレスの関連付けが解除されます。
    - **変更**: **[構成]** をクリックします。 この記事の「[パブリック IP アドレスの作成](#create-a-public-ip-address)」セクションにある手順 4. の情報を参考にして設定を変更します。 IPv4 アドレスの割り当て方法を静的から動的に変更する場合はまず、IP 構成からパブリック IPv4 アドレスの関連付けを解除する必要があります。 そのうえで、動的な割り当て方法に変更し、**[関連付け]** をクリックして、同じ IP 構成または異なる IP 構成に IP アドレスを関連付けるか、関連付けを解除したままの状態にすることができます。 パブリック IP アドレスの関連付けを解除するには、**[概要]** セクションの **[関連付け解除]** をクリックします。

>[!WARNING]
>割り当て方法を静的から動的に変更すると、パブリック IP アドレスに割り当てられた IP アドレスが失われます。 静的または動的アドレスと任意の DNS 名ラベル (定義している場合) の間のマッピングは Azure パブリック DNS サーバーによって保持されますが、仮想マシンが停止 (割り当て解除) された状態にあった後に起動されると、動的 IP アドレスが変化する場合があります。 アドレスが変化しないようにするには、静的 IP アドレスを割り当ててください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network public-ip-list](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_list) (パブリック IP アドレスの一覧表示)、[az network public-ip-show](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_show) (設定の表示)、[az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_update) (更新)、[az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_delete) (削除)|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) (パブリック IP アドレス オブジェクトの取得とその設定の表示)、[Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) (設定の更新)、[Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) (削除)|

## <a name="next-steps"></a>次の手順
次の Azure リソースの作成時にパブリック IP アドレスを割り当てます。

- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシンまたは [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシン
- [インターネットに接続する Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure VPN Gateway を使用したサイト間接続](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
