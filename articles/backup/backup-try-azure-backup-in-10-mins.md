<properties
   pageTitle="ファースト ルック: Azure に Windows Server またはクライアントをバックアップする方法 | Microsoft Azure"
   description="簡単な手順で Azure Backup を使用して Windows Server をバックアップする方法について説明します"
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
	 ms.date="02/21/2016"
	 ms.author="jimpark;"/>

# Windows Server またはクライアントを Azure にバックアップする

わずかな手順で、Windows コンピューター (Windows クライアントまたは Windows Server) を Azure にバックアップできます。以下の 4 つの手順を実行すると、次の操作が完了します。

- Azure サブスクリプションの設定 (必要な場合)。
- バックアップ コンテナーの作成と必要なコンポーネントのダウンロード。
- Windows Server またはクライアントのコンポーネントのインストールと登録による準備。
- データのバックアップ。

## 手順 1: Azure サブスクリプションを入手する

Azure サブスクリプションがない場合は、すべての Azure サービスにアクセスできる[無料アカウント](https://azure.microsoft.com/free/)を作成できます。

>[AZURE.NOTE] 既に Azure サブスクリプションがある場合は、この手順を省略できます。

## 手順 2: バックアップ コンテナーを作成する

Windows コンピューターのファイルとデータを Azure にバックアップするには、データを保存するリージョンにバックアップ コンテナーを作成する必要があります。

1. まだサインインしていない場合は、Azure サブスクリプションを使用して [Azure ポータル](https://portal.azure.com/)にサインインします。

2. **[新規]、[ハイブリッド統合]、[バックアップ]** の順にクリックします。

    ![コンテナーの作成](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

    a.表示された画面の **[名前]** ボックスに、バックアップ コンテナーを識別するための表示名を入力します。2 ～ 50 文字の名前を入力します。名前の先頭にはアルファベットを使用する必要があります。また、名前に使用できるのはアルファベット、数字、ハイフンのみです。

    b.**[リージョン]** ボックスで、バックアップ コンテナーのリージョンを選択します。自分の場所から近いリージョンを選択することによって、Azure にバックアップする際のネットワーク待機時間を短縮できます。

    c.**[資格情報コンテナーの作成]** をクリックします。

    ![コンテナーの作成](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    状態を確認するには、ポータルの下部にある通知を監視します。

    ![Creating Vault](./media/backup-try-azure-backup-in-10-mins/creatingvault1.png)

    バックアップ コンテナーが作成されると、Recovery Services のリソースの一覧に "**アクティブ**" と表示されます。

    ![Creating Vault status](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

3. **ストレージ冗長**オプションを選択します。

    ストレージ冗長オプションを選択する最適なタイミングは、コンテナーの作成直後で、コンピューターをコンテナーに登録する前です。項目が資格情報コンテナーに登録されたら、ストレージ冗長オプションはロックされ、変更できなくなります。

    プライマリ バックアップ ストレージ エンドポイントとして Azure を使用している場合 (たとえば、Windows Server から Azure にバックアップする場合)、[geo 冗長ストレージ](../storage/storage-redundancy.md#geo-redundant-storage) オプション (既定値) の選択を検討する必要があります。

    Azure を第 3 のバックアップ ストレージ エンドポイントとして使用している場合 (たとえば、オンプレミスのローカル バックアップ コピーには SCDPM を使用し、Azure は長期保存に使用する場合) は、[ローカル冗長ストレージ](../storage/storage-redundancy.md#locally-redundant-storage)の選択を検討する必要があります。これにより、Azure でデータを格納するためのコストは削減されますが、データの持続性レベルは低くなります。これは、第 3 のコピーとしてなら許容される可能性があります。

    a.作成したコンテナーをクリックします。

    b.クイック スタート ページで、**[構成]** をクリックします。

    ![Configure Vault status](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

    c.適切なストレージ冗長オプションを選択します。

    **[Geo 冗長]** が既定のオプションであるため、**[ローカル冗長]** を選択した場合は **[保存]** をクリックする必要があります。

    ![GRS](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

    d.左側のナビゲーション ウィンドウで **[Recovery Services]** をクリックし、**[Recovery Services]** のリソースの一覧に戻ります。

    ![Select backup vault](./media/backup-try-azure-backup-in-10-mins/rs-left-nav.png)

    これで、Windows コンピューターは、作成したバックアップ コンテナーでの認証が必要になりました。認証はコンテナーの資格情報を使用して実行されます。

4.  作成したコンテナーをクリックします。

    ![Creating Vault status](./media/backup-try-azure-backup-in-10-mins/demo-vault-active-full-screen.png)

5. **クイック スタート** ページで **[コンテナー資格情報のダウンロード]** をクリックします。

    ![ダウンロード](./media/backup-try-azure-backup-in-10-mins/downloadvc.png)

    ポータルは、資格情報コンテナーの名前と現在の日付の組み合わせを使用して、資格情報コンテナーの資格情報を生成します。

    >[AZURE.NOTE] コンテナーの資格情報ファイルは登録ワークフロー中しか使用されず、48 時間後に期限切れとなります。

6. **[保存]** をクリックして、コンテナー資格情報をローカル アカウントの**ダウンロード** フォルダーにダウンロードするか、**[保存]** メニューの **[名前を付けて保存]** を選択して、保存場所を指定します。

    ![Select backup vault](./media/backup-try-azure-backup-in-10-mins/save.png)

    コンテナーの資格情報は、ご自身のコンピューターからアクセスできる場所に保存してください。ファイルがファイル共有/SMB に格納されている場合は、アクセス許可を確認します。

    >[AZURE.NOTE] この時点でコンテナーの資格情報を開く必要はありません。

    次に、バックアップ エージェントをダウンロードする必要があります。

7. 左側のナビゲーション ウィンドウの **[Recovery Services]** をクリックして、サーバーに登録するバックアップ コンテナーをクリックします。

    ![Select backup vault](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

8. クイック スタート ページで **[Windows Server、System Center Data Protection Manager、Windows クライアント向けエージェント]、[保存]** の順にクリックするか、**[保存]** メニューの **[名前を付けて保存]** を選択してエージェントの場所を指定します。

    ![エージェントの保存](./media/backup-try-azure-backup-in-10-mins/agent.png)

これで、バックアップ コンテナーの作成と必要なコンポーネントのダウンロードが完了しました。次に、バックアップ エージェントをインストールします。

## 手順 3: Windows Server またはクライアントに Azure Backup エージェントをインストールする

1. ダウンロードの完了後、保存した場所で **MARSagentinstaller.exe** をダブルクリックします (または、前の手順でファイルを保存する代わりに **[実行]** をクリックすることもできます)。

2. エージェントに必要な**インストール フォルダー**と**キャッシュ フォルダー**を選択します。

    キャッシュ フォルダーとして指定する場所には、バックアップ データの 5% 以上の空き領域が必要です。

    **[次へ]** をクリックします。

    ![Scratch and Cache](./media/backup-try-azure-backup-in-10-mins/recovery-services-agent-setup-wizard-1.png)

3. 既定のプロキシ設定を使用して引き続きインターネットに接続することができます。また、インターネットへの接続にプロキシ サーバーを使用する場合は、**[プロキシの構成]** 画面で **[カスタムのプロキシ設定を使用する]** チェック ボックスをオンにし、プロキシ サーバーの詳細を入力します。

    認証済みのプロキシを使用する場合は、ユーザー名とパスワードの詳細を入力します。

    **[次へ]** をクリックします。

    ![Proxy configuration](./media/backup-try-azure-backup-in-10-mins/proxy-configuration.png)

4. **[インストール]** をクリックします。

    ![Proxy configuration](./media/backup-try-azure-backup-in-10-mins/agent-installation.png)

    Azure Backup エージェントは、インストールを完了するために、.NET Framework 4.5 と Windows PowerShell をインストールします (まだインストールされていない場合)。

5. エージェントがインストールされたら、**[登録処理を続行]** をクリックして、ワークフローを続行します。

    ![登録](./media/backup-try-azure-backup-in-10-mins/register.png)

6. ダウンロードしておいた**コンテナーの資格情報ファイル**を **[資格情報コンテナーの識別]** 画面で参照し、選択します。セットアップ アプリケーションがアクセスできる場所に、コンテナーの資格情報ファイルがあることを確認します。

    **[次へ]** をクリックします。

    ![コンテナー資格情報](./media/backup-try-azure-backup-in-10-mins/vault-identification-with-creds.png)

7. **[暗号化の設定]** 画面で、パスフレーズを生成するか、パスフレーズ (最小 16 文字) を指定することができます。必ず安全な場所にパスフレーズを保存してください。

    > [AZURE.WARNING] パスフレーズを紛失または忘れてしまった場合、Microsoft はバックアップ データの回復を支援することはできません。エンド ユーザーは暗号化パスフレーズを所有していますが、Microsoft はエンド ユーザーが使用するパスフレーズを確認できません。回復操作中に必要になる場合もあるため、ファイルは安全な場所に保存してください。

    **[完了]** をクリックします。

    ![暗号化](./media/backup-try-azure-backup-in-10-mins/encryption.png)

    **サーバーの登録ウィザード**では、サーバーを Microsoft Azure Backup に登録します。

    ![暗号化](./media/backup-try-azure-backup-in-10-mins/registering-server.png)

8. **暗号化キー**が設定されたら、**[Microsoft Azure Recovery Services Agent の起動]** チェック ボックスをオンのままにし、**[閉じる]** をクリックします。

    ![暗号化](./media/backup-try-azure-backup-in-10-mins/close-server-registration.png)

    これで、コンピューターがコンテナーに正しく登録され、バックアップ オプションの構成とスケジュールを行う準備ができました。

## 手順 4: ファイルとフォルダーをバックアップする

1. MMC スナップイン (**[Microsoft Azure Recovery Services Agent の起動]** チェック ボックスをオンのままにしておくと、自動的に開かれます) で **[バックアップのスケジュール]** をクリックします。

    ![Windows Server のバックアップ スケジュールを設定します](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. **[作業の開始]** 画面で、**[次へ]** をクリックします。

    ![Windows Server のバックアップ スケジュールを設定します](./media/backup-try-azure-backup-in-10-mins/getting-started.png)

3. **[バックアップする項目の選択]** 画面で、**[項目の追加]** をクリックします。

    ![Windows Server のバックアップ スケジュールを設定します](./media/backup-try-azure-backup-in-10-mins/add-items.png)

    バックアップする項目を選択し、**[OK]** をクリックします。Windows Server または Windows クライアントの Azure Backup では、ファイルとフォルダーを保護することができます。

    ![Windows Server のバックアップ項目](./media/backup-try-azure-backup-in-10-mins/added-items.png)

    **[次へ]** をクリックします。

    ![Windows Server のバックアップ項目](./media/backup-try-azure-backup-in-10-mins/selected-items.png)

4. **バックアップのスケジュール**を指定し、**[次へ]** をクリックします。

    毎日 (1 日に最大 3 回) または毎週のバックアップをスケジュールすることができます。

    ![Windows Server のバックアップ項目](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule.png)

5. バックアップ コピーのための**保持ポリシー**を選択します。必要に応じて、日、週、月、および年単位で保有ポリシーを変更できます。

    >[AZURE.NOTE] バックアップ スケジュールの詳細については、こちらの[記事](backup-azure-backup-cloud-as-tape.md)で説明されています。

     **[次へ]** をクリックします。

    ![Windows Server のバックアップ項目](./media/backup-try-azure-backup-in-10-mins/select-retention-policy.png)

6. 初回バックアップの種類を選択します。

    ネットワーク経由で自動的にバックアップことも、オフラインでバックアップすることもできます。この記事の残りの部分では、自動バックアップのプロセスについて説明します。オフライン バックアップを実行する場合は、「[Azure Backup でのオフライン バックアップのワークフロー](backup-azure-backup-import-export.md)」で追加情報を参照してください。

    **[次へ]** をクリックします。

    ![Windows Server の初回バックアップ](./media/backup-try-azure-backup-in-10-mins/choose-initial-backup-type.png)

7. **[確認]** 画面で情報を確認し、**[完了]** をクリックします。

    ![Windows Server の初回バックアップ](./media/backup-try-azure-backup-in-10-mins/confirmation.png)

8. ウィザードで**バックアップ スケジュール**の作成が完了したら、**[閉じる]** をクリックします。

    ![Windows Server の初回バックアップ](./media/backup-try-azure-backup-in-10-mins/backup-schedule-created.png)

9. MMC スナップインで **[今すぐバックアップ]** をクリックして、ネットワーク経由での最初のシード処理を完了します。

    ![Windows Server を今すぐバックアップする](./media/backup-try-azure-backup-in-10-mins/snap-in-backup-now.png)

10. **[確認]** 画面で、ウィザードでマシンのバックアップに使用される設定を確認し、**[バックアップ]** をクリックします。

    ![Windows Server を今すぐバックアップする](./media/backup-try-azure-backup-in-10-mins/backup-now-confirmation.png)

11. **[閉じる]** をクリックしてウィザードを閉じます。これは、**バックアップ プロセス**が完了する前に行うことができ、バックグラウンドで実行され続けます。

    ![Windows Server を今すぐバックアップする](./media/backup-try-azure-backup-in-10-mins/backup-progress.png)

12. 初回バックアップが完了すると、Azure Backup コンソールの **[ジョブ]** ビューに "ジョブは完了しました" という状態が表示されます。

    ![IR complete](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

これで、ファイルやフォルダーが Azure Backup に正常にバックアップされました。

## 次のステップ
- Azure Backup の詳細については、「[Azure Backup の概要](backup-introduction-to-azure-backup.md)」を参照してください。
- [Windows コンピューターをバックアップするための環境の準備](backup-configure-vault.md)の詳細を参照します。
- [Windows Server のバックアップ](backup-azure-backup-windows-server.md)の詳細を参照します。
- [Azure のバックアップ フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=290933)にアクセスします。

<!---HONumber=AcomDC_0302_2016-->