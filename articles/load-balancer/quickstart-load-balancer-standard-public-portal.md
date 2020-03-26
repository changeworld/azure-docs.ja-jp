---
title: 'クイックスタート: パブリック ロード バランサーを作成する - Azure portal'
titleSuffix: Azure Load Balancer
description: このクイックスタートでは、Azure portal を使用してロード バランサーを作成する方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: b424fe315737b84479283eed2d77398c8ce4f148
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "78898826"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>クイック スタート:VM の負荷を分散するロード バランサーを Azure portal を使用して作成する

負荷分散では、着信要求を複数の仮想マシンに分散させることで、より高いレベルの可用性とスケールを実現します。 Azure Portal を使用して、仮想マシン (VM) の負荷分散を行うロード バランサーを作成できます。 このクイックスタートでは、パブリック ロード バランサーを使用して VM の負荷を分散する方法を示します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="create-a-load-balancer"></a>ロード バランサーを作成する

このセクションでは、仮想マシンの負荷分散に役立つロード バランサーを作成します。 パブリック ロード バランサーまたは内部ロード バランサーを作成できます。 パブリック ロード バランサーを作成するときは、ロード バランサーのフロントエンド (既定では *LoadBalancerFrontend* という名前) として構成される新しいパブリック IP アドレスも作成する必要があります。

1. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーク]**  >  **[Load Balancer]** を選択します。
2. **[ロード バランサーの作成]** ページの **[基本]** タブで、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[確認と作成]** を選択します。

    | 設定                 | Value                                              |
    | ---                     | ---                                                |
    | サブスクリプション               | サブスクリプションを選択します。    |    
    | Resource group         | **[新規作成]** を選択して、テキスト ボックスに「*myResourceGroupSLB*」と入力します。|
    | 名前                   | *myLoadBalancer*                                   |
    | リージョン         | **[西ヨーロッパ]** を選択します。                                        |
    | Type          | **[パブリック]** を選択します。                                        |
    | SKU           | **[Standard]** または **[Basic]** を選択します。 Microsoft では、運用環境のワークロードに Standard をお勧めします。 |
    | パブリック IP アドレス | **[新規作成]** を選択します。 使用したいパブリック IP が既にある場合は、 **[既存のものを使用]** を選択します。 |
    | パブリック IP アドレス名              | テキスト ボックスに「*myPublicIP*」と入力します。   Basic パブリック IP を作成するには、```-SKU Basic``` を使用します。 Basic パブリック IP は、**Standard** Load Balancer と共に利用することはできません。 運用環境のワークロードには **Standard** の使用をお勧めします。|
    | 可用性ゾーン | 回復性があるロード バランサーを作成するには、「*ゾーン冗長*」と入力します。 ゾーンのロード バランサーを作成するには、1、2、3 の中から特定のゾーンを選択します。 |

> [!IMPORTANT]
> 以降このクイックスタートでは、前の SKU 選択プロセスで **Standard** SKU が選択されたことを前提に話を進めます。


3. **[確認および作成]** タブで、 **[作成]** を選択します。   

    ![Standard Load Balancer を作成する](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Load Balancer のリソースを作成する

このセクションでは、バックエンド アドレス プールの Load Balancer の設定と正常性プローブを構成し、バランサーの規則を指定します。

### <a name="create-a-backend-pool"></a>バックエンド プールを作成する

トラフィックを VM に分散するには、バックエンド アドレス プールに、Load Balancer に接続される仮想 NIC の IP アドレスを含めます。 バックエンド アドレス プール *myBackendPool* を作成し、インターネット トラフィックを負荷分散するために仮想マシンを含めます。

1. 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** の順に選択し、リソースの一覧で **[myLoadBalancer]** を選択します。
2. **[設定]** で、 **[バックエンド プール]** 、 **[追加]** の順に選択します。
3. **[バックエンド プールの追加]** ページ上で、バックエンド プールの名前として「*myBackendPool*」と入力し、 **[追加]** を選択します。

### <a name="create-a-health-probe"></a>正常性プローブの作成

Load Balancer でアプリの状態を監視するには、正常性プローブを使用します。 正常性プローブは、Load Balancer のローテーションに含める VM を、正常性チェックへの応答に基づいて動的に追加したり削除したりする働きをします。 正常性プローブ *myHealthProbe* を作成し、VM の正常性を監視します。

1. 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** の順に選択し、リソースの一覧で **[myLoadBalancer]** を選択します。
2. **[設定]** で、 **[正常性プローブ]** 、 **[追加]** の順に選択します。
    
    | 設定 | Value |
    | ------- | ----- |
    | 名前 | 「*myHealthProbe*」と入力します。 |
    | Protocol | **[HTTP]** を選択します。 |
    | Port | 「*80*」と入力します。|
    | Interval | プローブの試行の**間隔**を示す秒数として、「*15*」を入力します。 |
    | 異常のしきい値 | **異常しきい値**またはプローブの連続する失敗の回数として **[2]** を選択します。この回数を超えると、VM は異常と見なされます。|
    | | |
4. **[OK]** を選択します。

### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則を作成する
ロード バランサー規則の目的は、一連の VM に対するトラフィックの分散方法を定義することです。 着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを、必要な発信元ポートと宛先ポートと共に定義します。 Load Balancer の規則 *myLoadBalancerRuleWeb* を作成して、フロントエンド *FrontendLoadBalancer* のポート 80 をリッスンし、同じポート 80 を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール *myBackEndPool* に送信します。 

1. 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** の順に選択し、リソースの一覧で **[myLoadBalancer]** を選択します。
2. **[設定]** で、 **[負荷分散規則]** 、 **[追加]** の順に選択します。
3. 負荷分散規則の構成には、以下の値を使用します。
    
    | 設定 | Value |
    | ------- | ----- |
    | 名前 | 「*myHTTPRule*」と入力します。 |
    | Protocol | **[TCP]** を選択します。 |
    | Port | 「*80*」と入力します。|
    | バックエンド ポート | 「*80*」と入力します。 |
    | バックエンド プール | *[myBackendPool]* を選択します。|
    | 正常性プローブ | *[myHealthProbe]* を選択します。 |
4. 残りの部分は既定値のままにし、次に **[OK]** を選択します。


## <a name="create-backend-servers"></a>バックエンド サーバーの作成

このセクションでは、仮想ネットワークを作成し、Load Balancer のバックエンド プール用に 3 台の仮想マシンを作成して、Load Balancer をテストするために仮想マシンに IIS をインストールします。

## <a name="virtual-network-and-parameters"></a>仮想ネットワークとパラメーター

このセクションの手順では、各パラメーターを次のように置き換える必要があります。

| パラメーター                   | Value                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupSLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | 西ヨーロッパ      |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>仮想マシンを作成する
パブリック IP の SKU とロード バランサーの SKU は一致している必要があります。 Standard Load Balancer の場合、Standard IP アドレスの VM をバックエンド プールで使用します。 このセクションでは、先ほど作成したロード バランサーのバックエンド プールに後で追加される 3 つの異なるゾーン ("*ゾーン 1*"、"*ゾーン 2*"、および "*ゾーン 3*") 内に、Standard パブリック IP アドレスを持つ 3 台の VM (*myVM1*、*myVM2*、および *myVM3*) を作成します。 Basic を選択した場合は、Basic IP アドレスの VM を使用します。

1. ポータルの左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[Windows Server 2019 Datacenter]** の順に選択します。 
   
1. **[仮想マシンの作成]** の **[Basic]** タブに次の値を入力するか選択します。
   - **[サブスクリプション]**  >  **[リソース グループ]** : **[myResourceGroupSLB]** を選択します。
   - **[インスタンスの詳細]**  >  **[仮想マシン名]** : 「*myVM1*」と入力します。
   - **[インスタンスの詳細]**  >  **[リージョン]** > **[西ヨーロッパ]** を選択します。
   - **[インスタンスの詳細]**  >  **[可用性オプション]** > **[可用性ゾーン]** を選択します。 
   - **[インスタンスの詳細]**  >  **[可用性ゾーン]** > **[1]** を選択します。
   - **[管理者アカウント]** : **[ユーザー名]** 、 **[パスワード]** 、および **[パスワードの確認]** に情報を入力します。
   - **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。
  
1. **[ネットワーク]** タブで、以下が選択されていることを確認します。
   - **[仮想ネットワーク]** : *myVNet*
   - **[サブネット]** : *myBackendSubnet*
   - **[パブリック IP]** > **[新規作成]** を選択し、 **[パブリック IP アドレスの作成]** ウィンドウで **[SKU]** に **[Standard]** を選択し、 **[可用性ゾーン]** に **[ゾーン冗長]** を選択して、 **[OK]** をクリックします。 Basic Load Balancer を作成した場合は、[Basic] を選択します。 運用環境のワークロードには Standard SKU の使用をお勧めします。
   - 新しいネットワーク セキュリティ グループ (NSG) を作成し、ファイアウォールの種類を選択するには、 **[ネットワーク セキュリティ グループ]** で **[Advanced]\(高度\)** を選択します。 
       1. **ネットワーク セキュリティ グループの構成**フィールドで、 **[新規作成]** を選択します。 
       1. 「*myNetworkSecurityGroup*」と入力し、 **[OK]** を選択します。
   - VM を Load Balancer のバックエンド プールの一部にするには、次の手順を実行します。
        - **[負荷分散]** 内の **[この仮想マシンを既存の負荷分散ソリューションの後ろに配置しますか?]** で **[はい]** を選択します。
        - **[Load balancing settings]\(負荷分散の設定\)** 内の **[負荷分散のオプション]** で **[Azure Load Balancer]** を選択します。
        - **[ロード バランサーを選択します]** で *[myLoadBalancer]* を選択します。
        - **[管理]** タブまたは **[次へ]**  >  **[管理]** を選択します。
2. **[管理]** タブの **[監視]** で、 **[起動の診断]** を **[オフ]** に設定します。 
1. **[Review + create]\(レビュー + 作成\)** を選択します。   
1. 設定を確認し、 **[作成]** を選択します。
1. 手順 2 から 6 に従って、次の値を持つ 2 つの追加の VM を作成します。他の設定はすべて *myVM1* と同じにします。

    | 設定 | VM 2| VM 3|
    | ------- | ----- |---|
    | 名前 |  *myVM2* |*myVM3*|
    | 可用性ゾーン | 2 |3|
    |パブリック IP| **Standard** SKU|**Standard** SKU|
    | パブリック IP - 可用性ゾーン| **ゾーン冗長** |**ゾーン冗長**|
    | ネットワーク セキュリティ グループ | 既存の "*myNetworkSecurity グループ*" を選択します| 既存の "*myNetworkSecurity グループ*" を選択します|

 ### <a name="create-nsg-rule"></a>NSG 規則の作成

このセクションでは、HTTP を使用する受信接続を許可するためのネットワーク セキュリティ グループの規則を作成します。

1. 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** の順に選択し、リソースの一覧で **[myResourceGroupSLB]** リソース グループにある **[myNetworkSecurityGroup]** を選択します。
2. **[設定]** で **[受信セキュリティ規則]** を選択し、 **[追加]** を選択します。
3. *myHTTPRule* という名前の受信セキュリティ規則のために以下の値を入力し、ポート 80 を使用する受信 HTTP 接続を許可します。
    - **ソース**:"*サービス タグ*"
    -  **[ソース サービス タグ]** : *Internet*
    - **[宛先ポート範囲]** : *80*
    - **プロトコル**:*TCP*
    - **アクション**:*許可*
    - **[優先度]** :*100*
    - **名前**: *myHTTPRule* 
    - **説明**:「*HTTP を許可する*」 
4. **[追加]** を選択します。
5. 受信 RDP 規則で手順を繰り返します。必要に応じて次の値を入力します。
   - **[宛先ポート範囲]** : 「*3389*」と入力します。
   - **[優先度]** :「*200*」と入力します。 
   - **Name**:「*MyRDPRule*」と入力します。 
   - **説明**:「*RDP を許可する*」と入力します。 
 
### <a name="install-iis"></a>IIS のインストール

1. 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** の順に選択し、リソースの一覧で *[myResourceGroupSLB]* リソース グループにある **[myVM1]** を選択します。
2. **[概要]** ページで **[接続]** を選択して、RDP で VM に接続します。
5. VM の作成中に指定した資格情報を使用して、この VM にログインします。 これで、仮想マシン *myVM1* とのリモート デスクトップ セッションが起動されます。
6. サーバーのデスクトップで、 **[Windows 管理ツール]** > **[Windows PowerShell]** の順に移動します。
7. PowerShell ウィンドウで、以下のコマンドを実行して IIS サーバーをインストールし、既定の iisstart.htm ファイルを削除して、VM の名前を表示する新しい iisstart.htm ファイルを追加します。

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. *myVM1* で RDP セッションを閉じます。
7. 手順 1. から 6. を繰り返して、IIS と更新済み iisstart.htm ファイルを *myVM2* と *myVM3* にインストールします。

## <a name="test-the-load-balancer"></a>Load Balancer をテストする
1. **[概要]** 画面で、ロード バランサーのパブリック IP アドレスを見つけます。 左側のメニューで **[すべてのサービス]** 、 **[すべてのリソース]** 、 **[myPublicIP]** の順に選択します。

2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 IIS Web サーバーの既定のページがブラウザーに表示されます。

   ![IIS Web サーバー](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Load Balancer が 3 つの VM すべてにトラフィックを負荷分散していることを確認するには、各 VM の IIS Web サーバーの既定のページをカスタマイズした後、クライアント マシンから Web ブラウザーを強制的に最新の情報に更新します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループ、Load Balancer、および関連するすべてのリソースは、不要になったら削除します。 これを行うには、Load Balancer を含むリソース グループ (*myResourceGroupSLB*) を選択し、 **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Standard Load Balancer を作成し、それに VM をアタッチして、ロード バランサー トラフィック規則と正常性プローブを構成してから、ロード バランサーをテストしました。 Azure Load Balancer についてさらに学習するには、[Azure Load Balancer のチュートリアル](tutorial-load-balancer-standard-public-zone-redundant-portal.md)に進んでください。

[Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)について理解を深めます。
