<properties
	pageTitle="Windows を実行する仮想マシンを Azure プレビュー ポータル | Microsoft Azure で作成します。"
	description="Windows を実行する Azure 仮想マシン (VM) リソースを、Azure プレビュー ポータルの Azure Marketplace を利用して作成する方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/13/2015"
	ms.author="kathydav"/>

# Windows を実行する仮想マシンを Azure プレビュー ポータルで作成する#

> [AZURE.SELECTOR]
- [Azure preview portal](virtual-machines-windows-tutorial.md)
- [Azure portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource manager](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service management](virtual-machines-ps-create-preconfigure-windows-vms.md)

このチュートリアルでは、プレビュー ポータルで Azure 仮想マシン (VM) を数分で簡単に作成する方法を示します。Azure リソース マネージャーで VM を作成するための例として、Windows Server 2012 R2 Datacenter イメージを使用しますが、これは Azure によって提供される多くのイメージの 1 つにすぎません。イメージの選択肢は、サブスクリプションによって異なります。たとえば、デスクトップ イメージは MSDN サブスクリプション会員のみが使用できますです。

リソース マネージャーのテンプレートまたは自動化ツールとともに、独自のイメージを使用して VM を作成することもできます。さまざまな方法の詳細については、「[Windows 仮想マシンを作成するさまざまな方法](virtual-machines-windows-choices-create-vm.md)」を参照してください。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## イメージの選択

プレビュー ポータルで Azure Marketplace に移動して、必要な Windows Server VM イメージを検索します。

1. [プレビュー ポータル](https://portal.azure.com)にサインインします。

2. [ハブ] メニューで、**[新規]**、**[Compute]**、**[Windows Server 2012 R2 Datacenter]** の順にクリックします。

	![Marketplace](./media/virtual-machines-windows-tutorial/marketplace_new.png)

	>[AZURE.TIP]その他のイメージを見つけるには、**[Marketplace]** をクリックし、使用可能な項目を検索するか、フィルターで抽出します。

3. **[Windows Server 2012 R2 Datacenter]** ページで、**[リソース マネージャー スタックを使用]** を選択して、Azure リソース マネージャーで VM を作成します。(新しいワークロードのほとんどについて、リソース マネージャー スタックを使用することをお勧めします。考慮事項については、「[Azure リソース マネージャーにおける Azure コンピューティング、ネットワーク、Storage プロバイダー](virtual-machines-azurerm-versus-azuresm.md)」をご覧ください)。 **[作成]** をクリックします。

	![Marketplace での検索](./media/virtual-machines-windows-tutorial/marketplace_search_select.png)

## 仮想マシンの作成

イメージを作成したら、ほとんどの構成で Azure の既定の設定を使用できるため、迅速に VM を作成できます。

1. **[バーチャル マシンの作成]** ブレードで **[基本]** をクリックします。VM の **[名前]**、管理 **[ユーザー名]** と強力な **[パスワード]** を入力します。複数のサブスクリプションがある場合は、新しい VM に対して 1 つ指定し、新規または既存の**リソース グループ**と、Azure データ センターの**場所**を指定します。

	![VM の基礎の構成](./media/virtual-machines-windows-tutorial/create_vm_basics.PNG)

	>[AZURE.NOTE][ユーザー名] とは、サーバーの管理に使用する管理アカウントの名前です。覚えやすい (また、他の人が予測しずらい) パスワードを作成します。**仮想マシンにログオンする際に、このユーザー名とパスワードが必要になります。**

2. **[サイズ]** をクリックし、ニーズに応じた VM サイズを選択します。それぞれのサイズによって、コンピューティング コアの数、メモリ、および Premium Storage サポートなどの他の機能が指定されており、これが価格に影響します。Azure では、ユーザーが選択したイメージに応じて、特定のサイズが自動的に推奨されます。

	![VM のサイズの構成](./media/virtual-machines-windows-tutorial/create_vm_size.PNG)

	>[AZURE.NOTE]プレミアム ストレージは、特定のリージョンの DS シリーズの仮想マシンで使用できます。プレミアム ストレージは、データベースなどの高負荷のワークロードに最適なストレージ オプションです。詳細については、[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス Storage](storage-premium-storage-preview-portal.md)に関するページを参照してください。

3. **[設定]** をクリックして、新しい VM のストレージおよびネットワークの設定を表示します。最初の VM については、通常、既定の設定をそのまま使用します。Premium Storage がサポートされる VM サイズを選択した場合は、**[ディスクの種類]** の **[Premium (SSD)]** を選択することで、Premium Storage をお試しいただくことができます。

	![VM 設定の構成](./media/virtual-machines-windows-tutorial/create_vm_settings.PNG)

6. **[概要]** をクリックして、構成の選択内容を確認します。設定の確認または更新を終了したら、**[作成]** をクリックします。

	![VM 設定の構成](./media/virtual-machines-windows-tutorial/create_vm_summary.PNG)

8. Azure が VM を作成している間の進捗状況は、ハブ メニューの **[通知]** で確認できます。Azure で VM が作成されるとスタート画面に表示されます。ただし、**[バーチャル マシンの作成]** ブレードで **[スタート画面にピン留めする]** をオフにした場合は表示されません。

## 仮想マシンへのログオン

VM を作成したら、VM にログオンして、その設定や、そこで実行するアプリケーションを管理できます。

>[AZURE.NOTE]要件やトラブルシューティングのヒントについては、「[RDP または SSH で Azure 仮想マシンに接続する](https://msdn.microsoft.com/library/azure/dn535788.aspx)」を参照してください。

1. まだサインインしていない場合は、[プレビュー ポータル](https://portal.azure.com)にサインインします。

2. スタートボードの VM をクリックします。VM を探す場合は、**[すべて参照]**、**[最近使用した項目]** の順にクリックするか、**[すべて参照]**、**[仮想マシン]** の順にクリックします。その後、一覧から VM を選択します。

3. VM ブレードで、**[接続]** をクリックします。

	![仮想マシンへのログオン](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. **[開く]** をクリックして、Windows Server 仮想マシン用に自動的に作成されたリモート デスクトップ プロトコル ファイルを使用します。

5. **[接続]** をクリックします。

6. 仮想マシンの作成時に設定したユーザー名とパスワードを入力し、**[OK]** をクリックします。

7. **[はい]** をクリックして、目的の仮想マシンであることを確認します。

	これで、仮想マシンを他のサーバーとまったく同様に扱うことができます。

## 次のステップ

* Azure PowerShell と Azure CLI を使用して、[VM イメージの検索と選択](resource-groups-vm-searching.md)を行います。
* [Azure リソース マネージャー](virtual-machines-how-to-automate-azure-resource-manager.md)と[Azure リソース マネージャー テンプレート](http://azure.microsoft.com/documentation/templates/)を使用して、VM とワークロードのデプロイと管理を自動化します。

<!---HONumber=July15_HO5-->