---
title: Microsoft Azure Windows VM 用の非管理対象ディスク ストレージ (ページ BLOB) とマネージド ディスク ストレージについて | Microsoft Docs
description: Azure での Windows 仮想マシン用の非管理対象ディスク ストレージ (ページ BLOB) とマネージド ディスク ストレージの基本について説明します。
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.tgt_pltfrm: windows
ms.topic: article
ms.date: 11/15/2017
ms.author: rogarana
ms.component: disks
ms.openlocfilehash: 4fa8341b4d1953e3c59d345f45853f4c9a4a2941
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715456"
---
# <a name="about-disks-storage-for-azure-windows-vms"></a>Azure Windows VM 用のディスク ストレージについて
Azure の仮想マシンでは、その他のコンピューターとまったく同じように、オペレーティング システム、アプリケーション、およびデータを格納するための場所としてディスクを使用します。 Azure のすべての仮想マシンには、Windows オペレーティング システム ディスクと一時ディスクの少なくとも 2 つのディスクがあります。 オペレーティング システム ディスクはイメージから作成されます。オペレーティング システム ディスクとイメージの両方は仮想ハード ディスク (VHD) であり、Azure のストレージ アカウントに格納されます。 仮想マシンでは 1 つ以上のデータ ディスクも保持することができ、これらも VHD として格納されます。 

この記事では、ディスクのさまざまな使用方法について説明し、作成および使用できるディスクの種類を示します。 この記事は、 [Linux 仮想マシン](../linux/about-disks-and-vhds.md) にも利用できます。

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>VM で使用されるディスク

ディスクは VM でどのように使用されるのでしょうか。

### <a name="operating-system-disk"></a>オペレーティング システム ディスク
どの仮想マシンにも 1 つのオペレーティング システム ディスクが取り付けられています。 これは SATA ドライブとして登録され、C ドライブのラベルが付けられます。 このディスクの最大容量は 2,048 ギガバイト (GB) です。 

### <a name="temporary-disk"></a>一時ディスク
各 VM には、一時ディスクが含まれています。 一時ディスクは、アプリケーションとプロセスのための一時的なストレージを提供し、ページ ファイルやスワップ ファイルなどのデータのみを格納するためのものです。 一時ディスクのデータは、[メンテナンス イベント](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime)中、または [VM の再デプロイ](redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)時に失われる可能性があります。 VM の標準の再起動が正常に実行されている間、一時ドライブのデータは保持されます。 

この一時ディスクには D ドライブのラベルが付けられ、pagefile.sys を保存するために使用されます。 このディスクを再マッピングするには、 [Windows 一時ディスクのドライブ文字を変更する方法](change-drive-letter.md)に関するページを参照してください。 仮想マシンのサイズに基づいて、一時ディスクのサイズは異なります。 詳細については、「 [Azure の仮想マシンのサイズ](sizes.md)」を参照してください。

Azure による一時ディスクの使用方法については、「 [Understanding the temporary drive on Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)


### <a name="data-disk"></a>データ ディスク
データ ディスクは仮想マシンに取り付けられる VHD であり、ユーザーが保存しておく必要があるアプリケーションなどのデータを格納するためのものです。 データ ディスクは SCSI ドライブとして登録され、ユーザーが選択した文字のラベルが付けられます。 各ディスクの最大容量は 4,095 GB です。 仮想マシンのサイズにより、そこに取り付けできるデータ ディスクの数と、ディスクをホストするために使用できるストレージの種類が決まります。

> [!NOTE]
> 仮想マシンの容量については、「 [Azure の Windows 仮想マシンのサイズ](sizes.md)」を参照してください。
> 

ユーザーがイメージから仮想マシンを作成するときに、Azure はオペレーティング システム ディスクを作成します。 ユーザーがデータ ディスクを含むイメージを使用する場合、Azure は仮想マシンの作成時にデータ ディスクも作成します。 それ以外の場合は、仮想マシンを作成した後にデータ ディスクを追加してください。

仮想マシンにディスクを**取り付ける**ことで、ユーザーは仮想マシンにデータ ディスクをいつでも追加できます。 ユーザーがアップロードした VHD やストレージ アカウントにコピーした VHD を使用できます。または Azure によって作成される空の VHD も使用できます。 データ ディスクを取り付けると、VHD ファイルが VM に関連付けられます。VHD で "リース" を設定しておけば、仮想マシンに取り付けている間にストレージから削除することはできなくなります。


[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="one-last-recommendation-use-trim-with-unmanaged-standard-disks"></a>最後の 1 つの推奨事項: Standard非管理ディスクでの TRIM の使用 

Standard 非管理ディスク (HDD) を使用する場合は、TRIM を有効にする必要があります。 TRIM はディスク上の未使用のブロックを破棄するため、実際に使用しているストレージにのみ課金されます。 これにより、サイズの大きいファイルを作成した後に削除した場合、コストを節約できます。 

次のコマンドを実行すると、TRIM の設定を確認できます。 Windows VM 上でコマンド プロンプトを開いて、次のように入力します。


```
fsutil behavior query DisableDeleteNotify
```

このコマンドが 0 を返す場合、TRIM は適切に有効化されています。 1 が返される場合は、次のコマンドを実行して TRIM を有効にします。

```
fsutil behavior set DisableDeleteNotify 0
```

> [!NOTE]
> 注: Trim は Windows Server 2012/Windows 8 以上でサポートされます。「[New API allows apps to send "TRIM and Unmap" hints to storage media](https://msdn.microsoft.com/windows/compatibility/new-api-allows-apps-to-send-trim-and-unmap-hints)」(新しい API を使用してアプリで "TRIM および Unmap" のヒントをストレージ メディアに送る方法) を参照してください。
> 

<!-- Might want to match next-steps from overview of managed disks -->
## <a name="next-steps"></a>次の手順
* [ディスクのアタッチ](attach-disk-portal.md) による VM 用のストレージの追加。
* [スナップショットの作成](snapshot-copy-managed-disk.md)。
* 
  [マネージド ディスクへの変換](convert-unmanaged-to-managed-disks.md)。


