<properties linkid="manage-windows-howto-capture-an-image" urlDisplayName="イメージのキャプチャ" pageTitle="Azure で Windows Server を実行する仮想マシンのイメージのキャプチャ" metaKeywords="Azure キャプチャ イメージ VM, VM のキャプチャ" description="Windows Server 2008 R2 を実行する Azure の仮想マシン (VM) のイメージをキャプチャする方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Windows Server が実行されている仮想マシンのイメージをキャプチャする方法" authors=""  solutions="" writer="kathydav" manager="jeffreyg" editor="tysonn"  />




#Windows Server が実行されている仮想マシンのイメージをキャプチャする方法#

イメージ ギャラリーにあるイメージを使って簡単に仮想マシンを作成できます。または、独自のイメージをキャプチャしてそれを使ってカスタム仮想マシンを作成することもできます。イメージとは、仮想マシンの作成時にテンプレートとして使用される仮想ハード ディスク (.vhd) ファイルです。イメージはテンプレートです。イメージには、コンピューター名、ユーザー アカウント設定など、構成された仮想マシンにある特定の設定がないからです。同じ形態でセットアップされた仮想マシンを複数作成する場合は、構成済みの仮想マシンのイメージをキャプチャして、そのイメージをテンプレートとして使用することができます。


1. 「[Windows Server を実行する仮想マシンにログオンする方法][]」に示された手順に従って仮想マシンに接続します。

2.	管理者としてコマンド プロンプト ウィンドウを開きます。


3.	ディレクトリを `%windir%\system32\sysprep` に変更し、sysprep.exe を実行します。


4. 	**[システム準備ツール]** ダイアログ ボックスが表示されます。


	**[システム クリーンアップ アクション]** で **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。Sysprep の使い方の詳細については、「[Sysprep の使用方法: 紹介][]」を参照してください。

5.	**[シャットダウン オプション]** の **[シャットダウン]** を選択します。

6.	**[OK]** をクリックします。

7.	Sysprep コマンドにより仮想マシンがシャットダウンされます。これにより、[管理ポータル](http://manage.windowsazure.com)のマシンの状態が **[停止済み]** に変わります。


8.	**[仮想マシン]** をクリックし、キャプチャする仮想マシンを選択します。

9.	コマンド バーで、**[取り込み]** をクリックします。

	**[仮想マシンからイメージをキャプチャします]** ダイアログ ボックスが表示されます。

10.	**[イメージの名前]** に新しいイメージの名前を入力します。

11.	一連のカスタム イメージを Windows Server のイメージを追加する前に、前の手順で指示されたとおりに Sysprep を実行して一般化する必要があります。**[仮想マシンで Sysprep を実行しました]** をクリックして、この一般化が完了したことを通知します。

12.	チェック マークをクリックしてイメージをキャプチャします。仮想マシンのイメージをキャプチャしたら、そのマシンは削除されます。

	これで **[イメージ]** で新しいイメージが使用可能になりました。

	**[ギャラリーから]** を使用して仮想マシンを作成すると、**[イメージの選択]** ページで **[マイ イメージ]** をクリックすることで、キャプチャしたイメージを使用できます。

	

[Windows Server を実行する仮想マシンにログオンする方法]:http://www.windowsazure.com/ja-jp/manage/windows/how-to-guides/log-on-a-windows-vm/
[Sysprep の使用方法: 紹介]:http://technet.microsoft.com/ja-jp/library/bb457073.aspx
[Sysprep.exe の実行]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Sysprep.exe オプションの入力]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[仮想マシンの停止]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[仮想マシンのイメージのキャプチャ]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[イメージの名前の入力]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[イメージのキャプチャの成功]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[キャプチャしたイメージの使用]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

