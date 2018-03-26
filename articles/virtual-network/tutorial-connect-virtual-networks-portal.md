---
title: 仮想ネットワーク ピアリングを使用して仮想ネットワークを接続する - Azure Portal | Microsoft Docs
description: 仮想ネットワーク ピアリングを使用して仮想ネットワークを接続する方法。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: ac0c1033546758a77b43298a5fa8cba5f5204650
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Azure Portal を使用して仮想ネットワーク ピアリングで仮想ネットワークを接続する

仮想ネットワーク ピアリングを使用して、仮想ネットワークを相互に接続できます。 仮想ネットワークをピアリングすると、それぞれの仮想ネットワークに存在するリソースが、あたかも同じ仮想ネットワーク内に存在するかのような待ち時間と帯域幅で相互に通信できます。 この記事では、2 つの仮想ネットワークの作成とピアリングについて説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 2 つの仮想ネットワークを作成する
> * 仮想ネットワーク間のピアリングを作成する
> * ピアリングをテストする

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="log-in-to-azure"></a>Azure にログインする 

Azure Portal (http://portal.azure.com) にログインします。

## <a name="create-virtual-networks"></a>仮想ネットワークを作成する

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[ネットワーク]** を選択してから、**[仮想ネットワーク]** を選択します。
3. 次の図に示すように、**[名前]** に「*myVirtualNetwork1*」、**[アドレス空間]** に「*10.0.0.0/16*」、**[リソース グループ]** に「**myResourceGroup**」、サブネットの **[名前]** に「*Subnet1*」、サブネットの **[アドレス範囲]** に「10.0.0.0/24」と入力し、**[場所]** と **[サブスクリプション]** を選択し、残りの既定値はそのままにして **[作成]** を選択します。

    ![仮想ネットワークの作成](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. 次のように値を変更して、手順 1. ～ 3. を繰り返します。
    - **[名前]**: *myVirtualNetwork2*
    - **[リソース グループ]**: **[既存のものを使用]** を選択し、**[myResourceGroup]** を選択します。
    - **[アドレス空間]**: *10.1.0.0/16*
    - **[サブネットのアドレス範囲]**: *10.1.0.0/24*

    *myVirtualNetwork2* 仮想ネットワークのアドレス プレフィックスは、*myVirtualNetwork1* 仮想ネットワークのアドレス空間とは重複しません。 アドレス空間が重複する仮想ネットワークをピアリングすることはできません。

## <a name="peer-virtual-networks"></a>仮想ネットワークをピアリングする

1. Azure Portal の上部にある [検索] ボックスで、「*MyVirtualNetwork1*」の入力を開始します。 検索結果に **[myVirtualNetwork1]** が表示されたら、それを選択します。
2. 次の図に示すように、**[設定]** で **[ピアリング]** を選択し、**[+ 追加]** を選択します。

    ![ピアリングを作成する](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. 次の図に示すように、情報を入力または選択し、**[OK]** を選択します。 *myVirtualNetwork2* 仮想ネットワークを選択するには、**[仮想ネットワーク]**、*[myVirtualNetwork2]* の順に選択します。

    ![ピアリングの設定](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    次の図に示すように、**[ピアリング状態]** は "*開始済み*" です。

    ![ピアリングの状態](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    状態が表示されない場合は、ブラウザーを更新します。

4. *myVirtualNetwork2* 仮想ネットワークを検索します。 検索結果で返された場合は、それを選択します。
5. 次のように値を変更して手順 1. ～ 3. を繰り返し、**[OK]** を選択します。
    - **[名前]**: *myVirtualNetwork2-myVirtualNetwork1*
    - **[仮想ネットワーク]**: *myVirtualNetwork1*

    **[ピアリング状態]** は "*接続済み*" です。 Azure によって、*myVirtualNetwork2-myVirtualNetwork1* ピアリングのピアリング状態も "*開始済み*" から "*接続済み*" に変更されました。 両方の仮想ネットワークのピアリング状態が "*接続済み*" になるまで、仮想ネットワーク ピアリングは完全には確立されません。 

ピアリングは 2 つの仮想ネットワークの間で行われますが、推移的ではありません。 このため、たとえば *myVirtualNetwork2* と *myVirtualNetwork3* をピアリングしたい場合は、仮想ネットワーク *myVirtualNetwork2* と *myVirtualNetwork3* の間に追加ピアリングを作成する必要があります。 *myVirtualNetwork1* が *myVirtualNetwork2* とピアリングされていても、*myVirtualNetwork1* が *myVirtualNetwork3* ともピアリングされている場合は、*myVirtualNetwork1* のリソースがアクセスできるのは *myVirtualNetwork3* のリソースだけです。 

運用仮想ネットワークをピアリングする前に、[ピアリング概要](virtual-network-peering-overview.md)、[ピアリングの管理](virtual-network-manage-peering.md)、および[仮想ネットワークの制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)について十分に理解しておくことをお勧めします。 この記事は、同じサブスクリプションや同じ場所にある 2 つの仮想ネットワーク間のピアリングについて説明していますが、[異なるリージョン](#register)や[異なる Azure サブスクリプション](create-peering-different-subscriptions.md#portal)の仮想ネットワークをピアリングすることもできます。 [ハブとスポークのネットワーク設計](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)をピアリングで作成することもできます。

## <a name="test-peering"></a>ピアリングをテストする

異なる仮想ネットワーク内にある仮想マシン間のピアリングを使用したネットワーク通信をテストするには、仮想マシンを各サブネットにデプロイし、仮想マシン間で通信します。 

### <a name="create-virtual-machines"></a>仮想マシンを作成する

各仮想ネットワークに仮想マシンを作成して、後の手順でそれらの間の通信を検証できるようにします。

### <a name="create-virtual-machines"></a>仮想マシンを作成する

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[コンピューティング]**、**[Windows Server 2016 Datacenter]** の順に選択します。 別のオペレーティング システムを選択することもできますが、以降の手順では、**[Windows Server 2016 Datacenter]** を選択したという前提で説明します。 
3. **[基本]** で以下の情報を選択または入力し、**[OK]** を選択します。
    - **[名前]**: *myVm1*
    - **[リソース グループ]**: **[既存のものを使用]** を選択し、*[myResourceGroup]* を選択します。
    - **[場所]**: *[米国東部]* を選択します。

    入力した**ユーザー名**と**パスワード**は、以降の手順で使用されます。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。 選択した**場所**および**サブスクリプション**は、仮想ネットワークの場所およびサブスクリプションと同じである必要があります。 仮想ネットワークが作成されているリソース グループと同じリソース グループを選択することは必須ではありませんが、この記事では同じリソース グループを選択しています。
4. **[サイズの選択]** で、VM サイズを選択します。
5. **[設定]** で以下の情報を選択または入力し、**[OK]** を選択します。
    - **[仮想ネットワーク]**: **[myVirtualNetwork1]** が選択されていることを確認します。 選択されていない場合は、**[仮想ネットワーク]** を選択し、**[仮想ネットワークの選択]** で **[myVirtualNetwork1]** を選択します。
    - **[サブネット]**: **[Subnet1]** が選択されていることを確認します。 選択されていない場合は、**[サブネット]** を選択し、次の図に示すように **[サブネットの選択]** で **[Subnet1]** を選択します。
    
        ![仮想マシンの設定](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. **[概要]** の **[作成]** で **[作成]** を選択して、仮想マシンのデプロイを開始します。
7. 手順 1. ～6. を繰り返しますが、今回は仮想マシンの **[名前]** に「*myVm2*」と入力し、**[仮想ネットワーク]** で *[myVirtualNetwork2]* を選択します。

*myVirtualNetwork1* および *myVirtualNetwork2* 仮想ネットワークの *Subnet1* で使用できる最初の IP アドレスが、*myVm1* 仮想マシンでは *10.0.0.4*、*myVm2* 仮想マシンでは 10.1.0.4 であるため、Azure では、その IP アドレスが、それぞれの仮想マシンのプライベート IP アドレスとして割り当てられます。

仮想マシンの作成には数分かかります。 両方の仮想マシンの作成が完了するまで、以降の手順に進まないでください。

### <a name="test-virtual-machine-communication"></a>仮想マシンの通信をテストする

1. ポータルの上部にある *[検索]* ボックスで、「*myVm1*」の入力を開始します。 検索結果に **myVm1** が表示されたら、それを選択します。
2. 次の図に示すように、**[接続]** を選択して *myVm1* 仮想マシンへのリモート デスクトップ接続を作成します。

    ![仮想マシンへの接続](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. VM に接続するには、ダウンロードした RDP ファイルを開きます。 メッセージが表示されたら、**[Connect]** を選択します。
4. 仮想マシンの作成時に指定したユーザー名とパスワードを入力し (仮想マシンの作成時に入力した資格情報を指定するために、**[More choices]\(その他の選択肢\)**、**[Use a different account]\(別のアカウントを使用する\)** の選択が必要になる場合があります)、**[OK]** を選択します。
5. サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** を選択して、接続処理を続行します。
6. 後の手順で、*myVmWeb* 仮想マシンから *myVm2* 仮想マシンと通信するために ping を使用します。 ping は ICMP を使用しますが、既定では ICMP は Windows ファイアウォールで拒否されます。 コマンド プロンプトから次のコマンドを入力して、Windows ファイアウォールで ICMP を有効にします。

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    この記事ではテスト目的で ping を使用していますが、運用環境のデプロイでは Windows ファイアウォールで ICMP を許可することは推奨されません。

7. *myVm2* 仮想マシンに接続するには、*myVm1* 仮想マシン上でコマンド プロンプトから以下のコマンドを入力します。

    ```
    mstsc /v:10.1.0.4
    ```
    
8. *myVm1* で ping を有効にしたため、そのマシンに対して IP アドレスで ping を実行できます。

    ```
    ping 10.0.0.4
    ```
    
    4 つの応答を受信します。 仮想マシンの IP アドレスではなく名前 (*myVm1*) で ping を実行すると、ping に失敗します。これは *myVm1* が不明なホスト名であるためです。 Azure の既定の名前解決は、同じ仮想ネットワークの仮想マシン間で機能しますが、異なる仮想ネットワークの仮想マシン間では機能しません。 仮想ネットワークの枠を越えて名前を解決するには、[独自の DNS サーバーをデプロイ](virtual-networks-name-resolution-for-vms-and-role-instances.md)するか、[Azure DNS プライベート ドメイン](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を使用する必要があります。

9. *myVm1* と *myVm2* の両方への RDP セッションを切断します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループとそれに含まれるすべてのリソースが不要になったら、それらを削除します。 

1. ポータル上部の **[検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **[myResourceGroup]** が表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[TYPE THE RESOURCE GROUP NAME:]\(リソース グループ名を入力してください:\)** に「*myResourceGroup*」と入力し、**[削除]** を選択します。

**<a name="register"></a>グローバル仮想ネットワーク ピアリング プレビューへの登録**

同一リージョン内の仮想ネットワーク ピアリングの機能は一般公開されています。 異なるリージョン内の複数の仮想ネットワークのピアリングは、現在プレビュー段階です。 使用可能なリージョンについては、[仮想ネットワークの更新情報](https://azure.microsoft.com/updates/?product=virtual-network)を参照してください。 リージョンの枠を越えて仮想ネットワークをピアリングするには、まずプレビューに登録する必要があります。 ポータルを使って登録することはできません。登録には [PowerShell](tutorial-connect-virtual-networks-powershell.md#register) または [Azure CLI](tutorial-connect-virtual-networks-cli.md#register) を使用します。 機能登録の前に異なるリージョンの複数の仮想ネットワークをピアリングしようとすると、ピアリングは失敗します。

## <a name="next-steps"></a>次の手順

この記事では、仮想ネットワーク ピアリングを使用して 2 つのネットワークを接続する方法を学習しました。

次はユーザーのコンピューターを VPN 経由で仮想ネットワークに接続し、仮想ネットワーク、またはピアリングされた仮想ネットワークのリソースを操作します。

> [!div class="nextstepaction"]
> [仮想ネットワークへのユーザーのコンピューターの接続](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
