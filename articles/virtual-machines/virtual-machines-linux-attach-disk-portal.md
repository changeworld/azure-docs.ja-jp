---
title: "データ ディスクを Linux VM に接続する | Microsoft Docs"
description: "Azure ポータルでリソース マネージャー デプロイ モデルを使用して、新規または既存のデータ ディスクを Linux VM に接続する方法。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 7c481cfae5f97b71c0ab184419ceaa46ab3f5a5b
ms.openlocfilehash: 394d82e444bdbc8b07243d92743ceb660f142509


---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Azure ポータルで Linux VM にデータ ディスクを接続する方法
この記事では、Azure ポータルを使用して新しいディスクと既存のディスクの両方を Linux 仮想マシンに接続する方法について示します。 [Azure Portal で Windows VM にデータ ディスクを接続する](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)こともできます。 接続する前に、次のヒントを確認してください。

* 仮想マシンのサイズによって、接続できるデータ ディスク数は変わります。 詳細については、「 [仮想マシンのサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。
* Premium Storage を使用するには、DS シリーズまたは GS シリーズの仮想マシンが必要です。 これらの仮想マシンでは、Premium および Standard のストレージ アカウントのディスクを使用できます。 Premium Storage は特定のリージョンで使用できます。 詳細については、「 [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。
* 仮想マシンに接続されているディスクは、実際には Azure ストレージ アカウントの .vhd ファイルです。 詳細については、 [仮想マシン用のディスクと VHD](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。
* 新しいディスクの場合、接続時に Azure で自動的に作成されるので、最初に作成する必要はありません。
* 既存のディスクの場合、Azure ストレージ アカウントで .vhd ファイルを使用できる必要があります。 別の仮想マシンに接続されていない場合は既存の .vhd を使用できます。そうでなければ、独自の .vhd ファイルをストレージ アカウントにアップロードできます。


## <a name="find-the-virtual-machine"></a>仮想マシンの検索
1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. ハブ メニューで **[Virtual Machines]**をクリックします。
3. 一覧から仮想マシンを選択します。
4. [仮想マシン] ブレードにアクセスし、**[要点]** で、**[ディスク]** の順にクリックします。
   
    ![ディスク設定を開く](./media/virtual-machines-linux-attach-disk-portal/find-disk-settings.png)

次の手順に従って、[新しいディスク](#option-1-attach-a-new-disk)または[既存のディスク](#option-2-attach-an-existing-disk)を接続します。

## <a name="option-1-attach-a-new-disk"></a>オプション 1: 新しいディスクを接続する
1. **[ディスク]** ブレードの **[新しいディスクの接続]** をクリックします。
2. 既定の設定を確認し、必要に応じて更新して、 **[OK]**をクリックします。
   
   ![ディスク設定を確認する](./media/virtual-machines-linux-attach-disk-portal/attach-new.png)
3. Azure でディスクが作成され、仮想マシンに接続されると、仮想マシンのディスク設定の **[データ ディスク]**に新しいディスクが表示されます。

## <a name="option-2-attach-an-existing-disk"></a>オプション 2: 既存のディスクを接続する
1. **[ディスク]** ブレードの **[既存のディスクの接続]** をクリックします。
2. **[既存のディスクの接続]** の **[VHD ファイル]** をクリックします。
   
   ![既存のディスクを接続する](./media/virtual-machines-linux-attach-disk-portal/attach-existing.png)
3. **[ストレージ アカウント]**で、アカウントと、.vhd ファイルが格納されているコンテナーを選択します。
   
   ![VHD の場所を検索する](./media/virtual-machines-linux-attach-disk-portal/find-storage-container.png)
4. .vhd ファイルを選択します。
5. **[既存のディスクの接続]** の **[VHD ファイル]** に、選択したファイルが表示されます。 **[OK]**をクリックします。
6. Azure で仮想マシンにディスクが接続されると、仮想マシンのディスク設定の **[データ ディスク]**にそのディスクが表示されます。



## <a name="next-steps"></a>次のステップ
ディスクを追加した後、使用できるように準備する必要があります。 詳細については、「[方法: Linux での新しいデータ ディスクの初期化](virtual-machines-linux-classic-attach-disk.md#initialize-a-new-data-disk-in-linux)」を参照してください。



<!--HONumber=Nov16_HO5-->


