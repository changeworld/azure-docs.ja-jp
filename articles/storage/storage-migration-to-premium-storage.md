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
    ms.date="07/06/2015" 
    ms.author="tamram"/>


# Azure Premium Storage への移行

## 概要

Premium Storage は、最新テクノロジーのソリッド ステート ドライブ (SSD) にデータを保存し、Azure Virtual Machines で実行される I/O 集中型ワークロード向けに高パフォーマンスで待機時間の短いディスク サポートを提供します。Premium Storage を使用すると、アプリケーションに VM あたり最大 32 TB のストレージを設定できます。さらに VM あたり 50,000 IOPS (1 秒あたりの入力/出力操作) を実現でき、読み取り操作の待ち時間も非常に短くなります。この記事では、お使いのディスクである仮想マシン (VM) をオンプレミス、Standard Storage、または別のクラウド プラットフォームから Azure Premium Storage へ移行する方法に関するガイドラインについて説明します。Azure Premium Storage の詳細については、「[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](storage-premium-storage-preview-portal.md)」を参照してください。

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
|ディスク サイズ|128 GB|512 GB|1024 GB (1 TB)|
|ディスクあたりの IOPS|500|2300|5000|
|ディスクあたりのスループット|100 MB/秒|150 MB/秒|200 MB/秒|

#### ストレージ アカウントのスケーラビリティ ターゲット

Premium Storage アカウントには、[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](http://msdn.microsoft.com/library/azure/dn249410.aspx)に加え、次のスケーラビリティ ターゲットがあります。アプリケーションの要件が単一のストレージ アカウントのスケーラビリティ ターゲットを上回った場合は、複数のストレージ アカウントを使用するようにアプリケーションを構築し、それらのストレージ アカウント間でデータを分割します。

|合計アカウント容量|ローカル冗長ストレージ アカウントの合計帯域幅|
|:--|:---|
|ディスク容量: 35 TB<br />スナップショット容量: 10 TB|受信と送信を合わせて最大 50 GB/秒|

Premium Storage の仕様の詳細については、「[Premium Storage を使用するときの拡張性とパフォーマンスのターゲット](storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whja-jping-premium-storage)」を参照してください。

#### 追加のデータ ディスク 
ワークロードに応じて、VM にデータ ディスクを追加する必要があるかどうかを判断します。複数の永続データ ディスクを VM に接続できます。必要に応じて、ディスク全体をストライピングして容量を増やし、ボリュームのパフォーマンスを高めることができます。[記憶域スペース](http://technet.microsoft.com/library/hh831739.aspx)を使用して Premium Storage データ ディスクをストライピングする場合は、使用するディスクごとに 1 つの列で構成する必要があります。そうしない場合は、ディスク全体のトラフィックの配分が不均等になるため、ストライプ ボリュームの全体的なパフォーマンスが低下する可能性があります。Linux VM の場合は、*mdadm* ユーティリティを使用すると同じ結果を得ることができます。詳細については、[Linux でのソフトウェア RAID の構成](../virtual-machines-linux-configure-raid.md)に関する記事を参照してください。

#### ディスク キャッシュ ポリシー 
既定では、ディスクのキャッシュ ポリシーは、すべてのPremium データ ディスクに対して「*読み取り専用*」、VM にアタッチされた Premium オペレーティング システム ディスクに対して「*読み取り/書き込み*」です。アプリケーションの IO パフォーマンスを最適化するには、この構成をお勧めします。書き込み量の多いディスクや書き込み専用のディスク (SQL Server ログ ファイルなど) の場合は、ディスク キャッシュを無効にすることで、アプリケーションのパフォーマンスを向上できる場合があります。既存のデータ ディスクのキャッシュ設定は、Azure ポータル、または *Set-azuredatadisk* コマンドレットの *-HostCaching* パラメーターを使用して更新できます。

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

VM を移行するには、次のものが必要です。

- Azure サブスクリプション、ストレージ アカウント、および VHD のコピー先となるそのストレージ アカウント内のコンテナー。コピー先のストレージ アカウントには、要件に応じて Standard Storage アカウントまたは Premium Storage アカントを使用できることに注意してください。 
- VHD から複数の VM インスタンスを作成する予定の場合にその VHD を一般化するツール。たとえば、Windows の sysprep や Ubuntu の virt-sysprep があります。 
- ストレージ アカウントに VHD ファイルをアップロードするツール。たとえば、[AzCopy](storage-use-azcopy.md) や [Azure Storage エクスプローラー](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)があります。このガイドでは、AzCopy ツールを使用して VHD をコピーする手順について説明します。 

> [AZURE.NOTE]パフォーマンスを最適化するには、コピー先のストレージ アカウントと同じリージョンにある Azure VM からこれらのツールのいずれかを実行して VHD をコピーします。別のリージョンの Azure VM から VHD をコピーする場合は、パフォーマンスが減速することがあります。
>
> 制限された帯域幅で大量のデータをコピーするには、[Microsoft Azure Import/Export サービス](storage-import-export-service.md)を使用して、ハード ディスク ドライブを Azure データセンターへ配布してデータを転送することを検討できます。Azure Import/Export サービスを使用すると、データを Standard ストレージ アカウントのみにコピーできます。データが Standard ストレージ アカウントに配置されたら、[Copy Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) または AzCopy を使用して、そのデータを Premium ストレージ アカウントに転送できます。
> 
> Microsoft Azure でサポートされているのは、固定サイズの VHD ファイルのみです。VHDX ファイルまたは動的 VHD はサポートされていません。動的 VHD を使用している場合は、[Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) コマンドレットを使用して固定サイズに変換できます。

### VHD の準備のシナリオ

VHD を準備するためのいくつかのシナリオについて説明します。

#### 複数の VM インスタンスを作成するために一般化されたオペレーティング システム VHD

複数の汎用的な Azure VM インスタンスの作成に使用する VHD をアップロードする場合は、最初に、Sysprep ユーティリティを使用して VHD を一般化する必要があります。これは、オンプレミスまたはクラウド内の VHD に適用されます。Sysprep は、コンピューター固有の情報を VHD から削除します。

>[AZURE.IMPORTANT]VHD を一般化する前に、スナップショットを取得または VM をバックアップします。Sysprep を実行すると VM インスタンスが削除されます。次の手順に従って、Windows OS VHD に Sysprep を実行します。Sysprep コマンドを実行する場合、仮想マシンをシャットダウンする必要があります。Sysprep の詳細については、[Sysprep の概要](http://technet.microsoft.com/library/hh825209.aspx)に関するページまたは [Sysprep Technical Reference](http://technet.microsoft.com/library/cc766049(v=ws.10).aspx) を参照してください。

1. 管理者としてコマンド プロンプト ウィンドウを開きます。
2. 次のコマンドを入力して、Sysprep を開きます。
 
		%windir%\system32\sysprep\sysprep.exe

4. システム準備ツールで、システムの OOBE (Out-of-Box Experience) を選択し、[一般化する] チェック ボックスをオンにして、**[シャットダウン]** を選択してから、以下のイメージに示されているように **[OK]** をクリックします。これにより、オペレーティング システムが一般化され、システムがシャットダウンされます。

	![][1]

Ubuntu VM でこれを行うには virt-sysprep を使用します。詳細については、[virt-sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) に関するページを参照してください。また、その他の Linux オペレーティング システムについては、オープン ソースの [Linux サーバー プロビジョニング ソフトウェア](http://www.cyberciti.biz/tips/server-provisioning-software.html)に関する記述も参照してください。

#### 単一の VM インスタンスを作成するための一意のオペレーティング システム VHD

マシン固有の情報を必要とする VM 上で実行中のアプリケーションがある場合は、VHD を一般化しないでください。一意の Azure VM インスタンスの作成に使用できるのは、一般化されていない VHD です。たとえば、VHD にドメイン コントローラーがある場合は、Sysprep を実行すると、ドメイン コントローラーとしては無効になります。VHD を一般化する前に、VM 上で実行中のアプリケーションと、これらに対する Sysprep の影響を確認してください。

#### VM インスタンスに接続するデータ ディスク VHD

移行するデータ ディスクがクラウド ストレージ内にある場合は、これらのデータ ディスクを使用する VM を必ずシャットダウンする必要があります。オンプレミスのデータ ディスクの場合は、一貫性のある VHD を作成します。

## Azure Storage への VHD のコピー

VHD の準備が完了したら、次のセクションで説明する手順に従って、VHD を Azure Storage にアップロードし、オペレーティング システム イメージ、プロビジョニングされたオペレーティング システム ディスク、またはプロビジョニングされたデータ ディスクとして登録します。

### VHD のコピー先を作成する

VHD を管理するためにストレージ アカウントを作成します。VHD の保存場所を計画する際は、次の点を考慮してください。

- 対象のストレージ アカウントは、アプリケーション要件に応じて Standard Storage または Premium Storage になります。 
- ストレージ アカウントの場所は、最終ステージで作成する DS シリーズの Azure VM と同じにする必要があります。必要に応じて、新しいストレージ アカウントにコピーするか、同じストレージ アカウントの使用を計画することができます。
- 次のステージ用に、移行先ストレージ アカウントのストレージ アカウント キーをコピーして保存します。
- データ ディスクの場合、一部のデータ ディスク (たとえば、負荷の軽いストレージのあるディスク) を Standard ストレージ アカウントで保持し、IOP の高いディスクを Premium ストレージ アカウントに移行することもできます。

### ソースから VHD をコピーする

次に VHD をコピーするためのいくつかのシナリオを紹介します。

#### Azure Storage から VHD をコピーする

Azure Standard Storage アカウントから Azure Premium Storage アカウントへ VHD を移行する場合は、VHD コンテナーのソース パス、VHD ファイル名、ソース ストレージ アカウントのストレージ アカウント キーをコピーする必要があります。

1. **[Azure ポータル]、[仮想マシン]、[ディスク]** の順に移動します。
2. [場所] 列から VHD のコンテナー URL コピーして保存します。コンテナー URL は `https://myaccount.blob.core.windows.net/mycontainer/` のようになります。

#### Azure 以外のクラウドから VHD をコピーする

Azure 以外のクラウド ストレージから Azure へ VHD を移行する場合は、最初に VHD をローカル ディレクトリにエクスポートする必要があります。VHD が保存されているローカル ディレクトリの完全なソース パスをコピーします。

1. AWS を使用している場合は、Amazon S3 バケット内の VHD に EC2 インスタンスをエクスポートします。Amazon ドキュメントの「[Amazon EC2 インスタンスのエクスポート](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExportingEC2Instances.html)」で説明されている手順に従い、Amazon EC2 コマンド ライン インターフェイス (CLI) ツールをインストールし、EC2 インスタンスを VHD ファイルにエクスポートするコマンドを実行します。 
	
	コマンドを実行する場合は、DISK&#95;IMAGE&#95;FORMAT 変数に必ず **VHD** を使用してください。エクスポートされた VHD ファイルは、その処理中に指定した Amazon S3 バケットに保存されます。

	![][2]

2. VHD ファイルを S3 バケットからダウンロードします。VHD ファイルを選択し、**[操作]**、**[ダウンロード]** の順に選択します。

	![][3]|

#### オンプレミスから VHD をコピーする

オンプレミス環境から VHD を移行する場合は、VHD の保存場所の完全なソース パスが必要です。これは、サーバーの場所の場合もあれば、ファイル共有の場合もあります。

### AzCopy による VHD のコピー

AzCopy を使用すると、インターネット経由で VHD を簡単にアップロードできます。VHD のサイズによっては、この処理に時間がかかる場合があります。このオプションを使用する場合は、ストレージ アカウントの送受信制限を確認することを忘れないでください。詳細については、「[Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット](storage-scalability-targets.md)」を参照してください。

1. 次のリンクから AzCopy をダウンロードしてインストールします: [AzCopy の最新バージョン](http://aka.ms/downloadazcopy)。  
2. Azure PowerShell を開き、AzCopy がインストールされているフォルダーに移動します。  
3. 次のコマンドを使用して、"Source" から "Destination" に VHD ファイルをコピーします。 

		AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>  

	次に AzCopy コマンドで使用するパラメーターを示します。

 - **/Source: *&lt;source&gt;:*** VHD が格納されているフォルダーの場所またはストレージ コンテナー URL。    
 - **/SourceKey: *&lt;source-account-key&gt;:*** コピー元ストレージ アカウントのストレージ アカウント キー。  
 - **/Dest: *&lt;destination&gt;:*** VHD のコピー先のストレージ コンテナー URL。
 - **/DestKey: *&lt;dest-account-key&gt;:*** コピー先ストレージ アカウントのストレージ アカウント キー。
 - **/BlobType: page:** コピー先がページ BLOB であることを指定。
 - **/Pattern: *&lt;file-name&gt;:*** コピーする VHD のファイル名を指定。
   
AzCopy ツールの使用方法の詳細については、[AzCopy コマンド ライン ユーティリティの概要](storage-use-azcopy.md)に関するページを参照してください。

### VHD をアップロードするためのその他のオプション 

また、次のいずれかの方法を使用して、VHD をストレージ アカウントにアップロードすることもできます。

- [Azure Storage Copy Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) 
- [Azure Import/Export サービス](https://msdn.microsoft.com/library/dn529096.aspx) 

>[AZURE.NOTE]Import/Export は、Standard Storage アカウントへのコピーのみに使用できます。Standard Storage アカウントから Premium Storage アカウントにコピーするには、AzCopy などのツールを使用する必要があります。

## Premium Storage を使用した Azure VM の作成

目的のストレージ アカウントに VHD がアップロードされたら、このセクションの手順に従って、シナリオに応じて VHD を OS イメージまたは OS ディスクとして登録し、その VHD から VM インスタンスを作成します。作成後に、データ ディスク VHD を VM に接続できます。

### VHD を登録する
  
OS VHD から VM を作成するか、新しい VM にデータ ディスクを接続するには、最初に VHD を登録する必要があります。シナリオに応じて次の手順に従います。

#### 複数の Azure VM インスタンスを作成するために一般化されたオペレーティング システム VHD

一般化された OS イメージ VHD をストレージ アカウントにアップロードした後、そのVHD を **Azure VM イメージ**として登録すると、そこから 1 つ以上の VM インスタンスを作成できます。次の PowerShell コマンドレットを使用して、VHD を Azure VM OS イメージとして登録します。VHD のコピー先の完全なコンテナー URL を入力します。

	Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows

この新しい Azure VM イメージの名前をコピーして保存します。上記の例では *OSImageName* です。

#### 単一の Azure VM インスタンスを作成するための一意のオペレーティング システム VHD

一意の OS VHD をストレージ アカウントにアップロードした後、その VHD を **Azure OS ディスク**として登録すると、そこから VM インスタンスを作成できます。次の PowerShell コマンドレットを使用して、VHD を Azure OS ディスクとして登録します。VHD のコピー先の完全なコンテナー URL を入力します。

	Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"

この新しい Azure OS ディスクの名前をコピーして保存します。上記の例では *OSDisk* です。

#### 新しい Azure VM インスタンスに接続するデータ ディスク VHD

データ ディスク VHD をストレージ アカウントにアップロードした後、その VHD を Azure データ ディスクとして登録すると、それを新しい DS シリーズ Azure VM インスタンスに接続できます。

次の PowerShell コマンドレットを使用して、VHD を Azure データ ディスクとして登録します。VHD のコピー先の完全なコンテナー URL を入力します。

	Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"

この新しい Azure データ ディスクの名前をコピーして保存します。上記の例では *DataDisk* です。

### Azure DS シリーズの VM を作成する  

OS イメージまたは OS ディスクを登録したら、新しい DS シリーズ Azure VM インスタンスを作成します。登録したオペレーティング システム イメージまたはオペレーティング システム ディスクの名前を使用します。Premium Storage レベルから VM の種類を選択します。次の例では、*Standard\_DS2* という VM サイズを使用します。

>[AZURE.NOTE]ディスク サイズを更新し、容量、パフォーマンス要件、使用可能な Azure ディスク サイズに適していることを確認します。

次の PowerShell コマンドレットの手順に従い、新しい VM を作成します。最初に、共通のパラメーターを設定します。

	$serviceName = "yourVM"
	$location = "location-name" (e.g., West US) 
	$vmSize ="Standard_DS2"
	$adminUser = "youradmin"
	$adminPassword = "yourpassword"
	$vmName ="yourVM"
	$vmSize = "Standard_DS2"

最初に、新しい VM をホストするクラウド サービスを作成します。

	New-AzureService -ServiceName $serviceName -Location $location

次に、シナリオに応じて、登録した OS イメージまたは OS ディスクから Azure VM インスタンスを作成します。

#### 複数の Azure VM インスタンスを作成するために一般化されたオペレーティング システム VHD

登録した **Azure OS イメージ**を使用して、1 つ以上の新しい DS シリーズ Azure VM インスタンスを作成します。新しい VM を作成する際に、次のように、VM 構成でこの OS イメージの名前を指定します。

	$OSImage = Get-AzureVMImage –ImageName "OSImageName"	

	$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

	Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm  

	New-AzureVM -ServiceName $serviceName -VM $vm	

#### 単一の Azure VM インスタンスを作成するための一意のオペレーティング システム VHD

登録した **Azure OS ディスク**を使用して、新しい DS シリーズ Azure VM インスタンスを作成します。新しい VM を作成する際に、次のように、VM 構成でこの OS ディスクの名前を指定します。

	$OSDisk = Get-AzureDisk –DiskName "OSDisk"  
	
	$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName
	
	New-AzureVM -ServiceName $serviceName –VM $vm

クラウド サービス、リージョン、ストレージ アカウント、可用性セット、キャッシュ ポリシーなど、その他の Azure VM 情報を指定します。VM インスタンスは、関連付けられたオペレーティング システムまたはデータ ディスクと併置する必要があるため、選択したクラウド サービス、リージョン、ストレージ アカウントはすべて、それらのディスクの基になる VHD と同じ場所に配置する必要がある点に注意してください。

### データ ディスクの接続  

最後に、データ ディスク VHD を登録した場合は、それらを新しい DS シリーズ Azure VM に接続します。

次の PowerShell コマンドレットを使用して、新しい VM にデータ ディスクに接続し、キャッシュ ポリシーを指定します。次の例では、キャッシュ ポリシーは読み取り専用 (*ReadOnly*) に設定されています。

	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

	Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm  

	Update-AzureVM  -VM $vm

>[AZURE.NOTE]このガイドで説明されていないアプリケーションをサポートするには、追加の手順が必要になる場合があります。


## 次のステップ  

仮想マシンの移行に関する具体的なシナリオについては、次のリソースを参照してください。

- [ストレージ アカウント間での Azure 仮想マシンの移行](http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)  
- [Windows Server VHD の作成と Azure へのアップロード](../virtual-machines-create-upload-vhd-windows-server.md)  
- [Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](../virtual-machines-linux-create-upload-vhd.md)  
- [Amazon AWS から Microsoft Azure への仮想マシンの移行](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)  

また、Azure Storage と Azure Virtual Machines の詳細については、次のリソースも参照してください。

- [Azure ストレージ](http://azure.microsoft.com/documentation/services/storage/)   
- [Azure Virtual Machines](http://azure.microsoft.com/documentation/services/virtual-machines/)  
- [: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](storage-premium-storage-preview-portal.md)  

[1]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png

<!---HONumber=July15_HO5-->