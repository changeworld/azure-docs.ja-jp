---
title: 'チュートリアル: 複数のサブネットでの AG の前提条件'
description: 'このチュートリアルでは、Azure Virtual Machines (VM) 上の SQL Server の複数のサブネットに Always On 可用性グループを作成するための前提条件を構成する方法について説明します。 '
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 4d1ec223a745f49518a7936bc85d40f470739c80
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132159353"
---
# <a name="tutorial-prerequisites-for-availability-groups-in-multiple-subnets-sql-server-on-azure-vms"></a>チュートリアル: 複数のサブネットでの可用性グループの前提条件 (Azure VM 上の SQL Server) 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> 同じ Azure 仮想ネットワーク内の複数のサブネットに SQL Server VM を作成すると、Always On 可用性グループに対して Azure Load Balancer または分散ネットワーク名 (DNN) が不要になります。

このチュートリアルでは、[複数のサブネット内の Azure SQL Server (VM) 上の SQL Server に Always On 可用性グループ](availability-group-manually-configure-tutorial-multi-subnet.md)を作成するための前提条件を完成させます。 このチュートリアルを終了すると、2 つの Azure 仮想マシンに 1 つのドメイン コントローラー、複数のサブネット内に 2 つの SQL Server VM、1 つのリソース グループに 1 つのストレージ アカウントができます。 

**推定所要時間**: このチュートリアルでは、Azure に複数のリソースを作成します。完了まで最大 30 分かかる場合があります。 

次の図は、このチュートリアルでデプロイするリソースを示しています。 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/multi-subnet-availability-group-diagram.png" alt-text="次の図は、このチュートリアルでデプロイするリソースを示しています":::


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- Azure サブスクリプション。 [無料の Azure アカウントを作成する](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic)か、[Visual Studio サブスクライバーの特典を有効にする](/visualstudio/subscriptions/subscriber-benefits)ことができます。
- [SQL Server の Always On 可用性グループ](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)の基本的な理解と知識。 


## <a name="create-resource-group"></a>リソース グループの作成

Azure portal でリソース グループを作成するには、次の手順に従います。 

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータルで **[+ リソースの作成]** を選び、新しいリソースを作成します。

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/01-portal-plus.png" alt-text="ポータルで [+ リソースの作成] を選び、新しいリソースを作成します。":::

1. **Marketplace** の検索ボックスで **リソース グループ** を検索し、Microsoft の **[リソース グループ]** タイルを選びます。 **[リソース グループ]** ページで **[作成]** を選びます。 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/01-resource-group-search.png" alt-text="Marketplace でリソース グループを検索し、リソース グループの作成を選びます。":::

1. **[リソース グループの作成]** ページで、値を入力してリソース グループを作成します。
   1. ドロップダウンから適切な Azure サブスクリプションを選びます。 
   1. リソース グループの名前 (**SQL-HA-RG** など) を入力します。
   1. ドロップダウンから場所 (**米国西部 2** など) を選びます。 以降のすべてのリソースもこの場所にデプロイしてください。 
   1. **[確認と作成]** を選択してリソース パラメーターを確認し、 **[作成]** を選択してリソース グループを作成します。  

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/01-resource-group-create-complete.png" alt-text="Azure portal で値を入力して、リソース グループを作成します。":::


## <a name="create-network-and-subnets"></a>ネットワークとサブネットの作成

次に、仮想ネットワークと 3 つのサブネットを作成します。 詳細については、[仮想ネットワークの概要](../../../virtual-network/virtual-networks-overview.md)に関するページを参照してください。 

Azure portal で仮想ネットワークを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、ご利用のリソース グループに移動して **[+ 作成]** を選びます。

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/02-create-resource-rg.png" alt-text="新しいリソースをリソース グループに作成する":::

1. **Marketplace** の検索ボックスで「**仮想ネットワーク**」を検索し、Microsoftの **[仮想ネットワーク]** タイルを選択します。 **[仮想ネットワーク]** ページで **[作成]** を選びます。  
1. **[仮想ネットワークの作成]** ページの **[基本]** タブで、次の情報を入力します。 
   1. **[プロジェクトの詳細]** で、適切な Azure **サブスクリプション** と、前に作成した **リソース グループ** (**SQL-HA-RG** など) を選びます。 
   1. **[インスタンスの詳細]** で、ご利用の仮想ネットワークの名前 (**SQLHAVNET** など) を指定し、ドロップダウンからリソース グループと同じリージョンを選びます。

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/03-create-vnet-basics.png" alt-text="前に作成したリソース グループを選び、仮想ネットワークの名前 (SQLHAVNET など) を指定します":::

1. **[IP アドレス]** タブで、**既定の** サブネットを選んで **[サブネットの編集]** ページを開きます。 ドメイン コントローラー サブネットに使用する名前を **DC-subnet** に変更します。 **[保存]** を選択します。  

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/04-create-vnet-ip-address-rename-default-subnet.png" alt-text="[IP アドレス] タブで、既定のサブネットを選んで [サブネットの編集] ページを開きます。ドメイン コントローラー サブネットに使用する名前を DC-subnet に変更します。[保存] を選びます":::

1. **[+ サブネットの追加]** を選び、最初の SQL Server VM にサブネットを追加し、次の値を入力します。 
   1. **[サブネット名]** に値 (**SQL-subnet-1** など) を指定します。 
   1. 仮想ネットワーク アドレス空間内に一意のサブネット アドレス範囲を指定します。 たとえば、DC-subnet アドレス範囲の 3 番目のオクテットを 1 回繰り返すことができます。 
      - たとえば、**DC-subnet** の範囲が *10.38.0.0/24* の場合、**SQL-subnet-1** の IP アドレス範囲 `10.38.1.0/24` を入力します。 
      - 同様に、**DC-subnet** の IP 範囲が *10.5.0.0/24* の場合、新しいサブネットに対して `10.5.1.0/24` と入力します。 
   1. **[追加]** を選び、新しいサブネットを追加します。 

     :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/05-create-vnet-ip-address-add-sql-subnet-1.png" alt-text="最初のサブネットに sql-subnet-1 などの名前を付け、3 番目のオクテットを 1 回繰り返します。これにより、DC-subnet の IP アドレスが 10.5.0.0 の場合、新しいサブネットは 10.5.1.0 になります":::

1. 前の手順を繰り返して、**SQL-subnet-2** などの名前を持つ 2 つ目の SQL Server VM に対して一意のサブネット範囲を追加します。 もう一度 3 番目のオクテットを 1 回繰り返すことができます。 
   - たとえば、**DC-subnet** の IP 範囲が *10.38.0.0/24* で、**SQL-subnet-1** が *10.38.1.0/24* の場合、新しいサブネットに対して「`10.38.2.0/24`」と入力します。
   - 同様に、**DC-subnet** の IP 範囲が *10.5.0.0/24* で、**SQL-subnet-1** が *10.5.1.0/24* の場合、**SQL-subnet-2** の IP アドレス範囲「`10.5.2.0/24`」を入力します。 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/06-create-vnet-ip-address-add-sql-subnet-2.png" alt-text="2 番目のサブネットに sql-subnet-2 などの名前を付け、3 番目のオクテットを 2 回繰り返します。これにより、DC-subnet の IP アドレスが 10.38.0.0/24 の場合、新しいサブネットは 10.38.2.0/24 になります":::

1. 2 つ目のサブネットを追加したら、サブネットの名前と範囲を確認します (IP アドレス範囲は画像と異なる場合があります)。 すべて正しければ、 **[確認と作成]** 、 **[作成]** の順に選択して、新しい仮想ネットワークを作成します。 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/07-create-vnet-ip-address.png" alt-text="2 つ目のサブネットを追加したら、サブネットの名前と範囲が画像のようになっていることを確認します (ただし IP アドレス範囲は異なる場合があります)。すべて正しければ、[確認と作成]、[作成] の順に選択して、新しい仮想ネットワークを作成します。":::

   新しいネットワークが作成されると、画面がポータル ダッシュボードに切り替わって通知が表示されます。


## <a name="create-domain-controllers"></a>ドメイン コントローラーを作成する

ネットワークとサブネットの準備ができたら、仮想マシンを 1 つ (または高可用性のために必要に応じて 2 つ) 作成し、ドメイン コントローラーとして構成します。 

### <a name="create-dc-virtual-machines"></a>DC 仮想マシンを作成する

Azure portal でドメイン コントローラー (DC) 仮想マシンを作成するには、次の手順に従います。 

1. [Azure portal](https://portal.azure.com) で、ご利用のリソース グループに移動して **[+ 作成]** を選びます。

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/02-create-resource-rg.png" alt-text="新しいリソースをリソース グループに作成する":::

1. **Marketplace** の検索ボックスで **Windows Server** を検索します。 
1. Microsoft の **[Windows Server]** タイルで、 **[作成]** ドロップダウン、 **[Windows Server 2016 Datacenter]** イメージの順に選択します。 
1. **[仮想マシンの作成]** ページで値を入力して、ドメイン コントローラー VM (**DC-VM-1** など) を作成します。 必要に応じて、**DC-VM-2** などの追加の VM を作成して、Azure Active Directory Domain Services に高可用性を提供します。 次の表の値を使用して、VM を作成します。

    | **フィールド** | 値 |
    | --- | --- |
    | **サブスクリプション** |*該当するサブスクリプション* |
    | **リソース グループ** |SQL-HA-RG |
    | **仮想マシン名** |最初のドメイン コントローラー: *DC-VM-1*。</br>2 番目のドメイン コントローラー: *DC-VM-2*。 |
    | **リージョン** |*リソース グループと仮想ネットワークをデプロイした場所。* |
    | **可用性オプション** |可用性ゾーン </br> *可用性ゾーンをサポートしていない Azure リージョンの場合は、代わりに可用性セットを使用してください。新しい可用性セットを作成し、このチュートリアルで作成したすべての VM をその可用性セット内に配置します。* |
    | **可用性ゾーン** |DC-VM-1 には 1 を指定します。 </br> DC-VM-2 には 2 を指定します。 |
    | **Size** |D2s_v3 (2 vCPU、8 GB RAM) |
    | **ユーザー名** |DomainAdmin |
    | **パスワード** |Contoso!0000 |
    | **パブリック インバウンド ポート** | *[選択したポートを許可する]* |
    | **受信ポートの選択** | *RDP (3389)* |
    | **OS ディスクの種類** | Premium SSD (ローカル冗長ストレージ) |
    | **仮想ネットワーク** |SQLHAVNET |
    | **サブネット** |DC-subnet |
    | **パブリック IP** |*VM と同じ名前 (DC-VM-1 や DC-VM-2 など)* |
    | **NIC ネットワーク セキュリティ グループ**| Basic |
    | **パブリック インバウンド ポート**| 選択したポートを許可する |
    | **受信ポートの選択**| RDP (3389) |
    | **ブート診断** |マネージド ストレージ アカウントで有効にする (推奨)。 |

    仮想マシンが作成され、使用する準備ができると、Azure から通知が送信されます。 


### <a name="configure-the-domain-controller"></a>ドメイン コントローラーの構成

DC 仮想マシンの準備ができたら、corp.contoso.com のドメイン コントローラーを構成します。

ドメイン コントローラーとして **DC-VM-1** を構成するには、次の手順に従います。 

1. [Azure portal](https://portal.azure.com) でリソース グループに移動し、**DC-VM-1** マシンを選びます。 
1. **[DC-VM-1]** ページで、 **[接続]** を選び、リモート デスクトップ アクセス用の RDP ファイルをダウンロードし、ファイルを開きます。  

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/08-dc-vm-1-rdp-connect.png" alt-text="仮想マシンへの接続":::

1. 構成済みの管理者アカウント (**DomainAdmin**) とパスワード (**Contoso!0000**) を使用して RDP セッションに接続します。
1. **[サーバー マネージャー]** ダッシュボードを開き (既定で開く場合があります)、 **[ロールと機能の追加]** を選びます。 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/09-add-features.png" alt-text="サーバー マネージャー - 役割の追加":::

1. **[サーバーの役割]** セクションが表示されるまで **[次へ]** を選択します。
1. **[Active Directory Domain Services]** と **[DNS サーバー]** という役割を選択します。 メッセージが表示されたら、これらの役割に必要なその他の機能を追加します。

   > [!NOTE]
   > Windows から、静的 IP アドレスがないという警告が出されます。 構成をテストしている場合は、 **[続行]** を選択します。 運用シナリオの場合は、Azure Portal で IP アドレスを静的アドレスに設定するか、[PowerShell を使用してドメイン コントローラー コンピューターの静的 IP アドレスを設定](/azure/virtual-network/virtual-networks-static-private-ip-arm-ps)してください。
   >

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/10-add-roles.png" alt-text="役割の追加のダイアログ":::

1. **[確認]** セクションが表示されるまで **[次へ]** を選択します。 **[必要に応じてターゲット サーバーを自動的に再起動する]** チェック ボックスをオンにします。
1. **[インストール]** を選択します。
1. 機能のインストールが完了したら、 **[サーバー マネージャー]** ダッシュボードに戻ります。
1. 左側のウィンドウで新しい **[AD DS]** オプションを選択します。
1. 黄色の警告バーにある **[その他]** リンクを選択します。

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/11-ad-ds-more.png" alt-text="DMS サーバー VM 上の AD DS を示すダイアログ":::
    
1. **[すべてのサーバー タスクの詳細]** ダイアログ ボックスの **[操作]** 列で、 **[このサーバーをドメイン コントローラーに昇格する]** を選択します。
1. **Active Directory Domain Services の構成ウィザード** で、次の値を使用します。

    | **ページ** | 設定 |
    | --- | --- |
    | **デプロイ構成** |**[新しいフォレストの追加]**<br/> **[ルート ドメイン名]** = corp.contoso.com |
    | **ドメイン コントローラー オプション** |**[DSRM パスワード]** = Contoso!0000<br/>**[パスワードの確認]** = Contoso!0000 |

1. **[次へ]** を選択して、ウィザード内の他のページを進めます。 **[前提条件のチェック]** ページで、"**すべての前提条件のチェックに合格しました**" というメッセージが表示されることを確認します。 該当する警告メッセージを確認してください。ただし、インストールは続行できます。
1. **[インストール]** を選択します。 **DC-VM-1** 仮想マシンが自動的に再起動されます。

### <a name="identify-dns-ip-address"></a>DNS IP アドレスを特定する

DNS 用に、プライマリ ドメイン コントローラーを使用します。 これを行うには、プライマリ ドメイン コントローラーに使用される VM のプライベート IP アドレスを特定します。 

Azure portal で VM のプライベート IP アドレスを特定するには、次の手順に従います。 

1. [Azure portal](https://portal.azure.com) でリソース グループに移動し、プライマリ ドメイン コントローラー **DC-VM-1** を選びます。 
1. **[DC-VM-1]** ページの **[設定]** ペインで **[ネットワーク]** を選びます。 
1. **NIC のプライベート IP** アドレスをメモします。 この IP アドレスは、他の仮想マシンの DNS サーバーとして使用します。  この例の画像では、プライベート IP アドレスは **10.38.0.4** です。 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-dc-vm-1-private-ip.png" alt-text="[DC-VM-1] ページの [設定] ペインで [ネットワーク] を選び、NIC のプライベート IP アドレスをメモします。この IP アドレスは、他の仮想マシンの DNS サーバーとして使用します。":::

### <a name="configure-virtual-network-dns"></a>仮想ネットワーク DNS を構成する

最初のドメイン コント ローラーを作成し、DNS を有効にしたら、DNS にこの VM を使用するように仮想ネットワークを構成します。

DNS 用に仮想ネットワークを構成するには、次の手順に従います。 

1. [Azure portal](https://portal.azure.com) で、リソース グループに移動して、仮想ネットワーク (**SQLHAVNET** など) を選択します。 
1. **[設定]** ペインで **[DNS サーバー]** を選択してから、 **[カスタム]** を選択します。 
1. **[IP アドレス]** フィールドに、前に特定したプライベート IP アドレス (`10.38.0.4` など) を入力します。 
1. **[保存]** を選択します。 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-identify-dns-ip-address.png" alt-text="[設定] ペインで [DNS サーバー] を選択してから、[カスタム] を選択します。[IP アドレス] フィールドに、前に特定したプライベート IP アドレス (10.38.0.4 など) を入力します。":::


### <a name="configure-second-domain-controller"></a>2 つ目のドメイン コントローラーを構成する

プライマリ ドメイン コントローラーが再起動したら、高可用性を目的として、必要に応じて 2 つ目のドメイン コントローラーを構成できます。 2 つ目のドメイン コントローラーを構成しない場合は、この手順をスキップします。 ただし、運用環境では、2 つ目のドメイン コントローラーをお勧めします。 

優先 DNS サーバー アドレスを設定し、ドメインに参加してから、セカンダリ ドメイン コントローラーを構成します。 

#### <a name="set-preferred-dns-server-address"></a>優先 DNS サーバー アドレスを設定する

まず、優先 DNS サーバー アドレスを変更します。 これを行うには、次のステップに従います。 

1. [Azure portal](https://portal.azure.com) でリソース グループに移動し、2 つ目のドメイン コントローラー マシン (**DC-VM-2** など) を選びます。 
1. **[DC-VM-2]** ページで、 **[接続]** を選び、リモート デスクトップ アクセス用の RDP ファイルをダウンロードし、ファイルを開きます。 
1. 構成済みの管理者アカウント (**DomainAdmin**) とパスワード (**Contoso!0000**) を使用して RDP セッションに接続します。
1. **[ネットワークと共有センター]** を開き、ネットワーク インターフェイスを選びます。 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/13-network-interface.png" alt-text="ネットワーク インターフェイス":::

1. **[プロパティ]** ページを開きます。 
1. **[インターネット プロトコル バージョン 4 (TCP/IPv4)]** 、 **[プロパティ]** の順に選びます。
1. **[次の DNS サーバーのアドレスを使う]** を選び、 **[優先 DNS サーバー]** にプライマリ ドメイン コントローラーのプライベート IP アドレス (`10.38.0.4` など) を指定します。 
1. **[OK]** 、 **[閉じる]** の順に選択して変更を適用します。 DNS IP アドレスを変更した後にリモート デスクトップ接続が失われる場合は、[Azure portal](https://portal.azure.com) で仮想マシンに移動してその VM を再起動します。 

### <a name="join-the-domain"></a>ドメインに参加する 

次に、**corp.contoso.com** ドメインに参加します。 これを行うには、次のステップに従います。 

1. **BUILTIN\DomainAdmin** アカウントを使用して仮想マシンにリモート接続します。
1. **サーバー マネージャー** を開き **[ローカル サーバー]** を選びます。
1. **[WORKGROUP]** を選びます。
1. **[コンピューター名]** セクションで、 **[変更]** を選択します。
1. **[ドメイン]** チェック ボックスをオンにし、テキスト ボックスに「**corp.contoso.com**」を入力します。 **[OK]** を選択します。
1. **[Windows セキュリティ]** ポップアップ ダイアログで、既定のドメイン管理者の資格情報であるアカウント (**CORP\DomainAdmin**) とパスワード (**Contoso!0000**) を指定します。
1. "corp.contoso.com ドメインへようこそ" のメッセージが表示されたら、 **[OK]** を選択します。
1. **[閉じる]** を選択し、ポップアップ ダイアログで **[今すぐ再起動]** を選択します。


#### <a name="configure-domain-controller"></a>ドメイン コントローラーを構成する 

サーバーがドメインに参加したら、2 つ目のドメイン コントローラーとして構成できます。 これを行うには、次のステップに従います。 

1. まだ接続していない場合は、セカンダリ ドメイン コントローラーへの RDP セッションを開き、**サーバー マネージャー ダッシュボード** を開きます (既定で開く場合があります)。
1. ダッシュボードの **[役割と機能の追加]** リンクを選択します。

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/09-add-features.png" alt-text="サーバー マネージャー - 役割の追加":::

1. **[サーバーの役割]** セクションが表示されるまで **[次へ]** を選択します。
1. **[Active Directory Domain Services]** と **[DNS サーバー]** という役割を選択します。 メッセージが表示されたら、これらの役割に必要なその他の機能を追加します。
1. 機能のインストールが完了したら、 **[サーバー マネージャー]** ダッシュボードに戻ります。
1. 左側のウィンドウで新しい **[AD DS]** オプションを選択します。
1. 黄色の警告バーにある **[その他]** リンクを選択します。
1. **[すべてのサーバー タスクの詳細]** ダイアログ ボックスの **[操作]** 列で、 **[このサーバーをドメイン コントローラーに昇格する]** を選択します。
1. **[デプロイ構成]** で **[既存のドメインにドメイン コントローラーを追加する]** を選択します。
1. **[選択]** をクリックします。
1. 管理者アカウント (**CORP.CONTOSO.COM\domainadmin**) とパスワード (**Contoso!0000**) を使用して接続します。
1. **[フォレストからのドメインの選択]** でドメインを選択し、 **[OK]** を選択します。
1. **[ドメイン コントローラー オプション]** で、既定値を使用し、DSRM パスワードを設定します。

    >[!NOTE]
    >**[DNS オプション]** ページでは、この DNS サーバーの委任を作成できないという警告が表示される場合があります。 この警告は、非運用環境では無視してかまいません。
    >

1. **前提条件** の確認のダイアログ ボックスが表示されるまで、 **[次へ]** を選択します。 その後、 **[インストール]** を選択します。

サーバーによる構成の変更が完了したら、サーバーを再起動します。

### <a name="add-second-dc-ip-address-to-dns"></a>2 つ目の DC IP アドレスを DNS に追加する

2 つ目のドメイン コントローラーを構成したら、前と同じ手順に従って [VM のプライベート IP アドレスを特定し](#identify-dns-ip-address)、リソース グループの仮想ネットワークに[セカンダリ カスタム DNS サーバーとしてプライベート IP アドレスを追加します](#configure-virtual-network-dns)。 Azure portal でセカンダリ DNS サーバーを追加すると、DNS サービスの冗長性が有効になります。 



## <a name="configure-domain-accounts"></a>ドメイン アカウントの構成

ドメイン コントローラーを構成し、Azure portal で DNS サーバーを設定したら、SQL Server をインストールするユーザーと SQL Server サービス アカウント用にドメイン アカウントを作成します。 

両方の SQL Server VMに 1 つのインストール アカウントと、SQL Server VM ごとに 1 つのサービス アカウントの合計 3 つのアカウントを構成します。 たとえば、次の表の値をアカウントに使用します。

|Account  | VM  |完全なドメイン名  |説明   |
|---------|---------|---------|---------|
|インストール    |両方| Corp\Install        |このアカウントを使用していずれかの VM にログインし、クラスターと可用性グループを構成します。 |
|SQLSvc1     |SQL-VM-1 |Corp\SQLSvc1 | このアカウントは、1 つ目の SQL Server VM 上の SQL Server サービスに使用します。 |
|SQLSvc2     |SQL-VM2 |Corp\SQLSvc2| このアカウントは、2 つ目の SQL Server VM 上の SQL Server サービスに使用します。|

各アカウントを作成するには、次の手順に従います。 

1. プライマリ ドメイン コントローラー マシン (**DC-VM-1** など) に接続します。 .
1. **[サーバー マネージャー]** で、 **[ツール]** を選択し、 **[Active Directory 管理センター]** を選択します。
1. 左側のウィンドウで **[corp (ローカル)]** を選択します。
1. 右側の **[タスク]** ペインで、 **[新規]** を選択し、 **[ユーザー]** を選択します。 
1. 新しいユーザー アカウントを入力し、複雑なパスワードを設定します。 非運用環境では、ユーザー アカウントを期限なしに設定してください。

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/14-ad-dc-new-user.png" alt-text="Active Directory 管理センター":::

1. **[OK]** を選択してユーザーを作成します。
1. これらの手順を繰り返して、3 つのアカウントすべてを作成します。 

### <a name="grant-installation-account-permissions"></a>インストール アカウントのアクセス許可を付与する

アカウントが作成されたら、インストール アカウントが AD でオブジェクトを作成できるように、必要なドメイン アクセス許可をアカウントに付与します。 

インストール アカウントにアクセス許可を付与するには、次の手順に従います。 

1. **Active Directory 管理センター** がまだ開いていない場合は、**サーバー マネージャー** から開きます。 
1. 左側のペインで **[corp (ローカル)]** を選びます。 
1. 右側の **[タスク]** ペインで、ドロップダウンに **[corp (ローカル)]** が表示されていることを確認してから、下の **[プロパティ]** を選びます。

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/15-ad-dc-properties.png" alt-text="corp のユーザー プロパティ":::

1. **[拡張機能]** を選択し、 **[セキュリティ]** タブの **[詳細設定]** を選択します。
1. **[corp のセキュリティの詳細設定]** ダイアログ ボックスで、 **[追加]** を選びます。
1. **[プリンシパルの選択]** を選び、**CORP\Install** を検索して、 **[OK]** を選びます。
1. **[すべてのプロパティの読み取り]** と **[コンピューター オブジェクト作成]** の横のボックスをオンにします。 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/16-add-permissions.png" alt-text="corp のユーザーのアクセス許可":::

1. **[OK]** を選択してから、もう一度 **[OK]** を選択します。 **corp** のプロパティ ウィンドウを閉じます。

Active Directory とユーザー オブジェクトの構成が完了したので、新しい SQL Server VM を作成する準備が整いました。 

## <a name="create-sql-server-vms"></a>SQL Server VM の作成

AD、DNS、ユーザー アカウントを構成したら、新しい SQL Server VM を作成する準備が整います。 わかりやすくするために、マーケットプレースで SQL Server VM イメージを使用します。 

ただし、SQL Server VM を作成する前に、次の設計上の決定を検討してください。 

**可用性 - Availability Zones**   

最も高いレベルの冗長性、回復性、可用性を実現するために、VM を個別の Availability Zones にデプロイします。 Availability Zones は、Azure リージョン内の一意の物理的な場所です。 各ゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つ以上のデータセンターで構成されています。 Availability Zones がまだサポートされていない Azure リージョンの場合は、代わりに可用性セットを使用してください。 すべての VM を同じ可用性セット内に配置します。 

**ストレージ: Azure Managed Disks**   

仮想マシンのストレージには、Azure Managed Disks を使用します。 Managed Disks を使用すると、バックグラウンドでストレージが処理されるため、Microsoft では、SQL Server 仮想マシンにはこちらを使用することを推奨しています。 詳細については、[Azure Managed Disks の概要](../../../virtual-machines/managed-disks-overview.md)に関する記事をご覧ください。 

**ネットワーク: 運用中のプライベート IP アドレス**   

このチュートリアルでは、仮想マシンにパブリック IP アドレスを使用します。 パブリック IP アドレスを使うと、インターネット経由で仮想マシンに直接リモート接続できるため、構成手順が簡素化されます。 運用環境では、SQL Server インスタンス VM リソースの脆弱性の範囲を縮小するため、プライベート IP アドレスのみを使用することをお勧めします。

**ネットワーク - サーバーごとに 1 つの NIC**   

サーバー (クラスター ノード) ごとに 1 つの NIC を使用します。 Azure ネットワークは物理的な冗長を備えているので、Azure 仮想マシンにデプロイされたフェールオーバー クラスターに NIC を追加する必要はありません。 クラスター検証レポートには、ノードは 1 つのネットワーク上でのみ到達可能であることを警告するメッセージが表示されます。 フェールオーバー クラスターが Azure 仮想マシン上にある場合は、この警告を無視できます。 

VM を作成するには、次の手順に従います。 

1. [Azure portal](https://portal.azure.com) で、ご利用のリソース グループに移動して **[+ 作成]** を選びます。
1. **Azure SQL** を検索し、Microsoft から **[Azure SQL]** タイルを選びます。 
1. **[Azure SQL]** ページで **[作成]** を選び、ドロップダウンから **[Windows Server 2016 の SQL Server 2016 SP2 Enterprise]** イメージを選びます。 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/18-select-sql-vm-image.png" alt-text="ポータルの [Azure SQL] ページで [作成] を選び、ドロップダウンから [Windows Server 2016 の SQL Server 2016 SP2 Enterprise] イメージを選びます。":::

次の表を使用して、 **[仮想マシンの作成]** ページの値を入力し、両方の SQL Server VM (**SQL-VM-1** と **SQL-VM-2** など) を作成します (実際の IP アドレスは表の例とは異なる場合があります)。 

| 構成 | SQL-VM-1 | SQL-VM-2 |
| --- | --- | --- |
| ギャラリー イメージ |**Windows Server 2016 の SQL Server 2016 SP2 Enterprise** |**Windows Server 2016 の SQL Server 2016 SP2 Enterprise** |
| **VM の基礎** |**[名前]** = SQL-VM-1<br/>**[ユーザー名]** = DomainAdmin<br/>**パスワード** = Contoso!0000<br/>**[サブスクリプション]** = 自分のサブスクリプション<br/>**[リソース グループ]** = SQL-HA-RG<br/>**[場所]** = 該当する Azure の場所 |**[名前]** = SQL-VM-2<br/>**[ユーザー名]** = DomainAdmin<br/>**パスワード** = Contoso!0000<br/>**[サブスクリプション]** = 自分のサブスクリプション<br/>**[リソース グループ]** = SQL-HA-RG<br/>**[場所]** = 該当する Azure の場所 |
| **VM のサイズ** |**[サイズ]** = E2ds_v4 (2 vCPU、16 GB RAM)</br> |**[サイズ]** = E2ds_v4 (2 vCPU、16 GB RAM) |
| **VM 設定** |**[可用性オプション]** = 可用性ゾーン<br/>**[可用性ゾーン]** = 1<br/>**[パブリック受信ポート]** = 選択されたポートを許可する<br/>**[受信ポートを選択]** = RDP (3389)<br/>**[OS ディスクの種類]** = Premium SSD (ローカル冗長ストレージ)<br/>**[仮想ネットワーク]** = SQLHAVNET<br/>**[サブネット]** = SQL-subnet-1(10.38.1.0/24)<br/>**[パブリック IP アドレス]** = 自動的に生成されます。<br/>**[NIC ネットワーク セキュリティ グループ]** = Basic<br/>**[パブリック受信ポート]** = 選択されたポートを許可する <br/> **[受信ポートを選択]** = RDP (3389)<br/>**[ブート診断]** = マネージド ストレージ アカウントで有効にする (推奨)<br/>|**[可用性オプション]** = 可用性ゾーン<br/>**[可用性ゾーン]** = 2<br/>**[パブリック受信ポート]** = 選択されたポートを許可する<br/>**[受信ポートを選択]** = RDP (3389)<br/>**[OS ディスクの種類]** = Premium SSD (ローカル冗長ストレージ)<br/>**[仮想ネットワーク]** = SQLHAVNET<br/>**[サブネット]** = SQL-subnet-2(10.38.2.0/24)<br/>**[パブリック IP アドレス]** = 自動的に生成されます。<br/>**[NIC ネットワーク セキュリティ グループ]** = Basic<br/>**[パブリック受信ポート]** = 選択されたポートを許可する <br/> **[受信ポートを選択]** = RDP (3389)<br/>**[ブート診断]** = マネージド ストレージ アカウントで有効にする (推奨)<br/> |
| **SQL Server の設定** |**[SQL 接続]** = プライベート (仮想ネットワーク内)<br/>**[ポート]** = 1433<br/>**[SQL 認証]** = 無効<br/>**[Azure Key Vault の統合]** = 無効 <br/>**[ストレージの最適化]** = トランザクション処理<br/>**[SQL データ]** = 1024 GiB、5000 IOPS、200 MB/秒<br/>**[SQL ログ]** = 1024 GiB、5000 IOPS、200 MB/秒<br/>**[SQL TempDb]** = ローカル SSD ドライブを使用する<br/>**[自動修正]** = 日曜日 2:00<br/>**[自動バックアップ]** = 無効 |**[SQL 接続]** = プライベート (仮想ネットワーク内)<br/>**[ポート]** = 1433<br/>**[SQL 認証]** = 無効<br/>**[Azure Key Vault の統合]** = 無効 <br/>**[ストレージの最適化]** = トランザクション処理<br/>**[SQL データ]** = 1024 GiB、5000 IOPS、200 MB/秒<br/>**[SQL ログ]** = 1024 GiB、5000 IOPS、200 MB/秒<br/>**[SQL TempDb]** = ローカル SSD ドライブを使用する<br/>**[自動修正]** = 日曜日 2:00<br/>**[自動バックアップ]** = 無効 |

<br/>

> [!NOTE]
> これらの推奨されるマシン サイズは、Azure Virtual Machines での可用性グループのテストのみを目的としています。 最適化された運用ワークロードについては、[Azure VM 上の SQL Server のパフォーマンスのベスト プラクティス](./performance-guidelines-best-practices-checklist.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)のサイズに関する推奨事項を参照してください。
>

## <a name="configure-sql-server-vms"></a>SQL Server VM を構成する

VM の作成が完了したら、各 VM にセカンダリ IP アドレスを追加し、それらをドメインに参加させて、SQL Server VM を構成します。 


### <a name="add-secondary-ips-to-sql-server-vms"></a>セカンダリ IP を SQL Server VM に追加する

マルチサブネット環境では、可用性グループ リスナーに使用するためにセカンダリ IP アドレスを各 SQL Server VM に割り当てます。Windows Server 2016 以前では、クラスター IP アドレスの各 SQL Server VM にもセカンダリ IP アドレスを割り当てます。 これを行うと、単一のサブネット環境での要件と同様に、Azure Load Balancer の必要性がなくなります。  

Windows Server 2016 以前では、クラスターで Windows Server 2019 で導入された既定の分散ネットワーク名 (DNN) ではなく **クラスター ネットワーク名** が使用されるため、Windows クラスター IP に使用する各 SQL Server VM に追加のセカンダリ IP アドレスを割り当てる必要があります。 DNN を使用すると、クラスター名オブジェクト (CNO) がクラスターのすべてのノードの IP アドレスに自動的に登録されるため、専用の Windows クラスター IP アドレスは不要になります。

Windows Server 2016 以前を使用している場合は、このセクションの手順に従って、可用性グループ リスナー "*および*"クラスターの "*両方*"の各 SQL Server VM にセカンダリ IP アドレスを割り当てます。 

Windows Server 2019 以降を使用している場合は、可用性グループ リスナーのセカンダリ IP アドレスのみを割り当て、Windows クラスター IP を割り当てる手順をスキップします。ただし、仮想ネットワーク名 (VNN) を使用してクラスターを構成する予定がない場合は、Windows Server 2016 の場合と同様に両方の IP アドレスを各 SQL Server VM に割り当てます。 

追加のセカンダリ IP を VM に割り当てるには、次の手順を実行します。 

1. [Azure portal](https://portal.azure.com/) で、リソース グループに移動して、最初の SQL Server VM (**SQL-VM-1** など) を選びます。 
1. **[設定]** ペインで **[ネットワーク]** を選択してから、 **[ネットワーク インターフェイス]** を選びます。 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/19-sql-vm-network-interface.png" alt-text="[設定] ペインで [ネットワーク] を選択してから [ネットワーク インターフェイス] を選択する":::

1. **[ネットワーク インターフェイス]** ページで、 **[設定]** ペインの **[IP 構成]** を選択してから、 **[+ 追加]** を選択して追加の IP アドレスを追加します。 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/20-ip-configurations-add.png" alt-text="IP 構成":::

1. **[IP 構成の追加]** ページで、次の操作を行います。 
   1. **[名前]** を Windows クラスター IP として指定します (Windows 2016 以前の場合は **windows-cluster-ip** など)。 Windows Server 2019 以降を使用している場合は、この手順をスキップします。  
   1. **[割り当て]** を **[静的]** に設定します。
   1. SQL Server VM (**SQL-VM-1**) と同じサブネット (**SQL-subnet-1**) に、未使用の **IP アドレス** (`10.38.1.10` など) を入力します。 
   1. **[パブリック IP アドレス]** は既定値の **[関連付けの解除]** のままにします。 
   1. **[OK]** を選び、IP 構成の追加を終了します。 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/21-add-ip-windows-cluster.png" alt-text="最初の SQL Server VM のサブネット内の未使用 IP アドレスを入力してクラスター IP を追加する":::

1. もう一度 **[+ 追加]** を選び、(**availability-group-listener** のような名前の) 可用性グループ リスナーに対して追加の IP アドレスを構成します。ここでも、**SQL-subnet-1** 内の未使用の IP アドレス (`10.38.1.11` など) を指定します。 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/22-add-ip-ag-listener.png" alt-text="もう一度 [+ 追加] を選択して、(availability-group-listener のような名前の) 可用性グループ リスナーに対して追加の IP アドレスを構成する。ここでも、SQL-subnet-1 内の未使用の IP アドレス (10.31.1.11 など) を指定する":::

1. 2 番目の SQL Server VM (**SQL-VM-2** など) について、以上の手順をもう一度繰り返します。 **SQL-subnet-2** 内の未使用のセカンダリ IP アドレスを 2 つ割り当てます。 次の表の値を使用して、IP 構成を追加します。 

   
    | **フィールド** | 入力 | 入力 | 
    | --- | --- | --- |
    | **名前** |windows-cluster-ip | availability-group-listener |
    | **Allocation** | 静的 | 静的 |
    | **IP アドレス (IP address)** | 10.38.2.10 | 10.38.2.11 | 
    |  |  |  |

これで、**corp.contoso.com** に参加する準備が整いました。 

### <a name="join-the-servers-to-the-domain"></a>ドメインへのサーバーの参加

2 つのセカンダリ IP アドレスを両方の SQL Server VM に割り当てたら、各 SQL Server VM を **corp.contoso.com** ドメインに参加させます。 

corp.contoso.com ドメインに参加するには、セカンダリ ドメイン コントローラーを使用して[ドメインに参加](#join-the-domain)した時に行ったのと同じ手順を SQL Server VM に対して実行します。 

各 SQL Server VM が再起動するのを待ってから、アカウントを追加できます。 


## <a name="add-accounts"></a>アカウントの追加

各 VM の管理者としてインストール アカウントを追加し、SQL Server 内のインストール アカウントとローカル アカウントにアクセス許可を付与して、SQL Server サービス アカウントを更新します。 

### <a name="add-install-account"></a>インストール アカウントを追加する

両方の SQL Server VM がドメインに参加したら、ローカル管理者グループのメンバーとして **CORP\Install** を追加します。

>[!TIP]
> 必ずこの "*ドメイン*" の管理者アカウントでサインインしてください。 前の手順では、**BUILTIN** 管理者アカウントを使用していました。 これでサーバーがドメインの一部になったので、このドメイン アカウントを使用します。 RDP セッションで、*DOMAIN*\\*username* (**CORP\DomainAdmin** など) を指定します。

アカウントを管理者として追加するには、次の手順に従います。 

1. VM が再起動されるまで待ち、最初の SQL Server VM からもう一度 RDP ファイルを起動し、**CORP\DomainAdmin** アカウントを使用して **SQL-VM-1** にサインインします。
1. **[サーバー マネージャー]** で **[ツール]** を選択し、 **[コンピューターの管理]** を選択します。
1. **[コンピューターの管理]** ウィンドウで、 **[ローカル ユーザーとグループ]** を展開し、 **[グループ]** を選択します。
1. **[Administrators]** グループをダブルクリックします。
1. **[Administrators のプロパティ]** ダイアログ ボックスで、 **[追加]** を選択します。
1. ユーザーに「**CORP\Install**」と入力し、 **[OK]** を選択します。
1. **[OK]** を選択して **[管理者のプロパティ]** ダイアログ ボックスを閉じます。
1. **SQL-VM-2** でこれらの手順を繰り返します。 

### <a name="add-account-to-sysadmin"></a>sysadmin にアカウントを追加する 

可用性グループの構成に使用されるインストール アカウント (CORP\install) は、各 SQL Server VM 上の **sysadmin** 固定サーバー ロールの一部である必要があります。 

インストール アカウントに **sysadmin** 権限を付与するには、次の手順に従います。 

1. *\<MachineName\>\DomainAdmin* アカウント (`SQL-VM-1\DomainAdmin` など) を使用して、リモート デスクトップ プロトコル (RDP) 経由でサーバーに接続します。
1. SQL Server Management Studio を開き、SQL Server のローカル インスタンスに接続します。
1. **オブジェクト エクスプローラー** で **[セキュリティ]** を選択します。
1. **[ログイン]** を右クリックします。 **[新しいログイン]** を選択します。
1. **[ログイン - 新規作成]** で **[検索]** を選択します。
1. **[場所]** を選択します。
1. ドメイン管理者のネットワーク資格情報を入力します。
1. インストール アカウント (CORP\install) を使用します。
1. サインインを **sysadmin** 固定サーバー ロールのメンバーに設定します。
1. **[OK]** を選択します。
1. 2 つ目の SQL Server VM (**SQL-VM-2** など) でこれらの手順を繰り返し、関連するマシン名アカウント (`SQL-VM-2\DomainAdmin` など) に接続します。 


### <a name="add-system-account"></a>システム アカウントを追加する 

新しいバージョンの SQL Server では、[NT AUTHORITY\SYSTEM] アカウントに SQL Server へのアクセス許可が既定で付与されないため、手動で付与する必要があります。 

[NT AUTHORITY\SYSTEM] を追加し、適切なアクセス許可を付与するには、次の手順に従います。 

1. *\<MachineName\>\DomainAdmin* アカウント (`SQL-VM-1\DomainAdmin` など) を使用して、リモート デスクトップ プロトコル (RDP) 経由で最初の SQL Server VM に接続します。
1. SQL Server Management Studio を開き、SQL Server のローカル インスタンスに接続します。
1. 次の Transact-SQL (T-SQL) コマンドを使用して、各 SQL Server インスタンス上の `[NT AUTHORITY\SYSTEM]` にアカウントを作成します。 

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. 各 SQL Server インスタンスで、次のアクセス許可を `[NT AUTHORITY\SYSTEM]` に付与します。

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   これらのアクセス許可を付与するには、次の Transact-SQL (T-SQL) コマンドを使用します。 

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

1. 2 つ目の SQL Server VM (**SQL-VM-2** など) でこれらの手順を繰り返し、関連するマシン名アカウント (`SQL-VM-2\DomainAdmin` など) に接続します。 

### <a name="set-the-sql-server-service-accounts"></a>SQL Server サービス アカウントの設定

各 VM の SQL Server サービスでは、専用のドメイン アカウントを使用する必要があります。  **SQL-VM-1** の場合は **Corp \ SQLSvc1**、**SQL-VM-2** の場合は **Corp \ SQLSvc2** というように、以前に作成したドメイン アカウントを使用します。 

サービス アカウントを設定するには、次の手順に従います。 

1. *\<MachineName\>\DomainAdmin* アカウント (`SQL-VM-1\DomainAdmin` など) を使用して、リモート デスクトップ プロトコル (RDP) 経由で最初の SQL Server VM に接続します。
1. **[SQL Server 構成マネージャー]** を開きます。
1. SQL Server サービスを右クリックし、 **[プロパティ]** を選択します。
1. アカウント (**Corp\SQLSvc1**) とパスワードを指定します。
1. **[適用]** を選択して変更をコミットし、SQL Server サービス を再起動します。 
1. 他の SQL Server VM (SQL-VM-1) でこれらの手順を繰り返し、`SQL-VM-2\DomainAdmin` などのマシン ドメイン アカウントでサインインし、2 つ目のサービス アカウント (**Corp\SQLSvc2**) を指定します。 


## <a name="create-azure-storage-account"></a>Azure ストレージ アカウントを作成する

2 ノードの Windows Server フェールオーバー クラスターを展開するには、クォーラムを確立するために 3 番目のメンバーが必要です。 Azure VM で推奨されるクォーラム オプションは、クラウド監視です。 クラウド監視を構成するには、Azure Storage アカウントが必要です。 詳細については、「[フェールオーバー クラスターにクラウド監視を展開する](/windows-server/failover-clustering/deploy-cloud-witness)」をご覧ください。

ポータルで Azure Storage アカウントを作成するには:

1. ポータルで **SQL-HA-RG** リソース グループを開き、 **[+ 作成]** を選びます。
1. **ストレージ アカウント** を検索します。
1. **[ストレージ アカウント]** 、 **[作成]** の順に選び、次の値で構成します。 

    1. サブスクリプションを選び、リソース グループ **SQL-HA-RG** を選びます。
    1. **[ストレージ アカウント名]** に一意のストレージ アカウントを入力します。
       ストレージ アカウント名の長さは 3 から 24 文字である必要があり、数字と小文字のみを使用できます。 ストレージ アカウント名は、Azure 内で一意である必要もあります。    
    1. 自分の **リージョン** を選びます。
    1. **[パフォーマンス]** には、 **[Standard: Recommended for most scenarios (general-purpose v2 account)]\(標準: ほとんどのシナリオに推奨 (汎用 v2 アカウント)\)** を選びます。 Azure Premium Storage は、クラウド監視ではサポートされていません。
    1. **[冗長]** には **[ローカル冗長ストレージ (LRS)]** を選びます。
       フェールオーバー クラスタリングでは、アービトレーション ポイントとして BLOB ファイルを使用します。ここでは、データの読み取り時に一定の整合性が保証される必要があります。 そのため、レプリケーションの種類として [ローカル冗長ストレージ] を選択する必要があります。 
    1. **[確認と作成]** を選択します

## <a name="configure-the-firewall"></a>ファイアウォールを構成する

可用性グループ機能は、次の TCP ポートを経由するトラフィックに依存します。 

- **SQL Server VM**:SQL Server の既定のインスタンス用のポート 1433。
- **データベース ミラーリング エンドポイント:** 使用可能な任意のポート。 例では 5022 がよく使用されます。

両方の SQL Server VM でこれらのファイアウォール ポートを開きます。 ポートを開く方法は、使用するファイアウォール ソリューションによって異なります。また、このセクションで説明する Windowsファイアウォールの例とは異なる場合があります。 

Windows ファイアウォールでこれらのポートを開くには、次の手順に従います。 

1. 最初の SQL サーバーの **スタート** 画面で、 **[セキュリティが強化された Windows ファイアウォール]** を開きます。
1. 左側のウィンドウで、 **[受信の規則]** を選択します。 右側のペインで **[新しい規則]** を選択します。
1. **[規則の種類]** で **[ポート]** を選択します。
1. ポートに対して **[TCP]** を選択し、適切なポート番号を入力します。 次の例を参照してください。

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/17-firewall-tcp-ports.png" alt-text="SQL ファイアウォール":::

1. **[次へ]** を選択します。
1. **[操作]** ページで、 **[接続を許可する]** を選択し、 **[次へ]** を選択します。
1. **[プロファイル]** ページで、既定の設定をそのまま使用し、 **[次へ]** を選択します。
1. **[名前]** ページで、 **[名前]** テキスト ボックスに規則の名前 (**SQL Inbound** など) を指定し、 **[完了]** を選択します。
1. 2 つ目の SQL Server VM についても同じ手順を繰り返します。

## <a name="next-steps"></a>次のステップ

前提条件の構成が完了したので、複数のサブネットで[可用性グループの構成](availability-group-manually-configure-tutorial-multi-subnet.md)を開始します。 

詳細については、以下をご覧ください。

- [Windows Server フェールオーバー クラスターと Azure VM 上の SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [AlwaysOn 可用性グループと Azure VM 上の SQL Server](availability-group-overview.md)
- [AlwaysOn 可用性グループの概要](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
- [Azure VM 上の SQL Server に対する HADR 設定](hadr-cluster-best-practices.md)