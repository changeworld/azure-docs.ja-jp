<properties
	pageTitle="Windows を実行する仮想マシンを Azure プレビュー ポータルで作成する"
	description="Windows を実行する Azure 仮想マシン (VM) を、Azure プレビュー ポータルの Azure Marketplace を利用して作成する方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/03/2015"
	ms.author="kathydav"/>

# Windows を実行する仮想マシンを Azure プレビュー ポータルで作成する#

> [AZURE.SELECTOR]
- [Azure Preview Portal](virtual-machines-windows-tutorial.md)
- [Azure Portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource Management](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service Management](virtual-machines-ps-create-preconfigure-windows-vms.md)

このチュートリアルでは、Azure プレビュー ポータルで Azure 仮想マシン (VM) を簡単に作成する方法を示します。例として Windows Server イメージを使用しますが、Azure では他にも使用可能なイメージが多数あります。イメージの選択肢は、サブスクリプションによって異なります。たとえば、デスクトップ イメージは MSDN サブスクリプション会員のみが使用できますです。

また、[独自のイメージ](virtual-machines-create-upload-vhd-windows-server.md)を使用して VM を作成することもできます。この方法や他の方法の詳細については、「[Windows 仮想マシンを作成するさまざまな方法](virtual-machines-windows-choices-create-vm.md)」を参照してください。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## 仮想マシンの作成

Windows Server 2012 R2 Datacenter を例として使用して、わずか数分で VM を作成して試すことができます。ほとんどの構成に Azure の既定の設定を使用できます。

1. [プレビュー ポータル](https://portal.azure.com)にサインインします。

2. ハブ メニューの **[新規]** をクリックします。

3. **新しい**ブレードで、**[Compute]**、**[Windows Server 2012 R2 Datacenter]** の順にクリックします。

	![Marketplace から VM イメージを選択する](./media/virtual-machines-windows-tutorial/marketplace_portal.png)

4. **[VM の作成]** ブレードで、**[ホスト名]** に VM の名前を入力し、管理 **[ユーザー名]** と強力な **[パスワード]** を入力します。**[ユーザー名]** とは、サーバーの管理に使用する管理アカウントの名前です。覚えやすい (また、他の人が予測しずらい) パスワードを作成します。**仮想マシンにログオンする際に、このユーザー名とパスワードが必要になります。**パスワードを忘れた場合、[この手順](virtual-machines-windows-reset-password.md)に従ってリセットできます。

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial/create_vm_name_pwd_portal.png)

5. **[価格レベル]**、**[オプションの構成]** などの既定の設定を確認します。これらの選択は、ドメイン メンバーシップなどのネットワークのオプションと VM のサイズに影響します。たとえば、仮想マシンでプレミアム ストレージを使用するには、プレミアム ストレージをサポートするリージョンとサイズを選択する必要があります。初めての仮想マシンの場合、通常、既定の設定をそのまま使用できます。

	>[AZURE.NOTE]プレミアム ストレージは、特定のリージョンの DS シリーズの仮想マシンで使用できます。プレミアム ストレージは、データベースなどの高負荷のワークロードに最適なストレージ オプションです。詳細については、[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](storage-premium-storage-preview-portal.md)に関するページを参照してください。

6. 設定の確認または更新を終了したら、**[作成]** をクリックします。

7. Azure が VM を作成している間の進捗状況は、ハブ メニューの **[通知]** で確認できます。Azure で VM が作成されるとスタート画面に表示されます。**[VM の作成]** ブレードで **[スタート画面にピン留めする]** をオフにした場合は表示されません。

## 仮想マシンへのログオン

VM を作成したら、VM にログオンして、その設定や、そこで実行するアプリケーションを管理できます。

>[AZURE.NOTE]要件やトラブルシューティングのヒントについては、「[RDP または SSH で Azure 仮想マシンに接続する](https://msdn.microsoft.com/library/azure/dn535788.aspx)」を参照してください。

1. まだサインインしていない場合は、[プレビュー ポータル](https://portal.azure.com)にサインインします。

2. スタートボードの VM をクリックします。VM を探す場合は、**[参照]** をクリックし、次に **[仮想マシン]** をクリックします。その後、一覧から VM を選択します。

3. VM ブレードで、**[接続]** をクリックします。

	![仮想マシンへのログオン](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. **[開く]** をクリックして、仮想マシン用に自動的に作成されたリモート デスクトップ プロトコル ファイルを使用します。

5. **[接続]** をクリックします。

6. 仮想マシンの作成時に作成したユーザー名とパスワードを入力し、**[OK]** をクリックします。

7. **[はい]** をクリックして、目的の仮想マシンであることを確認します。

	これで、仮想マシンを他のサーバーとまったく同様に扱うことができます。

## 次のステップ

Azure での Windows 仮想マシンの構成に関する詳細については、次の記事を参照してください。

[仮想マシンを仮想ネットワークまたはクラウド サービスと接続する](cloud-services-connect-virtual-machine.md)

[データ ディスクを仮想マシンに接続する方法](storage-windows-attach-disk.md)

[仮想マシンの可用性管理](../manage-availability-virtual-machines.md)

[Azure VM 構成設定について](http://msdn.microsoft.com/library/azure/dn763935.aspx)
 

<!---HONumber=July15_HO2-->