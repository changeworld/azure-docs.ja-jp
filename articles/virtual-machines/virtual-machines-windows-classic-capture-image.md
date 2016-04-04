<properties
	pageTitle="Azure Windows VM のイメージをキャプチャする | Microsoft Azure"
	description="クラシック デプロイ モデルを使用して作成された Azure Windows 仮想マシンのイメージをキャプチャする"
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
	ms.date="03/11/2016"
	ms.author="cynthn"/>

#クラシック デプロイ モデルを使用して作成された Azure Windows 仮想マシンのイメージをキャプチャする

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-windows-capture-image.md).


ここでは、Windows を実行する Azure 仮想マシンをキャプチャして、他の仮想マシンを作成する際にイメージとして使用する方法を示します。このイメージには、オペレーティング システム ディスクと、仮想マシンに接続されているすべてのデータ ディスクが含まれます。ネットワーク構成は含まれないため、イメージを使用する他の仮想マシンを作成するときは、ネットワーク構成を行う必要があります。

Azure では、イメージは **[マイ イメージ]** に格納されます。この場所は、アップロードしたイメージが格納される場所と同じです。イメージの詳細については、「[仮想マシン イメージについて](virtual-machines-linux-classic-about-images.md)」を参照してください。

##開始する前に##

これらの手順は、すでに Azure 仮想マシンを作成し、データ ディスクの接続を含め、オペレーティング システムの構成が完了していることを前提としています。まだ完了していない場合は、下記の手順を確認してください。

- [イメージから仮想マシンを作成](virtual-machines-windows-classic-createportal.md)
- [データ ディスクを仮想マシンに接続する方法](virtual-machines-windows-classic-attach-disk.md)

> [AZURE.WARNING] キャプチャされた後に、このプロセスは元の仮想マシンを削除します。

Azure 仮想マシンのイメージをキャプチャする前に、ターゲット仮想マシンをバックアップしておくことをお勧めします。Azure 仮想マシンは、Azure Backup を使用してバックアップできます。詳細については、「[Back up Azure virtual machines (Azure 仮想マシンのバックアップ)](../backup/backup-azure-vms.md)」を参照してください。その他のソリューションは認定パートナーから利用できます。利用可能なソリューションについては、Azure Marketplace を検索して確認してください。


##仮想マシンをキャプチャする

1. [Azure クラシック ポータル](http://manage.windowsazure.com)で仮想マシンに**接続**します。詳細については、「[Windows Server が実行されている仮想マシンにサインインする方法][]」を参照してください。

2.	管理者としてコマンド プロンプト ウィンドウを開きます。

3.	ディレクトリを `%windir%\system32\sysprep` に変更し、sysprep.exe を実行します。

4. 	**[システム準備ツール]** ダイアログ ボックスが表示されます。以下の手順を実行します。

	- **[システム クリーンアップ アクション]** で **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。Sysprep の使い方の詳細については、「[Sysprep の使用方法: 紹介][]」を参照してください。

	- **[シャットダウン オプション]** の **[シャットダウン]** を選択します。

	- **[OK]** をクリックします。

	![Sysprep を実行する](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)

7.	Sysprep コマンドにより仮想マシンがシャットダウンされます。これにより、Azure クラシック ポータルの仮想マシンの状態が **[停止済み]** に変わります。

8.	Azure クラシック ポータルで **[Virtual Machines]** をクリックし、キャプチャする仮想マシンを選択します。

9.	コマンド バーで、**[取り込み]** をクリックします。

	![仮想マシンをキャプチャする](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)

	**[仮想マシンからイメージをキャプチャします]** ダイアログ ボックスが表示されます。

10.	**[イメージの名前]** に新しいイメージの名前を入力します。

11.	一連のカスタム イメージを Windows Server のイメージを追加する前に、前の手順で指示されたとおりに Sysprep を実行して一般化する必要があります。**[仮想マシンで Sysprep を実行しました]** をクリックして、一般化を実行したことを通知します。

12.	チェック マークをクリックしてイメージをキャプチャします。これで **[イメージ]** で新しいイメージが使用可能になりました。

 	![イメージのキャプチャの成功](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

##次のステップ

イメージを使用して仮想マシンを作成する準備ができました。これを行うには、**[ギャラリーから]** メニュー項目を使用して先ほど作成したイメージを選択し、仮想マシンを作成します。手順については、「[イメージから仮想マシンを作成](virtual-machines-windows-classic-createportal.md)」を参照してください。



[Windows Server が実行されている仮想マシンにサインインする方法]: virtual-machines-windows-classic-connect-logon.md
[Sysprep の使用方法: 紹介]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!---HONumber=AcomDC_0323_2016-->