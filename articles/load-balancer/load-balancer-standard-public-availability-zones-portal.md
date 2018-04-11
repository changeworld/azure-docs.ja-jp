---
title: 可用性ゾーン間のロード バランサー VM - Azure Portal | Microsoft Docs
description: Azure Portal を使用して、ゾーン冗長フロントエンドを使用する Standard Load Balancer を作成し、可用性ゾーン間で VM の負荷を分散します
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
ms.date: 03/26/18
ms.author: kumud
ms.openlocfilehash: ad476922342844a908961960407eb344711932f5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Azure Portal と Standard Load Balancer を使用して、可用性ゾーン間で VM の負荷を分散します

この記事では、複数の DNS レコードに依存せずにゾーン冗長性を実現するために、ゾーン冗長フロントエンドを使用するパブリック Load Balancer Standard を作成する手順について説明します。 Standard Load Balancer の単一のフロントエンド IP アドレスは自動的にゾーン冗長になります。 単一の IP アドレスを持つロード バランサーにゾーン冗長フロントエンドを使用すると、すべての可用性ゾーン全体でリージョン内の仮想ネットワークの任意の VM に到達できるようになります。 可用性ゾーンは、データセンター全体に及ぶ珍しい障害や損失からアプリとデータを保護するために使用します。 ゾーン冗長では、1 つまたは複数の可用性ゾーンで障害が発生しても対応可能であり、リージョン内に正常なゾーンが 1 つでも残っていれば、データ パスは存続します。 

可用性ゾーンと Standard Load Balancer の使用方法の詳細については、「[Standard Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

## <a name="log-in-to-azure"></a>Azure にログインする

Azure Portal ([http://portal.azure.com](http://portal.azure.com)) にログインします。

## <a name="create-a-public-standard-load-balancer"></a>パブリック Standard Load Balancer を作成する

Standard Load Balancer では、Standard パブリック IP アドレスだけがサポートされています。 ロード バランサーの作成時に新しいパブリック IP を作成すると、自動的に Standard SKU バージョンとして構成され、自動的にゾーン冗長になります。

1. 画面の左上で、**[リソースの作成]** > **[ネットワーキング]** > **[ロード バランサー]** の順にクリックします。
2. **[Create a load balancer]\(ロード バランサーの作成\)** ページで、ロード バランサーの以下の値を入力します。
    - *myLoadBalancer* - ロード バランサーの名前。
    - **パブリック** - ロード バランサーの種類。
     - *myPublicIP* - 作成する新しいパブリック IP アドレス。 この場合、**[パブリック IP アドレスの選択]** をクリックし、**[新規作成]** をクリックします。 [名前] に「*myPublicIP*」と入力します。SKU の既定は Standard です。**[可用性ゾーン]** には **[ゾーン冗長]** を選択します。
    - *myResourceGroupLBAZ* - 作成する新しいリソース グループの名前。
    - **westeurope** - 場所。
3. **[作成]** をクリックして、ロード バランサーを作成します。
   
    ![ロード バランサーの作成](./media/load-balancer-standard-public-availability-zones-portal/1a-load-balancer.png)


## <a name="create-backend-servers"></a>バックエンド サーバーの作成

このセクションでは、仮想ネットワークを作成し、ロード バランサーのバックエンド プールに追加するリージョン用に異なるゾーン (ゾーン 1、ゾーン 2、およびゾーン 3) に仮想マシンを作成します。次に、ゾーン冗長ロード バランサーをテストするために仮想マシンに IIS をインストールします。 今後ゾーンに障害が発生すると、同じゾーン内の VM のヘルス プローブは失敗し、トラフィックは他のゾーン内の VM によって引き続き処理されます。

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成
1. 画面の左上で **[リソースの作成]** > **[ネットワーク]** > **[仮想ネットワーク]** の順にクリックし、仮想ネットワークの以下の値を入力します。
    - *myVnet* - 仮想ネットワークの名前。
    - *myResourceGroupLBAZ* - 既存のリソース グループの名前。
    - *myBackendSubnet* - サブネット名。
2. **[作成]** をクリックして、仮想ネットワークを作成します。

    ![仮想ネットワークの作成](./media/load-balancer-standard-public-availability-zones-portal/2-load-balancer-virtual-network.png)

## <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

1. 画面の左上にある **[リソースの作成]** をクリックし、検索ボックスに「*ネットワーク セキュリティ グループ*」と入力し、ネットワーク セキュリティ グループ ページで **[作成]** をクリックします。
2. [ネットワーク セキュリティ グループの作成] で次の値を入力します。
    - *myNetworkSecurityGroup* - ネットワーク セキュリティ グループの名前。
    - *myResourceGroupLBAZ* - 既存のリソース グループの名前。
   
![仮想ネットワークの作成](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-nsg-rules"></a>NSG ルールを作成する

このセクションでは、Azure Portal で HTTP と RDP を使用する受信接続を許可するための NSG 規則を作成します。

1. Azure Portal の左側のメニューで **[すべてのリソース]** をクリックし、**[myResourceGroupLB]** リソース グループにある **[myNetworkSecurityGroup]** を検索してクリックします。
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


### <a name="create-virtual-machines"></a>仮想マシンを作成する

1. 画面の左上で **[リソースの作成]** > **[コンピューティング]** > **[Windows Server 2016 Datacenter]** の順にクリックし、仮想マシンの以下の値を入力します。
    - *myVM1* - 仮想マシンの名前です。        
    - *azureuser* - 管理者のユーザー名です。    
    - *myResourceGroupLBAZ* - **リソース グループ**。**[既存のものを使用]** を選択し、*[myResourceGroupLBAZ]* を選択します。
2. Click **OK**.
3. 仮想マシンのサイズとして **[DS1_V2]** を選択し、**[選択]** をクリックします。
4. VM の設定に以下の値を入力します。
    - *ゾーン 1* - VM を配置するゾーン。
    -  *myVNet* - 仮想ネットワークとしてこれが選択されていることを確認します。
    - *myBackendSubnet* - サブネットとしてこれが選択されていることを確認します。
    - *myNetworkSecurityGroup* - ネットワーク セキュリティ グループ (ファイアウォール) の名前。
5. **[無効]** をクリックして、ブート診断を無効にします。
6. **[OK]** をクリックし、概要ページの設定を確認して、**[作成]** をクリックします。
  
 ![仮想マシンの作成](./media/load-balancer-standard-public-availability-zones-portal/create-vm-standard-ip.png)

7. 手順 1 ～ 6 を使用して、ゾーン 2 に *VM2* という 2 つ目の VM、ゾーン 3 に 3 つ目の VM、仮想ネットワークとして *myVnet*、サブネットとして *myBackendSubnet*、ネットワーク セキュリティ グループとして **myNetworkSecurityGroup* を作成します。

### <a name="install-iis-on-vms"></a>VM に IIS をインストールする

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で *[myResourceGroupLBAZ]* リソース グループにある **[myVM1]** をクリックします。
2. **[概要]** ページで **[接続]** をクリックして、RDP で VM に接続します。
3. ユーザー名 *azureuser* で VM にログインします。
4. サーバーのデスクトップで、**[Windows 管理ツール]**>**[サーバー マネージャー]** の順に移動します。
5. [サーバー マネージャー] クイック スタート ページの **[役割と機能の追加]** をクリックします。

   ![バックエンド アドレス プールへの追加 ](./media/load-balancer-standard-public-availability-zones-portal/servermanager.png)    

1. **役割と機能の追加ウィザード**で、次の値を指定します。
    - **[インストールの種類の選択]** ページで、**[役割ベースまたは機能ベースのインストール]** をクリックします。
    - **[対象サーバーの選択]** ページで **[myVM1]** をクリックします。
    - **[サーバーの役割を選択してください]** ページで、**[Web Server (IIS)]** をクリックします。
    - 指示に従って、ウィザードの残りの部分を完了します。
2. 仮想マシン *myVM1* との RDP セッションを閉じます。
3. 手順 1 ～ 7 を繰り返して、VM *myVM2* および *myVM3* に IIS をインストールします。

## <a name="create-load-balancer-resources"></a>ロード バランサーのリソースを作成する

このセクションでは、バックエンド アドレス プールと正常性プローブのロード バランサー設定を構成し、ロード バランサーと NAT 規則を指定します。


### <a name="create-a-backend-address-pool"></a>バックエンド アドレス プールの作成

トラフィックを VM に分散するには、バックエンド アドレス プールに、ロード バランサーに接続される仮想 NIC の IP アドレスを含めます。 バックエンド アドレス プール *myBackendPool* を作成し、*VM1* および *VM2* を含めます。

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で **[myLoadBalancer]** をクリックします。
2. **[設定]** で **[バックエンド プール]** をクリックし、**[追加]** をクリックします。
3. **[バックエンド プールの追加]** ページで、以下の操作を行います。
    - バックエンド プールの名前として、「myBackEndPool」と入力します。
    - **[仮想ネットワーク]** の場合は、ドロップダウン メニューの **[myVNet]** をクリックします。
    - **[仮想マシン]** の場合は、ドロップダウン メニューの **[myVM1]** をクリックします。
    - **[IP アドレス]** の場合、ドロップダウン メニューで、myVM1 の IP アドレスをクリックします。
4. **[新しいローカル リソースを追加する]** をクリックして、ロード バランサーのバックエンド プールに追加する各仮想マシン (*myVM2* と *myVM3*) を追加します。
5. **[追加]**をクリックします。

    ![バックエンド アドレス プールへの追加 ](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. ロード バランサー バックエンド プール設定に、3 つの VM **myVM1**、**myVM2**、および **myVM3** すべてが表示されることを確認します。

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
    
    ![負荷分散規則の追加](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする
1. **[概要]** 画面で、ロード バランサーのパブリック IP アドレスを見つけます。 **[すべてのリソース]** をクリックし、**[myPublicIP]** をクリックします。

2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 IIS Web サーバーの既定のページがブラウザーに表示されます。

      ![IIS Web サーバー](./media/load-balancer-standard-public-availability-zones-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、ロード バランサー、および関連するすべてのリソースは、不要になったら削除します。 これを行うには、ロード バランサーを含むリソース グループを選択し、**[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

[Standard Load Balancer](load-balancer-standard-overview.md) の詳細を確認する。
