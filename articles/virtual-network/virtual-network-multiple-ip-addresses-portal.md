---
title: "仮想マシンの複数の IP アドレス - Portal | Microsoft Docs"
description: "Azure Portal を使用して、仮想マシンに複数の IP アドレスを割り当てる方法を説明します。"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 716046d5e23de015da42f25f8a1f674de228efac
ms.openlocfilehash: a04192ffde57ae38d901a78a74724a89f43caedb


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>仮想マシンに複数の IP アドレスを割り当てる
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)


Azure 仮想マシン (VM) には、1 つ以上ネットワーク インターフェイス (NIC) をアタッチできます。 NIC には、1 つ以上のパブリックまたはプライベート IP アドレスを割り当てることができます。 Azure 内の IP アドレスの詳細については、「 [Azure 内の IP アドレス](virtual-network-ip-addresses-overview-arm.md) 」の記事をご覧ください。 この記事では、Azure Resource Manager デプロイ モデルで、Azure Portal を使用して VM に複数の IP アドレスを割り当てる方法について説明します。

VM に複数の IP アドレスを割り当てると、次のことが可能になります。

* 異なる IP アドレスと SSL 証明書を持つ複数のウェブサイトやサービスを、1つのサーバーでホストする。
* ファイアウォールやロード バランサーのような、ネットワーク仮想アプライアンスとして機能する。
* 複数の NIC いずれかの複数のプライベート IP アドレスいずれかを Azure Load Balancer のバックエンド プールに追加する。 以前は、プライマリ NIC のプライマリ IP アドレスのみをバックエンド プールに追加できました。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

プレビューに登録するには、 [複数の IP 係](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) まで、メールでサブスクリプション ID と使用目的をご連絡ください。

## <a name="scenario"></a>シナリオ
この記事では、ネットワーク インターフェイスに 3 つの IP 構成を関連付けます。
次のように、3 つのプライベート IP アドレスと 1 つのパブリック IP アドレスが割り当てられた構成が作成され、NIC に割り当てられます。

* IPConfig-1: 動的なプライベート IP アドレス (既定値) と、PIP1 という名前のパブリック IP アドレス リソースからのパブリック IP アドレス。
* IPConfig-2: 静的なプライベート IP アドレス。パブリック IP アドレスなし。
* IPConfig-3: 動的なプライベート IP アドレス。パブリック IP アドレスなし。

    ![イメージ テキスト](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

このシナリオでは、*RG1* というリソース グループがあり、その中に *VNet1* という VNet と *Subnet1* というサブネットがあると想定しています。 さらに、*VM1* という VM と、これに関連付けられている *VM1-NIC1* というネットワーク インターフェイスがあり、*PIP1* というパブリック IP アドレスがあると想定しています。

上記のリソースをまだ作成していない場合は、[こちらの記事](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)で作成方法を確認してください。

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>複数の IP アドレスを持つ VM を作成する
Azure プレビュー ポータルを使用して、上記のシナリオに基づいた複数の IP 構成を作成するには、次の手順に従います。

1. ブラウザーから http://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. IP 構成を追加するネットワーク インターフェイスを選択します。**Virtual Machines** > **VM1** > **ネットワーク インターフェイス** > **VM1-NIC1**
3. **[ネットワーク インターフェイス]** ブレードで、**[IP 構成]** を選択します。 既存の IP 構成の一覧が表示されます。
4. **PIP1** を **ipconfig1** に関連付けるには、**[IP 構成]** ブレードで **[ipconfig1]** を選択します。 **[ipconfig1]** ブレードで、**[パブリック IP アドレス]** の **[有効]** を選択します。
5. **[IP アドレス]** タブで **[必要な設定の構成]** を選択し、**[PIP1]** を選択するか、このプライマリ IP 構成に関連付けるパブリック IP アドレスを選択して [保存] をクリックします。 ここで、関連付ける新しいパブリック IP アドレスを作成することもできます。 ネットワーク インターフェイスを保存中であることを示す通知が表示されます。保存が完了すると、**ipconfig1** に関連付けられた **PIP1** が表示されます。

    ![イメージ テキスト](media\\virtual-network-multiple-ip-addresses-portal\\01-portal.PNG)

    >[!NOTE] 
    > パブリック IP アドレスには、わずかな費用がかかります。 IP アドレスの料金の詳細については、「 [IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses) 」ページをご覧ください。

1. 次に、IP 構成を追加するには、ネットワーク インターフェイスの **[IP 構成]** セクションで **[+追加]** をクリックします。

   > [!NOTE]
   > 最大 250 個のプライベート IP アドレスを NIC に割り当てることができます。 サブスクリプション内で使用できるパブリック IP アドレスの数には制限があります。 詳細については、「 [Azure 制限](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager) 」の記事をご覧ください。
   >
   >
2. **[IP 構成の追加]** ブレードで、IP 構成に名前を付けます。 ここでは、**IPConfig-2** です。 **[割り当て]** で **[静的]** を選択し、使用する IP アドレスを入力します。 このシナリオでは 10.0.0.5 です。 次に、 **[OK]**をクリックします 構成が保存されると、一覧にその IP 構成が表示されます。

    ![イメージ テキスト](media\\virtual-network-multiple-ip-addresses-portal\\02-portal.PNG)
3. 次に、3 つ目の IP 構成を追加します。**[IP 構成]** ブレードで **[+追加]** をクリックし、次に示すように必要な詳細を入力します。 **[OK]** をクリックします。

    ![イメージ テキスト](media\\virtual-network-multiple-ip-addresses-portal\\03-portal.PNG)

    IPConfig-2 と IPConfig-3 もパブリック IP に関連付けることができます。そのためには、**[IP 構成の追加]** ブレードの **[パブリック IP アドレス]** セクションで **[有効]** を選択します。 新しいパブリック IP を作成することも、作成済みのパブリック IP を IP 構成に関連付けることもできます。
4. 次に示すように、すべてのプライベート IP アドレス (プライマリを含む) を、オペレーティング システムの TCP/IP 構成に手動で追加します。 IP アドレスを手動で追加するには、VM に接続し、次の手順に従う必要があります。

**Windows**

1. コマンド プロンプトで、「 *ipconfig /all*」と入力します。  *プライマリ* の IP アドレス (DHCP 経由) のみを表示できます。
2. 次に、コマンド プロンプト ウィンドウで「*ncpa.cpl*」と入力します。 これにより、新しいウィンドウが開きます。
3. **[ローカル エリア接続]** のプロパティを開きます。
4. インターネット プロトコル バージョン 4 (IPv4) をダブルクリックします。
5. **[次の IP アドレスを使う]** を選択して、次の値を入力します。

   * **IP アドレス**: *プライマリ* のプライベート IP アドレスを入力します。
   * **サブネット マスク**: 自分のサブネットに基づいて設定します。 たとえば、たとえば、サブネットが/24 サブネットであれば、サブネット マスクは 255.255.255.0 になります。
   * **デフォルト ゲートウェイ**: サブネット内の最初の IP アドレスです。 サブネットが 10.0.0.0/24 の場合、ゲートウェイの IP アドレスは 10.0.0.1 になります。
   * **[次の DNS サーバーのアドレスを使う]** をクリックし、次の値を入力します。
     * **優先 DNS サーバー:** 独自の DNS サーバーを使用していない場合は、「168.63.129.16」と入力します。  使用している場合は、DNS サーバーの IP アドレスを入力します。
   * **[詳細]** ボタンをクリックし、他の IP アドレスを追加します。 手順 8 にある各セカンダリ プライベート IP アドレスを、プライマリ IP アドレスに指定されたのと同じサブネットの NIC に追加します。
   * **[OK]** をクリックして TCP/IP 設定を閉じ、もう一度 **[OK]** をクリックしてアダプター設定を閉じます。 これで、RDP 接続が再確立されます。
6. コマンド プロンプトで、「 *ipconfig /all*」と入力します。 追加したすべての IP アドレスが表示され、DHCP が無効になります。

**Linux (Ubuntu)**

1. ターミナル ウィンドウを開きます。
2. 自身がルート ユーザーになっていることを確認します。 ルート ユーザーになっていない場合は、次のコマンドを使います。

            sudo -i
3. ネットワーク インターフェイスの構成ファイルを更新します (‘eth0’ と仮定)。

   * DHCP の既存の行アイテムを保持します。 これによって、以前と同様にプライマリ IP アドレスが設定されます。
   * 次のコマンドを使用して、追加の静的 IP アドレスの構成を追加します。

               cd /etc/network/interfaces.d/
               ls

       .cfg ファイルが表示されます。
4. ファイルを開きます: vi *filename*。

    ファイルの末尾に次の行が表示されます。

            auto eth0
            iface eth0 inet dhcp
5. このファイルの行の最後に、次の行を追加します。

            iface eth0 inet static
            address <your private IP address here>
6. 次のコマンドを使用して、ファイルの内容を保存します。

            :wq
7. 次のコマンドを使用して、ネットワーク インターフェイスをリセットします。

           sudo ifdown eth0 && sudo ifup eth0

   > [!IMPORTANT]
   > リモート接続を使用する場合は、同じ行で ifdown と ifup の両方を実行します。
   >
   >
8. 次のコマンドを使用して、IP アドレスがネットワーク インターフェイスに追加されたことを確認します。

            ip addr list eth0

    追加した IP アドレスが、リストの一部として表示されます。

**Linux (Redhat、CentOS、その他)**

1. ターミナル ウィンドウを開きます。
2. 自身がルート ユーザーになっていることを確認します。 ルート ユーザーになっていない場合は、次のコマンドを使います。

            sudo -i
3. パスワードを入力し、画面の指示に従います。 ルート ユーザーになったら、次のコマンドを使用して、ネットワーク スクリプト フォルダーに移動します。

            cd /etc/sysconfig/network-scripts
4. 次のコマンドを使用して、関連する ifcfg ファイルをリストアップします。

            ls ifcfg-*

    ファイルのうちの 1 つに *ifcfg-eth0* があります。
5. 次のコマンドを使用して、*ifcfg-eth0* ファイルをコピーし、名前を *ifcfg-eth0:0* に変更します。

            cp ifcfg-eth0 ifcfg-eth0:0
6. 次のコマンドを使って、 *ifcfg-eth0:0* ファイルを編集します。

            vi ifcfg-eth1
7. 次のコマンドを使って、ファイル内のデバイスを適切な名前 (ここでは *eth0:0* ) に変更します。

            DEVICE=eth0:0
8. *IPADDR = YourPrivateIPAddress* 行を、IP アドレスを反映するように変更します。
9. 次のコマンドを使用して、ファイルの内容を保存します。

            :wq
10. ネットワーク サービスを再起動し、次のコマンドを実行して、変更が成功したかどうかを確認します。

            /etc/init.d/network restart
            Ipconfig

    返されるリストに、追加した IP アドレス「 *eth0:0*」が表示されることを確認します。

## <a name="a-nameaddaadd-ip-addresses-to-an-existing-vm"></a><a name="add"></a>既存の VM に IP アドレスを追加する
既存の NIC に IP アドレスを追加するには、次の手順を完了します。

1. ブラウザーから http://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. **[ネットワーク インターフェイス]** セクションで、IP 構成を追加するネットワーク インターフェイスを選択します。
3. **[ネットワーク インターフェイス]** ブレードで、**[IP 構成]** を選択します。 既存の IP 構成の一覧が表示されます。
4. 次に、IP 構成を追加するには、ネットワーク インターフェイスの [IP 構成] セクションで **[+追加]** をクリックします。
5. [IP 構成の追加] ブレードで、IP 構成に名前を付けます。 **[割り当て]** で使用する IP アドレスの種類 ([静的] または [動的]) を選択します。 IP 構成をパブリック IP に関連付ける場合は、**[パブリック IP アドレス]** の **[有効]** をクリックします。 関連付けない場合は、**[無効]** をクリックします。 **[有効]** を選択すると、既存のパブリック IP を構成に関連付けることができます。[OK] をクリックします。 構成が保存されると、一覧にその IP 構成が表示されます。



<!--HONumber=Nov16_HO3-->


