---
title: "Azure Backup の FAQ | Microsoft Docs"
description: "バックアップ サービス、バックアップ エージェント、バックアップと保持、復元、セキュリティに関してよく寄せられる質問と、バックアップや障害復旧に関するその他の一般的な質問に対する回答。"
services: backup
documentationcenter: 
author: markgalioto
manager: jwhit
editor: 
keywords: "バックアップと障害復旧; バックアップ サービス"
ms.assetid: 1011bdd6-7a64-434f-abd7-2783436668d7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/21/2016
ms.author: trinadhk; giridham; arunak; markgal; jimpark;
translationtype: Human Translation
ms.sourcegitcommit: e29891dc03f8a864ecacc893fd1cc0d3cc1436cb
ms.openlocfilehash: f85b3210fc1bdab65da29c3355ed3e1eb35da2ab


---
# <a name="azure-backup-service-faq"></a>Azure Backup サービス - FAQ
この記事は、Azure Backup サービスに関してよく寄せられる質問 (およびそれぞれに対する回答) を一覧にしたものです。 Azure コミュニティでは質問に対して短時間で回答が寄せられています。この記事には、コミュニティで頻繁に寄せられる質問を追加しています。 一般的に、質問への回答ではリファレンスやサポート情報が提供されています。 この記事や関連の記事の「DISQUS」セクションでも Azure Backup について質問できます。 また、 [ディスカッション フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)でも、Azure Backup サービスに関する質問を投稿できます。

## <a name="what-is-the-list-of-supported-operating-systems-from-which-i-can-back-up-to-azure-using-azure-backup-br"></a>どのようなオペレーティング システムが Azure Backup を使用した Azure へのバックアップでサポートされていますか。 <br/>
Azure Backup では、Azure Backup Server および SCDPM を使用したファイル フォルダー バックアップとアプリケーション バックアップ向けに、以下の一覧に示すオペレーティング システムがサポートされています。

| オペレーティング システム | プラットフォーム | SKU |
|:--- | --- |:--- |
| Windows 8 と最新 SP |64 ビット |Enterprise、Pro |
| Windows 7 と最新 SP |64 ビット |Ultimate、Enterprise、Professional、Home Premium、Home Basic、Starter |
| Windows 8.1 と最新 SP |64 ビット |Enterprise、Pro |
| Windows 10 |64 ビット |Enterprise、Pro、Home |
| Windows Server 2012 R2 と最新 SP |64 ビット |Standard、Datacenter、Foundation |
| Windows Server 2012 と最新 SP |64 ビット |Datacenter、Foundation、Standard |
| Windows Storage Server 2012 R2 と最新 SP |64 ビット |Standard、Workgroup |
| Windows Storage Server 2012 と最新 SP |64 ビット |Standard、Workgroup |
| Windows Server 2012 R2 と最新 SP |64 ビット |Essential |
| Windows Server 2008 R2 SP1 |64 ビット |Standard、Enterprise、Datacenter、Foundation |
| Windows Server 2008 SP2 |64 ビット |Standard、Enterprise、Datacenter、Foundation |

Azure VM バックアップのサポート

* **Linux**: Azure Backup は、Core OS Linux を除き、[Azure で承認されている一連のディストリビューション](../virtual-machines/virtual-machines-linux-endorsed-distros.md) をサポートしています。  他の個人所有の Linux ディストリビューションも、仮想マシン上で VM エージェントが動作し、かつ Python がサポートされていれば使用できます。
* **Windows Server**: Windows Server 2008 R2 より前のバージョンはサポートされていません。

## <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>最新の Azure Backup エージェントはどこでダウンロードできますか。 <br/>
Windows Server、System Center DPM、または Windows クライアントをバックアップするための最新のエージェントは、[こちら](http://aka.ms/azurebackup_agent)からダウンロードできます。 仮想マシンをバックアップする場合は、VM エージェントを使用してください (適切な拡張機能が自動的にインストールされます)。 VM エージェントは、Azure ギャラリーから作成された仮想マシン内に既に存在しています。

## <a name="which-version-of-scdpm-server-is-supported-br"></a>どのバージョンの SCDPM サーバーがサポートされていますか。 <br/>
[最新版](http://aka.ms/azurebackup_agent) の Azure Backup エージェントを SCDPM の最新の更新プログラムのロールアップにインストールすることをお勧めします (2016 年 8 月時点で UR11)。

## <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>Azure Backup エージェントを構成するときに、コンテナー資格情報の入力が求められます。 コンテナー資格情報には有効期限がありますか。
はい。コンテナー資格情報は 48 時間後に有効期限が切れます。 ファイルの有効期限が切れた場合は、Azure Portal にログインし、コンテナーからコンテナー資格情報ファイルをダウンロードしてください。

## <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>各 Azure サブスクリプションで作成できるコンテナーの数に制限はありますか。 <br/>
はい。 2016 年 9 月の時点では、サブスクリプションあたり 25 個のバックアップ コンテナーを作成できます。 各サブスクリプションでサポートされている Azure Backup のリージョンごとに、最大 25 個の Recovery Services コンテナーを作成できます。 さらにコンテナーが必要な場合は、新しいサブスクリプションを作成してください。

## <a name="are-there-any-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>各資格情報コンテナーに登録できるサーバーやマシンの数に制限はありますか。 <br/>
はい。コンテナーにつき最大 50 のコンピューターを登録できます。 Azure IaaS 仮想マシンの場合、コンテナーあたりの VM の上限は 200 です。 さらにコンピューターを登録する必要がある場合は、新しい資格情報コンテナーを作成してください。

## <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>別のデータ センターにサーバーを登録する方法を教えてください。<br/>
バックアップ データは登録されているコンテナーのデータセンターに送信されます。 データセンターを変更する最も簡単な方法は、エージェントをアンインストールしてから再インストールし、変更先のデータセンターに属している新しいコンテナーに登録する方法です。

## <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>Azure にデータをバックアップしている Windows サーバーの名前を変更するとどうなりますか。<br/>
サーバーの名前を変更すると、現在構成されているすべてのバックアップが停止します。
Backup コンテナーにサーバーの新しい名前を登録する必要があります。 新たに登録を作成するときは、初回のバックアップ操作は増分バックアップではなく完全バックアップになります。 以前のサーバー名でコンテナーにバックアップしたデータを回復する必要がある場合は、**データの回復**ウィザードで [**[別のサーバー]**](backup-azure-restore-windows-server.md#recover-to-an-alternate-machine) オプションを使用してそのデータを回復できます。

## <a name="what-types-of-drives-can-i-backup-files-and-folders-from-br"></a>ファイルとフォルダーのバックアップ元として、どのような種類のドライブを使用できますか。 <br/>
次の一連のドライブやボリュームはバックアップできません。

* リムーバブル メディア: バックアップ項目のソースを使用するには、固定のドライブとして認識される必要があります。
* 読み取り専用ボリューム: ボリューム シャドウ コピー サービス (VSS) が機能するには、ボリュームが書き込み可能である必要があります。
* オフライン ボリューム: VSS が機能するには、ボリュームがオンラインである必要があります。
* ネットワーク共有: オンライン バックアップを使用してバックアップするボリュームは、サーバーに対してローカルである必要があります。
* Bitlocker で保護されているボリューム: バックアップを行う前に、ボリュームのロックを解除する必要があります。
* ファイル システムの識別: このバージョンのオンライン バックアップ サービスでサポートされているファイル システムは NTFS だけです。

## <a name="what-file-and-folder-types-can-i-back-up-from-my-serverbr"></a>サーバーからバックアップできるのはどのような種類のファイルとフォルダーですか。<br/>
次の種類がサポートされています。

* 暗号化
* 圧縮
* スパース
* 圧縮 + スパース
* ハード リンク: サポートされていません。スキップされます。
* 再解析ポイント: サポートされていません。スキップされます。
* 暗号化 + 圧縮: サポートされていません。スキップされます。
* 暗号化 + スパース: サポートされていません。スキップされます。
* 圧縮されたストリーム: サポートされていません。スキップされます。
* スパース ストリーム: サポートされていません。スキップされます。

## <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>キャッシュ フォルダーの最小サイズ要件を教えてください。 <br/>
キャッシュ フォルダーのサイズによって、バックアップするデータ量が決まります。 キャッシュ フォルダーは、データの格納に必要なスペースの 5% に設定する必要があります。

## <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>組織で所有しているコンテナーが 1 つの場合、データを復元する際に特定のサーバーのデータを別のサーバーから分離するには、どうすればよいですか。<br/>
同じコンテナーに登録されたサーバーはどれもが、 *同じパスフレーズを使用する*他のサーバーによってバックアップされたデータを復元できます。 サーバーのバックアップ データを組織内の他のサーバーから分離する必要がある場合は、これらのサーバーごとに指定したパスフレーズを使用します。 たとえば、人事部門のサーバーで特定の暗号化パスフレーズを使用し、経理部門のサーバーで 2 番目、ストレージ サーバーで 3 番目の暗号化パスフレーズを使用することができます。

## <a name="can-i-migrate-my-backup-data-or-vault-between-subscriptions-br"></a>サブスクリプション間でバックアップ データまたはコンテナーを "移行" することはできますか。 <br/>
いいえ。 コンテナーはサブスクリプション レベルで作成されるため、作成後に別のサブスクリプションに再割り当てすることはできません。

## <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>Windows Server 2012 の重複除去を使用するサーバーでは、Azure Backup エージェントは動作しますか。 <br/>
はい。 エージェント サービスは、バックアップ操作を準備するときに、重複除去されたデータを通常のデータに変換します。 そのデータをバックアップ用に最適化し、暗号化してから、オンライン バックアップ サービスに送信します。

## <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>バックアップを開始した後でバックアップ ジョブを取り消すと、転送されたバックアップ データは削除されますか。 <br/>
いいえ。 バックアップ コンテナーには、取り消しの時点までに転送されたバックアップ データが格納されます。 Azure Backup では、チェックポイント メカニズムを使用して、バックアップ中に随時バックアップ データにチェックポイントを追加します。 バックアップ データにチェックポイントがあることで、次回のバックアップ処理でファイルの整合性を検証できます。 トリガーされる次のバックアップは、以前にバックアップされたデータに対する増分なります。 増分バックアップでは、帯域幅の使用状況が向上し、同じデータを繰り返し転送する必要がなくなります。

Azure VM バックアップの場合、ジョブが取り消されると転送済みのデータは無視され、新たなバックアップの際に以前成功したバックアップ ジョブからの増分データが転送されます。

## <a name="why-am-i-seeing-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-had-scheduled-regular-backups-previously-br"></a>事前に定期的なバックアップのスケジュールを設定してあるのに、"このサーバーに対して Azure Backups は構成されていません" という警告が表示されるのはなぜですか。 <br/>
ローカル サーバーに保存されているバックアップ スケジュールの設定がバックアップ コンテナーに格納されている設定と異なる場合、このような警告が表示されることがあります。 サーバーまたは設定が既知の正常な状態に復旧されると、バックアップ スケジュールの同期が失われることがあります。 この警告が表示された場合は、 [バックアップ ポリシーを再構成](backup-azure-manage-windows-server.md) した後、 **[今すぐバックアップ]** を実行して、ローカル サーバーと Azure を再同期してください。

## <a name="what-firewall-rules-should-be-configured-for-azure-backup-br"></a>どのようなファイアウォール ルールを Azure Backup 向けに構成する必要がありますか。 <br/>
オンプレミスから Azure に転送されるデータとワークロードから Azure に転送されるデータをシームレスに保護できるように、次の URL との通信を許可するようにファイアウォールを設定することをお勧めします。

* www.msftncsi.com
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

## <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>VM 拡張機能を使用して Azure Backup サービスで既にバックアップされている Azure VM に Azure Backup エージェントをインストールすることはできますか。 <br/>
そして、 Azure Backup では、VM 拡張機能を使用している Azure VM には VM レベルのバックアップを提供します。 ゲスト Windows OS に Azure Backup エージェントをインストールすると、そのゲスト OS 上のファイルとフォルダーを保護できます。

## <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>Azure VM に Azure Backup エージェントをインストールし、Azure VM で提供される一時ストレージ上のファイルとフォルダーをバックアップすることはできますか。 <br/>
ゲスト Windows OS に Azure Backup エージェントをインストールして、ファイルやフォルダーを一時的なストレージにバックアップすることはできますが、 一時的なストレージのデータが消去されるとバックアップは失敗するので注意してください。 また、一時的なストレージのデータが削除された場合、復元先に指定できるのは非揮発性ストレージのみになります。

## <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-now-install-scdpm-to-work-with-azure-backup-agent-to-protect-onpremises-applicationvm-workloads-to-azure-br"></a>ファイルとフォルダーを保護するために Azure Backup エージェントをインストールしました。 Azure に転送するオンプレミスのアプリケーション/VM ワークロードを保護するために、SCDPM をインストールして Azure Backup エージェントと連携させることはできますか。 <br/>
Azure Backup を SCDPM と共に使用する場合は、先に SCDPM をインストールしてから、Azure Backup エージェントをインストールすることをお勧めします。 これにより、Azure Backup エージェントと SCDPM のシームレスな統合が可能になり、Azure に転送するファイルやフォルダー、アプリケーション ワークロード、VM を、SCDPM の管理コンソールから直接保護できるようになります。 上記の目的で Azure Backup エージェントの後に SCDPM をインストールする方法は推奨されておらず、サポートも適用されません。

## <a name="what-is-the-length-of-file-path-that-can-be-specified-as-part-of-azure-backup-policy-using-azure-backup-agent-br"></a>Azure Backup エージェントを使用する Azure Backup ポリシーの一部として指定できるファイル パスの長さはどれくらいですか。 <br/>
Azure Backup エージェントでは NTFS が使用されています。 [ファイル パスの長さの指定は、Windows API による制限を受けます](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths)。 バックアップするファイルのファイル パスの長が Windows API で指定された長さを超える場合は、バックアップ ファイルの親フォルダーまたはディスク ドライブをバックアップするよう選択できます。  

## <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>Azure Backup エージェントを使用する Azure Backup ポリシーのファイル パスには、どのような文字を使用できますか。 <br>
 Azure Backup エージェントでは NTFS が使用されています。 そのため、ファイルの指定では [NTFS でサポートされている文字](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) を使用できます。  

## <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Azure Backup Server を使用して、物理サーバーのベア メタル回復 (BMR) バックアップを作成できますか。 <br/>
はい。

## <a name="can-i-configure-the-backup-service-to-send-mail-if-a-backup-job-fails-br"></a>バックアップ ジョブが失敗した場合にメールを送信するように Backup サービスを構成できますか。 <br/>
はい、Backup サービスには、PowerShell スクリプトで使用できるイベント ベースのアラートがいくつかあります。 詳細については、[通知の構成](backup-azure-monitor-vms.md#configure-notifications)に関するページをご覧ください

## <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up-br"></a>バックアップされる各データ ソースのサイズに制限はありますか。 <br/>
コンテナー レベルでバックアップできるデータ量に制限はありませんが、Azure Backup ではデータ ソースの最大サイズに制限が課されます (これらの制限は、実際には非常に高く設定されています)。 2015 年 8 月時点における、サポートされているオペレーティング システム別のデータ ソースの最大サイズは次のとおりです。

| No. | オペレーティング システム | データ ソースの最大サイズ |
|:---:|:--- |:--- |
| 1 |Windows Server 2012 またはそれ以降 |54,400 GB |
| 2 |Windows 8 またはそれ以降 |54,400 GB |
| 3 |Windows Server 2008、Windows Server 2008 R2 |1,700 GB |
| 4 |Windows 7 |1,700 GB |

次の表では、各データ ソースのサイズが決定される方法について説明しています。

| データソース | 詳細 |
|:---:|:--- |
| ボリューム |サーバーまたはクライアント コンピューターの 1 つのボリュームからバックアップされるデータの量 |
| Hyper-V 仮想マシン |バックアップ対象の仮想マシンのすべての VHD のデータの合計 |
| Microsoft SQL Server データベース |バックアップ対象の 1 つの SQL データベースのサイズ |
| Microsoft SharePoint |バックアップ対象の SharePoint ファーム内のコンテンツと構成データベースの合計 |
| Microsoft Exchange |バックアップ対象の Exchange サーバー内のすべての Exchange データベースの合計 |
| BMR/システム状態 |バックアップ対象のコンピューターの BMR またはシステム状態の個々のコピー |

## <a name="are-there-limits-on-the-number-of-times-a-backup-job-can-be-scheduled-per-daybr"></a>1 日にバックアップ ジョブをスケジュール設定できる回数に制限はありますか。<br/>
はい。Windows Server または Windows クライアントでは、1 日に最大 3 回バックアップ ジョブを実行できます。 System Center DPM では、1 日に最大 2 回バックアップ ジョブを実行できます。 IaaS VM では、1 日に 1 回バックアップ ジョブを実行できます。

## <a name="is-there-a-difference-between-the-scheduling-policy-for-dpm-and-windows-server-ie-on-windows-server-without-dpm-br"></a>DPM と Windows Server (DPM がインストールされていない Windows Server) のスケジュール設定ポリシーには違いがありますか。 <br/>
はい。 DPM を使用した場合、スケジュールには毎日、毎週、毎月、毎年を指定できます。 (DPM のない) Windows Server では、スケジュールに毎日と毎週しか指定できません。

## <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-ie-on-windows-server-without-dpmbr"></a>DPM と Windows Server/クライアント (DPM がインストールされていない Windows Server) の保持ポリシーには違いがありますか。<br/>
いいえ。DPM と Windows Server/クライアントのどちらの場合でも、リテンション期間ポリシーに毎日、毎週、毎月、毎年を指定できます。

## <a name="can-i-configure-my-retention-policies-selectively-ie-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>週単位と日単位を指定し、年単位と月単位を指定しないなど、保持ポリシーを選択的に構成することはできますか。<br/>
はい。Azure Backup の保有構造では、要件に合わせて保有ポリシーを柔軟に定義できます。

## <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>午後 6 時に "バックアップのスケジュールを設定" し、別の時刻に "保持ポリシー" を指定することはできますか。<br/>
いいえ。 保有ポリシーは、バックアップ ポイントにのみ適用できます。 次の画像では、午前 0 時と午後 6 時にバックアップが行われるように保持ポリシーが指定されています。 <br/>

![バックアップのスケジュールと保有期間](./media/backup-azure-backup-faq/Schedule.png)
<br/>

## <a name="is-an-incremental-copy-transferred-for-the-retention-policies-scheduled-br"></a>スケジュールが設定されている保持ポリシーでは増分コピーは転送されますか。 <br/>
いいえ。増分コピーはバックアップ スケジュール ページに記載されている時刻に基づいて送信されます。 保持できるポイントは、保有ポリシーに基づいて決定されます。

## <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>バックアップを長期にわたって保持した場合、データ ポイントが古いほど復元に時間がかかるのでしょうか。 <br/>
 いいえ。最古のデータ ポイントも最新のデータ ポイントも復元に要する時間は同じです。 それぞれの回復ポイントは、完全なポイントと同じように動作します。

## <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>それぞれの回復ポイントが完全なポイントと同じように機能する場合、それは課金対象のバックアップ ストレージの合計に影響するのでしょうか。<br/>
一般的に、回復ポイントのリテンション期間が長い製品では、バックアップ データが完全なポイントとして格納されます。 完全なポイントは、ストレージ効率は *悪く* なりますが、簡単かつ迅速に復元できます。 一方で増分コピーは、ストレージ効率は *良く* なりますが、データのチェーンを復元する必要があり、復旧時間に影響します。 Azure Backup のストレージ アーキテクチャを使用すると、高速に復元するためにデータの格納を最適化し、ストレージ コストを低く抑えることで、両方の長所を生かすことができます。 このようなデータ ストレージ手法を通じて、送受信の帯域幅が効率的に使用されています。 データ ストレージの量とデータ回復にかかる時間がどちらも最小限に抑えられます。 増分バックアップによる効率的な保存のしくみの詳細については、 [こちら](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/) を参照してください。

## <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>作成できる回復ポイント数に制限はありますか。<br/>
いいえ。 回復ポイントでの制限はなくなりました。 必要な数だけ回復ポイントを作成することができます。

## <a name="why-is-the-amount-of-data-transferred-in-backup-not-equal-to-the-amount-of-data-i-backed-upbr"></a>バックアップで転送されたデータの量が、バックアップしたデータの量と一致しないのはなぜですか。<br/>
 Azure Backup エージェント、SCDPM、または Azure Backup Server からバックアップしたすべてのデータは、圧縮および暗号化されてから転送されます。 圧縮と暗号化が適用されると、バックアップ コンテナー内のデータは 30 ～ 40% 小さくなります。

## <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>Backup サービスによって使用される帯域幅の量を調整する方法はありますか。<br/>
 はい。帯域幅の調整には、Backup エージェントの **[プロパティの変更]** オプションを使用します。 帯域幅の量と、帯域幅を使用する時間を調整できます。 詳細については、「[Resource Manager デプロイ モデルで Windows Server または Windows クライアントを Azure にバックアップする](backup-configure-vault.md)」の「**ネットワーク調整の有効化 (オプション)**」を参照してください。

## <a name="my-internet-bandwidth-is-limited-for-the-amount-of-data-i-need-to-back-up-is-there-a-way-i-can-move-data-to-a-certain-location-with-a-large-network-pipe-and-push-that-data-into-azure-br"></a>バックアップする必要のあるデータ量に対してインターネット帯域幅が限られています。 大容量のネットワーク パイプで特定の場所にデータを移動し、そのデータを Azure にプッシュする方法はありますか。 <br/>
標準的なオンライン バックアップ プロセスを使用して Azure にデータをバックアップできます。または、Azure Import/Export サービスを使用して Azure の Blob Storage にデータを転送できます。 Azure Storage にバックアップ データを格納する方法は他にはありません。 Azure Backup で Azure Import/Export サービスを使用する方法については、[オフライン バックアップのワークフロー](backup-azure-backup-import-export.md)に関する記事をご覧ください。

## <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>Azure にバックアップされたデータで実行できる回復の回数は何回ですか。<br/>
Azure Backup からの回復の数に制限はありません。

## <a name="do-i-have-to-pay-for-the-egress-traffic-from-azure-data-center-during-recoveriesbr"></a>回復時の Azure データ センターからのエグレス トラフィックに対して課金されますか。<br/>
 いいえ。 回復は無料ですので、送信トラフィックに対しては課金されません。

## <a name="is-the-data-sent-to-azure-encrypted-br"></a>Azure に送信されるデータは暗号化されますか。 <br/>
はい。 データは、オンプレミスのサーバー / クライアント / SCDPM コンピューターで AES256 を使用して暗号化され、セキュリティで保護された HTTPS リンク経由で送信されます。

## <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>Azure 上のバックアップ データも暗号化されますか。<br/>
 はい。 Azure に送信されるデータは、暗号化された状態で保存されます。 マイクロソフトは、どの時点でもバックアップ データの暗号化を解除しません。 Azure VM バックアップの場合、Azure Backup の暗号化形式は仮想マシンに依存します。つまり、VM が Azure Disk Encryption やその他の暗号化テクノロジを使用して暗号化されている場合、Azure Backup ではその暗号化テクノロジを使用してデータが保護されます。

## <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>バックアップ データの暗号化に使用される暗号化キーの最小の長さはどれくらいですか。 <br/>
 暗号化キーは 16 文字以上にする必要があります。

## <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>暗号化キーを紛失した場合はどうなりますか? 自分でデータを回復できますか。または Microsoft でデータを回復してもらうことはできますか。 <br/>
バックアップ データの暗号化に使用されるキーは、お客様のオンプレミスにのみ存在します。 マイクロソフトは Azure にコピーを保持していませんし、キーにもアクセスできません。 お客様がキーを紛失した場合、マイクロソフトはバックアップ データを回復できません。

## <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>Azure Backup エージェント用に指定されたキャッシュの場所を変更する方法を教えてください。<br/>
 キャッシュの場所を変更するには、以下に箇条書きで示した作業を上から順に行ってください。

* 管理者特権でのコマンド プロンプトで次のコマンドを実行して Backup エンジンを停止します。

  ```PS C:\> Net stop obengine```
* ファイルを移動するのではなく、 空き容量に余裕のある別のドライブにキャッシュ領域フォルダーをコピーします。 元のキャッシュ領域は、バックアップが新しいキャッシュ領域で正常に動作していることを確認した後で削除できます。
* 次のレジストリ エントリを、新しいキャッシュ領域のフォルダーへのパスで更新します。<br/>

| レジストリ パス | レジストリ キー | 値 |
| --- | --- | --- |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*新しいキャッシュ フォルダーの場所* |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*新しいキャッシュ フォルダーの場所* |

* 管理者特権でのコマンド プロンプトで次のコマンドを実行して Backup エンジンを再起動します。

  ```PS C:\> Net start obengine```

  バックアップが新しいキャッシュ場所に正常に作成されたら、元のキャッシュ フォルダーを削除できます。

## <a name="where-can-i-put-the-cachefolder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>適切に動作させるためには、Azure Backup エージェントのキャッシュ フォルダーをどこに設定すればよいですか。<br/>
次の場所へのキャッシュ フォルダーの設定はお勧めしません。

* ネットワーク共有またはリムーバブル メディア: キャッシュ フォルダーは、オンライン バックアップを使用したバックアップが必要なサーバーに対してローカルにする必要があります。 ネットワーク上の場所や USB ドライブなどのリムーバブル メディアはサポートされていません。
* オフライン ボリューム: キャッシュ フォルダーは、Azure Backup エージェントを使用したバックアップのために、オンラインにしておく必要があります。

## <a name="are-there-any-attributes-of-the-cachefolder-that-are-not-supportedbr"></a>キャッシュ フォルダーの属性としてサポートされていないものはありますか。<br/>
 キャッシュ フォルダーでは、次の各属性またはそれらの組み合わせはサポートされていません。

* 暗号化
* 重複除去
* 圧縮
* スパース
* 再解析ポイント

Azure Backup エージェントを適切に動作させるために、キャッシュ フォルダーとメタデータ VHD の両方が上記の属性を持たないように設定することをお勧めします。



<!--HONumber=Nov16_HO2-->


