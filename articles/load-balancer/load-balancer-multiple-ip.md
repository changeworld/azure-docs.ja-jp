---
title: 'チュートリアル: 複数の IP 構成での負荷分散 - Azure portal'
titleSuffix: Azure Load Balancer
description: この記事では、Azure portal を使用した、プライマリとセカンダリの NIC 構成の間の負荷分散について説明します。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 08/08/2021
ms.custom: template-tutorial
ms.openlocfilehash: c0a5f441b27d379ff94db41fabfdf398a19ca667
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254667"
---
# <a name="tutorial-load-balance-multiple-ip-configurations-using-the-azure-portal"></a>チュートリアル: Azure portal を使用した複数の IP 構成での負荷分散 

複数の Web サイトをホストするために、仮想マシンに関連付けられている別のネットワーク インターフェイスを使用できます。 Azure Load Balancer は、Web サイトの高可用性をサポートするための負荷分散の配置をサポートしています。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 仮想ネットワーク、サブネット、および NAT ゲートウェイを作成し、構成する。
> * Windows サーバー仮想マシンを 2 つ作成する
> * 仮想マシンごとにセカンダリ NIC とネットワーク構成を作成する
> * 各仮想マシンに 2 つのインターネット インフォメーション サーバー (IIS) Web サイトを作成する
> * Web サイトをネットワーク構成にバインドする
> * Azure Load Balancer を作成し、構成する
> * ロード バランサーをテストする

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-virtual-network"></a>Create virtual network

このセクションでは、ロード バランサーおよび仮想マシン用の仮想ネットワークを作成します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**仮想ネットワーク**」と入力します。 検索結果で、 **[仮想ネットワーク]** を選択します。

3. **[仮想ネットワーク]** で **[+ 作成]** を選択します。

4. **[仮想ネットワークの作成]** の **[基本]** タブで次の情報を入力または選択します。

    | **設定**          | **Value**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **プロジェクトの詳細**  |                                                                 |
    | サブスクリプション     | Azure サブスクリプションを選択します。                                  |
    | リソース グループ   | **[新規作成]** を選択します。 </br> **[名前]** に「**TutorialLBIP-rg**」と入力します。 </br> **[OK]** を選択します。 |
    | **インスタンスの詳細** |                                                                 |
    | 名前             | 「**myVNet**」と入力します                                    |
    | リージョン           | **[(ヨーロッパ) 西ヨーロッパ]** を選択します |

5. **[IP アドレス]** タブを選択するか、ページの下部にある **[Next: IP Addresses]\(次へ: IP アドレス\)** ボタンを選択します。

6. **[IP アドレス]** タブで、次の情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | IPv4 アドレス空間 | 「**10.1.0.0/16**」と入力します。 |

7. **[サブネット名]** で、 **[default]\(既定\)** という単語を選択します。

8. **[サブネットの編集]** に次の情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | サブネット名 | 「**myBackendSubnet**」と入力します |
    | サブネットのアドレス範囲 | 「**10.1.0.0/24**」と入力します。 |

9. **[保存]** を選択します。

10. **[セキュリティ]** タブをクリックします。

11. **[BastionHost]** で **[有効にする]** を選択します。 この情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | 要塞名 | 「**myBastionHost**」と入力します |
    | AzureBastionSubnet のアドレス空間 | 「**10.1.1.0/27**」 と入力します |
    | パブリック IP アドレス | **[新規作成]** を選択します。 </br> **[名前]** に「**myBastionIP**」と入力します。 </br> **[OK]** を選択します。 |


12. **[確認と作成]** タブを選択するか、 **[確認と作成]** ボタンを選択します。

13. **［作成］** を選択します

## <a name="create-nat-gateway"></a>NAT ゲートウェイの作成

このセクションでは、仮想ネットワーク内のリソースの送信インターネット アクセス用の NAT ゲートウェイを作成します。 

1. ポータルの上部にある検索ボックスに、「**NAT ゲートウェイ**」と入力します。 検索結果から **[NAT ゲートウェイ]** を選択します。

2. **[NAT ゲートウェイ]** で、 **[+ 作成]** を選択します。

3. **[ネットワーク アドレス変換 (NAT) ゲートウェイを作成します]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** |   |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[TutorialLBIP-rg]** を選択します。 |
    | **インスタンスの詳細** |    |
    | NAT ゲートウェイ名 | 「**myNATgateway**」と入力します。 |
    | 可用性ゾーン | **[なし]** を選択します。 |
    | アイドル タイムアウト (分) | 「**15**」と入力します。 |

4. **[送信 IP]** タブを選択するか、ページの下部にある **[次へ: 送信 IP]** ボタンを選択します。

5. **[送信 IP]** で、 **[パブリック IP アドレス]** の横にある **[新しいパブリック IP アドレスの作成]** を選択します。

6. **[パブリック IP アドレスの追加]** の **[名前]** に「**myNATgatewayIP**」と入力します。

7. **[OK]** を選択します。

8. **[サブネット]** タブを選択するか、ページ下部にある **[次へ: サブネット]** ボタンを選択します。

9. **[サブネット]** タブの **[仮想ネットワーク]** で **[myVNet]** を選択します。

10. **[サブネット名]** で **[myBackendSubnet]** を選択します。

11. ページ下部にある青色の **[確認と作成]** ボタンを選択するか、 **[確認と作成]** タブを選択します。

12. **［作成］** を選択します

## <a name="create-virtual-machines"></a>仮想マシンを作成する

このセクションでは、IIS web サイトをホストする 2 つの仮想マシンを作成します。

1. ポータルの上部にある検索ボックスに、「**仮想マシン**」と入力します。 検索結果から **[仮想マシン]** を選択します。

2. **[仮想マシン]** で、 **[+ 作成]** 、 **[+ 仮想マシン]** の順に選択します。

3. **[仮想マシンの作成]** で、次の情報を入力または選択します。

    | 設定 | [値]                                          |
    |-----------------------|----------------------------------|
    | **プロジェクトの詳細** |  |
    | サブスクリプション | Azure サブスクリプションを選択します。 |
    | リソース グループ | **[TutorialLBIP-rg]** を選択します |
    | **インスタンスの詳細** |  |
    | 仮想マシン名 | 「**myVM1**」と入力します |
    | リージョン | **[(ヨーロッパ) 西ヨーロッパ]** を選択します |
    | 可用性オプション | **[可用性ゾーン]** を選択します |
    | 可用性ゾーン | **[1]** を選択します |
    | Image | **[Windows Server 2019 Datacenter - Gen1]** を選択します |
    | Azure Spot インスタンス | 既定値のオフのままにします。 |
    | サイズ | VM サイズを選択するか、既定の設定を使用します |
    | **管理者アカウント** |  |
    | ユーザー名 | ユーザー名を入力します |
    | Password | [パスワード] を入力します |
    | [パスワードの確認入力] | パスワードを再入力します |
    | **受信ポートの規則** |  |
    | パブリック受信ポート | **[なし]** を選択します |

3. **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。
  
4. [ネットワーク] タブで、次を選択または入力します。

    | 設定 | [値] |
    |-|-|
    | **ネットワーク インターフェイス** |  |
    | 仮想ネットワーク | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | パブリック IP | **[なし]** を選択します。 |
    | NIC ネットワーク セキュリティ グループ | **[Advanced] \(詳細設定)** を選択します|
    | ネットワーク セキュリティ グループを構成する | **[新規作成]** を選択します。 </br> **[ネットワーク セキュリティ グループの作成]** で、 **[名前]** に「**myNSG**」と入力します。 </br> **[受信規則]** で、[ **+ 受信規則の追加]** を選択します。 </br> **[サービス]** で、 **[HTTP]** を選択します。 </br> **[優先度]** に「**100**」と入力します。 </br> **[名前]** に「**myNSGrule**」と入力します。 </br> **[追加]** を選択します。 </br> **[OK]** を選択します。 |
   
7. **[Review + create]\(レビュー + 作成\)** を選択します。 
  
8. 設定を確認し、 **[作成]** を選択します。

9. 手順 1 から 8 に従って、別の VM を作成します。次の値を使用し、他の設定はすべて **myVM1** と同じにします。

    | 設定 | VM 2 |
    | ------- | ---- |
    | 名前 |  **myVM2** |
    | 可用性ゾーン | **2** |
    | ネットワーク セキュリティ グループ | 既存の **[myNSG]** を選択します |

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-secondary-network-configurations"></a>セカンダリのネットワーク構成を作成する

このセクションでは、各仮想マシンの既存の NIC のプライベート IP アドレスを **[静的]** に変更します。 次に、**静的** プライベート IP アドレス構成を使用して、各仮想マシンに新しい NIC リソースを追加します。

1. ポータルの上部にある検索ボックスに、「**仮想マシン**」と入力します。 検索結果から **[仮想マシン]** を選択します。

2. **[myVM1]** を選択します。

3. この仮想マシンが実行されている場合は、停止します。 

4. **[設定]** の **[ネットワーク]** を選択します。

5. **[ネットワーク]** ページで、 **[ネットワーク インターフェイス]** の横にあるネットワーク インターフェイスの名前を選択します。 ネットワーク インターフェイスは VM の名前で始まり、ランダムな数が割り当てられています。 この例では、**myVM1266** です。

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-nic.png" alt-text="Azure portal の myVM1 のネットワーク構成のスクリーンショット。":::

6. そのネットワーク インターフェイス ページの **[設定]** で、 **[IP 構成]** を選択します。

7. **[IP 構成]** で、 **[ipconfig1]** を選択します。

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-ipconfig1.png" alt-text="myVM1 のネットワーク インターフェイス構成のスクリーンショット。":::

8. **ipconfig1** 構成の **[割り当て]** で **[静的]** を選択します。

9. **[保存]** を選択します。

10. **myVM1** の **[概要]** ページに戻ります。

11. **[設定]** の **[ネットワーク]** を選択します。

12. **[ネットワーク]** ページで、 **[ネットワーク インターフェイスの接続]** を選択します。

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-attach-nic.png" alt-text="myVM1 のネットワーク インターフェイスの接続のスクリーンショット。":::

13. **[ネットワーク インターフェイスの接続]** で、 **[Create and attach network interface]\(ネットワーク インターフェイスの作成と接続\)** を選択します。

14. **[ネットワーク インターフェイスの作成]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** |   |
    | Resource group | **[TutorialLBIP-rg]** を選択します。 |
    | **ネットワーク インターフェイス** |  |
    | 名前 | 「**myVM1NIC2** 」と入力します。 |
    | Subnet | **[myBackendSubnet (10.1.0.0/24)]** を選択します。 |
    | NIC ネットワーク セキュリティ グループ | **[Advanced] \(詳細設定)** を選択します。 |
    | ネットワーク セキュリティ グループを構成する | **[myNSG]** を選択します。 |
    | プライベート IP アドレスの割り当て | **[静的]** を選択します。 |
    | プライベート IP アドレス | 「**10.1.0.6**」と入力します。 |

15. **[作成]** を選択します。 

16. 仮想マシンを開始します。

17. **myVM2** に対して、手順 1 から 16 を繰り返します。ただし、次の情報を置き換えます。

    | Setting | myVM2 |
    | ------  | ----- |
    | 名前 | **myVM2NIC2** |
    | プライベート IP アドレス | **10.1.0.7** |

## <a name="configure-virtual-machines"></a>仮想マシンの構成

このセクションでは、Azure Bastion を使用して **myVM1** と **myVM2** を接続し、セカンダリ ネットワーク構成を設定します。 セカンダリ ネットワーク構成用に、ゲートウェイにルートを追加します。 次に、各仮想マシンに IIS をインストールし、仮想マシンのホスト名を表示するように Web サイトをカスタマイズします。

1. ポータルの上部にある検索ボックスに、「**仮想マシン**」と入力します。 検索結果から **[仮想マシン]** を選択します。

2. **[myVM1]** を選択します。

3. **myVM1** を起動します。

4. **[概要]** で **[接続]** 、 **[Bastion]** の順に選択します。

5. **[Bastion を使用する]** を選択します。

6. 仮想マシンの作成時に入力したユーザー名とパスワードを入力します。

7. Bastion でクリップボードを使用するには、 **[許可]** を選択します。

8. サーバーのデスクトップで、[スタート] > [Windows 管理ツール] > [Windows PowerShell] > [Windows PowerShell] の順に移動します。

9. PowerShell ウィンドウで `route print` コマンドを実行します。これにより、2 つのネットワーク インターフェイスが接続された仮想マシンについて、次のような出力が返されます。

    ```console
    ===========================================================================
    Interface List
      6...00 22 48 86 00 53 ......Microsoft Hyper-V Network Adapter #2
     13...00 22 48 83 0b da ......Microsoft Hyper-V Network Adapter #3
      1...........................Software Loopback Interface 1
    ===========================================================================
    ```
    この例で、**Microsoft Hyper-V Network Adapter #3** (インターフェイス 13) は、既定のゲートウェイが割り当てられていないセカンダリ ネットワーク インターフェイスです。

10. PowerShell ウィンドウで `ipconfig /all` コマンドを実行して、セカンダリ ネットワーク インターフェイスに割り当てられている IP アドレスを確認します。 この例では、インターフェイス 13 には 10.1.0.6 が割り当てられています。 セカンダリ ネットワーク インターフェイスに対して、既定のゲートウェイ アドレスは返されません。

11. サブネットの外部にあるアドレスに宛てたすべてのトラフィックをゲートウェイにルーティングするには、次のコマンドを実行します。

    ```console
    route -p add 0.0.0.0 MASK 0.0.0.0 10.1.0.1 METRIC 5015 IF 13
    ```

    この例の **10.1.0.1** は、前に作成した仮想ネットワークの既定のゲートウェイです。

12. PowerShell ウィンドウで次のコマンドを実行して、IIS とテスト Web サイトをインストールし、構成します。

    ```powershell
    ## Install IIS and the management tools. ##
    Install-WindowsFeature -Name Web-Server -IncludeManagementTools

    ## Set the binding for the Default website to 10.1.0.4:80. ##
    $para1 = @{
        Name = 'Default Web Site'
        BindingInformation = '10.1.0.4:80:'
        Protocol = 'http'
    }
    New-IISSiteBinding @para1

    ## Remove the default site binding. ##
    $para2 = @{
        Name = 'Default Web Site'
        BindingInformation = '*:80:'
    }
    Remove-IISSiteBinding @para2 -Force

    ## Remove the default htm file. ##
    Remove-Item c:\inetpub\wwwroot\iisstart.htm

    ## Add a new htm file that displays the Contoso website. ##
    $para3 = @{
        Path = 'c:\inetpub\wwwroot\iisstart.htm'
        Value = $("Hello World from www.contoso.com" + "-" + $env:computername)
    }
    Add-Content @para3

    ## Create folder to host website. ##
    $para4 = @{
        Path = 'c:\inetpub\'
        Name = 'fabrikam'
        Type = 'directory'
    }
    New-Item @para4

     ## Create a new website and site binding for the second IP address 10.1.0.6. ##
    $para5 = @{
        Name = 'Fabrikam'
        PhysicalPath = 'c:\inetpub\fabrikam'
        BindingInformation = '10.1.0.6:80:'
    }
    New-IISSite @para5

    ## Add a new htm file that displays the Fabrikam website. ##
    $para6 = @{
        Path = 'C:\inetpub\fabrikam\iisstart.htm'
        Value = $("Hello World from www.fabrikam.com" + "-" + $env:computername)

    }
    Add-Content @para6

    ```
13. **myVM1** への Bastion 接続を閉じます。

14. **myVM2** に対して手順 1 から 13 を繰り返します。 **myVM2** には、以下の PowerShell コードを IIS インストール用に使用します。

    ```powershell
    ## Install IIS and the management tools. ##
    Install-WindowsFeature -Name Web-Server -IncludeManagementTools

    ## Set the binding for the Default website to 10.1.0.5:80. ##
    $para1 = @{
        Name = 'Default Web Site'
        BindingInformation = '10.1.0.5:80:'
        Protocol = 'http'
    }
    New-IISSiteBinding @para1

    ## Remove the default site binding. ##
    $para2 = @{
        Name = 'Default Web Site'
        BindingInformation = '*:80:'
    }
    Remove-IISSiteBinding @para2

    ## Remove the default htm file. ##
    Remove-Item C:\inetpub\wwwroot\iisstart.htm

    ## Add a new htm file that displays the Contoso website. ##
    $para3 = @{
        Path = 'c:\inetpub\wwwroot\iisstart.htm'
        Value = $("Hello World from www.contoso.com" + "-" + $env:computername)
    }
    Add-Content @para3

    ## Create folder to host website. ##
    $para4 = @{
        Path = 'c:\inetpub\'
        Name = 'fabrikam'
        Type = 'directory'
    }
    New-Item @para4

    ## Create a new website and site binding for the second IP address 10.1.0.7. ##
    $para5 = @{
        Name = 'Fabrikam'
        PhysicalPath = 'c:\inetpub\fabrikam'
        BindingInformation = '10.1.0.7:80:'
    }
    New-IISSite @para5

    ## Add a new htm file that displays the Fabrikam website. ##
    $para6 = @{
        Path = 'C:\inetpub\fabrikam\iisstart.htm'
        Value = $("Hello World from www.fabrikam.com" + "-" + $env:computername)
    }
    Add-Content @para6

    ```

## <a name="create-load-balancer"></a>ロード バランサーの作成

このセクションでは、仮想マシンの負荷分散を行うゾーン冗長ロード バランサーを作成します。 

ゾーン冗長では、1 つまたは複数の可用性ゾーンで障害が発生しても対応可能であり、リージョン内に正常なゾーンが 1 つでも残っていれば、データ パスは存続します。

ロード バランサーの作成中に、次の構成を行います。

* 2 つのフロントエンド IP アドレス (Web サイトごとに 1 つ)。
* バックエンド プール
* インバウンドの負荷分散規則

1. ポータルの上部にある検索ボックスに、「**ロード バランサー**」と入力します。 検索結果で **[ロード バランサー]** を選択します。

2. **[ロード バランサー]** ページで、 **[作成]** を選択します。

3. **[ロード バランサーの作成]** ページの **[基本]** タブで、次の情報を入力または選択します。 

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | **プロジェクトの詳細** |   |
    | サブスクリプション               | サブスクリプションを選択します。    |    
    | Resource group         | **[TutorialLBIP-rg]** を選択します。 |
    | **インスタンスの詳細** |   |
    | 名前                   | 「**myLoadBalancer**」と入力します                                   |
    | リージョン         | **[(ヨーロッパ) 西ヨーロッパ]** を選択します。                                        |
    | Type          | **[パブリック]** を選択します。                                        |
    | SKU           | 既定値 **[標準]** のままにします。 |
    | レベル          | **[地域]** は既定値のままにします。 |

4. ページ下部にある **[次へ: フロントエンド IP の構成]** を選択します。

5. **[フロントエンド IP 構成]** で、 **[+ フロントエンド IP の追加]** を選択します。

6. **[名前]** に「**Frontend-contoso**」と入力します。

7. **[IP バージョン]** で **[IPv4]** を選択します。

    > [!NOTE]
    > IPv6 は現在、ルーティングの優先順位またはリージョン間の負荷分散 (グローバル階層) ではサポートされていません。

8. **[IP の種類]** として **[IP アドレス]** を選択します。

    > [!NOTE]
    > IP プレフィックスの詳細については、[Azure パブリック IP アドレス プレフィックス](../virtual-network/ip-services/public-ip-address-prefix.md)に関するページを参照してください。

9. **[パブリック IP アドレス]** で **[新規作成]** を選択します。

10. **[パブリック IP アドレスの追加]** で、 **[名前]** に「**myPublicIP-contoso**」と入力します。

11. **[可用性ゾーン]** で、 **[ゾーン冗長]** を選択します。

    > [!NOTE]
    > [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) があるリージョンでは、ゾーンなし (既定のオプション)、特定のゾーン、またはゾーン冗長を選択できます。 この選択は、特定のドメイン障害要件によって異なる場合があります。 Availability Zones がないリージョンでは、このフィールドは表示されません。 </br> 可用性ゾーンの詳細については、[可用性ゾーンの概要](../availability-zones/az-overview.md)に関するページを参照してください。

12. **[ルーティングの優先順位]** は、既定値の **[Microsoft ネットワーク]** のままにします。

13. **[OK]** を選択します。

14. **[追加]** を選択します。

14. **[+ フロントエンド IP の追加]** を選択します。

15. **[名前]** に「**Frontend-fabrikam**」と入力します。

7. **[IP バージョン]** で **[IPv4]** を選択します。

8. **[IP の種類]** として **[IP アドレス]** を選択します。

9. **[パブリック IP アドレス]** で **[新規作成]** を選択します。

10. **[パブリック IP アドレスの追加]** で、 **[名前]** に「**myPublicIP-fabrikam**」と入力します。

11. **[可用性ゾーン]** で、 **[ゾーン冗長]** を選択します。

14. **[追加]** を選択します。

15. ページ下部で **[次へ: バックエンド プール]** を選択します。

16. **[バックエンド プール]** タブで、 **[+ バックエンド プールの追加]** を選択します。

17. **[バックエンド プールの追加]** の **[名前]** に「**myBackendPool-contoso**」と入力します。

18. **[仮想ネットワーク]** で **[myVNet]** を選択します。

19. **[Backend Pool Configuration]\(バックエンド プールの構成\)** で **[NIC]** を選択します。

20. **[IP バージョン]** で **[IPv4]** を選択します。

21. **[仮想マシン]** で、 **[+ 追加]** を選択します。

22. **ipconfig1 (10.1.0.4)** と **ipconfig1 (10.1.0.5)** に対応する **[myVM1]** と **[myVM2]** を選択します。

23. **[追加]** を選択します。

21. **[追加]** を選択します。

22. **[バックエンド プールの追加]** を選択します。

23. **[バックエンド プールの追加]** の **[名前]** に「**myBackendPool-fabrikam**」と入力します。

24. **[仮想ネットワーク]** で **[myVNet]** を選択します。

19. **[Backend Pool Configuration]\(バックエンド プールの構成\)** で **[NIC]** を選択します。

20. **[IP バージョン]** で **[IPv4]** を選択します。

21. **[仮想マシン]** で、 **[+ 追加]** を選択します。

22. **ipconfig1 (10.1.0.6)** と **ipconfig1 (10.1.0.7)** に対応する **[myVM1]** と **[myVM2]** を選択します。

23. **[追加]** を選択します。

21. **[追加]** を選択します。

22. ページ下部にある **[次へ: 受信規則]** ボタンを選択します。

23. **[受信規則]** タブの **[負荷分散規則]** で、 **[+ 負荷分散規則の追加]** を選択します。

24. **[負荷分散規則の追加]** で、次の情報を入力または選択します。

    | 設定 | [値] |
    | ------- | ----- |
    | 名前 | 「**myHTTPRule-contoso**」と入力します。 |
    | IP バージョン | **[IPv4]** を選択します。 |
    | フロントエンド IP アドレス | **[Frontend-contoso]** を選択します。 |
    | Protocol | **[TCP]** を選択します。 |
    | Port | 「**80**」と入力します。 |
    | バックエンド ポート | 「**80**」と入力します。 |
    | バックエンド プール | **[myBackendPool-contoso]** を選択します。 |
    | 正常性プローブ | **[新規作成]** を選択します。 </br> **[名前]** に「**myHealthProbe-contoso**」と入力します。 </br> **[プロトコル]** で、 **[HTTP]** を選択します。 </br> 残りの部分は既定値のままにし、 **[OK]** を選択します。 |
    | セッション永続化 | **[なし]** を選択します。 |
    | アイドル タイムアウト (分) | 「**15**」を入力または選択します。 |
    | TCP リセット | **[Enabled]** を選択します。 |
    | フローティング IP | **[無効]** をクリックします。 |
    | アウトバウンド送信元ネットワーク アドレス変換 (SNAT) | 既定値の **[(推奨) アウトバウンド規則を使用して、バックエンド プールのメンバーがインターネットにアクセスできるようにします。]** のままにします。 |

25. **[追加]** を選択します。

26. **[負荷分散規則の追加]** を選択します。

27. **[負荷分散規則の追加]** で、次の情報を入力または選択します。

    | 設定 | [値] |
    | ------- | ----- |
    | 名前 | 「**myHTTPRule-fabrikam**」と入力します。 |
    | IP バージョン | **[IPv4]** を選択します。 |
    | フロントエンド IP アドレス | **[Frontend-fabrikam]** を選択します。 |
    | Protocol | **[TCP]** を選択します。 |
    | Port | 「**80**」と入力します。 |
    | バックエンド ポート | 「**80**」と入力します。 |
    | バックエンド プール | **[myBackendPool-fabrikam]** を選択します。 |
    | 正常性プローブ | **[新規作成]** を選択します。 </br> **[名前]** に「**myHealthProbe-fabrikam**」と入力します。 </br> **[プロトコル]** で、 **[HTTP]** を選択します。 </br> 残りの部分は既定値のままにし、 **[OK]** を選択します。 |
    | セッション永続化 | **[なし]** を選択します。 |
    | アイドル タイムアウト (分) | 「**15**」を入力または選択します。 |
    | TCP リセット | **[Enabled]** を選択します。 |
    | フローティング IP | **[無効]** をクリックします。 |
    | アウトバウンド送信元ネットワーク アドレス変換 (SNAT) | 既定値の **[(推奨) アウトバウンド規則を使用して、バックエンド プールのメンバーがインターネットにアクセスできるようにします。]** のままにします。 |

25. **[追加]** を選択します。

26. ページ下部にある青色の **[確認と作成]** ボタンを選択します。

27. **［作成］** を選択します

    > [!NOTE]
    > この例では、送信インターネット アクセスを提供する NAT ゲートウェイを作成しました。 構成の [アウトバウンド規則] タブは省略可能であり、NAT ゲートウェイでは不要なためバイパスされました。 Azure NAT ゲートウェイの詳細については、「[Virtual Network NAT とは](../virtual-network/nat-gateway/nat-overview.md)」を参照してください。
    > Azure でのアウトバウンド接続の詳細については、[アウトバウンド接続の送信元ネットワーク アドレス変換 (SNAT)](../load-balancer/load-balancer-outbound-connections.md) に関するページを参照してください。

## <a name="test-load-balancer"></a>ロード バランサーをテストする

このセクションでは、各 Web サイトのパブリック IP アドレスを検出します。 ブラウザーに IP を入力して、前に作成した Web サイトをテストします。

1. ポータルの上部にある検索ボックスに、「**パブリック IP**」と入力します。 検索結果から **[パブリック IP アドレス]** を選択します。

2. **[myPublicIP-contoso]** を選択します。

3. **myPublicIP-contoso** の [概要] ページの **IP アドレス** をコピーします。

    :::image type="content" source="./media/load-balancer-multiple-ip/public-ip-contoso.png" alt-text="myPublicIP-contoso のパブリック IP アドレスのスクリーンショット。":::

4. Web ブラウザーを開いて、アドレス バーにそのパブリック IP アドレスを貼り付けます。

    :::image type="content" source="./media/load-balancer-multiple-ip/test-contoso.png" alt-text="Web ブラウザーでの contoso の Web サイトのスクリーンショット。":::

5. **[パブリック IP アドレス]** に戻ります。 **[myPublicIP-fabrikam]** を選択します。

6. **myPublicIP-fabrikam** の [概要] ページの **IP アドレス** をコピーします。

    :::image type="content" source="./media/load-balancer-multiple-ip/public-ip-fabrikam.png" alt-text="myPublicIP-fabrikam のパブリック IP アドレスのスクリーンショット。":::

7. Web ブラウザーを開いて、アドレス バーにそのパブリック IP アドレスを貼り付けます。

    :::image type="content" source="./media/load-balancer-multiple-ip/test-fabrikam.png" alt-text="Web ブラウザーでの fabrikam の Web サイトのスクリーンショット。":::

8. ロード バランサーをテストするには、ブラウザーを更新するか、いずれかの仮想マシンをシャットダウンします。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

今後このアプリケーションを使い続ける予定がなければ、次の手順を使用して仮想マシンとロード バランサーを削除します。

1. ポータル上部の [検索] ボックスに「**リソース グループ**」と入力します。  検索結果から **[リソース グループ]** を選択します。

2. **[リソース グループ]** で、 **[TutorialLBPF-rg]** を選択します。

3. **[リソース グループの削除]** を選択します。

4. **[リソース グループ名を入力してください:]** に「**TutorialLBIP-rg**」と入力します。 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

次の記事に進んで、リージョン間ロード バランサーの作成方法を学習してください。

> [!div class="nextstepaction"]
> [Azure portal を使用してリージョン間ロード バランサーを作成する](tutorial-cross-region-portal.md)