---
title: "高速ネットワークを使った Azure 仮想マシンの作成 | Microsoft Docs"
description: "高速ネットワークを使った仮想マシンの作成方法について説明します。"
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
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 43663ed8becfa69c06699709a18623652df28ed6
ms.contentlocale: ja-jp
ms.lasthandoff: 05/16/2017


---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>高速ネットワークを使った仮想マシンの作成

このチュートリアルでは、高速ネットワークを使った Azure 仮想マシン (VM) の作成方法について説明します。 高速ネットワークによって、VM との間でシングル ルート I/O 仮想化 (SR-IOV) が可能になり、ネットワークのパフォーマンスが大幅に向上します。 この高パフォーマンスのパスによってデータパスからホストがバイパスされ、サポートされる VM タイプの最も要件の厳しいネットワーク ワークロードで使用した場合でも、待ち時間、ジッター、CPU 使用率が軽減されます。 次の図は、2 台の仮想マシン (VM) 間の通信を、高速ネットワークを使う場合と使わない場合とで比較したものです。

![比較](./media/virtual-network-create-vm-accelerated-networking/image1.png)

高速ネットワークを使用しない場合は、VM に出入りするすべてのネットワーク トラフィックがホストと仮想スイッチをスキャンする必要があります。 仮想スイッチでは、ネットワーク セキュリティ グループ、アクセス制御リスト、分離、その他のネットワーク仮想化サービスなど、すべてのポリシーが適用されます。 仮想スイッチの詳細については、[Hyper-V ネットワーク仮想化と仮想スイッチ](https://technet.microsoft.com/library/jj945275.aspx)に関する記事を参照してください。

高速ネットワークを使用した場合、ネットワーク トラフィックは、VM のネットワーク インターフェイス (NIC) に到達した後、VM に転送されます。 高速ネットワークを使用しなかった場合に仮想スイッチによって適用されるネットワーク ポリシーはすべてオフロードされ、ハードウェアで適用されます。 ハードウェアにポリシーを適用することによって、ホストに適用されるポリシーをすべて維持したまま、ホストや仮想スイッチをバイパスして、NIC からネットワーク トラフィックを直接 VM に転送できます。

高速ネットワークのメリットが得られるのは、高速ネットワークが有効になっている VM だけです。 最適な結果を得るには、同じ Azure 仮想ネットワーク (VNet) に接続された 2 台以上の VM でこの機能を有効にしておくことをお勧めします。 VNet の境界を越えて通信するときや、オンプレミスで接続する場合、全体的な待ち時間に対してこの機能がもたらす効果は限定的です。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>メリット
* **待機時間の短縮 / 1 秒あたりのパケット数 (pps)の向上:** データパスから仮想スイッチを削除することで、ホストにおけるパケットのポリシー処理に必要な時間がなくなるため、VM 内で処理できるパケット数が増加します。
* **ジッターの削減:** 仮想スイッチの処理は、適用するポリシーの量と、処理を行う CPU のワークロードによって異なります。 ハードウェアへのポリシーの適用をオフロードすると、パケットが直接 VM に配信され、ホストと VM 間の通信とソフトウェアによる干渉やコンテキスト スイッチがなくなるため、そのばらつきはなくなります。
* **CPU 使用率の削減:** ホストの仮想スイッチをバイパスすることによって、ネットワーク トラフィックを処理するための CPU の使用率を軽減できます。

## <a name="Limitations"></a>制限事項
この機能を使用する場合は、次の制限事項があります。

* **ネットワーク インターフェイスの作成:** 高速ネットワークは、新しい NIC でのみ有効にできます。 既存の NIC に対して有効にすることはできません。
* **VM の作成:** 高速ネットワークを有効にした NIC は、VM の作成時にのみ VM に接続できます。 既存の VM に NIC を接続することはできません。
* **リージョン:** 高速ネットワークを使用した Windows VM は、ほとんどの Azure リージョンで提供されています。 高速ネットワークを使用した Linux VM が提供されているリージョンは 2 つだけです (米国中南部と米国西部 2)。 この機能が利用できるリージョンは、今後拡大していく予定です。
* **サポートされているオペレーティング システム:** Windows では、Microsoft Windows Server 2012 R2 Datacenter と Windows Server 2016 がサポートされます。 Linux では、Ubuntu Server 16.04 LTS (カーネル 4.4.0-77 以上) でサポートされます。 その他のディストリビューションについても間もなく追加される予定です。
* **VM サイズ:** "汎用" タイプと "コンピューティングの最適化" タイプのインスタンス サイズ (8 コア以上)。 詳細については、[Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) と [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の VM サイズに関する記事を参照してください。 サポートされる VM のインスタンス サイズは、今後増える予定です。

これらの制限に対する変更については、[Azure 仮想ネットワークの更新情報](https://azure.microsoft.com/updates/accelerated-networking-in-preview)に関するページでお知らせします。

## <a name="create-a-windows-vm"></a>Windows VM の作成
VM の作成には、Azure Portal または Azure [PowerShell](#windows-powershell) を使用できます。

### <a name="windows-portal"></a>ポータル

1. インターネット ブラウザーから Azure [Portal](https://portal.azure.com) を開いて、ご利用の Azure [アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)にサインインします。 まだアカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)にサインアップできます。
2. ポータルで、**[+ 新規]** > **[Compute]** > **[Windows Server 2016 Datacenter]** の順にクリックします。 
3. **[Windows Server 2016 Datacenter]** ブレードが表示されたら、**[デプロイ モデルの選択]** の *[リソース マネージャー]* を選択状態のままにし、**[作成]** をクリックします。
4. **[基本]** ブレードが表示されたら、以下の値を入力します。それ以外のオプションは既定値のままにするか、独自の値を選択または入力して、**[OK]** ボタンをクリックします。

    |Setting|値|
    |---|---|
    |名前|MyVm|
    |リソース グループ|**[新規作成]** を選択して「*MyResourceGroup*」と入力します。|
    |場所|米国西部 2|

    Azure を初めてご利用の場合は、[リソース グループ](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)、[サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)、[場所](https://azure.microsoft.com/regions) (リージョン) とは何かを、それぞれのリンク先のページでご覧ください。
5. **[サイズの選択]** ブレードが表示されたら、**[最小コア数]** ボックスに「*8*」と入力し、**[すべて表示]** をクリックします。
6. **[DS4_V2 Standard]** をクリックし、**[選択]** ボタンをクリックします。
7. **[設定]** ブレードが表示されたら、**[高速ネットワーク]** で **[有効]** をクリックし、それ以外の設定はすべてそのままにして、**[OK]** ボタンをクリックします。 **注:** これまでの手順で、VM サイズ、オペレーティング システム、または場所に、この記事の「[制限事項](#Limitations)」セクションに記載されていない値を選択した場合、**[高速ネットワーク]** は表示されません。
8. **[概要]** ブレードが表示されたら、**[OK]** ボタンをクリックします。 Azure によって VM の作成が開始されます。 VM の作成には数分かかります。
9. Windows 用の高速ネットワークのドライバーをインストールするには、この記事の「[Windows の構成](#configure-windows)」セクションの手順を実行します。

### <a name="windows-powershell"></a>PowerShell
1. Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) モジュールの最新バージョンをインストールします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をお読みください。
2. Windows の [スタート] ボタンをクリックして「**powershell**」と入力し、検索結果の **[PowerShell]** をクリックして PowerShell セッションを開始します。
3. PowerShell ウィンドウで `login-azurermaccount` コマンドを入力し、ご利用の Azure [アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)でサインインします。 まだアカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)にサインアップできます。
4. ブラウザーで次のスクリプトをコピーします。
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Windows `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
5. PowerShell ウィンドウ内で右クリックし、先ほどのスクリプトを貼り付けて実行します。 ユーザー名とパスワードの入力が求められます。 次の手順で VM に接続する際は、これらの資格情報を使ってログインします。 スクリプト内の値を変更してからスクリプトを実行した結果、エラーが発生した場合、VM サイズ、オペレーティング システム、場所に使用した値が、この記事の「[制限事項](#Limitations)」セクションに記載されていることを確認してください。
6. Windows 用の高速ネットワークのドライバーをインストールするには、この記事の「[Windows の構成](#configure-windows)」セクションの手順を実行します。

### <a name="configure-windows"></a>Windows の構成
Azure に VM を作成したら、Windows 用の高速ネットワークのドライバーをインストールする必要があります。 以下の手順を実行する前に、この記事の「[ポータル](#windows-portal)」または「[PowerShell](#windows-powershell)」の手順に従って、高速ネットワークを使用した Windows VM を作成しておいてください。 

1. インターネット ブラウザーから Azure [Portal](https://portal.azure.com) を開いて、ご利用の Azure [アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)にサインインします。 まだアカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)にサインアップできます。
2. Azure Portal の上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに「*MyVm*」と入力します。 検索結果に **MyVm** が表示されたら、それをクリックします。
3. **[MyVm]** ブレードが表示されたら、その左上隅に表示される **[接続]** ボタンをクリックします。 **注:** **[接続]** ボタンの下に **[作成中]** と表示されている場合、Azure による VM の作成がまだ完了していません。 **[接続]** は必ず、**[接続]** ボタンの下の **[作成中]** が見えなくなってからクリックしてください。
4. **MyVm.rdp** ファイルのダウンロードをブラウザーに許可します。  ダウンロードが完了したら、そのファイルをクリックして開きます。 
5. リモート接続の発行元が特定できないことを伝える **[リモート デスクトップ接続]** ボックスが表示されたら、**[接続]** ボタンをクリックします。
6. **[Windows セキュリティ]** ボックスが表示されたら、**[その他]** をクリックし、**[別のアカウントを使う]** をクリックします。 手順 4. で入力したユーザー名とパスワードを入力し、**[OK]** ボタンをクリックします。
7. リモート コンピューターの ID が確認できないことを伝える [リモート デスクトップ接続] ボックスが表示されたら、**[はい]** ボタンをクリックします。
8. MyVm VM で Windows の [スタート] ボタンをクリックし、**[Internet Explorer]** をクリックします。
9. **[Internet Explorer 11]** ボックスが表示されたら、**[お勧めのセキュリティ、プライバシー、互換性の設定を使う]** をクリックし、**[OK]** をクリックします。
10. Internet Explorer のアドレス バーに「https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84」と入力し、Enter キーを押します。
11. **[セキュリティの警告]** ボックスが表示されたら、**[OK]** をクリックします。
12. **[Internet Explorer]** ボックスが表示されたら、**[追加]** をクリックし、**[信頼済みサイト]** ボックスの **[追加]** ボタンをクリックして、**[閉じる]** ボタンをクリックします。 以降表示されるすべてのボックスについて、これらの手順を実行します。
13. ダウンロードする場合は、ファイルをクリックします。
14. **[License, Terms of Use]\(ライセンス、使用条件\)** ボックスが表示されたら、**[同意する]** をクリックします。
15. 画面下部に表示されるボックスで **[保存]** ボタンをクリックして Internet Explorer にファイルの保存を許可し、**[フォルダーを開く]** ボタンをクリックします。
16. 高速ネットワークのドライバーをインストールする場合は、ファイルをダブルクリックします。 インストール ウィザードではすべて既定値を選択し、ウィザードの最後に **[はい]** ボタンをクリックして VM を再起動してください。
17. VM が再起動したら、もう一度手順 9. ～ 12. を実行して VM に接続します。
18. Windows の [スタート] ボタンを右クリックし、**[デバイス マネージャー]** をクリックします。 **[ネットワーク アダプター]** ノードを展開します。 **[Mellanox ConnectX-3 Virtual Function Ethernet Adapter]** が表示されていることを確認します (下図参照)。
   
    ![[デバイス マネージャー]](./media/virtual-network-create-vm-accelerated-networking/image2.png)

## <a name="create-a-linux-vm"></a>Linux VM の作成
VM の作成には、Azure Portal または Azure [PowerShell](#linux-powershell) を使用できます。

### <a name="linux-portal"></a>ポータル
1. この記事の「[Linux VM の作成 - PowerShell](#linux-powershell)」セクションの手順. 1 ～ 5. に従って、Linux プレビューの高速ネットワークの利用登録を行います。  プレビューの利用登録をポータルから行うことはできません。
2. この記事の「[Windows VM の作成 - ポータル](#windows-portal)」セクションの手順 1. ～ 8. の手順を実行します。 手順 2. では、**[Windows Server 2016 Datacenter]** の代わりに **[Ubuntu Server 16.04 LTS]** をクリックしてください。 このチュートリアルでは、SSH キーではなくパスワードを使用しますが、運用環境のデプロイでは、どちらでも使用できます。 この記事の「[Windows VM の作成 - ポータル](#windows-portal)」セクションの手順 7. で **[高速ネットワーク]** が表示されない場合、次のいずれかの理由が考えられます。
    - プレビューの利用登録が済んでいない。 この記事の「[Linux VM の作成 - Powershell](#linux-powershell)」セクションの手順 4. で説明したように、登録状態が **[登録済み]** になっていることを確認してください。 **注:** 過去に Windows VM プレビューの高速ネットワークに加入した場合 (Windows VM の高速ネットワークの場合、現在、利用登録は不要です)、Linux VM プレビューの高速ネットワークの利用登録は自動的には行われません。 Linux VM プレビューの高速ネットワークに加入するためには、その利用登録を行う必要があります。
    - 選択した VM サイズ、オペレーティング システム、場所のいずれかが、この記事の「[制限事項](#simitations)」セクションに記載された内容と異なる。
3. Linux 用の高速ネットワークのドライバーをインストールするには、この記事の「[Linux の構成](#configure-linux)」セクションの手順を実行します。

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>高速ネットワークを使用した Linux VM をサブスクリプションに作成した後、同じサブスクリプションに高速ネットワークを使用した Windows VM を作成しようとすると、Windows VM の作成に失敗する場合があります。 このプレビューの期間中は、高速ネットワークを使用した Linux VM と高速ネットワークを使用した Windows VM とを別々のサブスクリプションでテストすることをお勧めします。
>

1. Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) モジュールの最新バージョンをインストールします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をお読みください。
2. Windows の [スタート] ボタンをクリックして「**powershell**」と入力し、検索結果の **[PowerShell]** をクリックして PowerShell セッションを開始します。
3. PowerShell ウィンドウで `login-azurermaccount` コマンドを入力し、ご利用の Azure [アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)でサインインします。 まだアカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)にサインアップできます。
4. 以下の手順に従って、Azure プレビューの高速ネットワークの利用登録を行います。
    - 次のコマンドを入力します。

        ```powershell
        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
        ```
    - Azure サブスクリプション ID と意図した用途を記載した電子メールを [axnpreview@microsoft.com](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) に送信します。 
    - 次のコマンドを入力して、プレビューの利用登録が完了したことを確認します。
    
        `Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network`

        上記のコマンドを入力して返される出力結果に "**Registered (登録済み)**" と表示されるまでは、手順 5. に進まないでください。 次のような結果が出力されたときに初めて先に進むことができます。
    
        ```
        FeatureName                       ProviderName      RegistrationState
        -----------                       ------------      -----------------
        AllowAcceleratedNetworkingFeature Microsoft.Network Registered
        ```
      >[!NOTE]
      >過去に Windows VM プレビューの高速ネットワークに加入した場合 (Windows VM の高速ネットワークの場合、現在、利用登録は不要です)、Linux VM プレビューの高速ネットワークの利用登録は自動的には行われません。 Linux VM プレビューの高速ネットワークに加入するためには、その利用登録を行う必要があります。
      >
5. ブラウザーで次のスクリプトをコピーします。
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
6. PowerShell ウィンドウ内で右クリックし、先ほどのスクリプトを貼り付けて実行します。 ユーザー名とパスワードの入力が求められます。 次の手順で VM に接続する際は、これらの資格情報を使ってログインします。 スクリプトでエラーが発生した場合は、次の点を確認してください。
    - 手順 4. の説明に従ってプレビューの利用登録が済んでいること。
    - VM サイズ、オペレーティング システムの種類、場所のいずれかの値を変更してからスクリプトを実行した場合、それらの値が、この記事の「[制限事項](#Limitations)」セクションに記載されていること。
7. Linux 用の高速ネットワークのドライバーをインストールするには、この記事の「[Linux の構成](#configure-linux)」セクションの手順を実行します。

### <a name="configure-linux"></a>Linux の構成

Azure に VM を作成したら、Linux 用の高速ネットワークのドライバーをインストールする必要があります。 以下の手順を実行する前に、この記事の「[ポータル](#linux-portal)」または「[PowerShell](#linux-powershell)」の手順に従って、高速ネットワークを使用した Linux VM を作成しておいてください。 

1. インターネット ブラウザーから Azure [Portal](https://portal.azure.com) を開いて、ご利用の Azure [アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)にサインインします。 まだアカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)にサインアップできます。
2. ポータルの上部で、*[リソースの検索]* バーの右側にある **[>_]** アイコンをクリックして Bash Cloud Shell (プレビュー) を起動します。 ポータルの下部に Bash Cloud Shell ウィンドウが表示され、数秒後、**username@Azure:~$** プロンプトが表示されます。 VM には、Cloud Shell ではなく SSH でご利用のコンピューターから接続することもできますが、このチュートリアルの手順では、Cloud Shell の使用を想定しています。
3. ポータルの上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに「*MyVm*」と入力します。 検索結果に **MyVm** が表示されたら、それをクリックします。
4. **[MyVm]** ブレードが表示されたら、その左上隅に表示される **[接続]** ボタンをクリックします。 **注:** **[接続]** ボタンの下に **[作成中]** と表示されている場合、Azure による VM の作成がまだ完了していません。 **[接続]** は必ず、**[接続]** ボタンの下の **[作成中]** が見えなくなってからクリックしてください。
5. `ssh adminuser@<ipaddress>` の入力を求めるボックスが表示されます。 このコマンドを Cloud Shell に入力し (または、手順 4. で表示されたボックスからコピーして Cloud Shell に貼り付け)、Enter キーを押します。
6. 接続を続行するかどうかを確認する質問に対して「**yes**」と入力し、Enter キーを押します。
7. VM の作成時に入力したパスワードを入力します。 VM に正常にログインすると、adminuser@MyVm:~$ プロンプトが表示されます。 これで Cloud Shell セッションを通じて VM にログインした状態となります。 **注:** Cloud Shell セッションは、無操作状態が 10 分を超えるとタイムアウトになります。
8. このプロンプトで、「`uname -r`」と入力し、出力結果に表示されるバージョンが "4.4.0-77-generic" と一致することを確認します。
9.    以下のコマンドを実行して、標準的なネットワーク vNIC と高速ネットワークの vNIC との間にボンディングを作成します。 ネットワーク トラフィックには、パフォーマンス面で有利な高速ネットワークの vNIC が使用されますが、何らかの構成に変更が生じても、ボンディングによって、ネットワーク トラフィックの中断が確実に防止されます。 
    - `wget https://git.kernel.org/pub/scm/linux/kernel/git/next/linux-next.git/plain/tools/hv/bondvf.sh`
    - `chmod +x ./bondvf.sh`
    - `sudo ./bondvf.sh`
    - `sudo mv ~/bondvf.sh /etc/init.d`
    - `sudo update-rc.d bondvf.sh defaults` 注: "*insserv: warning: script 'bondvf.sh' missing LSB tags and overrides (insserv: 警告: スクリプト "bondvf.sh" に LSB タグとオーバーライドが欠落しています)*" というエラーが発生した場合は、無視してかまいません。
    - `sudo nano /etc/network/interfaces.d/50-cloud-init.cfg` GNU nano エディターを開いてファイルを編集します。
    - エディターで、*auto etho0* と *iface eth0 inet dhcp* の行をコメントアウトします。それぞれの行の先頭に *#* を追加してください。 次の例は、各行に *#* を追加した後の状態を示しています。
        - #<a name="auto-eth0"></a>auto eth0
        - #<a name="iface-eth0-inet-dhcp"></a>iface eth0 inet dhcp
10. **Ctrl + X** キーを押して「**Y**」と入力し、**Enter** キーを押してファイルを保存します。
11. `sudo shutdown -r now` コマンドを入力して VM を再起動します。
12. VM が再起動したら、もう一度手順 5. ～ 7. を実行して VM に再接続します。
13.    `ifconfig` コマンドを実行して、bond0 が表示されていること、また、このインターフェイスが UP として表示されていることを確認します。 **注:** 高速ネットワークを使用するアプリケーションは、*eth0* ではなく *bond0* インターフェイスを介して通信を行う必要があります。  このインターフェイス名は、高速ネットワークの一般提供が始まる前に変更される可能性があります。

