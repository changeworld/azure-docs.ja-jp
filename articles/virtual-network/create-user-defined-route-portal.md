---
title: "Azure Portal を使用してネットワーク仮想アプライアンス経由でネットワーク トラフィックをルーティングするユーザー定義ルートを作成する - Azure portal | Microsoft Docs"
description: "ネットワーク仮想アプライアンス経由でネットワーク トラフィックをルーティングして、Azure の既定のルーティングを上書きするユーザー定義ルートを作成する方法を説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 0319029277091611673f15c94604604850cbfcbe
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2017
---
# <a name="create-a-user-defined-route---azure-portal"></a>Azure Portal を使用してユーザー定義ルートを作成する

このチュートリアルでは、ネットワーク仮想アプライアンスを通じて 2 つの[仮想ネットワーク](virtual-networks-overview.md) サブネット間でトラフィックをルーティングするユーザー定義ルートを作成する方法を説明します。 ネットワーク仮想アプライアンスは、ファイアウォールなどのネットワーク アプリケーションが実行されている仮想マシンです。 Azure 仮想ネットワークにデプロイできる事前構成されたネットワーク仮想アプライアンスの詳細については、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) のページをご覧ください。

仮想ネットワークでサブネットを作成すると、次の図に示すように、既定の[システム ルート](virtual-networks-udr-overview.md#system-routes)が作成され、すべてのサブネットのリソースが相互に通信できます。

![既定のルート](./media/create-user-defined-route/default-routes.png)

このチュートリアルでは、次の図に示すように、パブリック サブネット、プライベート サブネット、および DMZ サブネットを使用して仮想ネットワークを作成します。 一般的には、パブリック サブネットには Web サーバーが、プライベート サブネットにはアプリケーションまたはデータベース サーバーがデプロイされます。 DMZ サブネットには、ネットワーク仮想アプライアンスとして機能する仮想マシンを作成し、必要に応じて、ネットワーク仮想アプライアンス経由で通信する仮想マシンを各サブネットに作成します。 次の図に示すように、パブリック サブネットとプライベート サブネットの間のトラフィックがすべてアプライアンス経由でルーティングされます。

![ユーザー定義のルート](./media/create-user-defined-route/user-defined-routes.png)

この記事では、Resource Manager デプロイメント モデルを使用してユーザー定義ルートを作成する手順について説明します。ユーザー定義ルートを作成するときは、このデプロイメント モデルを使用することをお勧めします。 ユーザー定義ルート (クラシック) を作成する必要がある場合は、[ユーザー定義ルート (クラシック) の作成](virtual-network-create-udr-classic-ps.md)に関するページをご覧ください。 Azure 展開モデルの知識がない場合は、[Azure 展開モデルの概要](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。 ユーザー定義ルートの詳細については、[ユーザー定義ルートの概要](virtual-networks-udr-overview.md#user-defined)に関する記事をご覧ください。

## <a name="create-routes-and-network-virtual-appliance"></a>ルートとネットワーク仮想アプライアンスの作成

1. **前提条件**: 「[仮想ネットワークの作成](#create-a-virtual-network)」の手順を実行して、2 つのサブネットで仮想ネットワークを作成します。
2. 仮想ネットワークの作成後、インターネット ブラウザーで [Azure Portal](https://portal.azure.com) に移動します。 [Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)を使用してログインします。
3. ポータル上部の **[リソースの検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **myResourceGroup** が表示されたら、それをクリックします。 リソース グループは、前提条件の手順で作成されています。
4. 次の図に示すように、**[myVnet]** をクリックします。

    ![ネットワーク インターフェイスの設定](./media/create-user-defined-route/virtual-network.png)

5. ネットワーク仮想アプライアンスのサブネットを作成します。
 
    - **[myVnet]** で、左側にある **[設定]** の **[サブネット]** をクリックします。
    - 次の図に示すように、**[+ サブネット]** をクリックします。

        ![サブネット](./media/create-user-defined-route/subnets.png) 
    - **[サブネットの追加]** で次の値を入力し、**[OK]** をクリックします。

        |設定|値|
        |-----|-----|
        |名前|DMZ|
        |アドレス範囲 (CIDR ブロック)|10.0.2.0/24|

6. ネットワーク仮想アプライアンスの仮想マシンを作成します。

    - ポータルの左側で **[+ 新規]**、**[Compute]** の順にクリックし、**[Windows Server 2016 Datacenter]** または **[Ubuntu Server 16.04 LTS]** をクリックします。
    - 表示された **[Basic]** ブレードで次の値を入力し、**[OK]** をクリックします。

        |設定|値|
        |---|---|
        |名前|myVm-Nva|
        |ユーザー名|azureuser|
        |パスワードとパスワードの確認|任意のパスワード|
        |[サブスクリプション]|サブスクリプションを選択します。|
        |リソース グループ|**[既存のものを使用]** をクリックし、**[myResourceGroup]** を選択します。|
        |場所|米国東部|
    - 表示された **[サイズの選択]** ブレードで、**[DS1_V2 Standard]**、**[選択]** の順にクリックします。
    - 表示された **[設定]** ブレードで、**[仮想ネットワーク]** をクリックします。 表示された **[仮想ネットワークの選択]** ブレードで、**[myVnet]** をクリックします。
    - **[設定]** ブレードで **[サブネット]** をクリックします。 表示された **[サブネットの選択]** で、**[DMZ]** をクリックします。 
    - 残りの設定は既定値のままにして、**[OK]** をクリックします。
    - 表示された **[作成]** ブレードで、**[作成]** をクリックします。 仮想マシンのデプロイには数分かかります。

    > [!NOTE]
    > Azure Portal では、仮想マシンを作成するほかに、パブリック IP アドレスが既定で作成され、仮想マシンに割り当てられます。 仮想マシンを運用環境にデプロイした場合は、パブリック IP アドレスが仮想マシンに割り当てられないように選択できます。 代わりに、仮想ネットワーク内の他の仮想マシンから、ネットワーク仮想アプライアンスにアクセスする場合があります。 パブリック IP アドレスの詳細については、[パブリック IP アドレスの管理](virtual-network-public-ip-address.md)に関するページをご覧ください。

7. 静的なプライベート IP アドレスを割り当てて、前の手順で作成されたネットワーク インターフェイスに対して IP 転送を有効にします。 
    - ページ上部の **[リソースの検索]** ボックスに「*myVm-Nva*」と入力します。
    - 検索結果に **myVm-Nva** が表示されたら、それをクリックします。
    - 左側にある **[設定]** で **[ネットワーク]** をクリックします。
    - **[myVm-Nva - ネットワーク インターフェイス]** でネットワーク インターフェイスの名前をクリックします。 名前は **myvm-nva***X* です。ここで、*X* は、ポータルによって割り当てられる番号です。
    - 次の図に示すように、ネットワーク インターフェイスの **[設定]** で **[IP 構成]** をクリックします。

        ![ネットワーク インターフェイスの設定](./media/create-user-defined-route/network-interface-settings.png)
        
    - **[IP 転送]** 設定の **[有効]** をクリックし、**[保存]** をクリックします。 自身に割り当てられている IP アドレスが宛先になっていないトラフィックを受信するネットワーク インターフェイスごとに、IP 転送を有効にする必要があります。 IP 転送を有効にすると、ネットワーク インターフェイスに対する Azure の送信元/送信先チェックが無効になります。
    - IP 構成の一覧で **[ipconfig1]** をクリックします。
    - 次の図に示すように、**[ipconfig1]** で、プライベート IP アドレスの **[割り当て]** で **[静的]** をクリックします。

        ![IP 構成](./media/create-user-defined-route/ip-configuration.png)
    - 前の図に示すように、**[プライベート IP アドレスの設定]** の **[IP アドレス]** に「*10.0.2.4*」と入力します。 静的 IP アドレスをネットワーク インターフェイスに割り当てた場合、そのアドレスは、ネットワーク インターフェイスが接続されている仮想マシンが有効な限り変更されません。 入力したアドレスは、現在、ネットワーク インターフェイスがある DMZ サブネットの別のリソースに割り当てられていません。 
    - 構成を保存するには、**[ipconfig1]** で **[保存]** をクリックします。 ipconfig1 ボックスは、ネットワーク インターフェイスが保存されているポータルで通知を受信するまで閉じないでください。
 
8. 2 つのルート テーブルを作成します。 ルート テーブルには、0 個以上のルートが含まれています。

    - ポータルの左側で、**[+ 新規]** > **[ネットワーク]** > **[ルート テーブル]** の順にクリックします。
    - **[ルート テーブルの作成]** で次の値を入力し、**[作成]** をクリックします。

        |設定|値|
        |---|---|
        |名前|myRouteTable-Public|
        |[サブスクリプション]|サブスクリプションを選択します。|
        |リソース グループ|**[既存のものを使用]**、**[myResourceGroup]** の順に選択します|
        |場所|米国東部|
    
    - 手順 8. の前のサブステップを再度実行しますが、ルート テーブルには、*myRouteTable-Private* という名前を付けてください。
9. ルートを *myRouteTable-Public* ルート テーブルに追加して、そのルート テーブルを "*パブリック*" サブネットに関連付けます。

    - ポータル上部の **[リソースの検索]** ボックスに「*myRouteTable-Public*」と入力します。 検索結果に **myRouteTable-Public** が表示されたら、それをクリックします。
    - **[myRouteTable-Public]** で、**[設定]** の一覧の **[ルート]** をクリックします。
    - **[myRouteTable-Public - ルート]** で **[+ 追加]** をクリックします。
    -  既定では、仮想ネットワーク内のすべてのサブネット間でトラフィックがルーティングされます。 ルートを作成することで、Azure の既定のルーティングを変更します。"*パブリック*" サブネットからのトラフィックは、直接ではなく、NVA 経由で "*プライベート*" サブネットにルーティングされます。 表示された **[ルートの追加]** ブレードで次の値を入力し、**[OK]** をクリックします。

        |設定|値|説明|
        |---|---|---|
        |ルート名|ToPrivateSubnet|このルートは、トラフィックを、ネットワーク仮想アプライアンス経由でプライベート サブネットにルーティングします。|
        |アドレス プレフィックス|10.0.1.0/24| このアドレス プレフィックス (10.0.1.0 - 10.0.1.254) 内のアドレスに送信されたトラフィックはすべて、ネットワーク仮想アプライアンスに送信されます。|
        |次ホップの種類| **[仮想アプライアンス]** を選択します||
        |次ホップ アドレス|10.0.2.4| ネットワーク仮想アプライアンスに接続されているネットワーク インターフェイスの静的なプライベート IP アドレス。 アドレスを指定できるホップの種類は、**[仮想アプライアンス]** だけです。|

    - **[myRouteTable-Public]** で、**[設定]** の **[サブネット]** をクリックします。 
    - **[myRouteTable-Public - サブネット]** で **[+ 関連付け]** をクリックします。
    - **[サブネットの関連付け]** で **[仮想ネットワーク]** をクリックし、**[myVnet]** をクリックします。
    - **[サブネットの関連付け]** で **[サブネット]** をクリックし、**[サブネットの選択]** で **[パブリック]** をクリックします。 
    - 構成を保存するには、**[サブネットの関連付け]** で **[OK]** をクリックします。 サブネットには、0 個または 1 個のルート テーブルを関連付けることができます。
10. 手順 9. をもう一度実行して **myRouteTable - プライベート**を検索し、次の設定でルートを作成して、ルート テーブルを **myVnet** 仮想ネットワークの**プライベート** サブネットに関連付けます。

    |設定|値|説明|
    |---|---|---|
    |ルート名|ToPublicSubnet|このルートは、トラフィックを、ネットワーク仮想アプライアンス経由でパブリック サブネットにルーティングします。|
    |アドレス プレフィックス|10.0.0.0/24| このアドレス プレフィックス (10.0.0.0 - 10.0.1.254) 内のアドレスに送信されたトラフィックはすべて、ネットワーク仮想アプライアンスに送信されます。|
    |次ホップの種類| **[仮想アプライアンス]** を選択します||
    |次ホップ アドレス|10.0.2.4||

    プライベート サブネットとパブリック サブネットのすべてのリソースの間のネットワーク トラフィックが、ネットワーク仮想アプライアンスを通過します。 
11. **省略可能:** パブリック サブネットとプライベート サブネットに仮想マシンを作成し、「[ルーティングの検証](#validate-routing)」の手順を実行して、仮想マシン間の通信がネットワーク仮想アプライアンス経由でルーティングされていることを検証します。 
12. **省略可能:** 「[リソースの削除](#delete-resources)」の手順を実行して、このチュートリアルで作成したリソースを削除します。

## <a name="validate-routing"></a>ルーティングの検証

1. 「[ルートとネットワーク仮想アプライアンスの作成](#create-routes-and-network-virtual-appliance)」の手順をまだ実行していない場合は、これを完了します。
2. 次のボックスの **[お試しください]** ボタンをクリックすると、Azure Cloud Shell が開きます。 メッセージが表示されたら、[Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)を使用して Azure にログインします。 Azure アカウントを持っていない場合、 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)でサインアップできます。 Azure Cloud Shell は、Azure コマンド ライン インターフェイスがプレインストールされた無料の Bash シェルです。 

    次のスクリプトにより、"*パブリック*" サブネットと "*プライベート*" サブネットに 1 つずつ、2 つの仮想マシンが作成されます。 また、このスクリプトによって、NVA のオペレーティング システム内のネットワーク インターフェイスで IP 転送が有効になり、オペレーティング システムが、ネットワーク インターフェイス経由でトラフィックをルーティングできるようになります。 運用 NVA は、通常、ルーティング前にトラフィックを検査しますが、このチュートリアルのシンプルな NVA は、検査せずにトラフィックをルーティングします。 

    次の **Linux** または **Windows** のスクリプトで **[コピー]** をクリックし、スクリプトの内容をテキスト エディターに貼り付けます。 *adminPassword* 変数のパスワードを変更し、スクリプトを Azure Cloud Shell に貼り付けます。 「[ルートとネットワーク仮想アプライアンスの作成](#create-routes-and-network-virtual-appliance)」の手順 6. でネットワーク仮想アプライアンスを作成したときに選択したオペレーティング システムに対して、スクリプトを実行します。 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. パブリック サブネットとプライベート サブネットの仮想マシン間の通信を検証します。 

    - *myVm-Public* 仮想マシンのパブリック IP アドレスへの [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) または[リモート デスクトップ](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Windows) 接続を確立します。
    - *myVm-Public* 仮想マシンのコマンド プロンプトで `ping myVm-Private` を入力します。 NVA がパブリック サブネットからプライベート サブネットへのトラフィックをルーティングするため、応答が届きます。
    - *myVm-Public* 仮想マシンから、パブリック サブネットとプライベート サブネットの仮想マシン間で traceroute を実行します。 パブリック サブネットとプライベート サブネットの仮想マシンにインストールしたオペレーティング システムに応じて、適切なコマンドを入力します。
        - **Windows**: コマンド プロンプトで、`tracert myvm-private` コマンドを実行します。
        - **Ubuntu**: `tracepath myvm-private` コマンドを実行します。
      トラフィックは、10.0.2.4 (NVA) を経由してから、10.0.1.4 (プライベート サブネットの仮想マシン) に到達します。 
    - 前の手順を完了するには、*myVm-Private* 仮想マシンに接続し、*myVm-Public* 仮想マシンに ping を実行します。 traceroute には、10.0.2.4 経由で 10.0.0.4 (パブリック サブネットの仮想マシン) に到達する通信が示されます。

      > [!NOTE]
      > 前述の手順で、Azure のプライベート IP アドレス間のルーティングを確認できます。 パブリック IP アドレスへのトラフィックをネットワーク仮想アプライアンス経由で転送またはプロキシ処理する場合:
      > - アプライアンスが、ネットワーク アドレス変換またはプロキシ機能を備えている必要があります。 ネットワーク アドレス変換を行う場合、アプライアンスは、ソース IP アドレスを独自に変換し、その要求をパブリック IP アドレスを転送する必要があります。 アプライアンスがネットワーク アドレスをソース アドレスに変換している場合でもプロキシ処理している場合でも、Azure は、ネットワーク仮想アプライアンスのプライベート IP アドレスをパブリック IP アドレスに変換します。 Azure がパブリック IP アドレスをプライベート IP アドレスに変換するために使用するさまざまな方法の詳細については、[送信用接続の詳細](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。
      > - ルート テーブル内の追加ルート。プレフィックス 0.0.0.0/0、次のホップの VirtualAppliance の種類、(前のスクリプト例で使用されている) 次のホップの IP アドレス 10.0.2.4 など。
      >
    - **省略可能**: Azure 内で 2 つの仮想マシン間の次ホップを検証するには、Azure Network Watcher の次ホップ機能を使用します。 Network Watcher を使用するには、使用するリージョンに対して [Azure Network Watcher インスタンスを作成](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)しておく必要があります。 このチュートリアルでは、米国東部リージョンが使用されます。 リージョンに対して Network Watcher インスタンスを有効にすると、次のコマンドを入力して、パブリック サブネットとプライベート サブネットの仮想マシン間の次ホップ情報を確認できます。
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       出力により *10.0.2.4* が **nextHopIpAddress** として、*VirtualAppliance* が **nextHopType** として返されます。 

> [!NOTE]
> このチュートリアルでは、概念を説明するために、パブリック IP アドレスがパブリック サブネットとプライベート サブネットの仮想マシンに割り当てられ、Azure 内ですべてのネットワーク ポート アクセスが、両方の仮想マシンに対して有効になっています。 運用環境で使用する仮想マシンを作成するときは、ネットワーク仮想アプライアンスをプライベート サブネットの前にデプロイするか、ネットワーク セキュリティ グループをサブネットまたはネットワーク インターフェイス、あるいはその両方に割り当てて、パブリック IP アドレスが仮想マシンに割り当てられないようにして、プライベート サブネットへのネットワーク トラフィックをフィルターします。 ネットワーク セキュリティ グループの詳細については、[ネットワーク セキュリティ グループ](virtual-networks-nsg.md)に関するページをご覧ください。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

このチュートリアルでは、2 つのサブネットを持つ既存の仮想ネットワークが必要です。 仮想ネットワークをすばやく作成するには、次のボックスの **[お試しください]** ボタンをクリックします。 **[お試しください]** ボタンをクリックすると、[Azure Cloud Shell](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) が開きます。 Cloud Shell は PowerShell または Bash シェルを実行しますが、このセクションでは、Bash シェルを使用して仮想ネットワークを作成します。 Bash シェルには、Azure コマンド ライン インターフェイスがインストールされています。 Cloud Shell でメッセージが表示されたら、[Azure アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)を使用して Azure にログインします。 Azure アカウントを持っていない場合、 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)でサインアップできます。 このチュートリアルで使用される仮想ネットワークを作成するには、次のボックスの **[コピー]** をクリックし、スクリプトを Azure Cloud Shell に貼り付けます。

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

ポータル、PowerShell、または Azure Resource Manager テンプレートを使用して仮想ネットワークを作成する方法の詳細については、[仮想マシンの作成](virtual-networks-create-vnet-arm-pportal.md)に関するページをご覧ください。

## <a name="delete-resources"></a>リソースを削除する

このチュートリアルが完了したら、使用料がかからないように、作成したリソースを削除することをお勧めします。 リソース グループを削除すると、リソース グループ内にあるリソースもすべて削除されます。 ポータルが開いたら、次の手順を実行します。

1. ポータルの検索ボックスに、「**myResourceGroup**」と入力します。 検索結果で、**[myResourceGroup]** をクリックします。
2. **myResourceGroup** ブレードで **[削除]** アイコンをクリックします。
3. 削除を確定するには、**[リソース グループ名を入力してください]** ボックスに「**myResourceGroup**」と入力し、**[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

- [可用性の高いネットワーク仮想アプライアンス](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json)を作成します。
- ネットワーク仮想アプライアンスには、多くの場合、複数のネットワーク インターフェイスと IP アドレスが割り当てられています。 [ネットワーク インターフェイスを既存の仮想マシンに追加](virtual-network-network-interface-vm.md#vm-add-nic)し、[IP アドレスを既存のネットワーク インターフェイスに追加](virtual-network-network-interface-addresses.md#add-ip-addresses)する方法を確認します。 すべての仮想マシンのサイズに 2 つ以上のネットワーク インターフェイスを接続できますが、各仮想マシンのサイズがサポートするのは、ネットワーク インターフェイスの最大数です。 各仮想マシンのサイズがサポートするネットワーク インターフェイスの数については、[Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) と [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の仮想マシンのサイズをご覧ください。 
- [サイト間 VPN 接続](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)によってオンプレミスのトンネル トラフィックを強制的に実施するユーザー定義ルートを作成します。
