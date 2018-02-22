---
title: "Azure 仮想ネットワーク サブネットの追加、変更、削除 | Microsoft Docs"
description: "Azure で仮想ネットワーク サブネットを追加、変更、削除する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: f8b60a27e760ae74c7f068844fad1ae0d4324366
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2018
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>仮想ネットワーク サブネットの追加、変更、削除

仮想ネットワーク サブネットを追加、変更、削除する方法について説明します。 仮想ネットワークを使い慣れていない場合は、サブネットを追加、変更、または削除する前に、[Azure Virtual Network の概要](virtual-networks-overview.md)に関するページと「[仮想ネットワークの作成、変更、削除](virtual-network-manage-network.md)」をお読みになることをお勧めします。 仮想ネットワークにデプロイされているすべての Azure リソースは、仮想ネットワーク内のサブネットにデプロイされます。
 
## <a name="before-you-begin"></a>開始する前に

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- ポータルを使用する場合は、https://portal.azure.com を開き、Azure アカウントでログインします。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/powershell) でコマンドを実行するか、お使いのコンピューターから PowerShell を実行してください。 Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 このチュートリアルには、Azure PowerShell モジュール バージョン 5.2.0 以降が必要です。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzureRmAccount` を実行して Azure との接続を作成することも必要です。
- Azure コマンド ライン インターフェイス (CLI) コマンドを使用してこの記事のタスクを実行する場合は、[Azure Cloud Shell](https://shell.azure.com/bash) でコマンドを実行するか、お使いのコンピューターから CLI を実行してください。 このチュートリアルには、Azure CLI バージョン 2.0.26 以降が必要です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 Azure CLI をローカルで実行している場合、`az login` を実行して Azure との接続を作成することも必要です。

## <a name="add-a-subnet"></a>サブネットの追加

1. ポータルの上部にある検索ボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。
2. 仮想ネットワークの一覧から、サブネットを追加する仮想ネットワークを選択します。
3. **[設定]** で、**[サブネット]** を選択します。
4. **[+サブネット]** を選択します。
5. 次のパラメーターの値を入力します。
    - **[名前]**: 名前は仮想ネットワーク内で一意である必要があります。
    - **[アドレス範囲]**: 範囲は仮想ネットワークのアドレス空間内で一意である必要があります。 仮想ネットワーク内の他のサブネット アドレス範囲と重複することはできません。 アドレス空間は、Classless Inter-Domain Routing (CIDR) 表記で指定する必要があります。 たとえば、アドレス空間が 10.0.0.0/16 の仮想ネットワークでは、10.0.0.0/24 のサブネット アドレス空間を定義できます。 指定できる最小範囲は、/29 です。これでサブネットに 8 つの IP アドレスを使用できます。 Azure では、サブネットごとに、最初と最後のアドレスがプロトコルに準拠するために予約されています。 そのほか、3 つのアドレスが Azure サービスの使用のために予約されています。 そのため、/29 のアドレス範囲が設定されたサブネットでは、使用できる IP アドレスが 3 つになります。 仮想ネットワークを VPN ゲートウェイに接続する場合は、ゲートウェイ サブネットを作成する必要があります。 詳細については、[ゲートウェイ サブネットに指定するアドレス範囲の考慮事項](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)に関する記事を参照してください。 特定の条件下でのみ、サブネットの追加後にアドレス範囲を変更できます。 サブネット アドレス範囲を変更する方法については、「[サブネット設定の変更](#change-subnet-settings)」をご覧ください。
    - **[ネットワーク セキュリティ グループ]**: 0 個か 1 個の既存のネットワーク セキュリティ グループをサブネットに関連付けることで、そのサブネットの受信および送信ネットワーク トラフィックをフィルター処理できます。 ネットワーク セキュリティ グループは、仮想ネットワークと同じサブスクリプションと場所に存在する必要があります。 [ネットワーク セキュリティ グループ](security-overview.md)と[ネットワーク セキュリティ グループを作成する方法](virtual-networks-create-nsg-arm-pportal.md)に関するページを参照してください。
    - **[ルート テーブル]:** 0 個または 1 個の既存のルート テーブルをサブネットに関連付けることで、他のネットワークへのネットワーク トラフィック ルーティングを制御できます。 ルート テーブルは、仮想ネットワークと同じサブスクリプションと場所に存在する必要があります。 [Azure のルーティング](virtual-networks-udr-overview.md)と[ルート テーブルを作成する方法](create-user-defined-route-portal.md)に関するページを参照してください。
    - **[サービス エンドポイント]:** サブネットは、そのサブネットに対して 0 個または複数のサービス エンドポイントを有効にできます。 サービスのサービス エンドポイントを有効にするには、サービス エンドポイントを有効にするサービス (複数可) を **[サービス]** 一覧から選択します。 サービス エンドポイントを削除するには、サービス エンドポイントを削除するサービスを選択解除します。 サービス エンドポイントの詳細については、[仮想ネットワーク サービス エンドポイントの概要](virtual-network-service-endpoints-overview.md)に関するページを参照してください。 サービスのサービス エンドポイントを有効にしたら、そのサービスで作成されたリソースのサブネットへのネットワーク アクセスも有効にする必要があります。 たとえば、*Microsoft.Storage* のサービス エンドポイントを有効にする場合は、ネットワーク アクセスを許可するすべての Azure ストレージ アカウントへのネットワーク アクセスも有効にする必要があります。 サービス エンドポイントが有効になっているサブネットへのネットワーク アクセスを有効にする方法の詳細については、サービス エンドポイントを有効にした個々のサービスのドキュメントを参照してください。
6. 選択した仮想ネットワークにサブネットを追加するには、**[OK]** を選択します。

**コマンド**

- Azure CLI: [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)
- PowerShell: [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>サブネット設定の変更

1. ポータルの上部にある検索ボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。
2. 仮想ネットワークの一覧から、設定を変更するサブネットを含む仮想ネットワークを選択します。
3. **[設定]** で、**[サブネット]** を選択します。
4. サブネットの一覧から、設定を変更するサブネットを選択します。 次の設定を変更できます。

    - **[アドレス範囲]:** サブネット内にリソースがデプロイされていない場合は、アドレス範囲を変更できます。 サブネット内にリソースが存在する場合は、まず、そのリソースを別のサブネットに移動するか、サブネットから削除する必要があります。 リソースを移動または削除する手順は、リソースによって異なります。 サブネット内のリソースを移動または削除する方法については、移動または削除するリソースの種類に応じたドキュメントをご覧ください。 「[サブネットの追加](#add-a-subnet)」の手順 5. の **[アドレス範囲]** の制約について確認してください。
    - **[ユーザー]**: 組み込みロールまたは独自のカスタム ロールを使用して、サブネットへのアクセス権を制御できます。 サブネットにアクセスするロールとユーザーの割り当ての詳細については、[ロールの割り当てを使用した Azure リソースへのアクセス権の管理](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access)に関するページをご覧ください。
    - **ネットワーク セキュリティ グループ**、**ルート テーブル**、**ユーザー**、および**サービス エンドポイント**を変更する方法については、「[サブネットの追加](#add-a-subnet)」の手順 5. を参照してください。
5. **[保存]** を選択します。

**コマンド**

- Azure CLI: [az network vnet subnet update](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>サブネットの削除

サブネット内にリソースがない場合にのみ、サブネットを削除できます。 サブネット内にリソースがある場合は、サブネットを削除する前に、そのサブネット内のリソースを削除する必要があります。 リソースを削除する方法は、リソースによって異なります。 サブネット内のリソースを削除する方法については、削除するリソースの種類に応じたドキュメントをご覧ください。

1. ポータルの上部にある検索ボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。
2. 仮想ネットワークの一覧から、削除するサブネットを含む仮想ネットワークを選択します。
3. **[設定]** で、**[サブネット]** を選択します。
4. サブネットの一覧から、削除するサブネットの右側にある **[...]** を選択します。
5. **[削除]** を選択してから、**[はい]** を選択します。

**コマンド**

- Azure CLI: [az network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_vnet_delete)
- PowerShell: [Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>アクセス許可

サブネットでタスクを実行するには、自分のアカウントを[ネットワークの共同作業者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または次の表に表示されている適切なアクセス許可が割り当てられている[カスタム](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ロールに割り当てる必要があります。

|操作                                                                |   操作の名前                               |
|-----------------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   仮想ネットワーク サブネットを取得する                   |
|Microsoft.Network/virtualNetworks/subnets/write                          |   仮想ネットワーク サブネットを作成または更新する      |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   仮想ネットワーク サブネットを削除する                |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   仮想ネットワークに参加する                         |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   サブネットにサービスを参加させる                     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   仮想ネットワーク サブネットの仮想マシンを取得する  |
