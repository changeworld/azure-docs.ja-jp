---
title: "Azure 仮想ネットワーク サブネットの作成、変更、削除 | Microsoft Docs"
description: "仮想ネットワーク サブネットの作成、変更、削除の方法について説明します。"
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
ms.date: 05/10/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 38dcdf2f313c236a507e6a418c39812da16c6345
ms.contentlocale: ja-jp
ms.lasthandoff: 05/16/2017


---
# <a name="create-change-or-delete-virtual-network-subnets"></a>仮想ネットワーク サブネットの作成、変更、削除

仮想ネットワーク (VNet) のサブネットの作成、変更、削除の方法について説明します。 VNet にまだ慣れていない場合は、サブネットの作成、変更、削除を行う前に、[Virtual Network の概要](virtual-networks-overview.md)に関する記事と[仮想ネットワークの作成、変更、削除](virtual-network-manage-network.md)に関する記事を読むことをお勧めします。 VNet に接続可能な Azure のリソースは、VNet 内のサブネットに接続されます。 サブネットは通常、次のような目的で VNet 内に複数作成されます。
- **サブネット間のトラフィックのフィルター処理:**ネットワーク セキュリティ グループ (NSG) をサブネットに適用することで、VNet に接続されているすべてのリソース (仮想マシンなど) の受信および送信ネットワーク トラフィックをフィルター処理できます。 NSG を作成する方法については、[ネットワーク セキュリティ グループの作成](virtual-networks-create-nsg-arm-pportal.md)に関する記事を参照してください。
- **サブネット間のルーティングの制御:** Azure では、サブネット間のトラフィックが自動的にルーティングされるように既定のルートが作成されています。 ユーザー定義ルート (UDR) を作成して、Azure 既定のルートを上書きできます。 UDR の詳細については、[ユーザー定義ルートの作成](virtual-network-create-udr-arm-ps.md)に関する記事を参照してください。 

この記事では、Azure Resource Manager デプロイメント モデルで VNet のサブネットを作成する方法と、そのサブネットを変更および削除する方法について説明します。
 
## <a name="before"></a>開始する前に

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- Azure の VNet とサブネットを初めて使用する場合は、この記事を読む前に、[初めての Azure Virtual Network の作成](virtual-network-get-started-vnet-subnet.md)に関する記事で演習を行うことをお勧めします。 この演習は VNet とサブネットを理解するのに役立ちます。
- サブネットと VNet の制限について、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事を参照してください。
- Azure アカウントを使用して、Azure Portal、Azure コマンド ライン インターフェイス (CLI)、または Azure PowerShell にログインします。 まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- Azure PowerShell のコマンドを使用してこの記事のタスクを行う場合は、最初に [Azure PowerShell をインストールして構成する](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)必要があります。 最新バージョンの Azure PowerShell コマンドレットがインストールされていることを確認してください。 PowerShell コマンドのヘルプとサンプルを表示するには、「`get-help <command> -full`」と入力します。
- Azure コマンド ライン インターフェイス (CLI) のコマンドを使用してこの記事のタスクを行う場合は、最初に [Azure CLI をインストールして構成する](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)必要があります。 最新バージョンの Azure CLI がインストールされていることを確認してください。 CLI コマンドのヘルプを表示するには、「`az <command> --help`」と入力します。


## <a name="create-subnet"></a>サブネットの作成

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで[ポータル](https://portal.azure.com)にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事を参照してください。
2. Azure Portal の上部にある "*リソースの検索*" というテキストが表示されたボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それをクリックします。
3. 表示される **[仮想ネットワーク]** ブレードで、サブネットを変更する仮想ネットワークをクリックします。
4. 選択した仮想ネットワークに対して表示されたウィンドウで、**[サブネット]** クリックします。
5. **[+ サブネット]**をクリックします。
6. **[サブネットの追加]** ブレードで、次のパラメーターの値を入力します。
    - **名前:** 仮想ネットワーク内で一意となる名前を指定する必要があります。
    - **アドレス範囲:** 範囲は VNet のアドレス空間内で一意である必要があります。VNet 内の他のサブネット アドレス範囲と重複することはできません。 アドレス空間は、CIDR 表記で指定する必要があります。 たとえば、アドレス空間が 10.0.0.0/16 の VNet では、10.0.0.0/24 のサブネット アドレス空間を定義できます。 指定できる最小範囲は、/29 です。これでサブネットに 8 つの IP アドレスを使用できます。 Azure では、サブネットごとに、最初と最後のアドレスがプロトコルに準拠するために予約されています。 そのほか、3 つのアドレスが Azure サービスの使用のために予約されています。 そのため、/29 のアドレス範囲が設定されたサブネットでは、使用できる IP アドレスが 3 つになります。 VNet を VPN ゲートウェイに接続する場合は、ゲートウェイ サブネットを作成する必要があります。 詳細については、[ゲートウェイ サブネットに指定するアドレス範囲の考慮事項](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet)に関する記事を参照してください。 特定の条件下でのみ、サブネットの作成後にアドレス範囲を変更できます。 サブネット アドレス範囲を変更する方法については、この記事のセクション「[サブネットの変更](#change-subnet)」を参照してください。
    - **ネットワーク セキュリティ グループ (NSG):** 必要に応じて、既存の NSG をサブネットに関連付けることで、サブネットの受信および送信ネットワーク トラフィックを制御できます。 NSG は VNet と同じサブスクリプションおよび場所に存在し、Resource Manager デプロイメント モデルで作成されている必要があります。 NSG を作成する方法については、[ネットワーク セキュリティ グループ](virtual-networks-create-nsg-arm-pportal.md)に関する記事を参照してください。
    - **ルート テーブル:** 必要に応じて、既存のルート テーブルをサブネットに関連付けることで、他のネットワークへのネットワーク トラフィック ルーティングを制御できます。 ルート テーブルは VNet と同じサブスクリプションおよび場所に存在し、Resource Manager デプロイメント モデルで作成されている必要があります。 ルート テーブルを作成する方法については、[ユーザー定義ルート](virtual-network-create-udr-arm-ps.md)に関する記事を参照してください。
    - **ユーザー**: 組み込みのロールまたは独自のカスタム ロールを使用して、サブネットへのアクセス権を制御できます。 サブネットにアクセスするロールおよびユーザーの割り当ての詳細については、[ロールの割り当てを使用して Azure リソースへのアクセス権を管理する](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access)方法に関する記事を参照してください。
7. **[OK]** をクリックすると、選択した VNet にサブネットが追加されます。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network vnet subnet create](/cli/azure/network/vnet/subnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>サブネット設定の変更

リソースがサブネットに接続されているときに、そのサブネットの NSG、ルート テーブル、およびユーザー アクセス権を変更できます。 これらの設定の詳細については、この記事のセクション「[サブネットの作成](#create-subnet)」の手順 6. を参照してください。 サブネットに接続されているリソースがあるときに、サブネットのアドレス空間を変更することはできません。 サブネットに接続されているリソースがある場合は、アドレス範囲を変更する前に、そのリソースを削除する必要があります。 リソースを削除する方法は、リソースによって異なります。 サブネットに接続されているリソースを削除する方法については、削除するリソースの種類に応じたドキュメントを参照してください。 サブネットのアドレス範囲を変更するには、次の手順を実行します。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで[ポータル](https://portal.azure.com)にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事を参照してください。
2. ポータルの上部にある "*リソースの検索*" というテキストが表示されたボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それをクリックします。
3. 表示される **[仮想ネットワーク]** ブレードで、サブネットのアドレス範囲を変更する VNet をクリックします。
4. アドレス範囲を変更するサブネットをクリックします。
5. 選択したサブネットに対して表示されるブレードで、**[アドレス範囲]** ボックスに新しいアドレス範囲を入力します。 この範囲は VNet のアドレス空間内で一意である必要があります。VNet 内の他のサブネット アドレス範囲と重複することはできません。 アドレス空間は、CIDR 表記で指定する必要があります。 たとえば、アドレス空間が 10.0.0.0/16 の VNet では、10.0.0.0/24 のサブネット アドレス空間を定義できます。 指定できる最小範囲は、/29 です。これでサブネットに 8 つの IP アドレスを使用できます。 Azure では、サブネットごとに、最初と最後のアドレスがプロトコルに準拠するために予約されています。 そのほか、3 つのアドレスが Azure サービスの使用のために予約されています。 そのため、/29 のアドレス範囲が設定されたサブネットで使用できる IP アドレスは 3 つです。 VNet を VPN ゲートウェイに接続する場合は、ゲートウェイ サブネットを作成する必要があります。 詳細については、[ゲートウェイ サブネットに指定するアドレス範囲の考慮事項](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet)に関する記事を参照してください。 特定の条件下でのみ、サブネットの作成後にアドレス範囲を変更できます。 サブネット アドレス範囲を変更する方法については、この記事のセクション「[サブネットの変更](#change-subnet)」を参照してください。
6. [ **Save**] をクリックします。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network vnet subnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-subnet"></a>サブネットの削除

接続されているリソースがない場合にのみ、サブネットを削除できます。 サブネットに接続されているリソースがある場合は、先に、そのリソースを削除する必要があります。 リソースを削除する方法は、リソースによって異なります。 サブネットに接続されているリソースを削除する方法については、削除するリソースの種類に応じたドキュメントを参照してください。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで[ポータル](https://portal.azure.com)にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事を参照してください。
2. Azure Portal の上部にある "*リソースの検索*" というテキストが表示されたボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それをクリックします。
3. 表示される **[仮想ネットワーク]** ブレードで、サブネットを削除する VNet をクリックします。
4. 選択した VNet に対して表示されるブレードの **[設定]** で、**[サブネット]** をクリックします。
5. サブネット ブレードに表示されたサブネットの一覧で、削除するサブネットを右クリックし、**[削除]** をクリックします。**[はい]** をクリックすると、サブネットが削除されます。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>次のステップ

VM を作成してサブネットに接続する方法については、[VNet の作成と VM の接続](virtual-network-get-started-vnet-subnet.md#a-namecreate-vmsacreate-virtual-machines)に関する記事を参照してください。
