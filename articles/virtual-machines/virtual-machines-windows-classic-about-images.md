---
title: "Windows 仮想マシンのイメージについて | Microsoft Docs"
description: "Azure で Windows 仮想マシンのイメージを使用する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 66ff3fab-8e7f-4dff-b8da-ab1c9c9c9af8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/21/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 28720bcedb4df66c2f7434f25ed251d4e116c415


---
# <a name="about-images-for-windows-virtual-machines"></a>Windows 仮想マシンのイメージについて
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Resource Manager モデルでのイメージの検索と使用の詳細については、[こちら](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)をご覧ください。

[!INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]

## <a name="working-with-images"></a>イメージの操作
Azure サブスクリプションに使用できるイメージの管理には、Azure PowerShell モジュールを使用することができます。 いくつかのイメージ タスクには Azure クラシック ポータルを使用することもできますが、コマンドラインの方が多くのオプションを使用できます。

* **すべてのイメージを取得する**: `Get-AzureVMImage` は、現在のサブスクリプションで使用できるすべてのイメージ (自分のイメージと Azure またはパートナーが提供しているイメージ) の一覧を返します。 つまり、取得する一覧はかなり大きくなる可能性があります。 次の例では、取得する一覧を小さくする方法を示します。
* **イメージのファミリを取得する**: `Get-AzureVMImage | select ImageFamily` は、**ImageFamily** プロパティの文字列を表示して、イメージのファミリの一覧を取得します。
* **特定ファミリ内のすべてのイメージを取得する**: `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **VM イメージの検索**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` は、VM イメージにのみ適用される DataDiskConfiguration プロパティをフィルター処理します。 この例では、ラベルとイメージの名前のみの出力もフィルタリングします。
* **一般化されたイメージを保存する**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **専用のイメージを保存する**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
  >[Azure.Tip] オペレーティング システム ディスクだけではなく、データ ディスクも含む VM イメージを作成する場合は、OSState パラメーターが必要です。 パラメーターを使用しない場合、コマンドレットは、OS イメージを作成します。 パラメーターの値は、オペレーティング システム ディスクを再利用できるように準備されているかどうかによって、イメージを一般化するか、または専用化するかを示しています。
* **イメージを削除する**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`

## <a name="next-steps"></a>次のステップ
[クラシック ポータルを使用して Windows マシンを作成する](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)こともできます。




<!--HONumber=Nov16_HO3-->


