---
title: Windows VM 用のディスクと VHD について | Microsoft Docs
description: Azure での Windows 仮想マシン用のディスクと VHD の基本について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn

---
# <a name="about-disks-and-vhds-for-azure-virtual-machines"></a>Azure 仮想マシン用のディスクと VHD について
Azure の仮想マシンでは、その他のコンピューターとまったく同じように、オペレーティング システム、アプリケーション、およびデータを格納するための場所としてディスクを使用します。 Azure のすべての仮想マシンには、Windows オペレーティング システム ディスクと一時ディスクの少なくとも 2 つのディスクがあります。 オペレーティング システム ディスクはイメージから作成されます。オペレーティング システム ディスクとイメージの両方は仮想ハード ディスク (VHD) であり、Azure のストレージ アカウントに格納されます。 仮想マシンでは 1 つ以上のデータ ディスクも保持することができ、これらも VHD として格納されます。 この記事は、 [Linux 仮想マシン](virtual-machines-linux-about-disks-vhds.md)にも利用できます。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="operating-system-disk"></a>オペレーティング システム ディスク
どの仮想マシンにも 1 つのオペレーティング システム ディスクが取り付けられています。 これは SATA ドライブとして登録され、C ドライブのラベルが付けられます。 このディスクの最大容量は 1023 ギガバイトです。 

## <a name="temporary-disk"></a>一時ディスク
一時ディスクが自動的に作成されます。 この一時ディスクには D ドライブのラベルが付けられ、pagefile.sys を保存するために使用されます。 

仮想マシンのサイズに基づいて、一時ディスクのサイズは異なります。 詳細については、「 [Azure の仮想マシンのサイズ](virtual-machines-windows-sizes.md)」を参照してください。

> [!WARNING]
> 一時的なディスクにデータを格納しないでください。 これはアプリケーションとプロセスに一時的なストレージを提供し、ページ ファイルやスワップ ファイルなどのデータのみを格納するためのものです。 このディスクを再マッピングするには、 [Windows 一時ディスクのドライブ文字を変更する方法](virtual-machines-windows-classic-change-drive-letter.md)に関するページを参照してください。
> 
> 

Azure による一時ディスクの使用方法については、 [Microsoft Azure Virtual Machines の一時ドライブの概要](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>データ ディスク
データ ディスクは仮想マシンに取り付けられる VHD であり、ユーザーが保存しておく必要があるアプリケーションなどのデータを格納するためのものです。 データ ディスクは SCSI ドライブとして登録され、ユーザーが選択した文字のラベルが付けられます。  各ディスクの最大容量は 1023 ギガバイトです。 仮想マシンのサイズにより、そこに取り付けできるデータ ディスクの数と、ディスクをホストするために使用できるストレージの種類が決まります。

> [!NOTE]
> 仮想マシンの容量については、「 [Azure の Windows 仮想マシンのサイズ](virtual-machines-windows-sizes.md)」を参照してください。
> 
> 

ユーザーがイメージから仮想マシンを作成するときに、Azure はオペレーティング システム ディスクを作成します。 ユーザーがデータ ディスクを含むイメージを使用する場合、Azure は仮想マシンの作成時にデータ ディスクも作成します。 それ以外の場合は、仮想マシンを作成した後にデータ ディスクを追加してください。

仮想マシンにディスクを**取り付ける**ことで、ユーザーは仮想マシンにデータ ディスクをいつでも追加できます。 ユーザーがアップロードした VHD やストレージ アカウントにコピーした VHD を使用できます。または Azure が作成する VHD も使用できます。 データ ディスクを取り付けると、VHD ファイルが VM に関連付けられます。VHD で「リース」を設定しておけば、仮想マシンに取り付けている間にストレージから削除することはできなくなります。

## <a name="about-vhds"></a>VHD について
Azure で使用される VHD は .vhd ファイルです。Azure では Standard または Premium Storage アカウントでページ BLOB としてこれらを格納します。 ページ BLOB の詳細については、「[ブロック BLOB およびページ BLOB について](https://msdn.microsoft.com/library/ee691964.aspx)」を参照してください。 Premium Storage の詳細については、「[Premium Storage: Azure Virtual Machines ワークロード向けの高パフォーマンス ストレージ](../storage/storage-premium-storage.md)」を参照してください。

Azure は VHD フォーマットの固定ディスクをサポートしています。 固定フォーマットの場合、ファイル内で論理ディスクがリニアにレイアウトされるため、ディスク オフセット X は BLOB オフセット X に格納されます。BLOB 末尾の小さなフッターに、VHD のプロパティが記述されます。 固定フォーマットの場合、ほとんどのディスクに大きな未使用の範囲が含まれるため、容量が無駄になることがよくあります。 しかし、Azure では .vhd ファイルをスパース フォーマットで格納するため、固定ディスクのメリットと動的ディスクのメリットを同時に享受できます。 詳細については、「[仮想ハード ディスクの概要](https://technet.microsoft.com/library/dd979539.aspx)」をご覧ください。

ディスクやイメージを作成する際にソースとして使用する場合、Azure でのすべての .vhd ファイルは読み出し専用です。 ユーザーがディスクやイメージを作成するときに Azure は .vhd ファイルのコピーを作成します。 これらのコピーは、ユーザーが VHD をどのように使用するかに応じて、読み出し専用または読み書き可能にすることができます。

ユーザーがイメージから仮想マシンを作成するときに、Azure はソースの .vhd ファイルのコピーである仮想マシンのディスクを作成します。 Azure では、誤って削除されるのを防ぐために、イメージ、オペレーティング システム ディスク、またはデータ ディスクを作成する際に使用されるソースの .vhd ファイルにリースを設定します。

ディスクやイメージを削除してリースを解除しなければ、ソースの .vhd ファイルを削除できません。 仮想マシンを削除し、関連付けられたすべてのディスクを削除することで、ユーザーは仮想マシン、オペレーティング システム ディスク、およびソースの .vhd ファイルをすべて一度に削除することができます。 ただし、データ ディスクのソースである .vhd ファイルを削除するには、いくつかの手順を決められた順序で行う必要があります。 まず、仮想マシンからディスクを取り外し、ディスクを削除し、続いて .vhd ファイルを削除します。

> [!WARNING]
> ユーザーがストレージからソースの .vhd ファイルを削除する場合、またはストレージ アカウントを削除する場合、Microsoft はそのデータを回復することはできません。
> 
> 

## <a name="next-steps"></a>次のステップ
* [ディスクのアタッチ](virtual-machines-windows-attach-disk-portal.md) による VM 用のストレージの追加。
* [Windows VM イメージを Azure にアップロード](virtual-machines-windows-upload-image.md) します。
* [Windows 一時ディスクのドライブ文字を変更する方法](virtual-machines-windows-classic-change-drive-letter.md) します。

<!--HONumber=Oct16_HO2-->


