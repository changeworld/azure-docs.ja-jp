---
title: "Windows VHD の一般化 | Microsoft Docs"
description: "Resource Manager デプロイメント モデルで使用する Windows VM を Sysprep を使用して一般化する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a745d400-c8be-48b4-a891-4a18495ef3fd
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 9e9d973ef36cde4f0a1ada2ba7bf7d01a8d8f687


---
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Sysprep を使用した Windows 仮想マシンの一般化
このセクションでは、Windows 仮想マシンをイメージとして使用できるように一般化する方法について説明します。 特に重要な点は、Sysprep がすべての個人アカウント情報を削除して、マシンをイメージとして使用できるように準備することです。 Sysprep の詳細については、「 [Sysprep の使用方法: 紹介](http://technet.microsoft.com/library/bb457073.aspx)」を参照してください。

コンピューター上で実行されるサーバー ロールが Sysprep でサポートされていることを確認します。 詳しくは、「 [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> 初めて VHD を Azure にアップロードする前に Sysprep を実行する場合、Sysprep の実行前に [VM の準備](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ができていることを確認してください。 
> 
> 

1. Windows 仮想マシンへのサインイン
2. 管理者としてコマンド プロンプト ウィンドウを開きます。 ディレクトリを **%windir%\system32\sysprep** に変更し、`sysprep.exe` を実行します。
3. **[システム準備ツール]** ダイアログ ボックスで **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。
4. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。
5. **[OK]**をクリックします。
   
    ![Sysprep の開始](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)
6. Sysprep は完了時に仮想マシンをシャットダウンします。 

## <a name="next-steps"></a>次のステップ
* VM がオンプレミスの場合、[VHD を Azure にアップロード](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)できるようになりました。
* VM が既に Azure に存在する場合、[一般化された VM からイメージを作成](virtual-machines-windows-capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)できるようになりました。




<!--HONumber=Nov16_HO3-->


