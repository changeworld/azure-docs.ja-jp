---
title: "サブネットを含んだ Azure 仮想ネットワークを作成する | Microsoft Docs"
description: "Azure で、複数のサブネットを含んだ仮想ネットワークを作成する方法について説明します。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: jdial
ms.custom: 
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 1ecfba0470c31d1119b5e6c0f9d8538abe40f834
ms.contentlocale: ja-jp
ms.lasthandoff: 06/02/2017


---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>複数のサブネットを含んだ仮想ネットワークを作成する

このチュートリアルでは、パブリック用とプライベート用に別個のサブネットを含んだ基本的な Azure 仮想ネットワークの作成方法について説明します。 サブネットには、仮想マシン、Azure App Service 環境、仮想マシンのスケール セット、Azure HDInsight 、およびその他のクラウド サービスなどの Azure リソースを接続することができます。 仮想ネットワークに接続されているリソースは、プライベートの Azure ネットワーク経由で相互に通信できます。

次のセクションでは、[Azure ポータル](#portal)、Azure コマンドライン インターフェイス ([Azure CLI](#azure-cli))、 [Azure PowerShell](#powershell)、および[Azure Resource Manager テンプレート](#resource-manager-template)を使って仮想ネットワークにデプロイするために実行する手順を説明します。 仮想ネットワークのデプロイに使用したツールに関係なく、結果は同じです。 このチュートリアルの該当セクションに移動するには、ツール リンクをクリックしてください。 すべての仮想ネットワークとサブネットの設定に関する詳細については、[仮想ネットワークの管理](virtual-network-manage-network.md)、および[仮想サブネットの管理](virtual-network-manage-subnet.md)に関する記事をご覧ください。

## <a name="portal"></a>Azure Portal

1. インターネット ブラウザーで [Azure Portal](https://portal.azure.com) を開きます。 お使いの [Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)を使用して、サインインします。 Azure アカウントを持っていない場合、 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)でサインアップできます。
2. ポータルで、**[+ 新規]** > **[ネットワーキング]** > **[仮想ネットワーク]** の順にクリックします。
3. **[仮想ネットワーク]** ブレードで、**[デプロイ モデルの選択]** の **[Resource Manager]** が選択されていることを確認して、**[作成]** をクリックします。
4. **[仮想ネットワークの作成]** ブレードで、次の値を入力して **[作成]** をクリックします。

    |Setting|値|
    |---|---|
    |名前|MyVnet|
    |アドレス空間|10.0.0.0/16|
    |サブネット名|パブリック|
    |サブネットのアドレス範囲|10.0.0.0/24|
    |リソース グループ|**[新規作成]** を選択して、「**MyResourceGroup**」と入力します。|
    |サブスクリプションと場所|サブスクリプションと場所を選択します。

    Azure を初めてご利用の場合は、[リソース グループ](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)、[サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)、[場所](https://azure.microsoft.com/regions) (*リージョン*とも呼ばれる) について、それぞれのリンク先のページでご覧ください。
6. ポータルでは、仮想ネットワークを作成するときに、サブネットを 1 つだけ作成できます。 このチュートリアルでは、仮想ネットワークを作成した後に、2 つ目のサブネットを作成します。 インターネットにアクセス可能なリソースは、後で**パブリック** サブネットに接続できます。 また、インターネットからアクセスできないリソースは、**プライベート** サブネットに接続できます。 2 つ目のサブネットを作成するために、ページの上部にある **[リソースの検索]** ボックスに、「**MyVnet**」と入力します。 検索結果で、**[MyVnet]** をクリックします。 サブスクリプションに同じ名前の複数の仮想ネットワークがある場合は、各仮想ネットワーク下に一覧表示されるリソース グループを確認してください。 必ず **MyResourceGroup** リソース グループを含む **MyVnet**の検索結果をクリックしてください。
7. **[MyVnet]** ブレードで、**[設定]** の **[サブネット]** をクリックします。
8. **[MyVnet - サブネット]** ブレードで **[+サブネット]** をクリックします。
9. **[サブネットの追加]** ブレードで、**[名前]** に「**Private**」と入力します。 **[アドレス範囲]**に「**10.0.1.0/24**」と入力します。  **[OK]**をクリックします。
10. **[MyVnet - サブネット]** ブレードで、サブネットを確認します。 先ほど作成した **Public** サブネットと **Private** サブネットが表示されます。
11. **省略可:** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-portal)」の手順を実行してください。

## <a name="azure-cli"></a>Azure CLI

Windows、Linux、または macOS のどこからコマンドを実行しても、Azure CLI コマンドは同じです。 ただし、オペレーティング システム シェル間ではスクリプトに違いがあります。 以降の手順では、Azure CLI コマンドを含んだ Bash スクリプトの実行方法を説明しています。

1. インターネット ブラウザーで [Azure Portal](https://portal.azure.com) を開きます。 お使いの [Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)を使用して、サインインします。 Azure アカウントを持っていない場合、 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)でサインアップできます。
2. ポータル ページの上部で、**[リソースの検索]** ボックスの右側にある **[>_]** アイコンをクリックして Bash Azure Cloud Shell (プレビュー) を起動します。 [Cloud Shell] ウィンドウが、ポータルの下部に表示されます。 数秒後に、 **username@Azure:~ $** プロンプトが表示されます。 Azure へのサインイン処理は、ポータルにサインインする際に使用される資格情報を使用して、Cloud Shell によって自動的に行われます。
3. ブラウザーで次のスクリプトをコピーします。
    ```azurecli
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name MyResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet.
    az network vnet create \
      --name MyVnet \
      --resource-group MyResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet within the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name MyVnet \
      --resource-group MyResourceGroup
    ```
4. スクリプト ファイルを作成して保存します。 Cloud Shell コマンド プロンプトで、「`nano myscript.sh --nonewlines`」と入力します。 このコマンドを実行すると、GNU nano エディターが起動して空の myscript.sh ファイルが表示されます。 エディター ウィンドウ内にカーソルを置いて、**[貼り付け]** をクリックします。  
5. ファイルを myscript.sh として保存するには、Ctrl キーを押しながら X キーを押して、「**Y**」と入力してから、Enter キーを押します。 Cloud Shell のストレージでは、セッションの終了後は保存されたファイルを維持しません。 Cloud Shell セッション終了後もスクリプトを維持する場合は、Cloud Shell の [永続的ストレージ](../cloud-shell/persisting-shell-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)をセットアップして、永続的なストレージにファイルを保存してください。
6. このファイルを実行可能ファイルとして指定するために、Cloud Shell のコマンド プロンプトで `chmod +x myscript.sh` コマンドを実行します。
7. スクリプトを実行するには、「`./myscript.sh`」と入力します。
8. スクリプトの実行が終了するときに、仮想ネットワークのサブネットを確認するには、以下のコマンドをコピーして、Bash Cloud Shell ウィンドウに貼り付けます。
    ```azurecli
    az network vnet subnet list --resource-group MyResourceGroup --vnet-name MyVnet --output table
    ```
9. **省略可:** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-cli)」の手順を実行してください。

## <a name="powershell"></a>PowerShell

1. PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) モジュールの最新バージョンをインストールします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。
2. PowerShell セッションを開始するには、**[開始]** に移動して「**powershell**」と入力し、**[PowerShell]** をクリックします。
3. PowerShell ウィンドウで、ご利用の [Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)を使ってサインインするには、「`login-azurermaccount`」と入力します。
4. ブラウザーで次のスクリプトをコピーします。
    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroup `
      -Location eastus
    
    # Create two subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroup `
      -Location eastus `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    #
    ```
5. スクリプトを実行するには、PowerShell ウィンドウでマウスの右ボタンをクリックします。
6. 仮想ネットワークのサブネットを確認するには、以下のコマンドをコピーして、PowerShell ウィンドウに貼り付けます。
    ```powershell
    $Vnet = $Vnet.subnets | Format-Table Name, AddressPrefix
    ```
7. **省略可:** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-powershell)」の手順を実行してください。

## <a name="resource-manager-template"></a>Resource Manager テンプレート

Azure Resource Manager テンプレートを使用して、仮想ネットワークをデプロイできます。 テンプレートの詳細については、[Resource Manager の概要](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment)に関する記事をご覧ください。 テンプレートにアクセスしてそのパラメーターについての情報を入手するには、「[Create a virtual network with two subnets](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)」(2 つのサブネットを含んだ仮想ネットワークの作成) のテンプレートをご覧ください。 テンプレートのデプロイには、[ポータル](#template-portal)、[Azure CLI](#template-cli)、[PowerShell](#template-powershell) のいずれかを使用できます。

**省略可:** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete)」のセクションにある手順を実行してください。

### <a name="template-portal"></a>Azure Portal

1. ブラウザーで[テンプレート ページ](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets)を開きます。
2. **[Deploy to Azure]** (Azure にデプロイ) ボタンをクリックします。 Azure Portal サインイン ページが開きます。
3. [Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)を使用して、ポータルにサインインします。 Azure アカウントを持っていない場合、 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)でサインアップできます。
4. 各パラメーターの値を次のように入力します。

    |パラメーターが含まれる必要があります。|値|
    |---|---|
    |サブスクリプション|サブスクリプションを選択します。|
    |リソース グループ|MyResourceGroup|
    |場所|場所を選択します。|
    |VNet 名|MyVnet|
    |VNet のアドレス プレフィックス|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|パブリック|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Private|

5. 利用条件に同意して **[購入]** をクリックし、仮想ネットワークをデプロイします。

### <a name="template-cli"></a>Azure CLI

1. [ポータル](https://portal.azure.com)で、[Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)を使ってサインインします。 Azure アカウントを持っていない場合、 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)でサインアップできます。
2. **[リソースの検索]** ボックスの右側にある **[>_]** アイコンをクリックして Bash Azure Cloud Shell (プレビュー) を起動します。 [Cloud Shell] ウィンドウが、ポータルの下部に表示されます。 数秒後に、 **username@Azure:~ $** プロンプトが表示されます。 Azure へのサインイン処理は、Azure Portal にサインインする際に使用される資格情報を使って、Cloud Shell によって自動的に行われます。
3. 仮想ネットワークのリソース グループを作成するには、 `az group create --name MyResourceGroup --location eastus` というコマンドを入力します。
4. 次のパラメーター オプションのいずれかを使用して、テンプレートをデプロイできます。
    - **既定のパラメーター値** ph id="ph1" xmlns="urn:oasis:names:tc:xliff:document:1.2">  `az group deployment create --resource-group MyResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`</ph> というコマンドを入力する
    - **カスタムのパラメーター値** テンプレートをデプロイする前に、テンプレートをダウンロードして変更します。 また、コマンド ラインでパラメーターを使用してテンプレートをデプロイしたり、別個のパラメーター ファイルを使ってテンプレートをデプロイすることもできます。 テンプレートとパラメーター ファイルをダウンロードするには、[[Create a virtual network with two subnets]\(2 つのサブネットで仮想ネットワークを作成する\)](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) テンプレート ページで、**[GitHub で参照する]** ボタンをクリックします。 GitHub で **azuredeploy.parameters.json** ファイルまたは **azuredeploy.json** ファイルをクリックします。 次に、**[Raw]** ボタンをクリックして、ファイルを表示します。 ブラウザーで、ファイルのコンテンツをコピーします。 お使いのコンピューター上のファイルにコンテンツを保存します。 テンプレート内のパラメーターの値を変更するか、または別個のパラメーター ファイルを使ってテンプレートをデプロイできます。  

    これらの手段でテンプレートをデプロイする方法について詳しくは、「`az group deployment create --help`」と入力してください。

### <a name="template-powershell"></a>PowerShell

1. PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) モジュールの最新バージョンをインストールします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。
2. PowerShell セッションを開始するには、**[開始]** に移動して「**powershell**」と入力し、**[PowerShell]** をクリックします。
3. PowerShell ウィンドウで、ご利用の [Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)を使ってサインインするには、「`login-azurermaccount`」と入力します。
4. 仮想ネットワークのリソース グループを作成するには、 `New-AzureRmResourceGroup -Name MyResourceGroup -Location eastus` というコマンドを入力します。
5. 次のパラメーター オプションのいずれかを使用して、テンプレートをデプロイできます。
    - **既定のパラメーター値** ph id="ph1" xmlns="urn:oasis:names:tc:xliff:document:1.2">  `New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName MyResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`</ph> というコマンドを入力する        
    - **カスタムのパラメーター値** デプロイする前に、テンプレートをダウンロードして変更します。 また、コマンド ラインでパラメーターを使用してテンプレートをデプロイしたり、別個のパラメーター ファイルを使ってテンプレートをデプロイすることもできます。 テンプレートとパラメーター ファイルをダウンロードするには、[[Create a virtual network with two subnets]\(2 つのサブネットで仮想ネットワークを作成する\)](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) テンプレート ページで、**[GitHub で参照する]** ボタンをクリックします。 GitHub で **azuredeploy.parameters.json** ファイルまたは **azuredeploy.json** ファイルをクリックします。 次に、**[Raw]** ボタンをクリックして、ファイルを表示します。 ブラウザーで、ファイルのコンテンツをコピーします。 お使いのコンピューター上のファイルにコンテンツを保存します。 テンプレート内のパラメーターの値を変更するか、または別個のパラメーター ファイルを使ってテンプレートをデプロイできます。  

    これらの手段でテンプレートをデプロイする方法に関する詳細は、「`Get-Help New-AzureRmResourceGroupDeployment`」と入力して確認してください。 

## <a name="delete"></a>リソースの削除
このチュートリアルが完了したら、使用料がかからないように、チュートリアルで使用したリソースを削除したい場合があります。 リソース グループを削除すると、リソース グループ内にあるリソースもすべて削除されます。

### <a name="delete-portal"></a>Azure Portal

1. ポータルの検索ボックスに、「**MyResourceGroup**」と入力します。 検索結果で、**[MyResourceGroup]** をクリックします。
2. **MyResourceGroup** ブレードで **[削除]** アイコンをクリックします。
3. 削除を確定するには、**[リソース グループ名を入力してください]** ボックスに「**MyResourceGroup**」と入力し、**[削除]** をクリックします。

### <a name="delete-cli"></a>Azure CLI

Cloud Shell のコマンド プロンプトで、`az group delete --name MyResourceGroup --yes` というコマンドを入力します。

### <a name="delete-powershell"></a>PowerShell

PowerShell のコマンド プロンプトで、`Remove-AzureRmResourceGroup -Name MyResourceGroup` というコマンドを入力します。

## <a name="next-steps"></a>次のステップ

- すべての仮想ネットワークとサブネットの設定に関する詳細については、[仮想ネットワークの管理](virtual-network-manage-network.md#view-vnet)、および[仮想サブネットの管理](virtual-network-manage-subnet.md#create-subnet)に関する記事をご覧ください。 運用環境の仮想ネットワークとサブネットを使用して別の要件を満たすには、さまざまなオプションがあります。
- 受信方向と送信方向のサブネット トラフィックをフィルタリングするには、[ネットワーク セキュリティ グループ](virtual-networks-nsg.md) (NSG) を作成してサブネットに適用します。
- [Windows 仮想マシン](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json)または[Linux 仮想マシン](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を作成して、仮想ネットワークに接続します。
- [仮想ネットワーク ピアリング](virtual-network-peering-overview.md)を使用して、仮想ネットワークを同じ場所にある別の仮想ネットワークに接続します。
- [サイト間の仮想プライベート ネットワーク](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Azure ExpressRoute ](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)回線を使用して、仮想ネットワークをオンプレミスのネットワークに接続します。

