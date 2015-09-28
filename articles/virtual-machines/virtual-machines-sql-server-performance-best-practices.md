<properties 
	pageTitle="Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス"
	description="Microsoft Azure VM で SQL Server のパフォーマンスを最適化するためのベスト プラクティスを紹介します。"
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="09/01/2015"
	ms.author="jroth" />

# Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス

## 概要

このトピックでは、Microsoft Azure Virtual Machines で SQL Server のパフォーマンスを最適化するためのベスト プラクティスを紹介します。Azure Virtual Machines で SQL Server を実行するときは、オンプレミスのサーバー環境で SQL Server に適用されるデータベース パフォーマンス チューニング オプションと同じものを引き続き使用することをお勧めします。ただし、パブリック クラウド内のリレーショナル データベースのパフォーマンスは、仮想マシンのサイズやデータ ディスクの構成などのさまざまな要素に左右されます。

>[AZURE.NOTE]このドキュメントは、Azure VM で SQL Server の最適なパフォーマンスを得ることに重点を置いています。ワークロードの要求が厳しくない場合は、以下に示す最適化がすべて必要になるわけではありません。各推奨事項を評価するときに、パフォーマンスのニーズとワークロードのパターンを考慮してください。

## クイック チェック リスト

Azure Virtual Machines で SQL Server の最適なパフォーマンスを実現するためのクイック チェック リストを次に示します。

|領域|最適化|
|---|---|
|**VM サイズ**|[DS3](virtual-machines-size-specs.md#standard-tier-ds-series) 以上 (SQL Enterprise Edition の場合)。<br/><br/>[DS2](virtual-machines-size-specs.md#standard-tier-ds-series) 以上 (SQL Standard Edition および Web Edition の場合)。|
|**ストレージ**|[Premium Storage](../storage/storage-premium-storage-preview-portal.md) を使用します。<br/><br/>[ストレージ アカウント](../storage/storage-create-storage-account.md)と SQL Server VM を同じリージョンに保持します。<br/><br/>ストレージ アカウントで Azure [地理冗長ストレージ](../storage/storage-redundancy.md) (geo レプリケーション) を無効にします。|
|**ディスク**|少なくとも 2 つの [P30 ディスク](../storage/storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whja-JPing-premium-storage) (ログ ファイル用に 1 つ、データ ファイルと TempDB 用に 1 つ) を使用します。<br/><br/>データベースの保存またはログ記録にオペレーティング システム ディスクまたは一時ディスクを使用することは避けます。<br/><br/>データ ファイルと TempDB をホストするディスクで読み取りキャッシュを有効にします。<br/><br/>ログ ファイルをホストするディスクでは、キャッシュを有効にしないでください。<br/><br/>複数の Azure データ ディスクをストライプして、IO スループットを増やします。<br/><br/>ドキュメントに記載されている割り当てサイズでフォーマットします。|
|**I/O**|データベース ページの圧縮を有効にします。<br/><br/>データ ファイルの瞬時初期化を有効にします。<br/><br/>データベースで自動拡張を制限するか、無効にします。<br/><br/>データベースで自動圧縮を無効にします。<br/><br/>システム データベースも含め、すべてのデータベースをデータ ディスクに移動します。<br/><br/>SQL Server エラー ログとトレース ファイルのディレクトリをデータ ディスクに移動します。<br/><br/>既定のバックアップ ファイルとデータベース ファイルの場所を設定します。<br/><br/>ロックされたページを有効にします。<br/><br/>SQL Server パフォーマンスの修正プログラムを適用します。|
|**機能固有**|BLOB ストレージに直接バックアップします。|

詳細については、以下のサブ セクションで説明するガイドラインに従ってください。

## 仮想マシン サイズとストレージ アカウントに関する考慮事項

パフォーマンス重視のアプリケーションでは、次の仮想マシン サイズを使用することをお勧めします。

- **SQL Server Enterprise Edition**: DS3 以上

- **SQL Server Standard Edition または Web Edition**: DS2 以上

サポートされている仮想マシン サイズの最新情報については、「[Virtual Machines のサイズ](virtual-machines-size-specs.md)」を参照してください。

さらに、転送遅延を低減するために、SQL Server 仮想マシンと同じデータ センターで Azure ストレージ アカウントを作成することをお勧めします。ストレージ アカウントの作成時に、geo レプリケーションを無効にします。複数のディスクでの一貫性のある書き込み順序が保証されないためです。代わりに、2 つの Azure データ センター間で SQL Server 障害復旧テクノロジを構成することを検討します。詳細については、「[Azure Virtual Machines における SQL Server の高可用性と災害復旧](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)」を参照してください。

## ディスクとパフォーマンスに関する考慮事項

Azure 仮想マシンを作成すると、プラットフォームによって、オペレーティング システム ディスク用に少なくとも 1 つのディスクが VM に接続されます。このディスクは、ページ BLOB としてストレージに格納されている VHD です。追加のディスクをデータ ディスクとして仮想マシンに接続することもできます。これらのディスクは、ページ BLOB としてストレージに格納されます。Azure Virtual Machines には、一時ディスクと呼ばれる別のディスクも存在します。これは、スクラッチ領域に使用できるノード上のディスクです。

### オペレーティング システム ディスク

オペレーティング システム ディスクは、実行中のバージョンのオペレーティング システムとして起動およびマウントできる VHD であり、**C** ドライブとしてラベル付けされます。

オペレーティング システム ディスクの既定のキャッシュ ポリシーは、**読み取り/書き込み**です。パフォーマンス重視のアプリケーションでは、オペレーティング システム ディスクではなく、データ ディスクを使用することをお勧めします。下記のデータ ディスクに関するセクションをご覧ください。

### 一時ディスク

**D**: ドライブとしてラベル付けされる一時ストレージ ドライブは、Azure BLOB ストレージに保持されません。**D**: ドライブにデータ ファイルやログ ファイルを保存しないでください。

D シリーズまたは G シリーズの Virtual Machines (VM) を使用している場合、**D** ドライブには TempDB とバッファー プール拡張機能だけを保存します。他の VM シリーズとは異なり、D シリーズと G シリーズの VM の **D** ドライブは SSD ベースです。そのため、一時オブジェクトを頻繁に使用するワークロードや、メモリに収まらないワーキング セットを持つワークロードのパフォーマンスを向上させることができます。詳細については、[Azure VM で SSD を使用した SQL Server TempDB とバッファー プール拡張機能の保存](http://blogs.technet.com/b/dataplatforminsider/archive/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions.aspx)に関するページを参照してください。

### データ ディスク

- **データ ファイルとログ ファイル用のデータ ディスクの数**: 少なくとも、2 つの [P30 ディスク](../storage/storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whja-JPing-premium-storage)を使用し、一方のディスクにログ ファイル、もう一方にデータ ファイルと TempDB を含めます。スループットを向上させるために、データ ディスクを追加することが必要になる場合もあります。データ ディスクの数を決定するには、データおよびログ ディスクで使用可能な IOPS の数を分析する必要があります。詳細については、[ディスクへの Premium Storage の使用](../storage/storage-premium-storage-preview-portal.md)に関する記事で [VM サイズ](virtual-machines-size-specs.md)およびディスク サイズごとの IOPS を示す表をご覧ください。さらに多くの帯域幅が必要な場合は、追加のディスクを接続し、ディスク ストライピングを使用できます。Premium Storage を使用しない場合は、ご使用の [VM サイズ](virtual-machines-size-specs.md)でサポートされる最大数のデータ ディスクを追加し、ディスク ストライピングを使用することをお勧めします。ディスク ストライピングの詳細については、下記の関連セクションをご覧ください。

- **キャッシュ ポリシー**: データ ファイルと TempDB をホストするデータ ディスクのみで読み取りキャッシュを有効にします。Premium Storage を使用していない場合は、どのデータ ディスクでもキャッシュを有効にしないでください。ディスクのキャッシュを構成する手順については、「[Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847)」および「[Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx)」を参照してください。

- **NTFS アロケーション ユニット サイズ**: データ ディスクをフォーマットするときは、データ ファイルとログ ファイルに加えて TempDB にも 64 KB アロケーション ユニット サイズを使用することをお勧めします。

- **ディスク ストライピング**: 次のガイドラインに従うことをお勧めします。

	- Windows 8 と Windows Server 2012 以降では、[記憶域スペース](https://technet.microsoft.com/library/hh831739.aspx)を使用します。ストライプ サイズを、OLTP ワークロードでは 64 KB、データ ウェアハウス ワークロードでは 256 KB にそれぞれ設定して、パーティションの不適切な配置に起因するパフォーマンスの低下を防ぎます。また、列数を物理ディスクの数に設定します。8 個以上のディスクで記憶域スペースを構成するには、(サーバー マネージャーの UI ではなく) PowerShell を使用して、ディスクの数に一致する列数を明示的に設定する必要があります。[記憶域スペース](https://technet.microsoft.com/library/hh831739.aspx)の構成方法の詳細については、[Windows PowerShell の記憶域スペース コマンドレット](https://technet.microsoft.com/library/jj851254.aspx)に関するページを参照してください。
	
	- Windows 2008 R2 以前では、ダイナミック ディスク (OS ストライプ ボリューム) を使用できます。ストライプ サイズは常に 64 KB です。Windows 8 および Windows Server 2012 の時点で、このオプションは使用されていません。詳細については、[Windows Storage Management API に移行しつつある仮想ディスク サービス](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx)に関するページでサポートに関する声明を参照してください。
	
	- ワークロードで大量のログが発生するわけではなく、専用の IOPS を必要としない場合は、記憶域プールを 1 つだけ構成します。それ以外の場合は、記憶域プールを 2 つ作成して、1 つをログ ファイルに使用し、もう 1 つをデータ ファイルと TempDB に使用します。負荷予測に基づいて、各記憶域プールに関連付けるディスクの数を決定します。接続できるデータ ディスクの数は VM サイズによって異なることに注意してください。詳細については、「[Virtual Machines のサイズ](virtual-machines-size-specs.md)」を参照してください。

## I/O パフォーマンスに関する考慮事項

- Premium Storage では、アプリケーションと要求の並列処理を実行するときに最良の結果が得られます。Premium Storage は、IO キューの深さが 1 より大きいシナリオ向けに設計されているので、シングル スレッド シリアル要求では (ストレージを集中的に使用する場合でも)、パフォーマンスの向上はほとんどまたはまったくありません。たとえば、これは、パフォーマンス分析ツール (SQLIO など) のシングル スレッド テストの結果に影響する可能性があります。

- I/O 集中型ワークロードのパフォーマンスを向上させるために、[データベース ページの圧縮](https://msdn.microsoft.com/library/cc280449.aspx)を使用することを検討します。ただし、データ圧縮を使用すると、データベース サーバーでの CPU 消費量が増加する場合があります。

- Azure との間での転送時にデータ ファイルを圧縮することを検討します。

- 初期ファイル割り当てに必要な時間を短縮するために、ファイルの瞬時初期化を有効にすることを検討します。ファイルの瞬時初期化を利用するには、SQL Server (MSSQLSERVER) サービス アカウントに SE\_MANAGE\_VOLUME\_NAME を付与し、**[ボリュームの保守タスクを実行]** セキュリティ ポリシーにそのサービス アカウントを追加します。Azure の SQL Server プラットフォーム イメージを使用している場合、既定のサービス アカウント (NT Service\\MSSQLSERVER) は、**[ボリュームの保守タスクを実行]** セキュリティ ポリシーに追加されません。つまり、SQL Server Azure プラットフォーム イメージでは、ファイルの瞬時初期化は有効になりません。**[ボリュームの保守タスクを実行]** セキュリティ ポリシーに SQL Server サービス アカウントを追加したら、SQL Server サービスを再起動します。この機能を使用する場合、セキュリティに関する考慮事項があります。詳細については、「[データベース ファイルの初期化](https://msdn.microsoft.com/library/ms175935.aspx)」を参照してください。

- **自動拡張**は、予想外の増加に付随するものと見なされています。自動拡張を使用して、データやログの増加に日常的に対処しないでください。自動拡張を使用する場合は、Size スイッチを使用してファイルを事前に拡張します。

- パフォーマンスに悪影響を及ぼすおそれのある不要なオーバーヘッドを回避するために、**自動圧縮**が無効になっていることを確認します。

- SQL Server 2012 を実行している場合は、Service Pack 1 Cumulative Update 10 をインストールします。この更新プログラムには、SQL Server 2012 で一時テーブルに対して SELECT INTO ステートメントを実行したときに I/O のパフォーマンスが低下する問題に対処するための修正プログラムが含まれています。詳細については、この[サポート技術情報の記事](http://support.microsoft.com/kb/2958012)を参照してください。

- パフォーマンスを向上させるために、システム データベース (msdb や TempDB など)、バックアップ、SQL Server の既定のデータ ディレクトリとログ ディレクトリをキャッシュされないデータ ディスクに移動します。その後、次の操作を行います。

	- XEvent とトレース ファイルのパスを調整する。
	
	- SQL エラー ログのパスを調整する。
	
	- 既定のバックアップ パスを調整する
	
	- 既定のデータベースの場所を調整する。

- ロックされたページを確立して、IO とページング アクティビティを減らします。

## 機能固有のパフォーマンスに関する考慮事項

一部のデプロイメントでは、より高度な構成手法を使用することで、パフォーマンスがさらに向上する場合があります。パフォーマンスの向上を実現する際に役立つ SQL Server の機能を次に示します。

- **Azure Storage へのバックアップ**: Azure 仮想マシンで実行される SQL Server のバックアップを実行する際は、[SQL Server Backup to URL](https://msdn.microsoft.com/library/dn435916.aspx) を使用できます。SQL Server 2012 SP1 CU2 以降で使用できるこの機能は、接続されているデータ ディスクにバックアップする場合に推奨されます。Azure Storage との間でバックアップと復元を行うときは、「[SQL Server Backup to URL に関するベスト プラクティスとトラブルシューティング](https://msdn.microsoft.com/library/jj919149.aspx)」に記載されている推奨事項に従ってください。[Azure Virtual Machines での SQL Server の自動バックアップ](virtual-machines-sql-server-automated-backup.md)を使用して、バックアップを自動化することもできます。

	SQL Server 2012 より前のバージョンでは、[SQL Server Backup to Azure Tool](https://www.microsoft.com/download/details.aspx?id=40740) を使用できます。このツールでは、複数のバックアップ ストライプ ターゲットを使用することで、バックアップ スループットを向上させることができます。

- **Azure 内の SQL Server データ ファイル**: [Azure 内の SQL Server データ ファイル](https://msdn.microsoft.com/library/dn385720.aspx)は、SQL Server 2014 以降で使用できる新機能です。Azure 内のデータ ファイルを使用して SQL Server を実行すると、Azure データ ディスクを使用する場合と同等のパフォーマンス特性が得られます。

## 次のステップ

SQL Server と Premium Storage についてさらに詳しく調べたい場合は、「[Virtual Machines 上での Azure Premium Storage と SQL Server の使用](virtual-machines-sql-server-use-premium-storage.md)」を参照してください。

セキュリティのベスト プラクティスについては、[Azure Virtual Machines における SQL Server のセキュリティに関する考慮事項](virtual-machines-sql-server-security-considerations.md)に関するページを参照してください。

SQL Server Virtual Machines に関する他のトピックについては、[Azure Virtual Machines における SQL Server の概要](virtual-machines-sql-server-infrastructure-services.md)に関するページを参照してください。

<!---HONumber=Sept15_HO3-->