<properties
	pageTitle="Debian Linux VHD の準備 | Microsoft Azure"
	description="Azure へのデプロイ用に Debian 7 および 8 の VHD ファイルを作成する方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="mingzhan"/>




#Azure 用の Debian VHD の準備

##前提条件
このセクションでは、[Debian Web サイト](https://www.debian.org/distrib/)から仮想ハード ディスクにダウンロードした .iso ファイルから Debian Linux オペレーティング システムを既にインストールしているものと想定しています。.vhd ファイルを作成するためのツールは複数あり、Hyper-V は 1 つの例にすぎません。Hyper-V の使い方の詳細については、[Hyper-V の役割のインストールと仮想マシンの構成](https://technet.microsoft.com/library/hh846766.aspx)に関するページを参照してください。


## インストールに関する注記

- 新しい VHDX 形式は、Azure ではサポートされていません。Hyper-V マネージャーまたは **convert-vhd** コマンドレットを使用して、ディスクを VHD 形式に変換できます。
- Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。必要な場合は、LVM または RAID をデータ ディスク上で使用できます。
- OS ディスクにスワップ パーティションを構成しないでください。Azure Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。このことに関する詳細については、次の手順を参照してください。
- すべての VHD のサイズは 1 MB の倍数であることが必要です。


##Debian 7.x および 8.x

1. Hyper-V マネージャーで仮想マシンを選択します。

2. **[接続]** をクリックすると、仮想マシンのコンソール ウィンドウが開きます。

3. ISO ファイルに対して VM を設定する場合は、`/etc/apt/source.list` の **deb cdrom** の行をコメント アウトします。

4. `/etc/default/grub` ファイルを編集し、**GRUB\_CMDLINE\_LINUX** パラメーターを次のように変更して、Azure の追加のカーネル パラメーターを含めます。

        GRUB_CMDLINE_LINUX="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

5. GRUB をリビルドして、次のスクリプトを実行します。

        # sudo update-grub 

6. Azure Linux エージェントの依存関係パッケージをインストールします。

        # apt-get install -y git parted

7.	[ガイダンス](virtual-machines-linux-update-agent.md)に従って Azure Linux エージェントを Github からインストールし、バージョン 2.0.14 を選択します。

			# wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
			# chmod +x waagent
			# cp waagent /usr/sbin
			# /usr/sbin/waagent -install -verbose

8. 仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備して、次のスクリプトを実行します。

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout
 
9. Hyper-V マネージャーで **[アクション]**、[シャットダウン] の順にクリックします。これで、Linux VHD を Azure にアップロードする準備が整いました。

##Credativ スクリプトを使用して Debian VHD を作成する

Credativ の Web サイトには、Debian VHD を自動的にビルドするのに役立つスクリプトが用意されています。このスクリプトを[ここ](https://gitlab.credativ.com/de/azure-manage)からダウンロードして、Linux VM にインストールすることができます。Debian VHD (たとえば、Debian 7) を作成するには、次のスクリプトを実行します。

        # azure_build_image --option release=wheezy --option image_prefix=lilidebian7 --option image_size_gb=30 SECTION

このスクリプトを使用したときに問題が発生した場合は、[ここ](https://gitlab.credativ.com/groups/de/issues)から Credativ に問題を報告してください。

## 次のステップ

これで、Debian .vhd を使用して新しい Azure 仮想マシンを作成する準備が整いました。Azure を使用し、.vhd ファイルを Azure にアップロードするのは今回が初めての場合は、[このガイダンス](virtual-machines-linux-create-upload-vhd.md)の手順 2 と 3 に従ってください。

<!---HONumber=AcomDC_0121_2016-->