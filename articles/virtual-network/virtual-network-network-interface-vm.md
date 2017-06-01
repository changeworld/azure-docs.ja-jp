---
title: "Azure Virtual Machines に対するネットワーク インターフェイスの追加と削除 | Microsoft Docs"
description: "仮想マシンへのネットワーク インターフェイス (NIC) の追加および仮想マシンからの NIC の削除の方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 0d609b20040572e3fb371a277603109d64a0ba37
ms.contentlocale: ja-jp
ms.lasthandoff: 05/13/2017


---

# <a name="add-network-interfaces-to-or-remove-from-virtual-machines"></a>仮想マシンに対するネットワーク インターフェイスの追加と削除

VM を作成する際に既存のネットワーク インターフェイス (NIC) を追加する方法と、停止 (割り当て解除) 状態の既存の VM に対して NIC の追加または削除を行う方法について説明します。 NIC は、Azure 仮想マシン (VM) がインターネット、Azure、オンプレミスのリソースと通信するために使用します。 VM には、1 つ以上の NIC を含めることができます。 

NIC に対して IP アドレスの追加、変更、削除が必要な場合は、[IP アドレスの追加、変更、削除](virtual-network-network-interface-addresses.md)に関する記事をご覧ください。 NIC の作成、変更、または削除が必要な場合は、[NIC の設定とタスク](virtual-network-network-interface.md)に関する記事を参照してください。

## <a name="before"></a>開始する前に

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- 各サイズの Linux VM および Windows VM でサポートされる NIC の数については、[Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) および [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の VM のサイズに関する記事をご覧ください。
- Azure アカウントを使用して、Azure Portal、Azure コマンド ライン インターフェイス (CLI)、または Azure PowerShell にログインします。 まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順を実行して、Azure PowerShell をインストールして構成します。 最新バージョンの Azure PowerShell コマンドレットがインストールされていることを確認してください。 PowerShell コマンドのヘルプとサンプルを表示するには、「`get-help <command> -full`」と入力します。
- Azure コマンド ライン インターフェイス (CLI) コマンドを使用して、この記事のタスクを実行する場合は、[Azure CLI のインストールと構成の方法](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順を実行して、Azure CLI をインストールして構成します。 最新バージョンの Azure CLI がインストールされていることを確認してください。CLI コマンドのヘルプを表示するには、「`az <command> --help`」と入力します。

## <a name="about"></a>NIC と VM の概要

VM を作成する際に既存の NIC を VM に追加 (アタッチ) できます。ただし、別の VM にアタッチされていない NIC に限られます。 既存の VM に対する NIC の追加と削除 (デタッチ) は、VM が停止 (割り当て解除) 状態にあるときに実行できます。 Azure Portal を使用して VM を作成すると、NIC が既定の設定で自動的に作成されます。 ポータルでは次のことは実行できません。

- VM の作成時に追加する既存の NIC の指定
- 複数 NIC を持つ VM の作成
- NIC の名前を指定 (ポータルは既定の名前で NIC を作成します)。

Azure PowerShell または CLI を使用すると、ポータルでは使用できないすべての属性を使用して NIC や VM を作成できます。 この後のセクションのタスクを実行する前に、次の制約と動作について検討してください。

- どの VM サイズでも NIC を少なくとも 2 つサポートしていますが、3 つ以上の NIC をサポートする VM サイズは限られます。 以前は、一部の VM サイズでは NIC を 1 つしかサポートしていませんでした。 各サイズの VM でサポートされる NIC の数については、[Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) と [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の VM のサイズに関する記事をご覧ください。 
- 以前は、複数の NIC をサポートしていて、少なくとも 2 つの NIC で作成された VM にのみ、NIC を追加できました。 VM が複数の NIC をサポートしているサイズであっても、1 つの NIC で作成された VM には NIC を追加できませんでした。 逆に、NIC を削除できるのは、少なくとも 3 つの NIC がアタッチされている VM からのみでした。これは、少なくとも 2 つの NIC で作成された VM には、常に少なくとも 2 つの NIC がアタッチされている必要があったためです。 これらの制約は、どちらも当てはまらなくなっています。 (その VM サイズでサポートされている数までであれば) 任意の数の NIC をアタッチして VM を作成できるようになりました。また、VM に常に少なくとも 1 つの NIC がアタッチされている限り、(停止 (割り当て解除) 状態にある VM から) 任意の数の NIC を追加したり、削除したりできます。
- 既定では、VM に最初にアタッチされた NIC が "*プライマリ*" NIC と定義されます。 それ以外の NIC はすべて、"*セカンダリ*" NIC になります。
- プライマリ NIC は Azure DHCP サーバーによってデフォルト ゲートウェイに割り当てられますが、セカンダリ NIC は割り当てられません。 セカンダリ NIC はデフォルト ゲートウェイに割り当てられていないため、既定では、サブネットの外部にあるリソースと通信できません。 Windows VM 内のセカンダリ NIC がサブネットの外部にあるリソースと通信できるようにするには、Windows コマンド ラインから `route add` コマンドを実行してオペレーティング システムにルートを追加します。 Linux VM の場合は、既定の動作では弱いホスト ルーティングが使用されるので、セカンダリ NIC のトラフィックを 1 つのサブネットに制限することをお勧めします。 セカンダリ NIC でサブネット外部への接続が必要な場合は、送信と受信のトラフィックが同じ NIC を使用するようにポリシー ベースのルーティングを有効にする必要があります。
- 既定では、VM からのすべての送信トラフィックで、プライマリ NIC のプライマリ IP 構成に割り当てられたプライマリ IP アドレスが使用されます。 VM のオペレーティング システム内で送信トラフィックに使用する IP アドレスを制御できますが、既定では、プライマリ NIC を経由します。
- かつては、同じ可用性セット内のすべての VM で、アタッチする NIC を 1 つにするか複数にするかを統一する必要がありました。 現在は、VM サイズでサポートされている数までであれば、NIC の数に関係なく VM が同じ可用性セットに存在できます。 ただし、VM を可用性セットに追加できるのは、VM の作成時のみです。 可用性セットについて詳しくは、[Azure での VM の可用性の管理](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)に関する記事をご覧ください。
- 同じ VM にアタッチされている複数の NIC それぞれを同一 VNet 内の異なるサブネットに接続することはできますが、異なる VNet に接続することはできません。
- 任意のプライマリまたはセカンダリ NIC の任意の IP 構成の任意の IP アドレスを Azure Load Balancer バックエンド プールに追加できます。 以前は、プライマリ NIC のプライマリ IP アドレスのみをバックエンド プールに追加できました。 IP アドレスと IP 構成について詳しくは、[IP アドレスの追加、変更、削除](virtual-network-network-interface-addresses.md)に関する記事をご覧ください。
- VM を削除しても VM にアタッチされた NIC は削除されません。 VM を削除すると、NIC は VM からデタッチされます。 この NIC を別の VM に追加することも削除することもできます。

## <a name="vm-create"></a>新しい VM への既存の NIC の追加
ポータルで VM を作成すると、NIC が既定の設定で自動的に作成されて、VM にアタッチされます。 新しい VM への既存の NIC の追加と、複数の NIC がアタッチされた VM の作成は、Azure Portal を使用して行うことはできません。 この 2 つは CLI または PowerShell を使用して行うことができます。 NIC は、作成する VM サイズでサポートされている数まで VM に追加することができます。 各サイズの VM でサポートされる NIC の数について詳しくは、[Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) と [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の VM のサイズに関する記事をご覧ください。 VM に追加した NIC を別の VM にも接続することはできません。 NIC の作成について詳しくは、[NIC の設定とタスク](virtual-network-network-interface.md#create-nic)に関する記事をご覧ください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v2.5.0/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>既存の VM への既存の NIC の追加

NIC は、NIC を追加する VM サイズでサポートされている数まで VM に追加することができます。 各サイズの VM でサポートされる NIC の数については、[Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) と [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の VM のサイズに関する記事をご覧ください。 NIC の追加先の VM は、複数の NIC をサポートし、停止 (割り当て解除) 状態になっている必要があります。 追加した NIC を別の VM にも接続することはできません。 Azure Portal を使用して既存の VM に NIC を追加することはできません。 既存の VM に NIC を追加するには、CLI または PowerShell を使用する必要があります。

|ツール|コマンド|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-view-nic"></a>VM の NIC の表示

VM に接続されている NIC を表示して、各 NIC の構成の詳細や、各 NIC に割り当てられている IP アドレスを確認できます。 

1. ご利用のサブスクリプションの所有者、共同作成者、またはネットワーク共同作成者いずれかのロールが割り当てられているアカウントで、[Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールの割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*仮想マシン*" と入力します。 検索結果に **[仮想マシン]** が表示されたら、それをクリックします。
3. 表示される **[仮想マシン]** ブレードで、NIC を表示する VM の名前をクリックします。
4. 選択した VM に対して表示される [仮想マシン] ブレードの **[設定]** で、**[ネットワーク インターフェイス]** をクリックします。 NIC の設定とその変更方法については、[NIC の設定とタスク](virtual-network-network-interface.md)に関する記事をご覧ください。 NIC に割り当てられる IP アドレスの追加、変更、削除について詳しくは、[IP アドレスの追加、変更、削除](virtual-network-network-interface-addresses.md)に関する記事をご覧ください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v1.3.4/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a> VM からの NIC の削除

NIC を削除する VM は、停止 (割り当て解除) 状態になっており、2 つ以上の NIC がアタッチされている必要があります。 任意の NIC を削除できますが、削除後の VM に 1 つ以上の NIC がアタッチされている必要があります。 プライマリ NIC を削除した場合は、VM に最も長い時間アタッチされている NIC にプライマリ属性が自動的に割り当てられます。 自分で任意の NIC をプライマリに指定することもできます。 Azure Portal では、VM からの NIC の削除と NIC に対するプライマリ属性の設定は実行できません。この 2 つは CLI または PowerShell を使用して行うことができます。 

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>次のステップ
複数の NIC または IP アドレスが構成された VM を作成する方法については、次の記事をご覧ください。

**コマンド**

|タスク|ツール|
|---|---|
|複数 NIC を持つ VM の作成|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|複数の IP アドレスが割り当てられた 1 つの NIC VM の作成|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

