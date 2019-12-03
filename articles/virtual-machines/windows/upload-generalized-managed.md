---
title: 汎用化したオンプレミスの VHD から管理対象の Azure VM を作成する
description: 汎用化した VHD を Azure にアップロードし、それを使用して Resource Manager デプロイ モデルで新しい VM を作成します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: d0995fed61d169cc173ca01767c2e48f4f798b0d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2019
ms.locfileid: "74067428"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>汎用化した VHD をアップロードして Azure で新しい VM を作成する

この記事では、汎用化した VM の VHD を PowerShell を使用して Azure にアップロードし、VHD からイメージを作成し、イメージから新しい VM を作成する方法を説明します。 オンプレミスの仮想化ツールや別のクラウドからエクスポートされた VHD をアップロードできます。 新しい VM に [Managed Disks](managed-disks-overview.md) を使用すると VM 管理が簡略化されます。また、VM を可用性セット内に配置したときの可用性が向上します。 

サンプル スクリプトの場合は、[VHD を Azure にアップロードして新しい VM を作成するサンプル スクリプト](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)を参照してください。

## <a name="before-you-begin"></a>開始する前に

- VHD を Azure にアップロードする前に、「[Prepare a Windows VHD or VHDX to upload to Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」(Azure にアップロードする Windows VHD または VHDX を準備する) に従う必要があります。
- [Managed Disks](managed-disks-overview.md) への移行を開始する前に、「[Plan for the migration to Managed Disks (Managed Disks への移行の計画)](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)」をご確認ください。

 


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Sysprep を使用してソース VM を一般化する

特に重要な点は、Sysprep がすべての個人アカウント情報を削除して、マシンをイメージとして使用できるように準備することです。 Sysprep の詳細については、[Sysprep の概要](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)に関するページを参照してください。

コンピューター上で実行されるサーバー ロールが Sysprep でサポートされていることを確認します。 詳しくは、「 [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)」 (サーバー ロールに対する Sysprep サポート) をご覧ください。

> [!IMPORTANT]
> 初めて VHD を Azure にアップロードする前に Sysprep を実行する予定の場合は、[VM の準備](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ができていることを確認してください。 
> 
> 

1. Windows 仮想マシンへのサインイン
2. 管理者としてコマンド プロンプト ウィンドウを開きます。 ディレクトリを `%windir%\system32\sysprep` に変更し、`sysprep.exe`を実行します。
3. **[システム準備ツール]** ダイアログ ボックスで **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、 **[一般化する]** チェック ボックスがオンになっていることを確認します。
4. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。
5. **[OK]** を選択します。
   
    ![Sysprep の開始](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep は完了時に仮想マシンをシャットダウンします。 VM は再起動しないでください。


## <a name="upload-the-vhd-to-your-storage-account"></a>ストレージ アカウントに VHD をアップロードする

VHD をマネージド ディスクに直接アップロードできるようになりました。 手順については、「[Azure PowerShell を使用して Azure に VHD をアップロードする](disks-upload-vhd-to-managed-disk-powershell.md)」を参照してください。


## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>アップロードした VHD から管理イメージを作成する 

一般化済みの OS マネージド ディスクから管理イメージを作成します。 次の値は実際の値に変更してください。


まずいくつかのパラメーターを設定します。

```powershell
$location = "East US" 
$imageName = "myImage"
```

一般化した OS VHD を使ってイメージを作成します。

```powershell
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>VM の作成

イメージが用意できたので、イメージから新しい VM を 1 つ以上作成できます。 この例では、*myResourceGroup* で *myImage* から *myVM* という名前の VM が作成されます。


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>次の手順

新しい仮想マシンにサインインします。 詳しくは、「[Windows が実行されている Azure 仮想マシンに接続してログオンする方法](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。 

