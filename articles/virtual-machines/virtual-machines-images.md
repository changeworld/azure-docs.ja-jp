<properties
	pageTitle="仮想マシンのイメージについて | Microsoft Azure"
	description="Azure で仮想マシンのイメージを使用する方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/15/2016"
	ms.author="cynthn"/>

# 仮想マシンのイメージについて

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。

イメージは、Azure でオペレーティング システムを持つ新しい仮想マシンを提供するために使用します。また、イメージには 1 つ以上のデータ ディスクも保持できます。イメージは複数のソースから利用できます。

-	Azure は、[Marketplace](http://azure.microsoft.com/gallery/virtual-machines/) でイメージを提供しています。最近のバージョンの Windows Server や Linux オペレーティング システムのディストリビューションがあります。また、いくつかのイメージには、SQL Server などのアプリが含まれます。MSDN 特典や MSDN の従量課金制サブスクライバーでは、その他のイメージにアクセスできます。
-	オープン ソース コミュニティは、[VM Depot](http://vmdepot.msopentech.com/List/Index) でイメージを提供しています。
-	また、既存の Azure 仮想マシンをキャプチャしてイメージとして使用するか、イメージをアップロードすることで、Azure 内で独自のイメージを使用することもできます。

## VM イメージと OS イメージについて

Azure では、VM イメージ と OS イメージの 2 つの種類のイメージを使用できます。VM イメージには、オペレーティング システムと、イメージの作成時に、仮想マシンにアタッチされているすべてのディスクが含まれています。これは新しいタイプのイメージです。VM イメージが導入される以前は、Azure 内のイメージが保持できるのは汎用オペレーティング システムのみで、追加のディスクは保持できませんでした。汎用オペレーティング システムのみを含む VM イメージは、基本的に元のタイプのイメージと同じで、これを OS イメージといいます。

Azure 内の仮想マシンや、他の場所で稼働している仮想マシンを元にして、コピーおよびアップロードして独自のイメージを作成できます。イメージを使用して、1 つ以上の仮想マシンを作成する場合は、準備としてイメージを一般化する必要があります。Windows Server イメージを作成するには、.vhd ファイルをアップロードする前に、サーバー上で Sysprep コマンドを実行して一般化を行います。Sysprep の詳細については、「[Sysprep の使用方法: 紹介](http://go.microsoft.com/fwlink/p/?LinkId=392030)」を参照してください。Linux イメージを作成するには、Azure Linux エージェントを実行する他に、ソフトウェアのディストリビューションにより、固有のコマンドを実行する必要があります。

## イメージの操作

Azure サブスクリプションに使用できるイメージの管理には、Mac、Linux、Windows、Azure PowerShell モジュール用の Azure コマンド ライン インターフェイス (CLI) を使用することができます。いくつかのイメージ タスクには Azure クラシック ポータルを使用することもできますが、コマンドラインの方が多くのオプションを使用できます。

リソース マネージャーのデプロイでのこれらのツールの使用方法の詳細については、「[PowerShell と Azure CLI による Azure Virtual Machine イメージのナビゲーションと選択](resource-groups-vm-searching.md)」をご覧ください。

次にクラシック デプロイでのツールの使用方法の例を示します。

- CLI については、「[Azure サービス管理での Mac、Linux、Windows 用 Azure CLI の使用](virtual-machines-command-line-tools.md)」の「Azure の仮想マシン イメージの管理用コマンド」をご覧ください。
- Azure PowerShell については、次のコマンドの一覧をご覧ください。イメージを見つけて仮想マシンを作成する例は、「[Azure PowerShell を使用して Windows ベースの Virtual Machines を作成し事前構成する](virtual-machines-ps-create-preconfigure-windows-vms.md)」の「手順 3. ImageFamily を特定する」をご覧ください。

-	**すべてのイメージを取得する**: `Get-AzureVMImage` は、現在のサブスクリプションで使用できるすべてのイメージの一覧を返します。Azure またはパートナーによって提供されるイメージも含みます。つまり、取得する一覧はかなり大きくなる可能性があります。次の例では、取得する一覧を小さくする方法を示します。
-	**イメージのファミリを取得する**: `Get-AzureVMImage | select ImageFamily` は、**ImageFamily** プロパティの文字列を表示して、イメージのファミリの一覧を取得します。
-	**特定ファミリ内のすべてのイメージを取得する**: `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-	**VM イメージの検索**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` は、VM イメージにのみ適用される DataDiskConfiguration プロパティをフィルタリングします。この例では、ラベルとイメージの名前のみの出力もフィルタリングします。
-	**一般化されたイメージを保存する**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-	**[専用のイメージを保存する]**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip]オペレーティング システム ディスクだけではなく、データ ディスクも含む VM イメージを作成する場合は、OSState パラメーターが必要です。パラメーターを使用しない場合、コマンドレットは、OS イメージを作成します。パラメーターの値は、オペレーティング システム ディスクを再利用できるように準備されているかどうかによって、イメージを一般化するか、または専用化するかを示しています。
-	**イメージの削除**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`


## その他のリソース

[Linux 仮想マシンを作成するさまざまな方法](virtual-machines-linux-choices-create-vm.md)

[Windows 仮想マシンを作成するさまざまな方法](virtual-machines-windows-choices-create-vm.md)

<!---HONumber=AcomDC_0121_2016-->