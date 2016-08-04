<properties
	pageTitle="Azure Backup とは | Microsoft Azure"
	description="Azure Backup と Recovery Services を利用すれば、Windows Server、Windows クライアント コンピューター、System Center DPM サーバー、Azure Virtual Machines のデータとアプリケーションをバックアップして、復元できます。"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"
	keywords="バックアップと復元, 復元サービス, バックアップ ソリューション"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/21/2016"
	ms.author="jimpark; trinadhk"/>

# Azure Backup とは
Azure Backup は、Microsoft Cloud のデータのバックアップと復元に使用するサービスです。既存のオンプレミスまたはオフサイトのバックアップ ソリューションを、信頼性の高い、セキュリティで保護された、コスト競争力のあるクラウド ベースのソリューションに置き換えます。また、クラウド内で実行されている資産を保護できます。Azure Backup は、拡張性、持続性、高可用性に優れた世界クラスのインフラストラクチャの上に構築された Recovery Services を提供します。

[Azure Backup の概要に関するビデオ](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## Azure Backup を使用する理由
従来のバックアップ ソリューションは、クラウドをディスクやテープなどのエンドポイントのように扱えるように進化してきました。この方法は単純ですが、制限もあります。たとえば、基礎となるプラットフォームを最大限に活用することができないため、非効率的でコストの高いソリューションになってしまいます。これに対し、Azure Backup では、強力で低コストなクラウド バックアップ ソリューションの利点を最大限に引き出すことができます。Azure Backup には次のような利点があります。

| 機能 | 長所 |
| ------- | ------- |
| ストレージ管理の自動化 | オンプレミスのストレージ デバイスに対する設備投資は必要ありません。Azure Backup は、従量制課金モデルを使用して、バックアップ ストレージを自動的に割り当てて管理します。 |
| 無制限のスケーリング | 保守と監視のオーバーヘッドなしに高可用性が保証されます。Azure Backup は、基盤となる Azure クラウドの機能とスケールを使用して、非侵入型の自動スケール機能を提供します。 |
| 複数のストレージ オプション | 必要に応じてバックアップ ストレージを選択できます。<li>ローカル冗長ストレージ ブロック BLOB は、コスト意識の高いお客様に最適です。ローカル ハードウェアの障害からデータを保護するうえでも役立ちます。<li>geo レプリケーション ストレージ ブロック BLOB は、ペアリングされているデータセンターに、さらに 3 つのコピーを提供します。この追加のコピーによって、Azure サイトレベルの障害が発生した場合でも、バックアップ データの高可用性を確保することができます。 |
| 無制限のデータ転送 | Backup コンテナーからの復元操作時の出力 (送信) データ転送は無料です。Azure へのデータ受信も無料です。利用できる場合はインポート サービスと連携します。 |
| データの暗号化 | データの暗号化によって、パブリック クラウドでの顧客データを安全に送信および保存できます。暗号化パスフレーズはソースに保存されます。転送されたり Azure に保存されたりすることはありません。データを復元するには暗号化キーが必要です。お客様のみがサービス内のデータにフル アクセスできます。 |  
| アプリケーションの整合性のバックアップ | Windows でのアプリケーション整合性バックアップの場合、復元時に修正は不要であり、目標復旧時間が短縮されます。ユーザーは迅速に実行状態に戻ることができます。 |
| 長期保存 | Azure にバックアップすると、ユーザーは、オフサイトのテープ バックアップ ソリューションに代価を払うよりも、低コストでテープに似た強力なソリューションを利用できます。 |

## Azure Backup のコンポーネント
Backup はハイブリッド バックアップ ソリューションなので、連携してエンド ツー エンドのバックアップと復元のワークフローを実現する複数のコンポーネントで構成されます。

![Azure Backup のコンポーネント](./media/backup-introduction-to-azure-backup/azure-backup-overview.png)

### デプロイメント シナリオ

| コンポーネント | Azure にデプロイできる? | オンプレミスにデプロイできる? | サポートされているターゲット ストレージ|
| --- | --- | --- | --- |
| Azure Backup エージェント | <p>**はい**</p> <p>Azure Backup エージェントは、Azure で実行されている任意の Windows Server VM にデプロイできます。</p> | <p>**はい**</p> <p>Backup エージェントは、任意の Windows Server VM または物理マシンにデプロイできます。</p> | <p>Azure Backup コンテナー</p> |
| System Center Data Protection Manager (DPM) | <p>**はい**</p><p>詳細については、[System Center DPM を使用して Azure のワークロードを保護する方法](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx)を確認してください。</p> | <p>**はい**</p> <p>詳細については、[自社のデータセンターのワークロードと VM を保護する方法](https://technet.microsoft.com/library/hh758173.aspx)を確認してください。</p> | <p>ローカルに接続されているディスク、</p><p>Azure Backup コンテナー、</p><p>テープ (オンプレミスのみ)</p> |
| Azure Backup Server | <p>**はい**</p><p>詳細については、[Azure Backup Server を使用して Azure のワークロードを保護する方法](backup-azure-microsoft-azure-backup.md)を確認してください。</p> | <p>**はい**</p> <p>詳細については、[Azure Backup Server を使用して Azure のワークロードを保護する方法](backup-azure-microsoft-azure-backup.md)を確認してください。</p> | <p>ローカルに接続されているディスク、</p><p>Azure Backup コンテナー</p> |
| Azure Backup (VM 拡張機能) | <p>**はい**</p><p>Azure のファブリックの一部</p><p>[Azure のサービスとしてのインフラストラクチャ (IaaS) 仮想マシンのバックアップ](backup-azure-vms-introduction.md)に特化しています。</p> | <p>**いいえ**</p> <p>System Center DPM を使用して、データセンターの仮想マシンをバックアップします。</p> | <p>Azure Backup コンテナー</p> |

### 各コンポーネントのメリットと制限事項

| コンポーネント | メリット | 制限事項 | 回復の単位 |
| --- | --- | --- | --- |
| Azure Backup (MARS) エージェント | <li>物理と仮想のどちらの Windows OS マシンでもファイルとフォルダーをバックアップできる (VM はオンプレミスと Azure のどちらにも配置できる)<li>別途バックアップ サーバーは不要<li>Azure Backup コンテナーを使用 | <li>バックアップ/ファイル レベルの復元は 1 日 3 回まで<li>ファイル/フォルダー/ボリューム レベルの復元のみ。アプリケーションは認識されない<li>Linux には非対応 | ファイル/フォルダー/ボリューム |
| System Center Data Protection Manager | <li>アプリ認識スナップショット (VSS)<li>バックアップを実行するタイミングを思いどおりに設定可能<li>きめ細かい回復単位 (全レベル)<li>Azure Backup コンテナーを使用可能<li>Linux に対応 (Hyper-V でホストされている場合) | <li>異機種混在環境には非対応 (VMware VM バックアップ、Oracle ワークロード バックアップ) | ファイル/フォルダー/ボリューム<br>/VM/アプリケーション |
| Microsoft Azure Backup サーバー | <li>アプリ認識スナップショット (VSS)<li>バックアップを実行するタイミングを思いどおりに設定可能<li>きめ細かい回復単位 (全レベル)<li>Azure Backup コンテナーを使用可能<li>Linux に対応 (Hyper-V でホストされている場合)<li>System Center ライセンスは不要 | <li>異機種混在環境には非対応 (VMware VM バックアップ、Oracle ワークロード バックアップ)<li>常にアクティブな Azure サブスクリプションが必須<li>テープ バックアップには非対応 | ファイル/フォルダー/ボリューム<br>/VM/アプリケーション |
| Azure IaaS VM のバックアップ | <li>Windows/Linux のネイティブ バックアップ<li>特定のエージェントのインストールが不要<li>バックアップ インフラストラクチャを必要としないファブリック レベルのバックアップ | <li>バックアップ/ディスク レベルの復元は 1 日に 1 回<li>オンプレミスでバックアップできない | VM<br>すべてのディスク (PowerShell を使用) |

## バックアップできるアプリケーションとワークロード

| ワークロード | ソース コンピューター | Azure Backup ソリューション |
| --- | --- |---|
| ファイルとフォルダー | Windows Server | <p>[Azure Backup エージェント](backup-configure-vault.md)、</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ Azure Backup エージェント)、</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (Azure Backup エージェントを含む)</p> |
| ファイルとフォルダー | Windows クライアント | <p>[Azure Backup エージェント](backup-configure-vault.md)、</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ Azure Backup エージェント)、</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (Azure Backup エージェントを含む)</p> |
| Hyper-V 仮想マシン (Windows) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ Azure Backup エージェント)、</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (Azure Backup エージェントを含む)</p> |
| Hyper-V 仮想マシン (Linux) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ Azure Backup エージェント)、</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (Azure Backup エージェントを含む)</p> |
| Microsoft SQL Server | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ Azure Backup エージェント)、</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (Azure Backup エージェントを含む)</p> |
| Microsoft SharePoint | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ Azure Backup エージェント)、</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (Azure Backup エージェントを含む)</p> |
| Microsoft Exchange | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md) (+ Azure Backup エージェント)、</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (Azure Backup エージェントを含む)</p> |
| Azure IaaS VM (Windows) | - | [Azure Backup (VM 拡張機能)](backup-azure-vms-introduction.md) | 
| Azure IaaS VM (Linux) | - | [Azure Backup (VM 拡張機能)](backup-azure-vms-introduction.md) |

## ARM と Linux のサポート

| コンポーネント | ARM サポート | Linux (Azure での動作保証済み) サポート |
| --- | --- | --- |
| Azure Backup (MARS) エージェント | あり | なし (Windows ベースのエージェントのみ) |
| System Center Data Protection Manager | あり (ゲスト内のエージェント) | Hyper-V のみ (Azure VM は不可) ファイル整合性のバックアップのみが可能 |
| Azure Backup Server (MABS) | あり (ゲスト内のエージェント) | Hyper-V のみ (Azure VM は不可) ファイル整合性のバックアップのみが可能 (DPM と同じ) |
| Azure IaaS VM のバックアップ | はい | はい |

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]


## Premium Storage VM のバックアップと復元

Azure Backup サービスで、Premium Storage VM が保護されるようになりました。

### Premium Storage VM のバックアップ

Premium Storage VM をバックアップすると、Backup サービスによって、Premium ストレージ アカウントの一時的なステージング場所が作成されます。"AzureBackup-" というステージングの場所は、VM にアタッチされている Premium ディスクの合計データ サイズと同じです。

>[AZURE.NOTE] ステージングの場所は変更または編集できません。

バックアップ ジョブが完了したら、ステージングの場所は削除されます。ステージングの場所に使用されるストレージの価格は、すべての [Premium Storage の価格](../storage/storage-premium-storage.md#pricing-and-billing)に準じます。

### Premium Storage VM の復元

Premium Storage VM は、Premium Storage と通常のストレージのどちらかに復元することができます。Premium Storage VM の復旧ポイントを Premium Storage に復元する方法が、復元の一般的なプロセスです。ただし、Premium Storage VM の復旧ポイント Standard Storage に復元する方がコスト効率が良い場合があります。このような復元は、VM の一部のファイルのみが必要な場合に利用できます。

## 機能
以下の 5 つの表では、各コンポーネントにおける Backup の機能の処理方法をまとめます。

### Storage

| 機能 | Azure Backup エージェント | System Center DPM | Azure Backup Server | Azure Backup (VM 拡張機能) |
| ------- | --- | --- | --- | ---- |
| Azure Backup コンテナー | ![Yes][green] | ![はい][green] | ![はい][green] | ![はい][green] |
| ディスク ストレージ | | ![はい][green] | ![はい][green] | |
| テープ ストレージ | | ![はい][green] | | |
| 圧縮 (バックアップ コンテナー) | ![はい][green] | ![はい][green]| ![はい][green] | |
| 増分バックアップ | ![はい][green] | ![はい][green] | ![はい][green] | ![Yes][green] |
| ディスクの重複除去 | | ![部分的][yellow] | ![部分的][yellow]| | |

![table key](./media/backup-introduction-to-azure-backup/table-key.png)

バックアップ コンテナーは、すべてのコンポーネントの中で優先されるストレージ ターゲットです。System Center DPM と Backup Server には、ローカル ディスク コピーを作成するオプションもありますが、テープ ストレージ デバイスにデータを書き込むオプションがあるのは System Center DPM のみです。

#### 増分バックアップ
ターゲット ストレージ (ディスク、テープ、バックアップ コンテナー) がどれであるかにかかわらず、すべてのコンポーネントが増分バックアップをサポートします。増分バックアップは、前回のバックアップ以降に行われた変更のみを転送対象とすることで、高い保存効率と時間効率を実現します。

#### 圧縮
必要となるストレージ使用量を削減するために、バックアップが圧縮されます。VM 拡張機能は、圧縮が行われない唯一のコンポーネントです。VM 拡張機能では、すべてのバックアップ データが圧縮されずに、ユーザーのストレージ アカウントから、同じリージョンにあるバックアップ コンテナーにコピーされます。圧縮せずにデータを格納することで、使用されるストレージは若干増えますが、復元時間が短縮されます。

#### 重複除去
System Center DPM と Backup Server では、[Hyper-V 仮想マシンにデプロイ](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx)すると、重複除去がサポートされます。重複除去は、バックアップ ストレージとして仮想マシンに接続された仮想ハード ディスク (VHD) 上で、Windows Server の重複除去を使用してホスト レベルで実行されます。

>[AZURE.WARNING] Azure では、どの Backup コンポーネントについても重複除去を使用できません。System Center DPM と Backup Server が Azure にデプロイされている場合は、VM に接続されているストレージ ディスクを重複除去できません。

### セキュリティ

| 機能 | Azure Backup エージェント | System Center DPM | Azure Backup Server | Azure Backup (VM 拡張機能) |
| ------- | --- | --- | --- | ---- |
| ネットワーク セキュリティ (対 Azure) | ![あり][green] |![はい][green] | ![Yes][green] | ![部分的][yellow]|
| ネットワーク セキュリティ (Azure 内) | ![Yes][green] |![はい][green] | ![Yes][green] | ![部分的][yellow]|

![table key](./media/backup-introduction-to-azure-backup/table-key.png)

サーバーからバックアップ コンテナーまでのすべてのバックアップ トラフィックは、Advanced Encryption Standard 256 を使用して暗号化されます。データは、セキュリティで保護された HTTPS リンク上で送信されます。また、バックアップ データは、暗号化された形式でバックアップ コンテナーにも格納されます。このデータのロックを解除するパスフレーズは、お客様のみが保持します。マイクロソフトは、どの時点でもバックアップ データの暗号化を解除できせん。

>[AZURE.WARNING] バックアップ データの暗号化に使用されるキーは、ユーザーの手元にだけ存在します。マイクロソフトは Azure にコピーを保持していませんし、キーにもアクセスできません。ユーザーがキーを紛失した場合、マイクロソフトはバックアップ データを復旧できません。

Azure VM のバックアップの場合は、仮想マシン "*内*" で暗号化を設定する必要があります。Windows 仮想マシンでは BitLocker を使用し、Linux 仮想マシンでは **dm-crypt** を使用します。Azure Backup では、この経路で受け取るバックアップ データは自動的に暗号化されません。

### サポートされるワークロード

| 機能 | Azure Backup エージェント | System Center DPM | Azure Backup Server | Azure Backup (VM 拡張機能) |
| ------- | --- | --- | --- | ---- |
| Windows Server コンピューター -- ファイルとフォルダー | ![あり][green] | ![はい][green] | ![はい][green] | |
| Windows クライアント コンピューター -- ファイルとフォルダー | ![はい][green] | ![はい][green] | ![はい][green] | |
| Hyper-V 仮想マシン (Windows) | | ![はい][green] | ![はい][green] | |
| Hyper-V 仮想マシン (Linux) | | ![はい][green] | ![はい][green] | |
| Microsoft SQL Server | | ![はい][green] | ![はい][green] | |
| Microsoft SharePoint | | ![はい][green] | ![はい][green] | |
| Microsoft Exchange | | ![はい][green] | ![はい][green] | |
| Azure 仮想マシン (Windows) | | | | ![はい][green] |
| Azure 仮想マシン (Linux) | | | | ![あり][green] |

![table key](./media/backup-introduction-to-azure-backup/table-key-2.png)

### ネットワーク

| 機能 | Azure Backup エージェント | System Center DPM | Azure Backup Server | Azure Backup (VM 拡張機能) |
| ------- | --- | --- | --- | ---- |
| ネットワーク圧縮 (対バックアップ サーバー) | | ![あり][green] | ![はい][green] | |
| ネットワーク圧縮 (対バックアップ コンテナー) | ![はい][green] | ![はい][green] | ![あり][green] | |
| ネットワーク プロトコル (対バックアップ サーバー) | | TCP | TCP | |
| ネットワーク プロトコル (対バックアップ コンテナー) | HTTPS | HTTPS | HTTPS | HTTPS |

![table key](./media/backup-introduction-to-azure-backup/table-key-2.png)

VM 拡張機能はデータを Azure ストレージ アカウントからストレージ ネットワーク経由で直接読み取るので、このトラフィックを最適化する必要はありません。トラフィックは Azure データセンターのローカル ストレージ ネットワークを経由し、帯域幅のために圧縮が必要になることはほとんどありません。

バックアップ サーバー (DPM または Backup Server) へのデータを保護する場合は、プライマリ サーバーからバックアップ サーバーへのトラフィックを圧縮して帯域幅を削減できます。

#### ネットワークのスロットル
Azure Backup エージェントには、データ転送時のネットワーク帯域幅の使用方法を制御できるスロットル機能があります。業務時間中に実行が必要なデータのバックアップ処理が他のインターネット トラフィックに干渉しないようにする必要がある場合などに効果を発揮します。データ転送のスロットルはバックアップと復元のアクティビティに適用されます。

### バックアップと保持

| | Azure Backup エージェント | System Center DPM | Azure Backup Server | Azure Backup (VM 拡張機能) |
| --- | --- | --- | --- | --- |
| バックアップ頻度 (対バックアップ コンテナー) | 3 バックアップ/日 | 2 バックアップ/日 |2 バックアップ/日 | 1 バックアップ/日 |
| バックアップ頻度 (対ディスク) | 適用不可 | <p>SQL Server の場合は 15 分ごと</p> <p>他のワークロードの場合は 1 時間ごと</p> | <p>SQL Server の場合は 15 分ごと</p> <p>他のワークロードの場合は 1 時間ごと</p> |適用不可 |
| 保持オプション | 毎日、毎週、毎月、毎年 | 毎日、毎週、毎月、毎年 | 毎日、毎週、毎月、毎年 |毎日、毎週、毎月、毎年 |
| 保持期間 | 最大 99 年 | 最大 99 年 | 最大 99 年 | 最大 99 年 |
| バックアップ コンテナーの回復ポイント | 無制限 | 無制限 | 無制限 | 無制限 |
| ローカル ディスクの回復ポイント | 適用不可 | ファイル サーバーの場合 64 個、<br><br>アプリケーション サーバーの場合 448 個 | ファイル サーバーの場合 64 個、<br><br>アプリケーション サーバーの場合 448 個 |適用不可 |
| テープの回復ポイント | 適用不可 | 無制限 | 適用不可 | 適用不可 |

## 資格情報コンテナーの資格情報ファイルとは

資格情報コンテナーの資格情報ファイルは、各バックアップ資格情報コンテナーごとにポータルによって生成される証明書です。ポータルは公開キーを Access Control Service (ACS) にアップロードします。ユーザーは、資格情報をダウンロードする際に提供される秘密キーをコンピューターの登録時に入力します。コンピューターは公開キーによって認証され、Azure Backup サービスで識別されたコンテナーにバックアップ データを送信できるようになります。

資格情報コンテナーの資格情報は登録ワークフロー中しか使用されません。コンテナーの資格情報ファイルが漏えいしないようにするのはユーザーの責任です。悪意のあるユーザーが入手した場合、資格情報コンテナーの資格情報ファイルを使用し、同じ資格情報コンテナーに対してその他のコンピューターが登録されてしまう可能性があります。ただし、バックアップ データは顧客ごとに専用のパスフレーズを使用して暗号化されているため、既存のバックアップ データが漏えいすることはありません。また、このような懸念を緩和するために、コンテナーの資格情報は 48 時間で期限切れになるよう設定されています。バックアップ コンテナーの資格情報は何回でもダウンロードできますが、登録ワークフロー時に適用できるのは最新のファイルのみです。

## Azure Backup と Azure Site Recovery はどのように違いますか
バックアップの復元と障害復旧はよく間違われます。どちらもデータをキャプチャして復元セマンティクスを提供しますが、中核となる価値提案は異なります。

Azure Backup は、オンプレミスのデータまたはクラウド内のデータをバックアップします。Azure Site Recovery は、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、フェールバックを調整します。障害復旧ソリューションでは、データの安全性と復元可能性を維持し (Backup)、"*さらに*"、障害が発生したときにワークロードの可用性を維持する (Site Recovery) 必要があるため、どちらのサービスも重要です。

バックアップと障害復旧に関する意思決定を行うにあたっては、次の概念が役立ちます。

| 概念 | 詳細 | バックアップ | 障害復旧 (DR) |
| ------- | ------- | ------ | ----------------- |
| 目標復旧時点 (RPO) | 復旧を行う必要がある場合に許容されるデータ損失の量です。 | バックアップ ソリューションの許容されるRPO には幅があります。仮想マシンのバックアップの RPO は通常 1 日であるのに対し、データベースのバックアップの RPO は最低 15 分です。 | 障害復旧ソリューションでは RPO が低くなります。DR コピーは、数秒遅れまたは数分遅れのことがあります。 |
| 目標復旧時間 (RTO) | 復旧または復元の完了に要する時間です。 | RPO が大きくなるほど、一般的にはバックアップ ソリューションで処理が必要なデータ量が増えるため、RTO は長くなります。たとえば、オフサイトの場所からテープを輸送するのにかかる時間によっては、テープからのデータの復元に日単位の時間を要する場合があります。 | 障害復旧ソリューションは、よりソースと同期されているため、RTO は短くなります。必要な変更は少数です。 |
| 保持 | データを保存する必要がある期間 | 運用の復旧を必要とするシナリオ (データの破損、不注意によるファイルの削除、OS の障害) では、通常、バックアップ データの保持期間は最大 30 日です。<br>コンプライアンスの観点から、月単位または年単位でデータを保存することが必要になる場合があります。バックアップ データは、このような場合のアーカイブに最適です。 | 障害復旧には運用復旧データのみが必要となり、実行には通常、数時間から最大で 1 日かかります。DR ソリューションでは詳細なデータ キャプチャが使用されるため、DR データを使用する長期的な保持はお勧めできません。 |

## 次のステップ

シンプルな Azure Backup をお試しください。手順については、次のいずれかのチュートリアルを参照してください。

- [Azure Backup を試す](backup-try-azure-backup-in-10-mins.md)
- [Azure VM Backup を試す](backup-azure-vms-first-look.md)

これらのチュートリアルに従うとすぐにバックアップできるため、データをバックアップするための最短コースしか確認できません。実行するバックアップの種類に関する詳細については、次のページを参照してください。

- [Windows マシンのバックアップ](backup-configure-vault.md)
- [アプリケーション ワークロードのバックアップ](backup-azure-microsoft-azure-backup.md)
- [Azure IaaS VM のバックアップ](backup-azure-vms-prepare.md)



[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

<!---HONumber=AcomDC_0727_2016-->