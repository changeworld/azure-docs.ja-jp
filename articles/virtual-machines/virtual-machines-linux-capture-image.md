<properties
	pageTitle="Linux を実行する仮想マシンのイメージのキャプチャ"
	description="Linux を実行する Azure の仮想マシン (VM) のイメージをキャプチャする方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/11/2015" 
	ms.author="kathydav"/>


# テンプレートとして使用するために Linux 仮想マシンをキャプチャする方法##

ここでは、Linux を実行する Azure 仮想マシンをキャプチャして、他の仮想マシンを作成する際のテンプレートのように使用する方法を示します。このテンプレートには、仮想マシンに接続された OS ディスクやデータ ディスクが含まれます。ネットワーク構成は含まれないため、テンプレートを使用する他の仮想マシンを作成するときは、ネットワーク構成を行う必要があります。

Azure ではこのテンプレートをイメージとして扱い、**[マイ イメージ]** に格納します。アップロードしたすべてのイメージがこの場所に格納されます。イメージの詳細については、「[Azure の仮想マシン イメージについて][]」を参照してください。

##開始する前に##

これらの手順は、すでに Azure 仮想マシンを作成し、データ ディスクの接続を含め、オペレーティング システムの構成が完了していることを前提としています。まだ完了していない場合は、下記の手順を確認してください。

- [カスタム仮想マシンの作成方法][]
- [データ ディスクを仮想マシンに接続する方法][]

##仮想マシンをキャプチャする##

1. コマンド バーで **[接続]** をクリックして、仮想マシンに接続します。詳細については、「[Linux を実行する仮想マシンにログオンする方法][]」を参照してください。

2. SSH のウィンドウで、次のコマンドを入力します。`waagent` からの出力はこのユーティリティのバージョンによって多少異なる場合があることに注意してください。

	`$ sudo waagent -deprovision+user`

	このコマンドは、システムをクリーンアップし、再プロビジョニングに適した状態にします。この操作では次のタスクが実行されます。

	- すべての SSH ホスト キーの削除 (構成ファイルで Provisioning.RegenerateSshHostKeyPair が 'y' の場合)
	- /etc/resolv.conf 内のネームサーバー構成の消去
	- /etc/shadow の `root` ユーザーのパスワードの削除 (構成ファイルで Provisioning.DeleteRootPassword が 'y' の場合)
	- キャッシュされた DHCP クライアントのリースの削除
	- ホスト名を localhost.localdomain にリセット
	- (/var/lib/waagent から取得した) 前回プロビジョニングされたユーザー アカウント**および関連データ**の削除

	![仮想マシンのプロビジョニング解除](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)

	>[AZURE.NOTE]プロビジョニング解除では、イメージを "一般化" する目的でファイルとデータが削除されます。このコマンドは、必ず新しいイメージ テンプレートとしてキャプチャする仮想マシン上で実行してください。


3. 「**y**」と入力して続行します。

	![仮想マシンのプロビジョニング解除の成功](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

	**注:** `-force` パラメーターを追加すると、この確認手順を省略できます。

	>[AZURE.NOTE]プロビジョニング解除は単に Linux イメージを一般化する操作です。イメージからすべての機密情報が削除され、イメージが第三者への再配布に適した状態になることが保証されるわけではありません。


4. 「**Exit**」と入力して、SSH クライアントを閉じます。

5. [管理ポータル](http://manage.windowsazure.com)で、仮想マシンを選択し、**[シャットダウン]** をクリックします。

6. 仮想マシンが停止したら、コマンド バーで **[取り込み]** をクリックし、**[仮想マシンのキャプチャ]** ダイアログ ボックスを開きます。

7.	**[イメージの名前]** に新しいイメージの名前を入力します。

8.	すべての Linux イメージを*プロビジョニング解除*する必要があります。そのためには `-deprovision` オプションを指定して `waagent` コマンドを実行します。**[仮想マシンで waagent プロビジョニング解除を実行しました]** をクリックして、オペレーティング システムがイメージ用に準備できたことを通知します。

9.	チェック マークをクリックしてイメージをキャプチャします。

	これで **[イメージ]** で新しいイメージが使用可能になりました。イメージのキャプチャ後に仮想マシンは削除されます。

	![イメージのキャプチャの成功](./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png)


##次のステップ##
イメージの準備ができましたので、これをテンプレートとして使用して仮想マシンを作成します。そのためには、**[ギャラリーから]** の方法を使用して、先ほど作成したイメージを選択することで、カスタム仮想マシンを作成します。手順については、「[カスタム仮想マシンの作成方法][]」を参照してください。

**関連項目:** [Azure Linux エージェント ユーザー ガイド](virtual-machines-linux-agent-user-guide.md)

[Linux を実行する仮想マシンにログオンする方法]: virtual-machines-linux-how-to-log-on.md
[Azure の仮想マシン イメージについて]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[カスタム仮想マシンの作成方法]: virtual-machines-create-custom.md
[データ ディスクを仮想マシンに接続する方法]: storage-windows-attach-disk.md

 

<!---HONumber=58_postMigration-->