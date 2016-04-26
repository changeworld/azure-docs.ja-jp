<properties
	pageTitle="Linux VM 用のディスクと VHD について | Microsoft Azure"
	description="Azure での Linux 仮想マシン用のディスクと VHD の基本について説明します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2016"
	ms.author="cynthn"/>

# Azure 仮想マシン用のディスクと VHD について

Azure の仮想マシンでは、その他のコンピューターとまったく同じように、オペレーティング システム、アプリケーション、およびデータを格納するための場所としてディスクを使用します。Azure のすべての仮想マシンには、Linux オペレーティング システム ディスク (Linux VM の場合) と一時ディスクの少なくとも 2 つのディスクがあります。オペレーティング システム ディスクはイメージから作成されます。オペレーティング システム ディスクとイメージの両方は実際に仮想ハード ディスク (VHD) であり、Azure のストレージ アカウントに格納されます。仮想マシンでは 1 つ以上のデータ ディスクも保持することができ、これらも VHD として格納されます。この記事は、[Windows 仮想マシン](virtual-machines-windows-about-disks-vhds.md)にも利用できます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-about-disks-vhds](../../includes/virtual-machines-common-about-disks-vhds.md)]

## 次のステップ

-  [ディスクのアタッチ](virtual-machines-linux-attach-disk-portal.md)による VM 用のストレージの追加。
-  冗長性を持たせるための[ソフトウェア RAID の構成](virtual-machines-linux-configure-raid.md)。
-  追加の VM を短時間でデプロイできるようにするための [Linux 仮想マシンのキャプチャ](virtual-machines-linux-classic-capture-image.md)。

<!---HONumber=AcomDC_0413_2016-->