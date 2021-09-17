---
title: 'チュートリアル: Azure Route Server と Quagga ネットワーク仮想アプライアンスの間のピアリングを構成する'
description: このチュートリアルでは、Azure Route Server を構成し、Quagga ネットワーク仮想アプライアンスとピアリングする方法について説明します。
author: duongau
ms.author: duau
ms.service: route-server
ms.topic: tutorial
ms.date: 08/23/2021
ms.custom: template-tutorial
ms.openlocfilehash: cbeee212b8ed7284780083166af34285f2d44a5e
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823955"
---
# <a name="tutorial-configure-peering-between-azure-route-server-and-quagga-network-virtual-appliance"></a>チュートリアル: Azure Route Server と Quagga ネットワーク仮想アプライアンスの間のピアリングを構成する

このチュートリアルでは、Azure Route Server を仮想ネットワークにデプロイし、Quagga ネットワーク仮想アプライアンスとの BGP ピアリング接続を確立する方法について説明します。 5 つのサブネットから成る仮想ネットワークをデプロイします。 1 つのサブネットは Azure Route Server 専用、もう 1 つのサブネットは Quagga NVA 専用となります。 Quagga NVA は、Route Server とルートを交換するように構成されます。 最後に、Azure Route Server と Quagga NVA でルートが適切に交換されるかどうかをテストします。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * 5 つのサブネットから成る仮想ネットワークの作成
> * Azure Route Server のデプロイ
> * Quagga を実行する仮想マシンのデプロイ
> * Route Server のピアリングの構成
> * 学習したルートの確認

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

Azure Route Server と Quagga NVA の両方をデプロイする仮想ネットワークが必要です。 各デプロイには、それぞれ独自の専用サブネットがあります。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 画面の左上で、 **[リソースの作成]** を選択し、「**Virtual Network**」を検索します。 **[作成]** を選択します。

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-new-virtual-network.png" alt-text="新しい仮想ネットワーク リソースの作成のスクリーンショット。":::

1. *[仮想ネットワークの作成]* の *[基本]* タブで、次の情報を入力するか選択してから、 **[次へ : IP アドレス >]** を選択します。

    | 設定 | 値 |
    | -------- | ----- | 
    | サブスクリプション | このデプロイのサブスクリプションを選択します。 |
    | Resource group | このデプロイ向けに、既存のものを選択するか、新しいリソース グループを作成します。 | 
    | Name | 仮想ネットワークの名前を入力します。 このチュートリアルでは、*myVirtualNetwork* を使用します。
    | リージョン | この仮想ネットワークをデプロイするリージョンを選択します。 このチュートリアルでは、"*米国西部*" を使用します。

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/virtual-network-basics-tab.png" alt-text="仮想ネットワークの [基本] タブの設定のスクリーンショット。":::

1. **[IP アドレス]** タブで、"*仮想ネットワークのアドレス空間*" を **10.1.0.0/16** に構成します。 次の 5 つのサブネットを構成します。

    | サブネット名 | サブネットのアドレス範囲 |
    | ----------- | -------------------- |
    | RouteServerSubnet | 10.1.1.0/25 |
    | subnet1 | 10.1.2.0/24 |
    | subnet2 | 10.1.3.0/24 |
    | subnet3 | 10.1.4.0/24 |
    | GatewaySubnet | 10.1.5.0/24 |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/virtual-network-ip-addresses.png" alt-text="仮想ネットワークの IP アドレス設定のスクリーンショット。":::

1. **[確認と作成]** を選択し、検証に成功したら **[作成]** を選択します。

## <a name="create-the-azure-route-server"></a>Azure Route Server の作成

Route Server は、BGP ピアリング接続を使用した NVA との通信や仮想ネットワーク ルートの交換に使用されます。

1. https://aka.ms/routeserver にアクセスします。

1. **[+ Create new route server]\(+ 新しいルート サーバーの作成\)** を選択します。

   :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="ルート サーバーのランディング ページのスクリーンショット。":::

1. **[Create a Route Server]\(Route Server の作成\)** ページで、次の情報を入力または選択します。

    | 設定 | 値 |
    | -------- | ----- |
    | サブスクリプション | 前のセクションで作成した仮想と同じサブスクリプションを選択します。 | 
    | Resource group | 既存のリソース グループ *[myRouteServerRG]* を選択します。 |
    | Name | 「*myRouteServer*」という Route Server 名を入力します。 |
    | リージョン | **[米国西部]** リージョンを選択します。 |
    | Virtual Network | *[myVirtualNetwork]* 仮想ネットワークを選択します。 |
    | Subnet | 先に作成した *[RouteServerSubnet (10.1.0.0/25)]* を選択します。 |
    | パブリック IP アドレス | Route Server で使用するために新しく作成するか、既存の標準パブリック IP アドレスを選択します。 この IP アドレスにより、Route Server の構成が管理されるバックエンド サービスへの接続性が確保されます。 |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/route-server-basics-tab.png" alt-text="Route Server を作成するための [基本] タブのスクリーンショット。":::

1. **[確認と作成]** を選択し、検証に成功したら **[作成]** を選択します。 Route Server のデプロイには約 15 分かかります。

## <a name="create-quagga-network-virtual-appliance"></a>Quagga ネットワーク仮想アプライアンスの作成

Quagga ネットワーク仮想アプライアンスを構成するために、Linux 仮想マシンをデプロイする必要があります。

1. Azure portal から、 **[+ リソースの作成] > [コンピューティング] > [仮想マシン]** の順に選択します。 **[作成]** を選択します。

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-virtual-machine.png" alt-text="新しい仮想マシンの作成ページのスクリーンショット。":::

1. *[基本]* タブで、次の情報を入力または選択します。

    | 設定 | 値 |
    | -------- | ----- |
    | サブスクリプション | 先にデプロイした仮想ネットワークと同じサブスクリプションを選択します。 |
    | Resource group | 既存のリソース グループ *[myRouteServerRG]* を選択します。 |
    | 仮想マシン名 | 「**Quagga**」という名前を入力します。 |
    | リージョン | **[米国西部]** リージョンを選択します。 |
    | イメージ | **[Ubuntu 18.04 LTS - Gen 1]** を選択します。 |
    | サイズ | **[Standard_B2s - 2vcpus、4GiB memory]** を選択します。 |
    | 認証の種類 | **[パスワード]** を選択します |
    | ユーザー名 | 「*azureuser*」と入力します。 ユーザー名に *quagga* を使用しないでください。後の手順でセットアップ スクリプトが失敗してしまいます。 |
    | Password | 任意のパスワードを入力し、確認します。 |
    | パブリック受信ポート | **[選択したポートを許可する]** を選択します。 |
    | 受信ポートの選択 | **[SSH (22)]** を選択します。 |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-quagga-basics-tab.png" alt-text="新しい仮想マシンを作成するための [基本] タブのスクリーンショット。" lightbox="./media/tutorial-configure-route-server-with-quagga/create-quagga-basics-tab-expanded.png":::
    
1. **[ネットワーク]** タブで、次のネットワーク設定を選択します。

    | 設定 | 値 |
    | -------- | ----- |
    | Virtual Network | **[myVirtualNetwork]** を選択します。 |
    | Subnet | **[subnet3 (10.1.4.0/24)]** を選択します。 |
    | パブリック IP | 既定値のままにします。 |
    | NIC ネットワーク セキュリティ グループ | 既定値のままにします。 **Basic**。 |
    | パブリック受信ポート | 既定値のままにします。 **[選択したポートを許可する]** 。 |
    | 受信ポートの選択 | 既定値のままにします。 **[SSH (22)]** 。 |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/create-quagga-networking-tab.png" alt-text="新しい仮想マシンを作成するための [ネットワーク] タブのスクリーンショット。" lightbox="./media/tutorial-configure-route-server-with-quagga/create-quagga-networking-tab-expanded.png":::

1. **[確認と作成]** を選択し、検証に成功したら **[作成]** を選択します。 VM のデプロイには約 10 分かかります。

1. VM がデプロイされたら、Quagga VM のネットワーク設定に移動し、ネットワーク インターフェイスを選択します。

     :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/quagga-network-settings.png" alt-text="Quagga VM の [ネットワーク] ページのスクリーンショット。":::

1. *[設定]* の下の **[IP 構成]** を選択してから、 **[ipconfig1]** を選択します。

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/quagga-ip-configuration.png" alt-text="Quagga VM の [IP 構成] ページのスクリーンショット。":::

1. 割り当てを "*動的*" から **静的** に変更し、IP アドレスを *10.1.4.4* から **10.1.4.10** に変更します。 この IP は、後の手順で実行されるこの[スクリプト](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/route-server-quagga/scripts/quaggadeploy.sh)で使用されます。 別の IP アドレスを使用する場合は、スクリプト内の IP を必ず更新してください。 **[保存]** を選択して VM の IP 構成を更新します。

1. [Putty](https://www.putty.org/) を使用して、VM の作成に使用したパブリック IP アドレスと資格情報で Quagga VMに接続します。

1. ログインしたら、「`sudo su`」と入力しスーパー ユーザーに切り替えて、スクリプトの実行中にエラーが発生しないようにします。 この[スクリプト](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/route-server-quagga/scripts/quaggadeploy.sh)をコピーして Putty セッションに貼り付けます。 このスクリプトでは、その他のネットワーク設定と共に仮想マシンに Quagga が構成されます。 ネットワーク環境に合わせてスクリプトを更新してから、仮想マシンで実行してください。 スクリプトで設定が完了するまでに数分かかります。

## <a name="configure-route-server-peering"></a>Route Server のピアリングの構成

1. 前の手順で作成した Route Server に移動します。

1. *[設定]* で **[ピア]** を選択します。 次に、 **[+ 追加]** を選択し、新しいピアを追加します。

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/peers.png" alt-text="Route Server の [ピア] ページのスクリーンショット。":::

1. *[ピアの追加]* ページで、次の情報を入力してから、 **[追加]** を選択して構成を保存します。

    | 設定 | 値 |
    | ------- | ----- |
    | 名前 | このピアを識別する名前を入力します。 「**Quagga**」。 |
    | ASN | Quagga NVA の AS 番号を入力します。 「**65001**」は、スクリプトで定義されている AS 番号です。 |
    | [IPv4 アドレス] | Quagga NVA 仮想マシンのプライベート IP を入力します。 |

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/add-peer.png" alt-text="ピアの追加ページのスクリーンショット。":::

1. ピアを追加すると、[ピア] ページは次のようになります。

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/peer-configured.png" alt-text="構成済みのピアのスクリーンショット。":::
    
## <a name="check-learned-routes"></a>学習したルートの確認
1. Route Server により学習されたルートを確認するために、次のコマンドを使用します。

    ```azurepowershell-interactive
    $routes = @{
        RouteServerName = 'myRouteServer'
        ResourceGroupName = 'myRouteServerRG'
        PeerName = 'Quagga'
    }
    Get-AzRouteServerPeerLearnedRoute @routes | ft
    ```

    出力は次のようになります。 

    :::image type="content" source="./media/tutorial-configure-route-server-with-quagga/routes-learned.png" alt-text="Route Server により学習されたルートのスクリーンショット。":::

1. Quagga NVA により学習されたルートを確認するために、「`vtysh`」と入力してから「`show ip bgp`」と入力します。 出力は次のようになります。

    ```
    root@Quagga:/home/azureuser# vtysh

    Hello, this is Quagga (version 1.2.4).
    Copyright 1996-2005 Kunihiro Ishiguro, et al.

    Quagga# show ip bgp
    BGP table version is 0, local router ID is 10.1.4.10
    Status codes: s suppressed, d damped, h history, * valid, > best, = multipath,
                  i internal, r RIB-failure, S Stale, R Removed
    Origin codes: i - IGP, e - EGP, ? - incomplete

        Network          Next Hop            Metric LocPrf Weight Path
        10.1.0.0/16      10.1.1.4                               0 65515 i
                         10.1.1.5                               0 65515 i
    *> 10.100.1.0/24    0.0.0.0                  0         32768 i
    *> 10.100.2.0/24    0.0.0.0                  0         32768 i
    *> 10.100.3.0/24    0.0.0.0                  0         32768 i
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Route Server および関連するリソースがすべて不要になった場合は、**myRouteServerRG** リソース グループを削除できます。

:::image type="content" source="./media/tutorial-configure-route-server-with-quagga/delete-resource-group.png" alt-text="[リソース グループ削除] ボタンのスクリーンショット。":::

## <a name="next-steps"></a>次の手順

次の記事に進み、Route Server のトラブルシューティング方法をご覧ください。
> [!div class="nextstepaction"]
> [Route Server のトラブルシューティング](troubleshoot-route-server.md)
