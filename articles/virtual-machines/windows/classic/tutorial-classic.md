---
title: VM を Azure Portal で作成する | Microsoft Docs
description: Azure ポータルで Windows 仮想マシンを作成します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 1871f823-ebd7-4eff-9a22-8e2411555595
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 5fd2128ff436d3211f41c7dfdcc4c2b8aabd0eb0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38232328"
---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-portal"></a>Windows を実行する仮想マシンを Azure ポータルで作成する
> [!div class="op_single_selector"]
> * [Azure Portal](tutorial.md)
> * [PowerShell: クラシック デプロイ](create-powershell.md)
>
>

<br>

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。 **Azure Portal** を使って [Resource Manager デプロイ モデルを使用してこれらの手順を実行する](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)方法について理解してください。
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

このチュートリアルでは、Windows を実行する Azure 仮想マシン (VM) を Azure Portal で作成する方法について説明します。 例として Windows Server イメージを使用しますが、Azure では他にも使用可能なイメージが多数あります。 イメージの選択肢は、サブスクリプションによって異なります。 たとえば、Windows デスクトップ イメージは MSDN サブスクライバーが使用できます。

このセクションでは、Azure Portal の **ダッシュボード** を使用して仮想マシンを選択して作成する方法について説明します。

また、 [独自のイメージ](createupload-vhd.md)を使用して VM を作成することもできます。 この方法や他の方法の詳細については、「 [Windows 仮想マシンを作成するさまざまな方法](../../virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

## <a id="createvirtualmachine"> </a>仮想マシンの作成
[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>次の手順
* Azure Portal で [Resource Manager デプロイ モデルを使用して VM を作成する](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)方法を理解します。
* 仮想マシンにログオンします。 手順については、 [Windows Server が実行されている仮想マシンへのログオン](connect-logon.md)に関する記事をご覧ください。
* データを格納するディスクを接続します。 空のディスクと、データが含まれているディスクのどちらも接続できます。 手順については、「 [クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにデータ ディスクをアタッチする](attach-disk.md)」をご覧ください。
