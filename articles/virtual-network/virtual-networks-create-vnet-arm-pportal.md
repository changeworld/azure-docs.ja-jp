---
title: 複数のサブネットを含んだ Azure 仮想ネットワークを作成する - Portal | Microsoft Docs
description: Azure Portal を使用して、複数のサブネットを含んだ仮想ネットワークを作成する方法について説明します。
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 898bdef779282d7312c76696f744b97ec2dfcded
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/13/2018
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-portal"></a>Azure Portal を使用して、複数のサブネットを含んだ仮想ネットワークを作成する

仮想ネットワークでは、インターネットとの通信、および仮想ネットワーク間のプライベート通信のために、複数の種類の Azure リソースを使用できます。 仮想ネットワークに複数のサブネットを作成すると、ネットワークをセグメント化して、サブネット間のトラフィックのフローをフィルター処理または制御できます。 この記事では、次の方法について説明します。

> [!div class="checklist"]
> * 仮想ネットワークの作成
> * サブネットの作成
> * 仮想マシン間のネットワーク通信のテスト

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="log-in-to-azure"></a>Azure にログインする 

Azure Portal (http://portal.azure.com) にログインします。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[ネットワーク]** を選択してから、**[仮想ネットワーク]** を選択します。
3. 次の図に示すように、**[名前]** に「*myVirtualNetwork*」、**[アドレス空間]** に「*10.0.0.0/16*」、**[リソース グループ]** に「**myResourceGroup**」、サブネットの **[名前]** に「*パブリック*」、サブネットの **[アドレス範囲]** に「10.0.0.0/24」と入力し、**[場所]** と **[サブスクリプション]** を選択し、残りの既定値はそのままにして **[作成]** を選択します。

    ![仮想ネットワークの作成](./media/virtual-networks-create-vnet-arm-pportal/create-virtual-network.png)

    **[アドレス空間]** と **[アドレス範囲]** は CIDR 表記で指定されます。 指定された**アドレス空間**には、IP アドレス 10.0.0.0 ～ 10.0.255.254 が含まれています。 サブネットに対して指定された**アドレス範囲**は、仮想ネットワークに対して定義された**アドレス空間**内である必要があります。 Azure DHCP は、サブネット内でデプロイされるリソースに、サブネットのアドレス範囲内の IP アドレスを割り当てます。 Azure は、**パブリック** サブネット内でデプロイされるリソースに、アドレス 10.0.0.4 ～ 10.0.0.254 のみを割り当てます。なぜなら、Azure は、各サブネット内の最初の 4 つのアドレス (この例のサブネットでは 10.0.0.0 ～ 10.0.0.3) と最後のアドレス (この例のサブネットでは 10.0.0.255) を予約するからです。

## <a name="create-a-subnet"></a>サブネットの作成

1. ポータルの上部にある **[Search resources, services, and docs]\(リソース、サービス、ドキュメントの検索\)** ボックスで、「*myVirtualNetwork*」の入力を開始します。 検索結果に **[myVirtualNetwork]** が表示されたら、それを選択します。
2. 次の図に示すように、**[サブネット]** を選択し、**[+ サブネット]** を選択します。

     ![サブネットの追加](./media/virtual-networks-create-vnet-arm-pportal/add-subnet.png)
     
3. 表示された **[サブネットの追加]** ボックスで、**[名前]** に「*プライベート*」、**[アドレス範囲]** に「*10.0.1.0/24*」を入力し、**[OK]** を選択します。  サブネットのアドレス範囲は、仮想ネットワーク内の他のサブネットのアドレス範囲と重複することはできません。 

運用で使用するために Azure 仮想ネットワークとサブネットをデプロイする前に、アドレス空間に関する[考慮事項](manage-virtual-network.md#create-a-virtual-network)や[仮想ネットワークの制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に十分精通しておくことをお勧めします。 リソースがサブネットにデプロイされた後に、仮想ネットワークやサブネットの変更 (アドレス範囲の変更など) を行うと、サブネットにデプロイされている既存の Azure リソースを再デプロイする必要が生じる場合があります。

## <a name="test-network-communication"></a>ネットワーク通信をテストする

仮想ネットワークでは、インターネットとの通信、および仮想ネットワーク間のプライベート通信のために、複数の種類の Azure リソースを使用できます。 仮想ネットワークにデプロイできるリソースの種類の 1 つは、仮想マシンです。 仮想ネットワークに 2 つの仮想マシンを作成して、後の手順でそれらの間のネットワーク通信と、インターネットとのネットワーク通信を検証できるようにします。

### <a name="create-virtual-machines"></a>仮想マシンを作成する

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[コンピューティング]**、**[Windows Server 2016 Datacenter]** の順に選択します。 別のオペレーティング システムを選択することもできますが、以降の手順では、**[Windows Server 2016 Datacenter]** を選択したという前提で説明します。 
3. **[基本]** で以下の情報を選択または入力し、**[OK]** を選択します。
    - **[名前]**: *myVmWeb*
    - **[リソース グループ]**: **[既存のものを使用]** を選択し、*[myResourceGroup]* を選択します。
    - **[場所]**: *[米国東部]* を選択します。

    入力した**ユーザー名**と**パスワード**は、以降の手順で使用されます。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。 選択した**場所**および**サブスクリプション**は、仮想ネットワークの場所およびサブスクリプションと同じである必要があります。 仮想ネットワークが作成されているリソース グループと同じリソース グループを選択することは必須ではありませんが、このチュートリアルでは同じリソース グループを選択しています。
4. **[サイズの選択]** で、VM サイズを選択します。
5. **[設定]** で以下の情報を選択または入力し、**[OK]** を選択します。
    - **[仮想ネットワーク]**: **[myVirtualNetwork]** が選択されていることを確認します。 そうでない場合は、**[仮想ネットワーク]** を選択し、**[仮想ネットワークの選択]** で **[myVirtualNetwork]** を選択します。
    - **[サブネット]**: **[パブリック]** が選択されていることを確認します。 そうでない場合は、**[サブネット]** を選択し、次の図に示すように **[サブネットの選択]** で **[パブリック]** を選択します。
    
        ![仮想マシンの設定](./media/virtual-networks-create-vnet-arm-pportal/virtual-machine-settings.png)
 
6. **[概要]** の **[作成]** で **[作成]** を選択して、仮想マシンのデプロイを開始します。
7. 手順 1 ～6 を再度実行しますが、今回は仮想マシンの **[名前]** に「*myVmMgmt*」と入力し、**[サブネット]** で **[プライベート]** を選択します。

仮想マシンの作成には数分かかります。 両方の仮想マシンの作成が完了するまで、以降の手順に進まないでください。

この記事で作成した仮想マシンは、1 つの[ネットワーク インターフェイス](virtual-network-network-interface.md)と、ネットワーク インターフェイスに動的に割り当てられる 1 つの IP アドレスを備えています。 VM をデプロイした後に、[複数のパブリックおよびプライベート IP アドレスを追加したり、IP アドレスの割り当て方法を静的に変更](virtual-network-network-interface-addresses.md#add-ip-addresses)したりできます。 仮想マシンの作成時に選択した [VM サイズ](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)でサポートされている最大数まで、[ネットワーク インターフェイスを追加](virtual-network-network-interface-vm.md#vm-add-nic)できます。 また、VM に対して[シングル ルート I/O 仮想化 (SR-IOV)](create-vm-accelerated-networking-powershell.md) を有効にすることもできます (ただし、作成した VM の VM サイズがこの機能に対応している場合に限ります)。

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>仮想マシン間の通信とインターネットとの通信

1. ポータルの上部にある *[検索]* ボックスで、「*myVmMgmt*」の入力を開始します。 検索結果に **[myVmMgmt]** が表示されたら、それを選択します。
2. 次の図に示すように、**[接続]** を選択して *myVmMgmt* 仮想マシンへのリモート デスクトップ接続を作成します。

    ![仮想マシンへの接続](./media/virtual-networks-create-vnet-arm-pportal/connect-to-virtual-machine.png)  

3. VM に接続するには、ダウンロードした RDP ファイルを開きます。 メッセージが表示されたら、**[Connect]** を選択します。
4. 仮想マシンの作成時に指定したユーザー名とパスワードを入力し (仮想マシンの作成時に入力した資格情報を指定するために、**[More choices]\(その他の選択肢\)**、**[Use a different account]\(別のアカウントを使用する\)** の選択が必要になる場合があります)、**[OK]** を選択します。
5. サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** を選択して、接続処理を続行します。
6. 後の手順で、*myVmWeb* 仮想マシンから *myVmMgmt* 仮想マシンと通信するために ping を使用します。 ping は ICMP を使用しますが、既定では ICMP は Windows ファイアウォールで拒否されます。 コマンド プロンプトから次のコマンドを入力して、Windows ファイアウォールで ICMP を有効にします。

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    この記事では ping を使用していますが、運用環境のデプロイでは Windows ファイアウォールで ICMP を許可することは推奨されません。
7. セキュリティ上の理由から、仮想ネットワーク内でリモートで接続できる仮想マシンの数を制限するのが一般的です。 このチュートリアルでは、*myVmMgmt* 仮想マシンは、仮想ネットワーク内で *myVmWeb* 仮想マシンを管理するために使用されます。 *myVmMgmt* 仮想マシンから *myVmWeb* 仮想マシンにリモート デスクトップ接続するには、コマンド プロンプトから次のコマンドを入力します。

    ``` 
    mstsc /v:myVmWeb
    ```
8. *myVmWeb* 仮想マシンから *myVmMgmt* 仮想マシンに通信するには、コマンド プロンプトで次のコマンドを入力します。

    ```
    ping myvmmgmt
    ```

    次の出力例のような出力が返されます。
    
    ```
    Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
    Ping statistics for 10.0.1.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms
    ```
      
    *myVmMgmt* 仮想マシンのアドレスは 10.0.1.4 であることがわかります。 10.0.1.4 は、前の手順で *myVmMgmt* 仮想マシンをデプロイした*プライベート* サブネットのアドレス範囲内にある、最初の利用可能な IP アドレスでした。  この仮想マシンの完全修飾ドメイン名は、*myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net* です。 ドメイン名の *dar5p44cif3ulfq00wxznl3i3f* の部分は仮想マシンと異なりますが、残りの部分は同じです。 

    既定では、すべての Azure 仮想マシンは既定の Azure DNS サービスを使用します。 仮想ネットワーク内のすべての仮想マシンは、Azure の既定の DNS サービスを使用して、同じ仮想ネットワーク内にある他のすべての仮想マシンの名前を解決できます。 Azure の既定の DNS サービスを使用する代わりに、独自の DNS サーバーや、Azure DNS サービスのプライベート ドメイン機能を使用することもできます。 詳細については、「[独自の DNS サーバーを使用する名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)」または「[プライベート ドメインに Azure DNS を使用する](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。

9. *myVmWeb* 仮想マシンに Windows Server 用インターネット インフォメーション サービス (IIS) をインストールするには、PowerShell セッションから次のコマンドを入力します。

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

10. IIS のインストールが完了したら、*myVmWeb* のリモート デスクトップ セッションを切断します。*myVmMgmt* のリモート デスクトップ セッションは保持されます。 Web ブラウザーを開き、http://myvmweb に移動します。 IIS のウェルカム ページが表示されます。
11. *myVmMgmt* のリモート デスクトップ セッションを切断します。
12. *myVmWeb* 仮想マシンのパブリック IP アドレスを見つけます。 Azure が *myVmWeb* 仮想マシンを作成したときに、*myVmWeb* という名前のパブリック IP アドレス リソースも作成され、仮想マシンに割り当てられています。 手順 2 の図では、*myVmMgmt* 仮想マシンに**パブリック IP アドレス**として 52.170.5.92 が割り当てられていることがわかります。 *myVmWeb* 仮想マシンに割り当てられているパブリック IP アドレスを調べるには、ポータルの検索ボックスで *myVmWeb* を検索し、それが検索結果に表示されたら選択します。

    仮想マシンへのパブリック IP アドレスの割り当ては必須ではありませんが、Azure の既定では、作成したすべての仮想マシンにパブリック IP アドレスが割り当てられます。 インターネットから仮想マシンに通信するには、仮想マシンにパブリック IP アドレスが割り当てられている必要があります。 仮想マシンにパブリック IP アドレスが割り当てられているかどうかにかかわらず、すべての仮想マシンがインターネットとの送信方向の通信を行うことができます。 Azure でのインターネットへの送信接続について詳しくは、「[Azure の送信接続](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。
13. お使いのコンピューターで、*myVmWeb* 仮想マシンのパブリック IP アドレスに移動します。 お使いのコンピューターから IIS のウェルカム ページを表示する試みが失敗します。 この試みが失敗するのは、仮想マシンがデプロイされたときに、Azure が既定で各仮想マシンのネットワーク セキュリティ グループを作成したためです。 

     ネットワーク セキュリティ グループには、ポートと IP アドレスによって受信/送信ネットワーク トラフィックを許可または拒否するセキュリティ規則が含まれています。 Azure が作成した既定のネットワーク セキュリティ グループでは、同じ仮想ネットワーク内のリソース間の、すべてのポート経由の通信が許可されます。 Windows 仮想マシンの場合、既定のネットワーク セキュリティ グループは、すべてのポート (TCP ポート 3389 (RDP) を除く) を経由したインターネットからのすべての受信トラフィックを拒否します。 その結果、既定では、Web サーバーに対してポート 3389 を開きたくない場合でも、インターネットから *myVmWeb* 仮想マシンに直接 RDP することもできます。 Web 閲覧はポート 80 経由で通信するため、インターネットからの通信は失敗します。なぜなら、既定のネットワーク セキュリティ グループにはポート 80 経由のトラフィックを許可するルールがないからです。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループとそれに含まれるすべてのリソースが不要になったら、それらを削除します。 

1. ポータル上部の **[検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **[myResourceGroup]** が表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[TYPE THE RESOURCE GROUP NAME:]\(リソース グループ名を入力してください:\)** に「*myResourceGroup*」と入力し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、複数のサブネットを含んだ仮想ネットワークをデプロイする方法を学習しました。 また、Windows 仮想マシンを作成したときに Azure がその仮想マシンに接続するネットワーク インターフェイスを作成し、ポート 3389 経由のインターネットからのトラフィックのみを許可するネットワーク セキュリティ グループを作成することを学習しました。 次のチュートリアルに進み、個別の仮想マシンではなく、サブネットへのネットワーク トラフィックをフィルター処理する方法を学習しましょう。

> [!div class="nextstepaction"]
> [サブネットへのネットワーク トラフィックをフィルター処理する](./virtual-networks-create-nsg-arm-pportal.md)
