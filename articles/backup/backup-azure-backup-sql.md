<properties
	pageTitle="DPM を使用した SQL ワークロード用の Azure Backup | Microsoft Azure"
	description="Azure Backup サービスを使用した SQL Database のバックアップの概要"
	services="backup"
	documentationCenter=""
	authors="giridharreddy"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/09/2015" ms.author="giridham"; "jimpark"/>


# DPM を使用した SQL ワークロード用の Azure Backup

この記事では、Azure Backup を使用して SQL のバックアップを構成する手順について説明します。SQL を Azure にバックアップするには、Azure アカウントが必要です。アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。

SQL Database の Azure へのバックアップと回復の管理には、次の 3 つの手順が含まれます。

1. SQL Server Database を保護するためのバックアップ ポリシーを Azure に作成します。
2. オンデマンドでのバックアップ コピーを Azure に作成します。
3. Azure からデータベースを回復します。

## 開始する前に
開始する前に、Microsoft Azure Backup を使用したワークロードの保護に関するすべての[前提条件](../backup-azure-dpm-introduction/#prerequisites)が満たされていることを確認します。前提条件は、バックアップ コンテナーの作成、コンテナー資格情報のダウンロード、Azure Backup エージェントのインストール、コンテナーへのサーバーの登録などのタスクに関するものです。

## SQL Database を保護するためのバックアップ ポリシーを Azure に作成します。

1. DPM サーバーで、新しい**保護グループ**を作成することにより、SQL Database の新しいバックアップ ポリシーを構成します。**[保護]** ワークスペースをクリックします。

2. **[新規]** をクリックし、新しい保護グループを作成します。

    ![Create Protection Group](./media/backup-azure-backup-sql/protection-group.png)

3. DPM のスタート画面に**保護グループ**の作成に関するガイダンスが表示されます。**[次へ]** をクリックします。

4. **[サーバー]** を選択します。

    ![Select Protection Group Type - 'Servers'](./media/backup-azure-backup-sql/pg-servers.png)

5. バックアップ対象の SQL Database がインストールされている SQL Server を展開します。DPM に、そのサーバーからバックアップ可能なさまざまなデータ ソースが表示されます。**[すべての SQL 共有]** を展開し、バックアップ対象の SQL Database (ここでは ReportServer$MSDPM2012 と ReportServer$MSDPM2012TempDB) を選択します。**[次へ]** をクリックします。

    次のような画面が表示されます。

    ![Select SQL DB](./media/backup-azure-backup-sql/pg-databases.png)

6. 作成する保護グループの名前を指定します。**[オンライン保護を利用する]** オプションを選択していることを確認してください。

    ![Data Protection Method - short term disk & Online Azure](./media/backup-azure-backup-sql/pg-name.png)

7. **[短期的な目標値の指定]** 画面で、ディスクへのバックアップ ポイントを作成するために必要な入力を含めます。

    ここで、**[保有期間の範囲]** が *5 日間*、**[同期の頻度]** (バックアップが実行される頻度) が *15 分*ごとに設定されていることを確認します。**[高速完全バックアップ]** は*午後 8 時 00 分*に設定されています。

    ![Short term goals](./media/backup-azure-backup-sql/pg-shortterm.png)

    >[AZURE.NOTE]\(画面の入力に従って) 毎日午後 8 時 00 分に、前日午後 8 時 00 分のバックアップ ポイントから変更されているデータを転送することにより、バックアップ ポイントが作成されます。このプロセスは、**高速完全バックアップ**と呼ばれます。SQL トランザクション ログは 15 分ごとに同期されますが、午後 9 時 00 分のデータベースを回復する必要がある場合は、最新の高速完全バックアップ ポイント (この場合は午後 8 時) からのログを再生することにより、ポイントが作成されます。

8. **[次へ]** をクリックします。

    DPM は、使用可能なストレージ領域全体と考えられるディスク領域使用率を表示します。

    ![Disk allocation](./media/backup-azure-backup-sql/pg-storage.png)

    >[AZURE.NOTE]DPM は、初期バックアップ コピーの作成用に、データ ソース (SQL Database) ごとに 1 つのボリュームを作成します。この方法では、論理ディスク マネージャー (LDM) は DPM が最大 300 のデータ ソース (SQL Database) のみを保護できるように制限します。これを避けるために、DPM は複数のデータ ソースに単一のボリュームを使用する別の方法を実装しています。これは、**DPM 記憶域プールへのデータの併置**を使用することで有効になります。この方法を使用すると、DPM は最大 2000 の SQL Database を保護できます。

    **[ボリュームを自動的に拡大する]** がオンになっている場合、運用データの増大に伴って DPM がバックアップ ボリュームを増加することができます。**[ボリュームを自動的に拡大する]** がオフになっている場合は、保護グループ内のデータ ソースのバックアップに使用するバックアップ ストレージが制限されます。

9. 管理者は、帯域幅の輻輳を避けるために手動 (オフ ネット) でこの初期バックアップを転送するか、ネットワーク経由で転送するかを選択できます。最初の転送が行われるときに構成することもできます。**[次へ]** をクリックします。

    ![Initial replication method](./media/backup-azure-backup-sql/pg-manual.png)

    >[AZURE.NOTE]初期バックアップのコピーでは、運用サーバー (SQL Server) から DPM サーバーへの、データ ソース (SQL Database) 全体の転送が必要です。このデータは場合によっては非常に大きくなり、ネットワーク経由で転送すると帯域幅を超える可能性があります。したがって、この初期バックアップを転送する際に、帯域幅の輻輳を避けるために**手動**で転送するか、ネットワーク経由で**自動的**に転送するかを管理者が選択できます。また、管理者が**ネットワーク**を選択した場合は、初期バックアップのコピーを**今**作成するか、**後で**指定した時刻に作成するかを選択できます。

    初期バックアップが完了した後は、残りのバックアップは初期バックアップのコピーに対する増分バックアップになります。これは一般に非常に小さく、ネットワーク経由で転送できます。

10. 整合性チェックをいつ実行するかを選択し、**[次へ]** をクリックします。

    ![Consistency check](./media/backup-azure-backup-sql/pg-consistent.png)

    >[AZURE.NOTE]DPM は整合性チェックを実行して、バックアップ ポイントの整合性を確認します。このチェックは、運用サーバー (このシナリオでは SQL Server) のバックアップ ファイルと DPM にバックアップされたこのファイルのデータのチェックサムを計算します。競合がある場合は、DPM にバックアップされたファイルが破損していると見なされます。DPM は、チェックサムの不一致に対応するブロックを送信することにより、バックアップされたデータを修正します。整合性チェックは負荷の高い処理であるため、管理者はこのチェックをスケジュール設定するか、自動的に実行するかを選択できます。

11. データ ソースのオンライン保護を指定するには、Azure で保護されるデータベースを選択し、**[次へ]** をクリックします。

    ![Select datasources](./media/backup-azure-backup-sql/pg-sqldatabases.png)

12. 管理者は、組織のポリシーに合わせてバックアップ スケジュールや保有ポリシーを選択できます。

    ![Schedule and Retention](./media/backup-azure-backup-sql/pg-schedule.png)

    この例では、バックアップは 1 日に 1 回、午後 12 時 00 分と午後 8 時 (画面の下部) に作成されます。

    >[AZURE.NOTE]すばやく回復するために、ディスク上にいくつか短期的な回復ポイントを作っておくことをお勧めします。これは、"オペレーショナル リカバリ" と呼ばれます。Azure は、より高い SLA を提供し、可用性を確保するのに適したオフサイトの場所として機能します。2015 年 H1 までに、DPM と Azure Backup に、バックアップの複数の "世代" を指定する機能 (GFS スキームとも呼ばれます) が追加される予定です。

    **ベスト プラクティス**: DPM を使用したローカル ディスク バックアップの完了より後に Azure Backup がスケジュールされていることを確認します。これにより、最新のディスク バックアップを Azure にコピーできるようになります。

13. 保有ポリシーのスケジュールを選択します。保有ポリシーのしくみの詳細については、「[Azure Backup を使用してテープのインフラストラクチャを置換する](backup-azure-backup-cloud-as-tape.md)」を参照してください。

    ![Retention Policy](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    次の点に注意してください。

    - バックアップは 1 日に 1 回、午後 12 時 00 分と午後 8 時 (画面の下部) に作成され、180 日間保有されます。
    - 土曜日の午後 12 時 00 分のバックアップは、104 週間保有されます。
    - 先週土曜日の午後 12 時 00 分のバックアップは、60 か月保有されます。
    - 3 月の最終土曜日の午後 12 時 00 分のバックアップは、10 年間保有されます。

14. **[次へ]** をクリックし、初期バックアップのコピーを Azure に転送するための適切なオプションを選択します。**[自動 (ネットワーク経由)]** または **[オフライン バックアップ]** を選択できます。

    - **[自動 (ネットワーク経由)]** の場合は、バックアップ用に選択されたスケジュールに従って、バックアップ データが Azure に転送されます。
    - **[オフライン バックアップ]** のしくみについては、「[Azure Backup でのオフライン バックアップのワークフロー](backup-azure-backup-import-export.md)」を参照してください。

    初期バックアップのコピーを Azure に転送するための適切な転送メカニズムを選択し、**[次へ]** をクリックします。

15. **[概要]** 画面でポリシーの詳細を確認したら、**[グループの作成]** をクリックしてワークフローを完了します。**[閉じる]** をクリックして、[監視] ワークスペースでジョブの進行状況を監視できます。

    ![Creation of Protection Group In-Progress](./media/backup-azure-backup-sql/pg-summary.png)

## SQL Database のオンデマンド バックアップ
前の手順でバックアップ ポリシーを作成しましたが、"回復ポイント" は最初のバックアップ実行時にのみ作成されます。次の手順は、スケジューラが開始するのを待つ代わりに、回復ポイントの作成を手動でトリガーします。

1. データベースの保護グループの状態に "**OK**" と表示されるのを待ってから、回復ポイントを作成します。

    ![Protection Group Members](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)

2. データベースを右クリックし、**[回復ポイントの作成]** を選択します。

    ![Create Online Recovery Point](./media/backup-azure-backup-sql/sqlbackup-createrp.png)

3. ボックスの一覧から **[オンライン保護]** を選択し、**[OK]** をクリックします。これにより、Azure での回復ポイントの作成が開始されます。

    ![Create recovery point](./media/backup-azure-backup-sql/sqlbackup-azure.png)

4. 次の図に示すように、進行中のジョブが表示される **[監視]** ワークスペースで、ジョブの進行状況を確認できます。

    ![Monitoring console](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## Azure から SQL Database を回復します。
次の手順は、保護されているエンティティ (SQL Database) を Azure から回復するために必要です。

1. DPM サーバーの管理コンソールを開きます。DPM によってバックアップされたサーバーを確認できる **[回復]** ワークスペースに移動します。目的のデータベース (この場合は ReportServer$MSDPM2012) を参照します。"**オンライン**" で終わる **[回復元]** の時間を選択します。

    ![Select Recovery point](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)

2. データベース名を右クリックし、**[回復]** をクリックします。

    ![Recover from Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)

3. DPM に回復ポイントの詳細が表示されます。**[次へ]** をクリックします。回復のタイプとして **[元の SQL Server のインスタンスに回復する]** を選択します。これは、データベースを上書きします。**[次へ]** をクリックします。

    ![Recover to Original Location](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    この例では、DPM はデータベースを別の SQL Server インスタンスまたはスタンドアロンのネットワーク フォルダーに回復できます。

4. **[回復オプションの指定]** 画面で、[ネットワークの使用帯域幅の調整] を選択して回復で使用される帯域幅を調整するなど、回復のオプションを選択できます。**[次へ]** をクリックします。

5. **[概要]** 画面に、これまでに指定した回復の構成が表示されます。**[回復]** をクリックします。

    回復の状態に、データベースが回復されていることが表示されます。**[閉じる]** をクリックしてウィザードを閉じ、**[監視]** ワークスペースで進行状況を確認できます。

    ![Initiate recovery process](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    回復が完了すると、復元されたデータベースのコピーはアプリケーション コンシステントになります。

### 次のステップ:

• [Azure Backup FAQ](backup-azure-backup-faq.md)

<!---HONumber=Oct15_HO3-->