---
title: "VM をクラシック ポータルで作成する | Microsoft Docs"
description: "Azure クラシック ポータルで Windows 仮想マシンを作成します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1871f823-ebd7-4eff-9a22-8e2411555595
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: ede78ff35ec6e7007e7a1c7b946c8e17bd425f37


---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>Windows を実行する仮想マシンを Azure クラシック ポータルで作成する
> [!div class="op_single_selector"]
> * [Azure クラシック ポータル](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [PowerShell: クラシック デプロイ](virtual-machines-windows-classic-create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> 
> 

<br>

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../azure-resource-manager/resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 **新しい Azure Portal** を使って [Resource Manager デプロイメント モデルを使用してこれらの手順を実行する](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)方法について説明します。 

このチュートリアルでは、Windows を実行する Azure 仮想マシン (VM) を Azure クラシック ポータルで作成する方法について説明します。 例として Windows Server イメージを使用しますが、Azure では他にも使用可能なイメージが多数あります。 イメージの選択肢は、サブスクリプションによって異なります。 たとえば、Windows デスクトップ イメージは MSDN サブスクライバーが使用できます。

このセクションでは、Azure 旧ポータルの **[ギャラリーから]** オプションを使用して仮想マシンを作成する方法について説明します。 このオプションは、 **[簡易作成]** オプションよりも多数の構成の選択肢があります。 たとえば、仮想マシンを仮想ネットワークに参加させる場合、 **[ギャラリーから]** オプションを使用する必要があります。

また、 [独自のイメージ](virtual-machines-windows-classic-createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)を使用して VM を作成することもできます。 この方法や他の方法の詳細については、「 [Windows 仮想マシンを作成するさまざまな方法](virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

## <a name="video-walkthrough"></a>ビデオ チュートリアル
次は、このチュートリアルのガイドです。

[!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Creating-a-Windows-VM-on-Microsoft-Azure-GA-Portal/player]


## <a id="createvirtualmachine"> </a>仮想マシンの作成
[!INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>次のステップ
* 新しい Azure Portal で [Resource Manager デプロイメント モデルを使用して VM を作成する](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)方法について説明します。 
* 仮想マシンにログオンします。 手順については、 [Windows Server が実行されている仮想マシンへのログオン](virtual-machines-windows-classic-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関する記事をご覧ください。
* データを格納するディスクを接続します。 空のディスクと、データが含まれているディスクのどちらも接続できます。 手順については、「 [クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにデータ ディスクをアタッチする](virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」をご覧ください。




<!--HONumber=Dec16_HO1-->


