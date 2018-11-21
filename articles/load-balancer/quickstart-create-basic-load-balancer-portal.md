---
title: 'クイック スタート: パブリック Basic Load Balancer を作成する - Azure Portal | Microsoft Docs'
description: このクイックスタートでは、Azure Portal を使用してパブリック Basic Load Balancer を作成する方法について説明します。
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 523f5eba632b15eaaf45f24be820f7b255aae7c0
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616029"
---
# <a name="quickstart-create-a-public-basic-load-balancer-by-using-the-azure-portal"></a>クイック スタート: Azure Portal を使用してパブリック Basic Load Balancer を作成する

負荷分散では、着信要求を複数の仮想マシン (VM) に分散させることで、より高いレベルの可用性とスケールを実現します。 Azure Portal を使用して、仮想マシンの負荷分散を行うロード バランサーを作成できます。 このクイック スタートでは、Basic 価格レベルでネットワーク リソース、バックエンド サーバー、およびロードバランサーを作成する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

このクイック スタートのすべてのタスクには、[Azure Portal](http://portal.azure.com) にサインインする必要があります。

## <a name="create-a-basic-load-balancer"></a>Basic Load Balancer を作成する

このセクションでは、ポータルを使用してパブリック Basic Load Balancer を作成します。 ポータルを使用してパブリック IP とロード バランサー リソースを作成する場合、パブリック IP アドレスは ロード バランサーのフロント エンドとして自動的に構成されます。 フロントエンドの名前は **myLoadBalancer** です。

1. ポータルの左上で、**[リソースの作成]** > **[ネットワーキング]** > **[Load Balancer]** の順に選択します。
2. **[ロード バランサーの作成]** ウィンドウに次の値を入力します。
   - **myLoadBalancer** - ロード バランサーの名前
   - **パブリック** - ロード バランサーの種類 
   - **myPublicIP** - **SKU** を **Basic** に設定して作成する必要があるパブリック IP。**[割り当て]** は **[動的]** に設定します。
   - **myResourceGroupLB** - 新しいリソース グループの名前
3. **作成**を選択します。
   
![ロード バランサーの作成](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-back-end-servers"></a>バックエンド サーバーを作成する

このセクションでは、仮想ネットワークを作成し、Basic Load Balancer のバックエンド プール用に 2 つの仮想マシンを作成します。 次に、ロード バランサーをテストするために仮想マシンにインターネット インフォメーション サービス (IIS) をインストールします。

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成
1. ポータルの左上で、**[新規]** > **[ネットワーキング]** > **[仮想ネットワーク]** の順に選択します。
2. **[仮想ネットワークの作成]** ウィンドウで、次の値を入力し、**[作成]** を選択します。
   - **myVnet** - 仮想ネットワークの名前
   - **myResourceGroupLB** - 既存のリソース グループの名前
   - **myBackendSubnet** - サブネット名

   ![仮想ネットワークの作成](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>仮想マシンを作成する

1. ポータルの左上で、**[新規]** > **[コンピューティング]** > **[Windows Server 2016 Datacenter]** の順に選択します。 
2. 次の仮想マシンの値を入力し、**[OK]** を選択します。
   - **myVM1** - 仮想マシンの名前。        
   - **azureuser** - 管理者のユーザー名。    
   - **myResourceGroupLB** - リソース グループ  (**[リソース グループ]** で **[既存のものを使用]** を選択し、**[myResourceGroupLB]** を選択します)。   
3. 仮想マシンのサイズとして **[DS1_V2]** を選択し、**[選択]** をクリックします。
4. VM の設定に以下の値を入力します。
   - **myAvailabilitySet** - 作成する新しい可用性セットの名前。
   - **myVNet** - 仮想ネットワークの名前  (選択されていることを確認します)。
   - **myBackendSubnet** - サブネットの名前  (選択されていることを確認します)。
   - **myVM1-ip** - パブリック IP アドレス。
   - **myNetworkSecurityGroup** - 作成する必要がある新しいネットワーク セキュリティ グループ (NSG、ファイアウォールの種類) の名前。
5. **[無効]** を選択して、ブート診断を無効にします。
6. **[OK]** を選択し、概要ページの設定を確認して、**[作成]** を選択します。
7. 手順 1 から 6 を実行して、**VM2** という名前の 2 つ目の VM を作成します。
   - **myAvailabilityset** - 可用性セット。
   - **myVnet** - 仮想ネットワーク。
   - **myBackendSubnet** - サブネット。
   - **myNetworkSecurityGroup** - ネットワーク セキュリティ グループ。 

### <a name="create-nsg-rules"></a>NSG ルールを作成する

このセクションでは、HTTP と RDP を使用する受信接続を許可するための NSG 規則を作成します。

1. 左側のメニューで、**[すべてのリソース]** を選択します。 リソースの一覧から、**myResourceGroupLB** リソース グループの **myNetworkSecurityGroup** を選択します。
2. **[設定]** で **[受信セキュリティ規則]** を選択し、**[追加]** を選択します。
3. **myHTTPRule** という名前の受信セキュリティ規則のために以下の値を入力し、ポート 80 を使用する受信 HTTP 接続を許可します。 **[OK]** をクリックします。
   - **サービス タグ** - **ソース**
   - **インターネット** - **ソース サービス タグ**
   - **80** - **宛先ポート範囲**
   - **TCP** - **プロトコル**
   - **許可** - **アクション**
   - **100** - **優先度**
   - **myHTTPRule** - **名前**
   - **HTTP を許可する** - **説明**
 
   ![NSG 規則を作成する](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
4. 手順 2 から 3 を繰り返して、**myRDPRule** という名前で規則をもう 1 つ作成し、ポート 3389 経由の受信 RDP 接続を許可します。 次の値を使用します。
   - **サービス タグ** - **ソース**
   - **インターネット** - **ソース サービス タグ**
   - **3389** - **宛先ポート範囲**
   - **TCP** - **プロトコル**
   - **許可** - **アクション**
   - **200** - **優先度**
   - **myRDPRule** - **名前**
   - **RDP を許可する** - **説明**

   

### <a name="install-iis"></a>IIS のインストール

1. 左側のメニューで、**[すべてのリソース]** を選択します。 リソースの一覧から、**myResourceGroupLB** リソース グループの **myVM1** を選択します。
2. **[概要]** ページで **[接続]** を選択して、RDP で VM に接続します。
3. ユーザー名を **azureuser**、パスワードを **Azure123456!** にして VM にサインインします。
4. サーバーのデスクトップで、**[Windows 管理ツール]** > **[サーバー マネージャー]** の順に移動します。
5. サーバー マネージャーで、**[管理]**、**[役割と機能の追加]** の順に選択します。
   ![サーバー マネージャーの役割の追加](./media/load-balancer-get-started-internet-portal/servermanager.png)
6. 役割と機能の追加ウィザードで、次の値を指定します。
   - **[インストールの種類の選択]** ページで、**[役割ベースまたは機能ベースのインストール]** を選択します。
   - **[対象サーバーの選択]** ページで **[myVM1]** を選択します。
   - **[サーバーの役割を選択してください]** ページで、**[Web Server (IIS)]** を選択します。
   - 指示に従って、ウィザードの残りの部分を完了します。 
7. 仮想マシン **myVM2** に対して、手順 1. から 6. を繰り返します。

## <a name="create-resources-for-the-basic-load-balancer"></a>Basic Load Balancer のリソースを作成する

このセクションでは、バックエンド アドレス プールと正常性プローブのロード バランサー設定を構成します。 また、ロード バランサーと NAT 規則を指定します。


### <a name="create-a-back-end-address-pool"></a>バックエンド アドレス プールを作成する

トラフィックを VM に分散するには、バックエンド アドレス プールに、ロード バランサーに接続される仮想 NIC の IP アドレスを含めます。 バックエンド アドレス プール **myBackendPool** を作成し、**VM1** および **VM2** を含めます。

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧から **[myLoadBalancer]** を選択します。
2. **[設定]** で、**[バックエンド プール]**、**[追加]** の順に選択します。
3. **[バックエンド プールの追加]** ページで、以下の操作を行い、**[OK]** を選択します。
   - **[名前]** に「**myBackEndPool**」と入力します。
   - **[関連付け先]** のドロップダウン メニューで **[可用性セット]** を選択します。
   - **[可用性セット]** で **[myAvailabilitySet]** を選択します。
   - **[ターゲット ネットワーク IP 構成の追加]** を選択して、作成した各仮想マシン (**myVM1** と **myVM2**) をバックエンド プールに追加します。

   ![バックエンド アドレス プールへの追加](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. ロード バランサーのバックエンド プールの設定に、**VM1** と **VM2** の両方の VM が表示されていることを確認します。

### <a name="create-a-health-probe"></a>正常性プローブの作成

Basic Load Balancer でアプリの状態を監視するには、正常性プローブを使用します。 正常性プローブは、ロード バランサーのローテーションに含める VM を、正常性チェックへの応答に基づいて動的に追加したり削除したりする働きをします。 **myHealthProbe** という正常性プローブを作成し、VM の正常性を監視します。

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧から **[myLoadBalancer]** を選択します。
2. **[設定]** で、**[正常性プローブ]**、**[追加]** の順に選択します。
3. 以下の値を使用し、**[OK]** を選択します。
   - **myHealthProbe** - 正常性プローブの名前
   - **HTTP** - プロトコルの種類
   - **80** - ポート番号
   - **Healthprobe.aspx** - URI パス。 この値は、他の URI に置き換えるか、既定のパス値である **"\\"** のままにして既定の URI を取得することもできます。
   - **15** - プローブの試行の**間隔**を示す秒数
   - **2** - プローブの連続する失敗回数を示す**異常しきい値** (この回数を超えると、VM は異常と見なされます)

   ![プローブの追加](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成

ロード バランサー規則は、VM に対するトラフィックの分散方法を定義するために使用します。 着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを、必要な発信元ポートと宛先ポートと共に定義します。 

フロント エンド **LoadBalancerFrontEnd** のポート 80 をリッスンするために、**myLoadBalancerRuleWeb** という名前のロード バランサー規則を作成します。 この規則は、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール **myBackEndPool** に送信するためにも使用され、この送信にもポート 80 が使用されます。 

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧から **[myLoadBalancer]** を選択します。
2. **[設定]** で、**[負荷分散規則]**、**[追加]** の順に選択します。
3. 以下の値を使用し、**[OK]** を選択します。
   - **myHTTPRule** - ロード バランサー規則の名前
   - **TCP** - プロトコルの種類
   - **80** - ポート番号
   - **80** - バックエンド ポート
   - **myBackendPool** - バックエンド プールの名前
   - **myHealthProbe** - 正常性プローブの名前
    
   ![ロード バランサー規則の追加](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする
1. **[概要]** 画面で、ロード バランサーのパブリック IP アドレスを見つけます。 **[すべてのリソース]** を選択し、**[myPublicIP]** を選択します。

2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 IIS Web サーバーの既定のページがブラウザーに表示されます。

   ![IIS Web サーバー](./media/load-balancer-get-started-internet-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、ロード バランサー、および関連するすべてのリソースは、不要になったら削除できます。 ロード バランサーを含むリソース グループを選択し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、リソース グループ、ネットワーク リソース、およびバックエンド サーバーを作成しました。 その後、それらのリソースを使用して、Basic Load Balancer を作成しました。 Azure Load Balancer についてさらに学習するには、Azure Load Balancer のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Load Balancer のチュートリアル](tutorial-load-balancer-basic-internal-portal.md)
