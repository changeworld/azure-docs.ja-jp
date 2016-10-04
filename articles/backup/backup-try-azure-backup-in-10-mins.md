<properties
   pageTitle="Azure Backup と Resource Manager デプロイメント モデルを使用して、Windows のファイルとフォルダーを Azure にバックアップする方法 | Microsoft Azure"
   description="コンテナーを作成し、Recovery Services エージェントをインストールして、ファイルとフォルダーを Azure にバックアップすることで、Windows Server のデータをバックアップする方法について説明します。"
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="cfreeman"
   editor=""
   keywords="バックアップする方法, バックアップ方法"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.date="09/27/2016"
   ms.author="markgal;"/>

# 最初に: Resource Manager デプロイメント モデルを使用して Azure Backup でファイルやフォルダーをバックアップする

この記事では、Resource Manager を使用して、Azure Backup で Windows Server または Windows クライアントのファイルやフォルダーを Azure にバックアップする方法について説明します。基本事項に関するチュートリアルです。この記事は、Azure Backup を始めて使用する場合に適しています。

Azure Backup の詳細については、こちらの[概要記事](backup-introduction-to-azure-backup.md)を参照してください。

ファイルとフォルダーを Azure にバックアップするには、以下のアクティビティが必要です。

![手順 1](./media/backup-try-azure-backup-in-10-mins/step-1.png) Azure サブスクリプションを入手する (まだない場合)。<br> ![手順 2.](./media/backup-try-azure-backup-in-10-mins/step-2.png) Recovery Services コンテナーを作成する。<br> ![手順 3.](./media/backup-try-azure-backup-in-10-mins/step-3.png) 必要なファイルをダウンロードする。<br> ![手順 4.](./media/backup-try-azure-backup-in-10-mins/step-4.png) Recovery Services エージェントをインストールして登録する。<br> ![手順 5.](./media/backup-try-azure-backup-in-10-mins/step-5.png) ファイルとフォルダーをバックアップする。

![How to back up your Windows machine with Azure Backup](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

## 手順 1: Azure サブスクリプションを入手する

Azure サブスクリプションがない場合は、すべての Azure サービスにアクセスできる[無料アカウント](https://azure.microsoft.com/free/)を作成します。

## ステップ 2: Recovery Services コンテナーを作成する

ファイルとフォルダーをバックアップするには、データを保存するリージョンに Recovery Services コンテナーを作成する必要があります。また、ストレージのレプリケート方法を決定する必要もあります。

### Recovery Services コンテナーを作成するには

1. まだサインインしていない場合は、Azure サブスクリプションを使用して [Azure ポータル](https://portal.azure.com/)にサインインします。

2. ハブ メニューの **[参照]** をクリックし、リソースの一覧で「**Recovery Services**」と入力して、**[Recovery Services コンテナー]** をクリックします。

    ![Create Recovery Services Vault step 1](./media/backup-try-azure-backup-in-10-mins/browse-to-rs-vaults.png) <br/>

3. **[Recovery Services コンテナー]** メニューの **[追加]** をクリックします。

    ![Create Recovery Services Vault step 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Recovery Services コンテナー ブレードが開き、**[名前]**、**[サブスクリプション]**、**[リソース グループ]**、および **[場所]** を指定するように求められます。

    ![Create Recovery Services vault step 5](./media/backup-try-azure-backup-in-10-mins/rs-vault-attributes.png)

4. **[名前]** ボックスに、コンテナーを識別する表示名を入力します。

5. **[サブスクリプション]** をクリックして、使用可能なサブスクリプションの一覧を表示します。

6. **[リソース グループ]** をクリックして、使用可能なリソース グループを表示するか、**[新規]** をクリックして、新しいリソース グループを作成します。

7. **[場所]** をクリックして、コンテナーの地理的リージョンを選択します。この選択により、バックアップ データの送信先となるリージョンが決まります。

8. **[作成]** をクリックします。

    完了した後に、コンテナーが一覧に表示されない場合は、**[最新の情報に更新]** をクリックします。一覧が更新されたら、コンテナーの名前をクリックします。

### ストレージの冗長性を確認するには
初めて Recovery Services コンテナーを作成するときに、ストレージのレプリケーション方法を決定します。

1. 新しいコンテナーをクリックして、ダッシュボードを開きます。

2. コンテナーのダッシュボードと一緒に自動的に開く **[設定]** ブレードで、**[Backup Infrastructure]** (バックアップ インフラストラクチャ) をクリックします。

3. [Backup Infrastructure] ブレードで **[Backup Configuration]** をクリックして、**[Storage replication type]** を表示します。

    ![Create Recovery Services vault step 5](./media/backup-try-azure-backup-in-10-mins/backup-infrastructure.png)

4. コンテナーの適切なストレージ レプリケーション オプションを選択します。

    ![List of recovery services vaults](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    既定では、コンテナーには geo 冗長ストレージがあります。プライマリ バックアップ ストレージ エンドポイントとして Azure を使用している場合は、引き続き geo 冗長ストレージを使用します。プライマリ以外のバックアップ ストレージ エンドポイントとして Azure を使用している場合は、ローカル冗長ストレージを選択します。ローカル冗長ストレージを選択すると、Azure にデータを格納するコストが削減されます。[geo 冗長](../storage/storage-redundancy.md#geo-redundant-storage)ストレージ オプションと[ローカル冗長](../storage/storage-redundancy.md#locally-redundant-storage)ストレージ オプションの詳細については、こちらの[概要](../storage/storage-redundancy.md)を参照してください。

これで、コンテナーが作成されました。次は、Microsoft Azure Recovery Services エージェントとコンテナーの資格情報をダウンロードして、ファイルとフォルダーをバックアップするインフラストラクチャを準備します。

## 手順 3: ファイルをダウンロードする

1. Recovery Services コンテナーのダッシュボードで、**[設定]** をクリックします。

    ![Open backup goal blade](./media/backup-try-azure-backup-in-10-mins/settings-button.png)

2. [設定] ブレードで、**[作業の開始]、[Backup]** の順にクリックします。

    ![Open backup goal blade](./media/backup-try-azure-backup-in-10-mins/getting-started-backup.png)

3. [Backup] ブレードで、**[Backup goal]** をクリックします。

    ![Open backup goal blade](./media/backup-try-azure-backup-in-10-mins/backup-goal.png)

4. [Where is your workload running] メニューの **[オンプレミス]** を選択します。

5. [What do you want to backup] メニューの **[Files and folders]** を選択し、**[OK]** をクリックします。

### Recovery Services エージェントのダウンロード

1. **[Prepare infrastructure]** ブレードで、**[Download Agent for Windows Server or Windows Client]** をクリックします。

    ![prepare infrastructure](./media/backup-try-azure-backup-in-10-mins/prepare-infrastructure-short.png)

2. ダウンロードのポップアップ ウィンドウで、**[保存]** をクリックします。既定では、**MARSagentinstaller.exe** ファイルがダウンロード フォルダーに保存されます。

### コンテナー資格情報のダウンロード

1. [Prepare infrastructure] ブレードで、**[ダウンロード]、[保存]** の順にクリックします。

    ![prepare infrastructure](./media/backup-try-azure-backup-in-10-mins/prepare-infrastructure-download.png)

## 手順 4: エージェントをインストールして登録する

>[AZURE.NOTE] 間もなく、Azure ポータルからバックアップを有効にできるようになります。現時点では、オンプレミスの Microsoft Azure Recovery Services エージェントを使用して、ファイルやフォルダーをバックアップします。

1. ダウンロード フォルダー (または他の保存場所) で **MARSagentinstaller.exe** を見つけて、ダブルクリックします。

2. Microsoft Azure Recovery Services エージェント セットアップ ウィザードを実行します。ウィザードでは以下のことを行う必要があります。

    - インストールとキャッシュ フォルダーの場所を選択します。
    - プロキシ サーバーを使用してインターネットに接続する場合は、プロキシ サーバーの情報を指定します。
    - 認証済みのプロキシを使用する場合は、ユーザー名とパスワードの詳細を入力します。
    - ダウンロードしたコンテナーの資格情報を指定します。
    - 暗号化パスフレーズを安全な場所に保存します。

    >[AZURE.NOTE] パスフレーズを紛失または忘れた場合、Microsoft はバックアップ データの回復を支援することはできません。安全な場所にファイルを保存してください。バックアップを復元するために必要です。

エージェントがインストールされ、コンピューターがコンテナーに登録されました。バックアップを構成してスケジュールする準備ができました。

## 手順 5: ファイルとフォルダーをバックアップする

初回バックアップには、次の 2 つの主要なタスクが含まれています。

- バックアップのスケジュール
- 初回のファイルとフォルダーのバックアップ

初回バックアップを実行するには、Microsoft Azure Recovery Services エージェントを使用します。

### バックアップのスケジュールを設定するには

1. Microsoft Azure Recovery Services エージェントを開きます。エージェントは、コンピューターで **Microsoft Azure Backup** を検索すると見つかります。

    ![Launch the Azure Recovery Services agent](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. Recovery Services エージェントで、**[バックアップのスケジュール]** をクリックします。

    ![Schedule a Windows Server backup](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. バックアップのスケジュール ウィザードの [作業の開始] ページで、**[次へ]** をクリックします。

4. [バックアップする項目の選択] 画面で、**[項目の追加]** をクリックします。

5. バックアップするファイルとフォルダーを選択し、**[OK]** をクリックします。

6. **[次へ]** をクリックします。

7. **[バックアップ スケジュールの選択]** ページで**バックアップ スケジュール**を指定し、**[次へ]** をクリックします。

    毎日 (1 日に最大 3 回) または毎週のバックアップをスケジュールすることができます。

    ![Windows Server のバックアップ項目](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule-close.png)

    >[AZURE.NOTE] バックアップ スケジュールを指定する方法の詳細については、「[Azure Backup を使用してテープのインフラストラクチャを置換する](backup-azure-backup-cloud-as-tape.md)」を参照してください。

8. **[保持ポリシーの選択]** ページで、バックアップ コピーの**保持ポリシー**を選択します。

    保有ポリシーは、バックアップを格納する必要がある期間を指定します。すべてのバックアップ ポイントに "同じポリシー" を指定するのでなく、バックアップが実行されるタイミングに基づいて異なる保持ポリシーを指定できます。必要に応じて、日、週、月、および年単位で保有ポリシーを変更できます。

9. [初期バックアップの種類の選択] ページで、初期バックアップの種類を選択します。**[自動でネットワーク経由]** オプションが選択された状態のままにし、**[次へ]** をクリックします。

    ネットワーク経由で自動的にバックアップことも、オフラインでバックアップすることもできます。この記事の残りの部分では、自動バックアップのプロセスについて説明します。オフライン バックアップを実行する場合は、「[Azure Backup でのオフライン バックアップのワークフロー](backup-azure-backup-import-export.md)」で詳細を確認してください。

10. [確認] ページで情報を確認し、**[完了]** をクリックします。

11. ウィザードでバックアップ スケジュールの作成が完了したら、**[閉じる]** をクリックします。

### 初回のファイルとフォルダーをバックアップするには

1. Recovery Services エージェントで **[今すぐバックアップ]** をクリックして、ネットワーク経由での最初のシード処理を完了します。

    ![Windows Server を今すぐバックアップする](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

2. [確認] ページで、今すぐバックアップ ウィザードによってコンピューターのバックアップに使用される設定を確認します。次に、**[バックアップ]** をクリックします。

3. **[閉じる]** をクリックしてウィザードを閉じます。バックアップ プロセスが完了する前にウィザードを閉じても、ウィザードはバックグラウンドで引き続き実行されます。

初回バックアップが完了すると、**[ジョブは完了しました]** 状態が Backup コンソールに表示されます。

![IR complete](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## 疑問がある場合
ご不明な点がある場合や今後搭載を希望する機能がある場合は、[フィードバックをお送りください](http://aka.ms/azurebackup_feedback)。

## 次のステップ
- [Windows コンピューターのバックアップ](backup-configure-vault.md)の詳細を参照してください。
- ファイルとフォルダーをバックアップしたので、[コンテナーとサーバーを管理](backup-azure-manage-windows-server.md)できます。
- バックアップを復元する必要がある場合は、[Windows コンピューターへのファイルの復元](backup-azure-restore-windows-server.md)に関する記事を参照してください。

<!---HONumber=AcomDC_0928_2016-->