<properties urlDisplayName="Upload a Linux VHD" pageTitle="Azure 上での Linux VHD の作成とアップロード" metaKeywords="Azure VHD, uploading Linux VHD" description="Learn to create and upload an Azure virtual hard disk (VHD) that contains a Linux operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains a Linux Operating System" authors="szarkos" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="06/05/2014" ms.author="szarkos" />


# <a id="nonendorsed"> </a>動作保証外のディストリビューションに関する情報 #

**重要**:Azure プラットフォームの SLA は、[動作保証済み Linux ディストリビューション](../virtual-machines-linux-endorsed-distributions)のいずれか 1 つを使用した場合にのみ、Linux OS を実行する仮想マシンに適用されます。Azure イメージ ギャラリーにあるすべての Linux ディストリビューションは、必須の構成による動作保証済みディストリビューションです。

- [Azure での動作保証済み Linux ディストリビューション](../virtual-machines-linux-endorsed-distributions)
- [マイクロソフト Azure での Linux のイメージのサポート](http://support2.microsoft.com/kb/2941892)

Azure 上で動作するすべてのディストリビューションは、プラットフォーム上で適切に実行できるように、いくつかの前提条件を満たす必要があります。この記事は決して包括的なものではありません。前提条件はディストリビューションによって異なるためです。次に示している条件をすべて満たす場合でも、プラットフォーム上で適切に動作するように Linux システムを微調整する必要があります。

この理由から、できるだけ [Azure での動作保証済み Linux ディストリビューション](../linux-endorsed-distributions) のいずれかで開始することをお勧めします。次の記事では、Azure でサポートされる以下のさまざまな動作保証済み Linux ディストリビューションを準備する方法について説明します。

- **[CentOS ベースのディストリビューション](../virtual-machines-linux-create-upload-vhd-centos)**
- **[Oracle Linux](../virtual-machines-linux-create-upload-vhd-oracle)**
- **[SLES と openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](../virtual-machines-linux-create-upload-vhd-ubuntu)**

以降では、Azure 上で Linux ディストリビューションを実行するための一般的なガイダンスについて、重点的に説明します。


##Linux のインストールに関する一般的な注記##

- 新しい VHDX 形式は、Azure ではサポートされていません。Hyper-V マネージャーまたは convert-vhd コマンドレットを使用して、ディスクを VHD 形式に変換できます。

- Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。必要な場合は、LVM または [RAID ](../virtual-machines-linux-configure-raid) をデータ ディスク上で使用できます。

- さらに大きいサイズの VM では NUMA はサポートされていません。2.6.37 以下のバージョンの Linux カーネルにバグがあるためです。この問題は、主に、アップストリームの Red Hat 2.6.32 カーネルを使用したディストリビューションに影響します。Azure Linux エージェント (waagent) を手動でインストールすると、Linux カーネルの GRUB 構成で NUMA が自動的に無効になります。

- OS ディスクにスワップ パーティションを構成しないでください。Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。このことに関する詳細については、次の手順を参照してください。

- すべての VHD のサイズは 1 MB の倍数であることが必要です。


## Linux カーネルの要件 ##

Hyper-V および Azure 用の Linux Integration Services (LIS) ドライバーは、アップストリームの Linux カーネルに直接提供されています。最新の Linux カーネル バージョン (つまり 3.x) を含む多くのディストリビューションでこれらのドライバーが含まれています。含まれていない場合は、これらのドライバーのバックポートされたバージョンがカーネルと共に提供されます。これらのドライバーは、アップストリームのカーネル内で新しい修正と機能を含んだ形で常に更新されているため、可能な場合は、これらの修正と更新を含む[動作保証済みディストリビューション](../linux-endorsed-distributions) を実行することをお勧めします。

Red Hat Enterprise Linux Version **6.0-6.3** の変形を実行する場合は、Hyper-V 用の最新の LIS ドライバーをインストールする必要があります。ドライバーは[この場所](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)にあります。RHEL **6.4 以上** (および派生版) では、LIS ドライバーは既にカーネルに含まれているため、Azure 上でこれらのシステムを実行するために追加のインストール パッケージは必要ありません。

カスタム カーネルが必要な場合は、最新のカーネル バージョン (つまり **3.8 以上**) の使用を強くお勧めします。独自のカーネルを維持するディストリビューションまたはベンダーの場合は、アップストリームのカーネルの LIS ドライバーをカスタム カーネルにバックポートすることをお勧めします。比較的最新のカーネル バージョンを既に実行している場合でも、一般的には LIS ドライバーのアップストリームの修正を常に追跡し、必要に応じてパックポートすることをお勧めします。LIS ドライバーのソース ファイルの場所は、次の Linux カーネルのソース ツリーの [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS)  ファイルに記載されています。

	F:	arch/x86/include/asm/mshyperv.h
	F:	arch/x86/include/uapi/asm/hyperv.h
	F:	arch/x86/kernel/cpu/mshyperv.c
	F:	drivers/hid/hid-hyperv.c
	F:	drivers/hv/
	F:	drivers/input/serio/hyperv-keyboard.c
	F:	drivers/net/hyperv/
	F:	drivers/scsi/storvsc_drv.c
	F:	drivers/video/fbdev/hyperv_fb.c
	F:	include/linux/hyperv.h
	F:	tools/hv/

最低限、以下のパッチが適用されていない場合は、Azure 上で問題が発生することがわかっているため、これらのパッチをカーネルに含める必要があります。この一覧は、すべてのディストリビューションに対して包括的または完全であるとは限りません。

- [ata_piix: defer disks to the Hyper-V drivers by default (既定ではディスクは Hyper-V ドライバーによって決定される)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [storvsc:Account for in-transit packets in the RESET path (RESET パスの転送中のパケットのアカウント)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)


## Azure Linux エージェント ##

[Azure Linux エージェント](../virtual-machines-linux-agent-user-guide)  (waagent) は、Azure で Linux 仮想マシンを適切にプロビジョニングするために必要です。[Linux Agent GitHub リポジトリ](https://github.com/Azure/WALinuxAgent)で、最新バージョンの取得、問題の報告、プル リクエストの提出を行うことができます。

- Linux エージェントは、Apache 2.0 ライセンス下でリリースされています。数多くのディストリビューションでは、このエージェント用の RPM パッケージや deb パッケージを既に提供しているため、場合によっては、ほとんど労力をかけずにこのエージェントをインストールし、更新することができます。

- Azure Linux エージェントには、Python v2.6 以上が必要です。

- このエージェントでは、python-pyasn1 モジュールも必要です。ほとんどのディストリビューションでは、インストール可能な個別のパッケージとして提供しています。

- Azure Linux エージェントは NetworkManager と互換性がない場合があります。ディストリビューションによって提供される多くの RPM/Deb パッケージでは、NetworkManager が waagent パッケージに対する競合として構成されるため、Linux エージェント パッケージをインストールすると NetworkManager がアンインストールされます。


## Linux システムの一般的な要件 ##

- GRUB または GRUB2 でカーネルのブート行を変更して次のパラメーターを含めます。これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。

	そのほかにも、次のパラメーターを*削除*することをお勧めします (ある場合)。

		rhgb quiet crashkernel=auto

	クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。

	必要に応じて `crashkernel` オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少するため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

- Azure Linux エージェントをインストールします。

	Azure Linux エージェントは、Azure で Linux イメージをプロビジョニングするために必要です。多くのディストリビューションでは、このエージェントを RPM または Deb パッケージとして提供しています (パッケージは、通常 'WALinuxAgent' または 'walinuxagent' と呼ばれます)。このエージェントは、[Linux エージェント ガイド](../virtual-machines-linux-agent-user-guide)の手順に従って手動でもインストールできます。

- SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。通常これが既定です。

- OS ディスクにスワップ領域を作成しないでください。

	Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。ローカル リソース ディスクは*一時*ディスクであるため、仮想マシンのプロビジョニングが解除されると空になることに注意してください。Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

- /etc/sudoers で、次の行を削除するかコメント アウトする必要があります (ある場合)。

		Defaults targetpw
		ALL    ALL=(ALL) ALL

- 最後の手順として、次のコマンドを実行して仮想マシンをプロビジョニング解除します。

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

- その後、仮想マシンをシャットダウンし、Azure に VHD をアップロードする必要があります。



<!--HONumber=35.1-->
