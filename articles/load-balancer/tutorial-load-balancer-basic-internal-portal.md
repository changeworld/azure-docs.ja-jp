---
title: 'チュートリアル: パブリック Basic Load Balancer を作成する - Azure Portal | Microsoft Docs'
description: このチュートリアルでは、Azure Portal を使用して、内部 Basic Load Balancer を作成する方法を示します。
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: c0d19c53a0bd217935a494dfb4affbaa85062247
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097480"
---
# <a name="tutorial-load-balance-internal-traffic-with-basic-load-balancer-to-vms-using-the-azure-portal"></a>チュートリアル: 内部トラフィックを Basic Load Balancer によって、Azure Portal を使用する VM に負荷分散する

負荷分散では、着信要求を複数の仮想マシンに分散させることで、より高いレベルの可用性とスケールを実現します。 Azure Portal を使用して、内部トラフィックを Basic Load Balancer によって仮想マシンに負荷分散できます。 このチュートリアルでは、ネットワーク リソース、バックエンド サーバー、および内部 Basic Load Balancer を作成する方法を示します。

好みに応じて、[Azure CLI](load-balancer-get-started-ilb-arm-cli.md) または [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) を使ってこのチュートリアルの手順を実行することもできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成
1. 画面の左上で **[新規]** > **[ネットワーク]** > **[仮想ネットワーク]** の順にクリックし、仮想ネットワークの以下の値を入力します。
    - *myVnet* - 仮想ネットワークの名前。
    - *myResourceGroupILB* - 既存のリソース グループの名前
    - *myBackendSubnet* - サブネット名。
2. **[作成]** をクリックして、仮想ネットワークを作成します。

![ロード バランサーの作成](./media/tutorial-load-balancer-basic-internal-portal/1-load-balancer.png)

## <a name="create-a-basic-load-balancer"></a>Basic Load Balancer を作成する
ポータルを使用して内部 Basic Load Balancer を作成します。

1. 画面の左上で、**[リソースの作成]** > **[ネットワーキング]** > **[ロード バランサー]** の順にクリックします。
2. **[Create a load balancer]\(ロード バランサーの作成\)** ページで、ロード バランサーの以下の値を入力します。
    - *myLoadBalancer* - ロード バランサーの名前。
    - **Internal** - ロード バランサーの種類。
    - **Basic** - SKU バージョン。
    - **10.1.0.7** - 静的プライベート IP アドレス。
    - *myVNet* - 既存のネットワークの一覧から選択する仮想ネットワーク。
    - *mySubnet* - 既存のサブネットの一覧から選択するサブネット。
    - *myResourceGroupILB* - 作成する新しいリソース グループの名前。
3. **[作成]** をクリックして、ロード バランサーを作成します。
   
    ## <a name="create-backend-servers"></a>バックエンド サーバーの作成

このセクションでは、Basic Load Balancer のバックエンド プール用に 2 つの仮想マシンを作成してから、ロード バランサーをテストするために仮想マシンに IIS をインストールします。

### <a name="create-virtual-machines"></a>仮想マシンを作成する

1. 画面の左上で **[リソースの作成]** > **[コンピューティング]** > **[Windows Server 2016 Datacenter]** の順にクリックし、仮想マシンの以下の値を入力します。
    - *myVM1* - 仮想マシンの名前です。        
    - *azureuser* - 管理者のユーザー名です。   
    - *myResourceGroupILB* - **リソース グループ**については **[既存のものを使用]** を選択し、*[myResourceGroupILB]* を選択します。
2. Click **OK**.
3. 仮想マシンのサイズとして **[DS1_V2]** を選択し、**[選択]** をクリックします。
4. VM の設定に以下の値を入力します。
    - *myAvailabilitySet* - 作成する新しい可用性セットの名前。
    -  *myVNet* - 仮想ネットワークとしてこれが選択されていることを確認します。
    - *myBackendSubnet* - サブネットとしてこれが選択されていることを確認します。
5. **[ネットワーク セキュリティ グループ]** で **[Advanced]\(高度\)** を選択します。 次に、**[ネットワーク セキュリティ グループ (ファイアウォール)]** で **[なし]** を選択します。
5. **[無効]** をクリックして、ブート診断を無効にします。
6. **[OK]** をクリックし、概要ページの設定を確認して、**[作成]** をクリックします。
7. 手順 1. から手順 6. を使用して *VM2* という名前の 2 つ目の VM を作成します。可用性セットとして *myAvailabilityset*、仮想ネットワークとして *myVnet*、サブネットとして *myBackendSubnet*、**[ネットワーク セキュリティ グループ (ファイアウォール)]** として **[なし]** を指定します。 

### <a name="install-iis-and-customize-the-default-web-page"></a>IIS をインストールして既定の Web ページをカスタマイズする

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で *[myResourceGroupILB]* リソース グループにある **[myVM1]** をクリックします。
2. **[概要]** ページで **[接続]** をクリックして、RDP で VM に接続します。
3. VM にログインします。
4. サーバーのデスクトップで、**[Windows 管理ツール]**>**[サーバー マネージャー]** の順に移動します。
5. VM1 で Windows PowerShell を起動し、次のコマンドを使用して IIS サーバーのインストールと既定の htm ファイルの更新を行います。
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
5. *myVM1* で RDP 接続を閉じます。
6. *myVM2* で手順 1. ～ 5. を繰り返し、IIS をインストールして既定の Web ページをカスタマイズします。

## <a name="create-basic-load-balancer-resources"></a>Basic Load Balancer のリソースを作成する

このセクションでは、バックエンド アドレス プールと正常性プローブのロード バランサー設定を構成し、ロード バランサーと NAT 規則を指定します。


### <a name="create-a-backend-address-pool"></a>バックエンド アドレス プールの作成

トラフィックを VM に分散するには、バックエンド アドレス プールに、ロード バランサーに接続される仮想 NIC の IP アドレスを含めます。 バックエンド アドレス プール *myBackendPool* を作成し、*VM1* および *VM2* を含めます。

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で **[myLoadBalancer]** をクリックします。
2. **[設定]** で **[バックエンド プール]** をクリックし、**[追加]** をクリックします。
3. **[バックエンド プールの追加]** ページで、以下の操作を行います。
    - バックエンド プールの名前として、[名前] に「*myBackEndPool*」と入力します。
    - **[関連付け先]** のドロップダウン メニューで **[可用性セット]** をクリックします。
    - **[可用性セット]** で **[myAvailabilitySet]** をクリックします。
    - **[ターゲット ネットワーク IP 構成の追加]** をクリックして、作成した各仮想マシン (*myVM1* & *myVM2*) をバックエンド プールに追加します。
    - Click **OK**.

        ![バックエンド アドレス プールへの追加 ](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)

3. ロード バランサーのバックエンド プールの設定に、**VM1** と **VM2** の両方の VM が表示されていることを確認します。

### <a name="create-a-health-probe"></a>正常性プローブの作成

Basic Load Balancer でアプリの状態を監視するには、正常性プローブを使用します。 正常性プローブは、ロード バランサーのローテーションに含める VM を、正常性チェックへの応答に基づいて動的に追加したり削除したりする働きをします。 正常性プローブ *myHealthProbe* を作成し、VM の正常性を監視します。

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で **[myLoadBalancer]** をクリックします。
2. **[設定]** で **[正常性プローブ]** をクリックし、**[追加]** をクリックします。
3. 正常性プローブの作成では、以下の値を使用します。
    - *myHealthProbe* - 正常性プローブの名前。
    - **HTTP** - プロトコルの種類。
    - *80* - ポート番号。
    - *15* - プローブの試行の**間隔**を示す秒数。
    - *2* - **異常しきい値**またはプローブの連続する失敗の回数。この回数を超えると、VM は異常と見なされます。
4. Click **OK**.

   ![プローブの追加](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則を作成する

ロード バランサー規則の目的は、一連の VM に対するトラフィックの分散方法を定義することです。 着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを、必要な発信元ポートと宛先ポートと共に定義します。 ロード バランサー規則 *myLoadBalancerRuleWeb* を作成して、フロントエンド *LoadBalancerFrontEnd* のポート 80 をリッスンし、同じポート 80 を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール *myBackEndPool* に送信します。 

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
    
    ![負荷分散規則の追加](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="create-a-virtual-machine-to-test-the-load-balancer"></a>仮想マシンを作成してロード バランサーをテストする
内部ロード バランサーをテストするには、バックエンド サーバーの VM と同じ仮想ネットワークに配置される仮想マシンを作成する必要があります。
1. 画面の左上で **[リソースの作成]** > **[コンピューティング]** > **[Windows Server 2016 Datacenter]** の順にクリックし、仮想マシンの以下の値を入力します。
    - *myVMTest* - 仮想マシンの名前です。        
    - *myResourceGroupILB* - **リソース グループ**については **[既存のものを使用]** を選択し、*[myResourceGroupILB]* を選択します。
2. Click **OK**.
3. 仮想マシンのサイズとして **[DS1_V2]** を選択し、**[選択]** をクリックします。
4. VM の設定に以下の値を入力します。
    -  *myVNet* - 仮想ネットワークとしてこれが選択されていることを確認します。
    - *myBackendSubnet* - サブネットとしてこれが選択されていることを確認します。
5. **[無効]** をクリックして、ブート診断を無効にします。
6. **[OK]** をクリックし、概要ページの設定を確認して、**[作成]** をクリックします。

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする
1. Azure Portal の **[概要]** 画面で、ロード バランサーのプライベート IP アドレスを入手します。 そのためには、次の手順を実行します: a. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で **[myLoadBalancer]** をクリックします。
    b. **[概要]** の詳細ページで、プライベート IP アドレスをコピーします (この例では 10.1.0.7 です)。

2. 次のように *myVMTest* へのリモート接続を作成します: a. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で *[myResourceGroupILB]* リソース グループにある **[myVMTest]** をクリックします。
2. **[概要]** ページで、**[接続]** をクリックして VM とのリモート セッションを開始します。
3. *myVMTest* にログインします。
3. *myVMTest* 内で、ブラウザーのアドレス バーにプライベート IP アドレスを貼り付けます。 IIS Web サーバーの既定のページがブラウザーに表示されます。

      ![IIS Web サーバー](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png)

アプリを実行している両方の VM にわたってロード バランサーがトラフィックを分散させていることを確認するには、Web ブラウザーを強制的に最新の情報に更新します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、ロード バランサー、および関連するすべてのリソースは、不要になったら削除します。 これを行うには、ロード バランサーを含むリソース グループを選択し、**[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、リソース グループ、ネットワーク リソース、およびバックエンド サーバーを作成しました。 次に、それらのリソースを使用して内部ロード バランサーを作成し、内部トラフィックを VM に負荷分散しました。 次に、[可用性ゾーン間で VM の負荷分散を行う](tutorial-load-balancer-standard-public-zone-redundant-portal.md)方法を学びます
