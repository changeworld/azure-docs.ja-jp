<properties
	pageTitle="Azure 上での Ubuntu Linux VHD の作成とアップロード"
	description="Ubuntu Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/15/2015"
	ms.author="szarkos"/>

# Azure 用の Ubuntu 仮想マシンの準備

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

##前提条件##

この記事では、既に Ubuntu Linux オペレーティング システムを仮想ハード ディスクにインストールしていることを前提にしています。.vhd ファイルを作成するツールは、Hyper-V のような仮想化ソリューションなど複数あります。詳細については、「[Hyper-V の役割のインストールと仮想マシンの構成](http://technet.microsoft.com/library/hh846766.aspx)」を参照してください。

**Ubuntu のインストールに関する注記**

- 新しい VHDX 形式は、Azure ではサポートされていません。Hyper-V マネージャーまたは convert-vhd コマンドレットを使用して、ディスクを VHD 形式に変換できます。

- Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。必要な場合は、LVM または [RAID](virtual-machines-linux-configure-raid.md) をデータ ディスク上で使用できます。

- OS ディスクにスワップ パーティションを構成しないでください。Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。このことに関する詳細については、次の手順を参照してください。

- すべての VHD のサイズは 1 MB の倍数であることが必要です。


## <a id="ubuntu"> </a>Ubuntu 12.04 以上 ##

1. Hyper-V マネージャーの中央のウィンドウで仮想マシンを選択します。

2. **[接続]** をクリックすると、仮想マシンのウィンドウが開きます。

3.	イメージ内の現在のリポジトリを置き換えて、Ubuntu の Azure リポジトリを使用します。この手順は、Ubuntu のバージョンによって多少異なります。

	/etc/apt/sources.list を編集する前に、バックアップを作成することをお勧めします。

		# sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

	Ubuntu 12.04:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
		# sudo apt-get update

	Ubuntu 12.10:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
		# sudo apt-get update

	Ubuntu 14 以上:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-get update

4. 次のコマンドを実行してオペレーティング システムを最新のカーネルに更新します。

	Ubuntu 12.04:

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot

	Ubuntu 12.10:

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot

	Ubuntu 14 以上:

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot

5.	(省略可能) Ubuntu システムにエラーが発生して、Ubuntu システムが再起動すると、多くの場合 Grub のブート プロンプトでユーザーの入力を待機し、適切にシステムが起動しなくなります。この動作を回避するために、次の手順を実行します。

	a) /etc/grub.d/00\_header ファイルを開きます。

	b) **make\_timeout()** 関数内で **if ["\\${recordfail}" = 1 ]; then** を検索します。

	c) この行の下の文を **set timeout=5** に変更します。

	d) 'sudo update-grub' を実行します。

6. Grub のカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。これを行うには、テキスト エディターで "/etc/default/grub" を開き、`GRUB_CMDLINE_LINUX_DEFAULT` という変数を探して (または、必要であれば追加して)、次のパラメーターが含まれるように編集します。

		GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

	このファイルを保存して閉じてから、"`sudo update-grub`" を実行します。これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。

8.	SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。通常これが既定です。

9.	Azure Linux エージェントをインストールします。

		# sudo apt-get update
		# sudo apt-get install walinuxagent

	`NetworkManager` パッケージおよび `NetworkManager-gnome` パッケージがインストールされている場合、`walinuxagent` パッケージをインストールするとこれらのパッケージが削除されることに注意してください。

10.	次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

11. Hyper-V マネージャーで **[アクション] -> [シャットダウン]** をクリックします。これで、Linux VHD を Azure にアップロードする準備が整いました。

<!---HONumber=Sept15_HO4-->