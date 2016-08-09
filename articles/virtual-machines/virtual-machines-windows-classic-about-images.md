<properties
	pageTitle="Windows 仮想マシンのイメージについて | Microsoft Azure"
	description="Azure で Windows 仮想マシンのイメージを使用する方法について説明します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="cynthn"/>

# Windows 仮想マシンのイメージについて

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]



## イメージの操作

Azure サブスクリプションに使用できるイメージの管理には、Azure PowerShell モジュールを使用することができます。いくつかのイメージ タスクには Azure クラシック ポータルを使用することもできますが、コマンドラインの方が多くのオプションを使用できます。


-	**すべてのイメージを取得する**: `Get-AzureVMImage` は、現在のサブスクリプションで使用できるすべてのイメージの一覧を返します。Azure またはパートナーによって提供されるイメージも含みます。つまり、取得する一覧はかなり大きくなる可能性があります。次の例では、取得する一覧を小さくする方法を示します。
-	**イメージのファミリを取得する**: `Get-AzureVMImage | select ImageFamily` は、**ImageFamily** プロパティの文字列を表示して、イメージのファミリの一覧を取得します。
-	**特定ファミリ内のすべてのイメージを取得する**: `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-	**VM イメージの検索**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` は、VM イメージにのみ適用される DataDiskConfiguration プロパティをフィルタリングします。この例では、ラベルとイメージの名前のみの出力もフィルタリングします。
-	**一般化されたイメージを保存する**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-	**[専用のイメージを保存する]**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip] オペレーティング システム ディスクだけではなく、データ ディスクも含む VM イメージを作成する場合は、OSState パラメーターが必要です。パラメーターを使用しない場合、コマンドレットは、OS イメージを作成します。パラメーターの値は、オペレーティング システム ディスクを再利用できるように準備されているかどうかによって、イメージを一般化するか、または専用化するかを示しています。
-	**イメージの削除**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`


## 次のステップ

[クラシック ポータルを使用して Windows マシンを作成する](virtual-machines-windows-classic-tutorial.md)こともできます。

<!---HONumber=AcomDC_0727_2016-->