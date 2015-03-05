<properties 
	pageTitle="Windows Server を実行する仮想マシンのイメージのキャプチャ" 
	description="Windows Server 2008 R2 を実行する Azure 仮想マシン (VM) のイメージをキャプチャする方法について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="kathydav"/>

#テンプレートとして使用する Windows 仮想マシンのキャプチャ方法#

ここでは、Windows を実行する Azure 仮想マシンをキャプチャして、他の仮想マシンを作成する際のテンプレートのように使用する方法を示します。このテンプレートには、仮想マシンに接続された OS ディスクやデータ ディスクが含まれます。ネットワーク構成は含まれないため、テンプレートを使用する他の仮想マシンを作成するときは、ネットワーク構成を行う必要があります。

Azure ではこのテンプレートをイメージとして扱い、**[マイ イメージ]** に格納します。アップロードしたすべてのイメージがこの場所に格納されます。イメージの詳細については、「[Azure のバーチャル マシン イメージについて]」を参照してください[]。

##開始する前に##

これらの手順は、すでに Azure 仮想マシンを作成し、データ ディスクの接続を含め、オペレーティング システムの構成が完了していることを前提としています。まだ完了していない場合は、下記の手順を確認してください。

- [カスタム仮想マシンの作成方法] []
- [データ ディスクを仮想マシンに接続する方法] []

##仮想マシンをキャプチャする##

1. コマンド バーで **[接続]** をクリックして、仮想マシンに接続します。詳細については、「[Windows Server が実行されている仮想マシンにログオンする方法]」を参照してください[]。

2.	管理者としてコマンド プロンプト ウィンドウを開きます。


3.	ディレクトリを `%windir%\system32\sysprep` に変更し、sysprep.exe を実行します。


4. 	**[システム準備ツール]** ダイアログ ボックスが表示されます。以下の手順を実行します。


	- **[システム クリーンアップ アクション]** で **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。Sysprep の使い方の詳細については、[Sysprep の使用方法: 紹介] を参照してください。

	- **[シャットダウン オプション]** の **[シャットダウン]** を選択します。

	- **[OK]** をクリックします。

	![Run Sysprep](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	Sysprep コマンドにより仮想マシンがシャットダウンされます。これにより、[管理ポータル](http://manage.windowsazure.com)の仮想マシンの状態が **[停止済み]** に変わります。


8.	**[仮想マシン]** をクリックし、キャプチャする仮想マシンを選択します。

9.	コマンド バーで、**[取り込み]** をクリックします。

	![Capture virtual machine](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	**[仮想マシンからイメージをキャプチャします]** ダイアログ ボックスが表示されます。

10.	**[イメージの名前]** に新しいイメージの名前を入力します。

11.	一連のカスタム イメージを Windows Server のイメージを追加する前に、前の手順で指示されたとおりに Sysprep を実行して一般化する必要があります。**[仮想マシンで Sysprep を実行しました]** をクリックして、この一般化が完了したことを通知します。

12.	チェック マークをクリックしてイメージをキャプチャします。 

  **注:一般化した仮想マシンのイメージをキャプチャすると、その仮想マシンは削除されます。**

 これで **[イメージ]** で新しいイメージが使用可能になりました。
 ![Image capture successful](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

##次のステップ##
イメージの準備ができましたので、これをテンプレートとして使用して仮想マシンを作成します。そのためには、**[ギャラリーから]** の方法を使用して、先ほど作成したイメージを選択することで、カスタム仮想マシンを作成します。手順については、「[カスタム仮想マシンの作成方法][]」を参照してください。

	
[Azure のバーチャル マシン イメージについて]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[カスタム仮想マシンの作成方法]: ../virtual-machines-create-custom/
[データ ディスクを仮想マシンに接続する方法]: ../storage-windows-attach-disk/
[Windows Server が実行されている仮想マシンにログオンする方法]:http://azure.microsoft.com/manage/windows/how-to-guides/log-on-a-windows-vm/
[Sysprep の使用方法: 紹介]:http://technet.microsoft.com/ library/bb457073.aspx
[Sysprep.exe の実行]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Sysprep.exe オプションの入力]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[仮想マシンの停止]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[仮想マシンのイメージのキャプチャ]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[イメージの名前の入力]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[イメージのキャプチャの成功]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[キャプチャしたイメージの使用]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!--HONumber=45--> 
