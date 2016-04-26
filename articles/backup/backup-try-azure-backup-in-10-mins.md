<properties
   pageTitle="Windows から Azure にファイルとフォルダーをバックアップする | Microsoft Azure"
   description="コンテナーを作成し、バックアップ エージェントをインストールして、ファイルとフォルダーを Azure にバックアップすることで、Windows Server のデータをバックアップする方法について説明します。"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="バックアップする方法, バックアップ方法"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.date="04/14/2016"
   ms.author="jimpark;"/>

# 最初に: Windows Server または Windows クライアントから Azure にファイルとフォルダーをバックアップする

この記事では、Azure Backup を使用して、Windows Server または Windows クライアントのファイルおよびフォルダーを Azure にバックアップする方法について説明します。基本事項に関するチュートリアルです。この記事は、Azure Backup を始めて使用する場合に適しています。

Azure Backup の詳細については、こちらの[概要記事](backup-introduction-to-azure-backup.md)を参照してください。

ファイルとフォルダーを Azure にバックアップするには、以下のアクティビティが必要です。

![手順 1.](./media/backup-try-azure-backup-in-10-mins/step-1.png) Azure サブスクリプションを入手する (まだない場合)。<br> ![手順 2.](./media/backup-try-azure-backup-in-10-mins/step-2.png) バックアップ コンテナーを作成し、必要なアイテムをダウンロードする。<br> ![手順 3.](./media/backup-try-azure-backup-in-10-mins/step-3.png) Backup エージェントをインストールして登録する。<br> ![手順 4.](./media/backup-try-azure-backup-in-10-mins/step-4.png) ファイルとフォルダーをバックアップする。


![How to back up your Windows machine with Azure Backup](./media/backup-try-azure-backup-in-10-mins/windows-machine-backup-process.png)

## 手順 1: Azure サブスクリプションを入手する

Azure サブスクリプションがない場合は、すべての Azure サービスにアクセスできる[無料アカウント](https://azure.microsoft.com/free/)を作成します。

## 手順 2: バックアップ コンテナーを作成し、必要なアイテムをダウンロードする

ファイルとフォルダーをバックアップするには、データを保存するリージョンにバックアップ コンテナーを作成する必要があります。また、ストレージのレプリケート方法を決定し、資格情報と Backup エージェントをダウンロードします。

### バックアップ資格情報コンテナーを作成するには

1. まだサインインしていない場合は、Azure サブスクリプションを使用して [Azure ポータル](https://portal.azure.com/)にサインインします。

2. **[新規]、[ハイブリッド統合]、[Backup]** の順にクリックします。

    ![Begin preparing to back up your files and folders](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

3. **[名前]** ボックスに、バックアップ資格情報コンテナーを識別する表示名を入力します。

4. **[リージョン]** で、ファイル転送の時間が最短になるように最も近いリージョンを選択します。

5. **[コンテナーの作成]** をクリックします。

    ![コンテナーの作成](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    バックアップ コンテナーの準備ができると、Recovery Services のリソースの一覧に "**アクティブ**" と表示されます。

    ![Vault status is active](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

コンテナーを作成した後は、ストレージのレプリケート方法を選択します。

>[AZURE.NOTE] コンテナーを作成した直後、コンピューターをコンテナーに登録する前に、ストレージのレプリケート方法を選択する必要があります。アイテムをコンテナーに登録すると、ストレージ レプリケーションはロックされて、変更できなくなります。

### ストレージのレプリケート方法を選択するには

1. 作成したコンテナーをクリックします。
2. [クイック スタート] ページで、**[構成]** をクリックします。

    ![Configure vault](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

3. 適切なストレージ オプションを選択します。

    プライマリ バックアップとして Azure を使用する場合は、[geo 冗長ストレージ](../storage/storage-redundancy.md#geo-redundant-storage)を選択します。第 3 のバックアップとして Azure を使用する場合は、[ローカル冗長ストレージ](../storage/storage-redundancy.md#locally-redundant-storage)を選択します。

    ![ストレージ レプリケーション オプションの選択](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

4. **[Geo 冗長]** が既定であるため、**[ローカル冗長]** を選択した場合は **[保存]** をクリックしてください。

バックアップ コンテナーでコンピューターを認証するには、コンテナー資格情報を使用します。資格情報をダウンロードする方法を次に示します。

### コンテナー資格情報をダウンロードするには
コンテナーの資格情報ファイルは登録プロセス中しか使用されず、48 時間後に期限切れとなります。

1. コンテナーの **[クイック スタート]** ページに戻るには、![Select your new vault](./media/backup-try-azure-backup-in-10-mins/quick-start-icon.png) をクリックします。

2. **[コンテナー資格情報のダウンロード]、[保存]** の順にクリックします。

次に、Backup エージェントをダウンロードします。

### Backup エージェントをダウンロードするには

**[Windows Server、System Center Data Protection Manager、Windows クライアント向けエージェント]、[保存]** の順にクリックします。

![Save the backup agent](./media/backup-try-azure-backup-in-10-mins/agent.png)

コンテナーを作成し、必要なものをすべてダウンロードしたので、Backup エージェントをインストールして登録します。

## 手順 3: Backup エージェントをインストールして登録する

1. 保存場所の **MARSagentinstaller.exe** をダブルクリックします。
2. Microsoft Azure Recovery Services エージェント セットアップ ウィザードを実行します。ウィザードでは以下のことを行う必要があります。
    - インストールとキャッシュ フォルダーの場所を選択します。
    - プロキシ サーバーを使用してインターネットに接続する場合は、プロキシ サーバーの情報を指定します。
    - 認証済みのプロキシを使用する場合は、ユーザー名とパスワードの詳細を入力します。
    - 暗号化パスフレーズを安全な場所に保存します。

    >[AZURE.NOTE] パスフレーズを紛失または忘れた場合、Microsoft はバックアップ データの回復を支援することはできません。安全な場所にファイルを保存してください。バックアップを復元するために必要です。

エージェントがインストールされ、コンピューターがコンテナーに登録されました。バックアップを構成してスケジュールする準備ができました。

## 手順 4: ファイルとフォルダーをバックアップする
Backup エージェントがまだ開いていない場合は、コンピューターで Microsoft Azure Backup を検索すると見つかります。

1. **Backup エージェント**で、**[バックアップのスケジュール]** をクリックします。

    ![Windows Server のバックアップ スケジュールを設定します](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. Backup のスケジュール ウィザードを実行します。ウィザードでは以下のことを行います。

    - バックアップするファイルとフォルダーを選択します。
    - バックアップ スケジュールを指定します (毎日または毎週)。
    - 保持ポリシーを決定します。
    - 最初のバックアップの実行方法を選択します (ネットワーク経由またはオフライン)。

    詳細については、「[Azure Backup でのオフライン バックアップのワークフロー](backup-azure-backup-import-export.md)」を参照してください。 <br><br>

3. ウィザードが完了したら、**Backup エージェント**に戻り、**[今すぐバックアップ]** をクリックして、ネットワーク経由で最初のバックアップを実行します。

    ![Windows Server を今すぐバックアップする](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

4. **[確認]** 画面で、**[バックアップ]** をクリックします。バックアップ プロセスが完了する前にウィザードを閉じると、プロセスはバックグラウンドで引き続き実行されます。

    最初のバックアップが完了すると、コンソールの **[ジョブ]** ビューにジョブの完了が示されます。

    ![Initial backup complete](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

これで、ファイルやフォルダーが Azure Backup に正常にバックアップされました。

## 次のステップ
- [Windows コンピューターのバックアップ](backup-configure-vault.md)の詳細を参照してください。
- ファイルとフォルダーをバックアップしたので、[コンテナーとサーバーを管理](backup-azure-manage-windows-server.md)できます。
- バックアップを復元する必要がある場合は、「[Windows Server または Windows クライアント コンピューターへのファイルの復元](backup-azure-restore-windows-server.md)」を参照してください。

<!---HONumber=AcomDC_0420_2016-->