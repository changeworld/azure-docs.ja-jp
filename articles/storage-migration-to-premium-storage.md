<properties 
    pageTitle="Azure Premium Storage への移行 | Microsoft Azure" 
    description="Azure Virtual Machines で実行される I/O 集中型ワークロード向けに高パフォーマンスで待機時間の短いディスクをサポートするために Azure Premium Storage へ移行します。" 
    services="storage" 
    documentationCenter="na" 
    authors="tamram" 
    manager="adinah" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/14/2015" 
    ms.author="tamram"/>


# Azure Premium Storage への移行

## 概要

Premium Storage は、最新テクノロジーのソリッド ステート ドライブ \(SSD\) にデータを保存し、Azure Virtual Machines で実行される I/O 集中型ワークロード向けに高パフォーマンスで待機時間の短いディスク サポートを提供します。Premium Storage を使用すると、アプリケーションに VM あたり最大 32 TB のストレージを設定できます。さらに VM あたり 50,000 IOPS \(1 秒あたりの入力/出力操作\) を実現でき、読み取り操作の待ち時間も非常に短くなります。この記事では、お使いのディスクである仮想マシン \(VM\) をオンプレミス、Standard Storage、または別のクラウド プラットフォームから Azure Premium Storage へ移行する方法に関するガイドラインについて説明します。Azure Premium Storage の詳細については、「Premium Storage: [Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](storage-premium-storage-preview-portal.md)」を参照してください。

## 開始する前に 

### 前提条件
- Azure サブスクリプションが必要です。サブスクリプションがない場合は、1 か月間の[無料評価版](http://azure.microsoft.com/pricing/free-trial/)サブスクリプションを作成するか、「[Azure の料金設定](http://azure.microsoft.com/pricing/)」でさらに多くのオプションを利用することができます。 
- PowerShell コマンドレットを実行するには、Microsoft Azure PowerShell モジュールが必要です。このモジュールをダウンロードするには、[Microsoft Azure のダウンロード](http://azure.microsoft.com/downloads/) ページにアクセスしてください。
- Premium Storage で実行されている Azure VM を使用する場合は、DS シリーズ VM を使用する必要があります。DS シリーズの VM では、Standard Storage ディスクと Premium Storage ディスクの両方を使用できます。Premium Storage ディスクの方が、将来的により多くの VM の種類を使用できます。使用可能な Azure VM ディスクの種類とサイズの詳細については、「[Azure の仮想マシンおよびクラウド サービスのサイズ](http://msdn.microsoft.com/library/azure/dn197896.aspx)」を参照してください。 

### 考慮事項 

#### VM サイズ 
以下に、DS シリーズ VM のサイズとその特性をまとめています。これらの Premium Storage サービスのパフォーマンス特性を確認し、Azure ディスクに最適なオプションとワークロードに最適な VM を選択してください。ディスク トラフィックが流れるのに十分な帯域幅が VM で利用できることを確認します。

|VM サイズ|CPU コア数|最大IOPS|最大ディスク帯域幅|
|:---:|:---:|:---:|:---:|
|**STANDARD\_DS1**|1|3,200|32 MB/秒|
|**STANDARD\_DS2**|2|6,400|64 MB/秒|
|**STANDARD\_DS3**|4|12,800|128 MB/秒|
|**STANDARD\_DS4**|8|25,600|256 MB/秒|
|**STANDARD\_DS11**|2|6,400|64 MB/秒|
|**STANDARD\_DS12**|4|12,800|128 MB/秒|
|**STANDARD\_DS13**|8|25,600|256 MB/秒|
|**STANDARD\_DS14**|16|50,000|512 MB/秒|

#### ディスク サイズ 
VM で使用できるディスクには 3 種類あり、それぞれに特定の IOPS とスループットの制限があります。VM のディスクの種類を選択する場合は、容量、パフォーマンス、スケーラビリティ、最大負荷に関するアプリケーションのニーズに基づいて、これらの制限を考慮してください。

|Premium Storage ディスク タイプ|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|ディスク サイズ|128 GB|512 GB|1024 GB \(1 TB\)|
|ディスクあたりの IOPS|500|2300|5000|
|ディスクあたりのスループット|100 MB/秒|150 MB/秒|200 MB/秒|

#### ストレージ アカウントのスケーラビリティ ターゲット

Premium Storage アカウントには、[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](http://msdn.microsoft.com/library/azure/dn249410.aspx)に加え、次のスケーラビリティ ターゲットがあります。アプリケーションの要件が単一のストレージ アカウントのスケーラビリティ ターゲットを上回った場合は、複数のストレージ アカウントを使用するようにアプリケーションを構築し、それらのストレージ アカウント間でデータを分割します。

|合計アカウント容量|ローカル冗長ストレージ アカウントの合計帯域幅|
|:--|:---|
|ディスク容量: 35 TB<br />スナップショット容量: 10 TB|受信と送信を合わせて最大 50 GB/秒|

Premium Storage の仕様の詳細については、「[Premium Storage を使用するときの拡張性とパフォーマンスのターゲット](storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whja-jping-premium-storage)」を参照してください。

#### 追加のデータ ディスク 
ワークロードに応じて、VM にデータ ディスクを追加する必要があるかどうかを判断します。複数の永続データ ディスクを VM に接続できます。必要に応じて、ディスク全体をストライピングして容量を増やし、ボリュームのパフォーマンスを高めることができます。[記憶域スペース](http://technet.microsoft.com/library/hh831739.aspx)を使用して Premium Storage データ ディスクをストライピングする場合は、使用するディスクごとに 1 つの列で構成する必要があります。そうしない場合は、ディスク全体のトラフィックの配分が不均等になるため、ストライプ ボリュームの全体的なパフォーマンスが低下する可能性があります。Linux VM の場合は、mdadm ユーティリティを使用すると同じ結果を得ることができます。詳細については、[Linux でのソフトウェア RAID の構成](virtual-machines-linux-configure-raid.md)に関する記事を参照してください。

#### ディスク キャッシュ ポリシー 
既定では、ディスクのキャッシュ ポリシーは、すべてのPremium データ ディスクに対して「読み取り専用」、VM にアタッチされた Premium オペレーティング システム ディスクに対して「読み取り/書き込み」です。アプリケーションの IO パフォーマンスを最適化するには、この構成をお勧めします。書き込み量の多いディスクや書き込み専用のディスク \(SQL Server ログ ファイルなど\) の場合は、ディスク キャッシュを無効にすることで、アプリケーションのパフォーマンスを向上できる場合があります。既存のデータ ディスクのキャッシュ設定は、Azure ポータル、または *Set-azuredatadisk* コマンドレットの *-HostCaching* パラメーターを使用して更新できます。

#### 場所 
Azure Premium Storage を使用できる場所を選択します。使用可能な場所の最新情報については、「[Premium Storage について理解しておくべきこと](storage-premium-storage-preview-portal.md#important-things-to-know-about-premium-storage)」を参照してください。VM のディスクを保存するストレージ アカウントと同じリージョンにある VM は、個々のリージョン内にある場合よりも優れたパフォーマンスを提供します。

#### Azure VM のその他の構成設定 

Azure VM を作成するときに、特定の VM の設定を構成するよう求められます。VM の有効期間中は固定されている設定がいくつかありまりますが、後で変更または追加できる設定もあることを忘れないでください。このような Azure VM の構成設定を確認し、ワークロードの要件に合わせて適切に構成されていることを確認します。詳細については、[VM の構成設定](https://msdn.microsoft.com/library/azure/dn763935.aspx)に関するページを参照してください。

## 移行のための VHD の準備 

次のセクションでは、すぐに移行できるように VM から VHD を準備するためのガイドラインを示します。VHD は次のいずれかになります。
 
- 複数の Azure VM の作成に使用できる一般化されたオペレーティング システム イメージ。  
- 1 つの Azure 仮想マシン インスタンスで使用できるオペレーティング システム ディスク。  
- 永続的なストレージ用に Azure VM に接続できるデータ ディスク。

### 前提条件

- Azure サブスクリプション、ストレージ アカウント、および VHD のコピー先となるそのストレージ アカウント内のコンテナー。コピー先のストレージ アカウントには、要件に応じて Standard Storage アカウントまたは Premium Storage アカントを使用できることに注意してください。 
- VHD から複数の VM インスタンスを作成する予定の場合にその VHD を一般化するツール。たとえば、Windows の sysprep や Ubuntu の virt-sysprep があります。ストレージ アカウントに VHD ファイルをアップロードするツール。たとえば、[AzCopy](storage-use-azcopy.md) や [Azure Storage エクスプローラー](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)があります。このガイドでは、AzCopy ツールを使用した手順について説明します。帯域幅が制限された大量のデータの場合は、[Microsoft Azure Import/Export サービス](storage-import-export-service.md)を使用して、ハード ディスク ドライブを Azure データセンターへ配布してデータを転送することを検討できます。Import/Export サービスを使用すると、データのみを Standard Storage アカウントにコピーできます。データが Standard Storage アカウントに配置されたら、[copy blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) または AzCopy を使用して、そのデータを Premium Storage アカウントに転送できます。
- VHD をコピーするツールは、VHD と同じリージョン内の Azure VM で実行できます。
- Microsoft Azure でサポートされているのは、固定サイズの VHD ファイルのみです。VHDX ファイルまたは動的 VHD はサポートされていません。動的 VHD を使用している場合は、[Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) コマンドレットを使用して固定サイズに変換できます。

### VHD の準備

|シナリオ|手順|
|:---|:---|
|複数の VM インスタンスを作成するために一般化されたオペレーティング システム VHD|<p>**複数の汎用的な Azure VM インスタンスの作成に使用する VHD** をアップロードする場合は、最初に、Sysprep ユーティリティを使用して VHD を一般化する必要があります。これは、オンプレミスまたはクラウド内の VHD に適用されます。Sysprep は、VHD からマシン固有の情報を削除します。</p><p>\*\*重要:\*\* 一般化する前に、スナップショットを作成するか VM をバックアップしてください。Sysprep を実行すると、VM インスタンスが削除されます。</p>Windows OS VHD に対して Sysprep を実行するには、以下の手順に従います。<br />Sysprep コマンドを実行するには、仮想マシンのシャットダウンが必要になることに注意してください。Sysprep の詳細については、[Sysprep の概要](http://technet.microsoft.com/library/hh825209.aspx)に関するページまたは[Sysprep テクニカル リファレンス](http://technet.microsoft.com/library/cc766049(v=ws.10).aspx)に関するページを参照してください。<ul><li>管理者としてコマンド プロンプト ウィンドウを開きます。</li><li>次のコマンドを入力して Sysprep を開きます。<br />\*\*%windir%\\system32\\sysprep\\sysprep.exe\*\*</li><li>システム準備ツールで、\[システムの OOBE \(Out-of-Box Experience\) に入る\] を選択し、\[一般化する\] チェック ボックスをオンにして、**\[シャットダウン\]** を選択し、**\[OK\]** をクリックします。</li><li>これにより、オペレーティング システムは一般化され、システムがシャットダウンされます。![][1]</li></ul>Ubuntu VM の場合は、virt-sysprep を使用すると同じ結果を得られます。詳細については、[virt-sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) に関するページを参照してください。また、その他の Linux オペレーティング システムについては、オープン ソースの [Linux サーバー プロビジョニング ソフトウェア](http://www.cyberciti.biz/tips/server-provisioning-software.html)に関する記述も参照してください。|
|単一の VM インスタンスを作成するための一意のオペレーティング システム VHD|マシン固有の情報を必要とする VM 上で実行中のアプリケーションがある場合は、VHD を一般化しないでください。**一意の Azure VM インスタンスの作成に使用できるのは、一般化されていない VHD です。** たとえば、VHD にドメイン コントローラーがある場合は、Sysprep を実行すると、ドメイン コントローラーとしては無効になります。VHD を一般化する前に、VM 上で実行中のアプリケーションと、これらに対する Sysprep の影響を確認してください。|
|VM インスタンスに接続するデータ ディスク VHD|移行するデータ ディスクがクラウド ストレージ内にある場合は、これらのデータ ディスクを使用する VM を必ずシャットダウンする必要があります。オンプレミスのデータ ディスクの場合は、一貫性のある VHD を作成します。|
VHD の準備が完了したら、次のセクションで説明する手順に従って、VHD を Azure Storage にアップロードし、オペレーティング システム イメージ、プロビジョニングされたオペレーティング システム ディスク、またはプロビジョニングされたデータ ディスクとして登録します。

## Azure Storage への VHD のコピー

### ソース

|シナリオ|手順|
|:---|:---|
|Azure Storage から VHD をコピーする|Azure Standard Storage アカウントから Azure Premium Storage アカウントへ VHD を移行する場合は、VHD コンテナーのソース パス、VHD ファイル名、ソース ストレージ アカウントのストレージ アカウント キーをコピーする必要があります。<ul><li>Azure ポータルへ移動し、\[Virtual Machines\]、\[ディスク\] の順に選択します。</li><li>\[場所\] 列から VHD のコンテナー URL をコピーして保存します。https://*AccountName*.blob.core.windows.net/*ContainerName*/</li></ul>|
|Azure 以外のクラウドから VHD をコピーする|Azure 以外のクラウド ストレージから Azure へ VHD を移行する場合は、最初に VHD をローカル ディレクトリにエクスポートする必要があります。VHD が保存されているローカル ディレクトリの完全なソース パスをコピーします。<ul><li>AWS を使用している場合は、Amazon S3 バケット内の VHD に EC2 インスタンスをエクスポートします。「[Amazon EC2 インスタンスのエクスポート](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExportingEC2Instances.html)」で説明されている手順に従い、Amazon EC2 コマンド ライン インターフェイス \(CLI\) ツールをインストールし、EC2 インスタンスを VHD ファイルにエクスポートするコマンドを実行します。コマンドを実行する場合は、DISK\_IMAGE\_FORMAT 変数に必ず **VHD** を使用してください。エクスポートされた VHD ファイルは、その処理中に指定した Amazon S3 バケットに保存されます。</li></ul>![][2]<ul><li>VHD ファイルを S3 バケットからダウンロードします。VHD ファイルを選択し、**\[操作\]**、**\[ダウンロード\]** の順に選択します。</li></ul>![][3]|
|オンプレミスから VHD をコピーする|オンプレミス環境から VHD を移行する場合は、VHD の保存場所の完全なソース パスが必要です。これは、サーバーの場所の場合もあれば、ファイル共有の場合もあります。

### 変換先 

VHD を管理するためにストレージ アカウントを作成します。VHD の保存場所を計画する際は、次の点を考慮してください。

- 対象のストレージ アカウントは、アプリケーション要件に応じて Standard Storage または Premium Storage になります。 
- ストレージ アカウントの場所は、最終ステージで作成する DS シリーズの Azure VM と同じにする必要があります。必要に応じて、新しいストレージ アカウントにコピーするか、同じストレージ アカウントの使用を計画することができます。
- 次のステージ用に、移行先ストレージ アカウントのストレージ アカウント キーをコピーして保存します。
- データ ディスクの場合、一部のデータ ディスク \(たとえば、負荷の軽いストレージのあるディスク\) を Standard Storage アカウントで、IOP の高い一部のディスクを Premium Storage アカウントで保持することもできます。

### AzCopy による VHD のコピー

AzCopy を使用すると、インターネット経由で VHD を簡単にアップロードできます。VHD のサイズによっては、この処理に時間がかかる場合があります。このオプションを使用する場合は、ストレージ アカウントの送受信制限を確認することを忘れないでください。Azure Storage の制限は、[ここ](azure-subscription-service-limits#storage-limits)で確認できます。

1. 次のリンクから AzCopy をダウンロードしてインストールします: [AzCopy の最新バージョン](http://aka.ms/downloadazcopy)。  
2. Azure PowerShell を開き、AzCopy がインストールされているフォルダーに移動します。  
3. 次のコマンドを使用して、"Source" から "Destination" に VHD ファイルをコピーします。**AzCopy /Source:** *&lt;Source&gt;* **/SourceKey:** *&lt;Source-Storage-Key&gt;* **/Destination:** *&lt;Destination&gt;* **/DestKey:** *&lt;Dest-Storage-Key&gt;* **/BlobType:page /Pattern:** *&lt;File-Name&gt;*  
  - *&lt;Source&gt;*: VHD が格納されているフォルダーの場所またはストレージ コンテナー URL。    
 - *&lt;Source-Storage-Key&gt;*: コピー元ストレージ アカウントのストレージ アカウント キー。  
 - *&lt;Destination&gt;*: VHD のコピー先のストレージ コンテナー URL。
 - *&lt;Dest-Storage-Key&gt;*: コピー先ストレージ アカウントのストレージ アカウント キー。
 - /BlobType:page: コピー先がページ BLOB であることを指定。
 - *&lt;File-Name&gt;*: コピーする VHD ファイルの名前。
 - /Pattern:\*&lt;File-Name&gt;:\* コピーする VHD のファイル名を指定。
   
このコマンドによって、*&lt;Source&gt;* 内のすべてのファイルが *&lt;Destination&gt;* コンテナーにコピーされます。AzCopy ツールの使用方法の詳細については、[AzCopy コマンド ライン ユーティリティの概要](storage-use-azcopy.md)に関するページを参照してください。
### VHD をアップロードするためのその他のオプション 

- [Azure Storage Copy Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) 
- [Azure Import/Export サービス](https://msdn.microsoft.com/library/dn529096.aspx) 

>[AZURE.NOTE]Import/Export は、Standard Storage アカウントへのコピーのみに使用できます。Standard Storage アカウントから Premium Storage アカウントにコピーするには、AzCopy などのツールを使用する必要があります。

## Premium Storage を使用した Azure VM の作成

目的のストレージ アカウントに VHD がアップロードされたら、このセクションの手順に従って、シナリオに応じて VHD を OS イメージまたは OS ディスクとして登録し、その VHD から VM インスタンスを作成します。作成後に、データ ディスク VHD を VM に接続できます。

### VHD の登録
  
OS VHD から VM を作成するか、新しい VM にデータ VHD を接続するには、最初に VHD を登録する必要があります。シナリオに応じて次の手順に従います。

<table>
<tr>
<th>シナリオ</th>
<th>VHD を登録する手順</th>
</tr>
<tr>
<td>複数の Azure VM インスタンスを作成するために一般化されたオペレーティング システム VHD</td>
<td><p>一般化された OS イメージ VHD をストレージ アカウントにアップロードした後、そのVHD を <b>Azure VM イメージ</b>として登録すると、そこから 1 つ以上の VM インスタンスを作成できます。</p><p>次の PowerShell コマンドレットを使用して、VHD を Azure VM OS イメージとして登録します。VHD のコピー先の完全なコンテナー URL を入力します。</p><p><code>Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows</code></p>この新しい Azure VM イメージの名前をコピーして保存します。上記の例では "OSImageName" です。</td>
</tr>
</tr>
<td>単一の Azure VM インスタンスを作成するための一意のオペレーティング システム VHD</td>
<td><p>一意の OS VHD をストレージ アカウントにアップロードした後、その VHD を <b>Azure OS ディスク</b>として登録すると、そこから VM インスタンスを作成できます。</p><p>次の PowerShell コマンドレットを使用して、VHD を Azure OS ディスクとして登録します。VHD のコピー先の完全なコンテナー URL を入力します。</p><code>"https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "OS Disk" -OS "Windows"</code><p>この新しい Azure OS ディスクの名前をコピーして保存します。上記の例では "OSDisk" です。</p></td>
</tr>
</tr>
<td>新しい Azure VM インスタンスに接続するデータ ディスク VHD</td>
<td><p>データ ディスク VHD をストレージ アカウントにアップロードした後、その VHD を Azure データ ディスクとして登録すると、それを新しい DS シリーズ Azure VM インスタンスに接続できます。</p><p>次の PowerShell コマンドレットを使用して、VHD を Azure データ ディスクとして登録します。VHD のコピー先の完全なコンテナー URL を入力します。</p><code>Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "Data Disk"</code><p>この新しい Azure データ ディスクの名前をコピーして保存します。上記の例では "DataDisk" です。</p></td>
</tr>
</table>

### DS シリーズ Azure VM の作成  

OS イメージまたは OS ディスクを登録したら、新しい DS シリーズ Azure VM インスタンスを作成できます。登録したオペレーティング システム イメージまたはオペレーティング システム ディスクの名前を使用します。Premium Storage レベルから VM の種類を選択します。次の例では、"Standard\_DS2" という VM サイズを使用します。

>[AZURE.NOTE]ディスク サイズを更新し、容量、パフォーマンス要件、使用可能な Azure ディスク サイズに適していることを確認します。

次の PowerShell コマンドレットの手順に従い、新しい VM を作成します。

最初に、共通のパラメーターを設定します。

	$vmSize ="Standard_DS2"
	$adminName = "youradmin"
	$adminPassword = "yourpassword"
	$vmName ="yourVM"
	$location = "East US 2"  

次に、シナリオに応じて、登録した OS イメージまたは OS ディスクから Azure VM インスタンスを作成します。

<table>
<tr>
<th>シナリオ</th>
<th>手順</th>
</tr>
<tr>
<td>複数の Azure VM インスタンスを作成するために一般化されたオペレーティング システム VHD</td>
<td>登録した <b>Azure OS イメージ</b>を使用して、1 つ以上の新しい DS シリーズ Azure VM インスタンスを作成します。新しい VM を作成する際に、次のように、VM 構成でこの OS イメージの名前を指定します。</br></br>
<code>$OSImage = Get-AzureVMImage –ImageName "OSImageName" $vm = New-AzureVMConfig -Name "NewVM" -InstanceSize $vmSize -ImageName $OSImage.ImageName</code>
</br></br>
<code>Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm New-AzureVM -ServiceName "NewVM"</code></td></tr>  
<tr>
<td>単一の Azure VM インスタンスを作成するための一意のオペレーティング システム VHD</td>
<td>登録した <b>Azure OS ディスク</b>を使用して、新しい DS シリーズ Azure VM インスタンスを作成します。新しい VM を作成する際に、次のように、VM 構成でこの OS ディスクの名前を指定します。</br></br>
<code>$OSDisk = Get-AzureDisk –DiskName "OSDisk" $vm = New-AzureVMConfig -Name "NewVM" -InstanceSize $vmSize -DiskName $OSDisk.DiskName</code>
</br></br>  
<code>Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm New-AzureVM -ServiceName "NewVM"</code>
</td>
</tr>
</table>

クラウド サービス、リージョン、ストレージ アカウント、可用性セット、キャッシュ ポリシーなど、その他の Azure VM 情報を指定します。VM インスタンスは、関連付けられたオペレーティング システムまたはデータ ディスクと併置する必要があるため、選択したクラウド サービス、リージョン、ストレージ アカウントはすべて、それらのディスクの基になる VHD と同じ場所に配置する必要がある点に注意してください。

### データ ディスクの接続  

最後に、データ ディスク VHD を登録した場合は、それらを新しい DS シリーズ Azure VM に接続します。

次の PowerShell コマンドレットを使用して、新しい VM にデータ ディスクに接続し、キャッシュ ポリシーを指定します。次の例では、キャッシュ ポリシーは読み取り専用 \(ReadOnly\) に設定されています。

	$vmName ="yourVM"
	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
	Add-AzureDataDisk -ImportFrom -DiskName “DataDisk” -LUN 0 –HostCaching ReadOnly –VM $vm | Update-AzureVM  

>[AZURE.NOTE]このガイドで説明されていないアプリケーションをサポートするには、特定の手順が必要になる場合があります。


## 次のステップ  

仮想マシンの移行に関する具体的なシナリオについては、次のリソースを参照してください。

- [ストレージ アカウント間での Azure 仮想マシンの移行](http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)  
- [Windows Server VHD の作成と Azure へのアップロード](virtual-machines-create-upload-vhd-windows-server.md)  
- [Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](virtual-machines-linux-create-upload-vhd.md)  
- [Amazon AWS から Microsoft Azure への仮想マシンの移行](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)  

また、Azure Storage と Azure Virtual Machines の詳細については、次のリソースも参照してください。

- [Azure Storage](http://azure.microsoft.com/documentation/services/storage/)   
- [Azure Virtual Machines](http://azure.microsoft.com/documentation/services/virtual-machines/)  
- [: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](storage-premium-storage-preview-portal.md)  



[1]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image1.png
[2]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image2.png
[3]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image3.png
<!--HONumber=52-->
