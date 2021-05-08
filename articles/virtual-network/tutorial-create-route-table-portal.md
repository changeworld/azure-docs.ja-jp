---
title: ネットワーク トラフィックをルーティングする - チュートリアル - Azure portal
titlesuffix: Azure Virtual Network
description: このチュートリアルでは、Azure Portal を使用してルート テーブルでネットワーク トラフィックをルーティングする方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/16/2021
ms.author: kumud
ms.openlocfilehash: 7da59e996ec37d3653dbde68c5f56caa9e8261ee
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061912"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>チュートリアル:Azure Portal を使用してルート テーブルでネットワーク トラフィックをルーティングする

既定では、仮想ネットワーク内のすべてのサブネット間でトラフィックがルーティングされます。 Azure の既定のルーティングは、独自のルートを作成してオーバーライドすることができます。 カスタム ルートは、たとえば、サブネット間でネットワーク仮想アプライアンス (NVA) を越えてトラフィックをルーティングしたい場合に便利です。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * トラフィックをルーティングする NVA を作成する
> * ルート テーブルの作成
> * ルートの作成
> * サブネットへのルート テーブルの関連付け
> * 仮想マシン (VM) を異なるサブネットに展開する
> * NVA を介して、あるサブネットから別のサブネットにトラフィックをルーティングする

このチュートリアルでは、[Azure portal](https://portal.azure.com) を使用します。 [Azure CLI](tutorial-create-route-table-cli.md) または [Azure PowerShell](tutorial-create-route-table-powershell.md) を使用することもできます。

## <a name="prerequisites"></a>前提条件

開始する前に、アクティブなサブスクリプションを含む Azure アカウントが必要です。 アカウントがない場合は、[無料でアカウントを作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ことができます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

1. Azure portal メニューから **[リソースの作成]** を選択します。 Azure Marketplace から **[ネットワーク]**  >  **[仮想ネットワーク]** を選択するか、検索ボックスから「**仮想ネットワーク**」を検索します。

2. **［作成］** を選択します

2. **[仮想ネットワークの作成]** に次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | サブスクリプション | サブスクリプションを選択します。|
    | Resource group | **[新規作成]** を選択し、「**MyResourceGroup**」と入力します。 </br> **[OK]** を選択します。 |
    | 名前 | 「**myVirtualNetwork**」と入力します。 |
    | 場所 | **[(米国) 米国東部]** を選択します。|

3. **[IP アドレス]** タブを選択するか、ページ下部の **[次へ: IP アドレス]** ボタンを選択します。

4. **[IPv4 アドレス空間]** で、既存のアドレス空間を選択し、「**10.0.0.0/16**」に変更します。

4. **[サブネットの追加]** を選択し、 **[サブネット名]** に「**Public**」、 **[サブネットのアドレス範囲]** に「**10.0.0.0/24**」と入力します。

5. **[追加]** を選択します。

6. **[サブネットの追加]** を選択し、 **[サブネット名]** に「**Private**」、 **[サブネットのアドレス範囲]** に「**10.0.1.0/24**」と入力します。

7. **[追加]** を選択します。

8. **[サブネットの追加]** を選択し、 **[サブネット名]** に「**DMZ**」、 **[サブネットのアドレス範囲]** に「**10.0.2.0/24**」と入力します。

9. **[追加]** を選択します。

10. **[セキュリティ]** タブを選択するか、ページ下部にある **[次へ: セキュリティ]** ボタンを選択します。

11. **[BastionHost]** で **[有効にする]** を選択します。 この情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | 要塞名 | 「**myBastionHost**」と入力します |
    | AzureBastionSubnet のアドレス空間 | 「**10.0.3.0/24**」と入力します。 |
    | パブリック IP アドレス | **[新規作成]** を選択します。 </br> **[名前]** に「**myBastionIP**」と入力します。 </br> **[OK]** を選択します。 |

12. **[確認と作成]** タブを選択するか、 **[確認と作成]** ボタンを選択します。

13. **［作成］** を選択します

## <a name="create-an-nva"></a>NVA を作成する

ネットワーク仮想アプライアンス (NVA) は、ルーティングやファイアウォールの最適化などのネットワーク機能に役立つ仮想マシンです。 このチュートリアルでは、**Windows Server 2019 Datacenter** を使用していることを前提としています。 必要に応じて、別のオペレーティング システムを選択することもできます。

1. ポータルの左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[仮想マシン]** を選択します。 
   
2. **[仮想マシンの作成]** の **[Basic]** タブに、値を入力するか選択します。

    | 設定 | 値                                          |
    |-----------------------|----------------------------------|
    | **プロジェクトの詳細** |  |
    | サブスクリプション | Azure サブスクリプションを選択します。 |
    | リソース グループ | **[myResourceGroup]** を選択します |
    | **インスタンスの詳細** |  |
    | 仮想マシン名 | 「**myVMNVA**」と入力します |
    | リージョン | **[(米国) 米国東部]** を選択します |
    | 可用性オプション | **[インフラストラクチャ冗長は必要ありません]** を選択します |
    | Image | **[Windows Server 2019 Datacenter]** を選択します |
    | Azure Spot インスタンス | **[いいえ]**  を選択します |
    | サイズ | VM サイズを選択するか、既定の設定を使用します |
    | **管理者アカウント** |  |
    | ユーザー名 | ユーザー名を入力します |
    | Password | [パスワード] を入力します |
    | [パスワードの確認入力] | パスワードを再入力します |
    | **受信ポートの規則** |    |
    | パブリック受信ポート | **[なし]** を選択します。 |
    |

3. **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。
  
4. [ネットワーク] タブで、次を選択または入力します。

    | 設定 | 値 |
    |-|-|
    | **ネットワーク インターフェイス** |  |
    | 仮想ネットワーク | **[myVirtualNetwork]** を選択します。 |
    | Subnet | **[DMZ]** を選択します。 |
    | パブリック IP | **[なし]** を選択します |
    | NIC ネットワーク セキュリティ グループ | **[Basic]** を選択します|
    | パブリック受信ポート ネットワーク | **[なし]** を選択します。 |
   
5. **[Review + create]\(確認と作成\)** タブを選択するか、ページの下部にある青色の **[Review + create]\(確認と作成\)** ボタンを選択します。
  
6. 設定を確認し、 **[作成]** を選択します。

## <a name="create-a-route-table"></a>ルート テーブルの作成

1. [[Azure portal]](https://portal.azure.com) メニュー上または **[ホーム]** ページから **[リソースの作成]** を選択します。

2. 検索ボックスに、「"**ルート テーブル**"」と入力します。 検索結果に **[ルート テーブル]** が表示されたら、それを選択します。

3. **[ルート テーブル]** ページで、 **[作成]** を選択します。

4. **[基本]** タブの **[ルート テーブルの作成]** で、次の情報を入力するか選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** |   |
    | サブスクリプション | サブスクリプションを選択します。|
    | Resource group | **[myResourceGroup]** を選択します。 |
    | **インスタンスの詳細** |    |
    | リージョン | **[米国東部]** を選択します。 |
    | 名前 | 「**myRouteTablePublic**」と入力します。 |
    | ゲートウェイのルートを伝達する | **[はい]** を選択します。 |

    :::image type="content" source="./media/tutorial-create-route-table-portal/create-route-table.png" alt-text="ルート テーブルの作成、Azure portal" border="true":::

5. **[Review + create]\(確認と作成\)** タブを選択するか、ページの下部にある青色の **[Review + create]\(確認と作成\)** ボタンを選択します。

## <a name="create-a-route"></a>ルートの作成

1. [Azure portal](https://portal.azure.com) に移動して、ルート テーブルを管理します。 **ルート テーブル** を検索して選択します。

2. 目的のルート テーブルの名前 (**myRouteTablePublic**) を選択します。

3. **[myRouteTablePublic]** ページの **[設定]** セクションで、 **[ルート]** を選択します。

4. [ルート] ページで、 **[+ 追加]** ボタンを選択します。

5. **[ルートの追加]** に次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | ルート名 | 「**ToPrivateSubnet**」と入力します |
    | アドレス プレフィックス | 「**10.0.1.0/24**」(先ほど作成した "**プライベート**" サブネットのアドレス範囲) と入力します |
    | ネクストホップの種類 | **[仮想アプライアンス]** を選択します。 |
    | 次ホップ アドレス | 「**10.0.2.4**」(**DMZ** サブネットのアドレス範囲に含まれるアドレス) と入力します |

6. **[OK]** を選択します。

## <a name="associate-a-route-table-to-a-subnet"></a>サブネットへのルート テーブルの関連付け

1. [Azure portal](https://portal.azure.com) に移動して、仮想ネットワークを管理します。 **仮想ネットワーク** を検索して選択します。

2. 目的の仮想ネットワークの名前を選択します (**myVirtualNetwork**)。

3. **[myVirtualNetwork]** ページの **[設定]** セクションで、 **[サブネット]** を選択します。

4. 仮想ネットワークのサブネット一覧で **[パブリック]** を選択します。

5. **[ルート テーブル]** で、**myRouteTablePublic** を作成したルート テーブルを選択します。 

6. **[保存]** を選択して、ルート テーブルを **Public** サブネットに関連付けます。

    :::image type="content" source="./media/tutorial-create-route-table-portal/associate-route-table.png" alt-text="ルート テーブルの関連付け、サブネット一覧、仮想ネットワーク、Azure portal。" border="true":::

## <a name="turn-on-ip-forwarding"></a>IP 転送を有効にする

次に、新しい NVA 仮想マシン (**myVMNVA**) の IP 転送を有効にします。 Azure からネットワーク トラフィックが **myVMNVA** に送信され、そのトラフィックが別の IP アドレス宛ての場合、IP 転送でトラフィックが正しい場所に送信されます。

1. [Azure portal](https://portal.azure.com) に移動して、VM を管理します。 **[仮想マシン]** を検索して選択します。

2. 仮想マシンの名前として **myVMNVA** を選択します。

3. **myVMNVA** の概要ページの **[設定]** で、 **[ネットワーク]** を選択します。

4. **myVMNVA** の **[ネットワーク]** ページで、 **[ネットワーク インターフェイス]** の横のネットワーク インターフェイスを選択します。  インターフェイスの名前は、**myvmnva** で始まります。

    :::image type="content" source="./media/tutorial-create-route-table-portal/virtual-machine-networking.png" alt-text="ネットワーク、ネットワーク仮想アプライアンス (NVA) の仮想マシン (VM)、Azure portal" border="true":::

5. ネットワーク インターフェイスの概要ページの **[設定]** で、 **[IP 構成]** を選択します。

6. **[IP 構成]** ページで、 **[IP 転送]** を **[有効]** に設定して **[保存]** を選択します。

    :::image type="content" source="./media/tutorial-create-route-table-portal/enable-ip-forwarding.png" alt-text="IP 転送を有効にする" border="true":::

## <a name="create-public-and-private-virtual-machines"></a>パブリックおよびプライベートの仮想マシンを作成する

仮想ネットワーク内にパブリック VM とプライベート VM を作成します。 後の手順でこれらを使用して、Azure によって **Public** サブネット トラフィックが NVA を介して **Private** サブネットにルーティングされることを確認します。


### <a name="public-vm"></a>パブリック VM

1. ポータルの左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[仮想マシン]** を選択します。 
   
2. **[仮想マシンの作成]** の **[Basic]** タブに、値を入力するか選択します。

    | 設定 | 値                                          |
    |-----------------------|----------------------------------|
    | **プロジェクトの詳細** |  |
    | サブスクリプション | Azure サブスクリプションを選択します。 |
    | リソース グループ | **[myResourceGroup]** を選択します |
    | **インスタンスの詳細** |  |
    | 仮想マシン名 | 「**myVMPublic**」と入力します |
    | リージョン | **[(米国) 米国東部]** を選択します |
    | 可用性オプション | **[インフラストラクチャ冗長は必要ありません]** を選択します |
    | Image | **[Windows Server 2019 Datacenter]** を選択します |
    | Azure Spot インスタンス | **[いいえ]**  を選択します |
    | サイズ | VM サイズを選択するか、既定の設定を使用します |
    | **管理者アカウント** |  |
    | ユーザー名 | ユーザー名を入力します |
    | Password | [パスワード] を入力します |
    | [パスワードの確認入力] | パスワードを再入力します |
    | **受信ポートの規則** |    |
    | パブリック受信ポート | **[なし]** を選択します。 |
    |

3. **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。
  
4. [ネットワーク] タブで、次を選択または入力します。

    | 設定 | 値 |
    |-|-|
    | **ネットワーク インターフェイス** |  |
    | 仮想ネットワーク | **[myVirtualNetwork]** を選択します。 |
    | Subnet | **[Public]** を選択します |
    | パブリック IP | **[なし]** を選択します |
    | NIC ネットワーク セキュリティ グループ | **[Basic]** を選択します|
    | パブリック受信ポート ネットワーク | **[なし]** を選択します。 |
   
5. **[Review + create]\(確認と作成\)** タブを選択するか、ページの下部にある青色の **[Review + create]\(確認と作成\)** ボタンを選択します。
  
6. 設定を確認し、 **[作成]** を選択します。

### <a name="private-vm"></a>プライベート VM

1. ポータルの左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[仮想マシン]** を選択します。 
   
2. **[仮想マシンの作成]** の **[Basic]** タブに、値を入力するか選択します。

    | 設定 | 値                                          |
    |-----------------------|----------------------------------|
    | **プロジェクトの詳細** |  |
    | サブスクリプション | Azure サブスクリプションを選択します。 |
    | リソース グループ | **[myResourceGroup]** を選択します |
    | **インスタンスの詳細** |  |
    | 仮想マシン名 | 「**myVMPrivate**」と入力します |
    | リージョン | **[(米国) 米国東部]** を選択します |
    | 可用性オプション | **[インフラストラクチャ冗長は必要ありません]** を選択します |
    | Image | **[Windows Server 2019 Datacenter]** を選択します |
    | Azure Spot インスタンス | **[いいえ]**  を選択します |
    | サイズ | VM サイズを選択するか、既定の設定を使用します |
    | **管理者アカウント** |  |
    | ユーザー名 | ユーザー名を入力します |
    | Password | [パスワード] を入力します |
    | [パスワードの確認入力] | パスワードを再入力します |
    | **受信ポートの規則** |    |
    | パブリック受信ポート | **[なし]** を選択します。 |
    |

3. **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。
  
4. [ネットワーク] タブで、次を選択または入力します。

    | 設定 | 値 |
    |-|-|
    | **ネットワーク インターフェイス** |  |
    | 仮想ネットワーク | **[myVirtualNetwork]** を選択します。 |
    | Subnet | **[Private]** を選択します |
    | パブリック IP | **[なし]** を選択します |
    | NIC ネットワーク セキュリティ グループ | **[Basic]** を選択します|
    | パブリック受信ポート ネットワーク | **[なし]** を選択します。 |
   
5. **[Review + create]\(確認と作成\)** タブを選択するか、ページの下部にある青色の **[Review + create]\(確認と作成\)** ボタンを選択します。
  
6. 設定を確認し、 **[作成]** を選択します。

## <a name="route-traffic-through-an-nva"></a>NVA を経由するトラフィックのルーティング

### <a name="sign-in-to-private-vm"></a>プライベート VM にサインインする

1. [Azure portal](https://portal.azure.com) に移動して、プライベート VM を管理します。 **[仮想マシン]** を検索して選択します。

2. プライベート仮想マシンの名前「**myVmPrivate**」を選択します。

3. VM のメニュー バーで、 **[接続]** を選択し、 **[Bastion]** を選択します。

4. **[接続]** ページで、青色の **[Bastion を使用する]** ボタンを選択します。

5. **[Bastion]** ページで、前に仮想マシン用に作成したユーザー名とパスワードを入力します。

6. **[接続]** を選択します。

### <a name="configure-firewall"></a>ファイアウォールを構成する

後の手順では、ルーティングのテストにトレース ルート ツールを使用します。 トレース ルートはインターネット制御メッセージ プロトコル (ICMP) を使用していますが、Windows ファイアウォールでは既定で拒否されます。 

ICMP が Windows ファイアウォールを通過できるようにします。

1. **myVMPrivate** の Bastion 接続で、管理特権を使用して PowerShell を開きます。

2. 次のコマンドを入力します。

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    このチュートリアルでは、トレース ルートを使用してルーティングをテストします。 運用環境では、Windows ファイアウォール経由の ICMP はお勧めしません。

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>myVMNVA 内で IP 転送を有効にする

Azure を使用して VM のネットワーク インターフェイスの [IP 転送を有効にしました](#turn-on-ip-forwarding)。 仮想マシンのオペレーティング システムでも、ネットワーク トラフィックを転送する必要があります。 

これらのコマンドを使用して、**myVMNVA** の IP 転送を有効にします。

1. **myVMPrivate** VM の PowerShell から、**myVMNVA** VM へのリモート デスクトップを開きます。

    ```powershell
    mstsc /v:myvmnva
    ```

2. **myVMNVA** VM の PowerShell から、次のコマンドを入力して IP 転送を有効にします。

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

3. **myVMNVA** を再起動します。

    ```powershell
    Restart-Computer
    ```

4. **myVMNVA** の再起動後、**myVMPublic** へのリモート デスクトップ セッションを作成します。 
    
    **myVMPrivate** に接続されている状態で、PowerShell を開いてこのコマンドを実行します。

    ```powershell
    mstsc /v:myvmpublic
    ```
5. **myVMPublic** のリモート デスクトップで、PowerShell を開きます。

6. 次のコマンドを入力して、ICMP が Windows ファイアウォールを通過できるようにします。

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>ネットワーク トラフィックのルーティングをテストする

まず、**myVMPublic** から **myVMPrivate** へのネットワーク トラフィックのルーティングをテストしましょう。

1. **myVMPublic** の PowerShell から、このコマンドを入力します。

    ```powershell
    tracert myvmprivate
    ```

    応答は次の例のようになります。

    ```powershell
    Tracing route to myvmprivate.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

      1     1 ms     *        2 ms  myvmnva.internal.cloudapp.net [10.0.2.4]
      2     2 ms     1 ms     1 ms  myvmprivate.internal.cloudapp.net [10.0.1.4]

    Trace complete.
    ```

    * 最初のホップ先が 10.0.2.4 であることを確認できます。これは、**myVMNVA** のプライベート IP アドレスです。 

    * 2 番目のホップ先は、**myVMPrivate** のプライベート IP アドレスである 10.0.1.4 です。 

    以前の手順で、このルートを **myRouteTablePublic** ルート テーブルに追加し、それを *Public* サブネットに関連付けました。 Azure によってトラフィックが *Private* サブネットに直接送信されず、NVA 経由で送信されました。

2. **myVMPublic** へのリモート デスクトップ セッションを閉じます。**myVMPrivate** にはまだ接続されたままです。

3. **myVMPrivate** の PowerShell を開き、このコマンドを入力します。

    ```powershell
    tracert myvmpublic
    ```

    このコマンドでは、*myVmPrivate* VM から *myVmPublic* VM へのネットワーク トラフィックのルーティングをテストします。 応答は次の例のようになります。

    ```cmd
    Tracing route to myvmpublic.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

      1     1 ms     1 ms     1 ms  myvmpublic.internal.cloudapp.net [10.0.0.4]

    Trace complete.
    ```

    Azure によって *myVMPrivate* から *myVMPublic* にトラフィックが直接ルーティングされていることがわかります。 既定では、サブネット間でトラフィックが直接ルーティングされます。

4. **myVMPrivate** への Bastion セッションを閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループが不要になったら、**myResourceGroup** とそこに含まれるすべてのリソースを削除します。

1. [Azure portal](https://portal.azure.com) に移動して、リソース グループを管理します。 **リソース グループ** を検索して選択します。

2. 目的のリソース グループの名前 (**myResourceGroup**) を選択します。

3. **[リソース グループの削除]** を選択します。

4. 確認ダイアログ ボックスで、 **[TYPE THE RESOURCE GROUP NAME]\(リソース グループ名を入力してください\)** に「**myResourceGroup**」と入力して、 **[削除]** を選択します。 


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のことを行いました。

* ルート テーブルを作成し、それをサブネットに関連付けました。
* トラフィックをパブリック サブネットからプライベート サブネットにルーティングする単純な NVA を作成しました。 

[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) から、便利なネットワーク機能を多く備えたさまざまな構成済み NVA をデプロイすることができます。 

ルーティングの詳細については、[ルーティングの概要](virtual-networks-udr-overview.md)と[ルート テーブルの管理](manage-route-table.md)に関する記事をご覧ください。

仮想ネットワークのネットワーク トラフィックをフィルター処理する方法を次の記事でご覧ください。
> [!div class="nextstepaction"]
> [チュートリアル: Azure portal を使用してネットワーク セキュリティ グループでネットワーク トラフィックをフィルター処理する](tutorial-filter-network-traffic.md)
