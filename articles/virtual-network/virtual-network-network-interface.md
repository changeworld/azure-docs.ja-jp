---
title: "Azure ネットワーク インターフェイスの作成、変更、削除 | Microsoft Docs"
description: "ネットワーク インターフェイス (NIC) の概要と、その作成、設定変更、削除の方法について説明します。"
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
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 871d469ada9857af6d6fe70f1dd38385fb18e312
ms.contentlocale: ja-jp
ms.lasthandoff: 06/02/2017


---

# <a name="create-change-or-delete-network-interfaces"></a>ネットワーク インターフェイスの作成、変更、削除

ネットワーク インターフェイスの作成、設定変更、削除の方法について説明します。 NIC は、Azure 仮想マシン (VM) がインターネット、Azure、オンプレミスのリソースと通信するために使用します。 Azure Portal を使用して VM を作成すると、既定の設定で NIC が 1 つ自動的に作成されます。 カスタム設定で NIC を作成することもできます。自分で作成すると、1 つ以上の NIC を VM に追加できます。 また、既存の NIC に対して、既定の NIC 設定を変更することもできます。 この記事では、カスタム設定を使用して NIC を作成する方法、ネットワーク フィルターの割り当て (ネットワーク セキュリティ グループ)、サブネットの割り当て、DNS サーバーの設定、IP 転送などの既存の NIC 設定を変更する方法、および NIC を削除する方法について説明します。

NIC に対して IP アドレスの追加、変更、削除が必要な場合は、[IP アドレスの追加、変更、削除](virtual-network-network-interface-addresses.md)に関する記事をご覧ください。 VM への NIC の追加または VM からの NIC の削除が必要な場合は、[NIC の追加と削除](virtual-network-network-interface-vm.md)に関する記事をご覧ください。


## <a name="before"></a>開始する前に

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- NIC の制限について、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。
- Azure アカウントを使用して、Azure Portal、Azure コマンド ライン インターフェイス (CLI)、または Azure PowerShell にログインします。 まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- PowerShell コマンドを使用して、この記事のタスクを実行する場合、[Azure PowerShell のインストールと設定方法](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順を実行して、Azure PowerShell をインストールして設定します。 最新バージョンの Azure PowerShell コマンドレットがインストールされていることを確認してください。 PowerShell コマンドのヘルプとサンプルを表示するには、「`get-help <command> -full`」と入力します。
- Azure コマンド ライン インターフェイス (CLI) コマンドを使用して、この記事のタスクを実行する場合は、[Azure CLI のインストールと構成の方法](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順を実行して、Azure CLI をインストールして構成します。 最新バージョンの Azure CLI がインストールされていることを確認してください。 CLI コマンドのヘルプを表示するには、「`az <command> --help`」と入力します。

## <a name="create-nic"></a>NIC の作成
Azure Portal を使用して VM を作成すると、既定の設定で NIC が 1 つ自動的に作成されます。 すべての NIC 設定を自分で指定したい場合は、カスタム設定で NIC を作成しておき、VM の作成時にその NIC を VM にアタッチできます。 NIC を作成して既存の VM に追加することもできます。 既存の NIC で VM を作成する方法、NIC を既存の VM に追加する方法、VM から NIC を削除する方法については、[NIC の追加と削除](virtual-network-network-interface-vm.md)に関する記事をご覧ください。 NIC を作成する前に、NIC を作成するのと同じ場所およびサブスクリプションに仮想ネットワーク (VNet) が作成されている必要があります。 VNet を作成する方法については、[VNet の作成](virtual-networks-create-vnet-arm-pportal.md)に関する記事をご覧ください。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. **[ネットワーク インターフェイス]** ブレードが表示されたら、**[+ 追加]** をクリックします。
4. 表示される **[ネットワーク インターフェイスの作成]** ブレードで以下の設定の値を入力するか選択し、**[作成]** をクリックします。

    |設定|必須|詳細|
    |---|---|---|
    |名前|あり|選択したリソース グループ内で一意となる名前を使用してください。 長く使用する中で、Azure サブスクリプションに複数の NIC を作成する可能性があります。 複数の NIC を管理しやすいように、名前付け規則を作成する際は、[名前付け規則](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions)に関する記事に記載された推奨事項をご覧ください。 名前は、NIC の作成後に変更することはできません。|
    |Virtual Network|あり|NIC に接続する VNet を選択します。 NIC を接続できるのは、NIC と同じサブスクリプションと場所に存在する VNet のみです。 NIC が作成された後で、接続先の VNet を変更することはできません。 NIC を追加する VM も、NIC と同じ場所とサブスクリプションに存在する必要があります。|
    |サブネット|あり|選択した VNet 内のサブネットを選択します。 NIC の接続先のサブネットは、作成した後で変更できます。|
    |プライベート IP アドレスの割り当て|あり| 次の割り当て方法から選択します。**動的:** このオプションを選択すると、選択したサブネットのアドレス空間から使用可能なアドレスが自動的に割り当てられます。 VM が停止 (割り当て解除) 状態になった後で再起動された場合は、NIC に異なるアドレスが割り当てられる可能性があります。 停止 (割り当て解除) 状態にならずに VM が再起動された場合、アドレスは変わりません。 **静的:** このオプションを選択した場合は、選択したサブネットのアドレス空間内から使用可能な IP アドレスを手動で割り当てる必要があります。 静的アドレスは、手動で変更するか NIC が削除されるまで変わることはありません。 NIC の作成後に、割り当て方法を変更することができます。 このアドレスは Azure DHCP サーバーによって VM のオペレーティング システム内の NIC に割り当てられます。|
    |ネットワーク セキュリティ グループ|いいえ| **[なし]** に設定されたままにするか、既存のネットワーク セキュリティ グループ (NSG) を選択するか、新しい NSG を作成することができます。 NSG を使用すると、NIC と外部との間のネットワーク トラフィックをフィルター処理できます。 NSG について詳しくは、[ネットワーク セキュリティ グループ](virtual-networks-nsg.md)に関する記事をご覧ください。 NSG を作成する方法については、[NSG の作成](virtual-networks-create-nsg-arm-pportal.md)に関する記事をご覧ください。 NIC には 0 個以上の NSG を適用できます。 0 個または 1 個の NSG は、その NIC の接続先の NIC にも適用できます。 NSG を NIC と接続先のサブネットに適用すると、予期しない結果が発生することがあります。 NIC に適用された NSG をトラブルシューティングする方法については、[NSG のトラブルシューティング](virtual-network-nsg-troubleshoot-portal.md#a-namensgaview-effective-security-rules-for-a-network-security-group-nsg)に関する記事をご覧ください。|
    |サブスクリプション|あり|ご利用の Azure [サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)を 1 つ選択します。 NIC のアタッチ先の VM と接続先の Vnet は、同じサブスクリプションに存在していることが必要です。|
    |リソース グループ|あり|既存の[リソース グループ](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)を選択するか、新しく作成することができます。 NIC は、アタッチ先の VM や接続先の Vnet と、同じリソース グループに存在することも、違うリソース グループに存在することも可能です。|
    |場所|あり|NIC のアタッチ先の VM と接続先の Vnet は、同じ[場所](https://azure.microsoft.com/regions)に存在している必要があります。この場所はリージョンとも呼ばれます。|

ポータルでは、VM を作成する際に NIC にパブリック IP アドレスを割り当てることはできますが、NIC の作成時にパブリック IP アドレスを NIC に割り当てることはできません。 NIC を作成した後でパブリック IP アドレスを追加する方法については、[IP アドレスの追加、変更、削除](virtual-network-network-interface-addresses.md)に関する記事をご覧ください。 NIC の作成時にパブリック IP アドレスを割り当てるには、CLI または PowerShell を使用して NIC を作成する必要があります。

>[!Note]
> Azure によって MAC アドレスが NIC に割り当てられるのは、NIC が VM にアタッチされ、VM が最初に起動した後です。 Azure が NIC に割り当てる MAC アドレスは指定できません。 MAC アドレスが NIC に割り当てられると、NIC が削除されるか、プライマリ NIC のプライマリ IP 構成に割り当てられたプライベート IP アドレスが変更されるまで、その状態は変わりません。 IP アドレスと IP 構成について詳しくは、[IP アドレスの追加、変更、削除](virtual-network-network-interface-addresses.md)に関する記事をご覧ください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

## <a name="view-nics"></a>NIC 設定の表示

NIC に関するほとんどの設定を表示して変更できます。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. 表示される **[ネットワーク インターフェイス]** ブレードで、設定を表示または変更する NIC をクリックします。
4. 次の設定が、選択した NIC に対して表示されるブレードに表示されます。
    - **概要:** 割り当てられている IP アドレス、NIC の接続先の VNet/サブネット、NIC のアタッチ先の VM (アタッチされている場合) など、NIC に関する情報が表示されます。 次の図は、**mywebserver256** という名前の NIC の概要設定です。  ![ネットワーク インターフェイスの概要](./media/virtual-network-network-interface/nic-overview.png)NIC を別のリソース グループまたはサブスクリプションに移動するには、**[リソース グループ]** または **[サブスクリプション名]** の横にある **[(変更)]** をクリックします。 NIC を移動する場合は、NIC に関連付けられているリソースもすべて移動する必要があります。 たとえば、NIC が VM にアタッチされている場合は、VM と VM に関連する他のリソースも移動する必要があります。 NIC を移動するには、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal)」の記事をご覧ください。 この記事には、リソースを移動するための前提条件や、Azure Portal、PowerShell、および Azure CLI を使用して移動する方法が記載されています。
    - **IP 構成:** NIC の 1 つ以上の IP 構成にはパブリック IP アドレスとプライベート IP アドレスが割り当てられています。 NIC でサポートされる IP 構成の最大数について詳しくは、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。 各 IP 構成には、1 つのプライベート IP アドレスが割り当てられます。また、1 つのパブリック IP アドレスを関連付けることができます。 NIC に対して IP 構成の追加、変更、削除を行うには、[IP アドレスの追加、変更、削除](virtual-network-network-interface-addresses.md)に関する記事の [NIC へのセカンダリ IP 構成の追加](virtual-network-network-interface-addresses.md#create-ip-config)、[IP 構成の変更](virtual-network-network-interface-addresses.md#change-ip-config)、または [IP 構成の削除](virtual-network-network-interface-addresses.md#delete-ip-config)に関するセクションの手順を実行します。 IP 転送とサブネットの割り当ても、このセクションで構成します。 これらの設定について詳しくは、この記事のセクション「[IP 転送の有効化と無効化](#ip-forwarding)」と「[サブネットの割り当ての変更](#subnet)」をご覧ください。
    - **DNS サーバー:** Azure DHCP サーバーが NIC に割り当てる DNS サーバーを指定できます。 この設定は、NIC が接続されている VNet から継承するか、カスタム設定を使用して VNet の設定を上書きすることができます。 表示される内容を変更するには、この記事のセクション「[DNS サーバーの変更](#dns)」の手順を実行します。
    - **ネットワーク セキュリティ グループ (NSG):** NIC に関連付けられている NSG (ある場合) が表示されます。 NSG には、NIC のネットワーク トラフィックをフィルター処理するための送受信の規則が含まれています。 NSG が NIC に関連付けられている場合は、関連付けられている NSG の名前が表示されます。 表示される内容を変更するには、この記事の「[NSG とネットワーク インターフェイスの関連付けまたは関連付け解除](#associate-nsg)」セクションの手順を実行します。
    - **プロパティ:** NIC の MAC アドレスや NIC が存在するサブスクリプションなど、NIC に関する重要な設定が表示されます (NIC が VM に接続されていない場合は空白です)。
    - **有効なセキュリティ規則:** セキュリティ規則が一覧表示されるのは、NIC が実行中の VM にアタッチされ、NSG が NIC または NIC の接続先サブネット (あるいは両方) に関連付けられている場合です。 表示される内容について詳しくは、[ネットワーク セキュリティ グループのトラブルシューティング](virtual-network-nsg-troubleshoot-portal.md#a-namensgaview-effective-security-rules-for-a-network-security-group-nsg)に関する記事をご覧ください。 NSG について詳しくは、[ネットワーク セキュリティ グループ](virtual-networks-nsg.md)に関する記事をご覧ください。
    - **有効なルート:** ルートが一覧表示されるのは、NIC が実行中の VM にアタッチされている場合です。 ルートは、Azure 既定ルート、ユーザー定義ルート (UDR)、BGP ルート (NIC の接続先サブネットに存在する場合) が組み合わされたものです。 表示される内容について詳しくは、[ルートのトラブルシューティング](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface)に関する記事をご覧ください。 Azure 既定ルートと UDR について詳しくは、[ユーザー定義ルート](virtual-networks-udr-overview.md)に関する記事をご覧ください。
    - **Azure Resource Manager の一般的な設定:** Azure Resource Manager の一般的な設定については、[アクティビティ ログ](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)、[Access Control](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)、[タグ](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)[ロック](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、および [Automation スクリプト](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group) に関する各記事をご覧ください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) を使用して、サブスクリプションの NIC を表示します。[az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) を使用して、NIC の設定を表示します。|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) を使用して、サブスクリプションの NIC または NIC の設定を表示します。|

## <a name="dns"></a>DNS サーバーの変更

DNS サーバーは、Azure DHCP サーバーによって VM オペレーティング システム内の NIC に割り当てられます。 割り当てられる DNS サーバーは、NIC の DNS サーバー設定に基づきます。 NIC の名前解決について詳しくは、[VM の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md)に関する記事をご覧ください。 この設定は、VNet から継承するか、独自の設定を使用して VNet の設定を上書きすることができます。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. 表示される **[ネットワーク インターフェイス]** ブレードで、設定を表示または変更する NIC をクリックします。
4. 選択した NIC のブレードの **[設定]** にある **[DNS サーバー]** をクリックします。
5. 次のいずれかをクリックします。
    - **[仮想ネットワークから継承する] \(既定)**: このオプションを選択すると、NIC の接続先仮想ネットワークで定義された DNS サーバー設定を継承します。 VNet レベルでは、カスタム DNS サーバーまたは Azure 提供の DNS サーバーが定義されます。 Azure 提供の DNS サーバーは、同じ VNet に接続しているリソースの名前を解決できます。 異なる VNet に接続しているリソースの名前解決には FQDN を使用する必要があります。
    - **[カスタム]**: 独自の DNS サーバーを構成して、複数の VNet で名前を解決できます。 DNS サーバーとして使用するサーバーの IP アドレスを入力します。 指定する DNS サーバー アドレスはこの NIC のみに割り当てられ、NIC が接続されている Vnet のすべての DNS 設定を上書きします。
6. [ **Save**] をクリックします。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-forwarding"></a>IP 転送の有効化と無効化

IP 転送を使用すると、NIC がアタッチされている VM で次が可能になります。
- NIC に割り当てられた IP 構成のいずれかに割り当てられた IP アドレスの 1 つが宛先として指定されていなくても、ネットワーク トラフィックを受信できます。
- NIC の IP 構成のいずれかに割り当てられた IP アドレスとは異なる送信元 IP アドレスを使用してネットワーク トラフィックを送信できます。

トラフィックを受け取る VM と転送を行う必要がある VM にアタッチされているすべての NIC で、設定を有効にする必要があります。 VM は、アタッチされている NIC が 1 つでも複数でも、トラフィックを転送できます。 IP 転送は Azure の設定ですが、ファイアウォール、WAN 最適化、負荷分散アプリケーションなど、トラフィックを転送できるアプリケーションを VM が実行する必要があります。 ネットワーク アプリケーションを実行する VM は、しばしばネットワーク仮想アプライアンス (NVA) と呼ばれます。 すぐにデプロイできる NVA の一覧については、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) をご覧ください。 通常、IP 転送はユーザー定義ルートで使用されます。 ユーザー定義ルートについて詳しくは、[ユーザー定義のルート](virtual-networks-udr-overview.md)に関する記事をご覧ください。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. 表示される **[ネットワーク インターフェイス]** ブレードで、IP 転送を有効または無効にする NIC をクリックします。
4. 選択した NIC のブレードの **[設定]** セクションで、**[IP 構成]** をクリックします。
5. **[有効]** または **[無効]** (既定の設定) をクリックして、設定を変更します。
6. [ **Save**] をクリックします。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="subnet"></a>サブネットの割り当ての変更

NIC の接続先のサブネットは変更できますが、Vnet は変更できません。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. 表示される **[ネットワーク インターフェイス]** ブレードで、設定を表示または変更する NIC をクリックします。
4. 選択した NIC のブレードの **[設定]** で、**[IP 構成]** をクリックします。 一覧表示された IP 構成のプライベート IP アドレスの中に、横に **[(静的)]** と表示されているものがある場合は、次の手順を実行して、IP アドレスの割り当て方法を "動的" に変更する必要があります。 NIC のサブネット割り当てを変更するには、すべてのプライベート IP アドレスを動的な方法で割り当てる必要があります。 アドレスが動的な方法で割り当てられている場合は、手順 5. に進みます。 静的な割り当て方法で割り当てられているアドレスがある場合は、次の手順を実行して、割り当て方法を "動的" に変更します。
    - IP 構成の一覧で、IP アドレスの割り当て方法を変更する IP 構成をクリックします。
    - IP 構成に対して表示されるブレードで、**[割り当て]** 方法で **[動的]** をクリックします。
    - **[Save]**をクリックします。
5. NIC を接続するサブネットを、**[サブネット]** ドロップダウン リストで選択します。
6. [ **Save**] をクリックします。 新しい動的アドレスが、新しいサブネットのサブネット アドレス範囲から割り当てられます。 NIC を新しいサブネットに割り当てた後で、必要に応じて、新しいサブネット アドレス範囲から静的 IP アドレスを割り当てることもできます。 NIC に対する IP アドレスの追加、変更、削除について詳しくは、[IP アドレスの追加、変更、削除](virtual-network-network-interface-addresses.md)に関する記事をご覧ください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-nic"></a>NIC の削除

NIC を削除できるのは VM にアタッチされていない場合です。 VM にアタッチされている場合は、NIC を削除する前に、VM を停止 (割り当て解除) 状態にしてから、VM から NIC をデタッチする必要があります。 NIC を VM からデタッチするには、[ネットワーク インターフェイスの追加と削除](virtual-network-network-interface-vm.md)に関する記事の[仮想マシンからの NIC のデタッチ](virtual-network-network-interface-vm.md#vm-remove-nic)に関するセクションの手順を実行します。 VM を削除すると、VM にアタッチされたすべての NIC がデタッチされますが、NIC は削除されません。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事をご覧ください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*ネットワーク インターフェイス*" と入力します。 検索結果に **[ネットワーク インターフェイス]** が表示されたら、それをクリックします。
3. 削除する NIC を右クリックし、**[削除]** をクリックします。
4. **[はい]** をクリックして、NIC の削除を確認します。

NIC を削除すると、割り当てられていた MAC アドレスまたは IP アドレスは解放されます。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.1.0/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>次のステップ
複数の NIC または IP アドレスが構成された VM を作成する方法については、次の記事をご覧ください。

**コマンド**

|タスク|ツール|
|---|---|
|複数 NIC を持つ VM の作成|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|複数の IP アドレスが割り当てられた 1 つの NIC VM の作成|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

