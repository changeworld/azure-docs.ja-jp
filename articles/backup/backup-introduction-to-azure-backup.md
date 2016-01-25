<properties
	pageTitle="Azure Backup とは | Microsoft Azure"
	description="Azure Backup と復元サービスを利用すれば、Windows Server、Windows クライアント コンピューター、SCDPM サーバー、Azure Virtual Machines のデータとアプリケーションをバックアップし、復元できます。"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"
	keywords="バックアップと復元、復元サービス"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016"
	ms.author="trinadhk;jimpark"/>

# Azure Backup とは
Azure Backup は、Microsoft Cloud のデータのバックアップと復元に使用するサービスです。既存のオンプレミスまたはオフサイトのバックアップ ソリューションを、信頼性の高い、セキュリティで保護された、コスト競争力のあるクラウド ベースのソリューションに置き換えます。また、クラウドにあるアセットを保護します。Azure Backup は、拡張性、持続性、高可用性に優れた世界クラスのインフラストラクチャの上に構築された復旧サービスを提供します。

[Azure Backup の概要に関するビデオ](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## Azure Backup を使用する理由
従来のバックアップ ソリューションは、クラウドをディスクやテープなどのエンドポイントのように扱えるように進化してきました。この方法は簡単ですが、制限があり、基盤となるクラウド プラットフォームを最大限に活用していません。これは、非効率的でコストの高いソリューションになります。これに対し、Azure Backup は、強力かつ低コストのクラウド バックアップ ソリューションを最大限に活用します。Azure Backup には次のような利点があります。

| 機能 | 長所 |
| ------- | ------- |
| ストレージ管理の自動化 | オンプレミスのストレージ デバイスに対する設備投資は必要ありません。Azure Backup は、従量制課金モデルを使用して、バックアップ ストレージを自動的に割り当てて管理します。 |
| 無制限のスケーリング | 保守と監視のオーバーヘッドなしに高可用性が保証されます。Azure Backup は、基盤となる Azure クラウドの機能とスケールを使用して、非侵入型の自動スケーリング機能を提供します。 |
| 複数のストレージ オプション | 必要に応じてバックアップ ストレージを選択できます。<li>LRS (ローカル冗長ストレージ) ブロック BLOB は、ローカル ハードウェアの障害からデータを保護しながらコスト意識の高いお客様に最適です。<li>GRS (Geo レプリケーション ストレージ) ブロック BLOB は、ペアになったデータ センターで 3 つの追加コピーを提供し、Azure サイト レベルの障害が発生してもバックアップ データの高可用性を保証します。 |
| 無制限のデータ転送 | Azure Backup コンテナーからの復元操作時の出力 (送信) データ転送にコストはかかりません。Azure へのデータ受信も無料です。 |
| 集中管理 | Azure ポータルの簡単さとわかりやすさ。サービスが進化すると、中央管理などの機能を使用して 1 つの場所からバックアップ インフラストラクチャを管理できるようになります。 |
| データの暗号化 | パブリック クラウドでの顧客データの安全な送信と保存。暗号化パスフレーズはソースに保存されます。転送されたり Azure に保存されたりることはありません。データを復元するには暗号化キーが必要です。お客様のみがサービス内のデータにフル アクセスできます。 |  
| VSS の統合 | Windows でのアプリケーション整合性バックアップにより、復元時に修正は必要ありません。これにより、RTO が削減され、ユーザーは実行状態に短時間で戻ることができます。 |
| 長期的な保持 | ユーザーは、オフサイトのテープ バックアップ ソリューションの代価を払うよりも、かなり低コストでテープに似た形式の強力なソリューションを提供する Azure にバックアップできます。 |

## Azure Backup のコンポーネント
Azure Backup はハイブリッド バックアップ ソリューションなので、連携してエンド ツー エンドのバックアップと復元のワークフローを実現する複数のコンポーネントで構成されます。

![Azure Backup のコンポーネント](./media/backup-introduction-to-azure-backup/azure-backup-overview.png)

## デプロイメント シナリオ

| コンポーネント | Azure にデプロイできる? | オンプレミスにデプロイできる? | サポートされているターゲット ストレージ|
| --- | --- | --- | --- |
| Azure Backup エージェント | <p>**はい**</p> <p>Azure Backup エージェントは、Azure で実行されている任意の Windows Server VM にデプロイできます。</p> | <p>**はい**</p> <p>Azure Backup エージェントは、任意の Windows Server VM または物理マシンにデプロイできます。</p> | Azure Backup コンテナー |
| System Center Data Protection Manager (SCDPM) | <p>**はい**</p> <p>[SCDPM による Azure のワークロードの保護](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx)の詳細を参照してください。</p> | <p>**はい**</p> <p>[データ センターのワークロードと VM の保護](https://technet.microsoft.com/library/hh758173.aspx)の詳細を参照してください。 | ローカルに接続されているディスク、</p><p>Azure Backup コンテナー、</p><p>テープ (オンプレミスのみ)</p> |
| Azure Backup Server | <p>**はい**</p> <p>[Azure Backup Server による Azure のワークロードの保護](backup-azure-microsoft-azure-backup.md)の詳細を参照してください。</p> | <p>**はい**</p> <p>[Azure Backup Server による Azure のワークロードの保護](backup-azure-microsoft-azure-backup.md)の詳細を参照してください。</p> | Azure Backup コンテナー |
| Azure Backup (VM 拡張機能) | <p>はい</p> <p>[Azure IaaS 仮想マシンのバックアップ](backup-azure-vms-introduction.md)に特化しています。</p> | <p>**いいえ**</p> <p>データ センターの仮想マシンをバックアップするには SCDPM を使用します。 | Azure Backup コンテナー</p> |

## アプリケーションとワークロード

| ワークロード | ソース コンピューター | Azure Backup ソリューション |
| --- | --- |---|
| ファイルとフォルダー | Windows Server | <p>[Azure Backup エージェント](backup-configure-vault.md)、</p><p>[System Center DPM](backup-azure-dpm-introduction.md)、</p><p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| ファイルとフォルダー | Windows クライアント | <p>[Azure Backup エージェント](backup-configure-vault.md)、</p><p>[System Center DPM](backup-azure-dpm-introduction.md)、</p><p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Hyper-V 仮想マシン (Windows) | Windows Server | <p>System Center DPM、</p><p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Hyper-V 仮想マシン (Linux) | Windows Server | <p>System Center DPM、</p><p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SQL Server | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md)、</p><p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SharePoint | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md)、</p><p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft Exchange | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md)、</p><p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md)</p> |
| Azure IaaS VM (Windows)| - | [Azure Backup (VM 拡張機能)](backup-azure-vms-introduction.md) | | Azure IaaS VM (Linux) | - | [Azure Backup (VM 拡張機能)](backup-azure-vms-introduction.md) |

## 機能
以下の表では、各コンポーネントにおける Azure Backup の機能の処理方法をまとめます。

### Storage

| 機能 | Azure Backup エージェント | SCDPM | Azure Backup Server | Azure Backup (VM 拡張機能) |
| ------- | --- | --- | --- | ---- |
| Azure Backup コンテナー | ![あり][green] | ![あり][green] | ![あり][green] | ![あり][green] |
| ディスク ストレージ | | ![あり][green] | ![あり][green] | |
| テープ ストレージ | | ![あり][green] | | |
| 圧縮 (バックアップ コンテナー) | ![あり][green] | ![あり][green]| ![あり][green] | |
| 増分バックアップ | ![あり][green] | ![あり][green] | ![あり][green] | ![あり][green] |
| ディスクの重複除去 | | ![部分的][yellow] | ![部分的][yellow]| | |

Azure Backup コンテナーは、すべてのコンポーネントに対して優先されるストレージ ターゲットです。SCDPM と Azure Backup Server は、ローカル ディスク コピーを作成するオプションも提供しますが、テープ ストレージ デバイスにデータを書き込むオプションを提供するのは SCDPM だけです。

#### 増分バックアップ
ターゲット ストレージ (ディスク、テープ、バックアップ コンテナー) とは関係なく、すべてのコンポーネントが増分バックアップをサポートします。前回のバックアップ以降の増分変更のみがターゲット ストレージに転送されるので、ストレージと時間の効率に優れたバックアップが保証されます。さらに、ストレージ使用量を削減するためにバックアップは圧縮されます。

>[AZURE.NOTE]圧縮を行わないコンポーネントは、VM 拡張機能です。すべてのバックアップ データは、圧縮されずに、ユーザーのストレージ アカウントから、同じリージョンにあるバックアップ コンテナーにコピーされます。使用されるストレージは若干増えますが、圧縮しないでデータを格納することにより復元時間を短縮できます。

#### 重複除去
[Hyper-V 仮想マシンにデプロイ](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx)すると、SCDPM と Azure Backup Server で重複除去がサポートされます。重複除去は、バックアップ ストレージとして仮想マシンに接続された VHD で、Windows Server の重複除去機能を利用してホスト レベルで実行されます。

>[AZURE.WARNING]Azure では、どの Azure Backup コンポーネントについても重複除去を使用できません。 SCDPM および Azure Backup Server が Azure にデプロイされている場合は、VM に接続されているストレージ ディスクを重複除去できません。

### セキュリティ

| 機能 | Azure Backup エージェント | SCDPM | Azure Backup Server | Azure Backup (VM 拡張機能) |
| ------- | --- | --- | --- | ---- |
| ネットワーク セキュリティ (対 Azure) | ![あり][green] |![あり][green] | ![あり][green] | ![部分的][yellow]|
| ネットワーク セキュリティ (Azure 内) | ![あり][green] |![あり][green] | ![あり][green] | ![部分的][yellow]|

サーバーから Azure Backup コンテナーへのすべてのバックアップ トラフィックは AES256 を使用して暗号化され、データはセキュリティ保護された HTTPS リンク経由で送信されます。バックアップ コンテナーに格納されるバックアップ データも暗号化されます。このデータをロック解除するためのパスフレーズは、ユーザーだけが持っています。マイクロソフトはいかなる時点でもバックアップ データを暗号解除できません。

>[AZURE.WARNING]バックアップ データの暗号化に使用されるキーは、ユーザーの手元にだけ存在します。マイクロソフトは Azure にコピーを保持していませんし、キーにもアクセスできません。ユーザーがキーを紛失した場合、マイクロソフトはバックアップ データを復旧できません。

Azure VM のバックアップの場合は、ユーザーが仮想マシン*内*で暗号化を明示的に設定する必要があります。Windows 仮想マシンでは BitLocker を使用し、Linux 仮想マシンでは dm-crypt を使用します。Azure Backup では、この経路で受け取るバックアップ データは自動的に暗号化されません。

### サポートされるワークロード

| 機能 | Azure Backup エージェント | SCDPM | Azure Backup Server | Azure Backup (VM 拡張機能) |
| ------- | --- | --- | --- | ---- |
| Windows Server マシン - ファイル/フォルダー | ![あり][green] | ![あり][green] | ![あり][green] | |
| Windows クライアント マシン - ファイル/フォルダー | ![あり][green] | ![あり][green] | ![あり][green] | |
| Hyper-V 仮想マシン (Windows) | | ![あり][green] | ![あり][green] | |
| Hyper-V 仮想マシン (Linux) | | ![あり][green] | ![あり][green] | |
| Microsoft SQL Server | | ![あり][green] | ![あり][green] | |
| Microsoft SharePoint | | ![あり][green] | ![あり][green] | |
| Microsoft Exchange | | ![あり][green] | ![あり][green] | |
| Azure 仮想マシン (Windows) | | | | ![あり][green] |
| Azure 仮想マシン (Linux) | | | | ![あり][green] |

### ネットワーク

| 機能 | Azure Backup エージェント | SCDPM | Azure Backup Server | Azure Backup (VM 拡張機能) |
| ------- | --- | --- | --- | ---- |
| ネットワーク圧縮 (対バックアップ サーバー) | | ![あり][green] | ![あり][green] | |
| ネットワーク圧縮 (対バックアップ コンテナー) | ![あり][green] | ![あり][green] | ![あり][green] | |
| ネットワーク プロトコル (対バックアップ サーバー) | | TCP | TCP | |
| ネットワーク プロトコル (対バックアップ コンテナー) | HTTPS | HTTPS | HTTPS | HTTPS |

VM 拡張機能はデータを Azure Storage アカウントからストレージ ネットワーク経由で直接読み取るので、このトラフィックを最適化する必要はありません。トラフィックは Azure DC のローカル ストレージ ネットワークを経由し、帯域幅のために圧縮が必要になることはほとんどありません。

バックアップ サーバー (SCDPM または Azure Backup Server) へのデータを保護する場合は、プライマリ サーバーからバックアップ サーバーへのトラフィックを圧縮して帯域幅使用量を削減することもできます。

### バックアップと保持

| バックアップ頻度 (対 Azure コンテナー) | 3 バックアップ/日 | 2 バックアップ/日 | 1 バックアップ/日 |
| --- | --- | --- | --- |
| バックアップ頻度 (対ディスク) | 適用不可 | <p>SQL Server の場合は 15 分ごと</p> <p>他のワークロードの場合は 1 時間ごと</p> | 適用不可 |
| 保持オプション | 毎日、毎週、毎月、毎年 | 毎日、毎週、毎月、毎年 | 毎日、毎週、毎月、毎年 |
| 保持期間 | 最大 99 年 | 最大 99 年 | 最大 99 年 |
| Azure コンテナーの回復ポイント | 無制限 | 無制限 | 無制限 |
| ローカル ディスクの回復ポイント | 適用不可 | 適用不可 | 適用不可 |
| テープの回復ポイント | 適用不可 | 適用不可 | 適用不可 |

## Azure Backup と Azure Site Recovery はどのように違いますか
バックアップと障害復旧はよく間違われます。どちらもデータをキャプチャして復元セマンティクスを提供しますが、中核となる価値提案はそれぞれ異なります。

Azure Backup は、オンプレミスのデータまたはクラウド内のデータをバックアップします。Azure Site Recovery は、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、フェールバックを調整します。障害復旧戦略では、データを安全で復旧可能な状態に保ち (Azure Backup)、かつ、障害が発生したときにワークロードを使用可能でアクセス可能な状態に保つ (Azure Site Recovery) 必要があるので、完全な障害復旧ソリューションにはどちらも必要です。

バックアップおよび障害復旧に関する意思決定を行うには、いくつかの重要な概念を理解していなければなりません。

| 概念 | 詳細 | バックアップ | 障害復旧 (DR) |
| ------- | ------- | ------ | ----------------- |
| 目標復旧時点 (RPO) | 復旧を行う必要がある場合に許容されるデータ損失の量。 | バックアップ ソリューションで許容される RPO はさまざまです。仮想マシンのバックアップの RPO は通常 1 日であるのに対し、データベースのバックアップの RPO は最低 15 分です。 | 障害復旧ソリューションでは、RPO は非常に低くなります。DR コピーは、数秒遅れまたは数分遅れのことがあります。 |
| 目標復旧時間 (RTO) | 復旧/復元の完了に要する時間です。 | RPO が大きくなるほど、バックアップ ソリューションで処理する必要のあるデータ量が通常は増えるので、RTO は長くなります。たとえば、オフサイトの場所からテープを輸送するのにかかる時間によっては、テープからのデータの復元に日単位の時間を要する場合があります。 | 障害復旧ソリューションでは、ソースとの同期レベルが高く、処理の必要な変更が少ないので、RTO は小さくなります。 |
| 保持 | データを保存する必要がある期間 | <p>運用の復旧を必要とするシナリオでは (データの破損、不注意によるファイルの削除、OS の障害)、通常、バックアップ データの保持期間は最大 30 日です。</p> <p>コンプライアンスの観点からは、月単位または年単位でのデータの保存が必要になります。バックアップ データは、このような場合のアーカイブに最適です。</p> | 障害復旧では、運用復旧データのみが必要であり、通常は数時間分または最大でも 1 日分です。DR ソリューションでは詳細なデータ キャプチャが使用されるため、DR データを使用する長期的な保持はお勧めできません。 |

## 次のステップ

- [Azure Backup を試す](backup-try-azure-backup-in-10-mins.md)
- Azure Backup サービスに関してよく寄せられる質問は、[こちら](backup-azure-backup-faq.md)をご覧ください。
- [Azure Backup フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=290933)にアクセスします。


[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

<!---HONumber=AcomDC_0114_2016-->