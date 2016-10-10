<properties
    pageTitle="Azure Premium Storage への移行 | Microsoft Azure"
    description="既存の仮想マシンを Azure Premium Storage に移行します。Premium Storage は、Azure Virtual Machines で実行される高負荷の I/O ワークロードのための、高パフォーマンスで待ち時間の少ないディスク サポートを提供します。"
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="aungoo;robinsh"/>


# Azure Premium Storage への移行

## Overview

Azure Premium Storage は、高負荷の I/O ワークロードを実行する仮想マシン向けに高パフォーマンスで待ち時間の少ないディスク サポートを提供します。Premium Storage を使用する仮想マシン (VM) では、ソリッド ステート ドライブ (SSD) にデータを格納します。アプリケーションの VM ディスクを Azure Premium Storage に移行することで、これらのディスクの速度とパフォーマンスを最大限に利用することができます。

Azure VM では複数の Premium Storage ディスクをアタッチすることができるので、アプリケーションには VM あたり最大で 64 TB のストレージを設定することができます。Premium Storage を使用すると、アプリケーションは、VM あたり 80,000 IOPS (1 秒あたりの入力/出力操作) と、VM あたり 2000 MB/秒のディスク スループットを実現でき、読み取り操作の待ち時間も非常に短くなります。

>[AZURE.NOTE] 高い IOPS を必要とする仮想マシンのディスクは Azure Premium Storage に移行して、アプリケーションが最高のパフォーマンスを発揮できるようにすることをお勧めします。ディスクが高い IOPS を必要としない場合は、ディスクを Standard Storage 内に保持することでコストを抑えることができます。Standard Storage の場合、仮想マシンのディスク データは SSD ではなくハード ディスク ドライブ (HDD) に格納されます。

このガイドでは、Azure Premium Storage の新しいユーザーが現在のシステムから Premium Storage への円滑な移行に向けて周到に準備できるようにすることを目的としています。このガイドでは、移行プロセスの重要な要素のうち、Premium Storage への移行の計画、既存の仮想ハード ディスク (VHD) の Premium Storage への移行、Premium Storage での Azure 仮想マシン インスタンスの作成という 3 点を扱います。

移行プロセス全体を完了するには、このガイドで説明する手順の前後で追加の操作が必要になる場合があります。たとえば、仮想ネットワークやエンドポイントの構成、アプリケーション内部のコード変更があります。これらの操作は、アプリケーションごとに異なります。Premium Storage への完全な移行をできる限りシームレスに行うには、このガイドで説明する手順と共に、これらの必要な操作を完了してください。

Premium Storage の機能の概要については、「[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](storage-premium-storage.md)」を参照してください。

このガイドでは、次の 2 つの移行シナリオを説明するために 2 つのセクションに分かれています。

- [他のプラットフォームの VM の Azure Premium Storage への移行](#migrating-vms-from-other-platforms-to-azure-premium-storage)
- [既存の Azure VM の Azure Premium Storage への移行](#migrating-existing-azure-vms-to-azure-premium-storage)。

状況に応じて、関連するセクションに示されている手順に従ってください。

## 他のプラットフォームの VM の Azure Premium Storage への移行

### 前提条件
- Azure サブスクリプションが必要です。サブスクリプションがない場合は、1 か月間の[無料試用版](https://azure.microsoft.com/pricing/free-trial/)サブスクリプションを作成するか、「[Azure の価格](https://azure.microsoft.com/pricing/)」でさらに多くのオプションを利用することができます。
- PowerShell コマンドレットを実行するには、Microsoft Azure PowerShell モジュールが必要です。このモジュールをダウンロードするには、[Microsoft Azure のダウンロード](https://azure.microsoft.com/downloads/) ページにアクセスしてください。
- Premium Storage で実行される Azure VM を使用する場合は、DS シリーズ、DSv2 シリーズ、または GS シリーズの VM を使用する必要があります。DS シリーズ、DSv2 シリーズ、GS シリーズの VM では、Standard Storage ディスクと Premium Storage ディスクの両方を使用できます。Premium Storage ディスクの方が、将来的により多くの VM の種類を使用できます。使用可能な Azure VM ディスクの種類とサイズの詳細については、「[仮想マシンのサイズ](../virtual-machines/virtual-machines-windows-sizes.md)」と「[Cloud Services のサイズ](../cloud-services/cloud-services-sizes-specs.md)」を参照してください。

### 考慮事項

#### VM サイズ
Azure VM のサイズの仕様は、「[仮想マシンのサイズ](../virtual-machines/virtual-machines-windows-sizes.md)」に記載されています。Premium Storage で動作する仮想マシンのパフォーマンス特性を確認し、ワークロードに最適な VM を選択してください。ディスク トラフィックが流れるのに十分な帯域幅が VM で利用できることを確認します。


#### ディスク サイズ
VM で使用できるディスクには 3 種類あり、それぞれに特定の IOPS とスループットの制限があります。VM のディスクの種類を選択する場合は、容量、パフォーマンス、スケーラビリティ、最大負荷に関するアプリケーションのニーズに基づいて、これらの制限を考慮してください。

|Premium Storage ディスク タイプ|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|ディスク サイズ|128 GB|512 GB|1024 GB (1 TB)|
|ディスクあたりの IOPS|500|2300|5000|
|ディスクあたりのスループット|100 MB/秒|150 MB/秒|200 MB/秒|

#### ストレージ アカウントのスケーラビリティ ターゲット

Premium Storage アカウントには、[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](storage-scalability-targets.md)に加え、次のスケーラビリティ ターゲットがあります。アプリケーションの要件が単一のストレージ アカウントのスケーラビリティ ターゲットを上回った場合は、複数のストレージ アカウントを使用するようにアプリケーションを構築し、それらのストレージ アカウント間でデータを分割します。

|合計アカウント容量|ローカル冗長ストレージ アカウントの合計帯域幅|
|:--|:---|
|ディスク容量: 35 TB<br />スナップショット容量: 10 TB|受信と送信を合わせて最大 50 GB/秒|

Premium Storage の仕様の詳細については、「[Premium Storage を使用するときの拡張性とパフォーマンスのターゲット](storage-premium-storage.md#scalability-and-performance-targets-whja-JPing-premium-storage)」を参照してください。

#### 追加のデータ ディスク

ワークロードに応じて、VM にデータ ディスクを追加する必要があるかどうかを判断します。複数の永続データ ディスクを VM に接続できます。必要に応じて、ディスク全体をストライピングして容量を増やし、ボリュームのパフォーマンスを高めることができます。[記憶域スペース](http://technet.microsoft.com/library/hh831739.aspx)を使用して Premium Storage データ ディスクをストライピングする場合は、使用するディスクごとに 1 つの列で構成する必要があります。そうしない場合は、ディスク全体のトラフィックの配分が不均等になるため、ストライプ ボリュームの全体的なパフォーマンスが低下する可能性があります。Linux VM の場合は、*mdadm* ユーティリティを使用すると同じ結果を得ることができます。詳細については、[Linux でのソフトウェア RAID の構成](../virtual-machines/virtual-machines-linux-configure-raid.md)に関する記事を参照してください。

#### ディスク キャッシュ ポリシー
既定では、ディスクのキャッシュ ポリシーは、すべてのPremium データ ディスクに対して「*読み取り専用*」、VM にアタッチされた Premium オペレーティング システム ディスクに対して「*読み取り/書き込み*」です。アプリケーションの IO パフォーマンスを最適化するには、この構成をお勧めします。書き込み量の多いディスクや書き込み専用のディスク (SQL Server ログ ファイルなど) の場合は、ディスク キャッシュを無効にすることで、アプリケーションのパフォーマンスを向上できる場合があります。既存のデータ ディスクのキャッシュ設定は、[Azure ポータル](https://portal.azure.com)、または *Set-AzureDataDisk* コマンドレットの *-HostCaching* パラメーターを使用して更新できます。

#### 場所
Azure Premium Storage を使用できる場所を選択します。使用できる場所に関する最新情報については、「[リージョン別のサービス](https://azure.microsoft.com/regions/#services)」を参照してください。VM のディスクを保存するストレージ アカウントと同じリージョンにある VM は、個々のリージョン内にある場合よりも優れたパフォーマンスを提供します。

#### Azure VM のその他の構成設定

Azure VM を作成するときに、特定の VM の設定を構成するよう求められます。VM の有効期間中は固定されている設定がいくつかありまりますが、後で変更または追加できる設定もあることを忘れないでください。このような Azure VM の構成設定を確認し、ワークロードの要件に合わせて適切に構成されていることを確認します。

## 移行のための VHD の準備

次のセクションでは、すぐに移行できるように VM から VHD を準備するためのガイドラインを示します。VHD は次のいずれかになります。

- 複数の Azure VM の作成に使用できる一般化されたオペレーティング システム イメージ。
- 1 つの Azure 仮想マシン インスタンスで使用できるオペレーティング システム ディスク。
- 永続的なストレージ用に Azure VM に接続できるデータ ディスク。

### 前提条件

VM を移行するには、次のものが必要です。

- Azure サブスクリプション、ストレージ アカウント、および VHD のコピー先となるそのストレージ アカウント内のコンテナー。コピー先のストレージ アカウントには、要件に応じて Standard Storage アカウントまたは Premium Storage アカウントを使用できることに注意してください。
- VHD から複数の VM インスタンスを作成する予定の場合にその VHD を一般化するツール。たとえば、Windows の sysprep や Ubuntu の virt-sysprep があります。
- ストレージ アカウントに VHD ファイルをアップロードするツール。「[AzCopy コマンド ライン ユーティリティを使ったデータの転送](storage-use-azcopy.md)」を参照するか、[Azure ストレージ エクスプローラー](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)を使用してください。このガイドでは、AzCopy ツールを使用して VHD をコピーする手順について説明します。

> [AZURE.NOTE] パフォーマンスを最適化するには、コピー先のストレージ アカウントと同じリージョンにある Azure VM からこれらのツールのいずれかを実行して VHD をコピーします。別のリージョンの Azure VM から VHD をコピーする場合は、パフォーマンスが減速することがあります。
>
> 制限された帯域幅で大量のデータをコピーするには、[Azure Import/Export サービスを使用してデータを Blob Storage へ転送](storage-import-export-service.md)することを検討してください。この方法であれば、ハード ディスク ドライブを Azure データセンターへ送付してデータを転送することができます。Azure Import/Export サービスを使用すると、データを Standard Storage アカウントのみにコピーできます。データが Standard Storage アカウントに配置されたら、[Copy Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) または AzCopy を使用して、そのデータを Premium Storage アカウントに転送できます。
>
> Microsoft Azure でサポートされているのは、固定サイズの VHD ファイルのみです。VHDX ファイルまたは動的 VHD はサポートされていません。動的 VHD を使用している場合は、[Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) コマンドレットを使用して固定サイズに変換できます。

### VHD の準備のシナリオ

VHD を準備するためのいくつかのシナリオについて説明します。

#### 複数の VM インスタンスを作成するために一般化されたオペレーティング システム VHD

複数の汎用的な Azure VM インスタンスの作成に使用する VHD をアップロードする場合は、最初に、Sysprep ユーティリティを使用して VHD を一般化する必要があります。これは、オンプレミスまたはクラウド内の VHD に適用されます。Sysprep は、コンピューター固有の情報を VHD から削除します。

>[AZURE.IMPORTANT] VHD を一般化する前に、スナップショットを取得または VM をバックアップします。Sysprep を実行すると VM インスタンスが削除されます。次の手順に従って、Windows OS VHD に Sysprep を実行します。Sysprep コマンドを実行する場合、仮想マシンをシャットダウンする必要があります。Sysprep の詳細については、「[Sysprep (システム準備) の概要](http://technet.microsoft.com/library/hh825209.aspx)」または「[Sysprep テクニカル リファレンス](http://technet.microsoft.com/library/cc766049.aspx)」を参照してください。

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
- ストレージ アカウントの場所は、最終ステージで作成する DS シリーズ、DSv2 シリーズ、または GS シリーズの Azure VM と同じである必要があります。必要に応じて、新しいストレージ アカウントにコピーするか、同じストレージ アカウントの使用を計画することができます。
- 次のステージ用に、移行先ストレージ アカウントのストレージ アカウント キーをコピーして保存します。
- データ ディスクの場合、一部のデータ ディスク (たとえば、負荷の軽いストレージのあるディスク) を Standard Storage アカウントで保持し、IOP の高いディスクを Premium Storage アカウントに移行することもできます。

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

AzCopy を使用すると、インターネット経由で VHD を簡単にアップロードできます。VHD のサイズによっては、この処理に時間がかかる場合があります。このオプションを使用する場合は、ストレージ アカウントの送受信制限を確認することを忘れないでください。詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](storage-scalability-targets.md)」を参照してください。

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

AzCopy ツールの使用の詳細については、「[AzCopy コマンド ライン ユーティリティを使用してデータを転送する](storage-use-azcopy.md)」を参照してください。

### PowerShell による VHD のコピー
Start-AzureStorageBlobCopy という PowerShell コマンドレットを使用して、VHD ファイルをコピーすることもできます。VHD をコピーするには、Azure PowerShell で次のコマンドを使用します。<> 内の値を、対応するコピー元とコピー先のストレージ アカウントの値に置き換えます。このコマンドを使用するには、vhds と呼ばれるコンテナーがコピー先ストレージ アカウントに用意されている必要があります。このコンテナーが存在しない場合は、コマンドを実行する前に作成してください。

    $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"
    $sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>
    $destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>
    Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext

### VHD をアップロードするためのその他のオプション

また、次のいずれかの方法を使用して、VHD をストレージ アカウントにアップロードすることもできます。

- [Azure Storage Copy Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Storage Import/Export Service REST API リファレンス](https://msdn.microsoft.com/library/dn529096.aspx)

>[AZURE.NOTE] Import/Export は、Standard Storage アカウントへのコピーのみに使用できます。Standard Storage アカウントから Premium Storage アカウントにコピーするには、AzCopy などのツールを使用する必要があります。

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

データ ディスク VHD をストレージ アカウントにアップロードした後、その VHD を Azure データ ディスクとして登録すると、それを DS シリーズ、DSv2 シリーズ、または GS シリーズの新しい Azure VM インスタンスに接続できます。

次の PowerShell コマンドレットを使用して、VHD を Azure データ ディスクとして登録します。VHD のコピー先の完全なコンテナー URL を入力します。

	Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"

この新しい Azure データ ディスクの名前をコピーして保存します。上記の例では *DataDisk* です。

### Azure DS シリーズ、DSv2 シリーズ、または GS シリーズの VM を作成する

OS イメージまたは OS ディスクが登録されたら、DS シリーズ、DSv2 シリーズ、または GS シリーズの新しい VM を作成します。登録したオペレーティング システム イメージまたはオペレーティング システム ディスクの名前を使用します。Premium Storage レベルから VM のタイプを選択します。次の例では、*Standard\_DS2* という VM サイズを使用します。

>[AZURE.NOTE] ディスク サイズを更新し、容量、パフォーマンス要件、使用可能な Azure ディスク サイズに適していることを確認します。

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

### Attach data disk

最後に、データ ディスク VHD を登録した場合は、それらを DS シリーズ、DSv2 シリーズ、または GS シリーズの新しい Azure VM に接続します。

次の PowerShell コマンドレットを使用して、新しい VM にデータ ディスクに接続し、キャッシュ ポリシーを指定します。次の例では、キャッシュ ポリシーは読み取り専用 (*ReadOnly*) に設定されています。

	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

	Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

	Update-AzureVM  -VM $vm

>[AZURE.NOTE] このガイドで説明されていないアプリケーションをサポートするには、追加の手順が必要になる場合があります。

## 既存の Azure VM の Azure Premium Storage への移行

現在、Standard Storage ディスクを使用する Azure VM がある場合、それを Premium Storage に移行するには、以下のプロセスに従います。大まかに言えば、移行には次の 2 段階があります。
-	Standard Storage アカウントから Premium Storage アカウントにディスクを移行する
-	Premium Storage ディスクを使用するために VM サイズを A/D/G から DS、DSv2、または GS に変換する

さらに、Premium Storage に向けて実行できるさまざまな最適化を理解するには、前のセクションの「考慮事項」を参照してください。移行プロセスは、アプリケーションに適用される最適化に応じて、以下の移行シナリオのいずれかに分類できます。

### 単純な移行
この単純なシナリオでは、Standard Storage から Premium Storage に移行する際に構成をそのまま保持することに目を向けています。ここでは、各ディスクをそのままの状態で移行した後、さらに VM を変換します。このシナリオの利点は移行が簡単であること、欠点は、移行後の構成がコストを最小限に抑えるように最適化されない可能性があることです。

#### 準備
1. 移行先のリージョンで Premium Storage が利用可能であることを確認します。
2. 使用する新しい VM シリーズを決定します。リージョンでの可用性や自身のニーズに応じて DS シリーズ、DSv2 シリーズ、GS シリーズのいずれかにする必要があります。
3. 使用する正確な VM サイズを決定します。VM サイズは、所有するデータ ディスクの数がサポートされるように十分な大きさにする必要があります。たとえば、データ ディスクが 4 つある場合、VM には 2 つ以上のコアが必要です。さらに、処理能力、メモリ、ネットワーク帯域幅のニーズについても検討します。
4. 移行先のリージョンで Premium Storage アカウントを作成します。これが新しい VM に使用するアカウントです。
5. 現在の VM の詳細 (ディスクの一覧や対応する VHD BLOB など) を手元に用意します。
6. ダウンタイムに備えてアプリケーションを準備します。移行を問題なく実行するには、現在のシステムですべての処理を停止する必要があります。そうすることで初めて、新しいプラットフォームに移行できる安定した状態になります。ダウンタイム時間は、移行するディスクのデータ量によって異なります。

#### 実行ステップ
1.	VM を停止します。既に説明したとおり、安定した状態で移行するために VM は完全に停止している必要があります。移行が完了するまではダウンタイムになります。

2.	VM が停止したら、その VM の各 VHD を新しい Premium Storage アカウントにコピーします。すべてのデータ ディスクの VHD BLOB だけでなく、OS ディスクの VHD BLOB もコピーする必要があります。移行の際には、AzCopy または CopyBlob を使用することをお勧めします。必要に応じて、その他のサード パーティ製のツールを使用することもできます。

  コマンドについては、前のセクションの「[AzCopy による VHD のコピー](#copy-a-vhd-with-azcopy)」または「[PowerShell による VHD のコピー](#copy-a-vhd-with-powershell)」を参照してください。

3.	コピーが完了したかどうかを確認します。すべてのディスクがコピーされるまで待機します。すべてのディスクのコピーが完了したら、新しい VM を作成するために、次の手順に進むことができます。
4.	Premium Storage アカウントにコピーした OS ディスクの VHD BLOB を使用して、新しい OS ディスクを作成します。これには、"Add-AzureDisk" PowerShell コマンドレットを使用します。

    サンプル スクリプト: Add-AzureDisk -DiskName "NewOSDisk1" -MediaLocation "https://newpremiumstorageaccount.blob.core.windows.net/vhds/MyOSDisk.vhd" -OS "Windows"
5. 次に、上記の OS ディスクとデータ ディスクを使用して、DS シリーズ (または DSv2 シリーズか GS シリーズ) の VM を作成します。

    新しいクラウド サービスを作成して、そのサービス内に新しい VM を作成するためのサンプル スクリプト: New-AzureService -ServiceName "NewServiceName" -Location "East US 2"

        New-AzureVMConfig -Name "NewDSVMName" -InstanceSize "Standard_DS2" -DiskName "NewOSDisk1" | Add-AzureProvisioningConfig -Windows | Add-AzureDataDisk -LUN 0 -DiskLabel "DataDisk1" -ImportFrom -MediaLocation "https://newpremiumstorageaccount.blob.core.windows.net/vhds/Disk1.vhd" | Add-AzureDataDisk -LUN 1 -DiskLabel "DataDisk2" -ImportFrom -MediaLocation https://newpremiumstorageaccount.blob.core.windows.net/vhds/Disk2.vhd | New-AzureVM -ServiceName "NewServiceName" –Location “East US 2”

6.	新しい VM が稼働状態になったら、元の VM と同じログイン ID とパスワードを使用して新しい VM にアクセスし、すべてが正しく動作していることを確認します。すべての設定 (ストライプ ボリュームを含む) が新しい VM に見られます。

7.	最後の手順として、アプリケーションのニーズに応じて、新しい VM のバックアップとメンテナンスのスケジュールを計画します。

### 自動化
移行する VM が複数ある場合は、PowerShell スクリプトを使用した自動化が便利です。次に示すサンプルは、VM の移行を自動化するサンプル スクリプトです。このスクリプトはほんの一例であり、現在の VM ディスクについていくつかの仮定が含まれていることに注意してください。特定のシナリオに対応するように、スクリプトの更新が必要になる場合があります。

    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a vm from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source vm to the new storage account.
    And then it will create a new vm from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED “AS IS” WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location “Southeast Asia”

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you’d like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the vm mannually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires to delete the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`tMoving VM requires to remove the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
    	    Start-Sleep -Seconds 10
    	    $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a vm from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location

### 最適化
現在の VM の構成は、Standard ディスクで問題なく動作するようにカスタマイズされている可能性があります。たとえば、パフォーマンスを向上させるために、ストライプ ボリュームで多くのディスクを使用しています。Premium Storage ディスクを使用するとパフォーマンスはさらに向上するため、ディスクの数を減らして、コストを最適化できます。たとえば、アプリケーションに 2000 IOPS のボリュームが必要な場合は、4 つの Standard Storage ディスクで構成されるストライプ セットを使用して、4 x 500 = 2000 IOPS にすることがあります。Premium Storage ディスクを使用すると、512 GB の 1 つのディスクで、2300 IOPS を実現できます。つまり、Premium Storage では、4 つのディスクを個別に使用するのではなく、1 つのディスクを使用することによって、コストを最適化できる場合があります。このような最適化は個々のケースに応じて処理する必要があり、移行後にカスタム手順が必要になります。このプロセスは、セットアップ時に定義されたディスク レイアウトに依存するデータベースおよびアプリケーションではうまく機能しない可能性があることに注意してください。

#### 準備
1.	前のセクションで説明した単純な移行を完了します。最適化は、移行後に新しい VM で実行されます。
2.	最適化された構成に必要な新しいディスク サイズを定義します。
3.	新しいディスクの指定に対する現在のディスク/ボリュームのマッピングを決定します。

#### 実行ステップ:
1.	Premium Storage VM で適切なサイズの新しいディスクを作成します。
2.	VM にログインし、現在のボリュームのデータを、そのボリュームにマッピングされる新しいディスクにコピーします。この操作を、新しいディスクにマッピングする必要のある現在のボリュームすべてに対して実行します。
3.	次に、新しいディスクに切り替えるようにアプリケーションの設定を変更し、以前のボリュームをデタッチします。

###  アプリケーションの移行
データベースやその他の複雑なアプリケーションを移行する場合は、アプリケーションの提供元が指定する特別な手順が必要になることがあります。各アプリケーションのドキュメントを参照してください。たとえば、通常、データベースは、バックアップと復元を使用して移行できます。

## 次のステップ

仮想マシンの移行に関する具体的なシナリオについては、次のリソースを参照してください。

- [ストレージ アカウント間での Azure 仮想マシンの移行](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
- [Windows Server VHD の作成と Azure へのアップロード](../virtual-machines/virtual-machines-windows-classic-createupload-vhd.md)
- [Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](../virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md)
- [Amazon AWS から Microsoft Azure への仮想マシンの移行](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

また、Azure Storage と Azure Virtual Machines の詳細については、次のリソースも参照してください。

- [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
- [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
- [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](storage-premium-storage.md)

[1]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png

<!---HONumber=AcomDC_0928_2016-->