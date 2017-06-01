---
title: "Azure 仮想ネットワークの作成 | Microsoft Docs"
description: "複数のサブネットを含んだ仮想ネットワークの作成方法について説明します。"
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
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 19857ad1e970ad32359708ded320c53a4778ef8c
ms.contentlocale: ja-jp
ms.lasthandoff: 05/16/2017


---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>複数のサブネットを含んだ仮想ネットワークを作成する

このチュートリアルでは、パブリック用とプライベート用に別個のサブネットを含んだ基本的な Azure 仮想ネットワーク (VNet) の作成方法について説明します。 サブネットには、Virtual Machines (VM)、App Service Environment、Virtual Machine Scale Sets、HDInsight、Cloud Services などの Azure リソースを接続することができます。 VNet に接続されたリソースは互いに、プライベート Azure ネットワーク経由で通信することができます。

以降の各セクションでは、それぞれ Azure [Portal](#portal)、Azure [コマンド ライン インターフェイス](#cli) (CLI)、Azure [PowerShell](#powershell)、Azure Resource Manager [テンプレート](#template)を使って VNet をデプロイする手順を紹介しています。 VNet のデプロイに使用したツールに関係なく結果は同じです。 ツールのリンクをクリックすると、本記事の該当するセクションに直接移動します。 VNet とサブネットの詳しい設定については、[VNet の管理](virtual-network-manage-network.md)と[サブネットの管理](virtual-network-manage-subnet.md)に関する記事を参照してください。

## <a name="portal"></a>Azure Portal

1. インターネット ブラウザーから Azure [Portal](https://portal.azure.com) を開いて、ご利用の Azure [アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)にサインインします。 まだアカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)にサインアップできます。
2. ポータルで、**[+ 新規]** > **[ネットワーキング]** > **[仮想ネットワーク]** の順にクリックします。
3. **[仮想ネットワーク]** ブレードが表示されたら、**[デプロイ モデルの選択]** の *[Resource Manager]* が選択されていることを確認して、**[作成]** をクリックします。
4. **[仮想ネットワークの作成]** ブレードが表示されたら、以下の値を入力して **[作成]** ボタンをクリックします。

    |Setting|値|
    |---|---|
    |名前|*MyVnet*|
    |アドレス空間|*10.0.0.0/16*|
    |サブネット名|パブリック|
    |サブネットのアドレス範囲|*10.0.0.0/24*|
    |リソース グループ|**[新規作成]** を選択して「*MyResourceGroup*」と入力します。|
    |サブスクリプションと場所|サブスクリプションと場所を選択します。

    Azure を初めてご利用の場合は、[リソース グループ](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)、[サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)、[場所](https://azure.microsoft.com/regions) (リージョン) とは何かを、それぞれのリンク先のページでご覧ください。
6. ポータルでは、VNet を作成するときに、サブネットを 1 つだけ作成できます。 このチュートリアルでは、VNet を作成した後にサブネットをもう 1 つ作成します。 後で "*パブリック*" サブネットにはインターネットからアクセスできるリソースを、プライベート サブネットにはインターネットからアクセスできないリソースを接続することになります。 2 つ目のサブネットを作成するために、ポータル上部にある *[リソースの検索]* ボックスに「*MyVnet*」と入力します。 検索結果に **MyVnet** が表示されたら、それをクリックします。 ご利用のサブスクリプションに、同じ名前の VNet が複数存在する場合、同じ名前の各 VNet の下にリソース グループの名前が表示されます。 検索結果で必ず、下に *MyResourceGroup* と表示されているの MyVnet をクリックしてください。
7. **[MyVnet]** ブレードが表示されたら、**[設定]** の **[サブネット]** をクリックします。
8. **[MyVnet - サブネット]** ブレードで **[+サブネット]** をクリックします。
9. **[名前]** に「*Private*」と入力し、**[サブネットの追加]** ブレードの **[アドレスの範囲]** に「*10.0.1.0/24*」と入力して、**[OK]** をクリックします。
10. **[MyVnet - サブネット]** ブレードでサブネットを確認します。 先ほど作成した **Public** サブネットと **Private** サブネットが表示されます。
11. **省略可:** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-portal)」セクションの手順を実行してください。

## <a name="cli"></a>CLI
CLI コマンドは、その実行環境 (Windows、Linux、macOS) に関係なく同じですが、オペレーティング システムのシェル間でスクリプトの記述方法に違いがあります。 以降の手順では、CLI コマンドを含んだ Bash スクリプトの実行方法を説明しています。

1. インターネット ブラウザーから Azure [Portal](https://portal.azure.com) を開いて、ご利用の Azure [アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)にサインインします。 まだアカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)にサインアップできます。
2. ポータルの上部で、*[リソースの検索]* バーの右側にある **[>_]** アイコンをクリックして Bash Azure Cloud Shell (プレビュー) を起動します。 ポータルの下部に Cloud Shell ウィンドウが表示され、数秒後、**username@Azure:~$** プロンプトが表示されます。 Azure へのログイン処理は、ポータルに対する認証時の資格情報を使用して Cloud Shell によって自動的に行われます。
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
    
    # Create an additional subnet within the VNet.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name MyVnet \
      --resource-group MyResourceGroup
    ```
4. スクリプト ファイルを作成して保存します。 Cloud Shell のプロンプトで「`nano myscript.sh --nonewlines`」と入力します。 このコマンドを実行すると、GNU nano エディターが起動して空の myscript.sh ファイルが表示されます。 エディター ウィンドウ内をマウスの右ボタンでクリックし、**[貼り付け]** をクリックします。 Cloud Shell の保存スペースは、セッションの終了後は維持されません。 Cloud Shell セッション終了後もスクリプトを維持する場合は、Cloud Shell の [永続的ストレージ](../cloud-shell/persisting-shell-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)をセットアップしてください。 
5. キーボードの **Ctrl + X** キーを押して「**Y**」と入力し、**Enter** キーを押して、ファイルを myscript.sh として保存します。
6. Cloud Shell プロンプトから `chmod +x myscript.sh` コマンドを実行し、このファイルを実行可能として指定します。
7. 「`./myscript.sh`」と入力してスクリプトを実行します。
8. スクリプトが完了したら、VNet のサブネットを確認します。次のコマンドをコピーして Bash Cloud Shell に貼り付けてください。
    ```azurecli
    az network vnet subnet list --resource-group MyResourceGroup --vnet-name MyVnet --output table
    ```
9. **省略可:** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-cli)」セクションの手順を実行してください。

## <a name="powershell"></a>PowerShell
1. Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) モジュールの最新バージョンをインストールします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をお読みください。
2. Windows の [スタート] ボタンをクリックして「**Powershell**」と入力し、検索結果の **[PowerShell]** をクリックして PowerShell セッションを開始します。
3. PowerShell ウィンドウで `login-azurermaccount` コマンドを入力し、ご利用の Azure [アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)でサインインします。 まだアカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)にサインアップできます。
4. ブラウザーで次のスクリプトをコピーします。
    ```powershell
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name MyResourceGroup `
      -Location eastus
    
    # Create two subnets
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroup `
      -Location eastus `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    #
    ```
5. スクリプトを実行するには、PowerShell ウィンドウでマウスの右ボタンをクリックします。
6. VNet のサブネットを確認します。次のコマンドをコピーして、PowerShell ウィンドウに貼り付けてください。
    ```powershell
    $Vnet = $Vnet.subnets | Format-Table Name, AddressPrefix
    ```
7. **省略可:** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete-powershell)」セクションの手順を実行してください。

## <a name="template"></a>テンプレート

VNet は、Azure Resource Manager テンプレートを使ってデプロイすることができます。 テンプレートの詳細については、[Resource Manager の概要](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment)に関する記事を参照してください。 実際のテンプレートにアクセスしてそのパラメーターについての情報を入手するには、[2 つのサブネットを含んだ VNet の作成テンプレート](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)の Web ページを参照してください。 テンプレートのデプロイには、[ポータル](#template-portal)、[CLI](#template-cli)、[PowerShell](#template-powershell) のいずれかを使用できます。

**省略可:** このチュートリアルで作成したリソースを削除するには、この記事の「[リソースの削除](#delete)」セクションの各サブセクションに記載されている手順を実行してください。

### <a name="template-portal"></a>ポータル

1. ブラウザーでテンプレートの [Web ページ](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets)を開きます。
2. **[Azure に配置する]** ボタンをクリックして、Azure Portal のサインイン ページを開きます。
3. Azure [アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)でポータルにサインインします。 まだアカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)にサインアップできます。
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

5. 利用条件に同意して **[購入]** をクリックし、VNet をデプロイします。

### <a name="template-cli"></a>CLI

1. インターネット ブラウザーから Azure [Portal](https://portal.azure.com) を開いて、ご利用の Azure [アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)にサインインします。 まだアカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)にサインアップできます。
2. ポータルの上部で、*[リソースの検索]* バーの右側にある **[>_]** アイコンをクリックして Bash Azure Cloud Shell (プレビュー) を起動します。 ポータルの下部に Cloud Shell ウィンドウが表示され、数秒後、**username@Azure:~$** プロンプトが表示されます。 Azure へのログイン処理は、ポータルに対する認証時の資格情報を使用して Cloud Shell によって自動的に行われます。
3. 「 `az group create --name MyResourceGroup --location eastus`」というコマンドを入力して VNet のリソース グループを作成します。
4. テンプレートは、次の方法でデプロイできます。
    - **既定のパラメーター値:** 次のコマンドを入力します:   `az group deployment create --resource-group MyResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
    - **カスタム パラメーター値:** テンプレートをダウンロードして編集してからデプロイするか、コマンド ラインからパラメーターを指定してテンプレートをデプロイするか、または、別個のパラメーター ファイルを使用してテンプレートをデプロイします。 テンプレートとパラメーター ファイルは、[2 つのサブネットを含んだ VNet の作成テンプレート](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)の Web ページにある **[GitHub で参照する]** ボタンをクリックしてダウンロードできます。 GitHub で **azuredeploy.parameters.json** ファイルまたは **azuredeploy.json** ファイルをクリックし、そのファイルの **[Raw]** ボタンをクリックします。 その内容をブラウザーでコピーし、コンピューター上のファイルに保存します。 テンプレート内のパラメーターの値を変更するか、または別個のパラメーター ファイルを使ってテンプレートをデプロイします。  

    これらの手段でテンプレートをデプロイする方法について詳しくは、「`az group deployment create --help`」と入力してください。

### <a name="template-powershell"></a>PowerShell

1. Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) モジュールの最新バージョンをインストールします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/azure/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をお読みください。
2. Windows の [スタート] ボタンをクリックして「**powershell**」と入力し、検索結果の **[PowerShell]** をクリックして PowerShell セッションを開始します。
3. PowerShell ウィンドウで `login-azurermaccount` コマンドを入力し、ご利用の Azure [アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)でサインインします。 まだアカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)にサインアップできます。
4. 「 `New-AzureRmResourceGroup -Name MyResourceGroup -Location eastus`」というコマンドを入力して VNet のリソース グループを作成します。
5. テンプレートは、次の方法でデプロイできます。
    - **既定のパラメーター値:** 次のコマンドを入力します:   `New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName MyResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`        
    - **カスタム パラメーター値:** テンプレートをダウンロードして編集してからデプロイするか、コマンド ラインからパラメーターを指定してテンプレートをデプロイするか、または、別個のパラメーター ファイルを使用してテンプレートをデプロイします。 テンプレートとパラメーター ファイルは、[2 つのサブネットを含んだ VNet の作成テンプレート](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/)の Web ページにある **[GitHub で参照する]** ボタンをクリックしてダウンロードできます。 GitHub で **azuredeploy.parameters.json** ファイルまたは **azuredeploy.json** ファイルをクリックし、そのファイルの **[Raw]** ボタンをクリックします。 その内容をブラウザーでコピーし、コンピューター上のファイルに保存します。 テンプレート内のパラメーターの値を変更するか、または別個のパラメーター ファイルを使ってテンプレートをデプロイします。  

    これらの手段でテンプレートをデプロイする方法について詳しくは、「`Get-Help New-AzureRmResourceGroupDeployment`」と入力してください。 

## <a name="delete"></a>リソースの削除
このチュートリアルを終えた後は、使用料が発生しないようリソースを削除してください。 リソース グループを削除すると、そこに含まれているリソースもすべて削除されます。

### <a name="delete-portal"></a>ポータル

1. ポータルの上部にある *[リソースの検索]* ボックスに「*MyResourceGroup*」と入力します。 検索結果に **MyResourceGroup** が表示されたら、それをクリックしてください。
2. MyResourceGroup ブレードが表示されたら、ブレード上部付近の [削除] アイコンをクリックします。
3. 削除を確定するには、**[リソース グループ名を入力してください]** ボックスに「*MyResourceGroup*」と入力し、**[削除]** をクリックします。

### <a name="delete-cli"></a>CLI

Cloud Shell のプロンプトから「`az group delete --name MyResourceGroup --yes`」というコマンドを入力します。

### <a name="delete-powershell"></a>PowerShell

PowerShell プロンプトから「`Remove-AzureRmResourceGroup -Name MyResourceGroup`」というコマンドを入力します。

## <a name="next-steps"></a>次のステップ

- VNet とサブネットの詳しい設定については、[VNet の管理](virtual-network-manage-network.md#view-vnet)と[サブネットの管理](virtual-network-manage-subnet.md#create-subnet)に関する記事を参照してください。 さまざまな要件を満たす運用環境の VNet とサブネットを作成できる各種オプションが存在します。
- 受信方向と送信方向のサブネット トラフィックをフィルタリングするには、[ネットワーク セキュリティ グループ](virtual-networks-nsg.md) (NSG) を作成してサブネットに適用します。
- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM または [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM を作成して VNet に接続します。
- VNet を同じ場所にある別の VNet に接続するには、[VNET ピアリング](virtual-network-peering-overview.md)を使用します。
- VNet をオンプレミスのネットワークに接続するには、[サイト間仮想プライベート ネットワーク](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (VPN) または [ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 回線を使用します。

