---
title: 'クイック スタート: Standard Load Balancer を作成する - Azure Portal | Microsoft Docs'
description: このクイックスタートでは、Azure Portal を使用して Standard Load Balancer を作成する方法について説明します。
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a Standard Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/21/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 2197ab230341fb2945e7b1acd9a010ef3d3f8c22
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2018
ms.locfileid: "42702408"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>クイック スタート: Azure Portal を使用して VM の負荷を分散する Standard Load Balancer を作成する

負荷分散では、着信要求を複数の仮想マシンに分散させることで、より高いレベルの可用性とスケールを実現します。 Azure Portal を使用して、仮想マシン (VM) の負荷分散を行うロード バランサーを作成できます。 このクイック スタートでは、Standard Load Balancer を使用して VM に負荷分散する方法を示します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([http://portal.azure.com](http://portal.azure.com)) にサインインします。

## <a name="create-a-public-load-balancer"></a>パブリック ロード バランサーを作成する

このセクションでは、仮想マシンの負荷分散に役立つパブリック ロード バランサーを作成します。 Standard Load Balancer では、Standard パブリック IP アドレスだけがサポートされています。 Standard Load Balancer を作成するときに、Standard Load Balancer のフロントエンド (既定では *LoadBalancerFrontend* という名前) として構成される新しい Standard パブリック IP アドレスも作成する必要があります。 

1. 画面の左上で、**[リソースの作成]** > **[ネットワーキング]** > **[ロード バランサー]** の順にクリックします。
2. **[ロード バランサーの作成]** ページで、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、**[作成]** を選択します。

    | Setting                 | 値                                              |
    | ---                     | ---                                                |
    | Name                   | *myLoadBalancer*                                   |
    | type          | パブリック                                        |
    | SKU           | 標準                          |
    | パブリック IP アドレス | **[新規作成]** を選択して、テキスト ボックスに「*myPublicIP*」と入力します。 既定では、パブリック IP アドレスの Standard SKU が選択されます。 **[可用性ゾーン]** で、**[ゾーン冗長]** を選択します。 |
    | サブスクリプション               | サブスクリプションを選択します。    |
    |リソース グループ | **[新規作成]** を選択して、「*myResourceGroupSLB*」と入力します。    |
    | Location           | **[西ヨーロッパ]** を選択します。                          |
    

![ロード バランサーの作成](./media/load-balancer-standard-public-portal/create-load-balancer.png)


## <a name="create-backend-servers"></a>バックエンド サーバーの作成

このセクションでは、仮想ネットワークを作成し、ロード バランサーのバックエンド プール用に 2 台の仮想マシンを作成して、ロード バランサーをテストするために仮想マシンに IIS をインストールします。

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
    - *myResourceGroupSLB* - **リソース グループ**。**[既存のものを使用]** を選択し、*[myResourceGroupSLB]* を選択します。
2. Click **OK**.
3. 仮想マシンのサイズとして **[DS1_V2]** を選択し、**[選択]** をクリックします。
4. VM の設定に以下の値を入力します。
    1. *myVNet* が仮想ネットワークとして選択され、*myBackendSubnet* がサブネットとして選択されていることを確認します。
    2. **[パブリック IP アドレス]** の **[パブリック IP アドレスの作成]** ウィンドウで、**[Standard]** を選択してから **[OK]** を選択します。
    3. **[ネットワーク セキュリティ グループ]** で **[詳細設定]** を選択して、以下を実行します。
        1. *[ネットワーク セキュリティ グループ (ファイアウォール)] を選択し、**[ネットワーク セキュリティ グループの選択]** ページで **[新規作成]** を選択します。 
        2. **[ネットワーク セキュリティ グループの作成]** ページで、**[名前]** に「*myNetworkSecurityGroup*」と入力し、**[OK]** を選択します。
5. **[無効]** をクリックして、ブート診断を無効にします。
6. **[OK]** をクリックし、概要ページの設定を確認して、**[作成]** をクリックします。
7. 手順 1. から 6. を使用して、*VM2* という名前の 2 つ目の VM を作成します。可用性セットとして *myAvailibilityset*、仮想ネットワークとして *myVnet*、サブネットとして *myBackendSubnet*、ネットワーク セキュリティ グループとして **myNetworkSecurityGroup* を指定します。 

### <a name="create-nsg-rule"></a>NSG 規則の作成

このセクションでは、HTTP を使用する受信接続を許可するための NSG 規則を作成します。

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
 
### <a name="install-iis"></a>IIS のインストール

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で *[myResourceGroupLB]* リソース グループにある **[myVM1]** をクリックします。
2. **[概要]** ページで **[接続]** をクリックして、RDP で VM に接続します。
3. ユーザー名 *azureuser* で VM にログインします。
4. サーバーのデスクトップで、**[Windows 管理ツール]**>**[サーバー マネージャー]** の順に移動します。
5. サーバー マネージャーで **[役割と機能の追加]** をクリックします。
6. **役割と機能の追加ウィザード**で、次の値を指定します。
    - **[インストールの種類の選択]** ページで、**[役割ベースまたは機能ベースのインストール]** をクリックします。
    - **[対象サーバーの選択]** ページで **[myVM1]** をクリックします。
    - **[サーバーの役割を選択してください]** ページで、**[Web Server (IIS)]** をクリックします。
    - 指示に従って、ウィザードの残りの部分を完了します 
7. 仮想マシン *myVM2* に対して、手順 1. から 6. を繰り返します。

## <a name="create-load-balancer-resources"></a>ロード バランサーのリソースを作成する

このセクションでは、バックエンド アドレス プールと正常性プローブのロード バランサー設定を構成し、ロード バランサーの規則を指定します。


### <a name="create-a-backend-address-pool"></a>バックエンド アドレス プールの作成

トラフィックを VM に分散するには、バックエンド アドレス プールに、ロード バランサーに接続される仮想 NIC の IP アドレスを含めます。 バックエンド アドレス プール *myBackendPool* を作成し、*VM1* および *VM2* を含めます。

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で **[myLoadBalancer]** をクリックします。
2. **[設定]** で **[バックエンド プール]** をクリックし、**[追加]** をクリックします。
3. **[バックエンド プールの追加]** ページで、以下の操作を行います。
   - バックエンド プールの名前として、[名前] に「*myBackendPool*」と入力します。
   - **[仮想ネットワーク]** で *[myVNet]* を選択します。
   - **[仮想マシン]** で、対応する IP アドレスと共に *myVM1* および *myVM2* を追加して、**[追加]** を選択します。
    - Click **OK**.

3. ロード バランサーのバックエンド プールの設定に、**VM1** と **VM2** の両方の VM が表示されていることを確認します。

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
    
    ![負荷分散規則の追加](./media/load-balancer-standard-public-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする
1. **[概要]** 画面で、ロード バランサーのパブリック IP アドレスを見つけます。 **[すべてのリソース]** をクリックし、**[myPublicIP]** をクリックします。

2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 IIS Web サーバーの既定のページがブラウザーに表示されます。

      ![IIS Web サーバー](./media/load-balancer-standard-public-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、ロード バランサー、および関連するすべてのリソースは、不要になったら削除します。 これを行うには、ロード バランサーを含むリソース グループを選択し、**[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Standard Load Balancer を作成し、それに VM をアタッチして、ロード バランサー トラフィック規則と正常性プローブを構成してから、ロード バランサーをテストしました。 Azure Load Balancer についてさらに学習するには、Azure Load Balancer のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Load Balancer のチュートリアル](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
