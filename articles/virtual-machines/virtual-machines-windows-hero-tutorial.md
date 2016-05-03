<properties
	pageTitle="Azure ポータルで Windows VM を作成する | Microsoft Azure"
	description="Azure ポータルを使用して Windows 仮想マシンを作成する方法について説明します。"
	keywords="Windows 仮想マシン, 仮想マシンの作成, 仮想コンピューター, 仮想マシンの設定"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/14/2016"
	ms.author="cynthn"/>

# Azure ポータルで Windows 仮想マシンを作成する

このチュートリアルでは、Azure ポータルを使用して Windows VM を数分で簡単に作成する方法について説明します。例として Windows Server 2012 R2 Datacenter イメージを使用しますが、Azure では他にも使用可能なイメージが多数あります。イメージの選択肢は、サブスクリプションによって異なります。たとえば、デスクトップ イメージは [MSDN サブスクライバー](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)のみが使用できますです。

Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を数分で作成することができます。

## ビデオ チュートリアル

[こちら](https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Create-A-Virtual-Machine-Running-Windows-In-The-Azure-Preview-Portal)で、このチュートリアルのビデオをご覧いただけます。


## Marketplace から Windows 2012 R2 仮想マシン イメージを選択する

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. [ハブ] メニューで、**[新規]**、**[Compute]**、**[Windows Server 2012 R2 Datacenter]** の順にクリックします。

	![ポータルで使用できる Azure VM イメージを示すスクリーンショット](./media/virtual-machines-windows-hero-tutorial/marketplace_new.png)


3. **[Windows Server 2012 R2 Datacenter]** ページの **[デプロイ モデルの選択]** で、**[リソース マネージャー]** を選択します。**[作成]** をクリックします。

	![Azure VM で選択するデプロイを示すスクリーンショット](./media/virtual-machines-windows-hero-tutorial/marketplace_search_select.png)

## Windows 仮想マシンの作成

イメージを作成したら、ほとんどの構成で Azure の既定の設定を使用できるため、迅速に仮想マシンを作成できます。

1. **[仮想マシンの作成]** ブレードで **[基本]** をクリックします。

2. 仮想マシンの任意の**名前**を入力します。名前には、特殊文字を含めることはできません。

3. 管理用の**ユーザー名**と強力な**パスワード**を入力します。パスワードは、8 ～ 123 文字で指定する必要があります。また、1 つの小文字、1 つの大文字、1 つの数字、1 つの特殊文字のうち、少なくとも 3 つを含める必要があります。**仮想マシンに接続する際に、このユーザー名とパスワードが必要になります。**


4. サブスクリプションが複数ある場合は、新しい仮想マシンに使用するサブスクリプションを指定します。新規または既存の[リソース グループ](../resource-group-overview/#resource-groups)と Azure データ センターの**場所** (**米国西部**など) を選択します。

	![Azure VM で構成する基本的な設定を示すスクリーンショット](./media/virtual-machines-windows-hero-tutorial/create_vm_basics.PNG)

	
2. **[サイズ]** をクリックし、ニーズに応じた仮想マシンのサイズを選択します。サイズごとに、コンピューティング コアの数、メモリ、および Premium Storage サポートなどの他の機能が指定されており、価格にも反映されています。Azure では、ユーザーが選択したイメージに応じて、特定のサイズが自動的に推奨されます。

	![選択できる Azure VM サイズを示すスクリーンショット](./media/virtual-machines-windows-hero-tutorial/create_vm_size.PNG)

	>[AZURE.NOTE] Premium Storage は、特定のリージョンの DS シリーズの仮想マシンで使用できます。Premium Storage は、データベースなどの高負荷のワークロードに最適なストレージ オプションです。詳細については、[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage/storage-premium-storage.md)に関するページを参照してください。

3. **[設定]** をクリックして、新しい仮想マシンのストレージとネットワークの設定を表示します。最初の仮想マシンについては、通常、既定の設定をそのまま使用します。Premium Storage がサポートされる仮想マシンのサイズを選択した場合は、**[ディスクの種類]** の **[Premium (SSD)]** を選択することで、Premium Storage をお試しいただくことができます。

	![Azure VM で構成できるオプションの機能を示すスクリーンショット](./media/virtual-machines-windows-hero-tutorial/create_vm_settings.PNG)

6. **[概要]** をクリックして、構成の選択内容を確認します。設定の確認または更新を終了したら、**[作成]** をクリックします。

	![Azure VM で選択できる構成の概要を示すスクリーンショット](./media/virtual-machines-windows-hero-tutorial/create_vm_summary.PNG)

8. Azure が仮想マシンを作成している間の進捗状況は、ハブ メニューの **[Virtual Machines]** で追跡できます。


## 仮想マシンへの接続とログオン

1. まだサインインしていない場合は、[Azure ポータル](https://portal.azure.com/)にサインインします。

2.	ハブ メニューで **[Virtual Machines]** をクリックします。

3.	一覧から仮想マシンを選択します。

4. 仮想マシンのブレードで、**[接続]** をクリックします。

	![VM に接続する方法を示す Azure ポータルのスクリーンショット。](./media/virtual-machines-windows-connect-logon/preview-portal-connect.png)


[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

接続時に問題が発生した場合は、「[Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md)」を参照してください。

これで、仮想マシンを他のサーバーとまったく同様に扱うことができます。

## 次のステップ

* [Powershell を使用して Windows VM を作成する](virtual-machines-windows-ps-create.md)ことも、Azure CLI を使用して [Linux 仮想マシンを作成する](virtual-machines-linux-quick-create-cli.md)こともできます。

<!---HONumber=AcomDC_0427_2016-->