<properties
	pageTitle="Azure で使用するための Red Hat Enterprise Linux VHD の作成とアップロード"
	description="Red Hat Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="mingzhan"/>


# Azure 用の Red Hat ベースの仮想マシンの準備

この記事では、Red Hat Enterprise Linux (RHEL) の仮想マシンを Azure で使用できるように準備する方法について説明します。この記事で取り上げる RHEL のバージョンは 6.7、7.1、および 7.2 で、準備対象のハイパーバイザーは Hyper-V、Kernel-based Virtual Machine (KVM)、および VMware です。Red Hat の Cloud Access プログラムに参加するための資格要件の詳細については、[Red Hat の Cloud Access Web サイト](http://www.redhat.com/en/technologies/cloud-computing/cloud-access)および[ Azure での RHEL の実行に関するページ](https://access.redhat.com/articles/1989673)を参照してください。

[Hyper-V マネージャーからの RHEL 6.7 仮想マシンの準備](#rhel67hyperv)

[Hyper-V マネージャーからの RHEL 7.1/7.2 仮想マシンの準備](#rhel7xhyperv)

[KVM からの RHEL 6.7 仮想マシンの準備](#rhel67kvm)

[KVM からの RHEL 7.1/7.2 仮想マシンの準備](#rhel7xkvm)

[VMWare からの RHEL 6.7 仮想マシンの準備](#rhel67vmware)

[VMWare からの RHEL 7.1/7.2 仮想マシンの準備](#rhel7xvmware)

[kickstart ファイルからの RHEL 7.1/7.2 仮想マシンの準備](#rhel7xkickstart)


## Hyper-V マネージャーからの Red Hat ベースの仮想マシンの準備
### 前提条件
このセクションでは、(Red Hat の Web サイトから取得した ISO ファイルの) RHEL イメージが仮想ハード ディスク (VHD) に既にインストールされていることを前提としています。Hyper-V マネージャーを使用してオペレーティング システム イメージをインストールする方法の詳細については、「[Hyper-V をインストールして仮想マシンを作成する](http://technet.microsoft.com/library/hh846766.aspx)」を参照してください。

**RHEL のインストールに関する注記**

- Azure で Linux を準備する際のその他のヒントについては、「[Linux のインストールに関する一般的な注記](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)」も参照してください。

- 新しい VHDX 形式は、Azure ではサポートされていません。Hyper-V マネージャーまたは **convert-vhd** PowerShell コマンドレットを使用して、ディスクを VHD 形式に変換できます。

- VHD は "固定" として作成する必要があります。動的 VHD はサポートされていません。

- Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。必要な場合は、LVM または [RAID](virtual-machines-linux-configure-raid.md) をデータ ディスク上で使用できます。

- OS ディスクにスワップ パーティションを構成しないでください。一時的なリソース ディスク上にスワップ ファイルを作成するよう Linux エージェントを構成できます。この詳細については、次の手順を参照してください。

- すべての VHD のサイズは 1 MB の倍数であることが必要です。

- **qemu-img** を使用してディスク イメージを VHD 形式に変換する場合、qemu-img のバージョン 2.2.1 以降には既知のバグがあることに注意してください。このバグが原因で、VHD は適切にフォーマットされません。この問題は、qemu-img の今後のリリースで修正される予定です。現時点では、qemu-img のバージョン 2.2.0 以前を使用することをお勧めします。

### <a id="rhel67hyperv"> </a>Hyper-V マネージャーからの RHEL 6.7 仮想マシンの準備###


1.	Hyper-V マネージャーで仮想マシンを選択します。

2.	**[接続]** をクリックすると、仮想マシンのコンソール ウィンドウが開きます。

3.	次のコマンドを実行して NetworkManager をアンインストールします。

        # sudo rpm -e --nodeps NetworkManager

    パッケージがまだインストールされていない場合、このコマンドは失敗してエラー メッセージが表示されます。これは予期されることです。

4.	`/etc/sysconfig/` ディレクトリに **network** という名前でファイルを作成し、次のテキストを追加します。

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	`/etc/sysconfig/network-scripts/` ディレクトリに **ifcfg-eth0** という名前でファイルを作成し、次のテキストを追加します。

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	udev ルールを移動 (または削除) して、イーサネット インターフェイスの静的ルールが生成されないようにします。これらのルールは、Microsoft Azure または Hyper-V で仮想マシンを複製する際に問題の原因となります。

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # sudo chkconfig network on

8.	RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9.	WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。次のコマンドを実行して extras リポジトリを有効にします。

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

10.	GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。これを行うには、テキスト エディターで `/boot/grub/menu.lst` を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。これにより、RHEL 6 で使用されているカーネル バージョンのバグが原因で NUMA が無効になります。

    上記の操作に加えて、次のパラメーターを削除することをお勧めします。

        rhgb quiet crashkernel=auto

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。

    必要に応じて crashkernel オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少します。このため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

11.	SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。通常これが既定です。/etc/ssh/sshd\_config を変更して、次の行を含めます。

        ClientAliveInterval 180

12.	次のコマンドを実行して Azure Linux エージェントをインストールします。

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    手順 2. で説明したように NetworkManager パッケージおよび NetworkManager-gnome パッケージがまだ削除されていない場合、WALinuxAgent パッケージをインストールすると、これらのパッケージが削除されることに注意してください。

13.	OS ディスクにスワップ領域を作成しないでください。Azure Linux エージェントは、Azure で VM がプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成できます。ローカル リソース ディスクは一時ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14.	次のコマンドを実行して、サブスクリプションを登録解除します (必要な場合)。

        # sudo subscription-manager unregister

15.	次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16.	Hyper-V マネージャーで **[アクション] > [シャットダウン]** をクリックします。これで、Linux VHD を Azure にアップロードする準備が整いました。

### <a id="rhel7xhyperv"> </a>Hyper-V マネージャーからの RHEL 7.1/7.2 仮想マシンの準備###

1.  Hyper-V マネージャーで仮想マシンを選択します。

2.	**[接続]** をクリックすると、仮想マシンのコンソール ウィンドウが開きます。

3.	`/etc/sysconfig/` ディレクトリに **network** という名前でファイルを作成し、次のテキストを追加します。

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.	`/etc/sysconfig/network-scripts/` ディレクトリに **ifcfg-eth0** という名前でファイルを作成し、次のテキストを追加します。

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

5.	次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # sudo chkconfig network on

6.	RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。これを行うには、テキスト エディターで `/etc/default/grub` を開き、**GRUB\_CMDLINE\_LINUX** パラメーターを編集します。次に例を示します。

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。上記の操作に加えて、次のパラメーターを削除することをお勧めします。

        rhgb quiet crashkernel=auto

	クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。必要に応じて crashkernel オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少します。このため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

8.	`/etc/default/grub` の編集を終了したら、次のコマンドを実行して GRUB 構成を再構築します。

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.	SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。通常これが既定です。`/etc/ssh/sshd_config` を変更して、次の行を含めます。

        ClientAliveInterval 180

10.	WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。次のコマンドを実行して extras リポジトリを有効にします。

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11.	次のコマンドを実行して Azure Linux エージェントをインストールします。

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

12.	OS ディスクにスワップ領域を作成しないでください。Azure Linux エージェントは、Azure で VM がプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成できます。ローカル リソース ディスクは一時ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。Azure Linux エージェントのインストール後に (前の手順を参照)、`/etc/waagent.conf` にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13.	サブスクリプションを登録解除する場合は、次のコマンドを実行します。

        # sudo subscription-manager unregister

14.	次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15.	Hyper-V マネージャーで **[アクション] > [シャットダウン]** をクリックします。これで、Linux VHD を Azure にアップロードする準備が整いました。


## KVM からの Red Hat ベースの仮想マシンの準備

### <a id="rhel67kvm"> </a>KVM からの RHEL 6.7 仮想マシンの準備###


1.	Red Hat の Web サイトからは、RHEL 6.7 の KVM イメージをダウンロードします。

2.	ルート パスワードを設定します。

    暗号化されたパスワードを生成し、コマンドの出力をコピーします。

        # openssl passwd -1 changeme

    guestfish でルート パスワードを設定します。

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

	ルート ユーザーの 2 番目のフィールドを、"!!" から、暗号化されたパスワードに変更します。

3.	qcow2 イメージから KVM に仮想マシンを作成し、ディスクの種類を **qcow2** に設定して、仮想ネットワーク インターフェイスのデバイス モデルを **virtio** に設定します。その後、仮想マシンを起動し、ルートとしてサインインします。

4.	`/etc/sysconfig/` ディレクトリに **network** という名前でファイルを作成し、次のテキストを追加します。

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	`/etc/sysconfig/network-scripts/` ディレクトリに **ifcfg-eth0** という名前でファイルを作成し、次のテキストを追加します。

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	udev ルールを移動 (または削除) して、イーサネット インターフェイスの静的ルールが生成されないようにします。これらのルールは、Microsoft Azure または Hyper-V で仮想マシンを複製する際に問題の原因となります。

        # mkdir -m 0700 /var/lib/waagent
        # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # chkconfig network on

8.	RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # subscription-manager register --auto-attach --username=XXX --password=XXX

9.	GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。これを行うには、テキスト エディターで `/boot/grub/menu.lst` を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。これにより、RHEL 6 で使用されているカーネル バージョンのバグが原因で NUMA が無効になります。

    上記の操作に加えて、次のパラメーターを削除することをお勧めします。

        rhgb quiet crashkernel=auto

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。必要に応じて crashkernel オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少します。このため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

10. Hyper-V モジュールを initramfs に追加します。

    `/etc/dracut.conf` を編集して、コンテンツを追加します: add\_drivers+="hv\_vmbus hv\_netvsc hv\_storvsc"

    initramfs を再構築します: # dracut –f -v

11.	cloud-init をアンインストールします。

        # yum remove cloud-init

12.	SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。

        # chkconfig sshd on

    /etc/ssh/sshd\_config を変更して、次の行を含めます。

        PasswordAuthentication yes
        ClientAliveInterval 180

    sshd を起動します。

		# service sshd restart

13.	WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。次のコマンドを実行して extras リポジトリを有効にします。

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

14.	次のコマンドを実行して Azure Linux エージェントをインストールします。

        # yum install WALinuxAgent
        # chkconfig waagent on

15.	Azure Linux エージェントは、Azure で VM がプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成できます。ローカル リソース ディスクは一時ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。Azure Linux エージェントのインストール後に (前の手順を参照)、**/etc/waagent.conf** にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16.	次のコマンドを実行して、サブスクリプションを登録解除します (必要な場合)。

        # subscription-manager unregister

17.	次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18.	KVM で VM をシャット ダウンします。

19.	qcow2 イメージを VHD 形式に変換します。まず、イメージを未加工の形式に変換します。

         # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    未加工のイメージのサイズが 1 MB になっていることを確認します。そうでない場合は、1 MB になるようにサイズの端数を切り上げます: # MB=$((1024*1024)) # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \\ gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}') # rounded\_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-6.7.raw $rounded_size

    未フォーマット ディスクを固定サイズの VHD に変換します。

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xkvm"> </a>KVM からの RHEL 7.1/7.2 仮想マシンの準備###


1.	Red Hat の Web サイトから RHEL 7.1 (または 7.2) の KVM イメージをダウンロードします。次の例では RHEL 7.1 を使用します。

2.	ルート パスワードを設定します。

    暗号化されたパスワードを生成し、コマンドの出力をコピーします。

        # openssl passwd -1 changeme

    guestfish でルート パスワードを設定します。

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    ルート ユーザーの 2 番目のフィールドを、"!!" から、暗号化されたパスワードに変更します。

3.	qcow2 イメージから KVM に仮想マシンを作成し、ディスクの種類を **qcow2** に設定して、仮想ネットワーク インターフェイスのデバイス モデルを **virtio** に設定します。その後、仮想マシンを起動し、ルートとしてサインインします。

4.	`/etc/sysconfig/` ディレクトリに **network** という名前でファイルを作成し、次のテキストを追加します。

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.	`/etc/sysconfig/network-scripts/` ディレクトリに **ifcfg-eth0** という名前でファイルを作成し、次のテキストを追加します。

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.	次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # chkconfig network on

7.	RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8.	GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。これを行うには、テキスト エディターで `/etc/default/grub` を開き、**GRUB\_CMDLINE\_LINUX** パラメーターを編集します。次に例を示します。

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。上記の操作に加えて、次のパラメーターを削除することをお勧めします。

        rhgb quiet crashkernel=auto

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。必要に応じて crashkernel オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少します。このため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

9.	`/etc/default/grub` の編集を終了したら、次のコマンドを実行して GRUB 構成を再構築します。

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10.	Hyper-V モジュールを initramfs に追加します。

    `/etc/dracut.conf` を編集して、コンテンツを追加します。

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    initramfs を再構築します。

        # dracut –f -v

11.	cloud-init をアンインストールします。

        # yum remove cloud-init

12.	SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。

        # systemctl enable sshd

    /etc/ssh/sshd\_config を変更して、次の行を含めます。

        PasswordAuthentication yes
        ClientAliveInterval 180

    sshd を起動します。

        systemctl restart sshd

13.	WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。次のコマンドを実行して extras リポジトリを有効にします。

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14.	次のコマンドを実行して Azure Linux エージェントをインストールします。

        # yum install WALinuxAgent

    waagent サービスを有効にします。

        # systemctl enable waagent.service

15.	OS ディスクにスワップ領域を作成しないでください。Azure Linux エージェントは、Azure で VM がプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成できます。ローカル リソース ディスクは一時ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。Azure Linux エージェントのインストール後に (前の手順を参照)、`/etc/waagent.conf` にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16.	次のコマンドを実行して、サブスクリプションを登録解除します (必要な場合)。

        # subscription-manager unregister

17.	次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18.	KVM で仮想マシンをシャットダウンします。

19.	qcow2 イメージを VHD 形式に変換します。

    まず、イメージを未加工の形式に変換します。

         # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw

    未加工のイメージのサイズが 1 MB になっていることを確認します。そうでない場合は、1 MB になるようにサイズの端数を切り上げます。

         # MB=$((1024*1024))
         # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                  gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
         # rounded_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-7.1.raw $rounded_size

    未フォーマット ディスクを固定サイズの VHD に変換します。

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## VMware からの Red Hat ベースの仮想マシンの準備
### 前提条件
このセクションでは、VMware に RHEL の仮想マシンが既にインストールされていると仮定します。VMware にオペレーティング システムをインストールする方法の詳細については、[VMware のゲスト オペレーティング システムのインストール ガイド](http://partnerweb.vmware.com/GOSIG/home.html)を参照してください。

- Linux オペレーティング システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。必要な場合は、LVM または RAID をデータ ディスク上で使用できます。

- OS ディスクにスワップ パーティションを構成しないでください。一時的なリソース ディスク上にスワップ ファイルを作成するよう Linux エージェントを構成できます。この詳細については、次の手順を参照してください。

- 仮想ハード ディスクを作成する場合は、**[Store virtual disk as a single file (仮想ディスクを 1 つのファイルとして格納する)]** を選択します。



### <a id="rhel67vmware"> </a>VMWare からの RHEL 6.7 仮想マシンの準備###

1.	次のコマンドを実行して NetworkManager をアンインストールします。

         # sudo rpm -e --nodeps NetworkManager

    パッケージがまだインストールされていない場合、このコマンドは失敗してエラー メッセージが表示されます。これは予期されることです。

2.	/etc/sysconfig/ ディレクトリに **network** という名前のファイルを作成し、次のテキストを追加します。

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3.	/etc/sysconfig/network-scripts/ ディレクトリに **ifcfg-eth0** という名前のファイルを作成し、次のテキストを追加します。

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4.	udev ルールを移動 (または削除) して、イーサネット インターフェイスの静的ルールが生成されないようにします。これらのルールは、Microsoft Azure または Hyper-V で仮想マシンを複製する際に問題の原因となります。

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

5.	次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # sudo chkconfig network on

6.	RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。次のコマンドを実行して extras リポジトリを有効にします。

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

8.	GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。これを行うには、テキスト エディターで "/boot/grub/menu.lst" を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。これにより、RHEL 6 で使用されているカーネル バージョンのバグが原因で NUMA が無効になります。上記の操作に加えて、次のパラメーターを削除することをお勧めします。

        rhgb quiet crashkernel=auto

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。必要に応じて crashkernel オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少します。このため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

9.	Hyper-V モジュールを initramfs に追加します。

	    Edit `/etc/dracut.conf` and add content:

	        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

	    Rebuild initramfs:

	        # dracut –f -v

10.	SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。通常これが既定です。`/etc/ssh/sshd_config` を変更して、次の行を含めます。

        ClientAliveInterval 180

11.	次のコマンドを実行して Azure Linux エージェントをインストールします。

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

12.	OS ディスクにスワップ領域を作成しないでください。

    Azure Linux エージェントは、Azure で VM がプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成できます。ローカル リソース ディスクは一時ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。Azure Linux エージェントのインストール後に (前の手順を参照)、`/etc/waagent.conf` にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13.	次のコマンドを実行して、サブスクリプションを登録解除します (必要な場合)。

        # sudo subscription-manager unregister

14.	次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15.	VM をシャットダウンし、VMDK ファイルを .vhd ファイルに変換します。

    まず、イメージを未加工の形式に変換します。

        # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw

    未加工のイメージのサイズが 1 MB になっていることを確認します。そうでない場合は、1 MB になるようにサイズの端数を切り上げます。

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
                gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.7.raw $rounded_size

    未フォーマット ディスクを固定サイズの VHD に変換します。

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xvmware"> </a>VMWare からの RHEL 7.1/7.2 仮想マシンの準備###

1.	/etc/sysconfig/ ディレクトリに **network** という名前のファイルを作成し、次のテキストを追加します。

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2.	/etc/sysconfig/network-scripts/ ディレクトリに **ifcfg-eth0** という名前のファイルを作成し、次のテキストを追加します。

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

3.	次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # sudo chkconfig network on

4.	RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5.	GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。これを行うには、テキスト エディターで `/etc/default/grub` を開き、**GRUB\_CMDLINE\_LINUX** パラメーターを編集します。次に例を示します。

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。上記の操作に加えて、次のパラメーターを削除することをお勧めします。

        rhgb quiet crashkernel=auto

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。必要に応じて crashkernel オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少します。このため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

6.	`/etc/default/grub` の編集を終了したら、次のコマンドを実行して GRUB 構成を再構築します。

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.	Hyper-V モジュールを initramfs に追加します。

    `/etc/dracut.conf` を編集して、コンテンツを追加します。

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    initramfs を再構築します。

        # dracut –f -v

8.	SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。通常これが既定です。`/etc/ssh/sshd_config` を変更して、次の行を含めます。

        ClientAliveInterval 180

9.	WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。次のコマンドを実行して extras リポジトリを有効にします。

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10.	次のコマンドを実行して Azure Linux エージェントをインストールします。

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11.	OS ディスクにスワップ領域を作成しないでください。Azure Linux エージェントは、Azure で VM がプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成できます。ローカル リソース ディスクは一時ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。Azure Linux エージェントのインストール後に (前の手順を参照)、`/etc/waagent.conf` にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12.	サブスクリプションを登録解除する場合は、次のコマンドを実行します。

        # sudo subscription-manager unregister

13.	次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14.	VM をシャットダウンし、VMDK ファイルを VHD 形式に変換します。

    まず、イメージを未加工の形式に変換します。

        # qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw

    未加工のイメージのサイズが 1 MB になっていることを確認します。そうでない場合は、1 MB になるようにサイズの端数を切り上げます。

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                 gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.1.raw $rounded_size

    未フォーマット ディスクを固定サイズの VHD に変換します。

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## kickstart ファイルを使用して ISO から Red Hat ベースの仮想マシンを自動的に準備する


### <a id="rhel7xkickstart"> </a>kickstart ファイルからの RHEL 7.1/7.2 仮想マシンの準備###


1.	以下のコンテンツを含む kickstart ファイルを作成し、そのファイルを保存します。kickstart のインストールの詳細については、[kickstart インストール ガイド](https://access.redhat.com/documentation/ja-JP/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)を参照してください。



        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
        auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff



        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^(ResourceDisk\.EnableSwap)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^(ResourceDisk\.SwapSizeMB)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^(GRUB_CMDLINE_LINUX)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#(ClientAliveInterval).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=yes
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

2.	インストール システムからアクセス可能な場所に kickstart ファイルを置きます。

3.	Hyper-V マネージャーで新しい VM を作成します。**[仮想ハード ディスクの接続]** ページで、**[後で仮想ハード ディスクを接続する]** を選択し、仮想マシンの新規作成ウィザードを完了します。

4.	VM 設定を開きます。

    a.新しい仮想ハード ディスクを VM に接続します。**[VHD Format (VHD 形式)]** と **[固定サイズ]** を選択します。

    b.インストール ISO を DVD ドライブに接続します。

    c.CD から起動するように BIOS を設定します。

5.	VM を起動します。インストール ガイドが表示されたら、**Tab** キーを押してブート オプションを構成します。

6.	ブート オプションの最後に `inst.ks=<the location of the kickstart file>` を入力し、**Enter** キーを押します。

7.	インストールが完了するのを待ちます。完了すると、VM は自動的にシャットダウンします。これで、Linux VHD を Azure にアップロードする準備が整いました。

## 既知の問題
HYPER-V と Azure で RHEL 7.1 を使用する場合に既知の問題があります。

### ディスク I/O のフリーズ

この問題は、Hyper-V および Azure の RHEL 7.1 で、ストレージ ディスク I/O のアクティビティが頻繁な場合に発生する可能性があります。

再現率:

この問題の発生は断続的です。Hyper-V および Azure で頻繁に行われるディスク I/O 操作中に非常によく発生します。


[AZURE.NOTE] この既知の問題は既に Red Hat で解決されています。関連付けられている修正プログラムをインストールするには、次のコマンドを実行します。

    # sudo yum update

### 非 Hyper-V ハイパーバイザーの使用時に Hyper-V ドライバーを初期 RAM ディスクに含めることができない

Linux インストーラーは、Hyper-V 環境で実行されていることを検出しない限り、初期 RAM ディスク (initrd または initramfs) に Hyper-V 用のドライバーが含まれないことがあります。

別の仮想化システム (Virtualbox、Xen など) を使用して Linux イメージを準備する場合は、少なくとも hv\_vmbus と hv\_storvsc のカーネル モジュールを初期 RAM ディスクで使用できるように initrd の再構築が必要になる場合があります。これは少なくとも、アップ ストリームの Red Hat ディストリビューションに基づくシステムの既知の問題です。

この問題を解決するには、initramfs に Hyper-V モジュールを追加して再構築する必要があります。

`/etc/dracut.conf` を編集して、コンテンツを追加します。

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

initramfs を再構築します。

        # dracut –f -v

詳細については、[initramfs の再構築](https://access.redhat.com/solutions/1958)に関する情報を参照してください。

## 次のステップ
これで、Red Hat Enterprise Linux 仮想ハード ディスク を使用して、Azure に新しい仮想マシンを作成する準備が整いました。.vhd ファイルを Azure に初めてアップロードする場合は、「[Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](virtual-machines-linux-classic-create-upload-vhd.md)」の手順 2 と 3 をご覧ください。

Red Hat Enterprise Linux の実行が認定されているハイパーバイザーの詳細については、[Red Hat の Web サイト](https://access.redhat.com/certified-hypervisors)を参照してください。

<!---HONumber=AcomDC_0518_2016-->