---
title: Azure で管理イメージから VM を作成する | Microsoft Docs
description: Resource Manager デプロイ モデルで Azure PowerShell または Azure Portal を使って、一般化した管理イメージから仮想マシンを作成します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: cynthn
ms.openlocfilehash: 6baf784068b1fba0c35d2848b8d2dda4f1064a2d
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867982"
---
# <a name="create-a-vm-from-a-managed-image"></a>管理イメージから VM を作成する

PowerShell または Azure Portal を使って、管理 VM イメージから複数の VM を作成できます。 管理 VM イメージには、OS や データ ディスクなど、VM の作成に必要な情報が含まれています。 OS ディスクやデータ ディスクなど、イメージを構成する VHD は、管理ディスクとして保管されます。 

新しい VM の作成に使用する[管理 VM イメージを作成](capture-image-resource.md)しておく必要があります。 

## <a name="use-the-portal"></a>ポータルの使用

1. [Azure Portal](https://portal.azure.com)を開きます。
2. 左側のメニューで、**[すべてのリソース]** を選びます。 **[種類]** でリソースを並べ替えて、イメージを簡単に見つけることができます。
3. 一覧から使うイメージを選びます。 イメージの **[概要]** ページが開きます。
4. メニューから **[+ VM の作成]** をクリックします。
5. 仮想マシンの情報を入力します。 ここに入力したユーザー名とパスワードが、仮想マシンへのログインに使用されます。 完了したら、**[OK]** をクリックします。 既存のリソース グループに新しい VM を作成するか、**[新規作成]** を選んで VM を格納する新しいリソース グループを作成することができます。
6. VM のサイズを選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、**[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。 
7. **[設定]** で必要な変更を行い、**[OK]** をクリックします。 
8. 概要ページでは、イメージ名が **[プライベート イメージ]** の一覧に表示されます。 **[OK]** をクリックして、仮想マシンのデプロイを開始します。


## <a name="use-powershell"></a>PowerShell の使用

PowerShell を使用すると、[New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm) コマンドレットに対して設定された簡略化されたパラメーターを使用して、イメージから VM を作成できます。 このイメージは、VM を作成するのと同じリソース グループに存在する必要があります。

この例では、AzureRM モジュール バージョン 5.6.0 以降が必要です。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

New-AzureRmVm に対して設定された簡略化されたパラメーターには、イメージから VM を作成するための名前、リソース グループ、およびイメージ名を指定するだけで済みますが、自動的に作成されるすべてのリソースの名前として **-Name** パラメーターの値が使用されます。 この例では、各リソースのより詳細な名前を指定しますが、それらがコマンドレットで自動的に作成されるようにしてください。 また、事前にリソース (仮想ネットワークなど) を作成し、その名前をコマンドレットに渡すこともできます。 その名前でリソースが見つかった場合は、既存のリソースが使用されます。

次の例では、*myImage* という名前のイメージから *myVMFromImage* という名前の VM を (*myResourceGroup* リソース グループ内に) 作成します。 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```



## <a name="next-steps"></a>次の手順
Azure PowerShell を使用して新しい仮想マシンを管理するには、「[Azure PowerShell モジュールを使用して Windows VM を作成および管理する](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

