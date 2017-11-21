---
title: "オンプレミス Active Directory ドメイン用のレプリカ ドメイン コントローラーを Azure 仮想マシンにインストールする | Microsoft Docs"
description: "オンプレミス Active Directory ドメイン用のレプリカ DC を Azure 仮想ネットワークの Azure 仮想マシン (VM) にインストールする方法。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 60839f93954bfe38f0346b235259f68e479b8a00
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2017
---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Azure の仮想ネットワークでのレプリカ Active Directory ドメイン コントローラーのインストール
この記事では、Azure 仮想ネットワークの Azure 仮想マシン (VM) に、オンプレミス Active Directory ドメインのレプリカ DC として使用される追加のドメイン コントローラー (DC) をインストールする方法を説明します。 [Azure 仮想ネットワーク上に Windows Server Active Directory フォレストをインストールする](active-directory-new-forest-virtual-machine.md)こともできます。 Azure 仮想ネットワークに Active Directory Domain Services (AD DS) をインストールする方法については、「[Azure の仮想マシンでの Windows Server Active Directory のデプロイ ガイドライン](https://msdn.microsoft.com/library/azure/jj156090.aspx)」を参照してください。

## <a name="scenario-diagram"></a>シナリオ図
このシナリオでは、外部ユーザーは、ドメインに参加しているサーバーで稼働するアプリケーションにアクセスする必要があります。 アプリケーション サーバーとレプリカ DC を実行する VM は、Azure の仮想ネットワーク内にインストールされています。 次の図に示すように、仮想ネットワークは、[ExpressRoute](../expressroute/expressroute-locations-providers.md) によってオンプレミス ネットワークに接続するか、[サイト間 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)接続を使用できます。 

![Azure vnet におけるレプリカ Active Directory ドメイン コントローラーの図][1]

アプリケーション サーバーと DC は、コンピューティング処理を分散するために別々のクラウド サービス内にデプロイされ、フォールト トレランスを強化するために可用性セット内にデプロイされます。
DC は、Active Directory レプリケーションを使用して、DC 同士で相互にレプリケートします。また、オンプレミス DC でレプリケートされます。 同期ツールは必要ありません。

## <a name="create-an-on-premises-active-directory-site-for-the-azure-virtual-network"></a>Azure 仮想ネットワーク用のオンプレミス Active Directory サイトを作成する
仮想ネットワークに対応するネットワーク リージョンを表すサイトを Active Directory 内に作成できます。 このサイトを使用して、認証、レプリケーション、およびその他の DC 検出操作を最適化できます。 次の手順では、サイトの作成方法を説明しています。背景情報については、「[Adding a New Site (新しいサイトの追加)](https://technet.microsoft.com/library/cc781496.aspx)」をご覧ください。

1. **[サーバー マネージャー]** > **[ツール]** > **[Active Directory サイトとサービス]** の順に選択して、Active Directory サイトとサービスを開きます。
2. Azure Virtual Network を作成したリージョンを表すサイトを作成します。**[サイト]** > **[アクション]** > **[新しいサイト]** の順にクリックし、「Azure US West」などの新しいサイトの名前を入力して、サイト リンクを選択し、**[OK]** をクリックします。
3. サブネットを作成し、新しいサイトに関連付けます。**[サイト]** をダブルクリックして、**[サブネット]** > **[新しいサブネット]** の順に右クリックし、仮想ネットワークの IP アドレスの範囲 (例: シナリオ図の 10.1.0.0/16) を入力し、新しい Azure サイトを選択して、**[OK]** をクリックします。

## <a name="create-an-azure-virtual-network"></a>Azure の仮想ネットワークを作成する
Azure 仮想ネットワークを作成し、サイト間 VPN を設定するには、[サイト間接続の作成](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)に関する記事に含まれている手順に従います。 

仮想ネットワーク ゲートウェイを構成して、セキュリティで保護されたサイト間 VPN 接続を作成することもできます。 新しい仮想ネットワークと内部設置型 VPN デバイスの間に、サイト間 VPN 接続を作成します。 方法については、[仮想ネットワーク ゲートウェイの構成](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) に関する記事を参照してください。

## <a name="create-azure-vms-for-the-dc-roles"></a>DC ロールの Azure VM を作成する
DC ロールをホストする VM を作成するには、「[Azure ポータルで Windows 仮想マシンを作成する](../virtual-machines/windows/quick-create-portal.md)」の手順に従います。 フォールト トレランスと冗長性を確保するために、最低 2 つの仮想 DC をデプロイします。 Azure 仮想ネットワークに、同じように構成された DC が少なくとも 2 つあれば、これらの DC を実行する VM を可用性セットに配置して、フォールト トレランスを向上させることができます。

Azure ポータルではなく Windows PowerShell を使用して VM を作成する方法については、[Azure PowerShell を使用した Windows ベースの Virtual Machines の作成と事前構成](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関する記事を参照してください。

DC ロールを実行する VM の静的 IP アドレスを予約します。 静的 IP アドレスを予約するには、Microsoft Web Platform Installer をダウンロードして、 [Azure PowerShell をインストール](/powershell/azure/overview) し、Set-AzureStaticVNetIP コマンドレットを実行します。 次に例を示します。

````
Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM
````
静的 IP アドレスを設定する方法の詳細については、[VM 用の静的内部 IP アドレスの構成](../virtual-network/virtual-networks-reserved-private-ip.md)に関する記事を参照してください。

## <a name="install-ad-ds-on-azure-vms"></a>Azure VM に AD DS をインストールする
VM にサインインし、サイト間 VPN 接続または ExpressRoute 接続で、オンプレミス ネットワーク上のリソースに接続できることを確認します。 次に、Azure VM に AD DS をインストールします。 オンプレミス ネットワーク上に追加の DC をインストールするために使用するのと同じプロセス (UI、Windows PowerShell、応答ファイル) を使用できます。 AD DS をインストールするときには、AD データベース、ログ、SYSVOL の場所の新しいボリュームを必ず指定します。 AD DS のインストール方法がわからない場合は、「[Active Directory Domain Services をインストールする (レベル 100)](https://technet.microsoft.com/library/hh472162.aspx)」、または、「[Windows Server 2012 のレプリカ ドメイン コントローラーを既存のドメインにインストールする (レベル 200)](https://technet.microsoft.com/library/jj574134.aspx)」をご覧ください。

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>仮想ネットワークの DNS サーバーを再構成する
1. 仮想ネットワーク名の一覧を取得するには、[Azure ポータル](https://portal.azure.com)で "*仮想ネットワーク*" を検索し、**[仮想ネットワーク]** を選択して一覧を表示します。 
2. 管理する仮想ネットワークを開き、オンプレミス DNS サーバーの IP アドレスではなく、レプリカ DC に割り当てられた静的 IP アドレスを使用するように[仮想ネットワークの DNS サーバーの IP アドレスを再構成](../virtual-network/virtual-network-manage-network.md#dns-servers)します。
3. 仮想ネットワーク上のすべてのレプリカ DC VM が、仮想ネットワーク上の DNS サーバーを使用するように構成されていることを確認するには:
  1. **[仮想マシン]** を選択します。
  2. VM を選択し、**[再起動]** を選択します。 
  3. VM が再び **[実行中]** になるまで待ってから、サインインします。

## <a name="create-vms-for-application-servers"></a>アプリケーション サーバー用の VM を作成する

アプリケーション サーバー ロールをホストする VM を作成するには、「[Azure ポータルで Windows 仮想マシンを作成する](../virtual-machines/windows/quick-create-portal.md)」の手順に従います。 Azure ポータルではなく Microsoft PowerShell を使用して VM を作成する方法については、[Azure PowerShell を使用した Windows ベースの Virtual Machines の作成と構成](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関する記事を参照してください。 次の表は、推奨する設定を示しています。

| 設定 | 値 |
| --- | --- |
|  **イメージの選択** | Windows Server 2012 R2 Datacenter |
|  **仮想マシンの構成** |<p>仮想マシン名: 単一ラベルの名前 (例: AppServer1) を入力します。</p><p>新しいユーザー名: ユーザーの名前を入力します。 このユーザーは、VM 上のローカルの Administrators グループのメンバーになります。 最初に VM にサインインするときは、この名前でサインインする必要があります。 Administrator という名前の組み込みのアカウントは機能しません。</p><p>新しいパスワード/確認: パスワードを入力します。</p> |
|  **仮想マシンの構成** |<p>クラウド サービス: 1 台目の VM の作成時には **[新しいクラウド サービスの作成]** を選択します。アプリケーションをホストする VM の追加作成時には、1 台目の VM と同じクラウド サービス名を選択します。</p><p>クラウド サービス DNS 名: グローバルに一意の名前を指定します。</p><p>リージョン/アフィニティ グループ/仮想ネットワーク: 仮想ネットワーク名 (例: WestUSVNet) を指定します。</p><p>ストレージ アカウント: 1 台目の VM の作成時には **[自動的に生成されたストレージ アカウントを使用]** を選択します。アプリケーションをホストする VM の追加作成時には、1 台目の VM と同じストレージ アカウント名を選択します。</p><p>可用性セット: **[可用性セットの作成]** を選択します。</p><p>可用性セット名: 1 台目の VM の作成時に、可用性セットの名前を入力します。その後、VM を追加で作成するときには、1 台目の VM と同じ可用性セット名を選択します。</p> |
|  **仮想マシンの構成** |<p><b>[VM エージェントのインストール]</b> と、必要な他の拡張機能を選択します。</p> |
  
各 VM がプロビジョニングされたら、サインインし、ドメインに参加させます。 
1. **[サーバー マネージャー]** &gt; **[ローカル サーバー]** &gt; **[ワークグループ]** &gt; **[変更…]** で、**[ドメイン]** を選択します。
2. オンプレミス ドメインの名前を入力します。 
3. ドメイン ユーザーの資格情報を指定します。
4. VM を再起動します。

## <a name="additional-resources"></a>その他のリソース

* Windows PowerShell の使い方の詳細については、「[Azure コマンドレットの概要](/powershell/azure/overview)」と「[Azure コマンドレット リファレンス](/powershell/azure/get-started-azureps)」をご覧ください。
* [Azure Virtual Machines での Windows Server Active Directory の展開ガイドライン](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Azure PowerShell を使用して既存のオンプレミス ハイパー-V のドメイン コント ローラーを Azure にアップロードする方法](http://support.microsoft.com/kb/2904015)
* [Azure の仮想ネットワークでの Active Directory フォレストのインストール](active-directory-new-forest-virtual-machine.md)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* [Microsoft Azure IT Pro IaaS: (01) 仮想マシンの基礎](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IT Pro IaaS: (05) 仮想ネットワークとクロスプレミス接続の作成](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure の管理コマンドレット](/powershell/module/azurerm.compute/#virtual_machines)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
