---
title: ゾーン内のロード バランサー VM - Azure Portal | Microsoft Docs
description: Azure Portal を使用して、ゾーン フロントエンドを使った Standard Load Balancer を作成し、可用性ゾーン内の VM の負荷を分散します。
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/18/2018
ms.author: kumud
ms.openlocfilehash: 41a33436cb0d2c4c2bbfef4888bb704c62e2b91e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
# <a name="load-balance-vms-within-an-availability-zone-with-a-standard-load-balancer-using-the-azure-portal"></a>Azure Portal を使用した Standard Load Balancer による可用性ゾーン内での VM の負荷分散

この記事では、Azure Portal を使用し、パブリック IP 標準アドレスを使ったゾーン フロントエンドでパブリック [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) を作成する手順について説明します。 このシナリオでは、フロントエンド インスタンスとバックエンド インスタンスに特定のゾーンを指定して、特定のゾーンに合わせてデータ パスとリソースを調整することができます。
可用性ゾーンと Standard Load Balancer の使用方法の詳細については、「[Standard Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)」を参照してください。

好みに応じて、[Azure CLI](load-balancer-standard-public-zonal-cli.md) を使ってこのチュートリアルの手順を実行することもできます。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure Portal ([http://portal.azure.com](http://portal.azure.com)) にログインします。

## <a name="create-a-public-standard-load-balancer"></a>パブリック Standard Load Balancer を作成する

Standard Load Balancer では、Standard パブリック IP アドレスだけがサポートされています。 ロード バランサーの作成時に新しいパブリック IP を作成すると、自動的に Standard SKU バージョンとして構成され、自動的にゾーン冗長になります。

1. 画面の左上で、**[リソースの作成]** > **[ネットワーキング]** > **[ロード バランサー]** の順にクリックします。
2. **[Create a load balancer]\(ロード バランサーの作成\)** ページで、ロード バランサーの以下の値を入力します。
    - *myLoadBalancer* - ロード バランサーの名前。
    - **パブリック** - ロード バランサーの種類。
     - *myPublicIPZonal* - 作成する新しいパブリック IP アドレス。 この場合、**[パブリック IP アドレスの選択]** をクリックし、**[新規作成]** をクリックします。 [名前] に「*myPublicIP*」と入力します。SKU の既定は Standard です。**[可用性ゾーン]** には **[Zone 1]\(ゾーン 1\)** を選択します。
    - *myResourceGroupZLB* - 作成する新しいリソース グループの名前。
    - **westeurope** - 場所。
3. **[作成]** をクリックして、ロード バランサーを作成します。
   
    ![Azure Portal を使用してゾーン Load Balancer Standard を作成する](./media/tutorial-load-balancer-standard-zonal-portal/create-load-balancer-zonal-frontend.png)


## <a name="create-backend-servers"></a>バックエンド サーバーの作成

このセクションでは、仮想ネットワークを作成し、リージョンでロード バランサーのバックエンド プールに追加される 2 つの仮想マシンを同じゾーン (つまり、ゾーン 1) に作成します。次に、ゾーン冗長ロード バランサーをテストするために仮想マシンに IIS をインストールします。 今後、1 つの VM に障害が発生すると、同じゾーン内の VM の正常性プローブは失敗し、トラフィックは同じゾーン内の他の VM によって引き続き処理されます。

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成
1. 画面の左上で **[リソースの作成]** > **[ネットワーク]** > **[仮想ネットワーク]** の順にクリックし、仮想ネットワークの以下の値を入力します。
    - *myVnet* - 仮想ネットワークの名前。
    - *myResourceGroupZLB* - 既存のリソース グループの名前。
    - *myBackendSubnet* - サブネット名。
2. **[作成]** をクリックして、仮想ネットワークを作成します。

    ![仮想ネットワークの作成](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

1. 画面の左上にある **[リソースの作成]** をクリックし、検索ボックスに「*ネットワーク セキュリティ グループ*」と入力し、ネットワーク セキュリティ グループ ページで **[作成]** をクリックします。
2. [ネットワーク セキュリティ グループの作成] で次の値を入力します。
    - *myNetworkSecurityGroup* - ネットワーク セキュリティ グループの名前。
    - *myResourceGroupLBAZ* - 既存のリソース グループの名前。
   
    ![仮想ネットワークの作成](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>NSG ルールを作成する

このセクションでは、Azure Portal で HTTP と RDP を使用する受信接続を許可するための NSG 規則を作成します。

1. Azure Portal の左側のメニューで **[すべてのリソース]** をクリックし、**[myResourceGroupZLB]** リソース グループにある **[myNetworkSecurityGroup]** を検索してクリックします。
2. **[設定]** で **[受信セキュリティ規則]** をクリックし、**[追加]** をクリックします。
3. *myHTTPRule* という名前の受信セキュリティ規則のために以下の値を入力し、ポート 80 を使用する受信 HTTP 接続を許可します。
    - "*サービス タグ*" - **ソース**。
    - "*インターネット*" - **ソース サービス タグ**
    - *80* - **宛先ポート範囲**
    - *TCP* - **プロトコル**
    - "*許可*" - **アクション**
    - *100* - **優先度**
    - *myHTTPRule* - **名前**
    - *Allow HTTP* - **説明**
4. Click **OK**.
 
 ![仮想ネットワークの作成](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)

5. 手順 2. から 4. を繰り返して、*myRDPRule* という名前で規則をもう 1 つ作成し、ポート 3389 を使用する受信 RDP 接続を許可します。設定には以下の値を使用します。
    - "*サービス タグ*" - **ソース**。
    - "*インターネット*" - **ソース サービス タグ**
    - *3389* - **宛先ポート範囲**
    - *TCP* - **プロトコル**
    - "*許可*" - **アクション**
    - *200* - **優先度**
    - *myRDPRule* - 名前
    - "*RDP を許可する*" - 説明

    ![RDP 規則を作成する](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>仮想マシンを作成する

1. 画面の左上で **[リソースの作成]** > **[コンピューティング]** > **[Windows Server 2016 Datacenter]** の順にクリックし、仮想マシンの以下の値を入力します。
    - *myVM1* - 仮想マシンの名前です。        
    - *azureuser* - 管理者のユーザー名です。    
    - *myResourceGroupZLB* - **リソース グループ**。**[既存のものを使用]** を選択し、*[myResourceGroupZLB]* を選択します。
2. Click **OK**.
3. 仮想マシンのサイズとして **[DS1_V2]** を選択し、**[選択]** をクリックします。
4. VM の設定に以下の値を入力します。
    - *ゾーン 1* - VM を配置する可用性ゾーン。
    -  *myVNet* - 仮想ネットワークとしてこれが選択されていることを確認します。
    - *myVM1PIP* - 作成する標準パブリック IP アドレス。 *[新規作成]* をクリックし、[名前] に「*myVM1PIP*」と入力して、[ゾーン] に **[1]** を選択します。 IP アドレスの SKU は、既定では Standard です。
    - *myBackendSubnet* - サブネットとしてこれが選択されていることを確認します。
    - *myNetworkSecurityGroup* - 既存のネットワーク セキュリティ グループ (ファイアウォール) の名前。
5. **[無効]** をクリックして、ブート診断を無効にします。
6. **[OK]** をクリックし、概要ページの設定を確認して、**[作成]** をクリックします。
7. 手順 1 ～ 6 を使用して、ゾーン 1 に *myVM2* という 2 つ目の VM、仮想ネットワークとして *myVnet*、標準パブリック IP アドレスとして *myVM2PIP*、サブネットとして *myBackendSubnet*、ネットワーク セキュリティ グループとして **myNetworkSecurityGroup* を作成します。

    ![RDP 規則を作成する](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>VM に IIS をインストールする

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で *[myResourceGroupZLB]* リソース グループにある **[myVM1]** をクリックします。
2. **[概要]** ページで **[接続]** をクリックして、RDP で VM に接続します。
3. VM の作成時に指定したユーザー名とパスワードを指定して VM にログインし (VM の作成時に入力した資格情報を指定するために、**[その他]**、**[別のアカウントを使う]** の選択が必要になる場合があります)、**[OK]** を選択します。 サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** を選択して、接続処理を続行します。
4. サーバーのデスクトップで、**[Windows 管理ツール]**>**[Windows PowerShell]** の順に移動します。
6. PowerShell ウィンドウで、以下のコマンドを実行して IIS サーバーをインストールし、既定の .htm ファイルを削除して、VM の名前を表示する既定の.htm と同名の新しいファイルを追加します。

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
   ```
8. *myVM1* で RDP セッションを閉じます。
9. 手順 1 ～ 8 を繰り返して、*myVM2* に IIS をインストールします。

## <a name="create-load-balancer-resources"></a>ロード バランサーのリソースを作成する

このセクションでは、バックエンド アドレス プールと正常性プローブのロード バランサー設定を構成し、ロード バランサーと NAT 規則を指定します。


### <a name="create-a-backend-address-pool"></a>バックエンド アドレス プールの作成

トラフィックを VM に分散するには、バックエンド アドレス プールに、ロード バランサーに接続される仮想 NIC の IP アドレスを含めます。 バックエンド アドレス プール *myBackendPool* を作成し、*VM1* および *VM2* を含めます。

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で **[myLoadBalancer]* をクリックします。
2. **[設定]** で **[バックエンド プール]** をクリックし、**[追加]** をクリックします。
3. **[バックエンド プールの追加]** ページで、以下の操作を行います。
    - バックエンド プールの名前として、[名前] に「*myBackEndPool*」と入力します。
    - **[仮想ネットワーク]** の場合は、ドロップダウン メニューの *[myVNet]* をクリックします。
    - **[仮想マシン]** および **[IP アドレス]** には、*myVM1* および *myVM2* と、これらに該当するパブリック IP アドレスを追加します。
4. **[追加]** をクリックします。
5. ロード バランサーのバックエンド プールの設定に、**myVM1** と **myVM2** の両方の VM が表示されていることを確認します。
 
    ![バックエンド プールの作成](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

### <a name="create-a-health-probe"></a>正常性プローブの作成

ロード バランサーでアプリの状態を監視するには、正常性プローブを使用します。 正常性プローブは、ロード バランサーのローテーションに含める VM を、正常性チェックへの応答に基づいて動的に追加したり削除したりする働きをします。 正常性プローブ *myHealthProbe* を作成し、VM の正常性を監視します。

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で **[myLoadBalancer]** をクリックします。
2. **[設定]** で **[正常性プローブ]** をクリックし、**[追加]** をクリックします。
3. 正常性プローブの作成では、以下の値を使用します。
    - *myHealthProbe* - 正常性プローブの名前。
    - **HTTP** - プロトコルの種類。
    - *80* - ポート番号。
    - *15* - プローブの試行の**間隔**を示す秒数。
    - *2* - **異常しきい値**またはプローブの連続する失敗の回数。この回数を超えると、VM は異常と見なされます。
4. Click **OK**.

   ![プローブの追加](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成

ロード バランサー規則の目的は、一連の VM に対するトラフィックの分散方法を定義することです。 着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを、必要な発信元ポートと宛先ポートと共に定義します。 ロード バランサー規則 *myLoadBalancerRuleWeb* を作成して、フロントエンド *FrontendLoadBalancer* のポート 80 をリッスンし、同じポート 80 を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール *myBackEndPool* に送信します。 

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で **[myLoadBalancer]** をクリックします。
2. **[設定]** で **[負荷分散規則]** をクリックし、**[追加]** をクリックします。
3. 負荷分散規則の構成には、以下の値を使用します。
    - *myHTTPRule* - 負荷分散規則の名前。
    - **TCP** - プロトコルの種類。
    - *80* - ポート番号。
    - *80* - バックエンド ポート。
    - *myBackendPool* - バックエンド プールの名前。
    - *myHealthProbe* - 正常性プローブの名前。
4. Click **OK**.
    
    ![負荷分散規則の追加](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする
1. **[概要]** 画面で、ロード バランサーのパブリック IP アドレスを見つけます。 **[すべてのリソース]** をクリックし、**[myPublicIP]** をクリックします。 

2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 Web サーバー ページの名前を含む既定のページが、ブラウザーに表示されます。

      ![IIS Web サーバー](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. ロード バランサーが稼働中であることを確認するには、表示されている VM を強制停止し、ブラウザーを更新して、ブラウザーに他のサーバー名が表示されることを確認します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、ロード バランサー、および関連するすべてのリソースは、不要になったら削除します。 これを行うには、ロード バランサーを含むリソース グループを選択し、**[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

- [Standard Load Balancer](load-balancer-standard-overview.md) の詳細を確認する。
- [可用性ゾーン間での VM の負荷分散](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
