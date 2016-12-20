---
title: "データ ディスクを Windows VM に接続する | Microsoft Docs"
description: "Azure ポータルでリソース マネージャー デプロイ モデルを使用して、新規または既存のデータ ディスクを Windows VM に接続する方法。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3790fc59-7264-41df-b7a3-8d1226799885
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/02/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: e7ec8715087abaf5c96a6327d4bcde0ad3fd547a


---
# <a name="how-to-attach-a-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Azure ポータルで Windows VM にデータ ディスクを接続する方法
この記事では、Azure ポータルを使用して新しいディスクと既存のディスクの両方を Windows 仮想マシンに接続する方法について示します。 [Azure ポータルで Linux VM にデータ ディスクを接続する](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)こともできます。 接続する前に、次のヒントを確認してください。

* 仮想マシンのサイズによって、接続できるデータ ディスク数は変わります。 詳細については、「 [仮想マシンのサイズ](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。
* 新しいディスクの場合、接続時に Azure で自動的に作成されるので、最初に作成する必要はありません。
* 既存のディスクの場合、Azure ストレージ アカウントで .vhd ファイルを使用できる必要があります。 別の仮想マシンに接続されていない場合は既存の .vhd を使用できます。そうでなければ、独自の .vhd ファイルをストレージ アカウントにアップロードできます。

[Powershell を使用してデータ ディスクを接続する](virtual-machines-windows-ps-manage.md#add-a-data-disk-to-a-virtual-machine)こともできます。

[!INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="a-idinitializeinwsahow-to-initialize-a-new-data-disk-in-windows-server"></a><a id="initializeinWS"></a>方法: Windows Server で新しいデータ ディスクを初期化する
1. 仮想マシンへの接続 手順については、「 [Windows が実行されている Azure 仮想マシンに接続してログオンする方法](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。
2. 仮想マシンにログオンした後、 **サーバー マネージャー**を開きます。 左側のウィンドウで、 **[ファイル サービスと記憶域サービス]**を選択します。
   
    ![サーバー マネージャーを開く](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)
3. メニューを展開し、 **[ディスク]**を選択します。
4. **[ディスク]** セクションにディスクが一覧表示されます。 普通、disk 0、disk 1、disk 2 といった名前です。 disk 0 はオペレーティング システム ディスク、disk 1 は一時ディスク、disk 2 は VM にアタッチしたデータ ディスクです。 新しいデータ ディスクのパーティションは **[不明]**と表示されます。 ディスクを右クリックし、 **[初期化]**を選択します。
5. ディスクの初期化時にすべてのデータが消去されることが通知されます。 **[はい]** をクリックして警告を了解し、ディスクを初期化します。 完了すると、パーティションは **[GPT]**と表示されます。 もう一度ディスクを右クリックし、 **[新しいボリューム]**を選択します。
6. 既定の値を使用してウィザードを完了します。 ウィザードが終了すると、 **[ボリューム]** セクションに新しいボリュームが表示されます。 ディスクがオンラインになり、データを格納できるようになります。

    ![ボリュームの初期化に成功](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [!NOTE]
> VM のサイズによって、アタッチできるディスクの数が決まります。 詳細については、「 [仮想マシンのサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。
> 
> 

## <a name="next-steps"></a>次のステップ
アプリケーションで D: ドライブを使用してデータを保存する必要がある場合は、 [Windows 一時ディスクのドライブ文字を変更](virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)できます。




<!--HONumber=Nov16_HO3-->


