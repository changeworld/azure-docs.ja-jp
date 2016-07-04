<properties
	pageTitle="Windows VM 用のディスクと VHD について | Microsoft Azure"
	description="Azure での Windows 仮想マシン用のディスクと VHD の基本について説明します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="cynthn"/>

# Azure 仮想マシン用のディスクと VHD について

Azure の仮想マシンでは、その他のコンピューターとまったく同じように、オペレーティング システム、アプリケーション、およびデータを格納するための場所としてディスクを使用します。Azure のすべての仮想マシンには、Windows オペレーティング システム ディスク (Windows VM の場合) と一時ディスクの少なくとも 2 つのディスクがあります。オペレーティング システム ディスクはイメージから作成されます。オペレーティング システム ディスクとイメージの両方は実際に仮想ハード ディスク (VHD) であり、Azure のストレージ アカウントに格納されます。仮想マシンでは 1 つ以上のデータ ディスクも保持することができ、これらも VHD として格納されます。この記事は、[Linux 仮想マシン](virtual-machines-linux-about-disks-vhds.md)にも利用できます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-about-disks-vhds](../../includes/virtual-machines-common-about-disks-vhds.md)]

## 次のステップ
-  VM のデプロイをスケール アウトできるよう、[Windows 仮想マシンをキャプチャ](virtual-machines-windows-capture-image.md)します。
-  新しい VM の作成時に使用する [Windows VM イメージを Azure にアップロード](virtual-machines-windows-upload-image.md)します。
-  D: ドライブをデータに使用できるよう、[Windows 一時ディスクのドライブ文字を変更](virtual-machines-windows-classic-change-drive-letter.md)します。

<!---HONumber=AcomDC_0622_2016-->