<properties linkid="manage-linux-common-task-upload-vhd" urlDisplayName="VHD のアップロード" pageTitle="Azure 上での Linux VHD の作成とアップロード" metaKeywords="Azure VHD, Linux VHD のアップロード" description="Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />





# Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード

Azure の仮想マシンでは、仮想マシンの作成時に選択したオペレーティング システムが実行されます。Azure は、仮想マシンのオペレーティング システムを VHD 形式 (.vhd ファイル) で仮想ハード ディスクに格納します。複製用に準備されたオペレーティング システムの VHD はイメージと呼ばれます。この記事では、インストールおよび一般化したオペレーティング システムの .vhd ファイルをアップロードすることで、独自のイメージを作成する方法について説明します。Azure でのディスクとイメージの詳細については、「[ディスクおよびイメージの管理](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj672979.aspx)」を参照してください。

**注**: 仮想マシンを作成するときに、オペレーティング システムの設定をカスタマイズして、アプリケーションを実行しやすくできます。設定した構成は、その仮想マシンのディスクに格納されます。手順については、「[カスタム仮想マシンの作成方法](/ja-jp/manage/windows/how-to-guides/custom-create-a-vm/)」を参照してください。

**重要**: Azure プラットフォームの SLA は、動作保証済みディストリビューションのいずれか 1 つを[この記事](http://support.microsoft.com/kb/2805216)で指定されている構成で使用した場合にのみ、Linux OS を実行する仮想マシンに適用されます。Azure イメージ ギャラリーにあるすべての Linux ディストリビューションは、必須の構成による動作保証済みディストリビューションです。


##前提条件##
この記事では、次の項目があることを前提としています。

- **管理証明書** - VHD をアップロードするサブスクリプションの管理証明書を作成し、その証明書を .cer ファイルにエクスポートしました。証明書の作成方法の詳細については、「[Azure の管理証明書の作成とアップロード](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx)」を参照してください。

- **.vhd ファイルにインストールされている Linux オペレーティング システム。**- サポートされている Linux オペレーティング システムを仮想ハード ディスクにインストールしています。.vhd ファイルを作成するツールはいくつかあります。Hyper-V などの仮想化ソリューションを使用して .vhd ファイルを作成し、オペレーティング システムをインストールすることができます。詳細については、「[Hyper-V の役割のインストールと仮想マシンの構成](http://technet.microsoft.com/library/hh846766.aspx)」を参照してください。

	**重要**: 新しい VHDX 形式は、Azure ではサポートされていません。Hyper-V マネージャーまたは convert-vhd コマンドレットを使用して、ディスクを VHD 形式に変換できます。

	動作保証済みディストリビューションの一覧については、「[Azure での動作保証済み Linux ディストリビューション](../linux-endorsed-distributions)」を参照してください。または、この記事の末尾の「[動作保証外のディストリビューションに関する情報][]」のセクションを参照してください。

- **Linux Azure コマンド ライン ツール。**Linux オペレーティング システムを使用してイメージを作成する場合は、このツールを使用して VHD ファイルをアップロードします。[Mac および Linux 用 Azure コマンド ライン ツール](http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409)をダウンロードしてください。

- **Add-AzureVHD コマンドレット**。これは Azure PowerShell モジュールの一部です。このモジュールをダウンロードするには、[Azure のダウンロード ページ] (/ja-jp/develop/downloads/) にアクセスしてください。詳細については、[Add-AzureVhd に関するページ](http://msdn.microsoft.com/library/windowsazure/dn495173.aspx)を参照してください。

**すべてのディストリビューションについて次の点に注意してください。**

- Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。

- Azure Linux エージェント (waagent) は NetworkManager と互換性がありません。ネットワーク構成は ifup/ifdown スクリプトから制御できる必要があります。ディストリビューションによって提供される多くの RPM/Deb パッケージでは、NetworkManager が waagent パッケージに対する競合として構成されるため、Linux エージェント パッケージをインストールすると NetworkManager がアンインストールされます。また、Azure Linux エージェントを使用するには、python-pyasn1 パッケージがインストールされている必要があります。

- VM サイズが大きい場合は NUMA がサポートされません。2.6.37 未満のバージョンの Linux カーネルにバグがあるためです。waagent を手動でインストールすると、Linux カーネルの GRUB 構成で NUMA が自動的に無効になります。この問題は、主に、アップストリームの Red Hat 2.6.32 カーネルを使用したディストリビューションに影響します。

- インストール時にスワップ パーティションを作成することは避けてください。Azure Linux エージェントを使用してスワップ領域を構成することはできます。また、[Microsoft Web サイト](http://go.microsoft.com/fwlink/?LinkID=253692&clcid=0x409)から入手可能な修正プログラムを適用せずに、広く使用されている Linux カーネルを Azure の仮想マシンで使用することも避けてください (最新のディストリビューション/カーネルの多くには、この修正が含まれています)。

- すべての VHD のサイズは 1 MB の倍数であることが必要です。


このタスクの手順は次のとおりです。

- [手順 1. アップロードするイメージを準備する] []
- [手順 2. Azure にストレージ アカウントを作成する] []
- [手順 3. Azure への接続を準備する] []
- [手順 4. Azure にイメージをアップロードする] []

## <a id="prepimage"> </a>手順 1. アップロードするイメージを準備する##

### CentOS 6.2 以上を準備する###

Azure 上で実行する仮想マシンのオペレーティング システムで固有の構成手順を完了する必要があります。

1. Hyper-V マネージャーの中央のウィンドウで仮想マシンを選択します。

2. **[接続]** をクリックすると、仮想マシンのウィンドウが開きます。

3. 次のコマンドを実行して NetworkManager をアンインストールします。

		rpm -e --nodeps NetworkManager

	**注意:** パッケージがまだインストールされていない場合、このコマンドは失敗してエラー メッセージが表示されます。これは予期されることです。

4.	`/etc/sysconfig/` ディレクトリに **network** という名前のファイルを作成し、次のテキストを追加します。

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

5.	`/etc/sysconfig/network-scripts/` ディレクトリに **ifcfg-eth0** という名前のファイルを作成し、次のテキストを追加します。

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

6.	udev ルールを移動 (または削除) して、イーサネット インターフェイスの静的ルールが生成されないようにします。これらのルールは、Azure または Hyper-V で仮想マシンを複製する際に問題の原因となります。

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

		# chkconfig network on

8.	**CentOS 6.2 または 6.3 のみ**: Linux Integration Services 用ドライバーをインストールします。

	**注:** この手順は CentOS 6.2 および CentOS 6.3 にのみ有効です。CentOS 6.4 以上では、Linux Integration Services はカーネルに組み込み済みです。

	a) [ダウンロード センター](http://www.microsoft.com/ja-jp/download/details.aspx?id=34603)から Linux Integration Services 用ドライバーが格納されている .iso ファイルを入手します。

	b) Hyper-V マネージャーの **[アクション]** ウィンドウで **[設定]** をクリックします。

	![Hyper-V の設定を開く](./media/virtual-machines-linux-create-upload-vhd/settings.png)

	c) **[ハードウェア]** ウィンドウで **[IDE コントローラー 1]** をクリックします。

	![インストール メディアがセットされた DVD ドライブの追加](./media/virtual-machines-linux-create-upload-vhd/installiso.png)

	d) **[IDE コントローラー]** ボックスで、**[DVD ドライブ]** をクリックし、**[追加]** をクリックします。

	e) **[イメージ ファイル]** を選択し、**Linux IC v3.2.iso** を参照して、**[開く]** をクリックします。

	f) **[設定]** ページで **[OK]** をクリックします。

	g) **[接続]** をクリックして仮想マシンのウィンドウを開きます。

	h) コマンド プロンプト ウィンドウに次のコマンドを入力します。

		mount /dev/cdrom /media
		/media/install.sh
		reboot

9. 次のコマンドを実行して python-pyasn1 をインストールします。

		# sudo yum install python-pyasn1

10. /etc/yum.repos.d/CentOS-Base.repo ファイルの内容を次のテキストに置き換えます。

		[openlogic]
		name=CentOS-$releasever - openlogic packages for $basearch
		baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
		enabled=1
		gpgcheck=0
		
		[base]
		name=CentOS-$releasever - Base
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#released updates
		[updates]
		name=CentOS-$releasever - Updates
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#additional packages that may be useful
		[extras]
		name=CentOS-$releasever - Extras
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#additional packages that extend functionality of existing packages
		[centosplus]
		name=CentOS-$releasever - Plus
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#contrib - packages by Centos Users
		[contrib]
		name=CentOS-$releasever - Contrib
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

11.	/etc/yum.conf ファイルに次の行を追加します。

		http_caching=packages

	さらに、CentOS 6.2 および 6.3 でのみ、次の行を追加します。

		exclude=kernel*

12. "/etc/yum/pluginconf.d/fastestmirror.conf" ファイルの [main] で次のように入力して、yum モジュール "fastestmirror" を無効にします。

		set enabled=0

13.	次のコマンドを実行して、現在の Yum メタデータをクリアします。

		yum clean all

14. CentOS 6.2 および 6.3 の場合、次のコマンドを実行して、実行中の VM のカーネルを更新します。

	CentOS 6.2 の場合、次のコマンドを実行します。

		sudo yum remove kernel-firmware
		sudo yum --disableexcludes=all install kernel

	CentOS 6.3 以上の場合、次のコマンドを入力します。

		sudo yum --disableexcludes=all install kernel

15.	GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。これを行うには、テキスト エディターで /boot/grub/menu.lst を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

	これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。さらに、これにより、CentOS 6 で使用されているカーネル バージョンのバグが原因で NUMA が無効になります。

16.	/etc/sudoers で、次のコード行をコメント アウトします (ある場合)。

		Defaults targetpw

17.	SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。

18. 次のコマンドを実行して Azure Linux エージェントをインストールします。

		yum install WALinuxAgent

	手順 2. で説明したように NetworkManager パッケージおよび NetworkManager-gnome パッケージがまだ削除されていない場合、WALinuxAgent パッケージをインストールすると、これらのパッケージが削除されることに注意してください。

19.	OS ディスクにスワップ領域を作成しないでください。

	Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

20.	次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

		waagent -force -deprovision
		export HISTSIZE=0
		logout

21. Hyper-V マネージャーで **[シャットダウン]** をクリックします。


### Ubuntu 12.04 以上を準備する###

1. Hyper-V マネージャーの中央のウィンドウで仮想マシンを選択します。

2. **[接続]** をクリックすると、仮想マシンのウィンドウが開きます。

3.	イメージ上の現在のリポジトリを、VM のアップグレードに必要になるカーネルとエージェント パッケージが格納されている Azure リポジトリに置き換えます。この手順は、Ubuntu のバージョンによって多少異なります。

	/etc/apt/sources.list を編集する前に、バックアップを作成することをお勧めします。
		sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

	Ubuntu 12.04: 

		sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
		sudo apt-get update

	Ubuntu 12.10: 

		sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
		sudo apt-get update

	Ubuntu 13.04 以上: 

		sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		sudo apt-get update

4. 次のコマンドを実行してオペレーティング システムを最新のカーネルに更新します。

	Ubuntu 12.04: 

		sudo apt-get update
		sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
		(recommended) sudo apt-get dist-upgrade
		sudo reboot

	Ubuntu 12.10: 

		sudo apt-get update
		sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
		(recommended) sudo apt-get dist-upgrade
		sudo reboot
	
	Ubuntu 13.04、13.10、および 14.04:

		sudo apt-get update
		sudo apt-get install hv-kvp-daemon-init
		(recommended) sudo apt-get dist-upgrade
		sudo reboot

5.	Ubuntu はシステムのクラッシュ後に Grub のプロンプトでユーザー入力を待ちます。この動作を回避するために、次の手順を実行します。

	a) /etc/grub.d/00_header ファイルを開きます。

	b) **make_timeout()** 関数内で **if ["\${recordfail}" = 1 ]; then** を検索します。

	c) この行の下の文を **set timeout=5** に変更します。

	d) 'sudo update-grub' を実行します。

6. Grub のカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。これを行うには、テキスト エディターで /etc/default/grub を開き、"GRUB_CMDLINE_LINUX_DEFAULT" という変数を探して (または、必要であれば追加して)、次のパラメーターが含まれるように編集します。

		GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

	このファイルを保存して閉じ、'sudo update-grub' を実行します。これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。

7.	/etc/sudoers で、次のコード行をコメント アウトします (ある場合)。

		Defaults targetpw

8.	SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。

9.	sudo で次のコマンドを実行してエージェントをインストールします。

		apt-get update
		apt-get install walinuxagent

	NetworkManager パッケージおよび NetworkManager-gnome パッケージがインストールされている場合、walinuxagent パッケージをインストールするとこれらのパッケージが削除されることに注意してください。

10.	OS ディスクにスワップ領域を作成しないでください。

	Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11.	次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

		waagent -force -deprovision
		export HISTSIZE=0
		logout

12. Hyper-V マネージャーで **[シャットダウン]** をクリックします。


### Oracle Linux 6.4 以上を準備する###

Azure 上で実行する仮想マシンのオペレーティング システムで固有の構成手順を完了する必要があります。

1. Hyper-V マネージャーの中央のウィンドウで仮想マシンを選択します。

2. **[接続]** をクリックすると、仮想マシンのウィンドウが開きます。

3. 次のコマンドを実行して NetworkManager をアンインストールします。

		rpm -e --nodeps NetworkManager

	**注意:** パッケージがまだインストールされていない場合、このコマンドは失敗してエラー メッセージが表示されます。これは予期されることです。

4.	`/etc/sysconfig/` ディレクトリに **network** という名前のファイルを作成し、次のテキストを追加します。

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

5.	`/etc/sysconfig/network-scripts/` ディレクトリに **ifcfg-eth0** という名前のファイルを作成し、次のテキストを追加します。

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

6.	udev ルールを移動 (または削除) して、イーサネット インターフェイスの静的ルールが生成されないようにします。これらのルールは、Azure または Hyper-V で仮想マシンを複製する際に問題の原因となります。

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

		# chkconfig network on

8. 次のコマンドを実行して python-pyasn1 をインストールします。

		# sudo yum install python-pyasn1

9.	GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。これを行うには、テキスト エディターで /boot/grub/menu.lst を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

	これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。さらに、これにより、CentOS 6 で使用されているカーネル バージョンのバグが原因で NUMA が無効になります。

10.	/etc/sudoers で、次のコード行をコメント アウトします (ある場合)。

		Defaults targetpw

11.	SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。

12. 次のコマンドを実行して Azure Linux エージェントをインストールします。

		yum install WALinuxAgent

	NetworkManager パッケージおよび NetworkManager-gnome パッケージがインストールされている場合、WALinuxAgent パッケージをインストールするとこれらのパッケージが削除されることに注意してください。

13.	OS ディスクにスワップ領域を作成しないでください。

	Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14.	次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

		waagent -force -deprovision
		export HISTSIZE=0
		logout

15. Hyper-V マネージャーで **[シャットダウン]** をクリックします。


### SUSE Linux Enterprise Server 11 SP2 および SP3 を準備する###

**注:** [SUSE Studio](http://www.susestudio.com) では、Azure および Hyper-V 用の SLES/opeSUSE イメージを簡単に作成、管理できます。さらに、SUSE Studio ギャラリーにある次の公式イメージをダウンロードまたは SUSE Studio アカウントに複製して、簡単にカスタマイズできます。

> - [SUSE Studio ギャラリーの Azure 向け SLES 11 SP3](http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure)

1. Hyper-V マネージャーの中央のウィンドウで仮想マシンを選択します。

2. **[接続]** をクリックすると、仮想マシンのウィンドウが開きます。

3. 最新のカーネルおよび Azure Linux エージェントが格納されているリポジトリを追加します。コマンド `zypper lr` を実行します。たとえば、SLES 11 SP3 の出力は次のようになります。

		# | Alias                        | Name               | Enabled | Refresh
		--+------------------------------+--------------------+---------+--------
		1 | susecloud:SLES11-SP1-Pool    | SLES11-SP1-Pool    | No      | Yes
		2 | susecloud:SLES11-SP1-Updates | SLES11-SP1-Updates | No      | Yes
		3 | susecloud:SLES11-SP2-Core    | SLES11-SP2-Core    | No      | Yes
		4 | susecloud:SLES11-SP2-Updates | SLES11-SP2-Updates | No      | Yes
		5 | susecloud:SLES11-SP3-Pool    | SLES11-SP3-Pool    | Yes     | Yes
		6 | susecloud:SLES11-SP3-Updates | SLES11-SP3-Updates | Yes     | Yes

 	このコマンドでは、次のようなエラー メッセージが返されます。

		"No repositories defined.Use the 'zypper addrepo' command to add one or more repositories."

	この場合、リポジトリを再度有効にするか、システムを登録する必要があります。これには、suse_register ユーティリティを使用します。詳細については、[SLES のドキュメント](https://www.suse.com/documentation/sles11/)を参照してください。

   更新したリポジトリのいずれかが有効になっていない場合は、次のコマンドを使用して有効にします。

		zypper mr -e [REPOSITORY NUMBER]

   この例では、適切なコマンドは次のようになります。

		zypper mr -e 1 2 3 4

4. カーネルを最新のバージョンに更新します。

		zypper up kernel-default

5. Azure Linux エージェントをインストールします。

		zypper up WALinuxAgent
		
	次のようなメッセージが表示されます。

		"There is an update candidate for 'WALinuxAgent', but it is from different vendor.
		Use 'zypper install WALinuxAgent-1.2-1.1.noarch' to install this candidate."

	パッケージのベンダーが "Microsoft Corporation" から "SUSE LINUX Products GmbH, Nuernberg, Germany" に変更されたため、メッセージで示されているパッケージを明示的にインストールする必要があります。

	注意: WALinuxAgent パッケージのバージョンは多少異なる場合があります。

6. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。これを行うには、テキスト エディターで /boot/grub/menu.lst を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。

7.	/etc/sysconfig/network/dhcp または同等のファイルで DHCLIENT_SET_HOSTNAME="yes" を DHCLIENT_SET_HOSTNAME="no" に変更することをお勧めします。

8.	/etc/sudoers で、次のコード行をコメント アウトします (ある場合)。

		Defaults targetpw

9.	SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。

10.	OS ディスクにスワップ領域を作成しないでください。

	Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11.	次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

		waagent -force -deprovision
		export HISTSIZE=0
		logout

12. Hyper-V マネージャーで **[シャットダウン]** をクリックします。


### openSUSE 12.3 以上を準備する###

**注:** [SUSE Studio](http://www.susestudio.com) では、Azure および Hyper-V 用の SLES/opeSUSE イメージを簡単に作成、管理できます。さらに、SUSE Studio ギャラリーにある次の公式イメージをダウンロードまたは SUSE Studio アカウントに複製して、簡単にカスタマイズできます。

> - [SUSE Studio ギャラリーの Azure 向け openSUSE 13.1](https://susestudio.com/a/02kbT4/opensuse-13-1-for-windows-azure)

1. Hyper-V マネージャーの中央のウィンドウで仮想マシンを選択します。

2. **[接続]** をクリックすると、仮想マシンのウィンドウが開きます。

3.  オペレーティング システムを最新のカーネルに更新し、更新プログラムを適用します。

4. シェルでコマンド '`zypper lr`' を実行します。このコマンドによって次のような出力が返されます (バージョン番号は異なる場合があります)。

		# | Alias                     | Name                      | Enabled | Refresh
		--+---------------------------+---------------------------+---------+--------
		1 | Cloud:Tools_openSUSE_12.3 | Cloud:Tools_openSUSE_12.3 | Yes     | Yes
		2 | openSUSE_12.3_OSS         | openSUSE_12.3_OSS         | Yes     | Yes
		3 | openSUSE_12.3_Updates     | openSUSE_12.3_Updates     | Yes     | Yes

   この場合、リポジトリは予期したとおりに構成されているため、調整は不要です。

   コマンドによって "No repositories defined. Use the 'zypper addrepo' command to add one 
   or more repositories" と返された場合は、次のようにリポジトリを再度有効にする必要があります。

		zypper ar -f http://download.opensuse.org/distribution/12.3/repo/oss openSUSE_12.3_OSS
		zypper ar -f http://download.opensuse.org/update/12.3 openSUSE_12.3_Updates

   'zypper lr' をもう一度呼び出してリポジトリが追加されたことを確認します。次の出力が予期されます。

   更新したリポジトリのいずれかが有効になっていない場合は、次のコマンドを使用して有効にします。

		zypper mr -e [NUMBER OF REPOSITORY]

5.	DVD ROM の自動プローブを無効にします。

6.	Azure Linux エージェントをインストールします。

	まず、新しい WALinuxAgent が格納されているリポジトリを追加します。

		zypper ar -f -r http://download.opensuse.org/repositories/Cloud:/Tools/openSUSE_12.3/Cloud:Tools.repo

	次に、次のコマンドを実行します。
 
		zypper up WALinuxAgent

	このコマンドを実行すると、次のようなメッセージが表示されます。

		"There is an update candidate for 'WALinuxAgent', but it is from different vendor. 
		Use 'zypper install WALinuxAgent' to install this candidate."

	このメッセージは予測されるものです。パッケージのベンダーが "Microsoft Corporation" から "obs://build.opensuse.org/Cloud" に変更されたため、メッセージで示されているパッケージを明示的にインストールする必要があります。

7.	GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。これを行うには、テキスト エディターで /boot/grub/menu.lst を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。また、カーネルのブート行に次のパラメーターがある場合は削除します。

		libata.atapi_enabled=0 reserve=0x1f0,0x8

9.	/etc/sysconfig/network/dhcp または同等のファイルで DHCLIENT_SET_HOSTNAME="yes" を DHCLIENT_SET_HOSTNAME="no" に変更することをお勧めします。

10.	/etc/sudoers で、次のコード行をコメント アウトします (ある場合)。

		Defaults targetpw

11.	SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。

12.	OS ディスクにスワップ領域を作成しないでください。

	Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13.	次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

		waagent -force -deprovision
		export HISTSIZE=0
		logout

14. 起動時に Azure Linux エージェントが実行されるようにします。

		systemctl enable waagent.service

14. Hyper-V マネージャーで **[シャットダウン]** をクリックします。


## <a id="createstorage"> </a>手順 2. Azure にストレージ アカウントを作成する##

ストレージ アカウントは、ストレージ サービスにアクセスするための最高レベルの名前空間を表し、Azure サブスクリプションに関連付けられています。仮想マシンの作成に使用できる .vhd ファイルを Azure にアップロードするには、Azure のストレージ アカウントが必要です。ストレージ アカウントは、Azure の管理ポータルを使って作成できます。

1. Azure 管理ポータルにサインインします。

2. コマンド バーで、**[新規]** をクリックします。

	![ストレージ アカウントの作成](./media/virtual-machines-linux-create-upload-vhd/create.png)

3. **[ストレージ アカウント]**、**[簡易作成]** の順にクリックします。

	![ストレージ アカウントの簡易作成](./media/virtual-machines-linux-create-upload-vhd/storage-quick-create.png)

4. 次のようにフィールドを指定します。

	![ストレージ アカウントの詳細の入力](./media/virtual-machines-linux-create-upload-vhd/storage-create-account.png)

- **[URL]** で、ストレージ アカウントの URL で使用するサブドメイン名を入力します。文字数は 3 ～ 24 文字で、アルファベット小文字と数字を使用できます。この名前は、対応するサブスクリプションの BLOB リソース、キュー リソース、またはテーブル リソースのアドレス指定に使用される URL のホスト名になります。
	
- ストレージ アカウントの場所またはアフィニティ グループを選択します。アフィニティ グループを指定することで、ストレージと同じデータ センターにクラウド サービスを配置できます。
 
- ストレージ アカウントのジオ (主要地域) レプリケーションを使用するかどうかを決定します。Geo レプリケーションは既定で有効です。このオプションでは、ユーザーのコスト負担なしで、データが 2 次拠点にコピーされるため、1 次拠点で対処できない大規模な障害が発生した場合に、2 次拠点にストレージをフェールオーバーすることができます。2 次拠点は自動的に割り当てられ、変更することはできません。法律上の要件または組織のポリシー上、クラウド方式のストレージの場所を厳格に管理する必要がある場合は、Geo レプリケーションを無効にすることができます。ただし、後で Geo レプリケーションを有効に戻すと、既存データを 2 次拠点にコピーするためのデータ転送料金が 1 回だけ発生することに注意してください。Geo レプリケーションなしのストレージ サービスも割引価格で提供されています。

5. **[ストレージ アカウントの作成]** をクリックします。

	作成したアカウントが **[ストレージ アカウント]** に表示されます。

	![ストレージ アカウントの作成に成功](./media/virtual-machines-linux-create-upload-vhd/Storagenewaccount.png)


## <a id="#connect"> </a>手順 3. Azure への接続を準備する##

.vhd ファイルをアップロードする前に、コンピューターと Azure のサブスクリプションの間にセキュリティで保護された接続を確立する必要があります。

1. Azure PowerShell ウィンドウを開きます。

2. 次のコマンドを入力します。

	`Get-AzurePublishSettingsFile`

	このコマンドにより、ブラウザー ウィンドウが開き、Azure サブスクリプションの情報と証明書を含む .publishsettings ファイルが自動的にダウンロードされます。

3. .publishsettings ファイルを保存します。

4. 次のコマンドを入力します。

	`Import-AzurePublishSettingsFile <PathToFile>`

	ここで、`<PathToFile>` は .publishsettings ファイルへの完全なパスです。

	詳細については、「[Azure コマンドレットの概要](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj554332.aspx)」を参照してください。


## <a id="upload"> </a>手順 4. Azure にイメージをアップロードする##

.vhd ファイルをアップロードするときは、BLOB ストレージ内であればどこにでも .vhd ファイルを置くことができます。以下のコマンドの例では、**BlobStorageURL** はステップ 2 で作成したストレージ アカウントの URL であり、**YourImagesFolder** は BLOB ストレージ内でイメージを格納するコンテナーです。**VHDName** は、仮想ハード ディスクを識別するために管理ポータルに表示されるラベルです。**PathToVHDFile** は、.vhd ファイルの完全なパスとファイル名です。

次のいずれかを実行します。

- 前の手順で使用した Azure PowerShell ウィンドウで、次のように入力します。

	`Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

	詳細については、[Add-AzureVhd に関するページ](http://msdn.microsoft.com/ja-jp/library/windowsazure/dn205185.aspx)を参照してください。

- Linux コマンド ライン ツールを使用してイメージをアップロードします。次のコマンドを使用してイメージをアップロードできます。

		Azure vm image create <image name> --location <Location of the data center> --OS Linux <Sourcepath to the vhd>

## <a id="nonendorsed"> </a>動作保証外のディストリビューションに関する情報##
基本的に、Azure 上で動作するすべてのディストリビューションは、プラットフォーム上で適切に実行できるように、次の前提条件を満たす必要があります。

この一覧は決して包括的なものではありません。前提条件はディストリビューションによって異なるためです。次に示している条件をすべて満たす場合でも、プラットフォーム上で適切に動作するようにイメージを微調整する必要があります。

この理由から[パートナー動作保証済みイメージ](https://www.windowsazure.com/ja-jp/manage/linux/other-resources/endorsed-distributions/)のいずれかで開始することをお勧めします。

次の手順は、独自の VHD を作成する「手順 1」に置き換わるものです。

1.	実行しているカーネルに Hyper-V の最新の LIS ドライバーが組み込まれていること、または、それらのドライバーを正常にコンパイルしたことを確認する必要があります (これらのドライバーはオープン ソース化されています)。必要なドライバーは[この場所](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)で見つかります。

2.	カーネルに ATA PiiX ドライバーの最新バージョンが組み込まれていることも必要です。そのドライバーを使用してイメージがプロビジョニングされます。また、修正プログラムがカーネルにコミットされている必要もあります (commit cd006086fa5d91414d8ff9ff2b78fbb593878e3c Date:   Fri May 4 22:15:11 2012 +0100   ata_piix: defer disks to the Hyper-V drivers by default)。

3.	圧縮後の initrd は 40 MB 未満であることが必要です (* このサイズは継続的に改善されているため、現状の数値と異なる場合があります)。

4.	Grub または Grub2 でカーネルのブート行を変更して次のパラメーターを含めます。これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

5.	/etc/sysconfig/network/dhcp または同等のファイルで DHCLIENT_SET_HOSTNAME="yes" を DHCLIENT_SET_HOSTNAME="no" に変更することをお勧めします。

6.	カーネルでマウントされているすべての SCSI デバイスの I/O タイムアウトが 300 秒以上であることを確認する必要があります。

7.	[Linux エージェント ガイド](https://www.windowsazure.com/ja-jp/manage/linux/how-to-guides/linux-agent-guide/)の手順に従って Azure Linux エージェントをインストールする必要があります。エージェントは Apache 2 のライセンス下でリリースされており、最新バージョンは[エージェントの GitHub サイト](http://go.microsoft.com/fwlink/p/?LinkID=250998&clcid=0x409)で入手できます。

8.	/etc/sudoers で、次のコード行をコメント アウトします (ある場合)。

		Defaults targetpw

9.	SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。

10.	OS ディスクにスワップ領域を作成しないでください。

	Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11.	次のコマンドを実行して仮想マシンをプロビジョニング解除することが必要になります。

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12.	その後、仮想マシンをシャットダウンし、アップロードの手順に進みます。


[手順 1. アップロードするイメージを準備する]: #prepimage
[手順 2. Azure にストレージ アカウントを作成する]: #createstorage
[手順 3. Azure への接続を準備する]: #connect
[手順 4. Azure にイメージをアップロードする]: #upload
[動作保証外のディストリビューションに関する情報]: #nonendorsed

