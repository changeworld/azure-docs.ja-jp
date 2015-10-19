<properties 
   pageTitle="Azure から VMware にフェールバックする手順" 
   description="この記事は、Azure Site Recovery と vContinuum ツールを使用して、仮想マシンを VMware にフェールバックする方法を説明します。" 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="10/07/2015"
   ms.author="ruturajd@microsoft.com"/>

# Azure から VMware にフェールバックする手順

このチュートリアルでは、Azure から VMware サイトにフェールバックするために必要な手順を、順を追って説明します。このチュートリアルの前提として、チュートリアル「[オンプレミスの VMWare 仮想マシンまたは物理サーバーと Azure 間の保護の設定](site-recovery-vmware-to-azure.md)」で説明されている手順が実行済みである必要があります。

仮想マシンは Azure へのフェールオーバーに成功すると、[仮想マシン] タブで利用できるようになります。フェールバックを行う際には、次の手順に従う必要があります。

Azure から VMware サイトにフェールバックする場合、復旧先は仮想マシンのみとなります。VMware 上の初期ソースが物理マシンだった場合でも、Azure へのフェールオーバー後の VMware へのフェールバックでは、仮想マシンに変換されます。

## 概要

1.  vContinuum サーバーをオンプレミスにインストールする

    a.CS をポイントするように構成する

2.  PS を Azure にデプロイする

3.  MT をオンプレミスにインストールする

4.  フェールオーバーした VM を、オンプレミスを宛先として保護する手順

    a.構成上の注意事項

5.  オンプレミスを宛先とする VM の保護の監視

6.  オンプレミスを宛先として VM をフェールオーバーする

以降の手順で達成するセットアップの概要を次に示します。セットアップの一部は、フェールオーバー時に既に完了しています。

-   青色の線は、フェールオーバー時に使用される接続です。

-   赤色の線は、フェールバック時に使用される接続です。

-   矢印のある線は、インターネットを経由することを意味します。

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## vContinuum をオンプレミスにインストールする

vContinuum セットアップをダウンロードするには、[ここ](http://go.microsoft.com/fwlink/?linkid=526305)をクリックしてください。さらに、[ここ](http://go.microsoft.com/fwlink/?LinkID=533813)をクリックして、vContinuum のパッチをダウンロードし、インストールしてください。

1.  セットアップを起動して、vContinuum のインストールを開始します。**[次へ]** をクリックします。![](./media/site-recovery-failback-azure-to-vmware/image2.png)
2.  CX サーバーの IP アドレスとポートを指定します。HTTPS を選択します。

	![](./media/site-recovery-failback-azure-to-vmware/image3.png)

3.  CX の IP アドレスを確認するには、Azure で CS のデプロイメントに移動し、そのダッシュボードを表示します。

	![](./media/site-recovery-failback-azure-to-vmware/image4.png)

4.  CX のパブリック ポートを確認するには、VM ページの [エンドポイント] タブに移動し、HTTPS エンドポイントのパブリック ポートを特定します。

	![](./media/site-recovery-failback-azure-to-vmware/image5.png)

5.  CS パスフレーズを指定します。パスフレーズは、CS を登録する際にメモされています。パスフレーズは、MT および PS のデプロイメントでも使用しています。パスフレーズを忘れた場合は、Azure 上の CS サーバーにアクセスすると、C:\\Program Files (x86)\\InMage Systems\\private\\connection.passphrase の下で見つかります。

	![](./media/site-recovery-failback-azure-to-vmware/image6.png)

6.  vContinuum サーバーをインストールする場所を指定して、インストールを開始します。

	![](./media/site-recovery-failback-azure-to-vmware/image7.png)

7.  インストールが完了したことを確認したら、vContinuum を起動して、動作することを確認します。![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## PS サーバーを Azure にインストールする

Azure 内の VM がデータをオンプレミスの MT に戻せるようにするために、プロセス サーバー (PS) を Azure 上にインストールする必要があります。Azure 上の PS は、構成サーバー (CS) と同じネットワーク上にデプロイする必要があります。

1.  Azure の **[構成サーバー]** ページで、新しいプロセス サーバーの追加を選択します。

	![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  プロセス サーバー名を指定し、仮想マシンに管理者として接続するために名前とパスワードを入力します。プロセス サーバーの登録先となる構成サーバーを選択します。これは仮想マシンの保護とフェールオーバーに使用しているサーバーと同じにする必要があります。プロセス サーバーをデプロイする Azure ネットワークを指定します。構成サーバーと同じネットワークにする必要があります。選択したサブネットから一意の IP アドレスを指定し、デプロイメントを開始します。

	![](./media/site-recovery-failback-azure-to-vmware/image10.png)


プロセス サーバーをデプロイするジョブがトリガーされます。

![](./media/site-recovery-failback-azure-to-vmware/image11.png)

プロセス サーバーが Azure にデプロイされたら、指定した資格情報を使用してプロセス サーバーにログインできます。保護の前方向処理時に使用した手順を使用して、PS を登録します。

![](./media/site-recovery-failback-azure-to-vmware/image12.png)

フェールバック中に登録されたサーバーは、VM プロパティの下に表示されません。それらのサーバーは、それらが登録されている構成サーバーの下の [サーバー] タブの下にのみ表示されます。PS が CS の下に一覧表示されるまでには、約 10 ～ 15 分かかります。

## MT サーバーをオンプレミスにインストールする

ソース側の仮想マシンに応じて、オンプレミスに Linux または Windows のマスター ターゲット (MT) サーバーをインストールすることが必要になります。

### Windows MT をデプロイする

Windows MT は、vContinuum セットアップに既にバンドルされています。vContinuum をインストールすると、MT も同じマシンにデプロイされ、構成サーバーに登録されます。

1.  デプロイメントを開始するには、Azure からの VM の復旧先となる、オンプレミスの ESX ホスト上に空のマシンを作成します。

2.  VM に 2 つ以上のディスクがアタッチされていることを確認します。1 つ目は OS 用に使用され、2 つ目はリテンション期間ドライブとして使用されます。

3.  オペレーティング システムをインストールします。

4.  vContinuum をサーバーにインストールします。これにより、MT のインストールも完了します。

### Linux MT をデプロイする

1.  デプロイメントを開始するには、Azure からの VM の復旧先となる、オンプレミスの ESX ホスト上に空のマシンを作成します。

2.  VM に 2 つ以上のディスクがアタッチされていることを確認します。1 つ目は OS 用に使用され、2 つ目はリテンション期間ドライブとして使用されます。

3.  Linux オペレーティング システムをインストールします。Linux マスター ターゲット (MT) システムでは、LVM をルートまたはリテンション期間ストレージ スペースとして使用しないでください。Linux MT は、既定で、LVM パーティション/ディスク検出を回避するように構成されます。
4.  作成できるパーティションを次の表に示します。

	![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  MT のインストールを開始する前に、次のインストール後の手順を実行します。


#### OS インストール後の手順

Linux 仮想マシンの各 SCSI ハード ディスクの SCSI ID を取得するには、パラメーター "disk.EnableUUID = TRUE" を有効にする必要があります。このパラメーターを有効にするには、次の手順を実行します。

1. 仮想マシンをシャットダウンします。
2. 左側のパネルで VM のエントリを右クリックし、**[設定の編集]** を選択します。
3. **[Options]** タブをクリックします。左側で **[Advanced] > [General item]** を選択し、右側に表示される **[Configuration Parameters]** をクリックします。[Configuration Parameters] オプションは、マシンの動作中には無効になります。このタブを有効にするには、マシンをシャットダウンします。

	![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. **[disk.EnableUUID]** と表示される行が存在するかどうかを確認します。 そのような行が存在し、値が False の場合、True の値で上書きします (True と False の値では、大文字小文字は区別されません)。そのような行が存在し、値が True の場合、[Cancel] をクリックし、ゲスト オペレーティング システムが起動した後に、その OS 内で SCSI コマンドをテストします。そのような行が存在しない場合、**[Add Row]** をクリックします。
5. [Name] 列に disk.EnableUUID を追加します。その値を TRUE に設定します。前述の値を追加する際には、二重引用符で囲まないでください。

	![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### その他のパッケージをダウンロードおよびインストールする

注: その他のパッケージをダウンロードおよびインストールする前に、システムがインターネットに接続されていることを確認します。

# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools

このコマンドは、次に示す 15 のパッケージを CentOS 6.6 リポジトリからダウンロードしてインストールします。

bc-1.06.95-1.el6.x86\_64.rpm

busybox-1.15.1-20.el6.x86\_64.rpm

elfutils-libs-0.158-3.2.el6.x86\_64.rpm

kexec-tools-2.0.0-280.el6.x86\_64.rpm

lsscsi-0.23-2.el6.x86\_64.rpm

lzo-2.03-3.1.el6\_5.1.x86\_64.rpm

perl-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-Module-Pluggable-3.90-136.el6\_6.1.x86\_64.rpm

perl-Pod-Escapes-1.04-136.el6\_6.1.x86\_64.rpm

perl-Pod-Simple-3.13-136.el6\_6.1.x86\_64.rpm

perl-libs-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-version-0.77-136.el6\_6.1.x86\_64.rpm

rsync-3.0.6-12.el6.x86\_64.rpm

snappy-1.1.0-1.el6.x86\_64.rpm

wget-1.12-5.el6\_6.1.x86\_64.rpm

注: ソース マシンで、ルートまたはブート デバイスとして Reiser または XFS のファイル システムが使用されている場合、保護する前に、次のパッケージを Linux マスター ターゲットにダウンロードしてインストールする必要があります。

# cd /usr/local

# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm

# wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### カスタム構成変更を適用する

カスタム構成変更を適用する前に、インストール後の手順が完了していることを確認します。

カスタム構成変更を適用するには、次の手順を実行します。

1. RHEL 6-64 ユニファイド・エージェント バイナリを、新たに作成した OS にコピーします。

2. 次のコマンドを実行して、バイナリを解凍します。

**tar -zxvf <File name>**

3. 次のコマンドを実行して、アクセス許可を付与します。

# **chmod 755 ./ApplyCustomChanges.sh**

4. 次のコマンドを実行して、スクリプトを実行します。

**# ./ApplyCustomChanges.sh**

注: スクリプトは、サーバー上で 1 回のみ実行してください。前述のスクリプトが正常に実行されたら、サーバーを**再起動**します。

### MT イントールを開始する

Linux マスター ターゲット サーバー インストール ファイルを[ダウンロード](http://go.microsoft.com/fwlink/?LinkID=529757)します。

お気に入りの sftp クライアント ユーティリティを使用して、ダウンロードした Linux マスター ターゲット サーバー インストーラーを Linux マスター ターゲット仮想マシンにコピーします。別の方法として、Linux マスター ターゲット仮想マシンにログインし、wget を使用して、提供されているリンクからインストール パッケージをダウンロードすることもできます。

お気に入りの ssh クライアントを使用して、Linux マスター ターゲット サーバー仮想マシンにログインします。

Linux マスター ターゲット サーバーをデプロイした Azure ネットワークに VPN 接続経由で接続している場合、仮想マシンのダッシュボードから取得した Linux マスター ターゲット サーバーの内部 IP アドレスとポート 22、および Secure Shell を使用して、Linux マスター ターゲット サーバーに接続します。

パブリック インターネット接続経由で Linux マスター ターゲット サーバーに接続している場合、仮想マシンのダッシュボード ページから取得した Linux マスター ターゲット サーバーのパブリック仮想 IP アドレスと、ssh 用に作成されたパブリック エンドポイントを使用して、Linux マスター ターゲット サーバーにログインします。

gzip 圧縮された Linux マスター ターゲット サーバー インストーラーの tar アーカイブから、ファイルを抽出します。これを行うには、

Linux マスター ターゲット サーバー インストーラーのコピー先のディレクトリから *"tar –xvzf Microsoft-ASR\_UA\_8.2.0.0\_RHEL6-64*"* を実行します。

![](./media/site-recovery-failback-azure-to-vmware/image16.png)

インストーラー ファイルを別のディレクトリに抽出した場合、tar アーカイブのコンテンツの抽出先であるディレクトリに移動します。このディレクトリ パスから、"sudo ./install.sh" を実行します。

![](./media/site-recovery-failback-azure-to-vmware/image17.png)

このエージェントのプライマリ ロールを問い合わせるプロンプトが表示されたら、[2] (マスター ターゲット) を選択します。

その他の対話型インストール オプションについては、既定値のままにします。

インストールの残りの処理が進行し、[Host Config Interface] が表示されるまで待機します。Linux マスター ターゲット サーバー用の Host Configuration ユーティリティは、コマンド ライン ユーティリティです。ssh クライアント ユーティリティ ウィンドウのサイズを変更しないでください。矢印キーを使用して [Global] オプションを選択し、キーボードで Enter キーを押します。

![](./media/site-recovery-failback-azure-to-vmware/image18.png)

![](./media/site-recovery-failback-azure-to-vmware/image19.png)

1.  [IP] フィールドに、仮想マシンのダッシュボード ページから取得した構成サーバーの内部 IP アドレスを入力し、Enter キーを押します。

2.  [Port] フィールドに、「22」と入力し、Enter キーを押します。

3.  [Use https:] は [Yes] のままにします。再び、Enter キーを押します。

4.  構成サーバーで生成されたパスフレーズを入力します。Windows マシンから PuTTY クライアントを使用して、ssh 経由で Linux マスター ターゲット サーバー仮想マシンに接続している場合、Shift キーを押しながら Insert キーを押すことにより、クリップボードのコンテンツを貼り付けることができます。Ctrl キーを押しながら C キーを押してパスフレーズをローカルのクリップボードにコピーし、Shift キーを押しながら Insert キーを押して貼り付けます。Enter キーを押します。

5.  右矢印キーを使用して [quit] を選択し、Enter キーを押します。

インストールが完了するのを待ちます。

![](./media/site-recovery-failback-azure-to-vmware/image20.png)

何らかの理由により、Linux マスター ターゲット サーバーを構成サーバーに登録する処理が失敗した場合、Host Configuration ユーティリティを /usr/local/ASR/Vx/bin/hostconfigcli から再び実行できます (最初に、super ユーザーとして chmod を実行して、このディレクトリにアクセス許可を設定する必要があります)。


#### マスター ターゲット サーバーが構成サーバーに登録されたことを検証する

マスター ターゲット サーバーが構成サーバーに正常に登録されたことを検証するには、[Azure Site Recovery] コンテナーの [構成サーバー] ページの下の [サーバーの詳細] にアクセスします。

注: MT の登録後に、仮想マシンが Azure から削除されている、またはエンドポイントが適切に構成されていないことが原因であると考えられる構成エラーが MT で見つかることがあります。これは、MT が Azure にデプロイされるときに、Azure のエンドポイントによって MT の構成が検出されるためです。ただし、これは、オンプレミスの MT にはあてはまらないため、このエラーは無視できます。これに起因するフェールバックに問題はありません。


## オンプレミスを宛先として仮想マシンの保護を開始する

VM をオンプレミスにフェールバックする前に、まず、オンプレミスを宛先として仮想マシンを保護する必要があります。アプリケーションの VM を保護するには、次の手順を実行します。

### 一時ドライブに関する注意事項

VM は Azure にフェールオーバーするときに、ページ ファイル用に余分な一時ドライブを追加します。フェールオーバーした VM には、通常、既に専用のページ ファイルが割り当てられているので、これは VM によって必要とされない余分なドライブです。

仮想マシンを逆方向に保護する手順を開始する前に、ドライブをオフラインにして、保護されないようにする必要があります。

IPython Notebook を実行するには、

1.  [コンピューターの管理] を開きます (コントロール パネル管理ツールを使用するか、またはエクスプローラー ウィンドウの [コンピューター] を右クリックして [管理] を選択します)。

2.  [ディスクの管理] を選択します。これにより、オンラインおよびマシンにアタッチされているディスクが一覧表示されます。

3.  マシンにアタッチされている一時ディスクを選択し、オフラインにする手順を実行します。

4.  一時ディスクが正常にオフラインになったら、仮想マシンを逆方向に保護する手順を開始できます。

### VM の保護計画

Azure ポータルで仮想マシンの状態をチェックし、フェールオーバーしていることを確認します。

![](./media/site-recovery-failback-azure-to-vmware/image21.png)

注: Azure からオンプレミスへのフェールオーバー時に、Azure VM は物理 VM として扱われます。オンプレミスへのフェールオーバーは、仮想マシンにフェールオーバーします。

1.  お使いのマシンで vContinuum を起動します。

![](./media/site-recovery-failback-azure-to-vmware/image8.png)

2.  **[Choose Application]** 設定で、**[P2V]** を選択します。

3.  **[New Protection]** オプションをクリックして、手順を開始します。

4.  開いた新しいウィンドウで、オンプレミスを宛先として仮想マシンの保護を開始します。

    a.フェールバックする VM に応じて、**[OS type]** を選択し、**[Get Details]** を選択します。

    b.**[Primary server details]** で、保護対象の仮想マシンを識別する必要があります。

    c.仮想マシンは、vCenter または Azure の場合と異なり、コンピューター ホスト名で一覧表示されます。

    d.仮想マシンは、フェールオーバーの前に属していた [vCenter Hostname] の下に一覧表示されます。

    e.保護対象の VM を識別したら、それらを 1 つずつ選択します。

5.  (既に Azure にフェールオーバーしている) 保護対象の仮想マシンを選択すると、仮想マシンの 2 つのエントリがあるポップアップ ウィンドウが表示されます。これは、CS が、登録されている仮想マシンの 2 つのインスタンスを検出しているためです。オンプレミス VM のエントリを削除して、正しい VM を保護できるようにする必要があります。エントリはコンピューター ホスト名を使用して表示されることに注意してください。ここで、正しい Azure VM エントリを識別するには、Azure VM にログインし、C:\\Program Files (x86)\\Microsoft Azure Site Recovery\\Application Data\\etc にアクセスします。ファイル drscout.conf に、Host ID のエントリがあります。vContinuum のダイアログで、VM で見つかったホスト ID を持つエントリを残します。他のエントリはすべて削除します。

![](./media/site-recovery-failback-azure-to-vmware/image22.png)

6.  正しい VM を選択するため、VMの IP アドレスを参照できます。オンプレミスの IP アドレス範囲は、オンプレミスの VM になります。
7.  **[削除]** をクリックしてエントリを削除します。

![](./media/site-recovery-failback-azure-to-vmware/image23.png)

8.  vCenter に移動し、vCenter 上の仮想マシンを停止します。
9.  次に、オンプレミスの仮想マシンを削除することもできます。
10.  次に、VM を保護するために宛先とするオンプレミス MT サーバーを指定します。
11.  これを行うには、フェールバック先の vCenter に接続します。

![](./media/site-recovery-failback-azure-to-vmware/image24.png)

12.  仮想マシンの復旧先となるホストに基づいて、MT サーバーを選択します。

![](./media/site-recovery-failback-azure-to-vmware/image24.png)

13.  次に、各仮想マシンのレプリケーション オプションを設定します。

![](./media/site-recovery-failback-azure-to-vmware/image25.png)

14.  これを行うには、復旧側の**データストア**を選択する必要があります。これは、VM の復旧先となるデータストアです。

VM に応じて、次のオプションを設定します。

**オプション** | **推奨されるオプション値**
---|---
Process Server IP | Azure にデプロイ済みの PS を選択します。
Retention size (in MB)| 
保持するサイズの値 | 1
Days or Hours | Days
一貫性を維持する期間 | 1
Select target datastore | 復旧側で利用可能なデータストア。このデータストアは、十分な容量があり、仮想マシンの実体化先の ESX ホストでも利用できる必要があります。
15.  次に、仮想マシンが、オンプレミス サイトにフェールオーバーした後に取得するプロパティを構成できます。次に示すさまざまなプロパティを構成できます。

![](./media/site-recovery-failback-azure-to-vmware/image26.png)


**プロパティ** | **構成方法**
---|---
Network Configuration|検出された各 NIC について、仮想マシンのフェールバック IP アドレスを構成します。NIC を選択し、**[変更]** をクリックして、IP アドレスの詳細を指定します。
Hardware Configuration|VM 用の CPU とメモリの値を指定できます。この設定を、保護対象のすべての VM に適用することもできます。
Displayname Settings|[CPUs] と [Memory] の正しい値を識別するには、IaaS VM ロール サイズを参照し、割り当てられているコア数とメモリ サイズを確認します。
Displayname Settings|フェールオーバーしてオンプレミスに戻った後で、仮想マシンの名前を変更できます。この名前は、vCenter のインベントリに表示されます。ここで表示される既定値は、仮想マシンのコンピューター ホスト名です。VM 名を識別するには、保護グループの VM リストを参照できます。
NAT Configuration|以降で詳細に説明します。

![](./media/site-recovery-failback-azure-to-vmware/image27.png)



> **PS NAT 設定の選択肢**
>
> 仮想マシンの保護を有効にするには、2 つの通信チャネルを確立する必要があります。
>
> 最初のチャネルは、仮想マシンとプロセス サーバーとの間に確立します。このチャネルは、VM からデータを収集し、PS に送信します。PS は、次に、このデータをマスター ターゲットに送信します。プロセス サーバーと保護対象の仮想マシンが同じ Azure vNet 上に存在する場合、NAT 設定を使用する必要はありません。PS と保護対象の仮想マシンが 2 つの異なる vNet に存在する場合、PS 用の NAT 設定を指定し、最初のオプションを有効にする必要があります。
>
> ![](./media/site-recovery-failback-azure-to-vmware/image28.png)
>
> プロセス サーバーのパブリック IP を識別するには、Azure の PS デプロイメントにアクセスし、そのパブリック IP アドレスを確認します。
>
> 2 つ目のチャネルは、プロセス サーバーとマスター ターゲットとの間に確立します。NAT を使用するかしないかのオプション選択は、MT と PS との間に VPN ベースの接続を使用しているか、またはインターネット経由で保護しているかによって異なります。PS が VPN 経由で MT と通信している場合は、このオプションを選択しないでください。マスター ターゲットがプロセス サーバーとインターネット経由で通信する必要がある場合は、PS 用の NAT 設定を指定します。
>
> ![](./media/site-recovery-failback-azure-to-vmware/image29.png)
>
> プロセス サーバーのパブリック IP を識別するには、Azure の PS デプロイメントにアクセスし、そのパブリック IP アドレスを確認します。
>
> ![](./media/site-recovery-failback-azure-to-vmware/image30.png)

1.  手順 5.d. の指示に従わずにオンプレミス仮想マシンを削除しておらず、手順 7.a. で選択したフェールバック先のデータストアに依然として古い VMDK が残っている場合、フェールバック VM が新しい場所に作成されることも確認する必要があります。このために、[Advanced settings] を選択することにより、[Advanced settings] の **[Folder Name Settings]** セクションでリストア先となる新しい代替フォルダーを指定できます。

![](./media/site-recovery-failback-azure-to-vmware/image31.png)

[Advanced settings] の他のオプションは、既定値のままにしておくことができます。フォルダー名設定がすべてのサーバーに適用されることを確認します。

2.  次に、保護手順の最終段階に進みます。ここで、適合性チェックを実行して、仮想マシンが、オンプレミスを宛先として保護される準備が整っていることを確認する必要があります。

![](./media/site-recovery-failback-azure-to-vmware/image32.png)

	a.  Click on the readiness check and wait for it to complete.

	b.  The Readiness Report tab will give the information if all the
    virtual machines are ready.

	c.  If the Readiness Report is successful on all the virtual machines it
    will allow you to specify a name to the Protection plan

![](./media/site-recovery-failback-azure-to-vmware/image33.png)

	d.  Give the plan a new Name and begin Protect by clicking the button
    below.


3.  これで、保護が始まります。

    a.保護の進行状況を、vContinuum で確認できます。

![](./media/site-recovery-failback-azure-to-vmware/image34.png)

	b.  Once the step **Activating Protection Plan** is completed, you can
    monitor the protection of the virtual machines via the ASR Portal.

	c.  You can also monitor the protection via Azure Site Recovery.

![](./media/site-recovery-failback-azure-to-vmware/image35.png)

仮想マシンをクリックし、[volume replication] セクションでその進捗を監視することにより、ペアの正確なステータスを確認することもできます。

![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## フェールバック計画を準備する

フェールバック計画は vContinuum を使用して準備できるので、アプリケーションをいつでも、オンプレミスにフェールオーバーして戻すことができます。これらの復旧計画は、ASR 復旧計画に非常に似ています。

1.  vContinuum を起動し、**[Manage plans]** のオプションを選択します。

2.  サブオプションとして、**[Recover]** を選択します。

![](./media/site-recovery-failback-azure-to-vmware/image37.png)

3.  仮想マシンを保護するために使用されたことのあるすべての計画が一覧表示されます。これらは、復旧に使用できるのと同じ計画です。

4.  保護計画を選択し、その中で復旧するすべての VM を選択します。

    a.各 VM を選択する際には、ソース VM、VM の復旧先となるターゲット ESX サーバー、およびソース VM ディスクに関する詳細情報を確認できます。

5.  [Next] をクリックして、**Recover** ウィザードを開始します。

6.  復旧する Virtual Machines を選択します。

    a.復旧できるすべての仮想マシンの一覧を確認します。

![](./media/site-recovery-failback-azure-to-vmware/image38.png)

	b.  You can **recover based on** multiple options however we recommend
    the **Latest Tag.** This will ensure that the latest data from the
    virtual machine will be used.

	c.  Select **Apply for All VMs** to ensure that the latest tag will be
    chosen for all the virtual machines.


7.  **[Run Readiness check]** をクリックして、適合性チェックを実行します。 これにより、仮想マシンの最新のタグ復旧が有効になるように正しいパラメーターが構成されているかどうかがわかります。すべてのチェックが正常に終了した場合は、[Next] をクリックします。そうでない場合は、ログを調査してエラーを解決します。

![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  ウィザードの VM 構成手順で、復旧設定が正しく設定されていることを確認します。VM 設定と必要な設定が異なる場合は、それらを変更することを選択できます。保護を行った時点でこのアクションを既に完了しているので、この時点では無視することをお勧めします。

![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9.  最後に、復旧対象の仮想マシンの一覧を確認します。

    a.復旧順序を仮想マシンに指定します。

注: 仮想マシンは、コンピューター ホスト名を使用して一覧表示されます。コンピューター ホスト名を仮想マシンにマップすることが困難な場合があります。名前をマップするには、Azure IaaS の仮想マシン ダッシュボードにアクセスして、仮想マシンのホスト名を確認します。

![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10.  **[Recovery Plan name]** に復旧計画名を入力し、**[Recovery options]** の **[Recover later]** を選択します。

    a.すぐに復旧したい場合は、**[Recovery options]** の **[Recover now]** を選択できます。

    b.保護の初期レプリケーションが完了していない可能性があるので、[Recover later] を選択することをお勧めします。

    c.最後に、**[Recover]** ボタンをクリックして、計画を保存するか、または **[Recovery options]** に基づいて復旧をトリガーします。

11.  復旧のステータス ([Recovery Status]) を確認できます。また、計画が正常に保存されたかどうかも確認できます。

![](./media/site-recovery-failback-azure-to-vmware/image42.png)

12.  [Recover later] を選択した場合、計画が作成され、後で復旧可能であることが通知されます。

![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## Virtual Machines を復旧する

計画を作成したら、仮想マシンを復旧することを選択できます。前提条件として、仮想マシンの同期が完了している必要があります。

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

[Recovery Status] に [OK] が表示されていると、保護が完了し、RPO しきい値が満たされています。レプリケーション ペアの正常性を確認するには、仮想マシンのプロパティにアクセスし、レプリケーションの正常性を確認します。

**復旧を開始する前に、Azure 仮想マシンを停止します。これにより、スプリット ブレインが発生せず、エンド ユーザーに対してアプリケーションの 1 つのコピーによってサービスが提供されます。Azure VM が確実に停止していることを確認できてから、次の手順に進みます。**

仮想マシンを復旧してオンプレミスに戻す処理を開始するには、保存されている計画を開始する必要があります。

1.  vContinuum で、**[Monitor]** を選択して計画を監視します。

2.  ウィザードにより、これまでに実行されたことのある計画が一覧表示されます。

![](./media/site-recovery-failback-azure-to-vmware/image45.png)

3.  **[Recovery]** ノードを選択し、復旧する計画を選択します。

    a.計画がまだ開始されていないことが通知されます。

4.  **[Start]** をクリックして、復旧を開始します。

5.  仮想マシンの復旧を監視できます。


![](./media/site-recovery-failback-azure-to-vmware/image46.png)

6. VM の電源を ON にすると、vCenter の仮想マシンに接続できます。

## フェールバック後に Azure を宛先として再び保護する

フェールバックが完了したら、仮想マシンを再び保護することが必要になる場合があります。次の手順は、保護を再構成するのに役立ちます。

1.  オンプレミスの仮想マシンが動作しており、アプリケーションがエンド ユーザーからアクセスできることを確認します。

2.  Azure Site Recovery ポータルで、仮想マシンを選択して削除します。

    a.仮想マシンの保護を無効にします。これにより、VM が保護されなくなります。

3.  Azure IaaS Virtual Machines にアクセスし、フェールオーバーした VM を削除します。

4.  vSpehere 上の古い VM を削除します。これらは、以前に、Azure にフェールオーバーした VM です。

5.  ASR ポータルで、最近フェールオーバーした仮想マシンを保護します。

6.  VM がいったん保護されると、それらを復旧計画に追加し、継続的に保護できます。


 

<!---HONumber=Oct15_HO2-->