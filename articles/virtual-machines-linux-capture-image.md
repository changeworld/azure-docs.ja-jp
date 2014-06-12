<properties linkid="manage-linux-howto-capture-an-image" urlDisplayName="イメージのキャプチャ" pageTitle="Linux を実行する仮想マシンのイメージのキャプチャ" metaKeywords="Azure Linux vm, Linux vm" description="Linux を実行する Azure の仮想マシン (VM) のイメージをキャプチャする方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Linux を実行する仮想マシンのイメージをキャプチャする方法" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />





# Linux を実行する仮想マシンのイメージをキャプチャする方法##

イメージ ギャラリーにあるイメージを使って簡単に仮想マシンを作成できます。または、独自のイメージをキャプチャしてそれを使ってカスタム仮想マシンを作成することもできます。イメージとは、仮想マシンの作成時にテンプレートとして使用される仮想ハード ディスク (.vhd) ファイルです。イメージがテンプレートなのは、構成済みの仮想マシンとは違って、ホスト名やユーザー アカウントの設定のような具体的な設定がなされていないためです。同じ形態でセットアップされた仮想マシンを複数作成する場合は、構成済みの仮想マシンのイメージをキャプチャして、そのイメージをテンプレートとして使用することができます。

1. 「[Linux を実行する仮想マシンにログオンする方法][]」に示された手順に従って仮想マシンに接続します。

2. SSH のウィンドウで、次のコマンドを入力し、仮想マシンで作成したアカウントのパスワードを入力します。`waagent` からの出力はこのユーティリティのバージョンによって多少異なる場合があることに注意してください。

	`sudo waagent -deprovision`

	![仮想マシンのプロビジョニング解除](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)


3. 「**y**」と入力して続行します。

	![仮想マシンのプロビジョニング解除の成功](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

4. 「**Exit**」と入力して、SSH クライアントを閉じます。

5. [管理ポータル](http://manage.windowsazure.com)で、仮想マシンを選択し、[**シャットダウン**] をクリックします。

	![仮想マシンのシャットダウン](./media/virtual-machines-linux-capture-image/ShutdownVM.png)

6. **[はい]** をクリックして、仮想マシンが実行されていないときも課金され続けることを承認します。

7. 仮想マシンが停止したら、コマンド バーで **[取り込み]** をクリックします。

	![仮想マシンのイメージのキャプチャ](./media/virtual-machines-linux-capture-image/CaptureVM.png)

	**[仮想マシンからイメージをキャプチャします]** ダイアログ ボックスが表示されます。
	
	![キャプチャの詳細の入力](./media/virtual-machines-linux-capture-image/CaptureLinux.png)

8.	**[イメージの名前]** に新しいイメージの名前を入力します。

9.	すべての Linux イメージを*プロビジョニング解除*する必要があります。そのためには `-deprovision` オプションを指定して `waagent` コマンドを実行します。**[仮想マシンでプロビジョニング解除コマンドを実行しました]** をクリックして、オペレーティング システムがイメージ用に準備できたことを通知します。

10.	チェック マークをクリックしてイメージをキャプチャします。

	これで **[イメージ]** で新しいイメージが使用可能になりました。イメージのキャプチャ後に仮想マシンは削除されます。

	![イメージのキャプチャの成功](./media/virtual-machines-linux-capture-image/CaptureSuccess.png)

	**[ギャラリーから]** を使用して仮想マシンを作成すると、**[仮想マシンのオペレーティング システムの選択]** ページで **[マイ イメージ]** をクリックすることで、キャプチャしたイメージを使用できます。
	
[Linux を実行する仮想マシンにログオンする方法]: ../virtual-machines-linux-how-to-log-on


