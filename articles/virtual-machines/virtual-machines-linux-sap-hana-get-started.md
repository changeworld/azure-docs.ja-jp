<properties
   pageTitle="Azure VM に SAP HANA を手動でインストールするためのクイック スタート ガイド | Microsoft Azure"
   description="Azure VM に SAP HANA を手動でインストールするためのクイック スタート ガイド"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# Azure VM に単一インスタンスの SAP HANA を手動でインストールするためのクイック スタート ガイド

## はじめに

このクイック スタート ガイドは、SAP NetWeaver 7.5 と SAP HANA SP12 の手動インストールを行って、単一インスタンスの SAP HANA プロトタイプ/デモ システムを Azure VM でセットアップする際に役立ちます。このガイドは、JSON テンプレートを使用する方法を含め、Azure Portal と Powershell/CLI のいずれかを使って仮想マシンまたは仮想ネットワークをデプロイする方法など、Azure IaaS の基本について読者が熟知していることを前提としています。さらに、読者が SAP HANA、SAP NetWeaver、オンプレミスでのそのインストール方法について理解しているとも想定しています。

この記事の末尾の一般情報セクションに記載されている SAP-Azure ドキュメントを参照してください。

非運用環境システムに対する制限があるため、HA、バックアップ、DR、高パフォーマンス、特別なセキュリティ考慮事項などのトピックについては、このガイドでは取り上げません。

サンプルのセットアップは、Azure Resource Manager モデルで SAP NetWeaver 分散インストールを実行できるように 2 つの仮想マシンを使用して行われています。これは、SAP-Linux-Azure がクラシック モデルではサポートされておらず、Azure Resource Manager モデルのみでサポートされているためです。Azure Resource Manager の詳細な情報については、この記事の末尾にある一般情報セクションのリンクをご利用ください。

サンプル インストールに使用された 2 つのテスト VM を次に示します。

* NW 7.5 ASCS インスタンスと PAS をホストする hana-appsrv (タイプ DS3)
* HANA SP12 をホストする hana-dbsrv (タイプ GS4)
* VM はどちらも 1 つの Azure 仮想ネットワーク (azure-hana-test-vnet) に属する
* OS はどちらも SLES 12 SP1


2016 年 7 月時点では、SAP HANA が完全にサポートされているのは Azure VM タイプ GS5 上の OLAP (BW) 運用システムのみであることに注意してください。公式の SAP サポートを想定していないテスト目的の場合は、GS4 などの比較的小規模なタイプを使用してもかまいません。Azure 上の SAP HANA で常に使用する必要があるのは、HANA データ ファイルとログ ファイルのための Azure Premium Storage です。詳しくは、下の「ディスクのセットアップ」セクションを参照してください。Azure でサポートされている SAP 製品の詳細については、この記事の末尾にある一般情報のセクションを確認してください。


このガイドでは、Azure VM に SAP HANA を手動でインストールする 2 とおりの方法について説明します。

* NetWeaver 分散インストールの一環として、"データベース インスタンス" の手順で SAP Software Provisioning Manager (SWPM) を使用して SAP HANA をインストールする
* HANA Life Cycle Manager ツール hdblcm を使用して SAP HANA をインストールし、その後 NetWeaver をインストールする

また、SWPM を使用してすべてのコンポーネント (SAP HANA、SAP アプリケーション サーバー、ASCS インスタンス、SAP GUI) を単一の VM にインストールすることも可能です。この記事ではこのオプションについて説明しませんが、考慮する必要のある事項は同じです。

インストールを開始する前に、Azure テスト VM のセットアップに関する以下のチェックリストに次ぐセクションをお読みください。これにより、既定の Azure VM 構成のみを使用している場合に発生する、いくつかの基本的な間違いを防ぐことができます。


## SAP SWPM を使用した SAP HANA のインストールのチェックリスト

次に示すのは、単一インスタンスの SAP HANA の手動インストールに関係する主な項目の簡単なチェックリストです。これは、SAP NW 7.5 分散インストールを行うための SAP SWPM を使用した、デモまたはプロトタイプの作成を目的とするものです。個々の項目については、記事の中でスクリーンショットを使用してより詳細に説明しています。

* 2 つのテスト VM が含まれる Azure 仮想ネットワークを作成する
* Azure Resource Manager モデルを使用して OS SLES 12 SP1 を備えた 2 つの Azure VM をデプロイする
* 2 つの Standard Storage ディスク (たとえば 75 GB と 500 GB) をアプリ サーバー VM にアタッチする
* 4 つのディスクを HANA DB サーバー VM にアタッチする (アプリ サーバー VM に使用するのと同様の 2 つの Standard Storage ディスクと、2 つの Premium Storage ディスク (たとえば 512 GB x 2))
* サイズやスループットの要件に応じて複数のディスクをアタッチし、lvm または mdadm を使用して VM 内の OS レベルでストライプ ボリュームを作成する
* アタッチされたディスク/論理ボリューム上に XFS ファイル システムを作成する
* 新しい XFS ファイル システムを OS レベルでマウントする。一方のファイルシステムは SAP ソフトウェア全体を保持するために使用します。もう一方のファイルシステムは sapmnt ディレクトリなどに使用するほか、場合によってはバックアップに使用します。SAP HANA DB サーバーの Premium Storage ディスクで XFS ファイルシステムを /hana と /usr/sap としてマウントします。これはすべて、大きさの足りない Linux Azure VM 上のルート ファイルシステムがいっぱいになるのを防ぐために必要です。
* テスト VM のローカル IP アドレスを /etc/hosts に入力する
* /etc/fstab に nofail パラメーターを入力する
* HANA-SLES-12 SAP Note に従ってカーネル パラメーターを設定する (詳しくは下の「カーネル パラメーター」セクションを参照)
* スワップ領域を追加する
* 必要に応じて、テスト VM にグラフィカル デスクトップをインストールする。インストールしない場合、リモート sapinst インストールを使用する
* SAP サービス マーケットプレースで SAP ソフトウェアをダウンロードする
* SAP ASCS インスタンスをアプリ サーバー VM にインストールする
* NFS を使用してテスト VM 間で sapmnt ディレクトリを共有する (アプリ サーバー VM が NFS サーバー)
* SWPM を使用して、HANA が含まれたデータベース インスタンスを DB サーバー VM にインストールする
* アプリ サーバー VM に PAS をインストールする
* SAP MC を起動し、SAP GUI や HANA Studio などを使用して接続する



## hdblcm を使用した SAP HANA インストールのチェックリスト

次に示すのは、単一インスタンスの SAP HANA の手動インストールに関係する主な項目の簡単なチェックリストです。これは、SAP NW 7.5 分散インストールを行うための SAP SWPM を使用した、デモまたはプロトタイプの作成を目的とするものです。個々の項目については、記事の中でスクリーンショットを使用してより詳細に説明しています。

* 2 つのテスト VM が含まれる Azure 仮想ネットワークを作成する
* Azure Resource Manager モデルを使用して OS SLES 12 SP1 を備えた 2 つの Azure VM をデプロイする
* 2 つの Standard Storage ディスク (たとえば 75 GB と 500 GB) をアプリ サーバー VM にアタッチする
* 4 つのディスクを HANA DB サーバー VM にアタッチする (アプリ サーバー VM に使用するのと同様の 2 つの Standard Storage ディスクと、2 つの Premium Storage ディスク (たとえば 512 GB x 2))
* サイズやスループットの要件に応じて複数のディスクをアタッチし、lvm または mdadm を使用して VM 内の OS レベルでストライプ ボリュームを作成する
* アタッチされたディスク/論理ボリューム上に XFS ファイル システムを作成する
* 新しい XFS ファイル システムを OS レベルでマウントする。一方のファイルシステムは SAP ソフトウェア全体を保持するために使用します。もう一方のファイルシステムは sapmnt ディレクトリなどに使用するほか、場合によってはバックアップに使用します。SAP HANA DB サーバーの Premium Storage ディスクで XFS ファイルシステムを /hana と /usr/sap としてマウントします。これはすべて、大きさの足りない Linux Azure VM 上のルート ファイルシステムがいっぱいになるのを防ぐために必要です。
* テスト VM のローカル IP アドレスを /etc/hosts に入力する
* /etc/fstab に nofail パラメーターを入力する
* HANA-SLES-12 SAP Note に従ってカーネル パラメーターを設定する (詳しくは下の「カーネル パラメーター」セクションを参照)
* スワップ領域を追加する
* 必要に応じて、テスト VM にグラフィカル デスクトップをインストールする。インストールしない場合、リモート sapinst インストールを使用する
* SAP サービス マーケットプレースで SAP ソフトウェアをダウンロードする
* HANA DB サーバー VM で、グループ ID が 1001 のグループ "sapsys" を作成する
* hdblcm を使用して SAP HANA を DB サーバー VM にインストールする
* SAP ASCS インスタンスをアプリ サーバー VM にインストールする
* NFS を使用してテスト VM 間で sapmnt ディレクトリを共有する (アプリ サーバー VM が NFS サーバー)
* SWPM を使用して、HANA が含まれたデータベース インスタンスを DB サーバー VM にインストールする
* アプリ サーバー VM に PAS をインストールする
* SAP MC を起動し、SAP GUI や HANA Studio などを使用して接続する




## SAP HANA を手動でインストールするための Azure VM の準備

この章では、SAP HANA を手動でインストールするための Azure VM の準備について取り上げますが、次のトピックについて説明する 5 つのセクションで構成されています。

* ディスクのセットアップ
* カーネル パラメーター
* ファイルシステム
* /etc/hosts
* /etc/fstab


### ディスクのセットアップ

Azure 上の Linux VM のルート ファイルシステムには、サイズの制限があります。このため、SAP を実行するための VM に追加のディスク領域をアタッチする必要があります。純粋なプロトタイプ/デモ環境で SAP アプリ サーバー VM を使用する場合は、Azure Standard Storage ディスクを使ってもかまいません。一方、SAP HANA DB データ ファイルとログ ファイルについては、非運用環境のランドスケープであっても Azure Premium Storage ディスクを使用する必要があります。

Linux VM にディスクをアタッチする方法の詳細については、[こちら](virtual-machines-linux-add-disk.md)を参照してください。

Azure ディスク キャッシュに関しては、HANA トランザクション ログの格納に使用されるディスクでは "None" の設定を使う必要があります。HANA データ ファイルについては、読み取りキャッシュを使用してかまいません。HANA はインメモリ データベースであるため、Azure ディスク レベルの読み取りキャッシュによるパフォーマンス向上 (HANA の起動、ディスクからメモリへのデータの読み取りなど) の度合いは、全体的な使用パターンに依存します。

Azure Premium Storage の詳細については、[こちら](../storage/storage-premium-storage.md)を参照してください。

VM を作成するためのサンプル JSON テンプレートは、[こちら](https://github.com/Azure/azure-quickstart-templates)で探すことができます。"101-vm-simple-linux" を参照すると、100 GB のデータ ディスクが追加されるストレージ セクションなど、基本的なテンプレートがどのようなものかがわかります。

[こちらの記事](virtual-machines-linux-sap-on-suse-quickstart.md)には、Powershell または CLI を使用して SUSE イメージを検索する方法が記載されているほか、UUID を使ってディスクをアタッチする重要性が示されています。


システムのサイズとスループット要件に応じて、1 つではなく複数のディスクをアタッチし、後でこれらのディスクにまたがるストライプ セットを OS レベルで作成する必要があります。複数の Azure ディスクにまたがるストライプ セットを作成する理由には、次の 2 つがあります。

* スループットの向上
* 1 TB より大きい単一のファイルシステムが必要である (2016 年 7 月現在、Azure ディスク サイズの制限が 1 TB であるため)


ストライピングを構成するための 2 つの主要ツールに関する詳細については、次を参照してください。

[mdadm を使用した Azure VM での Linux ソフトウェア RAID の構成に関する記事](virtual-machines-linux-configure-raid.md)

[Linux Azure VM での論理ボリューム マネージャーの構成に関する記事](virtual-machines-linux-configure-lvm.md)



![](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

テスト環境で、2 つの Azure Standard Storage ディスクが SAP アプリ サーバー VM にアタッチされています。1 つのディスクは、インストールで SAP ソフトウェア全体を格納するために使用されます (NetWeaver 7.5、SAP GUI、SAP HANA など)。もう 1 つのディスクは、(バックアップ、テスト データなどの) 何らかの理由で必要となる場合に備えて十分な領域を確保しておくために使用されます。また、同じ SAP ランドスケープに属するすべての VM 間で共有される (SAP プロファイルなどの) sapmnt ディレクトリに十分な領域を確保するために使用されます。

![](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

アプリ サーバー VM とは異なり、SAP HANA サーバー VM には 4 つのディスクがアタッチされています。前の場合と同様に、2 つのディスクは、SAP ソフトウェアの保持 (SAP ソフトウェア ディスクは NFS で共有することも可能) と、バックアップなどに備えた十分な領域の確保に使用されます。その他の 2 つのディスクは、SAP HANA データ ファイルとログ ファイルのほか、/usr/sap ディレクトリが保持される Azure Premium Storage ディスクです。


### カーネル パラメーター


SAP HANA では、標準的な Azure ギャラリー イメージに含まれていない、手動で設定する必要のある特定の Linux カーネル設定が必要です。この設定について説明されている特定の SAP Note があります。


SAP Note SAP HANA DB: SLES 12/SLES for SAP Applications 12 で推奨される OS 設定: [SAP Note 2205917](https://launchpad.support.sap.com/#/notes/2205917)

このほか、SLES 上の SAP HANA の実行に関係するページキャッシュについてのトピックは、[こちら](https://www.suse.com/documentation/sles_for_sap/singlehtml/sles_for_sap_guide/sles_for_sap_guide.html#sec.s4s.configure.page-cache)の第 6 章第 1 節「Kernel: Page-Cache Limit (カーネル: ページキャッシュの制限)」にあります。

また、ページキャッシュの制限に関する SAP Note もあります ([SAP Note 1557506](https://service.sap.com/sap/support/notes/1557506))。

SLES 12 には、かつての sapconf ユーティリティに代わる新しいツールが用意されています。これは "tuned-adm" というツールで、特殊な SAP HANA プロファイルを利用できます。このツールの詳細については、次の 2 つのリンクをご利用ください。

tuned-adm profile sap-hana についての SLES ドキュメントは、[こちら](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html)から参照できます。

tuned-adm profile sap-hana についての SLES ドキュメント (第6 章第 2 節「Tuning Systems for SAP Workloads with tuned-adm (tuned-adm を使用した SAP ワークロード用のシステムの調整)」) は、[こちら](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)から参照できます。


![](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

ここでは、必要とされる SAP HANA の設定に応じて transparent\_hugepage と numa\_balancing の値が "tuned-adm" によって変更されているようすがわかります。


SAP HANA カーネル設定を永続的な設定にするには、SLES 12 で grub2 を使用する必要があります。grub2 の詳細な情報については、[こちら](https://www.suse.com/documentation/sled-12/book_sle_admin/data/sec_grub2_file_structure.html)を参照してください。


![](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

このスクリーンショットでは、構成ファイルでカーネル設定が変更されており、grub2-mkconfig によって "コンパイル" されていることがわかります。


![](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

このほか、YaST で設定を変更し、ブート ローダーのカーネル パラメーター設定を変更するという方法もあります。


### ファイルシステム 

![](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

ここでは、SAP アプリ サーバー VM にアタッチされた 2 つの Azure Standard Storage ディスク上に 2 つのファイル システムが作成されていることがわかります。どちらのファイルシステムの種類も XFS であり、/sapdata と /sapsoftware にマウントされています。

この方法は必須ではありません。ディスク領域を構成する方法には、さまざまな選択肢があります。最も重要な点は、ルート ファイルシステムの領域不足を回避することです。


![](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

SAP HANA DB VM に関して重要なのは、sapinst (swpm) によるデータベース インストールのほか、単純で "一般的な" インストール オプションのみを使用したデータベース インストールのときに、既定では /hana と /usr/sap にデータがインストールされるという点を理解することです。既定の設定では、たとえば SAP HANA ログ バックアップは /usr/sap に格納されます。先ほどと同様に、ルート ファイルシステムの領域不足を回避することが重要です。このため、swpm で SAP HANA をインストールする前に、/hana と /usr/sap の空き領域を十分に確保しておく必要があります。

SAP の[この記事](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)では、SAP HANA の標準的なファイルシステム レイアウトについて説明されています。


![](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)

標準的な SLES 12 Azure ギャラリー イメージに SAP NetWeaver をインストールすると、スワップ領域がないことを示すメッセージが表示されます。このメッセージが表示されないようにするには、たとえば次のドキュメントで説明されているように、dd、mkswap、swapon を使用してスワップ ファイルを手動で追加します。[こちらの記事](https://www.suse.com/documentation/sled-12/book_sle_deployment/data/sec_yast2_i_y2_part_expert.html)で「Adding a Swap File Manually (スワップ ファイルの手動追加)」を探してみてください。

別の方法としては、Linux VM エージェントを使用したスワップ領域の構成があります。詳細については、[こちら](virtual-machines-linux-agent-user-guide.md)を参照してください。


### /etc/hosts

![](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

SAP のインストールを開始する前に、もう 1 つ重要な点があります。それは、SAP VM のホスト名と IP アドレスを /etc/hosts ファイルに含めておくことです。1 つの Azure 仮想ネットワーク内ですべての SAP VM をデプロイし、内部 IP アドレスを使用する必要があります。

### /etc/fstab

![](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

テスト フェーズ中に、nofail パラメーターを fstab に追加すると良いということがわかっています。ディスクに何らかの問題がある場合でも VM は起動し、起動プロセス中に停止することはありません。ただし、このケースでは追加のディスク領域が利用できない場合があり、プロセスによってルート ファイルシステムがいっぱいになる可能性があるため、注意が必要です。/hana がない場合、SAP HANA はまったく起動しません。


## SLES 12 への Gnome グラフィカル デスクトップのインストール

この章は、次のトピックに関する 2 つのセクションで構成されています。

* SLES 12 への Gnome デスクトップと xrdp のインストール
* SLES 12 での Firefox を使用した Java ベースの SAP MC の実行

Xterminal、VNC などを代わりに使用することもできますが、2016 年 9 月の時点では xrdp についてのみこの記事で説明します。

### SLES 12 への Gnome デスクトップと xrdp のインストール

Microsoft Windows のバックグラウンドを持つユーザーが、SAP Linux VM 内で直接グラフィカル デスクトップを使用して Firefox、Sapinst、SAP GUI、SAP MC または HANA Studio を実行したり、場合によっては Microsoft Windows コンピューターから RDP で VM に接続したりすることがあります。特にこの場合に、簡単に実現する方法があります。この方法は、運用データベース サーバーなどには適していない場合もありますが、純粋なプロトタイプ/デモ環境で使用するには問題ありません。Azure SLES 12 VM に Gnome デスクトップをインストールする手順は、次のとおりです。

(PuTTY ウィンドウなどで) 次のコマンドを使って Gnome デスクトップをインストールします。

   zypper in -t pattern gnome-basic

次に、RDP を使用して VM に接続できるように xrdp をインストールします。

   zypper in xrdp

/etc/sysconfig/windowmanager を編集し、既定のウィンドウ マネージャーを Gnome に設定します。

   DEFAULT\_WM="gnome"

再起動後に xrdp が自動的に起動するように、chkconfig を実行します。

  chkconfig -level 3 xrdp on

RDP 接続に関する問題がある場合は (おそらく PuTTY ウィンドウから) 再起動を試みます。

  /etc/xrdp/xrdp.sh restart

上記の xrdp の再起動がうまくいかない場合、.pid ファイルがないかチェックし、削除します。

  /var/run を確認して xrdp.pid を探し、それを削除してからもう一度再起動を試みます。



### SAP MC


前のセクションで説明したとおりに Gnome デスクトップをインストールした後、Azure SLES 12 VM で実行されている Firefox から Java ベースのグラフィカル SAP MC を起動します。ここで、Java ブラウザー プラグインがないためエラーが発生します。

SAP MC を開始するための URL は、<server>:5<instance\_number>13 です。

詳細については、[こちら](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)を参照してください。


![](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

上記のスクリーンショットでは、Java ブラウザー プラグインがない場合にどのようなエラー メッセージが表示されるかがわかります。

![](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

この問題を解決する方法の 1 つに、単純に不足しているプラグインを YaST でインストールする方法があります。

![](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

初めてプラグインのアクティブ化を要求する場合、SAP MC URL を繰り返すと小さなダイアログが表示されます。


発生する可能性があるもう 1 つの問題は、javafx.properties ファイルが存在しないことに関するエラー メッセージです。これは、SAP GUI 7.4 の場合に必要な Java 1.8 のインストールに関連している可能性が非常に高い問題です。

YaST で見られる IBM Java バージョンには、このファイルは含まれていません。解決策は、Oracle から Java をダウンロードすることです。この特定の問題について説明している記事は、[こちら](https://scn.sap.com/thread/3908306)にあります。



## NetWeaver 7.5 インストールの一環として実行する、SWPM を使用した SAP HANA の手動インストール


次の一連のスクリーンショットでは、SWPM (sapinst) を使って SAP NetWeaver 7.5 と SAP HANA SP12 をインストールする主な手順について説明しています。SWPM には、NW 7.5 インストールの一環として HANA データベースを単一インスタンスとしてインストールする機能も含まれています。


![](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

サンプルのテスト環境では、ABAP アプリ サーバーが 1 つだけインストールされています。"Distributed System" オプションは、ASCS インスタンスとプライマリ アプリ サーバー インスタンスを 1 つの Azure VM にインストールし、SAP HANA をデータベース システムとしてもう 1 つの Azure VM にインストールするために使用されています。


![](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

ASCS インスタンスがアプリ サーバー VM にインストールされ、SAP MC で "緑色" に変わったら、SAP プロファイル ディレクトリなどが含まれた sapmnt ディレクトリを SAP HANA DB サーバー VM と共有させる必要があります。DB のインストール手順では、この情報へのアクセスが必要になります。最善の方法は、YaST を使って構成できる NFS を使用することです。


![](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

アプリ サーバー VM で、"rw" オプションと "no\_root\_squash" オプションを使用して NFS で sapmnt ディレクトリを共有する必要があります。既定では "ro" と "root\_squash" になっており、データベース インスタンスをインストールする際に問題が生じる可能性があります。


![](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

SAP HANA DB サーバー VM で、(YaST などを利用して) "NFS クライアント" を使ってアプリ サーバー VM からの sapmnt 共有を構成する必要があります。


![](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

その後、SAP HANA DB サーバー VM にログインして SWPM を起動し、NW 7.5 分散インストールの次の手順 "データベース インスタンス" を完了する必要があります。


![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

SAP HANA インストールでは、"一般的な" インストールを選択した後に入力する項目はそれほど多くありません。インストール メディアへのパスのほか、DB SID、ホスト名、インスタンス番号、DB システム管理者のパスワードを入力する必要があります。

 
![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

次に、DBACOCKPIT スキーマのパスワードを入力します。



![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

さらに、SAPABAP1 スキーマのパスワードを入力します。


![](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

最後に、DB インストール プロセスの全フェーズの前に緑色のチェックマークが付いている必要があります。また、表示される小さなメッセージ ボックスに、"Execution of .....Database Instance has completed (.....データベース インスタンスの実行は完了しました)" と示されている必要があります。

 
![](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

インストールの成功後、SAP MC では DB インスタンスが "緑色" で表示され、SAP HANA プロセス (hdbindexserver、hdbcompileserver など) の完全な一覧が表示されます。


![](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

このスクリーンショットでは、HANA のインストール中に SWPM によって作成された /hana/shared のファイル構造が部分的に示されています。異なるパスを指定する方法はありませんでした。このため、SWPM を使用した SAP HANA インストールの前に、追加のディスク領域を /hana にマウントすることが重要です。そうすることで、ルート ファイルシステムの領域不足を回避できます。


![](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

ここでは /usr/sap ディレクトリについて、前述したのと同様のものが表示されています。


![](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

ABAP 分散インストールの最後の手順は、"プライマリ アプリケーション サーバー インスタンス" です。


![](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

PAS と SAP GUI のインストールが完了すると、"dbacockpit" トランザクションを使用して、SAP HANA のインストールに問題がないことを確認できます。

 
![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

最後の手順として、SAP HANA Studio を SAP アプリ サーバー VM にインストールし、DB サーバー VM で実行されている SAP HANA インスタンスに接続できます。




## HANA Life Cycle Manager ツール hdblcm を使用した SAP HANA の手動インストール


SWPM を使用した分散インストールの一部として SAP HANA をインストールする以外にも、まず hdblcm を使って HANA を単独でインストールし、その後 SAP NetWeaver 7.5 などをインストールすることもできます。次の一連のスクリーンショットでは、この方法について説明しています。

HANA hdblcm ツールに関する 3 つの情報源を次に示します。

[タスクに適した SAP HANA HDBLCM の選択](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)

[SAP HANA ライフサイクル管理ツール](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)

[SAP HANA サーバーのインストールと更新のガイド](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)



![](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

(hdblcm ツールによって作成される) <HANA SID>adm ユーザーの既定のグループ ID 設定に関する問題が後で発生するのを回避するために、hdblcm を使って SAP HANA をインストールする前に、グループ ID を 1001 にして "sapsys" という新しいグループを定義する必要があります。




![](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

hdblcm を初めて開始すると、単純なスタート メニューが表示されます。ここで、項目 1 の [Install new System (新しいシステムをインストールする)] を選択します。



![](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

このスクリーンショットには、前に入力した主なオプションがすべて示されています。重要 - HANA のログとデータ ボリューム用に名前が付けられたディレクトリのほか、インストール パス (このサンプルでは /hana/shared) と /usr/sap は、ルート ファイルシステムに含めないようにします。Azure VM のセットアップのセクションで説明したように、VM にアタッチされた Azure データ ディスクに含める必要があります。これにより、ルート ファイルシステムの領域不足が発生するリスクを回避できます。また、HANA 管理者ユーザーのユーザー ID が 1005 であり、インストール前に定義した sapsys グループ (ID 1001) に属していることがわかります。



![](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

/etc/passwd で、HANA <HANA SID>adm (このサンプルでは azdadm) ユーザーの詳細を確認できます。



![](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

hdblcm を使用して SAP HANA をインストールした後、これは SAP HANA Studio で表示できます。すべての SAP NetWeaver テーブルなどが含まれる SAPABAP1 スキーマは、まだ使用できません。



![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

SAP HANA をインストールしたら、その SAP HANA に SAP NetWeaver をインストールできます。このサンプルでは、対応する上記のセクションで説明したように、SWPM で "分散インストール" を使用して行われています。SWPM を使用してデータベース インスタンスをインストールする際に、hdblcm で先ほど使用したのと同じデータを入力する必要があります (ホスト名、HANA SID、インスタンス番号など)。その後、SWPM によって既存の HANA インストールが使用され、スキーマが追加されます。



![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

これは、SWPM のインストール手順を示した画像です。ここで、DBACOCKPIT スキーマに関するデータを入力する必要があります。


![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

その後、SWPM で SAPABAP1 スキーマに関するデータを入力する必要があります。


![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

SWPM データベース インスタンスのインストールが完了したら、HANA Studio で SAPABAP1 スキーマを確認できます。



![](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

SAP アプリ サーバーと SAP GUI のインストール後、最後に "dbacockpit" トランザクションを使用して、HANA DB インスタンスを確認することができます。




## SAP Azure 認定、Azure 上の SAP HANA の実行、SAP ソフトウェア ダウンロードに関する一般情報

* Windows OS を備えた Azure (クラシック モード) での SAP の実行に関する一般的な SAP Azure ドキュメント: [Azure での Windows 仮想マシンにおける SAP の使用](virtual-machines-windows-classic-sap-get-started.md)

* ユーザーが使用できる既存の SAP テンプレートについての情報: [SAP 用の Azure クイック スタート テンプレート](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)

* Linux OS を備えた Azure (Azure Resource Manager モデル) での SAP の実行に関する一般的な SAP Azure ドキュメント: [Linux 仮想マシン (VM) における SAP の使用](virtual-machines-linux-sap-get-started.md)

* 運用環境でサポートされている Azure VM の種類の一覧が記載された認定済み SAP HANA ハードウェア ディレクトリ: [認定済み SAP HANA® ハードウェア ディレクトリ](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)

* 特に Linux ワークロード用の仮想マシン サイズについての情報: [Azure の仮想マシンのサイズ](virtual-machines-linux-sizes.md)

* Azure でサポートされているすべての SAP 製品の一覧と、SAP でサポートされている Azure VM の種類の一覧が記載された SAP Note: [SAP Note 1928533](https://launchpad.support.sap.com/#/notes/1928533/E)

* Azure 上の Linux VM を使用した SAP の "拡張された監視機能" についての SAP Note: [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)

* Azure の "L インスタンス" での SAP HANA サービス。これは、Azure VM 上での SAP HANA の実行に関するものではありません。SAP アプリ サーバーが Azure VM で実行され、SAP HANA がベアメタル サーバーで実行されるハイブリッド環境での SAP HANA の実行に関するものであることに注意してください: [SAP Note 2316233](https://launchpad.support.sap.com/#/notes/2316233/E)

* Linux での SAPOSCOL に関する情報が記載された SAP Note: [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)

* Microsoft Azure 上の SAP 向けの主要な監視メトリック: [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)

* Azure Resource Manager についての情報: [Azure Resource Manager の概要](../resource-group-overview.md)

* テンプレートを使用した Linux VM のデプロイに関する情報: [Azure Resource Manager テンプレートと Azure CLI を使用した仮想マシンのデプロイと管理](virtual-machines-linux-cli-deploy-templates.md)

* Azure Resource Manager デプロイメント モデルとクラシック デプロイメント モデルの比較: [Azure Resource Manager とクラシック デプロイメント: デプロイメント モデルとリソースの状態について](../resource-manager-deployment-model.md)

* SAP サービス マーケットプレースからの Linux/HANA 用の NetWeaver 7.5 のダウンロード: ![](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)

* SAP サービス マーケットプレースからの HANA SP12 Platform Edition のダウンロード: ![](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)

<!---HONumber=AcomDC_0921_2016-->