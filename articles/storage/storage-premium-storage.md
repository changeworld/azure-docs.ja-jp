<properties
	pageTitle="Premium Storage: Azure の仮想マシン ワークロード用の高パフォーマンス ストレージ| Microsoft Azure"
	description="Premium Storage は、Azure Virtual Machines で実行される高負荷の I/O ワークロードのための、高パフォーマンスで待ち時間の少ないディスク サポートを提供します。Azure DS シリーズ、DSv2 シリーズおよび GS シリーズの VM は、Premium Storage をサポートしています。"
	services="storage"
	documentationCenter=""
	authors="aungoo-msft"
	manager="tadb"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="aungoo;robinsh"/>


# Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ

## Overview

Azure Premium Storage は、高負荷の I/O ワークロードを実行する仮想マシン向けに高パフォーマンスで待ち時間の少ないディスク サポートを提供します。Premium Storage を使用する仮想マシン (VM) では、ソリッド ステート ドライブ (SSD) にデータを格納します。アプリケーションの VM ディスクを Azure Premium Storage に移行することで、これらのディスクの速度とパフォーマンスを最大限に利用することができます。

Azure VM では複数の Premium Storage ディスクをアタッチすることができるので、アプリケーションには VM あたり最大で 64 TB のストレージを設定することができます。Premium Storage を使用すると、アプリケーションは、VM あたり 80,000 IOPS (1 秒あたりの入力/出力操作) と、VM あたり 2000 MB/秒のディスク スループットを実現でき、読み取り操作の待ち時間も非常に短くなります。

Premium Storage により、Azure は Dynamics AX、Dynamics CRM、Exchange Server、SharePoint Farms、SAP Business Suite などの要件の厳しいエンタープライズ アプリケーションを本当の意味でクラウドに昇格および移行できます。Premium Storage を使用すると、SQL Server、Oracle、MongoDB、MySQL、Redis のような一貫して高パフォーマンスと低待機時間を必要とする負荷の要件が厳しいデータベースを実行できます。

>[AZURE.NOTE] 高い IOPS を必要とする仮想マシンのディスクは Azure Premium Storage に移行して、アプリケーションが最高のパフォーマンスを発揮できるようにすることをお勧めします。ディスクが高い IOPS を必要としない場合は、ディスクを Standard Storage 内に保持することでコストを抑えることができます。Standard Storage の場合、仮想マシンのディスク データは SSD ではなくハード ディスク ドライブ (HDD) に格納されます。

Azure Premium Storage を使用するには、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)のページをご覧ください。既存の仮想マシンを Premium Storage に移行する方法の詳細については、「[Azure Premium Storage への移行](storage-migration-to-premium-storage.md)」を参照してください。

>[AZURE.NOTE] 現在、Premium Storage は一部のリージョンでのみサポートされています。使用できるリージョンの一覧については、「[リージョン別の Azure サービス](https://azure.microsoft.com/regions/#services)」を参照してください。

## Premium Storage の機能

**Premium Storage ディスク**: Azure Premium Storage は、Premium Storage でサポートされる DS、DSv2、GS、または Fs シリーズ Azure VM に接続可能な VM ディスクをサポートしています。Premium Storage の使用時に、それぞれパフォーマンス仕様の異なる P10 (128GiB)、P20 (512GiB)、P30 (1024GiB) の 3 つのディスク サイズから選択できます。アプリケーション要件に応じて、これらのディスクの 1 つ以上を Premium Storage でサポートされる VM に接続できます。[Premium Storage の拡張性とパフォーマンスの目標](#premium-storage-scalability-and-performance-targets)に関する次のセクションで、仕様についてさらに詳しく説明します。

**Premium ページ BLOB**: Premium Storage は Azure ページ BLOB をサポートしています。これは、Azure Virtual Machines (VM) の永続ディスクを保持するために使用されます。現在、Premium Storage は Azure ブロック BLOB、Azure ファイル、Azure 追加 BLOB、Azure テーブル、Azure キューをサポートしていません。Premium Storage アカウントに配置されている他のオブジェクトはページ BLOB であり、サポートされているプロビジョニングされたサイズのいずれかにスナップされます。したがって、Premium Storage アカウントは小さな BLOB を格納するためのものではありません。

**Premium Storage アカウント**: Premium Storage の使用を開始するには、Premium Storage アカウントを作成する必要があります。[Azure ポータル](https://portal.azure.com)を使用する場合は、"Premium" パフォーマンス レベルと、レプリケーション オプションとして "ローカル冗長ストレージ (LRS)" を指定することで、Premium Storage アカウントを作成できます。また、[Storage REST API](http://msdn.microsoft.com//library/azure/dd179355.aspx) バージョン 2014-02-14 以降、[Service Management REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx) バージョン 2014-10-01 以降 (クラシック デプロイ)、[Azure Storage Resource Provider REST API リファレンス](http://msdn.microsoft.com/library/azure/mt163683.aspx) (Resource Manager デプロイ)、[Azure PowerShell](../powershell-install-configure.md) バージョン 0.8.10 以降を使用している場合は、種類として "Premium\_LRS" を指定することで、Premium Storage アカウントを作成することもできます。Premium Storage アカウントの制限については、以降の「[Premium Storage の拡張性とパフォーマンスの目標](#premium-storage-scalability-and-performance-targets)」を参照してください。

**Premium ローカル冗長ストレージ**: Premium Storage アカウントは、レプリケーション オプションとしてローカル冗長ストレージ (LRS) のみをサポートし、1 つのリージョン内にデータのコピーを 3 つ保持します。Premium Storage を使用するときに Geo レプリケーションについて考慮すべき点については、この記事の「[スナップショットと Copy Blob](#snapshots-and-copy-blob)」を参照してください。

ストレージ アカウントは、オペレーティング システム (OS) とデータ ディスクのコンテナーとして使用されます。Azure DS、DSv2、GS、または Fs VM を作成して Azure Premium Storage アカウントを選択すると、オペレーティング システムとデータ ディスクはそのストレージ アカウントに格納されます。

ディスク向け Premium Storage は次のいずれかの方法で使用できます。
- まず、Premium Storage アカウントを作成します。次に、新しい DS、DSv2、GS、または Fs VM の作成時に、Storage の構成設定で Premium Storage アカウントを選択します。または
- DS、DSv2、GS、Fs VM の作成時に、Storage の構成設定に新しい Premium Storage アカウントを作成するか、Azure Portal で既定の Premium Storage アカウントを自動作成します。

詳細な手順については、この記事の以降の[クイック スタート](#quick-start)を参照してください。

>[AZURE.NOTE] Premium Storage のアカウントをカスタム ドメイン名にマッピングすることはできません。

## Premium Storage でサポートされる VM

Premium Storage は、DS シリーズ、DSv2 シリーズ、GS シリーズおよび Fs シリーズの Azure Virtual Machines (VM) をサポートしています。Premium Storage でサポートされる VM では、Standard Storage ディスクと Premium Storage ディスクの両方を使用できます。Premium Storage に互換性のない VM シリーズでは Premium Storage ディスクを使用できません。

Windows VM で使用できる Azure VM の種類やサイズについては、「[Windows VM のサイズ](../virtual-machines/virtual-machines-windows-sizes.md)」を参照してください。Linux VM の VM の種類やサイズについては、「[Linux VM のサイズ](../virtual-machines/virtual-machines-linux-sizes.md)」を参照してください。

次に、DS、DSv2、GS、および Fs シリーズ VM の機能をいくつか紹介します。

**Cloud Service**: DS シリーズ VM は DS シリーズ VM のみを含むクラウド サービスに追加できます。DS シリーズ以外の VM が存在する既存のクラウド サービスに DS シリーズの VM を追加しないでください。既存の VHD を DS シリーズ VM のみを実行する新しいクラウド サービスに移行できます。DS シリーズ VM をホストする新しいクラウド サービスに引き続き同じ仮想 IP アドレス (VIP) を使用するには、[予約済み IP アドレス](../virtual-network/virtual-networks-instance-level-public-ip.md)を使います。GS シリーズの VM は G シリーズの VM のみ実行している既存のクラウド サービスに追加できます。

**オペレーティング システム ディスク**: Premium Storage でサポートされる Azure 仮想マシンで使用するオペレーティング システム (OS) ディスクは、Standard Storage アカウントと Premium Storage アカウントのどちらでホストするように設定されていてもかまいません。最適なエクスペリエンスを実現するために、Premium Storage ベースの OS ディスクを使用することをお勧めします。

**データ ディスク**: 同じ Premium Storage でサポートされる VM 内では、Premium Storage ディスクと Standard Storage ディスクの両方を使用できます。Premium Storage を使用すると、Premium Storage でサポートされる VM をプロビジョニングし、複数の永続データ ディスクを VM に接続できます。必要に応じて、ディスク全体をストライピングして容量を増やし、ボリュームのパフォーマンスを高めることができます。

> [AZURE.NOTE] [記憶域スペース](http://technet.microsoft.com/library/hh831739.aspx)を使用して Premium Storage データ ディスクをストライピングする場合は、使用するディスクごとに 1 つの列で構成する必要があります。そうしない場合は、ディスク全体のトラフィックの配分が不均等になるため、ストライプ ボリュームの全体的なパフォーマンスが低下する可能性があります。既定では、サーバー マネージャー ユーザー インターフェイス (UI) で最大 8 つのディスクの列を設定できます。ただし、8 つ以上のディスクがある場合は、PowerShell を使用してボリュームを作成し、列の数を手動で指定する必要があります。そうしない場合、サーバー マネージャー UI はそれ以上のディスクがある場合でも 8 つの列を使用し続けます。たとえば、1 つのストライプ セット内に 32 のディスクがある場合は、32 の列を指定する必要があります。PowerShell の [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) コマンドレットの *NumberOfColumns* パラメーターを使用して、仮想ディスクが使用する列数を指定できます。詳細については、[記憶域スペースの概要](http://technet.microsoft.com/library/hh831739.aspx)に関するページおよび「[Storage Spaces Frequently Asked Questions (記憶域スペースに関してよく寄せられる質問)](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)」をご覧ください。

**キャッシュ**: Premium Storage でサポートされる VM は独自のキャッシュ機能を備えています。これにより、基になる Premium Storage ディスク パフォーマンス以上の高いレベルのスループットと待機時間を実現できます。Premium Storage ディスクで ReadOnly、ReadWrite、None などのディスク キャッシュ ポリシーを構成できます。既定のディスク キャッシュ ポリシーは、すべてのプレミアム データ ディスクで ReadOnly、オペレーティング システム ディスクで ReadWrite です。アプリケーションのパフォーマンスを最適化するために、適切な構成設定を使用してください。たとえば、SQL Server データ ファイルなどの読み取り負荷の高いデータ ディスクまたは読み取り専用のデータ ディスクの場合、ディスク キャッシュ ポリシーを "ReadOnly" に設定します。SQL Server ログ ファイルなどの書き込み負荷の高いまたは書き込み専用のデータ ディスクの場合、ディスク キャッシュ ポリシーを "None" に設定します。Premium Storage での設計の最適化について詳しくは、「[Premium Storage を使用した高パフォーマンスのための設計](storage-premium-storage-performance.md)」を参照してください。

**分析**: Premium Storage アカウントを使用して VM のパフォーマンスを分析するには、Azure ポータルで Azure VM 診断を有効にします。詳細については、「[Microsoft Azure Virtual Machine Monitoring with Azure Diagnostics Extension (Azure 診断の拡張機能を使用した Microsoft Azure の仮想マシンの監視)](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/)」をご覧ください。ディスクのパフォーマンスを確認するには、オペレーティング システム ベースのツール (Windows VM 向け [Windows パフォーマンス モニター](https://technet.microsoft.com/library/cc749249.aspx)、Linux VM 向け [IOSTAT](http://linux.die.net/man/1/iostat) など) を使用します。

**VM のスケール制限とパフォーマンス**: 各 Premium Storage でサポートされる VM サイズにはスケール制限や、IOPS、帯域幅、VM ごとに接続できるディスク数に関するパフォーマンス仕様があります。Premium Storage でサポートされる VM で Premium Storage ディスクを使用するときは、ディスク トラフィックを円滑にするために VM に十分な IOPS と帯域幅があることを確認してください。たとえば、STANDARD\_DS1 VM では、Premium Storage ディスク トラフィック専用の帯域幅は 32 MB/秒です。P10 プレミアム ディスクでは、100 MB/秒の帯域幅が提供されます。この VM に接続された Premium Storage ディスクが P10 の場合も、32 MB/秒が上限となり、P10 ディスクのスループットの上限である 100 MB に達することはありません。

現時点では、DS シリーズ最大の VM は STANDARD\_DS14 であり、この VM の全ディスクの帯域幅は最大 512 MB/秒です。現時点では、DS シリーズ最大の VM は STANDARD\_GS5 であり、この VM の全ディスクの帯域幅は最大 2000 MB/秒です。これらの制限は、ディスク トラフィックのみを対象としており、キャッシュ ヒットとネットワーク トラフィックは対象外となります。VM のネットワーク トラフィックの帯域幅は別に用意されており、Premium Storage ディスク専用の帯域幅とは区別されています。

Premium Storage でサポートされる VM の最大 IOPS とスループット (帯域幅) の最新情報については、「[Windows VM のサイズ](../virtual-machines/virtual-machines-windows-sizes.md)」または「[Linux VM のサイズ](../virtual-machines/virtual-machines-linux-sizes.md)」を参照してください。

Premium Storage のディスクそれぞれの IOPS とスループット上限については、この記事の「[Premium Storage の拡張性とパフォーマンスの目標](#premium-storage-scalability-and-performance-targets)」のセクションにある表を参照してください。

## Premium Storage の拡張性とパフォーマンスの目標

このセクションでは、Premium Storage を使用する際に考慮する必要のあるすべての拡張性とパフォーマンスの目標について説明します。

### Premium Storage アカウントの制限

Premium Storage アカウントの拡張性の目標は、以下のとおりです。

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

詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](storage-scalability-targets.md)」をご覧ください。

アプリケーションのニーズが単一のストレージ アカウントの拡張性ターゲットを上回った場合は、複数のストレージ アカウントを使用するようにアプリケーションを構築し、それらのストレージ アカウント間でデータを分割します。たとえば、多数の VM があり、アタッチするディスクの合計が 51 テラバイト (TB) の場合は、ディスクを 2 つのストレージ アカウントに分散させます。Premium Storage アカウント 1 つあたりの上限は 35 TB であるからです。1 つの Premium Storage アカウントでプロビジョニングするディスクの容量が 35 TB を超えることがないようにしてください。

### Premium Storage ディスクの制限

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
	<td>100 MB/秒 </td>
	<td>150 MB/秒 </td>
	<td>200 MB/秒 </td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] この記事の「[Premium Storage でサポートされる VM](#ds-dsv2-and-gs-series-vms)」で説明したように、VM の帯域幅が、ディスク トラフィックを処理するのに十分な大きさであることを確認してください。このとおりでない場合は、ディスクのスループットと IOPS が VM の上限の制約を受けて低くなり、前述の表で示したディスクの上限は達成できなくなります。

Premium Storage の拡張性とパフォーマンスの目標についか知っておく必要のある重要事項をいくつか説明します。

- **プロビジョニングされた容量とパフォーマンス**: Premium Storage ディスクをプロビジョニングした場合、Standard Storage とは異なり、対象ディスクの容量、IOPS、スループットが保証されます。たとえば、P30 ディスクを作成した場合、Azure により、対象のディスクに 1024 GB のストレージ容量、5000 IOPS、200 MB/秒のスループットがプロビジョニングされます。アプリケーションでは、容量とパフォーマンスのすべてまたは一部を使用できます。

- **ディスク サイズ**: Azure はディスク サイズ (切り上げたもの) を、表にある最も近い Premium Storage ディスク オプションにマッピングします。たとえば、ディスクのサイズが 100 GiB ならば P10 に分類され、1 秒間に処理できる I/O ユニットは最大 500、スループットは最大 100 MB/秒となります。同様に、ディスクのサイズが 400 GiB ならば P20 に分類され、1 秒間に処理できる I/O ユニットは最大 2300、スループットは最大 150 MB/秒となります。

	> [AZURE.NOTE] 既存のディスクのサイズを簡単に増やすことができます。たとえば、30 GB のディスクのサイズを 128 GB または 1 TB (テラバイト) にしたい場合があります。また、容量を増やしたり、IOPS とスループットを向上させたりする必要があるため、P20 ディスクを P30 ディスクに変換したい場合もあります。"-ResizedSizeInGB" プロパティを指定した "Update-AzureDisk" PowerShell コマンドレットを使用して、ディスクを拡張できます。この操作を実行するには、ディスクを VM からデタッチするか、VM を停止する必要があります。

- **IO サイズ**: 入力/出力 (I/O) ユニット サイズは 256 KB です。転送されるデータが 256 KB に満たない場合は、単一の I/O ユニットとみなされます。I/O のサイズが大きくなると、サイズが 256 KB の複数の I/O としてカウントされます。たとえば、1100 KB I/O は 5 つの I/O ユニットとしてカウントされます。

- **スループット**: スループットの制限には、ディスクへの書き込みと、キャッシュからではなく、そのディスクからの読み取りが含まれます。たとえば、P10 ディスクのスループットは、ディスクあたり 100 MB/秒です。P10 ディスクの有効なスループットの例を以下にいくつか示します。
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
<td><strong>P10 ディスクあたりの最大スループット</strong></td>
<td><strong>ディスクからの非キャッシュの読み取り</strong></td>
<td><strong>ディスクからの非キャッシュの書き込み</strong></td>
</tr>
<tr>
<td>100 MB/秒</td>
<td>100 MB/秒</td>
<td>0</td>
</tr>
<tr>
<td>100 MB/秒</td>
<td>0</td>
<td>100 MB/秒</td>
</tr>
<tr>
<td>100 MB/秒 </td>
<td>60 MB/秒 </td>
<td>40 MB/秒 </td>
</tr>
</tbody>
</table>

- **キャッシュ ヒット**: キャッシュ ヒットには、ディスクの割り当て済みの IOPS とスループットによる制限はありません。たとえば、Premium Storage でサポートされる VM でキャッシュを読み取り専用に設定したデータ ディスクを使用する場合、そのキャッシュからの読み取りは、Premium Storage のディスク制限の対象にはなりません。そのため、ワークロードの大部分が読み取りの場合、ディスクから非常に高いスループットが得られます。キャッシュには、VM サイズに基づいて、VM レベルで IOPS とスループットの個別の制限が適用されます。DS シリーズ VM は、IOPS が約 4000 で、キャッシュとローカル SSD の IO がコアあたり 33 MB/秒です。GS シリーズ VM の制限は、IOPS が 5000 で、キャッシュとローカル SSD の IO がコアあたり 50 MB/秒です。

## 調整
アプリケーションの IOPS またはスループットが Premium Storage ディスクの上限を超えた場合や、VM の全ディスクのディスク トラフィック合計がその VM でのディスク帯域幅上限を超えた場合に、調整 (スロットル) が行われることがあります。調整を回避するために、プロビジョニングしたディスクの拡張性とパフォーマンスのターゲット、および VM で使用可能なディスク帯域幅に基づいて、ディスクの保留中の I/O 要求の数を制限することをお勧めします。

調整を回避できれば、アプリケーションの待ち時間を最短にすることができます。一方で、ディスクの保留中の I/O 要求の数が少なすぎる場合は、ディスクで利用可能な最大 IOPS とスループット レベルのメリットを活用できません。

次の例は、スロットル レベルの計算方法を示しています。すべての計算は、256 KB の I/O ユニット サイズに基づいて行われます。

### 例 1:
アプリケーションは、P10 ディスク上で、1 秒間に 16 KB サイズの I/O ユニットを 495 回実行しました。これらは、1 秒間あたり (IOPS) 495 I/O ユニットとしてカウントされます。同じ 1 秒内に 2 MB の I/O を実行すると、I/O の合計ユニットは 495 + 8 となります。これは、I/O ユニット サイズが 256 KB であることから、2 MB の I/O は 2048 KB / 256 KB = 8 I/O ユニットとなるためです。合計の 495 + 8 はディスク制限の 500 IOPS を超過するため、調整が発生します。

### 例 2:
アプリケーションが P10 ディスク上で、256 KB サイズの I/O ユニットを 400 回実行しました。合計消費帯域幅は、(400 * 256) / 1024 = 100 MB/秒になります。P10 ディスクには、100 MB/秒というスループット制限があります。アプリケーションが 1 秒間あたりにより多くの I/O を実行しようとすると、割り当てられた制限を超過してしまうため、調整が発生します。

### 例 3:
DS4 VM に P30 ディスクが 2 つアタッチされています。P30 ディスク 1 つあたりのスループットは 200 MB/秒です。しかし、DS4 VM のディスク帯域幅合計は 256 MB/秒が上限です。したがって、この DS4 VM では、アタッチされたディスクの両方が最大スループットを発揮することはできません。このことを解決するには、一方のディスクのトラフィックを 200 MB/秒で維持し、他方では 56 MB/秒で維持します。ディスク トラフィックの合計が 256 MB/秒を超えると、ディスク トラフィックの調整が行われます。

>[AZURE.NOTE] ディスク トラフィックの大部分が小さなサイズの I/O で構成されていると、アプリケーションがスループット制限に達する前に、IOPS 制限に達してしまう場合がほとんどです。逆に、ディスク トラフィックの大部分が大きなサイズの I/O で構成されている場合は、IOPS 制限ではなくスループット制限に達してしまうことがほとんどです。最適なサイズの I/O を使用し、ディスクの保留中の I/O 要求の数を制限することで、アプリケーションの IOPS とスループットを最大化できます。

Premium Storage を使用した高パフォーマンスのための設計について詳しくは、「[Premium Storage を使用した高パフォーマンスのための設計](storage-premium-storage-performance.md)」を参照してください。

## スナップショットと BLOB のコピー
Premium Storage の場合も、Standard Storage を使用してスナップショットを作成するときと同じ方法でスナップショットを作成できます。Premium Storage はレプリケーション オプションとしてローカル冗長ストレージ (LRS) のみをサポートするため、スナップショットを作成し、そのスナップショットを geo 冗長 Standard Storage アカウントにコピーすることをお勧めします。詳細については、「[Azure Storage 冗長オプション](storage-redundancy.md)」をご覧ください。

ディスクが VM にアタッチされている場合、ディスクをバックアップするページ BLOB で特定の API 操作が許可されなくなります。たとえば、ディスクが VM にアタッチされている間は、その BLOB に対して [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) 操作を実行できません。代わりに、[Snapshot Blob](http://msdn.microsoft.com/library/azure/ee691971.aspx) REST API メソッドを使用して BLOB のスナップショットを作成してから、スナップショットの [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) を実行してアタッチされたディスクをコピーします。または、ディスクのアタッチを解除してから、基盤となる BLOB に対して必要な操作を実行します。

Premium Storage BLOB スナップショットには次の制限が適用されます。
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Premium Storage の制限</strong></td>
	<td><strong>値</strong></td>
</tr>
<tr>
	<td>BLOB あたりの最大スナップショット数</td>
	<td>100</td>
</tr>
<tr>
	<td>スナップショットのストレージ アカウントの容量 (スナップショットのデータのみを含み、ベース BLOB のデータは含まない)</td>
	<td>10 TB</td>
</tr>
<tr>
	<td>連続したスナップショットの最小時間間隔</td>
	<td>10 分</td>
</tr>
</tbody>
</table>

スナップショットの地理冗長コピーを維持するには、AzCopy か Copy Blob を使用して、Premium Storage アカウントから地理冗長 Standard Storage アカウントにスナップショットをコピーできます。詳細については、「[AzCopy コマンド ライン ユーティリティを使用してデータを転送する](storage-use-azcopy.md)」および「[BLOB のコピー](http://msdn.microsoft.com/library/azure/dd894037.aspx)」を参照してください。

Premium Storage アカウントでページ BLOB に対して REST 操作を実行する方法の詳細については、MSDN ライブラリの「[Azure Premium Storage での BLOB サービス操作の使用](http://go.microsoft.com/fwlink/?LinkId=521969)」をご覧ください。

## Linux VM とともに Premium Storage を使用する
Premium Storage 上で Linux VM を設定するときは、次の重要な手順を参照してください。

- Premium Storage ディスクのキャッシュ設定が ReadOnly または None に設定されている場合は、ファイル システムをマウントするときに "バリア" を無効にする必要があります。有効のままでは Premium Storage の拡張性ターゲットを達成できないからです。Premium Storage ディスクでこれらのキャッシュ設定を使用する場合は、ディスクへの書き込みの耐久性が保証されるので、バリアは必要ありません。書き込み要求が正常に完了した時点で、データは永続的なストアに書き込まれた状態になっています。お使いのファイル システムに応じて、次の方法を使用して "バリア" を無効にしてください。
	- **reiserFS**: バリアを無効にするには、マウント オプション "barrier=none" を使用します (バリアを有効にするには "barrier=flush" を使用します)。
	- **ext3/ext4**: バリアを無効にするには、マウント オプション "barrier=0" を使用します (バリアを有効にするには "barrier=1" を使用します)。
	- **XFS**: バリアを無効にするには、マウント オプション "nobarrier" を使用します (バリアを有効にするには "barrier" を使用します)。

- Premium Storage ディスクのキャッシュ設定が ReadWrite の場合は、書き込みの耐久性を保証するためにバリアを有効にしてください。
- VM を再起動してもボリューム ラベルが変更されないようにするには、ディスクに対する UUID 参照で /etc/fstab を更新する必要があります。「[データ ディスクを Linux 仮想マシンに接続する方法](../virtual-machines/virtual-machines-linux-classic-attach-disk.md)」も参照してください。

次に示す Linux ディストリビューションは、Premium Storage での検証が完了しています。Premium Storage を使用するときのパフォーマンスと安定性を高めるために、VM をこれらのバージョン (以降) の少なくとも 1 つにアップグレードすることをお勧めします。また、バージョンによっては最新の LIS (Linux Integration Services v4.0 for Microsoft Azure) が必要になります。下記のリンクからダウンロードとインストールを行ってください。マイクロソフトは今後もイメージの検証を行い、この一覧に追加していきます。これらのイメージのパフォーマンスは変動することが検証時に判明しています。また、ワークロードの特性とイメージの設定にも依存することにご注意ください。ワークロードの種類に応じて、異なるイメージをチューニングします。
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>ディストリビューション</strong></td>
	<td><strong>バージョン</strong></td>
	<td><strong>サポートされるカーネル</strong></td>
	<td><strong>詳細</strong></td>
</tr>
<tr>
	<td rowspan="2"><strong>Ubuntu</strong></td>
	<td>12.04</td>
	<td>3.2.0-75.110+</td>
	<td>Ubuntu-12_04_5-LTS-amd64-server-20150119-ja-JP-30GB</td>
</tr>
<tr>
	<td>14.04+</td>
	<td>3.13.0-44.73+</td>
	<td>Ubuntu-14_04_1-LTS-amd64-server-20150123-ja-JP-30GB</td>
</tr>
<tr>
	<td><strong>Debian</strong></td>
	<td>7.x、8.x</td>
	<td>3.16.7-ckt4-1+</td>
    <td> </td>
</tr>
<tr>
	<td rowspan="2"><strong>SUSE</strong></td>
	<td>SLES 12</td>
	<td>3.12.36-38.1+</td>
	<td>suse-sles-12-priority-v20150213<br>suse-sles-12-v20150213</td>
</tr>
<tr>
	<td>SLES 11 SP4</td>
    <td>3.0.101-0.63.1+</td>
    <td> </td>
</tr>
<tr>
	<td><strong>CoreOS</strong></td>
	<td>584.0.0+</td>
	<td>3.18.4+</td>
	<td>CoreOS 584.0.0</td>
</tr>
<tr>
	<td rowspan="2"><strong>CentOS</strong></td>
	<td>6.5、6.6、6.7、7.0</td>
	<td></td>
	<td>
		<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">LIS4 が必須</a> <br/>
		*下記の注参照*
	</td>
</tr>
<tr>
	<td>7.1+</td>
	<td>3.10.0-229.1.2.el7+</td>
	<td>
		<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">LIS4 が推奨</a> <br/>
		*下記の注参照*
	</td>
</tr>
<tr>
	<td><strong>RHEL</strong></td>
	<td>6.8+、7.2+</td>
	<td> </td>
	<td></td>
</tr>
<tr>
	<td rowspan="3"><strong>Oracle</strong></td>
    <td>6.8+、7.2+</td>
    <td> </td>
    <td> UEK4 または RHCK </td>

</tr>
<tr>
	<td>7.0-7.1</td>
	<td> </td>
	<td>UEK4 または RHCK と <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">LIS 4.1 +</a></td>
</tr>
<tr>
	<td>6.4-6.7</td>
	<td></td>
	<td>UEK4 または RHCK と <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">LIS 4.1 +</a></td>
</tr>
</tbody>
</table>


### Openlogic Centos 用 LIS ドライバー

OpenLogic CentOS VM を実行しているお客様は、次のコマンドを実行して最新のドライバーをインストールする必要があります。

	sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
	sudo yum install microsoft-hyper-v

その後再起動して、新しいドライバーをアクティブ化する必要があります。

## 価格と課金
Premium Storage を使用するときには、課金に関する次の考慮事項が適用されます。
- Premium Storage のディスク/BLOB サイズ
- Premium Storage のスナップショット
- 送信データ転送

**Premium Storage のディスク/BLOB サイズ**: Premium Storage ディスク/BLOB への課金は、プロビジョニングされたディスク/BLOB のサイズによって異なります。Azure は、プロビジョニングされたサイズ (切り上げたもの) を、「[Premium Storage の拡張性とパフォーマンスの目標](#premium-storage-scalability-and-performance-targets)」にある表に記載されているオプションの中で最も近い Premium Storage ディスク オプションにマップします。Premium Storage アカウントに保存されているすべてのオブジェクトが、サポートされているプロビジョニングされたサイズのいずれかにマップされ、それに応じて課金されます。そのため、小さな BLOB の格納には Premium Storage アカウントを使用しないでください。プロビジョニングされたディスク/BLOB には、Premium Storage プランの月額料金を使用して、時間割りで計算して課金されます。たとえば、P10 ディスクをプロビジョニングし、20 時間後にそのディスクを削除した場合は、P10 製品の 20 時間分に対して課金されます。これは、実際にディスクに書き込まれたデータの量や、使用した IOPS/スループットには関係ありません。

**Premium Storage のスナップショット**: Premium Storage でのスナップショットについては、スナップショットで使用した追加の容量に対して課金されます。スナップショットの詳細については、「[BLOB のスナップショットの作成](http://msdn.microsoft.com/library/azure/hh488361.aspx)」をご覧ください。

**送信データ転送**: [送信データ転送](https://azure.microsoft.com/pricing/details/data-transfers/) (Azure データ センターから送信されるデータ) では、帯域幅の使用量に対して課金されます。

Premium Storage と Premium Storage でサポートされる VM の料金についての詳細は、次の項目を参照してください。

- [Azure Storage の料金](https://azure.microsoft.com/pricing/details/storage/)
- [Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/)

## バックアップ
Premium Storage を使用した仮想マシンは、Azure Backup を使用してバックアップできます。[詳細についてはこちらをご覧ください](../backup/backup-azure-vms-first-look-arm.md)。

## クイック スタート

## 仮想マシンのデータ ディスク用に Premium Storage アカウントを作成する

このセクションでは、Azure ポータル、Azure PowerShell および Azure CLI を使用した次のシナリオについて説明します。

- Premium Storage アカウントの作成方法。
- Premium Storage を使用した際に、仮想マシンを作成して、その仮想マシンにデータ ディスクを接続する方法。
- 仮想マシンに接続されたデータ ディスクのディスク キャッシュ ポリシーを変更する方法。

### Premium Storage を使用する Azure 仮想マシンを Azure ポータルで作成する

#### I.Azure ポータルで Premium Storage アカウントを作成する

ここでは、Azure ポータルを使用して Premium Storage アカウントを作成する方法を説明します。

1.	[Azure ポータル](https://portal.azure.com)にサインインします。まだサブスクリプションをお持ちでない場合は、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)をお試しください。

2. ハブ メニューで、**[新規]**、**[データ + ストレージ]**、**[ストレージ アカウント]** の順にクリックします。

3. ストレージ アカウントの名前を入力します。

	> [AZURE.NOTE] ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。
	>  
	> ストレージ アカウント名は Azure 内で一意である必要があります。選択したストレージ アカウント名が既に使用されているかどうかが、Azure ポータルによって示されます。

4. 使用するデプロイメント モデル (**[Resource Manager]** または **[クラシック]**) を指定します。**[リソース マネージャー]** が、推奨されるデプロイ モデルです。詳細については、「[リソース マネージャー デプロイと従来のデプロイを理解する](../resource-manager-deployment-model.md)」を参照してください。

5. ストレージ アカウントのパフォーマンス レベルとして **[Premium]** を指定します。

6. **ローカル冗長ストレージ (LRS)** は、Premium Storage で使用できる唯一のレプリケーション オプションです。Azure Storage のレプリケーション オプションの詳細については、[Azure Storage のレプリケーション](storage-redundancy.md)に関するページをご覧ください。

7. 新しいストレージ アカウントを作成するサブスクリプションを選択します。

8. 新しいリソース グループを指定するか、既定のリソース グループを選択します。リソース グループの詳細については、「[Azure Resource Manager の概要](../resource-group-overview.md)」をご覧ください。

9. ストレージ アカウントの地理的な場所を選択します。「[リージョン別のサービス](https://azure.microsoft.com/regions/#services)」を参照して、選択した場所で Premium Storage を使用できるかどうかを確認できます。

10. **[作成]** をクリックしてストレージ アカウントを作成します。

#### II.Azure ポータルで Azure 仮想マシンを作成する

Premium Storage を使用するには、Premium Storage でサポートされる VM を作成する必要があります。[Azure Portal での Windows 仮想マシンの作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md)に関するページの手順に従って、DS、DSv2、GS または Fs 仮想マシンを新しく作成します。

#### III.Azure ポータルで Premium Storage データ ディスクを接続する

1. Azure Portal で新規または既存の DS、DSv2、GS または Fs VM を探します。
2. VM の **[すべての設定]** で、**[ディスク]** に移動し、**[新しいディスクの接続]** をクリックします。
3. データ ディスクの名前を入力し、**[種類]** として **[Premium]** を選択します。**[サイズ]** と **[ホスト キャッシュ]** で目的の設定を選択します。

	![Premium ディスク][Image1]

詳細な手順については、[Azure ポータルでデータ ディスクを接続する方法](../virtual-machines/virtual-machines-windows-attach-disk-portal.md)に関するページをご覧ください。

#### IV.Azure ポータルでディスク キャッシュ ポリシーを変更する

1. Azure Portal で新規または既存の DS、DSv2、GS または Fs VM を探します。
2. VM の [すべての設定] で、[ディスク] に移動し、変更するディスクをクリックします。
3. ホスト キャッシュ オプションを目的の値 (None、ReadOnly、ReadWrite) に変更します。

>[AZURE.WARNING] Azure ディスクのキャッシュ設定を変更すると、対象となるディスクをデタッチして再アタッチします。オペレーティング システム ディスクの場合は、VM が再起動されます。ディスク キャッシュの設定を変更する前に、この中断の影響を受ける可能性があるすべてのアプリケーションまたはサービスを停止します。

### Premium Storage を使用する Azure 仮想マシンを Azure PowerShell で作成する

#### I.Azure PowerShell で Premium Storage アカウントを作成する

この PowerShell の例では、新しい Premium Storage アカウントを作成してこのアカウントを使用するデータ ディスクを新しい Azure 仮想マシンにアタッチする方法を説明します。

1. 「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」に記載された手順に従って、PowerShell 環境を設定します。
2. PowerShell コンソールを起動してサブスクリプションに接続し、コンソール ウィンドウで次の PowerShell コマンドレットを実行します。この PowerShell ステートメントで示されているように、Premium Storage アカウントを作成するときは、**Type** パラメーターを **Premium\_LRS** に指定する必要があります。

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

#### II.Azure PowerShell で Azure 仮想マシンを作成する

次に、DS シリーズの VM を作成し、コンソール ウィンドウで次の PowerShell コマンドレットを実行して Premium Storage を指定します。同様の手順で GS シリーズの VM を作成できます。コマンドでは適切な VM サイズを指定してください。例: Standard\_GS2

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

#### III.Azure PowerShell で Premium Storage データ ディスクを接続する

VM のディスク領域を増やしたい場合は、VM の作成後に、コンソール ウィンドウで次の PowerShell コマンドレットを実行して既存の Premium Storage でサポートされる VM に新しいデータ ディスクをアタッチします。

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

#### IV.Azure PowerShell でディスク キャッシュ ポリシーを変更する

ディスク キャッシュ ポリシーを更新するには、接続されているデータ ディスクの LUN 番号を書き留めます。次のコマンドを実行して、LUN 番号 2 に接続されているデータ ディスクを ReadOnly に変更します。

		Get-AzureVM "myservice" -name "MyVM" | Set-AzureDataDisk -LUN 2 -HostCaching ReadOnly | Update-AzureVM

>[AZURE.WARNING] Azure ディスクのキャッシュ設定を変更すると、対象となるディスクをデタッチして再アタッチします。オペレーティング システム ディスクの場合は、VM が再起動されます。ディスク キャッシュの設定を変更する前に、この中断の影響を受ける可能性があるすべてのアプリケーションまたはサービスを停止します。

### Premium Storage を使用する Azure 仮想マシンを Azure コマンド ライン インターフェイスで作成する

[Azure コマンド ライン インターフェイス](../xplat-cli-install.md) (Azure CLI) は、Azure Platform を使用するための、一連のオープン ソース クロスプラットフォーム コマンドを提供します。以下の例では、Azure CLI (バージョン 0.8.14 以降) を使用して、Premium Storage アカウントを作成する方法、新しい仮想マシンを作成する方法、新しいデータ ディスクを Premium Storage アカウントからアタッチする方法を示します。

#### I.Azure CLI で Premium Storage アカウントを作成する

````
azure storage account create "premiumtestaccount" -l "west us" --type PLRS
````

#### II.Azure CLI で DS シリーズの仮想マシンを作成する

	azure vm create -z "Standard_DS2" -l "west us" -e 22 "premium-test-vm"
		"b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ja-JP-30GB" -u "myusername" -p "passwd@123"

仮想マシンに関する情報を表示する

	azure vm show premium-test-vm

#### III.Azure CLI で新しいプレミアム データを接続する

	azure vm disk attach-new premium-test-vm 20 https://premiumstorageaccount.blob.core.windows.net/vhd-store/data1.vhd

新しいデータ ディスクに関する情報を表示する

	azure vm disk show premium-test-vm-premium-test-vm-0-201502210429470316

#### IV.ディスク キャッシュ ポリシーを変更する

Azure CLI を使用して、いずれかのディスクのキャッシュ ポリシーを変更するには、次のコマンドを実行します。

		$ azure vm disk attach -h ReadOnly <VM-Name> <Disk-Name>

キャッシュ ポリシーのオプションは、ReadOnly、None、ReadWrite があります。その他のオプションについては、次のコマンドを実行してヘルプを参照してください。

		azure vm disk attach --help

>[AZURE.WARNING] Azure ディスクのキャッシュ設定を変更すると、対象となるディスクをデタッチして再アタッチします。オペレーティング システム ディスクの場合は、VM が再起動されます。ディスク キャッシュの設定を変更する前に、この中断の影響を受ける可能性があるすべてのアプリケーションまたはサービスを停止します。

## FAQ

1. **Premium および Standard データ ディスクの両方を Premium Storage でサポートされる VM に接続できますか?**

	はい。Premium および Standard データ ディスクの両方を Premium Storage でサポートされる VM に接続できます。

2. **Premium および Standard データ ディスクの両方を D、Dv2、G または F シリーズ VM に接続できますか?**

	いいえ。Standard データ ディスクを接続できるのは、Premium Storage でサポートされていないシリーズの VM です。

3. **Premium データ ディスクをサイズが 80 GB の既存の VHD から作成した場合、どのくらいの料金がかかりますか?**

	80 GB の VHD から作成した Premium データ ディスクは、次の使用可能な Premium ディスク サイズ、P10 ディスクとして考慮されます。従って、P10 ディスクの価格が課金されます。

4. **Premium Storage の使用ではトランザクションのコストは発生しますか?**

	これは、特定の数の IOPS およびスループットでプロビジョニングされる各ディスク サイズに固定のコストが設けられています。それ以外にかかるコストは、送信帯域幅とスナップショットの容量のみです (該当する場合)。詳細については、「[Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/)」をご覧ください。

5. **Premium Storage でサポートされる VM のブート診断はどこに保存できますか?**

	Standard ストレージ アカウントを作成して、Premium Storage でサポートされるシリーズの VM のブート診断を保存できます。

6. **ディスク キャッシュから入手できる IOPS 数とスループットを教えてください。**

	DS シリーズのキャッシュとローカル SSD の制限の合計は、コアあたり 4000 IOPS、またコアあたり 33 MB/秒 です。GS シリーズでは、コアあたり 5000 IOP、コアあたり 50 MB/秒です。

7. **Premium Storage でサポートされるシリーズの VM のローカル SSD とは何ですか?**

	ローカル SSD とは、Premium Storage でサポートされるシリーズの VM に含まれている一時的なストレージです。この一時ストレージに追加の料金は発生しません。これは Azure Blob Storage に永続化されないため、アプリケーション データの保存にこの一時的なストレージを使用しないことをお勧めします。

8. **Standard Storage アカウントを Premium Storage アカウントに変換できますか?**

	いいえ。Standard Storage アカウントを Premium Storage アカウントに変換することはできません。また、Premium Storage アカウントを Standard Storage アカウントに変換することもできません。該当する新しいストレージ アカウントに対応する種類とコピー データを提供する新しいストレージ アカウントを作成する必要があります。

9. **D シリーズ VM を DS シリーズ VM に変換する方法を教えてください。**

	移行ガイドの「[Azure Premium Storage への移行](storage-migration-to-premium-storage.md)」を参照して、Standard Storage アカウントを使用する D シリーズ VM のワークロードを Premium Storage アカウントを使用する DS シリーズ VM に移行します。

## 次のステップ

Azure Premium Storage の詳細については、以下の記事を参照してください。

### Design and implement with Azure Premium Storage (Azure Premium Storage での設計と実装)

- [Premium Storage を使用した高パフォーマンスのための設計](storage-premium-storage-performance.md)
- [Azure Premium Storage での BLOB サービス操作の使用](http://go.microsoft.com/fwlink/?LinkId=521969)

### 操作ガイダンス

- [Azure Premium Storage への移行](storage-migration-to-premium-storage.md)

### ブログ記事

- [Azure Premium Storage を一般に提供](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
- [Announcing the GS-Series: Adding Premium Storage Support to the Largest VMs in the Public Cloud (GS シリーズの提供開始を発表: パブリック クラウドの最大の VM に Premium Storage サポートを追加)](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)

[Image1]: ./media/storage-premium-storage/Azure_attach_premium_disk.png

<!---HONumber=AcomDC_0921_2016-->