---
title: "仮想マシンの複数の IP アドレス - Azure CLI | Microsoft Docs"
description: "Azure CLI を使用して Resource Manager で仮想マシンに複数の IP アドレスを割り当てる方法について説明します。"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: bf92cdc3f6adf1bfaffbcae4e8a0257d0682e33d
ms.openlocfilehash: 0bb3e5f74cb6d013a2b14b4e4c81f0936f90004a


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli"></a>Azure CLI を使用して仮想マシンに複数の IP アドレスを割り当てる

> [!div class="op_single_selector"]
> * [Azure ポータル](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)

Azure 仮想マシン (VM) には、1 つ以上のネットワーク インターフェイス (NIC) を接続できます。 NIC には、1 つ以上の静的または動的パブリックおよびプライベート IP アドレスを割り当てることができます。 VM に複数の IP アドレスを割り当てると、次のことが可能になります。

* 異なる IP アドレスと SSL 証明書を持つ複数のウェブサイトやサービスを、1つのサーバーでホストする。
* ファイアウォールやロード バランサーのような、ネットワーク仮想アプライアンスとして機能する。
* 複数の NIC いずれかの複数のプライベート IP アドレスいずれかを Azure Load Balancer のバックエンド プールに追加する。 以前は、プライマリ NIC のプライマリ IP アドレスのみをバックエンド プールに追加できました。 複数の IP 構成の負荷分散方法の詳細については、[複数の IP 構成の負荷分散](../load-balancer/load-balancer-multiple-ip.md)に関する記事をご覧ください。

VM に接続された各 NIC には、1 つ以上の IP 構成が関連付けられています。 各構成には、1 つの静的または動的プライベート IP アドレスが割り当てられています。 また、1 つのパブリック IP アドレス リソースが関連付けられている場合もあります。 パブリック IP アドレス リソースには、動的または静的 IP アドレスが割り当てられています。 Azure 内の IP アドレスの詳細については、「 [Azure 内の IP アドレス](virtual-network-ip-addresses-overview-arm.md) 」の記事をご覧ください。

この記事では、PowerShell を使用して、Azure Resource Manager デプロイ モデルで作成された VM に複数の IP アドレスを割り当てる方法について説明します。 クラシック デプロイ モデルで作成されたリソースには、複数の IP アドレスを割り当てることはできません。 Azure のデプロイ モデルの詳細については、[デプロイ モデルの概要](../resource-manager-deployment-model.md)に関する記事をご覧ください。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>シナリオ
1 つの NIC が接続された VM を作成し、仮想ネットワークに接続します。 VM には、3 つの*プライベート* IP アドレスと 2 つの*パブリック* IP アドレスが必要です。 各 IP アドレスを次の IP 構成に割り当てます。

* **IPConfig-1:** *動的*プライベート IP アドレス (既定) と*静的*パブリック IP アドレスを割り当てます。
* **IPConfig-2:** *静的*プライベート IP アドレスと*静的*パブリック IP アドレスを割り当てます。
* **IPConfig-3:** *動的*プライベート IP アドレスを割り当てます。パブリック IP アドレスは割り当てません。
  
    ![複数の IP アドレス](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

NIC の作成時に IP 構成を NIC に関連付け、VM の作成時に NIC を VM に接続します。 シナリオで使用する IP アドレスの種類は、わかりやすく説明するためのものです。 必要な IP アドレスの種類と割り当ての種類を使用できます。

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>複数の IP アドレスを持つ VM を作成する

以下の手順は、シナリオの説明に従って複数の IP アドレスを持つ VM を作成する方法を示しています。 変数名と IP アドレスの種類は、実際の実装に合わせて変更してください。

1. [Azure CLI のインストールと構成](../xplat-cli-install.md)に関する記事の手順に従って、Azure CLI をインストールして構成し、Azure アカウントにログインします。

2. サブスクリプション ID と使用目的を[複数の IP 係](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)まで電子メールで連絡して、プレビューに登録します。 次の場合、残りの手順は実行しないでください。
    - プレビューへの登録が受諾されたことを通知する電子メールを受け取っていない場合
    - 受け取った電子メールの指示に従っていない場合
3. [リソース グループを作成](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations)し、次に[仮想ネットワークとサブネット](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet)を作成します。 この記事で説明するシナリオに従うために、``` --address-prefixes ``` フィールドと ```--address-prefix``` フィールドを次のように変更します。

    ```azurecli
    --address-prefixes 10.0.0.0/16
    --address-prefix 10.0.0.0/24
    ```
    >[!NOTE] 
    >上記の参照先の記事では、リソースを作成する場所として西ヨーロッパを使用していますが、この記事では米国中西部を使用します。 場所は適宜変更してください。

4. VM の[ストレージ アカウントを作成](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account)します。


5. NIC と NIC に割り当てる IP 構成を作成します。 構成は、必要に応じて追加、削除、または変更できます。 このシナリオで説明する構成は次のとおりです。

    **IPConfig 1**

    次のコマンドを入力して、以下を作成します。

    - 静的パブリック IP アドレスが割り当てられたパブリック IP アドレス リソース
    - パブリック IP アドレス リソースと動的プライベート IP アドレスが割り当てられた IP 構成

    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP --domain-name-label mypublicdns --allocation-method Static
    ```
    > [!NOTE]
    > パブリック IP アドレスには、わずかな費用がかかります。 IP アドレスの料金の詳細については、「 [IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses) 」ページをご覧ください。 サブスクリプション内で使用できるパブリック IP アドレスの数には制限があります。 制限の詳細については、[Azure の制限](../azure-subscription-service-limits.md#networking-limits)に関する記事をご覧ください。

    ```azurecli
    azure network nic create --resource-group myResourceGroup --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 --public-ip-name myPublicIP
    ```

    **IPConfig-2**

     次のコマンドを入力して、新しいパブリック IP アドレス リソース、および静的パブリック IP アドレスと静的プライベート IP アドレスが割り当てられた新しい IP 構成を作成します。
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns2 --allocation-method Static

    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-2 --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
    ```

    **IPConfig-3**

    次のコマンドを入力して、動的プライベート IP アドレスが割り当てられ、パブリック IP アドレスは割り当てられていない IP 構成を作成します。

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3
    ```

    >[!NOTE] 
    >この記事では、すべての IP 構成を 1 つの NIC に割り当てていますが、複数の IP 構成を VM の任意の NIC に割り当てることもできます。 複数の NIC が接続された VM の作成方法については、複数の NIC が接続された VM の作成に関する記事をご覧ください。

6. [Linux VM の作成](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms)手順に従って、Linux VM を作成します。 ```  --availset-name myAvailabilitySet \ ``` プロパティはこのシナリオでは不要であるため、必ず削除してください。 実際のシナリオに基づいて適切な場所を使用します。 

    >[!WARNING] 
    > 選択した場所で該当の VM サイズがサポートされていない場合、VM の作成に関する記事の手順 6 は失敗します。 たとえば、米国中西部の VM の一覧を取得するには、次のコマンドを実行します。 場所の名前は、実際のシナリオに基づいて変更できます。
    > 
    >       azure vm sizes --location westcentralus

    たとえば、VM サイズを Standard DS2 v2 に変更するには、手順 6 の ``` azure vm create ``` コマンドに ```  --vm-size Standard_DS3_v2``` プロパティを追加します。

7. 次のコマンドを入力して、NIC および関連付けられた IP 構成を表示します。

    ```azurecli
    azure network nic show --resource-group myResourceGroup --name myNic1
    ```

## <a name="a-nameosconfigaadd-ip-addresses-to-a-vm-operating-system"></a><a name="OsConfig"></a>VM オペレーティング システムに IP アドレスを追加する

複数のプライベート IP アドレスを構成して作成した VM に接続し、ログインします。 VM に追加したプライベート IP アドレスは、プライマリも含め、すべて手動で追加する必要があります。 お使いの VM オペレーティング システムに応じて、次の手順を実行します。

### <a name="windows"></a>Windows

1. コマンド プロンプトで、「 *ipconfig /all*」と入力します。  *プライマリ* の IP アドレス (DHCP 経由) のみを表示できます。
2. コマンド プロンプトで「*ncpa.cpl*」と入力して、**[ネットワーク接続]** ウィンドウを開きます。
3. **[ローカル エリア接続]** のプロパティを開きます。
4. インターネット プロトコル バージョン 4 (IPv4) をダブルクリックします。
5. **[次の IP アドレスを使う]** を選択して、次の値を入力します。

    * **IP アドレス**: *プライマリ* のプライベート IP アドレスを入力します。
    * **サブネット マスク**: 自分のサブネットに基づいて設定します。 たとえば、たとえば、サブネットが/24 サブネットであれば、サブネット マスクは 255.255.255.0 になります。
    * **デフォルト ゲートウェイ**: サブネット内の最初の IP アドレスです。 サブネットが 10.0.0.0/24 の場合、ゲートウェイの IP アドレスは 10.0.0.1 になります。
    * **[次の DNS サーバーのアドレスを使う]** をクリックし、次の値を入力します。
        * **[優先 DNS サーバー]**: 独自の DNS サーバーを使用していない場合は、「168.63.129.16」と入力します。  独自の DNS サーバーを使用している場合は、そのサーバーの IP アドレスを入力します。
    * **[詳細]** ボタンをクリックし、他の IP アドレスを追加します。 手順 8 にある各セカンダリ プライベート IP アドレスを、プライマリ IP アドレスに指定されたのと同じサブネットの NIC に追加します。
    * **[OK]** をクリックして TCP/IP 設定を閉じ、もう一度 **[OK]** をクリックしてアダプター設定を閉じます。 これで RDP 接続が再確立されます。
6. コマンド プロンプトで、「 *ipconfig /all*」と入力します。 追加したすべての IP アドレスが表示され、DHCP が無効になります。
    
### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. ターミナル ウィンドウを開きます。
2. 自身がルート ユーザーになっていることを確認します。 ルート ユーザーでない場合は、次のコマンドを入力します。

    ```bash
    sudo -i
    ```

3. ネットワーク インターフェイスの構成ファイルを更新します (‘eth0’ と仮定)。

    * DHCP の既存の行アイテムを保持します。 プライマリ IP アドレスが以前と同じ構成のまま維持されます。
    * 次のコマンドを使用して、追加の静的 IP アドレスの構成を追加します。

        ```bash
        cd /etc/network/interfaces.d/
        ls
        ```

    .cfg ファイルが表示されます。
4. ファイルを開きます: vi *filename*。

    ファイルの末尾に次の行が表示されます。

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. このファイルの行の最後に、次の行を追加します。

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    ```

6. 次のコマンドを使用して、ファイルの内容を保存します。

    ```bash
    :wq
    ```

7. 次のコマンドを使用して、ネットワーク インターフェイスをリセットします。

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > リモート接続を使用する場合は、同じ行で ifdown と ifup の両方を実行します。
    >

8. 次のコマンドを使用して、IP アドレスがネットワーク インターフェイスに追加されたことを確認します。

    ```bash
    Ip addr list eth0
    ```

    追加した IP アドレスが、リストの一部として表示されます。
    
### <a name="linux-redhat-centos-and-others"></a>Linux (Redhat、CentOS、その他)

1. ターミナル ウィンドウを開きます。
2. 自身がルート ユーザーになっていることを確認します。 ルート ユーザーでない場合は、次のコマンドを入力します。

    ```bash
    sudo -i
    ```

3. パスワードを入力し、画面の指示に従います。 ルート ユーザーになったら、次のコマンドを使用して、ネットワーク スクリプト フォルダーに移動します。

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. 次のコマンドを使用して、関連する ifcfg ファイルをリストアップします。

    ```bash
    ls ifcfg-*
    ```

    ファイルのうちの 1 つに *ifcfg-eth0* があります。

5. 次のコマンドを使用して、*ifcfg-eth0* ファイルをコピーし、名前を *ifcfg-eth0:0* に変更します。

    ```bash
    cp ifcfg-eth0 ifcfg-eth0:0
    ```

6. 次のコマンドを使って、 *ifcfg-eth0:0* ファイルを編集します。

    ```bash
    vi ifcfg-eth1
    ```

7. 次のコマンドを使って、ファイル内のデバイスを適切な名前 (ここでは *eth0:0* ) に変更します。

    ```bash
    DEVICE=eth0:0
    ```

8. *IPADDR = YourPrivateIPAddress* 行を、IP アドレスを反映するように変更します。
9. 次のコマンドを使用して、ファイルの内容を保存します。

    ```bash
    :wq
    ```

10. ネットワーク サービスを再起動し、次のコマンドを実行して、変更が成功したかどうかを確認します。

    ```bash
    /etc/init.d/network restart
    Ipconfig
    ```

    返されるリストに、追加した IP アドレス「 *eth0:0*」が表示されることを確認します。

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>VM に IP アドレスを追加する

プライベートおよびパブリック IP アドレスを既存の NIC に追加するには、次の手順を実行します。 各例はこの記事で説明する[シナリオ](#Scenario)に基づいています。

1. Azure CLI を開き、1 つの CLI セッションでこのセクションの残りの手順を実行します。 Azure CLI のインストールと構成をまだ行っていない場合は、[Azure CLI のインストールと構成](../xplat-cli-install.md)に関する記事の手順を完了し、Azure アカウントにログインします。

2. サブスクリプション ID と使用目的を[複数の IP 係](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)まで電子メールで連絡して、プレビューに登録します。 次の場合、残りの手順は実行しないでください。
    - プレビューへの登録が受諾されたことを通知する電子メールを受け取っていない場合
    - 受け取った電子メールの指示に従っていない場合


3. 要件に基づいて、以下のいずれかのセクションの手順を実行します。

    **プライベート IP アドレスを追加する**
    
    NIC にプライベート IP アドレスを追加するには、次のコマンドを使用して IP 構成を作成する必要があります。  動的プライベート IP アドレスを追加する場合は、コマンドを入力する前に、```-PrivateIpAddress 10.0.0.7``` を削除します。 静的 IP アドレスを指定するときは、サブネットの未使用のアドレスを指定する必要があります。

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --private-ip-address 10.0.0.7 --name IPConfig-4
    ```
    一意の構成名とプライベート IP アドレス (静的 IP アドレスを使用する構成の場合) を使用して、必要な数だけ構成を作成します。

    **パブリック IP アドレスを追加する**
    
    パブリック IP アドレスを追加するには、新しい IP 構成または既存の IP 構成にパブリック IP アドレスを関連付けます。 必要に応じて、以下のいずれかのセクションの手順を実行します。

    > [!NOTE]
    > パブリック IP アドレスには、わずかな費用がかかります。 IP アドレスの料金の詳細については、「 [IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses) 」ページをご覧ください。 サブスクリプション内で使用できるパブリック IP アドレスの数には制限があります。 制限の詳細については、[Azure の制限](../azure-subscription-service-limits.md#networking-limits)に関する記事をご覧ください。
    >

    **リソースを新しい IP 構成に関連付ける**
    
    すべての IP 構成にプライベート IP アドレスが必要であるため、パブリック IP アドレスを新しい IP 構成に追加するときは、必ずプライベート IP アドレスも追加する必要があります。 既存のパブリック IP アドレス リソースを追加することも、新しいリソースを作成することもできます。 新しいパブリック IP アドレス リソースを作成するには、次のコマンドを入力します。
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3
    ```

    動的プライベート IP アドレスと、関連付けられた *myPublicIP3* パブリック IP アドレス リソースで新しい IP 構成を作成するには、次のコマンドを入力します。

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 --public-ip-name myPublicIP3
    ```

    **リソースを既存の IP 構成に関連付ける**
    パブリック IP アドレス リソースは、このリソースがまだ関連付けられていない IP 構成にのみ関連付けることができます。 IP 構成にパブリック IP アドレスが関連付けられているかどうかを確認するには、次のコマンドを入力します。

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

    返された出力で次のような行を探します。
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet
     
    *IpConfig-3* の **Public IP** 列が空白であるため、現在、この構成にはパブリック IP アドレス リソースは関連付けられていません。 IpConfig-3 に既存のパブリック IP アドレス リソースを追加することも、次のコマンドを入力して新しいリソースを作成することもできます。

    ```azurecli
    azure network public-ip create --resource-group  myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
    ```
    
    パブリック IP アドレス リソースを、*IPConfig-3* という名前の既存の IP 構成に関連付けるには、次のコマンドを入力します。
    
    ```azurecli
    azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 --public-ip-name myPublicIP3
    ```


7. 次のコマンドを入力して、NIC に割り当てられたプライベート IP アドレスとパブリック IP アドレス リソースを表示します。

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```
    次のような出力が表示されます。 
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet  myPublicIP3
     
9. この記事の「[VM オペレーティング システムに IP アドレスを追加する](#OsConfig)」の手順に従って、NIC に追加した IP アドレスを、VM オペレーティング システムに追加します。


<!--HONumber=Nov16_HO3-->


