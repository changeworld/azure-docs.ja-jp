<properties
	pageTitle="Azure 上での Ubuntu Linux VHD の作成とアップロード"
	description="Ubuntu Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="szark"/>

# Azure 用の Ubuntu 仮想マシンの準備

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 公式の Ubuntu クラウド イメージ
Ubuntu は、現在、公式の Azure VHD を公開しており、[http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/) でダウンロードできます。Azure 用に特殊な Ubuntu イメージを独自に構築する必要がある場合は、以下の手動の手順を使用するのではなく、このように動作している既知の VHD を基にして、必要に応じてカスタマイズすることをお勧めします。最新のイメージ リリースは、常に次の場所にあります。

 - Ubuntu 12.04/Precise: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/precise/release/ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)


## 前提条件

この記事では、既に Ubuntu Linux オペレーティング システムを仮想ハード ディスクにインストールしていることを前提にしています。.vhd ファイルを作成するツールは、Hyper-V のような仮想化ソリューションなど複数あります。詳細については、「[Hyper-V の役割のインストールと仮想マシンの構成](http://technet.microsoft.com/library/hh846766.aspx)」を参照してください。

**Ubuntu のインストールに関する注記**

- Azure で Linux を準備する際のその他のヒントについては、「[Linux のインストールに関する一般的な注記](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)」も参照してください。
- VHDX 形式は Azure ではサポートされていません。サポートされるのは **固定 VHD** のみです。Hyper-V マネージャーまたは convert-vhd コマンドレットを使用して、ディスクを VHD 形式に変換できます。
- Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。必要な場合は、[LVM](virtual-machines-linux-configure-lvm.md) または [RAID](virtual-machines-linux-configure-raid.md) をデータ ディスク上で使用できます。
- OS ディスクにスワップ パーティションを構成しないでください。Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。このことに関する詳細については、次の手順を参照してください。
- すべての VHD のサイズは 1 MB の倍数であることが必要です。


## 手動の手順

> [AZURE.NOTE] Azure 用のカスタム Ubuntu イメージを独自に作成する前に、[http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/) にあるイメージを使用することを検討してください。


1. Hyper-V マネージャーの中央のウィンドウで仮想マシンを選択します。

2. **[接続]** をクリックすると、仮想マシンのウィンドウが開きます。

3.	イメージ内の現在のリポジトリを置き換えて、Ubuntu の Azure リポジトリを使用します。この手順は、Ubuntu のバージョンによって多少異なります。

	/etc/apt/sources.list を編集する前に、バックアップを作成することをお勧めします。

		# sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

	Ubuntu 12.04:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-get update

	Ubuntu 14.04:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-get update

4. Ubuntu Azure イメージは、*HardWare Enablement* (HWE) カーネルに従うようになりました。次のコマンドを実行してオペレーティング システムを最新のカーネルに更新します。

	Ubuntu 12.04:

		# sudo apt-get update
		# sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
		# sudo apt-get install hv-kvp-daemon-init
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot

	Ubuntu 14.04:

		# sudo apt-get update
		# sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
		# sudo apt-get install hv-kvp-daemon-init
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot


5. Grub のカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。これを行うには、テキスト エディターで "/etc/default/grub" を開き、`GRUB_CMDLINE_LINUX_DEFAULT` という変数を探して (または、必要であれば追加して)、次のパラメーターが含まれるように編集します。

		GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

	このファイルを保存して閉じてから、"`sudo update-grub`" を実行します。これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。

6.	SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。通常これが既定です。

7.	Azure Linux エージェントをインストールします。

		# sudo apt-get update
		# sudo apt-get install walinuxagent

	`NetworkManager` パッケージおよび `NetworkManager-gnome` パッケージがインストールされている場合、`walinuxagent` パッケージをインストールするとこれらのパッケージが削除されることに注意してください。

8.	次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

9. Hyper-V マネージャーで **[アクション] -> [シャットダウン]** をクリックします。これで、Linux VHD を Azure にアップロードする準備が整いました。


## 次のステップ
これで、Ubuntu Linux 仮想ハード ディスク を使用して、Azure に新しい仮想マシンを作成する準備が整いました。.vhd ファイルを Azure に初めてアップロードする場合は、「[Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](virtual-machines-linux-classic-create-upload-vhd.md)」の手順 2 と 3 をご覧ください。

## 参照 ##

Ubuntu HardWare Enablement (HWE) カーネル:

- [http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
- [http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)

<!---HONumber=AcomDC_0831_2016-->