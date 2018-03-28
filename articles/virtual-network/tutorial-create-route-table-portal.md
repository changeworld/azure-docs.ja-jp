---
title: ネットワーク トラフィックをルーティングする - Azure Portal | Microsoft Docs
description: Azure Portal を使用してルート テーブルでネットワーク トラフィックをルーティングする方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 45b07c6ca86802d0cc3e773234e1122ba7bd9ea7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Azure Portal を使用してルート テーブルでネットワーク トラフィックをルーティングする

既定では、仮想ネットワーク内のすべてのサブネット間でトラフィックが自動的にルーティングされます。 Azure の既定のルーティングは、独自のルートを作成して上書きすることができます。 カスタム ルートを作成する機能は、たとえば、サブネット間でネットワーク仮想アプライアンス (NVA) を越えてトラフィックをルーティングしたい場合に便利です。 この記事では、次の方法について説明します。

> [!div class="checklist"]
> * ルート テーブルの作成
> * ルートの作成
> * 複数のサブネットを含んだ仮想ネットワークを作成する
> * サブネットへのルート テーブルの関連付け
> * トラフィックをルーティングする NVA を作成する
> * 仮想マシン (VM) を異なるサブネットに展開する
> * NVA を介して、あるサブネットから別のサブネットにトラフィックをルーティングする

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="log-in-to-azure"></a>Azure にログインする 

Azure Portal (http://portal.azure.com) にログインします。

## <a name="create-a-route-table"></a>ルート テーブルの作成

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[ネットワーク]**、**[ルート テーブル]** の順に選択します。
3. 次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、**[作成]** を選択します。

    |Setting|値|
    |---|---|
    |Name|myRouteTablePublic|
    |[サブスクリプション]| サブスクリプションを選択します。|
    |リソース グループ | **[新規作成]** を選択し、「*myResourceGroup*と入力します。|
    |場所|米国東部|
 
    ![ルート テーブルを作成する](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>ルートの作成

1. ポータルの上部にある *[Search resources, services, and docs]\(リソース、サービス、ドキュメントの検索\)* ボックスで、「*myRouteTablePublic*」の入力を開始します。 検索結果に **myRouteTablePublic** が表示されたら、それを選択してください。
2. 次の図に示すように、**[設定]** で **[ルート]** を選択し、**[+ 追加]** を選択します。

    ![ルートを追加する](./media/tutorial-create-route-table-portal/add-route.png) 
 
3. **[ルートの追加]** で、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、**[作成]** を選択します。

    |Setting|値|
    |---|---|
    |ルート名|ToPrivateSubnet|
    |アドレス プレフィックス| 10.0.1.0/24|
    |次ホップの種類 | **[仮想アプライアンス]** を選択します。|
    |次ホップ アドレス| 10.0.2.4|

## <a name="associate-a-route-table-to-a-subnet"></a>サブネットへのルート テーブルの関連付け

ルート テーブルをサブネットに関連付けるには、仮想ネットワークとサブネットを作成してから、ルート テーブルをサブネットに関連付ける必要があります。

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[ネットワーク]** を選択してから、**[仮想ネットワーク]** を選択します。
3. **[仮想ネットワークの作成]** で、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、**[作成]** を選択します。

    |Setting|値|
    |---|---|
    |Name|myVirtualNetwork|
    |アドレス空間| 10.0.0.0/16|
    |[サブスクリプション] | サブスクリプションを選択します。|
    |リソース グループ|**[既存のものを使用]**、**[myResourceGroup]** の順に選択します。|
    |場所|*[米国東部]* を選択します。|
    |サブネット名|パブリック|
    |アドレス範囲|10.0.0.0/24|
    
4. ポータルの上部にある **[Search resources, services, and docs]\(リソース、サービス、ドキュメントの検索\)** ボックスで、「*myVirtualNetwork*」の入力を開始します。 検索結果に **[myVirtualNetwork]** が表示されたら、それを選択します。
5. 次の図に示すように、**[設定]** で **[サブネット]** を選択し、**[+ サブネット]** を選択します。

    ![サブネットの追加](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. 以下の情報を選択または入力し、**[OK]** を選択します。

    |Setting|値|
    |---|---|
    |Name|プライベート|
    |アドレス空間| 10.0.1.0/24|

7. 手順 5. と 6. を繰り返して、次の情報を指定します。

    |Setting|値|
    |---|---|
    |Name|DMZ|
    |アドレス空間| 10.0.2.0/24|

8. **myVirtualNetwork - Subnets** ボックスは、前の手順を完了した後に表示されます。 **[設定]** で **[サブネット]** を選択し、**[パブリック]** を選択します。
9. 次の図に示すように、**[ルート テーブル]** を選択し、**[MyRouteTablePublic]** を選択し、**[保存]** を選択します。

    ![ルート テーブルを関連付ける](./media/tutorial-create-route-table-portal/associate-route-table.png) 

## <a name="create-an-nva"></a>NVA を作成する

NVA は、ルーティング、ファイアウォール、WAN 最適化などのネットワーク機能を実行する VM です。

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[コンピューティング]**、**[Windows Server 2016 Datacenter]** の順に選択します。 別のオペレーティング システムを選択することもできますが、以降の手順では、**[Windows Server 2016 Datacenter]** を選択したという前提で説明します。 
3. **[基本]** で以下の情報を選択または入力し、**[OK]** を選択します。

    |Setting|値|
    |---|---|
    |Name|myVmNva|
    |ユーザー名|任意のユーザー名を入力します。|
    |パスワード|任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
    |リソース グループ| **[既存のものを使用]**、*[myResourceGroup]* の順に選択します。|
    |場所|**[米国東部]** を選択します。|
4. **[サイズの選択]** で、VM サイズを選択します。
5. **[設定]** で以下の情報を選択または入力し、**[OK]** を選択します。

    |Setting|値|
    |---|---|
    |Virtual Network|myVirtualNetwork - 選択されていない場合は、**[仮想ネットワーク]** を選択し、**[仮想ネットワークの選択]** で **[myVirtualNetwork]** を選択します。|
    |サブネット|**[サブネット]** を選択し、**[サブネットの選択]** で **[DMZ]** を選択します。 |
    |パブリック IP アドレス| **[パブリック IP アドレス]** を選択し、**[パブリック IP アドレスの選択]** で **[なし]** を選択します。 この VM はインターネットと接続されないため、パブリック IP アドレスが割り当てられていません。
6. **[概要]** の **[作成]** で **[作成]** を選択して、VM のデプロイを開始します。

    VM の作成には数分かかります。 VM の作成が完了し、VM に関する情報を示すボックスが表示されるまでは、次の手順に進まないでください。

7. 次の図に示すように、VM の作成後、その VM に対して表示されたボックスの **[設定]** で **[ネットワーク]** を選択し、**[myvmnva158]** (ユーザーの VM に対して Azure が作成したネットワーク インターフェイスについては、**myvmnva** の後ろの番号が異なります) を選択します。

    ![VM ネットワーク](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

8. ネットワーク インターフェイスが、自身に送信されてきたネットワーク トラフィックのうち、自身の IP アドレス宛てではないものを転送できるように、このネットワーク インターフェイスに対して IP 転送を有効にする必要があります。 次の図に示すように、**[設定]** で **[IP 構成]** を選択し、**[IP 転送]** に **[有効]** を選択して、**[保存]** を選択します。

    ![IP 転送を有効にする](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

## <a name="create-virtual-machines"></a>仮想マシンを作成する

後の手順で、*パブリック* サブネットからのトラフィックが NVA 経由で*プライベート* サブネットにルーティングされていることを検証できるように、仮想ネットワークに 2 つの VM を作成します。 [NVA の作成](#create-a-network-virtual-appliance)手順の 1 から 6 を実行します。 次の変更を除き、手順 3. と 5. では同じ設定を使用します。

|仮想マシン名      |サブネット      | パブリック IP アドレス     |
|--------- | -----------|---------              |
| myVmPublic  | パブリック     | ポータルの既定値を使用 |
| myVmPrivate | プライベート    | ポータルの既定値を使用 |

Azure で *myVmPublic* VM が作成されている場合に *myVmPrivate* VM を作成できます。 Azure で両方の VM の作成が完了するまで、以降の手順に進まないでください。

## <a name="route-traffic-through-an-nva"></a>NVA を経由するトラフィックのルーティング

1. ポータルの上部にある *[検索]* ボックスで、「*myVmPrivate*」の入力を開始します。 検索結果に **[myVmPrivate]** VM が表示されたら、それを選択します。
2. 次の図に示すように、**[接続]** を選択して *myVmPrivate* VM へのリモート デスクトップ接続を作成します。

    ![VM への接続 ](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. VM に接続するには、ダウンロードした RDP ファイルを開きます。 メッセージが表示されたら、**[Connect]** を選択します。
4. VM の作成時に指定したユーザー名とパスワードを入力し (VM の作成時に入力した資格情報を指定するために、**[その他]**、**[別のアカウントを使う]** の選択が必要になる場合があります)、**[OK]** を選択します。
5. サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** を選択して、接続処理を続行します。
6. 後の手順では、ルーティングのテストに tracert.exe コマンドを使用します。 Tracert は Internet Control Message Protocol (ICMP) を使用していますが、Windows ファイアウォール経由では拒否されます。 PowerShell から次のコマンドを入力して、Windows ファイアウォールで ICMP を有効にします。

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    この記事では tracert を使用してルーティングをテストしていますが、運用環境のデプロイでは Windows ファイアウォールで ICMP を許可することは推奨されません。
7. [IP 転送の有効化](#enable-ip-forwarding)に関するセクションで、VM のネットワーク インターフェイスに対して Azure 内での IP 転送を有効にしました。 VM 内では、オペレーティング システム、または VM 内で実行中のアプリケーションも、ネットワーク トラフィックを転送できる必要があります。 *myVmPrivate* VM から次の手順を実行し、*myVmNva* VM のオペレーティング システム内で IP 転送を有効にします。

    コマンド プロンプトから次のコマンドを使用して、*myVmNva* VM にリモート デスクトップ接続します。

    ``` 
    mstsc /v:myvmnva
    ```
    
    オペレーティング システム内で IP 転送を有効にするには、PowerShell で次のコマンドを入力します。

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    VM を再起動すると、リモート デスクトップ セッションも切断されます。
8. *myVmPrivate* VM に接続されている状態で、*myVmNva* VM の再起動後、次のコマンドを使用して、*myVmPublic* VM へのリモート デスクトップ セッションを作成します。

    ``` 
    mstsc /v:myVmPublic
    ```
    
    PowerShell から次のコマンドを入力して、Windows ファイアウォールで ICMP を有効にします。

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

9. *myVmPublic* VM から *myVmPrivate* VM へのネットワーク トラフィックのルーティングをテストするには、PowerShell から次のコマンドを入力します。

    ```
    tracert myVmPrivate
    ```

    応答は次の例のようになります。
    
    ```
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    最初のホップが 10.0.2.4 であることを確認できます。これは、NVA のプライベート IP アドレスです。 2 番目のホップは 10.0.1.4 です。これは、*myVmPrivate* VM のプライベート IP アドレスです。 *myRouteTablePublic* ルート テーブルに追加され、"*パブリック*" サブネットに関連付けられているルートにより、Azure はトラフィックを "*プライベート*" サブネットに直接ルーティングするのではなく、NVA 経由でルーティングするようになります。
10.  *myVmPublic* VM へのリモート デスクトップ セッションを閉じます。*myVmPrivate* VM にはまだ接続されたままです。
11. *myVmPrivate* VM から *myVmPublic* VM へのネットワーク トラフィックのルーティングをテストするには、コマンド プロンプトから次のコマンドを入力します。

    ```
    tracert myVmPublic
    ```

    応答は次の例のようになります。

    ```
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    トラフィックは、*myVmPrivate* VM から *myVmPublic* VM に直接ルーティングされていることがわかります。 既定では、サブネット間でトラフィックが直接ルーティングされます。
12. *myVmPrivate* VM へのリモート デスクトップ セッションを閉じます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループとそれに含まれるすべてのリソースが不要になったら、それらを削除します。 

1. ポータル上部の **[検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **[myResourceGroup]** が表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[TYPE THE RESOURCE GROUP NAME:]\(リソース グループ名を入力してください:\)** に「*myResourceGroup*」と入力し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

この記事では、ルート テーブルを作成し、それをサブネットに関連付けました。 トラフィックをパブリック サブネットからプライベート サブネットにルーティングする単純な NVA を作成しました。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) からファイアウォールや WAN 最適化などのネットワーク機能を実行する、さまざまな事前構成された NVA を展開します。 運用で使用するためにルート テーブルを展開する前に、[Azure でのルーティング](virtual-networks-udr-overview.md)、[ルート テーブルの管理](manage-route-table.md)、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)について十分に理解しておくことをお勧めします。


仮想ネットワーク内では多数の Azure リソースをデプロイできますが、一部の Azure PaaS サービスのリソースは仮想ネットワークにデプロイできなません。 ただし、一部の Azure PaaS サービスのリソースへのアクセスを、仮想ネットワーク サブネットからのトラフィックのみに制限できます。 Azure PaaS リソースへのネットワーク アクセスを制限する方法を確認するには、次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [PaaS リソースへのネットワーク アクセスを制限する](virtual-network-service-endpoints-configure.md#azure-portal)
