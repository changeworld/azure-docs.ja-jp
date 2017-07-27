---
title: "Azure 仮想ネットワーク ピアリングの作成 | Microsoft Docs"
description: "2 つの仮想ネットワークの間に仮想ネットワーク ピアリングを作成する方法について説明します。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2017
ms.author: jdial;narayan;annahar
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 666165115e80a39d02386719b0d7e64d7ae17749
ms.contentlocale: ja-jp
ms.lasthandoff: 06/09/2017


---
# <a name="create-a-virtual-network-peering"></a>仮想ネットワーク ピアリングの作成

このチュートリアルでは、2 つの仮想ネットワークの間に仮想ネットワーク ピアリングを作成する方法について説明します。 2 つの仮想ネットワークをピアリングすると、別々の仮想ネットワークに存在するリソースが、あたかも同じ仮想ネットワーク内に存在するかのような帯域幅と待ち時間で相互に通信を行うことができます。 仮想ネットワーク ピアリングの作成は、同じ Azure リージョンに存在する 2 つの仮想ネットワークの間に限定されます。 仮想ネットワーク ピアリングの詳細については、「[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)」の概要記事を参照してください。 

別々の Azure リージョンに存在する仮想ネットワークを接続する必要がある場合は、Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) を使用してそれらの仮想ネットワークを接続できます。 

仮想ネットワーク ピアリングは、[Azure Portal](#portal)、Azure [PowerShell](#powershell)、Azure [コマンド ライン インターフェイス](#cli) (CLI)、[Azure Resource Manager テンプレート](#template)のいずれかを使って作成できます。 いずれかのリンクをクリックすると、そのツールを使って仮想ネットワーク ピアリングを作成するための手順に直接移動します。

## <a name="portal"></a>ピアリングの作成 - Azure Portal

次の手順では、Resource Manager でデプロイされた同じサブスクリプションの 2 つの仮想ネットワーク間に仮想ネットワーク ピアリングを作成します。 それ以外にも、[別々のサブスクリプションにある 2 つの仮想ネットワークをピアリング](#different-subscriptions)したり、[Resource Manager デプロイメント モデルの仮想ネットワークとクラシック デプロイメント モデルの仮想ネットワークとをピアリング](#different-models)したりすることもできます。 

1. [Azure ポータル](https://portal.azure.com)にサインインします。 サインインに使用するアカウントには、仮想ネットワーク ピアリングを作成するためのアクセス許可が必要です。 詳細については、この記事の「[アクセス許可](#permissions)」セクションを参照してください。
2. **[+ 新規]**、**[ネットワーキング]**、**[仮想ネットワーク]** の順にクリックします。
3. **[仮想ネットワーク]** ブレードで、**[デプロイ モデルの選択]** ボックスの **[Resource Manager]** が選択されていることを確認して、**[作成]** をクリックします。
4. **[仮想ネットワークの作成]** ブレードで、次の設定の値を入力するか選択し、**[作成]** をクリックします。
    - **[名前]**: *myVnet1*
    - **[アドレス空間]**: *10.0.0.0/16*
    - **[サブネット名]**: "*既定値*"
    - **[サブネット アドレス範囲]**: *10.0.0.0/24*
    - **[サブスクリプション]**: サブスクリプションを選択します。
    - **[リソース グループ]**: **[新規作成]** を選択し、「*myResourceGroup*」と入力します
    - **[場所]**: "*米国東部*"
5. 手順 2. ～ 4. を繰り返し、手順 4. で次の値を指定します。
    - **[名前]**: *myVnet2*
    - **[アドレス空間]**: *10.1.0.0/16*
    - **[サブネット名]**: "*既定値*"
    - **[サブネットのアドレス範囲]**: *10.1.0.0/24*
    - **[サブスクリプション]**: サブスクリプションを選択します。
    - **[リソース グループ]**: **[既存のものを使用]** を選択し、*[myResourceGroup]* を選択します。
    - **[場所]**: "*米国東部*"
6. ポータル上部の **[リソースの検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **myResourceGroup** が表示されたら、それをクリックします。 **myresourcegroup** リソース グループのブレードが表示されます。 このリソース グループには、先ほどの手順で作成した 2 つの仮想ネットワークが存在します。
7. **[myVNet1]** をクリックします。
8. 表示された **[myVnet1]** ブレードの左側にある縦長のオプション一覧で **[ピアリング]** をクリックします。
9. 表示された **[myVnet1 - ピアリング]** ブレードで **[+ 追加]** をクリックします。
10. 表示された **[ピアリングの追加]** ブレードで次のオプションを入力または選択し、**[OK]** をクリックします。
     - **[名前]**: *myVnet1ToMyVnet2*
     - **[仮想ネットワークのデプロイ モデル]**:  **[Resource Manager]** を選択します。 
     - **[サブスクリプション]**: サブスクリプションを選択します。
     - **[仮想ネットワーク]**:  **[仮想ネットワークの選択]** をクリックし、**[myVnet2]** をクリックします。
     - **[仮想ネットワーク アクセスを許可する]**: **[有効]** が選択されていることを確認します。
    その他の設定は、このチュートリアルでは使用しません。 すべてのピアリング設定については、[仮想ネットワーク ピアリングの管理](virtual-network-manage-peering.md#create-peering)に関するページを参照してください。
11. 前の手順で **[OK]** をクリックすると、**[ピアリングの追加]** ブレードが閉じて、**[myVnet1 - ピアリング]** ブレードが再度表示されます。 数秒後、作成したピアリングがブレードに表示されます。 作成した **myVnet1ToMyVnet2** ピアリングの **[ピアリング状態]** 列に **[開始済み]** と表示されます。 Vnet1 から Vnet2 へのピアリングを作成したので、今度は Vnet2 から Vnet1 へのピアリングを作成する必要があります。 各仮想ネットワーク内のリソースが相互に通信するためには、ピアリングを双方向で作成する必要があります。
12. myVnet2 について、手順 6. ～ 11. を繰り返します。  ピアリングに *myVnet2ToMyVnet1* という名前を付けます。
13. **[OK]** をクリックして MyVnet2 のピアリングを作成してから数秒経つと、作成したばかりの **myVnet2ToMyVnet1** ピアリングが**接続済み**として **[ピアリング状態]** 列に表示されます。
14. MyVnet1 について、手順 6. ～ 8. を繰り返します。 **myVnet1ToVNet2** ピアリングの **[ピアリング状態]** も**接続済み**になります。 ピアリングにおける両方の仮想ネットワークの **[ピアリング状態]** 列に**接続済み**と表示されれば、ピアリングは正常に確立されています。
15. **(省略可)** このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
16. **(省略可)** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-portal)」セクションの手順を実行してください。

2 つの仮想ネットワークに作成した Azure リソースが、その IP アドレスを使用して相互に通信できるようになりました。 仮想ネットワークに Azure の既定の名前解決を使用する場合、そのネットワーク内のリソースは、通信相手の仮想ネットワークに対して名前を解決することができません。 ピアリングされた仮想ネットワークの間で名前を解決する必要がある場合は、独自の DNS サーバーを作成する必要があります。 その方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)」を参照してください。

## <a name="cli"></a>ピアリングの作成 - Azure CLI

ここで使用するスクリプトの要件は次のとおりです。

- Azure CLI バージョン 2.0.4 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 アップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。
- Bash シェルで動作します。 Azure CLI スクリプトを Windows クライアントで実行する方法については、[Windows での Azure CLI の実行](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

1. リソース グループを 1 つと仮想ネットワークを 2 つ作成します。
    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroup"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network 1.
    az network vnet create \
      --name myVnet1 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Create virtual network 2.
    az network vnet create \
      --name myVnet2 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.1.0.0/16
    #
    ```
2. 2 つの仮想ネットワークの間に仮想ネットワーク ピアリングを作成します。
    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet1 \
      --query id --out tsv)

    # Get the id for VNet2.
    vnet2Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet2 \
      --query id \
      --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group $rgName \
      --vnet-name myVnet1 \
      --remote-vnet-id $vnet2Id \
      --allow-vnet-access

    # Peer VNet2 to VNet1.
    az network vnet peering create \
      --name myVnet2ToMyVnet1 \
      --resource-group $rgName \
      --vnet-name myVnet2 \
      --remote-vnet-id $vnet1Id \
      --allow-vnet-access
    #
    ```
3. スクリプトの実行後、各仮想ネットワークのピアリングを確認します。 次のコマンドをコピーし、コマンド ウィンドウに貼り付けてください。

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    #
    ```
*myVnet1* を *myVnet2* に置き換えて、このコマンドをもう一度実行します。 どちらのコマンドも、出力結果の **PeeringState** 列に **Connected** と表示されます。
4. **(省略可)** このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
5. **(省略可)** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-cli)」の手順を実行してください。

2 つの仮想ネットワークに作成した Azure リソースが、その IP アドレスを使用して相互に通信できるようになりました。 仮想ネットワークに Azure の既定の名前解決を使用する場合、そのネットワーク内のリソースは、通信相手の仮想ネットワークに対して名前を解決することができません。 ピアリングされた仮想ネットワークの間で名前を解決する必要がある場合は、独自の DNS サーバーを作成する必要があります。 その方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)」を参照してください。
 
## <a name="powershell"></a>ピアリングの作成 - PowerShell

1. PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) モジュールの最新バージョンをインストールします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。
2. PowerShell セッションを開始するには、**[開始]** に移動して「**powershell**」と入力し、**[PowerShell]** をクリックします。
3. PowerShell ウィンドウで `login-azurermaccount` コマンドを入力し、Azure にサインインします。 サインインに使用するアカウントには、仮想ネットワーク ピアリングを作成するためのアクセス許可が必要です。 詳細については、この記事の「[アクセス許可](#permissions)」セクションを参照してください。
4. ブラウザーで次のスクリプトをコピーし、PowerShell ウィンドウで右クリックして、スクリプトを実行します。このスクリプトによって、1 つのリソース グループと 2 つの仮想ネットワークが作成されます。
    ```powershell
    # Variables for common values used throughout the script.
    $rgName='myResourceGroup'
    $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network 1.
    $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location

    # Create virtual network 2.
    $vnet2 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet2' `
      -AddressPrefix '10.1.0.0/16' `
      -Location $location
    #
    ```
5. ブラウザーで次のスクリプトをコピーし、PowerShell ウィンドウで右クリックして、スクリプトを実行します。このスクリプトによって、2 つの仮想ネットワークの間に仮想ネットワーク ピアリングが作成されます。
    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet1ToMyVnet2' `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId $vnet2.Id

    # Peer VNet2 to VNet1.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet2ToMyVnet1' `
      -VirtualNetwork $vnet2 `
      -RemoteVirtualNetworkId $vnet1.Id
    #
    ```
6. 仮想ネットワークのサブネットを確認するには、次のコマンドをコピーして、PowerShell ウィンドウに貼り付けます。

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    #
    ```

*myVnet1* を *myVnet2* に置き換えて、このコマンドをもう一度実行します。 どちらのコマンドも、出力結果の **PeeringState** 列に **Connected** と表示されます。
7. **(省略可)** このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
8. **(省略可)** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-powershell)」の手順を実行してください。

2 つの仮想ネットワークに作成した Azure リソースが、その IP アドレスを使用して相互に通信できるようになりました。 仮想ネットワークに Azure の既定の名前解決を使用する場合、そのネットワーク内のリソースは、通信相手の仮想ネットワークに対して名前を解決することができません。 ピアリングされた仮想ネットワークの間で名前を解決する必要がある場合は、独自の DNS サーバーを作成する必要があります。 その方法については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)」を参照してください。

## <a name="template"></a>ピアリングの作成 - Resource Manager テンプレート

1. [仮想ネットワーク ピアリングの作成](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering)に関する Resource Manager テンプレートを参照します。 このテンプレートには、Azure Portal、PowerShell、Azure CLI を使用したデプロイ手順が記載されています。 仮想ネットワーク ピアリングを作成するために必要なアクセス許可が割り当てられているアカウントでいずれかのツールにサインインし、テンプレートをデプロイします。 詳細については、この記事の「[アクセス許可](#permissions)」セクションを参照してください。
2. **(省略可)** このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
3. **(省略可)** このチュートリアルで作成したリソースを削除するには、Azure Portal、PowerShell、Azure CLI のいずれかを使って、この記事の「[リソースの削除](#delete)」セクションの手順を実行してください。

## <a name="different-models"></a>異なるデプロイメント モデルで作成された仮想ネットワークのピアリング

これまでのセクションでは、Resource Manager デプロイメント モデルで作成された 2 つの仮想ネットワークの間に仮想ネットワーク ピアリングを作成する手順を説明しました。 ピアリングは、Resource Manager デプロイメント モデルで作成された仮想ネットワークとクラシック デプロイメント モデルで作成された仮想ネットワークとの間でも作成することができます。 クラシック デプロイメント モデルで作成された 2 つの仮想ネットワークの間で仮想ネットワーク ピアリングを作成することはできません。 Azure のデプロイメント モデルの詳細については、[Azure デプロイメント モデルの概要](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

仮想ネットワーク ピアリングを作成するには、この記事の[ポータル](#portal)に関するセクションの手順 1. ～ 11. を実行します。ただし手順 3. で myVnet2 を作成するときに、**クラシック** デプロイメント モデルを選択してください。

Resource Manager デプロイメント モデルで作成された仮想ネットワークとクラシック デプロイメント モデルで作成された仮想ネットワークとの間で仮想ネットワーク ピアリングを作成する場合、構成の対象となるのは、Resource Manager 側の仮想ネットワークからクラシック側の仮想ネットワークへのピアリングだけです。 Resource Manager 側の仮想ネットワークのピアリングを作成すると、そのピアリング状態が**更新中**になります。 クラシック側の仮想ネットワークにピアリングを作成する必要はないので、数秒後、この状態は自動的に**接続済み**に変わります。 

> [!NOTE]
> このチュートリアルでは説明していませんが、この記事の [Azure CLI](#cli) セクションと [PowerShell](#powershell) セクションのスクリプトに、このセクションと同じ内容の変更を反映することもできます。

## <a name="different-subscriptions"></a>サブスクリプションが異なる仮想ネットワークの間のピアリング

これまでのセクションでは、同じサブスクリプションにある 2 つの仮想ネットワークの間に仮想ネットワーク ピアリングを作成する手順を説明しました。 サブスクリプションが異なっていても両者が同じ Azure Active Directory テナントに関連付けられていれば、それらのサブスクリプションにある仮想ネットワークの間でピアリングを作成することができます。 AD テナントをまだ持っていない場合は、簡単に[作成](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch)できます。 2 つのサブスクリプションが別々の Active Directory テナントに関連付けられている場合、異なるサブスクリプションの仮想ネットワークの間で仮想ネットワーク ピアリングを作成することはできません。 ピアリングを作成する手順は、仮想ネットワークの作成に使われたデプロイメント モデルによって若干異なります。以降、それぞれの手順を示します。

### <a name="different-subscriptions-same-deployment-model"></a>両方の仮想ネットワークが Resource Manager で作成されている場合

1. この記事の[ポータル](#portal)に関するセクションの手順 1. ～ 7. に従います。ただし手順 5. で myVnet2 に対して選択するサブスクリプションは、手順 4. で myVnet1 に対して選択したものとは異なる (ただし同じ Azure Active Directory テナントに関連付けられている) サブスクリプションを使用してください。
2. 表示された **[myVnet1]** ブレードの左側にある縦長のオプション一覧で **[アクセス制御 (IAM)]** をクリックします。
3. **[myVnet1 - アクセス制御 (IAM)]** ブレードが表示されたら、**[+ 追加]** をクリックします。
4. **[アクセス許可の追加]** ブレードが表示されたら、**[ロール]** ボックスの **[ネットワーク共同作成者]** を選択します。
5. **[選択]** ボックスで、ユーザー名を選択するか、メール アドレスを入力して検索します。 一覧表示されるのは、ピアリングの設定対象の仮想ネットワークと同じ Azure Active Directory テナントからのユーザーです。
6. [ **Save**] をクリックします。
7. **[myVnet1 - アクセス制御 (IAM)]** ブレードの左側にある縦長のオプション一覧で **[プロパティ]** をクリックします。 **[リソース ID]** をコピーします (例: /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet1)。
8. myVnet2 について、このセクションの手順 1. ～ 7. を実行します。
9. 承認を確実に有効にするために、Azure から一度サインアウトして再度サインインします。 2 つの仮想ネットワークで別々のアカウントを使用した場合は、両方のアカウントからサインアウトした後で、もう一度両方のアカウントにログインしてください。
10. ポータル上部の **[リソースの検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **myResourceGroup** が表示されたら、それをクリックします。 **myresourcegroup** リソース グループのブレードが表示されます。 このリソース グループには、先ほどの手順で作成した 2 つの仮想ネットワークが存在します。
11. **[myVNet1]** をクリックします。
12. 表示された **[myVnet1]** ブレードの左側にある縦長のオプション一覧で **[ピアリング]** をクリックします。
13. 表示された **[myVnet1 - ピアリング]** ブレードで **[+ 追加]** をクリックします。
14. 表示された **[ピアリングの追加]** ブレードで次のオプションを入力または選択し、**[OK]** をクリックします。
     - **[名前]**: *myVnet1ToMyVnet2*
     - **[仮想ネットワークのデプロイ モデル]**:  **[クラシック]** を選択します。 
     - **[リソース ID を知っている]**: このチェック ボックスをオンにします。
     - **[リソース ID]**: myVnet2 のリソース ID を入力します。 このボックスは、**[リソース ID を知っている]** チェック ボックスがオンのときにのみ表示されます。
     - **[仮想ネットワーク アクセスを許可する]**: **[有効]** が選択されていることを確認します。
    その他の設定は、このチュートリアルでは使用しません。 すべてのピアリング設定については、[仮想ネットワーク ピアリングの管理](virtual-network-manage-peering.md#create-peering)に関するページを参照してください。
15. 前の手順で **[OK]** をクリックすると、**[ピアリングの追加]** ブレードが閉じて、**[myVnet1 - ピアリング]** ブレードが再度表示されます。 数秒後、作成したピアリングがブレードに表示されます。 作成した **myVnet1ToMyVnet2** ピアリングの **[ピアリング状態]** 列に **[開始済み]** と表示されます。 Vnet1 から Vnet2 へのピアリングを作成したので、今度は Vnet2 から Vnet1 へのピアリングを作成する必要があります。 各仮想ネットワーク内のリソースが相互に通信するためには、ピアリングを双方向で作成する必要があります。
16. myVnet2 について、このセクションの手順 10. ～ 14. を繰り返します。  ピアリングに *myVnet2ToMyVnet1* という名前を付け、**[リソース ID]** に myVnet1 のリソース ID を入力します。
17. **[OK]** をクリックして MyVnet2 のピアリングを作成してから数秒経つと、作成したばかりの **myVnet2ToMyVnet1** ピアリングが**接続済み**として **[ピアリング状態]** 列に表示されます。
18. MyVnet1 について、このセクションの手順 10. ～ 11. を繰り返します。 **myVnet1ToVNet2** ピアリングの **[ピアリング状態]** も**接続済み**になります。 ピアリングにおける両方の仮想ネットワークの **[ピアリング状態]** 列に**接続済み**と表示されれば、ピアリングは正常に確立されています。
19. **(省略可)** このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
20. **(省略可)** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-portal)」セクションの手順を実行してください。

### <a name="different-subscriptions-different-deployment-models"></a>Resource Manager デプロイメント モデルで作成された仮想ネットワークとクラシック デプロイメント モデルで作成された仮想ネットワークの場合
 
[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-preview.md)]

1. プレビューの利用登録を行います。 異なるデプロイメント モデルで作成された 2 つの仮想ネットワークが別々のサブスクリプションに存在する場合、それらの仮想ネットワークをピアリングするためには、プレビューへの登録が必要となります。 プレビューの利用登録は、ポータルまたは Azure CLI を使用して行うことができません。 プレビューの利用登録は PowerShell でのみ行うことができます。 プレビューの利用登録を行うには、次の作業を実行します。
    - PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) モジュールの最新バージョンをインストールします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。
    - Windows PC から PowerShell セッションを開始するには、**[開始]** に移動して「**powershell**」と入力し、**[PowerShell]** をクリックします。
    - PowerShell ウィンドウで `login-azurermaccount` コマンドを入力し、Azure にサインインします。 サインインに使用するアカウントには、仮想ネットワーク ピアリングを作成するためのアクセス許可が必要です。 詳細については、この記事の「[アクセス許可](#permissions)」セクションを参照してください。
    - 両方の Azure サブスクリプションについてプレビュー機能の利用登録を行い、PowerShell から次のコマンドを入力します。 
    
    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    #
    ```
    それぞれのサブスクリプションにサインインしている間にプレビューの利用登録を行います。 次のコマンドを入力した後に表示される **RegistrationState** の出力が両方のサブスクリプションで **Registered** になるまで、以降の手順に進まないでください。

    ```powershell
    Get-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    #
    ```

2. この記事の[ポータル](#portal)に関するセクションにある手順 1. ～ 7. を実行します。ただし、次の点を変更してください。
    - 手順 5. で myVnet2 に対して選択するサブスクリプションは、手順 4. で myVnet1 に対して選択したものとは異なる (ただし同じ Azure Active Directory テナントに関連付けられている) サブスクリプションを使用してください。
    - myVnet2 を作成するときに、手順 3. で **[クラシック]** を選択します。
3. ポータル上部の **[リソースの検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **myResourceGroup** が表示されたら、それをクリックします。 **myresourcegroup** リソース グループのブレードが表示されます。 このリソース グループには、先ほどの手順で作成した 2 つの仮想ネットワークが存在します。
4. **[myVNet1]** をクリックします。
5. この記事の「[両方の仮想ネットワークが Resource Manager で作成されている場合](#different-subscriptions-same-deployment-model)」セクションの手順 2. ～ 9. を実行します。
6. ポータル上部の **[リソースの検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **myResourceGroup** が表示されたら、それをクリックします。 **myresourcegroup** リソース グループのブレードが表示されます。 このリソース グループには、先ほどの手順で作成した 2 つの仮想ネットワークが存在します。
7. **[myVNet1]** をクリックします。
8. 表示された **[myVnet1]** ブレードの左側にある縦長のオプション一覧で **[ピアリング]** をクリックします。
9. 表示された **[myVnet1 - ピアリング]** ブレードで **[+ 追加]** をクリックします。
10. 表示された **[ピアリングの追加]** ブレードで次のオプションを入力または選択し、**[OK]** をクリックします。
     - **[名前]**: *myVnet1ToMyVnet2*
     - **[仮想ネットワークのデプロイ モデル]**:  **[クラシック]** を選択します。 
     - **[リソース ID を知っている]**: このチェック ボックスをオンにします。
     - **[リソース ID]**: myVnet2 のリソース ID を入力します。 このボックスは、**[リソース ID を知っている]** チェック ボックスがオンのときにのみ表示されます。
     - **[仮想ネットワーク アクセスを許可する]**: **[有効]** が選択されていることを確認します。
    その他の設定は、このチュートリアルでは使用しません。 すべてのピアリング設定については、[仮想ネットワーク ピアリングの管理](virtual-network-manage-peering.md#create-peering)に関するページを参照してください。

    Resource Manager デプロイメント モデルで作成された仮想ネットワークとクラシック デプロイメント モデルで作成された仮想ネットワークとの間で仮想ネットワーク ピアリングを作成する場合、構成の対象となるのは、Resource Manager 側の仮想ネットワークからクラシック側の仮想ネットワークへのピアリングだけです。 Resource Manager 側の仮想ネットワークのピアリングを作成すると、そのピアリング状態が**更新中**になります。 クラシック側の仮想ネットワークにピアリングを作成する必要はないので、数秒後、この状態は自動的に**接続済み**に変わります。 
11. **(省略可)** このチュートリアルでは仮想マシンの作成について説明していませんが、それぞれの仮想ネットワークに仮想マシンを作成し、一方の仮想マシンからもう一方の仮想マシンに接続することで接続を検証することができます。
12. **(省略可)** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-portal)」セクションの手順を実行してください。

> [!NOTE]
> このチュートリアルでは説明していませんが、この記事の [Azure CLI](#cli) セクションと [PowerShell](#powershell) セクションのスクリプトに、このセクションと同じ内容の変更を反映することもできます。

## <a name="permissions"></a>アクセス許可

仮想ネットワーク ピアリングの作成に使用するアカウントには、必要なロールまたはアクセス許可が割り当てられている必要があります。 たとえば、VNet1、VNet2 という名前の 2 つの仮想ネットワークをピアリングする場合、各仮想ネットワークに対する次の最小限のロールまたはアクセス許可をアカウントに割り当てる必要があります。
    
|Virtual Network|デプロイメント モデル|役割|アクセス許可|
|---|---|---|---|
|VNet1|リソース マネージャー|[ネットワークの共同作業者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |クラシック|[従来のネットワークの共同作業者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|該当なし|
|VNet2|リソース マネージャー|[ネットワークの共同作業者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||クラシック|[従来のネットワークの共同作業者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

[組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)と、特定のアクセス許可を[カスタム ロール](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てる方法 (Resource Manager のみ) の詳細を参照してください。

## <a name="delete"></a>リソースの削除
このチュートリアルが完了したら、使用料がかからないように、チュートリアルで作成したリソースを削除することをお勧めします。 リソース グループを削除すると、リソース グループ内にあるリソースもすべて削除されます。

### <a name="delete-portal"></a>Azure Portal

1. ポータルの検索ボックスに、「**myResourceGroup**」と入力します。 検索結果で、**[myResourceGroup]** をクリックします。
2. **myResourceGroup** ブレードで **[削除]** アイコンをクリックします。
3. 削除を確定するには、**[リソース グループ名を入力してください]** ボックスに「**myResourceGroup**」と入力し、**[削除]** をクリックします。

### <a name="delete-cli"></a>Azure CLI

Linux、macOS、Windows のいずれかのコマンド シェルから、次のコマンドを入力します。 または、次のコード ブロックの右上にある [使ってみる] ボタンをクリックして Cloud Shell を起動します。 そのうえで、[コピー] ボタンでサンプル コードをコピーし、Cloud Shell に貼り付けます。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

PowerShell のコマンド プロンプトで、次のコマンドを入力します。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

- 運用環境で使用する仮想ネットワーク ピアリングを作成する前に、[仮想ネットワーク ピアリングの制約と動作](virtual-network-manage-peering.md#about-peering)の要点をしっかりと理解します。
- [仮想ネットワーク ピアリングのさまざまな設定](virtual-network-manage-peering.md#create-peering)について理解を深めます。
- 仮想ネットワーク ピアリングで[ハブとスポークのネットワーク トポロジを作成](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)する方法を学習します。

