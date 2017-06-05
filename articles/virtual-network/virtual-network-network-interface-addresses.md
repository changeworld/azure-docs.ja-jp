---
title: "Azure ネットワーク インターフェイスの IP アドレスの構成 | Microsoft Docs"
description: "NIC にパブリック IP アドレスとプライベート IP アドレスを追加し、そのアドレスを変更および削除する方法について説明します。"
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
ms.openlocfilehash: cf7dbc648136897772bbd068687313eec16bed75
ms.contentlocale: ja-jp
ms.lasthandoff: 05/13/2017


---

# <a name="add-change-or-remove-ip-addresses-for-azure-network-interfaces"></a>Azure ネットワーク インターフェイスの IP アドレスの追加、変更、削除

ネットワーク インターフェイス (NIC) にパブリック IP アドレスとプライベート IP アドレスを追加し、そのアドレスを変更および削除する方法について説明します。 NIC に割り当てられているプライベート IP アドレスは、VM がインターネットへの通信や Azure Virtual Network (VNet) に接続されている他のリソースとの通信に使用します。 NIC に割り当てられているパブリック IP アドレスは、インターネットから VM への受信方向の通信に使用されます。 

NIC の作成、変更、または削除が必要な場合は、[NIC の設定とタスク](virtual-network-network-interface.md)に関する記事を参照してください。 仮想マシンへの NIC の追加または仮想マシンからの NIC の削除が必要な場合は、[NIC の追加と削除](virtual-network-network-interface-vm.md)に関する記事を参照してください。 


## <a name="before"></a>開始する前に

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- [Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事で、パブリック IP アドレスおよびプライベート IP アドレスの制限について確認します。
- Azure アカウントを使用して、Azure Portal、Azure コマンド ライン インターフェイス (CLI)、または Azure PowerShell にログインします。 まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順を実行して、Azure PowerShell をインストールして構成します。 最新バージョンの Azure PowerShell コマンドレットがインストールされていることを確認してください。 PowerShell コマンドのヘルプとサンプルを表示するには、「`get-help <command> -full`」と入力します。
- Azure コマンド ライン インターフェイス (CLI) コマンドを使用して、この記事のタスクを実行する場合は、[Azure CLI のインストールと構成の方法](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順を実行して、Azure CLI をインストールして構成します。 最新バージョンの Azure CLI がインストールされていることを確認してください。 CLI コマンドのヘルプを表示するには、「`az <command> --help`」と入力します。

## <a name="about"></a>NIC と IP アドレスの概要

[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)の範囲内であれば、各 NIC に複数のプライベート IP アドレスおよびパブリック IP アドレスを割り当てることができます。 NIC に複数の IP アドレスを割り当てると、次のようなシナリオで役立ちます。
- 異なる IP アドレスと SSL 証明書を持つ複数のウェブサイトやサービスを、1つのサーバーでホストする。
- VM がファイアウォールやロード バランサーのような、ネットワーク仮想アプライアンスとして機能する。
- 複数の NIC いずれかの複数のプライベート IP アドレスいずれかを Azure Load Balancer のバックエンド プールに追加する。 以前は、プライマリ NIC のプライマリ IP アドレスのみをバックエンド プールに追加できました。 複数の IP 構成の負荷分散方法の詳細については、[複数の IP 構成の負荷分散](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

複数の IP アドレスが 1 つの IP 構成に割り当てられます。 NIC には必ず**プライマリ** IP 構成が割り当てられますが、複数の**セカンダリ**構成が割り当てられる場合もあります。 各 IP 構成には、次の種類のアドレスのいずれかまたは両方が割り当てられます。
- **プライベート:** プライベート IP アドレスは、VM が NIC の属している VNet に接続された他のリソースと通信するために使用します。 IP 構成には、プライベート IP アドレスが 1 つ割り当てられている必要があります。 Azure DHCP サーバーは、NIC のプライマリ IP 構成のプライベート IP アドレスを、VM オペレーティング システム内の NIC に割り当てます。 プライベート IP アドレスは、VM がインターネットへの送信接続を行う際にも使用されます。 送信接続には、送信元ネットワーク アドレス変換 (SNAT) が適用されます。 Azure からインターネットへの送信接続の詳細については、[Azure からインターネットへの送信接続](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。 インターネットから VM のプライベート IP アドレスへの受信通信はできません。
- **パブリック:** パブリック IP アドレスは、インターネットから VM への受信接続に使用されます。 インターネットへの送信接続に SNAT は適用されません。 IP 構成にパブリック IP アドレスを割り当てることができますが、必須ではありません。 パブリック IP アドレスの詳細については、[パブリック IP アドレス](virtual-network-public-ip-address.md)に関する記事を参照してください。

NIC に割り当てることができるパブリック IP アドレスおよびプライベート IP アドレスの数には制限があります。 詳しくは、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。

パブリック IP アドレスとプライベート IP アドレスの割り当てには、次の方法を使用できます。
- **動的:** プライベート IP アドレスとパブリック IP アドレスは、既定で動的に割り当てられます。 動的アドレスは、VM が停止 (割り当て解除) 状態になり、再起動された場合、変更される可能性があります。 VM の有効期間中に IP アドレスを変更したくない場合は、静的アドレスを使用します。
- **静的:** 静的アドレスは、NIC が削除されるまで変更されません。 静的プライベート IP アドレスは、NIC が接続されているサブネットのアドレス空間から割り当てます。 Azure での静的パブリック IP アドレスの割り当ての詳細については、[パブリック IP アドレス](virtual-network-public-ip-address.md)に関する記事を参照してください。

## <a name="create-ip-config"></a>IP アドレスの追加

[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事に示されている制限内であれば、必要に応じて任意の数の IP アドレスを NIC に追加できます。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. 表示される **[ネットワーク インターフェイス]** ブレードで、IP アドレスを追加する NIC をクリックします。
4. 選択した NIC のブレードの **[設定]** セクションで、**[IP 構成]** をクリックします。
5. IP 構成に対して開くブレードで、**[+ 追加]** をクリックします。
6. 以下の項目を指定してから **[OK]** をクリックして、**[IP 構成の追加]** ブレードを閉じます。

    |設定|必須|詳細|
    |---|---|---|
    |名前|あり|NIC に対して一意である必要があります。|
    |型|あり|IP 構成を既存の NIC に追加しており、各 NIC には必ずプライマリ IP 構成があるため、選択できるオプションは **[セカンダリ]** のみです。|
    |プライベート IP アドレスの割り当て方法|あり|**[動的]** アドレスは、VM が停止 (割り当て解除) 状態になってから再起動した場合に変化することがあります。 NIC が接続されているサブネットのアドレス空間から使用可能なアドレスが自動的に割り当てられます。 **[静的]** アドレスは、NIC が削除されるまで解放されません。 別の IP 構成で現在使用されていないサブネット アドレス空間の範囲から IP アドレスを指定します。|
    |パブリック IP アドレス|なし|**[無効]:** パブリック IP アドレス リソースは現在 IP 構成に関連付けられていません。 **[有効]:** 既存のパブリック IP アドレスを選択するか、新しいパブリック IP アドレスを作成します。 パブリック IP アドレスの作成方法については、「[Public IP addresses](virtual-network-public-ip-address.md#create)」(パブリック IP アドレス) の記事をご覧ください。|
7. セカンダリ プライベート IP アドレスを VM オペレーティング システムに手動で追加します。これは、[仮想マシンへの複数 IP アドレスの割り当て](virtual-network-multiple-ip-addresses-portal.md#os-config)に関する記事の手順を実行して行います。 VM オペレーティング システムにパブリック IP アドレスは追加しないでください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-config"></a>IP アドレス設定の変更

IP アドレスの割り当て方法の変更、静的 IP アドレスの変更、または NIC に割り当てられたパブリック IP アドレスの変更が必要になることがあります。 VM 内のセカンダリ NIC (詳しくは[プライマリ NIC とセカンダリ NIC](virtual-network-network-interface-vm.md#about)に関する記事を参照) に関連付けられているセカンダリ IP 構成のプライベート IP アドレスを変更する場合は、VM を停止 (割り当て解除) 状態にしてから、次の手順を実行します。 

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. 表示される **[ネットワーク インターフェイス]** ブレードで、IP アドレス設定を表示または変更する NIC をクリックします。
4. 選択した NIC のブレードの **[設定]** セクションで、**[IP 構成]** をクリックします。
5. IP 構成に対して開いたブレードで、変更する IP 構成を一覧からクリックします。
6. この記事のセクション「[IP 構成の追加](#create-ip-config)」の手順 6. に記載された設定に関する情報を使用して、必要に応じて設定を変更します。 IP 構成を変更したら、**[保存]** をクリックしてブレードを閉じます。

>[!NOTE]
>プライマリ NIC に複数の IP 構成があるときに、プライマリ IP 構成のプライベート IP アドレスを変更すると、Windows ですべてのセカンダリ IP アドレスを NIC に手動で再割り当てする必要があります (Linux では必要ありません)。 オペレーティング システム内で IP アドレスを NIC に手動で割り当てるには、[仮想マシンへの複数 IP アドレスの割り当て](virtual-network-multiple-ip-addresses-portal.md#os-config)に関する記事をご覧ください。 VM オペレーティング システムにパブリック IP アドレスは追加しないでください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-ip-config"></a>IP アドレスの削除

NIC からプライベート IP アドレスとパブリック IP アドレスを削除することはできますが、NIC には少なくとも 1 つのプライベート IP アドレスが常に割り当てられている必要があります。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. 表示される **[ネットワーク インターフェイス]** ブレードで、IP アドレスを削除する NIC をクリックします。
4. 選択した NIC のブレードの **[設定]** セクションで、**[IP 構成]** をクリックします。
5. 削除するセカンダリ IP 構成を右クリックし (プライマリ構成を削除することはできません)、**[削除]**、**[はい]** の順にクリックして、削除を確定します。 構成にパブリック IP アドレスが関連付けられていた場合、リソースの関連付けが IP 構成から解除されますが、リソースは削除されません。
6. **[IP 構成]** ブレードを閉じます。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>次のステップ
複数の NIC または IP アドレスが構成された VM を作成する方法については、次の記事をご覧ください。

**コマンド**

|タスク|ツール|
|---|---|
|複数 NIC を持つ VM の作成|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|複数の IP アドレスが割り当てられた 1 つの NIC VM の作成|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

