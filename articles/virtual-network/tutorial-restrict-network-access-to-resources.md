---
title: PaaS リソースへのアクセスを制限する - チュートリアル - Azure portal
description: このチュートリアルでは、Azure portal を使用して仮想ネットワーク サービス エンドポイントで Azure Storage などの Azure リソースへのネットワーク アクセスを制限する方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 07/16/2021
ms.author: kumud
ms.openlocfilehash: a0d721e847d0e47358bfbeb640b9e9a6e6a551a3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463366"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>チュートリアル: Azure Portal を使用して仮想ネットワーク サービス エンドポイントで PaaS リソースへのネットワーク アクセスを制限する

仮想ネットワーク サービス エンドポイントを使うと、一部の Azure サービス リソースへのネットワーク アクセスを、仮想ネットワーク サブネットに制限できます。 また、リソースに対するインターネット アクセスを排除することもできます。 サービス エンドポイントにより、使用している仮想ネットワークからサポートされている Azure サービスへの直接接続が提供されるため、ご自身の仮想ネットワークのプライベート アドレス スペースを使用して、Azure サービスにアクセスできるようになります。 サービス エンドポイントを介して Azure リソースに送信されるトラフィックは、常に Microsoft Azure のバックボーン ネットワーク上に留まります。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 1 つのサブネットを含む仮想ネットワークを作成する
> * サブネットを追加し、サービス エンドポイントを有効にする
> * Azure リソースを作成し、サブネットからのみネットワーク アクセスできるようにする
> * 各サブネットに仮想マシン (VM) をデプロイする
> * サブネットからリソースへのアクセスを確認する
> * サブネットおよびインターネットからリソースへのアクセスが拒否されたことを確認する

好みに応じて、[Azure CLI](tutorial-restrict-network-access-to-resources-cli.md) または [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md) を使ってこのチュートリアルの手順を実行することもできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal の左上隅にある **[+ リソースの作成]** を選択します。 「**仮想ネットワーク**」を検索してから、 **[作成]** を選択します。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-resources.png" alt-text="[リソースの作成] ページでの仮想ネットワークの検索のスクリーンショット。":::    

1. **[基本]** タブで、次の情報を入力してから、 **[次へ :IP アドレス]** を選択します。 

   | 設定 | 値 |
   |----|----|
   | サブスクリプション | サブスクリプションを選択します。|
   | Resource group | **[新規作成]** を選択し、「*myResourceGroup* と入力します。|
   | 名前 | 「*myVirtualNetwork*」と入力します |
   | リージョン | **[(米国) 米国東部]** を選択します |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png" alt-text="仮想ネットワークを作成する [基本] タブのスクリーンショット。":::  

1. **[IP アドレス]** タブで、次の IP アドレス設定を選択してから、 **[確認と作成]** を選択します。
   
   | 設定 | 値 |
   | --- | --- |
   | IPv4 アドレス空間| 既定値のままにします。 |
   | サブネット名 | **[既定]** を選択し、サブネット名を「Public」に変更します。 |
   | サブネット アドレス範囲 | 既定値のままにします。 |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-virtual-network-ip-addresses.png" alt-text="仮想ネットワークを作成する [IP アドレス] タブのスクリーンショット。":::  

1. 検証チェックに成功したら、 **[作成]** を選択します。

1. デプロイの完了を待ってから、 **[リソースに移動]** を選択するか、次のセクションに進みます。 

## <a name="enable-a-service-endpoint"></a>サービス エンドポイントを有効にする

サービス エンドポイントはサービスごと、サブネットごとに有効にします。 サブネットを作成し、そのサブネットに対してサービス エンドポイントを有効にするには、次の手順に従います。

1. 仮想ネットワークのリソース ページがまだ表示されていない場合は、ポータルの上部にあるボックスで、新しく作成したネットワークを検索できます。 「*myVirtualNetwork*」と入力して、一覧から選択します。

1. 示されているように、[*設定*] の **[サブネット]** を選択してから、 **[+ サブネット]** を選択します。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/add-subnet.png" alt-text="既存の仮想ネットワークにサブネットを追加しているスクリーンショット。":::

1. **[サブネットの追加]** ページで、次の情報を選択または入力してから、 **[保存]** を選択します:

    | 設定 |値 |
    | --- | --- |
    | Name | プライベート |
    | サブネットのアドレス範囲 | 既定値のままにします|
    | サービス エンドポイント | **[Microsoft.Storage]** を選択します|
    | サービス エンドポイント ポリシー | 既定値のままにします。 *0 件選択済み*。 |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/add-subnet-settings.png" alt-text="サービス エンドポイントが構成された [サブネットの追加] ページのスクリーンショット。":::  

> [!CAUTION]
> リソースが含まれる既存のサブネットのサービス エンドポイントを有効にする前に、「[サブネット設定の変更](virtual-network-manage-subnet.md#change-subnet-settings)」を参照してください。

## <a name="restrict-network-access-for-a-subnet"></a>サブネットのネットワーク アクセスを制限する

既定では、サブネット内のすべての仮想マシン インスタンスが任意のリソースと通信できます。 ネットワーク セキュリティ グループを作成し、それをサブネットに関連付けることで、サブネット内のすべてのリソース間の通信を制限できます。

1. Azure portal の上部にある検索ボックスで、「**ネットワーク セキュリティ グループ**」を検索します。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/search-network-security-groups.png" alt-text="ネットワーク セキュリティ グループを検索しているスクリーンショット。":::  

1. [*ネットワーク セキュリティ グループ*] ページで、 **[+ 作成]** を選択します。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/network-security-groups-page.png" alt-text="[ネットワーク セキュリティ グループ] ランディング ページのスクリーンショット。"::: 

1. 次の情報を入力または選択します。

    |設定|値|
    |----|----|
    |サブスクリプション| サブスクリプションを選択します。|
    |Resource group | 一覧から *[myResourceGroup]* を選択します|
    |名前| 「**myNsgPrivate**」と入力します |
    |場所| **[米国東部]** を選択します。 |

1. **[確認と作成]** を選択し、検証チェックに成功したら、 **[作成]** を選択します。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-nsg-page.png" alt-text="[ネットワーク セキュリティ グループの作成] ページのスクリーンショット。":::

1. ネットワーク セキュリティ グループの作成後、 **[リソースに移動]** を選択するか、Azure portal の上部で「*myNsgPrivate*」を検索します。

1. *[設定]* の **[送信セキュリティ規則]** を選択してから、 **[+ 追加]** を選択します。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-outbound-rule.png" alt-text="送信セキュリティ規則を追加しているスクリーンショット。" lightbox="./media/tutorial-restrict-network-access-to-resources/create-outbound-rule-expanded.png":::

1. Azure Storage サービスへの送信方向の通信を許可するルールを作成します。 次の情報を入力または選択し、 **[追加]** を選択します。

    |設定|値|
    |----|----|
    |source| **VirtualNetwork** を選びます。 |
    |Source port ranges| * |
    |宛先 | **[Service Tag]\(サービス タグ\)** を選びます|
    |宛先サービス タグ | **[ストレージ]** を選びます|
    |サービス | 既定値の *[カスタム]* のままにします。 |
    |宛先ポート範囲| *445* に変更します。 後の手順で作成されるファイル共有に接続するために、SMB プロトコルが使用されます。 |
    |Protocol|Any|
    |アクション|Allow|
    |優先度|100|
    |名前|**Allow-Storage-All** という名前に変更します|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-outbound-storage-rule.png" alt-text="ストレージにアクセスするための送信セキュリティを作成しているスクリーンショット。":::

1. インターネットへの通信を拒否する別の送信セキュリティ ルールを作成します。 このルールは、送信方向のインターネット通信を許可する、すべてのネットワーク セキュリティ グループ内の既定のルールをオーバーライドします。 次の値を使用して手順 6 から 9 までを実行した後、 **[追加]** を選択します。

    |設定|値|
    |----|----|
    |source| **VirtualNetwork** を選びます。 |
    |Source port ranges| * |
    |宛先 | **[Service Tag]\(サービス タグ\)** を選びます|
    |宛先サービス タグ| **[インターネット]** を選びます|
    |サービス| 既定値の *[カスタム]* のままにします。 |
    |宛先ポート範囲| * |
    |Protocol|Any|
    |アクション| 既定値を **[拒否]** に変更します。 |
    |Priority|110|
    |Name|*Deny-Internet-All* に変更します|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-outbound-internet-rule.png" alt-text="インターネット アクセスをブロックするための送信セキュリティを作成しているスクリーンショット。":::

1. 任意の場所からサブネットへのリモート デスクトップ プロトコル (RDP) トラフィックを許可する "*インバウンド セキュリティ規則*" を作成します。 この規則は、インターネットからのすべての受信トラフィックを拒否する既定のセキュリティ規則をオーバーライドします。 後のステップで接続をテストできるように、サブネットへのリモート デスクトップ接続を許可します。 *[設定]* の **[受信セキュリティ規則]** を選択してから、 **[+ 追加]** を選択します。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-inbound-rule.png" alt-text="受信セキュリティ規則を追加しているスクリーンショット。" lightbox="./media/tutorial-restrict-network-access-to-resources/create-inbound-rule-expanded.png":::

1. 次の値を入力または選択してから、 **[追加]** を選択します。

    |設定|値|
    |----|----|
    |source| Any |
    |Source port ranges| * |
    |宛先 | **VirtualNetwork** を選びます。|
    |宛先ポート範囲| *3389* に変更します |
    |Protocol|Any|
    |アクション|Allow|
    |Priority|120|
    |Name|*Allow-RDP-All* に変更します|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-inbound-rdp-rule.png" alt-text="許可の受信リモート デスクトップ規則を作成しているスクリーンショット。":::

   >[!WARNING] 
   > RDP ポート 3389 がインターネットに公開されます。 これはテストにのみ推奨されます。 "*運用環境*" では、VPN またはプライベート接続を使用することをお勧めします。

1.  *[設定]* の **[サブネット]** を選択してから、 **[+ 関連付け]** を選択します。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/associate-subnets-page.png" alt-text="ネットワーク セキュリティ グループとサブネットの関連付けページのスクリーンショット。":::

1.  *[仮想ネットワーク]* の **[myVirtualNetwork]** を選択してから、 *[サブネット]* の **[プライベート]** を選択します。 **[OK]** を選択して、ネットワーク セキュリティ グループを選択したサブネットに関連付けます。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/associate-private-subnet.png" alt-text="ネットワーク セキュリティ グループをプライベート サブネットに関連付けているスクリーンショット。":::

## <a name="restrict-network-access-to-a-resource"></a>リソースへのネットワーク アクセスを制限する

サービス エンドポイントを有効にした Azure サービスを介して作成されたリソースへのネットワーク アクセスを制限するために必要な手順は、サービスによって異なります。 各サービスの具体的な手順については、それぞれのサービスのドキュメントをご覧ください。 このチュートリアルの残りの部分では、例として Azure ストレージ アカウントのネットワーク アクセスを制限する手順を示します。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。

1. 検索バーに「ストレージ アカウント」と入力し、それをドロップダウン メニューから選択します。 **[作成]** を選択します。

1. 次の情報を入力します。

    |設定|値|
    |----|----|
    |サブスクリプション| サブスクリプションを選択します。|
    |Resource group| *[myResourceGroup]* を選択します|
    |ストレージ アカウント名| すべての Azure の場所で一意の名前を入力します。 名前の長さは 3 - 24 文字で、使用できるのは数字と小文字のみです。|
    |リージョン| **[(米国) 米国東部]** を選択します |
    |パフォーマンス|Standard|
    |冗長性| ローカル冗長ストレージ (LRS)|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-storage-account.png" alt-text="ストレージ アカウントの新規作成のスクリーンショット。":::

1. **[作成と確認]** を選択し、検証チェックに成功したら、 **[作成]** を選択します。 

    >[!NOTE] 
    > デプロイが完了するまでに数分かかる場合があります。

1. ストレージ アカウントの作成後、 **[リソースに移動]** を選択します。

### <a name="create-a-file-share-in-the-storage-account"></a>ストレージ アカウントにファイル共有を作成する

1. *[データ ストレージ]* の **[ファイル共有]** を選択してから、 **[+ ファイル共有]** を選択します。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/file-share-page.png" alt-text="ストレージ アカウントの [ファイル共有] ページのスクリーンショット。":::

1. ファイル共有の次の値を入力または設定してから、 **[作成]** を選択します。

    |設定|値|
    |----|----|
    |名前| my-file-share|
    |Quota| **[最大値に設定する]** を選択します。 |
    |レベル| 既定値の *[トランザクション最適化]* のままにします。 |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-new-file-share.png" alt-text="新しいファイル共有を作成する設定ページのスクリーンショット。":::

1. 新しいファイル共有が [ファイル共有] ページに表示されます。表示されない場合は、ページの上部にある **[更新]** ボタンを選択します。

### <a name="restrict-network-access-to-a-subnet"></a>サブネットへのネットワーク アクセスを制限する

既定では、ストレージ アカウントは、インターネットを含む任意のネットワーク上のクライアントからのネットワーク接続を受け入れます。 インターネットおよびすべての仮想ネットワーク内のその他すべてのサブネットからのネットワーク アクセスを制限できます (*myVirtualNetwork* 仮想ネットワーク内の *Private* サブネットを除く)。サブネットへのネットワーク アクセスを制限するには、次の手順に従います。

1. 自分の (一意の名前の) ストレージ アカウントの *[設定]* で、 **[ネットワーク]** を選択します。

1. *[許可するアクセス元]* で **[選択されたネットワーク]** を選択してから、[+ 既存の仮想ネットワークを追加] を選択します。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/storage-network-settings.png" alt-text="ストレージ アカウントのネットワーク設定ページのスクリーンショット。":::

1. **[ネットワークの追加]** で次の値を選んで、 **[追加]** を選びます。

    |設定|値|
    |----|----|
    |サブスクリプション| サブスクリプションを選択します|
    |仮想ネットワーク| **myVirtualNetwork**|
    |サブネット| **プライベート**|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/add-virtual-network.png" alt-text="仮想ネットワークをストレージ アカウントに追加するページのスクリーンショット。":::

1. **[保存]** ボタンを選択して、仮想ネットワークの構成を保存します。

1. ストレージ アカウントの [*セキュリティとネットワーク*] で、 **[アクセス キー]** を選択し、 **[キーの表示]** を選択します。 キー 1 の値をメモして、後の手順でファイル共有を VM にマップするときに使用できるようにします。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/storage-access-key.png" alt-text="ストレージ アカウントのキーと接続文字列のスクリーンショット。" lightbox="./media/tutorial-restrict-network-access-to-resources/storage-access-key-expanded.png":::

## <a name="create-virtual-machines"></a>仮想マシンを作成する

ストレージ アカウントへのネットワーク アクセスをテストするには、各サブネットに VM を展開します。

### <a name="create-the-first-virtual-machine"></a>最初の仮想マシンを作成する

1. Azure portal で、 **[+ リソースの作成]** を選択します。

1. **[コンピューティング]** を選択してから、 *[仮想マシン]* の **[作成]** を選択します。

1. *[基本]* タブで、次の情報を入力または選択します。

   |設定|値|
   |----|----|
   |サブスクリプション| サブスクリプションを選択します。|
   |Resource group| 先ほど作成した **myResourceGroup** を選択します。|
   |仮想マシン名| 「*myVmPublic*」と入力します|
   |リージョン | (米国) 米国東部
   |可用性のオプション| 可用性ゾーン|
   |可用性ゾーン | 1 |
   |Image | OS イメージを選択します。 この VM では、 *[Windows Server 2019 Datacenter - Gen1]* を選択します。 |
   |サイズ | 使用する VM インスタンスのサイズを選択します |
   |ユーザー名|任意のユーザー名を入力します。|
   |Password| 任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.yml?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm-)を満たす必要があります。|
   |パブリック受信ポート | 選択したポートを許可する |
   |受信ポートの選択 | 既定の *[RDP (3389)]* のままにします |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-public-vm-settings.png" alt-text="パブリック仮想マシンを作成する際の設定のスクリーンショット。" lightbox="./media/tutorial-restrict-network-access-to-resources/create-public-vm-settings-expanded.png":::
  
1. **[ネットワーク]** タブで、次の情報を入力または選択します。

    |設定|値|
    |----|----|
    | Virtual Network | **[myVirtualNetwork]** を選択します。 |
    | Subnet | **[パブリック]** を選択します。 |
    | NIC ネットワーク セキュリティ グループ | **[Advanced] \(詳細設定)** を選択します。 ポータルで、ポート 3389 を許可するネットワーク セキュリティ グループが自動的に作成されます。 後の手順で仮想マシンに接続するには、このポートを開いておく必要があります。 |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/virtual-machine-networking.png" alt-text="パブリック仮想マシンを作成する際のネットワーク設定のスクリーンショット。" lightbox="./media/tutorial-restrict-network-access-to-resources/virtual-machine-networking-expanded.png":::

1. **[確認と作成]** を選択し、 **[作成]** を選択して、デプロイの完了を待ちます。

1. **[リソースに移動]** を選択するか、 **[ホーム] > [仮想マシン]** ページを開き、先ほど作成した VM (*myVmPublic*) を選択します。これで VM が起動します。

### <a name="create-the-second-virtual-machine"></a>2 番目の仮想マシンを作成する

1. 手順 1 から 5 を繰り返して、2 つ目の仮想マシンを作成します。 手順 3 で、仮想マシン名を *myVmPrivate* とし、[*NIC ネットワーク セキュリティ グループ*] を **[なし]** に設定します。 手順 4 で、 **[プライベート]** サブネットを選択します。

   :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/virtual-machine-2-networking.png" alt-text="プライベート仮想マシンを作成する際のネットワーク設定のスクリーンショット。" lightbox="./media/tutorial-restrict-network-access-to-resources/virtual-machine-2-networking-expanded.png":::

1. **[確認と作成]** を選択し、 **[作成]** を選択して、デプロイの完了を待ちます。 

    > [!WARNING]
    > デプロイが完了するまで、次の手順に進まないでください。

1. **[リソースに移動]** を選択するか、 **[ホーム] > [仮想マシン]** ページを開き、先ほど作成した VM (*myVmPrivate*) を選択します。これで VM が起動します。

## <a name="confirm-access-to-storage-account"></a>ストレージ アカウントへのアクセスを確認する

1. *myVmPrivate* VM が作成されたら、仮想マシンの [概要] ページにアクセスします。 **[接続]** ボタンを選択した後、ドロップダウンから **[RDP]** を選択して、VM に接続します。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/connect-private-vm.png" alt-text="プライベート仮想マシンの [接続] ボタンのスクリーンショット。":::

1. **[RDP ファイルのダウンロード]** を選択して、リモート デスクトップ ファイルをコンピューターにダウンロードします。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/download-rdp-file.png" alt-text="プライベート仮想マシンの [RDP ファイルのダウンロード] のスクリーンショット。":::
  
1. ダウンロードされた rdp ファイルを開きます。 メッセージが表示されたら、 **[Connect]** を選択します。 

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/rdp-connect.png" alt-text="プライベート仮想マシンの接続画面のスクリーンショット。":::

1. VM の作成時に指定したユーザー名とパスワードを入力します。 場合によっては、 **[その他]** 、 **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。 メール フィールドには、先ほど指定した "管理者アカウント: ユーザー名" の資格情報を入力します。 **[OK]** を選択して VM にサインインします。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/credential-screen.png" alt-text="プライベート仮想マシンの資格情報画面のスクリーンショット。":::

    > [!NOTE] 
    > サインイン処理中に証明書の警告が表示される場合があります。 警告を受け取ったら、 **[はい]** または **[続行]** を選択して接続処理を続行します。

1. サインインしたら、Windows PowerShell を開きます。 PowerShell で以下のスクリプトを使用して、Azure ファイル共有を Z ドライブにマップします。 `<storage-account-key>` および両方の `<storage-account-name>` 変数を、前の「[ストレージ アカウントの作成](#create-a-storage-account)」の手順で指定してメモした値に置き換えます。

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   PowerShell によって、次のサンプル出力のような出力が返されます。

   ```powershell
   Name        Used (GB)     Free (GB) Provider      Root
   ----        ---------     --------- --------      ----
   Z                                      FileSystem    \\mystorage007.file.core.windows.net\my-f...
   ```

   Azure ファイル共有は Z ドライブに正常にマップされました。

1.   *myVmPrivate* VM へのリモート デスクトップ セッションを閉じます。

## <a name="confirm-access-is-denied-to-storage-account"></a>ストレージ アカウントへのアクセスが拒否されたことを確認する

### <a name="from-myvmpublic"></a>myVmPublic から:

1. ポータルの上部にある *[リソース、サービス、ドキュメントの検索]* ボックスに「**myVmPublic**」と入力します。 検索結果に **myVmPublic** が表示されたら、それを選びます。

1. *myVmPublic* VM に対して、「[ストレージ アカウントへのアクセスを確認する](#confirm-access-to-storage-account)」の手順 1 から 5 を繰り返します。

   少し待つと、`New-PSDrive : Access is denied` エラーが発生します。 *myVmPublic* VM が *Public* サブネットに展開されているため、アクセスが拒否されました。 "*パブリック*" サブネットには、Azure Storage に対して有効になっているサービス エンドポイントがありません。 ストレージ アカウントは *Private* サブネットからのネットワーク アクセスのみを許可し、*Public* サブネットからは許可しません。

    ```powershell
    New-PSDrive : Access is denied
    At line:1 char:1
    + New-PSDrive -Name Z -PSProvider FileSystem -Root "\\mystorage007.file ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : InvalidOperation: (Z:PSDriveInfo) [New-PSDrive],     Win32Exception
        + Fu llyQualifiedErrorId : CouldNotMapNetworkDrive,Microsoft.PowerShell.Commands.NewPSDriveCommand

    ```

4. *myVmPublic* VM へのリモート デスクトップ セッションを閉じます。

### <a name="from-a-local-machine"></a>ローカル コンピューターから:

1. Azure portal で、先ほど作成した一意の名前のストレージ アカウントに移動します。 たとえば、*mystorage007* などです。

1. [*データ ストレージ*] の **[ファイル共有]** を選択してから、先ほど作成した *my-file-share* を選択します。

1. 次のエラー メッセージが表示されます。

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/access-denied-error.png" alt-text="アクセス拒否エラー メッセージのスクリーンショット。":::

>[!NOTE] 
> お使いのコンピューターは *myVirtualNetwork* 仮想ネットワークの "*プライベート*" サブネットに含まれていないため、アクセスが拒否されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループとそれに含まれるすべてのリソースが不要になったら、それらを削除します。

1. ポータル上部の **[検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **[myResourceGroup]** が表示されたら、それを選択します。

1. **[リソース グループの削除]** を選択します。

1. **[TYPE THE RESOURCE GROUP NAME:]\(リソース グループ名を入力してください:\)** に「*myResourceGroup*」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、仮想ネットワーク サブネットのサービス エンドポイントを有効にしました。 複数の Azure サービスからデプロイされているリソースに対して、サービス エンドポイントを有効にできることを学習しました。 Azure ストレージ アカウントを作成し、そのストレージ アカウントへのネットワーク アクセスを、仮想ネットワーク サブネット内のリソースのみに制限しました。 サービス エンドポイントの詳細については、[サービス エンドポイントの概要](virtual-network-service-endpoints-overview.md)と[サブネットの管理](virtual-network-manage-subnet.md)に関するページをご覧ください。

アカウントに複数の仮想ネットワークがある場合は、リソースが相互に通信できるように、それらの間の接続を確立できます。 仮想ネットワークを接続する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [仮想ネットワークを接続する](./tutorial-connect-virtual-networks-portal.md)
