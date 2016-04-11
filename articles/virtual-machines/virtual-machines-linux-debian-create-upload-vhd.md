<properties
	pageTitle="Debian Linux VHD の準備 | Microsoft Azure"
	description="Azure へのデプロイ用に Debian 7 および 8 の VHD ファイルを作成する方法について説明します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/25/2016"
	ms.author="szark"/>




# Azure 用の Debian VHD の準備

## 前提条件
このセクションでは、[Debian Web サイト](https://www.debian.org/distrib/)から仮想ハード ディスクにダウンロードした .iso ファイルから Debian Linux オペレーティング システムを既にインストールしているものと想定しています。.vhd ファイルを作成するためのツールは複数あり、Hyper-V は 1 つの例にすぎません。Hyper-V の使い方の詳細については、[Hyper-V の役割のインストールと仮想マシンの構成](https://technet.microsoft.com/library/hh846766.aspx)に関するページを参照してください。


## インストールに関する注記

- 新しい VHDX 形式は、Azure ではサポートされていません。Hyper-V マネージャーまたは **convert-vhd** コマンドレットを使用して、ディスクを VHD 形式に変換できます。
- Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。必要な場合は、LVM または RAID をデータ ディスク上で使用できます。
- OS ディスクにスワップ パーティションを構成しないでください。Azure Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。このことに関する詳細については、次の手順を参照してください。
- すべての VHD のサイズは 1 MB の倍数であることが必要です。


## Azure-Manage を使用した Debian VHD の作成

[credativ](http://www.credativ.com/) の [azure-manage](https://gitlab.credativ.com/de/azure-manage) スクリプトなど、Azure 用に Debian VHD を生成するために使用できるツールがあります。これは、最初からイメージを作成するよりもお勧めの方法です。たとえば、Debian 8 VHD を作成するには、次のコマンドを実行して azure-manage (と依存関係) をダウンロードし、azure\_build\_image スクリプトを実行します。

	# sudo apt-get update
	# sudo apt-get install git qemu-utils mbr kpartx debootstrap

	# sudo apt-get install python3-pip
	# sudo pip3 install azure-storage azure-servicemanagement-legacy pytest pyyaml
	# git clone https://gitlab.credativ.com/de/azure-manage.git
	# cd azure-manage
	# sudo pip3 install .

	# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## 手動での Debian VHD の準備

1. Hyper-V マネージャーで仮想マシンを選択します。

2. **[接続]** をクリックすると、仮想マシンのコンソール ウィンドウが開きます。

3. ISO ファイルに対して VM を設定する場合は、`/etc/apt/source.list` の **deb cdrom** の行をコメント アウトします。

4. `/etc/default/grub` ファイルを編集し、**GRUB\_CMDLINE\_LINUX** パラメーターを次のように変更して、Azure の追加のカーネル パラメーターを含めます。

        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"

5. GRUB をリビルドして、次のスクリプトを実行します。

        # sudo update-grub

6. Debian 6 または 7 については、/Etc/apt/sources.list に Debian の Azure リポジトリを追加します。

	**Debian 6.x "Wheezy"**

		deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
		deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
		deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
		deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main


	**Debian 7.x "Jessie"**

		deb http://debian-archive.trafficmanager.net/debian jessie-backports main
		deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
		deb http://debian-archive.trafficmanager.net/debian-azure jessie main
		deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


7. Azure Linux エージェントをインストールします。

		# sudo apt-get update
		# sudo apt-get install waagent

8. Debian 7 場合、wheezy backports リポジトリから 3.16 ベース カーネルを実行する必要があります。まず、次の内容を含む /etc/apt/preferences.d/linux.pref という名前のファイルを作成します。

		Package: linux-image-amd64 initramfs-tools
		Pin: release n=wheezy-backports
		Pin-Priority: 500

	次に、"sudo apt-get install linux-image-amd64" を実行して、新しいカーネルをインストールします。

8. 仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備して、次のスクリプトを実行します。

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

9. Hyper-V マネージャーで **[アクション]**、[シャットダウン] の順にクリックします。これで、Linux VHD を Azure にアップロードする準備が整いました。


## 次のステップ

これで、Debian 仮想ハード ディスク を使用して、Azure に新しい仮想マシンを作成する準備が整いました。.vhd ファイルを Azure に初めてアップロードする場合は、「[Creating and uploading a virtual hard disk that contains the Linux operating system (Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード)](virtual-machines-linux-classic-create-upload-vhd.md)」の手順 2 と 3 をご覧ください。

<!---HONumber=AcomDC_0330_2016------>