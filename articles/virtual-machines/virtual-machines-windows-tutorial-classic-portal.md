<properties
	pageTitle="Azure 上で Windows を実行する仮想マシンの作成"
	description="Azure 管理ポータルでの Windows 仮想マシン (VM) の作成について説明します。"
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
	ms.topic="article"
	ms.date="06/05/2015"
	ms.author="kathydav"/>

# Windows を実行する仮想マシンを Microsoft Azure 管理ポータルで作成する

> [AZURE.SELECTOR]
- [Azure Preview Portal](virtual-machines-windows-tutorial.md)
- [Azure Portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource Management](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service Management](virtual-machines-ps-create-preconfigure-windows-vms.md)


このチュートリアルでは、Azure の管理ポータルで Azure 仮想マシン (VM) を簡単に作成する方法を示します。例として Windows Server イメージを使用しますが、Azure では他にも使用可能なイメージが多数あります。イメージの選択肢は、サブスクリプションによって異なります。たとえば、デスクトップ イメージは MSDN サブスクリプション会員のみが使用できますです。

また、[独自のイメージ](virtual-machines-create-upload-vhd-windows-server.md)を使用して VM を作成することもできます。この方法や他の方法の詳細については、「[Windows 仮想マシンを作成するさまざまな方法](virtual-machines-windows-choices-create-vm.md)」を参照してください。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## <a id="createvirtualmachine"> </a>仮想マシンの作成方法

このセクションでは、Azure 管理ポータルのオプションの **[ギャラリーから]** を使用して仮想マシンを作成する方法について説明します。このオプションは、**[簡易作成]** オプションよりも多数の構成の選択肢があります。たとえば、仮想マシンを仮想ネットワークに参加させる場合、**[ギャラリーから]** オプションを使用する必要があります。

> [AZURE.NOTE]さらに、より機能が豊富でカスタマイズも可能な [Azure プレビュー ポータル](https://portal.azure.com)を使用すれば、仮想マシンの作成、複数の仮想マシンのアプリケーション テンプレートのデプロイの自動化、VM の強化された監視や診断の機能の使用など、さまざまな操作を試すことができます。この 2 つのポータルにある利用可能な VM の構成オプションには、性質上、重複するものもありますが、まったく同じではありません。

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

## 次のステップ

- 仮想マシンにログオンします。詳細については、「[Windows Server が実行されている仮想マシンにログオンする方法](virtual-machines-log-on-windows-server.md)」を参照してください。

- データを格納するディスクを接続します。空のディスクと、データが含まれているディスクのどちらも接続できます。手順については、[データ ディスクの接続に関するチュートリアル](storage-windows-attach-disk.md)を参照してください。

## その他のリソース

VM 用に構成できる内容とそのタイミングについては、[「Azure VM 構成設定について」](http://msdn.microsoft.com/library/azure/dn763935.aspx)を参照してください。
 

<!---HONumber=July15_HO4-->