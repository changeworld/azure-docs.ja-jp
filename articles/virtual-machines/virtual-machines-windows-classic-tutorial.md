<properties
	pageTitle="Windows を実行する VM をクラシック ポータルで作成する | Microsoft Azure"
	description="Azure クラシック ポータルで Windows 仮想マシンを作成します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="cynthn"/>

# Windows を実行する仮想マシンを Azure クラシック ポータルで作成する

> [AZURE.SELECTOR]
- [Azure ポータル](virtual-machines-windows-hero-tutorial.md)
- [Azure クラシック ポータル](virtual-machines-windows-classic-tutorial.md)
- [PowerShell: Resource Manager によるデプロイ](virtual-machines-windows-ps-manage.md)
- [PowerShell: クラシック デプロイ](virtual-machines-windows-classic-create-powershell.md)

<!-- HHTML comment in to break between the selector and the note in the include below-->

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager デプロイ モデルを使用してこれらの手順を実行する](virtual-machines-windows-hero-tutorial.md)方法について説明します。

このチュートリアルでは、Windows を実行する Azure 仮想マシン (VM) を Azure クラシック ポータルで簡単に作成する方法を説明します。例として Windows Server イメージを使用しますが、Azure では他にも使用可能なイメージが多数あります。イメージの選択肢は、サブスクリプションによって異なります。たとえば、Windows デスクトップ イメージは MSDN サブスクライバーが使用できます。


また、[独自のイメージ](virtual-machines-windows-classic-createupload-vhd.md)を使用して VM を作成することもできます。この方法や他の方法の詳細については、「[Windows 仮想マシンを作成するさまざまな方法](virtual-machines-windows-creation-choices.md)」を参照してください。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## ビデオ チュートリアル

次は、このチュートリアルのガイドです。

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>仮想マシンの作成方法

このセクションでは、Azure 旧ポータルの **[ギャラリーから]** オプションを使用して仮想マシンを作成する方法について説明します。このオプションは、**[簡易作成]** オプションよりも多数の構成の選択肢があります。たとえば、仮想マシンを仮想ネットワークに参加させる場合、**[ギャラリーから]** オプションを使用する必要があります。

> [AZURE.NOTE] さらに、より機能が豊富でカスタマイズも可能な Azure ポータルを使用すれば、仮想マシンの作成、強化された監視機能や診断機能の使用、Premium Storage の使用など、さまざまな操作を試すことができます。この 2 つのポータルにある利用可能な仮想マシン構成オプションには、性質上、重複するものもありますが、まったく同じではありません。たとえば、Azure ポータルを利用して、Premium Storage を使用する仮想マシンを構成します。

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## 次のステップ

- 仮想マシンにログオンします。手順については、[Windows Server が実行されている仮想マシンへのログオン](virtual-machines-windows-classic-connect-logon.md)に関する記事をご覧ください。

- データを格納するディスクを接続します。空のディスクと、データが含まれているディスクのどちらも接続できます。手順については、「[クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにデータ ディスクをアタッチする](virtual-machines-windows-classic-attach-disk.md)」をご覧ください。

<!---HONumber=AcomDC_0629_2016-->