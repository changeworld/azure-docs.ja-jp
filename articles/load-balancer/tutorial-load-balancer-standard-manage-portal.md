---
title: 'チュートリアル: Standard Load Balancer を作成して管理する - Azure portal | Microsoft Docs'
description: このチュートリアルでは、Azure Portal を使用して Standard Load Balancer を作成して管理する方法について説明します。
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create and Standard Load balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/18
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: b8fae8e0967f3a98f3ae0a41fad0e408b8310fa4
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757745"
---
# <a name="tutorial-create-and-manage-standard-load-balancer-using-the-azure-portal"></a>チュートリアル: Azure portal を使用して Standard Load Balancer を作成して管理する

負荷分散では、着信要求を複数の仮想マシンに分散させることで、より高いレベルの可用性とスケールを実現します。 このチュートリアルでは、トラフィックを分散し高可用性を提供する、Azure Standard Load Balancer のさまざまなコンポーネントについて説明します。 学習内容は次のとおりです。


> [!div class="checklist"]
> * Azure Load Balancer を作成する
> * ロード バランサーの正常性プローブの作成
> * ロード バランサーのトラフィック ルールを作成する
> * 仮想マシンを作成し、IIS サーバーをインストールする
> * 仮想マシンをロード バランサーに接続する
> * 動作中のロード バランサーを表示する
> * ロード バランサーに VM を追加または削除する



Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

## <a name="log-in-to-azure"></a>Azure にログインする

Azure Portal ([http://portal.azure.com](http://portal.azure.com)) にログインします。

## <a name="create-a-standard-load-balancer"></a>Standard Load Balancer を作成する

このセクションでは、仮想マシンの負荷分散に役立つパブリック ロード バランサーを作成します。 Standard Load Balancer では、Standard パブリック IP アドレスだけがサポートされています。 Standard Load Balancer を作成するときに、Standard Load Balancer のフロントエンド (既定では *LoadBalancerFrontend* という名前) として構成される新しい Standard パブリック IP アドレスも作成する必要があります。 

1. 画面の左上で、**[リソースの作成]** > **[ネットワーキング]** > **[ロード バランサー]** の順にクリックします。
2. **[Create a load balancer]\(ロード バランサーの作成\)** ページで、ロード バランサーの以下の値を入力します。
    - *myLoadBalancer* - ロード バランサーの名前。
    - **パブリック** - ロード バランサーの種類。
    - **Standard** - ロード バランサーの SKU バージョン。
     - *myPublicIP* - 作成する**新しい**パブリック IP。
    - *myResourceGroupSLB* - 作成することを選択した**新しい**リソース グループの名前。
    - **westeurope** - 場所。
3. **[作成]** をクリックして、ロード バランサーを作成します。

![ロード バランサーの作成](./media/load-balancer-standard-public-portal/1a-load-balancer.png)
   
## <a name="create-load-balancer-resources"></a>ロード バランサーのリソースを作成する

このセクションでは、バックエンド アドレス プールと正常性プローブのロード バランサー設定を構成し、ロード バランサーの規則を指定します。

### <a name="create-a-backend-address-pool"></a>バックエンド アドレス プールの作成

トラフィックを VM に分散するには、バックエンド アドレス プールに、ロード バランサーに接続される仮想 NIC の IP アドレスを含めます。 バックエンド アドレス プール *myBackendPool* を作成し、*VM1* および *VM2* を含めます。

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で **[myLoadBalancer]** をクリックします。
2. **[設定]** で **[バックエンド プール]** をクリックし、**[追加]** をクリックします。
3. **[バックエンド プールの追加]** ページで、バックエンド プールの名前として「*myBackEndPool*」と入力し、**[OK]** をクリックします。

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

   ![プローブの追加](./media/load-balancer-standard-public-portal/4-load-balancer-probes.png)

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
    
## <a name="create-backend-servers"></a>バックエンド サーバーの作成

このセクションでは、仮想ネットワークを作成し、ロード バランサーのバックエンド プール用に 3 台の仮想マシンを作成して、ロード バランサーをテストするために仮想マシンに IIS をインストールします。

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成
1. 画面の左上で **[新規]** > **[ネットワーク]** > **[仮想ネットワーク]** の順にクリックし、仮想ネットワークの以下の値を入力します。
    - *myVnet* - 仮想ネットワークの名前。
    - *myResourceGroupSLB* - 既存のリソース グループの名前。
    - *myBackendSubnet* - サブネット名。
2. **[作成]** をクリックして、仮想ネットワークを作成します。

    ![仮想ネットワークの作成](./media/load-balancer-standard-public-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>仮想マシンを作成する

1. 画面の左上で **[新規]** > **[コンピューティング]** > **[Windows Server 2016 Datacenter]** の順にクリックし、仮想マシンの以下の値を入力します。
    - *myVM1* - 仮想マシンの名前です。        
    - *azureuser* - 管理者のユーザー名です。    
    - *myResourceGroupSLB* - **リソース グループ**。**[既存のものを使用]** を選択し、*[myResourceGroupSLB]* を選択します。
2. Click **OK**.
3. 仮想マシンのサイズとして **[DS1_V2]** を選択し、**[選択]** をクリックします。
4. VM の設定に以下の値を入力します。
    -  *myVNet* - 仮想ネットワークとしてこれが選択されていることを確認します。
    - *myBackendSubnet* - サブネットとしてこれが選択されていることを確認します。
    - *myNetworkSecurityGroup* - 作成する必要がある新しいネットワーク セキュリティ グループ (ファイアウォール) の名前。
5. **[無効]** をクリックして、ブート診断を無効にします。
6. **[OK]** をクリックし、概要ページの設定を確認して、**[作成]** をクリックします。
7. 手順 1 から 6 を使用して、名前が *VM2* および *VM3*、仮想ネットワークが *myVnet*、サブネットが *myBackendSubnet*、ネットワーク セキュリティ グループが *myNetworkSecurityGroup* である、さらに 2 つの VM を作成します。 

### <a name="create-nsg-rules"></a>NSG ルールを作成する

このセクションでは、HTTP と RDP を使用する受信接続を許可するための NSG ルールを作成します。

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で **[myResourceGroupSLB]** リソース グループにある **[myNetworkSecurityGroup]** をクリックします。
2. **[設定]** で **[受信セキュリティ規則]** をクリックし、**[追加]** をクリックします。
3. *myHTTPRule* という名前の受信セキュリティ規則のために以下の値を入力し、ポート 80 を使用する受信 HTTP 接続を許可します。
    - "*サービス タグ*" - **ソース**。
    - "*インターネット*" - **ソース サービス タグ**
    - *80* - **宛先ポート範囲**
    - *TCP* - **プロトコル**
    - "*許可*" - **アクション**
    - *100* - **優先度**
    - *myHTTPRule* - 名前
    - "*HTTP を許可する*" - 説明
4. Click **OK**.
 
 ![仮想ネットワークの作成](./media/load-balancer-standard-public-portal/8-load-balancer-nsg-rules.png)
5. 手順 2. から 4. を繰り返して、*myRDPRule* という名前で規則をもう 1 つ作成し、ポート 3389 を使用する受信 RDP 接続を許可します。設定には以下の値を使用します。
    - "*サービス タグ*" - **ソース**。
    - "*インターネット*" - **ソース サービス タグ**
    - *3389* - **宛先ポート範囲**
    - *TCP* - **プロトコル**
    - "*許可*" - **アクション**
    - *200* - **優先度**
    - *myRDPRule* - 名前
    - "*RDP を許可する*" - 説明

### <a name="install-iis-on-vms"></a>VM に IIS をインストールする

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で *myResourceGroupSLB* リソース グループにある **myVM1** をクリックします。
2. **[概要]** ページで **[接続]** をクリックして、RDP で VM に接続します。
3. ユーザー名 *azureuser* で VM にログインします。
4. サーバーのデスクトップで、**[Windows 管理ツール]**>**[Windows PowerShell]** の順に移動します。
5. PowerShell ウィンドウで、以下のコマンドを実行して IIS サーバーをインストールし、既定の iisstart.htm ファイルを削除して、VM の名前を表示する新しい iisstart.htm ファイルを追加します。

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
6. *myVM1* で RDP セッションを閉じます。
7. 手順 1. から 6. を繰り返して、IIS と更新済み iisstart.htm ファイルを *myVM2* と *myVM3* にインストールします。

## <a name="add-vms-to-the-backend-address-pool"></a>バックエンド アドレス プールに VM を追加する

VM にトラフィックを分散させるには、仮想マシン *VM1*、*VM2*、*VM3* を、前に作成したバックエンド アドレス プール *myBackendPool* に追加します。 バックエンド アドレス プールには、ロード バランサーに接続された仮想 NIC の IP アドレスが含まれます。

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で **[myLoadBalancer]** をクリックします。
2. **[設定]** で **[バックエンド プール]** をクリックし、バックエンド プールの一覧で **myBackendPool** をクリックします。
3. **myBackendPool** のページで、以下のようにします。
    - **[ターゲット ネットワーク IP 構成の追加]** をクリックして、作成した各仮想マシン (*myVM1*、*myVM2*、*myVM3*) をバックエンド プールに追加します。
    - Click **OK**.

4. ロード バランサーのバックエンド プールの設定に、**VM1**、**VM2**、**myVM3** のすべての VM が表示されていることを確認します。

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする
1. **[概要]** 画面で、ロード バランサーのパブリック IP アドレスを見つけます。 **[すべてのリソース]** をクリックし、**[myPublicIP]** をクリックします。

2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 IIS Web サーバーの既定のページがブラウザーに表示されます。

      ![IIS Web サーバー](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

アプリを実行している 3 つの VM の間で、ロード バランサーがトラフィックを負荷分散していることを確認するには、Web ブラウザーを強制的に最新の情報に更新します。

## <a name="remove-or-add-vms-from-the-backend-pool"></a>バックエンド プールの VM を追加または削除する
アプリを実行している VM には、OS の更新プログラムをインストールするなどメンテナンスが必要になることもあります。 また、アプリのトラフィックが増大すれば、それに対処するために、新たに VM を追加しなければなりません。 このセクションでは、ロード バランサーから VM を除外したりロード バランサーに対して VM を追加したりする方法について説明します。

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で **[myLoadBalancer]** をクリックします。
2. **[設定]** で **[バックエンド プール]** をクリックし、バックエンド プールの一覧で **myBackendPool** をクリックします。
3. **myBackendPool** のページの **[ターゲット ネットワーク IP 構成]** で、*VM1* をバックエンドから削除するには、**仮想マシン:myVM1** の横にある削除アイコンをクリックします。

*myVM1* がバックエンド アドレス プールに存在しなくなると、ソフトウェア更新プログラムのインストールなど、*myVM1* に対するメンテナンス タスクを実行できます。 *VM1* がなくなると、負荷は *myVM2* と *myVM3* の間で分散されるようになります。 

*myVM1* を再びバックエンド プールに追加するには、この記事の「*バックエンド アドレス プールに VM を追加する*」セクションの手順に従います。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、ロード バランサー、および関連するすべてのリソースは、不要になったら削除します。 これを行うには、ロード バランサーを含むリソース グループを選択し、**[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Standard Load Balancer を作成し、それに VM をアタッチして、ロード バランサー トラフィック規則と正常性プローブを構成してから、ロード バランサーをテストしました。 また、負荷分散セットから VM を削除し、バックエンド アドレス プールに VM を追加して戻しました。 Azure Load Balancer についてさらに学習するには、Azure Load Balancer のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Load Balancer のチュートリアル](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
