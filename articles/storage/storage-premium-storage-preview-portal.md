<properties
	pageTitle="Premium Storage: Azure の仮想マシン ワークロード用の高パフォーマンス ストレージ| Microsoft Azure"
	description="Premium Storage は、Azure Virtual Machines で実行される高負荷の I/O ワークロードのための、高パフォーマンスで待ち時間の少ないディスク サポートを提供します。Azure DS シリーズおよび GS シリーズの VM は、Premium Storage をサポートしています。"
	services="storage"
	documentationCenter=""
	authors="ms-prkhad"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/04/2015"
	ms.author="robinsh;selcint"/>


# Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ

## 概要

Azure Premium Storage は、高負荷の I/O ワークロードを実行する仮想マシン向けに高パフォーマンスで待ち時間の少ないディスク サポートを提供します。Premium Storage を使用する仮想マシン (VM) では、ソリッド ステート ドライブ (SSD) にデータを格納します。アプリケーションの VM ディスクを Azure Premium Storage に移行することで、これらのディスクの速度とパフォーマンスを最大限に利用することができます。

Azure VM では複数の Premium Storage ディスクをアタッチすることができるので、アプリケーションには VM あたり最大で 64 TB のストレージを設定することができます。Premium Storage を使用すると、アプリケーションは、VM あたり 80,000 IOPS (1 秒あたりの入力/出力操作) と、VM あたり 2000 MB/秒のディスク スループットを実現でき、読み取り操作の待ち時間も非常に短くなります。

>[AZURE.NOTE]高い IOPS を必要とする仮想マシンのディスクは Azure Premium Storage に移行して、アプリケーションが最高のパフォーマンスを発揮できるようにすることをお勧めします。ディスクが高い IOPS を必要としない場合は、ディスクを Standard Storage 内に保持することでコストを抑えることができます。Standard Storage の場合、仮想マシンのディスク データは SSD ではなくハード ディスク ドライブ (HDD) に格納されます。

Azure Premium Storage を使用するには、[無料試用版](http://azure.microsoft.com/pricing/free-trial/)のページをご覧ください。既存の仮想マシンを Premium Storage に移行する方法の詳細については、「[Azure Premium Storage への移行](storage-migration-to-premium-storage.md)」を参照してください。

## Premium Storage について理解しておくべきこと

次は、Premium Storage の使用前や使用時に考慮すべき重要な事柄の一覧です。

- Premium Storage を使用するには、Premium Storage アカウントが必要です。Premium Storage アカウントの作成方法については、[ディスク向け Premium Storage アカウントの作成と使用](#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)に関するセクションをご覧ください。

- Premium Storage は [Azure ポータル](portal.azure.com)で利用できます。Premium Storage には、[Storage REST API](http://msdn.microsoft.com//library/azure/dd179355.aspx) バージョン 2014-02-14 以降、[Service Management REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx) バージョン 2014-10-01 以降、[Azure PowerShell](../install-configure-powershell.md) バージョン 0.8.10 以降の SDK ライブラリからアクセスできます。

- 現在 Premium Storage をサポートするリージョンの一覧については、「[リージョン別の Azure サービス](http://azure.microsoft.com/regions/#services)」を参照してください。

- Premium Storage は Azure ページ BLOB のみをサポートしています。これは、Azure Virtual Machines (VM) の永続ディスクを保持するために使用されます。Azure ページ BLOB については、[ブロック BLOB とページ BLOB](http://msdn.microsoft.com/library/azure/ee691964.aspx)に関するページをご覧ください。Premium Storage は Azure ブロック BLOB、Azure ファイル、Azure テーブル、Azure キューをサポートしていません。

- Premium Storage アカウントローカル冗長 (LRS) であり、1 つのリージョン内にデータのコピーを 3 つ保持します。Premium Storage を使用するときの geo レプリケーションに関する考慮事項については、この記事の「[Premium Storage を使用するときのスナップショットと Copy BLOB](#snapshots-and-copy-blob-whja-JPing-premium-storage)」をご覧ください。

- VM ディスクで Premium Storage アカウントを使用する場合は、DS シリーズまたは GS シリーズの VM を使用する必要があります。DS シリーズまたは GS シリーズの VM では、Standard Storage ディスクと Premium Storage ディスクの両方を使用できます。DS シリーズや GS シリーズ以外の VM では Premium Storage ディスクを使用できません。使用可能な Azure VM ディスクの種類とサイズについては、「[Azure の仮想マシンおよびクラウド サービスのサイズ](../virtual-machines/virtual-machines-size-specs.md)」をご覧ください。

- VM 用の Premium Storage ディスクの設定方法は、Standard Storage ディスクの場合と同じです。使用する Azure ディスクと VM に最適な Premium Storage オプションを選択する必要があります。VM のサイズは、Premium 製品のパフォーマンス特性に基づき、ワークロードに最適なものにします。詳細については、「[Premium Storage を使用するときの拡張性とパフォーマンスのターゲット](#scalability-and-performance-targets-whja-JPing-premium-storage)」をご覧ください。

- Premium Storage のアカウントをカスタム ドメイン名にマッピングすることはできません。

- 現在、Premium Storage では、Storage Analytics はサポートされていません。Premium Storage アカウントのディスクを使用する VM のパフォーマンス メトリックを分析するには、オペレーティング システム ベースのツールを使用します。たとえば、Windows VM の場合は [Windows パフォーマンス モニター](https://technet.microsoft.com/library/cc749249.aspx)、Linux VM の場合は [IOSTAT](http://linux.die.net/man/1/iostat) を使用します。Azure プレビュー ポータルで Azure VM 診断を有効にすることもできます。詳細については、「[Microsoft Azure Virtual Machine Monitoring with Azure Diagnostics Extension (Azure 診断の拡張機能を使用した Microsoft Azure の仮想マシンの監視)](http://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/)」をご覧ください。

## ディスク向け Premium Storage の使用
ディスク向け Premium Storage は次のいずれかの方法で使用できます。

- 最初に新しい Premium Storage アカウントを作成し、VM の作成時にそのアカウントを使用します。
- 新しい DS シリーズまたは GS シリーズの VM を作成します。VM の作成時には、以前に作成した Premium Storage アカウントを選択する、新しいアカウントを作成する、Azure ポータルで自動的に既定の Premium アカウントを作成する、のいずれかの方法を選択できます。

ストレージ アカウントは、オペレーティング システム (OS) とデータ ディスクのコンテナーとして使用されます。つまり、Azure DS シリーズまたは GS シリーズの VM を作成して Azure Premium Storage アカウントを選択すると、オペレーティング システムとデータ ディスクはそのストレージ アカウントに格納されます。

既存の仮想マシンを Premium Storage に移行する方法の詳細については、「[Azure Premium Storage への移行](storage-migration-to-premium-storage.md)」を参照してください。

Premium Storage のメリットを活用するには、アカウントの種類として *Premium\_LRS* を使用して、Premium Storage アカウントを作成します。アカウントの作成には、[Azure ポータル](portal.azure.com)、[Azure PowerShell](../install-configure-powershell.md)、または [Service Management REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx) を使用できます。詳しい手順については、[ディスク向け Premium Storage アカウントの作成と使用](#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)に関するセクションをご覧ください。

### 重要:

- Premium Storage アカウントの容量と帯域幅の制限の詳細については、「[Premium Storage を使用するときの拡張性とパフォーマンスのターゲット](#scalability-and-performance-targets-whja-JPing-premium-storage)」をご覧ください。アプリケーションのニーズが単一のストレージ アカウントの拡張性ターゲットを上回った場合は、複数のストレージ アカウントを使用するようにアプリケーションを構築し、それらのストレージ アカウント間でデータを分割します。たとえば、多数の VM があり、アタッチするディスクの合計が 51 テラバイト (TB) の場合は、ディスクを 2 つのストレージ アカウントに分散させます。Premium Storage アカウント 1 つあたりの上限は 35 TB であるからです。1 つの Premium Storage アカウントでプロビジョニングするディスクの容量が 35 TB を超えることがないようにしてください。
- 既定では、ディスクのキャッシュ ポリシーは、すべてのPremium データ ディスクに対して「読み取り専用」、VM にアタッチされた Premium オペレーティング システム ディスクに対して「読み取り/書き込み」です。アプリケーションの I/O のパフォーマンスを最適化するには、この構成をお勧めします。書き込み量の多いディスクや書き込み専用のディスク (SQL Server ログ ファイルなど) の場合は、ディスク キャッシュを無効にすることで、アプリケーションのパフォーマンスを向上できる場合があります。
- ディスク トラフィックが流れるのに十分な帯域幅が VM で利用できることを確認します。たとえば、STANDARD\_DS1 VM では、Premium Storage ディスク トラフィック専用の帯域幅は 32 MB/秒です。つまり、この VM にアタッチされている Premium Storage ディスクが P10 の場合も、32 MB/秒が上限となり、P10 ディスクのスループットの上限である 100 MB に達することはありません。同様に、STANDARD\_DS13 VM 1 つあたりの全ディスクの帯域幅は最大 256 MB/秒です。現時点では、DS シリーズ最大の VM は STANDARD\_DS14 であり、この VM の全ディスクの帯域幅は最大 512 MB/秒です。現時点では、DS シリーズ最大の VM は STANDARD\_GS5 であり、この VM の全ディスクの帯域幅は最大 2000 MB/秒です。

	これらの制限は、ディスク トラフィックのみを対象としており、キャッシュ ヒットとネットワーク トラフィックは対象外となります。VM のネットワーク トラフィックの帯域幅は別に用意されており、Premium Storage ディスク専用の帯域幅とは区別されています。
	
	DS シリーズと GS シリーズの VM の最大 IOPS、およびスループット (帯域幅) の最新の情報は次を参照してください。[仮想マシンと Azure のクラウド サービス サイズ](../virtual-machines/virtual-machines-size-specs.md)Premium Storage のディスクそれぞれの IOPS とスループット上限については、この記事の「[Premium Storage を使用するときの拡張性とパフォーマンスのターゲット](#scalability-and-performance-targets-whja-JPing-premium-storage)」のセクションにある表を参照してください。

> [AZURE.NOTE]キャッシュ ヒットには、ディスクの割り当て済みの IOPS とスループットによる制限はありません。つまり、DS シリーズ VM または GS シリーズ VM でキャッシュを読み取り専用に設定したデータ ディスクを使用する場合、そのキャッシュからの読み取りは、Premium Storage のディスク制限の対象にはなりません。そのため、ワークロードの大部分が読み取りの場合、ディスクから非常に高いスループットが得られます。キャッシュには、VM サイズに基づいて、VM レベルで IOPS とスループットの個別の制限が適用されます。DS シリーズ VM は、IOPS が約 4000 で、キャッシュとローカル SSD の IO がコアあたり 33 MB/秒です。

- 同じ DS シリーズの VM または GS シリーズの VM 内では、Premium Storage ディスクと Standard Storage ディスクの両方を使用できます。
- Premium Storage を使用すると、DS シリーズの VM をプロビジョニングし、複数の永続データ ディスクをVM に接続できます。必要に応じて、ディスク全体をストライピングして容量を増やし、ボリュームのパフォーマンスを高めることができます。[記憶域スペース](http://technet.microsoft.com/library/hh831739.aspx)を使用して Premium Storage データ ディスクをストライピングする場合は、使用するディスクごとに 1 つの列で構成する必要があります。そうしない場合は、ディスク全体のトラフィックの配分が不均等になるため、ストライプ ボリュームの全体的なパフォーマンスが低下する可能性があります。既定では、サーバー マネージャー ユーザー インターフェイス (UI) で最大 8 つのディスクの列を設定できます。ただし、8 つ以上のディスクがある場合は、PowerShell を使用してボリュームを作成し、列の数を手動で指定する必要があります。そうしない場合、サーバー マネージャー UI はそれ以上のディスクがある場合でも 8 つの列を使用し続けます。たとえば、1 つのストライプ セット内に 32 のディスクがある場合は、32 の列を指定する必要があります。PowerShell の [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) コマンドレットの *NumberOfColumns* パラメーターを使用して、仮想ディスクが使用する列数を指定できます。詳細については、[記憶域スペースの概要](http://technet.microsoft.com/library/jj822938.aspx)に関するページおよび「[Storage Spaces Frequently Asked Questions (記憶域スペースに関してよく寄せられる質問)](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)」をご覧ください。
- DS シリーズ以外の VM が存在する既存のクラウド サービスに DS シリーズの VM を追加することは避けてください。回避策の 1 つは、既存の VHD を新しいクラウド サービスに移行し、このクラウドでは DS シリーズ VM だけを運用するというものです。DS シリーズ VM をホストする新しいクラウド サービスに引き続き同じ仮想 IP アドレス (VIP) を使用するには、[予約済み IP アドレス](virtual-networks-configure-vnet-to-vnet-connection.md)機能を使います。GS シリーズの VM は G シリーズの VM のみ実行している既存のクラウド サービスに追加できます。
- DS シリーズの Azure 仮想マシンで使用するオペレーティング システム (OS) ディスクは、Standard Storage アカウントと Premium Storage アカウントのどちらでホストするように設定されていてもかまいません。OS ディスクをマシンの起動のみに使用する場合は、Standard Storage ベースの OS ディスクの使用を検討してください。コストの点で有利であり、起動後のパフォーマンスは Premium Storage とほぼ同じであるからです。マシン起動以外のタスクも OS ディスクで実行する場合は、Premium Storage を使用すると、より高いパフォーマンスが得られます。たとえば、アプリケーションで OS ディスクの読み取りと書き込みを行う場合は、Premium Storage ベースの OS ディスクを使うと VM のパフォーマンスが向上します。
- Premium Storage で [Azure コマンド ライン インターフェイス (Azure CLI)](../xplat-cli-install.md) を使用できます。Azure CLI を使用して、いずれかのディスクのキャッシュ ポリシーを変更するには、次のコマンドを実行します。

	`$ azure vm disk attach -h ReadOnly <VM-Name> <Disk-Name>`

	キャッシュ ポリシーのオプションは、ReadOnly、None、ReadWrite があります。その他のオプションについては、次のコマンドを実行してヘルプを参照してください。

	`azure vm disk attach --help`


## Premium Storage を使用するときの拡張性とパフォーマンスのターゲット

Premium Storage アカウントにディスクをプロビジョニングする場合、アカウントが利用可能な 1 秒あたりに処理できる入力/出力 (IOPS) の量やスループット (帯域幅) は、ディスクのサイズによって異なります。現在、Premium Storage ディスクには、P10、P20、P30 の 3 種類があります。次の表に示すように、それぞれに IOPS とスループットに固有の制限があります。

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Premium Storage ディスク タイプ</strong></td>
	<td><strong>P10</strong></td>
	<td><strong>P20</strong></td>
	<td><strong>P30</strong></td>
</tr>
<tr>
	<td><strong>ディスク サイズ</strong></td>
	<td>128 GiB</td>
	<td>512 GiB</td>
	<td>1024 GiB (1 TB)</td>
</tr>
<tr>
	<td><strong>ディスクあたりの IOPS</strong></td>
	<td>500</td>
	<td>2300</td>
	<td>5000</td>
</tr>
<tr>
	<td><strong>ディスクあたりのスループット</strong></td>
	<td>100 MB/秒 *</td>
	<td>150 MB/秒 *</td>
	<td>200 MB/秒 *</td>
</tr>
</tbody>
</table>

> [AZURE.NOTE]この記事の「[ディスク向け Premium Storage の使用](#using-premium-storage-for-disks)」で説明したように、VM の帯域幅が、ディスク トラフィックを処理するのに十分な大きさであることを確認してください。このとおりでない場合は、ディスクのスループットと IOPS が VM の上限の制約を受けて低くなり、前述の表で示したディスクの上限は達成できなくなります。

Azure はディスク サイズ (切り上げたもの) を、表にある最も近い Premium Storage ディスク オプションにマッピングします。たとえば、ディスクのサイズが 100 GiB ならば P10 に分類され、1 秒間に処理できる I/O ユニットは最大 500、スループットは最大 100 MB/秒となります。同様に、ディスクのサイズが 400 GiB ならば P20 に分類され、1 秒間に処理できる I/O ユニットは最大 2300、スループットは最大 150 MB/秒となります。

入力/出力 (I/O) ユニット サイズは 256 KB です。転送されるデータが 256 KB に満たない場合は、単一の I/O ユニットとみなされます。I/O のサイズが大きくなると、サイズが 256 KB の複数の I/O としてカウントされます。たとえば、1100 KB I/O は 5 つの I/O ユニットとしてカウントされます。

スループットの制限には、ディスクへの書き込みと、キャッシュからではなく、そのディスクからの読み取りが含まれます。たとえば、P10 ディスクの場合、非キャッシュの読み取りと書き込みの合計は、1 秒あたり 100 MB 以下になります。同様に、1 つの P10 ディスクは 1 秒あたり 100 MB の非キャッシュの読み取りまたは 1 秒あたり 100 MB の書き込みのいずれかを処理できます。また、たとえば、1 秒あたり 60 MB の読み取りと 1 秒あたり 40 MB の書き込みを処理することもできます。

Azure でディスクを作成する場合は、容量、パフォーマンス、拡張性、ピーク時の負荷に関するアプリケーションのニーズに基づいた最適な Premium Storage ディスク製品を選択します。

> [AZURE.NOTE]既存のディスクのサイズを簡単に増やすことができます。たとえば、30 GB のディスクのサイズを 128 GB または 1 TB (テラバイト) にしたい場合があります。また、容量を増やしたり、IOPS とスループットを向上させたりする必要があるため、P20 ディスクを P30 ディスクに変換したい場合もあります。"-ResizedSizeInGB" プロパティを指定した "Update-AzureDisk" PowerShell コマンドレットを使用して、ディスクを拡張できます。この操作を実行するには、ディスクを VM からデタッチするか、VM を停止する必要があります。

次の表は、Premium Storage アカウントの拡張性ターゲットを示しています。

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>合計アカウント容量</strong></td>
	<td><strong>ローカル冗長ストレージ アカウントの合計帯域幅</strong></td>
</tr>
<tr>
	<td>
	<ul>
       <li type=round>ディスク容量: 35 TB</li>
       <li type=round>スナップショット容量: 10 TB</li>
    </ul>
	</td>
	<td>受信と送信を合わせて最大 50 GB/秒</td>
</tr>
</tbody>
</table>

- 受信とはストレージ アカウントに送信されるすべてのデータ (要求) のことです。
- 送信とはストレージ アカウントから送信されるすべてのデータ (応答) のことです。

詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](http://msdn.microsoft.com/library/azure/dn249410.aspx)」をご覧ください。

## Premium Storage を使用するときの調整
アプリケーションの IOPS またはスループットが Premium Storage ディスクの上限を超えた場合や、VM の全ディスクのディスク トラフィック合計がその VM でのディスク帯域幅上限を超えた場合に、調整 (スロットリング) が行われることがあります。調整を回避するために、プロビジョニングしたディスクの拡張性とパフォーマンスのターゲット、および VM で使用可能なディスク帯域幅に基づいて、ディスクの保留中の I/O 要求の数を制限することをお勧めします。

調整を回避できれば、アプリケーションの待ち時間を最短にすることができます。一方で、ディスクの保留中の I/O 要求の数が少なすぎる場合は、ディスクで利用可能な最大 IOPS とスループット レベルのメリットを活用できません。

次の例は、調整レベルの計算方法を示しています。

### 例 1:
アプリケーションは、P10 ディスク上で、1 秒間に 16 KB ディスク サイズの I/O を 495 回実行しました (これは 495 I/O ユニットに等しくなります)。同じ 1 秒内に 2 MB の I/O を実行すると、I/O の合計ユニットは 495 + 8 となります。これは、I/O ユニット サイズが 256 KB であることから、2 MB の I/O は 2048 KB / 256 KB = 8 I/O ユニットとなるためです。合計の 495 + 8 はディスク制限の 500 IOPS を超過するため、調整が発生します。

### 注:
すべての計算は、256 KB の I/O ユニット サイズに基づいて行われます。

### 例 2:
アプリケーションが P10 ディスク上で、256 KB ディスク サイズの I/O を 400 回実行しました。合計消費帯域幅は、(400 * 256) / 1024 = 100 MB/秒になります。100 MB/秒は、P10 ディスクのスループット制限です。アプリケーションが 1 秒間あたりにより多くの I/O を実行しようとすると、割り当てられた制限を超過してしまうため、調整が発生します。

### 例 3:
DS4 VM に P30 ディスクが 2 つアタッチされています。P30 ディスク 1 つあたりのスループットは 200 MB/秒です。しかし、DS4 VM のディスク帯域幅合計は 256 MB/秒が上限です。したがって、この DS4 VM では、アタッチされたディスクの両方が最大スループットを発揮することはできません。このことを解決するには、一方のディスクのトラフィックを 200 MB/秒で維持し、他方では 56 MB/秒で維持します。ディスク トラフィックの合計が 256 MB/秒を超えると、ディスク トラフィックの調整が行われます。

### 注:
ディスク トラフィックの大部分が小さなサイズの I/O で構成されていると、アプリケーションがスループット制限に達する前に、IOPS 制限に達してしまう場合がほとんどです。逆に、ディスク トラフィックの大部分が大きなサイズの I/O で構成されている場合は、IOPS 制限ではなくスループット制限に達してしまうことがほとんどです。最適なサイズの I/O を使用し、ディスクの保留中の I/O 要求の数を制限することで、アプリケーションの IOPS とスループットを最大化できます。

## Premium Storage を使用するときのスナップショットと Copy Blob
Premium Storage の場合も、Standard Storage を使用してスナップショットを作成するときと同じ方法でスナップショットを作成できます。Premium Storage はローカル冗長であるため、スナップショットを作成して、それらのスナップショットを Geo 冗長 Standard Storage アカウントにコピーすることをお勧めします。詳細については、「[Azure Storage 冗長オプション](http://msdn.microsoft.com/library/azure/dn727290.aspx)」をご覧ください。

ディスクが VM にアタッチされている場合、ディスクをバックアップするページ BLOB で特定の API 操作が許可されなくなります。たとえば、ディスクが VM にアタッチされている間は、その BLOB に対して [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) 操作を実行できません。代わりに、[Snapshot Blob](http://msdn.microsoft.com/library/azure/ee691971.aspx) REST API メソッドを使用して BLOB のスナップショットを作成してから、スナップショットの [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) を実行してアタッチされたディスクをコピーします。または、ディスクのアタッチを解除してから、基盤となる BLOB に対して必要な操作を実行します。

### 重要:

- 単一の BLOB のスナップショットの数は 100 に制限されています。スナップショットは最大で 10 分間隔で取得できます。
- Premium Storage アカウントあたりのスナップショットの最大容量は 10 TB (テラバイト) です。スナップショット容量とは、スナップショットのみに存在するデータの総量のことであり、ベース BLOB のデータは含まれないことにご注意ください。
- スナップショットの Geo 冗長コピーを維持するには、AzCopy か Copy Blob を使用して、Premium Storage アカウントから Geo 冗長 Standard Storage アカウントにスナップショットをコピーできます。詳細については、「[Microsoft Azure Storage での AzCopy の使用方法](storage-use-azcopy.md)」および「[Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx)」をご覧ください。
- Premium Storage アカウントでページ BLOB に対して REST 操作を実行する方法の詳細については、MSDN ライブラリの「[Azure Premium Storage での BLOB サービス操作の使用](http://go.microsoft.com/fwlink/?LinkId=521969)」をご覧ください。

## Linux VM とともに Premium Storage を使用する
Premium Storage 上で Linux VM を設定するときは、次の重要な手順を参照してください。

- Premium Storage ディスクのキャッシュ設定が ReadOnly または None に設定されている場合は、ファイル システムをマウントするときに "バリア" を無効にする必要があります。有効のままでは Premium Storage の拡張性ターゲットを達成できないからです。Premium Storage ディスクでこれらのキャッシュ設定を使用する場合は、ディスクへの書き込みの耐久性が保証されるので、バリアは必要ありません。書き込み要求が正常に完了した時点で、データは永続的なストアに書き込まれた状態になっています。お使いのファイル システムに応じて、次の方法を使用して "バリア" を無効にしてください。
	- **reiserFS**: バリアを無効にするには、マウント オプション "barrier=none" を使用します (バリアを有効にするには "barrier=flush" を使用します)。
	- **ext3/ext4**: バリアを無効にするには、マウント オプション "barrier=0" を使用します (バリアを有効にするには "barrier=1" を使用します)。
	- **XFS**: バリアを無効にするには、マウント オプション "nobarrier" を使用します (バリアを有効にするには "barrier" を使用します)。

- Premium Storage ディスクのキャッシュ設定が ReadWrite の場合は、書き込みの耐久性を保証するためにバリアを有効にしてください。
- VM を再起動してもボリューム ラベルが変更されないようにするには、ディスクに対する UUID 参照で /etc/fstab を更新する必要があります。「[データ ディスクを Linux 仮想マシンに接続する方法](../virtual-machines/virtual-machines-linux-how-to-attach-disk)」も参照してください。

次に示す Linux ディストリビューションは、Premium Storage での検証が完了しています。Premium Storage を使用するときのパフォーマンスと安定性を高めるために、VM をこれらのバージョン (以降) の少なくとも 1 つにアップグレードすることをお勧めします。また、バージョンによっては最新の LIS (Linux Integration Services v4.0 for Microsoft Azure) が必要になります。下記のリンクからダウンロードとインストールを行ってください。マイクロソフトは今後もイメージの検証を行い、この一覧に追加していきます。これらのイメージのパフォーマンスは変動することが検証時に判明しています。また、ワークロードの特性とイメージの設定にも依存することにご注意ください。ワークロードの種類に応じて、異なるイメージをチューニングします。<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;"> <tbody> <tr> <td><strong>ディストリビューション</strong></td> <td><strong>バージョン</strong></td> <td><strong>サポートされているカーネル</strong></td> <td><strong>サポートされているイメージ</strong></td> </tr> <tr> <td rowspan="4"><strong>Ubuntu</strong></td> <td>12.04</td> <td>3.2.0-75.110</td> <td>Ubuntu-12\_04\_5-LTS-amd64-server-20150119-ja-JP-30GB</td> </tr> <tr> <td>14.04</td> <td>3.13.0-44.73</td> <td>Ubuntu-14\_04\_1-LTS-amd64-server-20150123-ja-JP-30GB</td> </tr> <tr> <td>14.10</td> <td>3.16.0-29.39</td> <td>Ubuntu-14\_10-amd64-server-20150202-ja-JP-30GB</td> </tr> <tr> <td>15.04</td> <td>3.19.0-15</td> <td>Ubuntu-15\_04-amd64-server-20150422-ja-JP-30GB</td> </tr> <tr> <td><strong>SUSE</strong></td> <td>SLES 12</td> <td>3.12.36-38.1</td> <td>suse-sles-12-priority-v20150213<br>suse-sles-12-v20150213</td> </tr> <tr> <td><strong>CoreOS</strong></td> <td>584.0.0</td> <td>3.18.4</td> <td>CoreOS 584.0.0</td> </tr> <tr> <td rowspan="2"><strong>CentOS</strong></td> <td>6.5、6.6、6.7、7.0</td> <td></td> <td><a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 (必須)</a></br> *以下の注記参照 </td> </tr> <tr> <td>7.1 </td> <td>3.10.0-229.1.2.el7 </td><td> <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 (推奨)</a> <br/>*以下の注記参照</td> </tr>

<tr>
	<td rowspan="2"><strong>Oracle</strong></td>
	<td>6.4.</td>
	<td></td>
	<td><a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 (必須)</a></td>
</tr>
<tr>
	<td>7.0</td>
	<td></td>
	<td>詳細についてはサポートにお問い合わせください。</td>
</tr>
</tbody> </table>


### Openlogic Centos 用 LIS ドライバー

OpenLogic CentOS VM を実行しているお客様は、次のコマンドを実行して最新のドライバーをインストールする必要があります。

	sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
	sudo yum install microsoft-hyper-v

その後再起動して、新しいドライバーをアクティブ化する必要があります。



## Premium Storage を使用するときの料金と課金
Premium Storage を使用するときには、課金に関する次の考慮事項が適用されます。

- Premium Storage ディスクへの課金はプロビジョニングしたディスクのサイズによって異なります。Azure は、ディスク サイズ (切り上げたもの) を「[Premium Storage を使用するときの拡張性とパフォーマンスのターゲット](#scalability-and-performance-targets-whja-JPing-premium-storage)」にある表で指定されている、最も近い Premium Storage ディスク オプションにマッピングしますプロビジョニングされたディスクには、Premium Storage プランの月額料金を使用して、時間割りで計算して課金されます。たとえば、P10 ディスクをプロビジョニングし、20 時間後にそのディスクを削除した場合は、P10 製品の 20 時間分に対して課金されます。これは、実際にディスクに書き込まれたデータの量や、使用した IOPS/スループットには関係ありません。
- Premium Storage でのスナップショットについては、スナップショットで使用した追加の容量に対して課金されます。スナップショットの詳細については、「[BLOB のスナップショットの作成](http://msdn.microsoft.com/library/azure/hh488361.aspx)」をご覧ください。
- [送信データ転送](http://azure.microsoft.com/pricing/details/data-transfers/) (Azure データ センターから送信されるデータ) では、帯域幅の使用量に対して課金されます。

Premium Storage、DS シリーズの VM、GS シリーズの VM の料金についての詳細は、次の項目を参照してください。

- [Azure Storage の料金](http://azure.microsoft.com/pricing/details/storage/)
- [Virtual Machines の料金](http://azure.microsoft.com/pricing/details/virtual-machines/)

## 仮想マシンのデータ ディスク用に Premium Storage アカウントを作成する

ここでは、Azure プレビュー ポータル、Azure PowerShell、Azure コマンド ライン インターフェイス (Azure CLI) を使用して、Premium Storage アカウントを作成する方法を示します。さらに、Premium Storage を使用するときの仮想マシンの作成や仮想マシンへのデータ ディスクのアタッチといった、Premium Storage アカウントの使用例を紹介します。

### Premium Storage を使用する Azure 仮想マシンを Azure プレビュー ポータルで作成する

ここでは、Azure プレビュー ポータルを使用して Premium Storage アカウントを作成する方法を説明します。

1.	[Azure ポータル](portal.azure.com)にサインインします。まだサブスクリプションをお持ちでない場合は、[無料試用版](http://azure.microsoft.com/pricing/free-trial/)をお試しください。

2.	ハブ メニューの **[新規]** をクリックします。

3.	**[新規]** で、**[すべて]** をクリックします。**[ストレージ、キャッシュ、バックアップ]** を選択します。そこから **[Storage]**、**[作成]** の順にクリックします。

4.	[ストレージ アカウント] ブレードで、ストレージ アカウントの名前を入力します。**[価格レベル]** をクリックします。**[推奨される価格レベル]** ブレードで、**[すべての価格レベルの参照]** をクリックします。**[価格レベルの選択]** ブレードで、**[Premium ローカル冗長]** を選択します。**[選択]** をクリックします。**[ストレージ アカウント]** ブレードには、既定で **[価格レベル]** に **[Standard-GRS]** と表示されます。**[選択]** をクリックすると、**[価格レベル]** に **[Premium-LRS]** と表示されます。

	![価格レベル][Image1]


5.	**[ストレージ アカウント]** ブレードの **[リソース グループ]**、**[サブスクリプション]**、**[場所]**、**[診断]** には既定値をそのまま使用します。**[作成]** をクリックします。

Azure 環境内部の完全なチュートリアルについては、「[Windows を実行する仮想マシンを Azure プレビュー ポータルで作成する](../virtual-machines-windows-tutorial-azure-preview.md)」をご覧ください。

### Premium Storage を使用する Azure 仮想マシンを Azure PowerShell で作成する
この PowerShell の例では、新しい Premium Storage アカウントを作成してこのアカウントを使用するデータ ディスクを新しい Azure 仮想マシンにアタッチする方法を説明します。

1. 「[Azure PowerShell のインストールおよび構成方法](../install-configure-powershell.md)」に記載された手順に従って、PowerShell 環境を設定します。
2. PowerShell コンソールを起動してサブスクリプションに接続し、コンソール ウィンドウで次の PowerShell コマンドレットを実行します。この PowerShell ステートメントでわかるように、Premium Storage アカウントを作成するときは、**Type** パラメーターを **Premium\_LRS** に指定する必要があります。

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

3. 次に、DS シリーズの VM を作成し、コンソール ウィンドウで次の PowerShell コマンドレットを実行して Premium Storage を指定します。同様の手順で GS シリーズの VM を作成できます。コマンドでは適切な VM サイズを指定してください。例: Standard\_GS2

    	$storageAccount = "yourpremiumaccount"
    	$adminName = "youradmin"
    	$adminPassword = "yourpassword"
    	$vmName ="yourVM"
    	$location = "West US"
    	$imageName = "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201409.01-en.us-127GB.vhd"
    	$vmSize ="Standard_DS2"
    	$OSDiskPath = "https://" + $storageAccount + ".blob.core.windows.net/vhds/" + $vmName + "_OS_PIO.vhd"
    	$vm = New-AzureVMConfig -Name $vmName -ImageName $imageName -InstanceSize $vmSize -MediaLocation $OSDiskPath
    	Add-AzureProvisioningConfig -Windows -VM $vm -AdminUsername $adminName -Password $adminPassword
    	New-AzureVM -ServiceName $vmName -VMs $VM -Location $location

4. VM のディスク領域を増やしたい場合は、VM の作成後に、コンソール ウィンドウで次の PowerShell コマンドレットを実行して既存の DS シリーズまたは GS シリーズの VM に新しいデータ ディスクをアタッチします。

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

### Premium Storage を使用する Azure 仮想マシンを Azure コマンド ライン インターフェイスで作成する

[Azure コマンド ライン インターフェイス](../xplat-cli-install.md) (Azure CLI) は、Azure Platform を使用するための、一連のオープン ソース クロス プラットフォーム コマンドを提供します。以下の例では、Azure CLI (バージョン 0.8.14 以降) を使用して、Premium Storage アカウントを作成する方法、新しい仮想マシンを作成する方法、新しいデータ ディスクを Premium Storage アカウントからアタッチする方法を示します。

#### Premium Storage アカウントを作成する

````
azure storage account create "premiumtestaccount" -l "west us" --type PLRS
````

#### DS シリーズの仮想マシンを作成する

	azure vm create -z "Standard_DS2" -l "west us" -e 22 "premium-test-vm"
		"b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ja-JP-30GB" -u "myusername" -p "passwd@123"

#### 仮想マシンに関する情報を表示する

	azure vm show premium-test-vm

#### 新しいデータ ディスクをアタッチする

	azure vm disk attach-new premium-test-vm 20 https://premiumstorageaccount.blob.core.windows.net/vhd-store/data1.vhd

#### 新しいデータ ディスクに関する情報を表示する

	azure vm disk show premium-test-vm-premium-test-vm-0-201502210429470316

## 次のステップ

- [Azure Premium Storage での BLOB サービス操作の使用](http://go.microsoft.com/fwlink/?LinkId=521969)
- [Azure Premium Storage への移行](storage-migration-to-premium-storage.md)
- [Windows を実行する仮想マシンの作成](../virtual-machines-windows-tutorial-azure-preview.md)
- [Azure の仮想マシンおよびクラウド サービスのサイズ](../virtual-machines/virtual-machines-size-specs.md)
- [Storage のドキュメント](http://azure.microsoft.com/documentation/services/storage/)

[Image1]: ./media/storage-premium-storage-preview-portal/Azure_pricing_tier.png
 

<!---HONumber=AcomDC_1210_2015-->