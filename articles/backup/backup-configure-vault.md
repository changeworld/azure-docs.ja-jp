<properties
	pageTitle="Windows Server または Windows クライアント コンピューターをバックアップするための環境の準備 | Microsoft Azure"
	description="バックアップ資格情報コンテナーを作成し、資格情報をダウンロードして、バックアップ エージェントをインストールすることにより、Windows をバックアップするための環境を準備します。"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="バックアップ資格情報コンテナー; バックアップ エージェント; Windows のバックアップ;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="trinadhk; jimpark; markgal"/>


# Windows コンピューターを Azure にバックアップするための環境の準備
この記事では、Windows コンピューターを Azure にバックアップするための環境を準備するうえで必要な作業を示します。

| 手順 | 名前 | 詳細 |
| :------: | ---- | ------- |
| 1 | [コンテナーの作成](#create-a-backup-vault) | [Azure Backup 管理ポータル](http://manage.windowsazure.com)でコンテナーを作成します。 |
| 2 | [コンテナーの資格情報のダウンロード](#download-the-vault-credential-file) | コンテナーの資格情報をダウンロードします。Windows コンピューターをバックアップ コンテナーに登録する際に使用します。 |
| 3 | [Azure Backup エージェントのインストール](#download-install-and-register-the-azure-backup-agent) | エージェントをインストールし、コンテナーの資格情報を使用してサーバーをバックアップ コンテナーに登録します。 |

大まかに示した上記の手順が既に完了している場合は、[Windows コンピューターのバックアップ](backup-azure-backup-windows-server.md)を開始できます。まだ完了していない場合は、以下の詳しい手順に従って環境を構築してください。

## 開始する前に
Windows コンピューターをバックアップするための環境を準備するには、Azure アカウントが必要です。アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を数分で作成できます。

## バックアップ資格情報コンテナーの作成
ファイルとデータを Windows コンピューターまたは Data Protection Manager (DPM) から Azure にバックアップしたり、IaaS VM を Azure にバックアップしたりする場合には、データを保存する地理的リージョンにバックアップ コンテナーを作成する必要があります。　

**バックアップ コンテナーを作成するには:**

1. [管理ポータル](https://manage.windowsazure.com/)にサインインします。

2. **[新規]**、**[Data Services]**、**[Recovery Services]**、**[バックアップ資格情報コンテナー]** の順にクリックして、**[簡易作成]** を選択します。

    ![コンテナーの作成](./media/backup-configure-vault/createvault1.png)

3. **[名前]** パラメーターについては、バックアップ資格情報コンテナーを識別する表示名を入力します。これは、サブスクリプションごとに一意である必要があります。

    **[リージョン]** パラメーターについては、バックアップ資格情報コンテナーの地理的リージョンを選択します。この選択により、バックアップ データの送信先となる地理的リージョンが決まります。自分の場所から近い地理的リージョンを選択することによって、Azure にバックアップする際のネットワーク待機時間を削減できます。

    **[コンテナーの作成]** をクリックして、ワークフローを完了します。

    バックアップ資格情報コンテナーが作成されるまで時間がかかることがあります。状態を確認するには、ポータルの下部にある通知を監視します。

    ![Creating Vault](./media/backup-configure-vault/creatingvault1.png)

    バックアップ コンテナーが作成された後、コンテナーが正常に作成されたことを示すメッセージが表示されます。この資格情報コンテナーはまた、Recovery Services 用のリソースで **Active** としてリストされます。

    ![Creating Vault status](./media/backup-configure-vault/backupvaultstatus1.png)

    >[AZURE.IMPORTANT] ストレージ冗長オプションを識別する最適なタイミングは、コンテナーの作成直後かつ、いずれかのコンピューターがコンテナーに登録される前です。項目が資格情報コンテナーに登録されたら、ストレージ冗長オプションはロックされ、変更できなくなります。

4. **ストレージ冗長**オプションを選択します。

    Azure をプライマリ バックアップ ストレージ エンドポイントとして使用する場合 (Windows Server から Azure にバックアップする場合など) は、[geo 冗長ストレージ](../storage/storage-redundancy.md#geo-redundant-storage) オプション (既定値) の選択を検討します。

    Azure を第 3 のバックアップ ストレージ エンドポイントとして使用する場合 (オンプレミスのローカル バックアップ コピーには SCDPM を使用し、長期保存には Azure を使用する場合など) は、[ローカル冗長ストレージ](../storage/storage-redundancy.md#locally-redundant-storage)の選択を検討します。これにより、Azure でデータを格納するためのコストは削減されますが、データの持続性レベルは低くなります。これは、第 3 のコピーとしてなら許容される可能性があります。

    この[概要](../storage/storage-redundancy.md)に記載されている [geo 冗長](../storage/storage-redundancy.md#geo-redundant-storage)ストレージ オプションと[ローカル冗長](../storage/storage-redundancy.md#locally-redundant-storage)ストレージ オプションの詳細をご覧ください。

    a.作成したコンテナーをクリックします。

    b.[クイック スタート] ページで、**[構成]** をクリックします。

    ![Configure Vault status](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

    c.適切なストレージ冗長オプションを選択します。

    **[Geo 冗長]** が既定のオプションであるため、**[ローカル冗長]** を選択した場合は **[保存]** をクリックする必要があります。

    ![GRS](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

    d.左側のナビゲーション ウィンドウで **[Recovery Services]** をクリックして、**[Recovery Services]** のリソースの一覧に戻ります。

    ![Select backup vault](./media/backup-try-azure-backup-in-10-mins/rs-left-nav.png)

## 資格情報コンテナーの資格情報ファイルのダウンロード
データを Azure にバックアップする前に、オンプレミスのサーバー (Windows クライアントまたは Windows Server または Data Protection Manager サーバー) をバックアップ資格情報コンテナーで認証する必要があります。認証は "資格情報コンテナーの資格情報" を使用して実行されます。コンテナーの資格情報ファイルは、Azure ポータルから安全なチャネルを介してダウンロードされます。この証明書の秘密キーは、Azure Backup サービスの管理外となり、ポータルにもサービスにも保存されません。

[コンテナー資格情報を使用した Azure Backup サービスでの認証](backup-introduction-to-azure-backup.md#what-is-the-vault-credential-file)の詳細をご覧ください。

**コンテナーの資格情報ファイルをローカル コンピューターにダウンロードするには:**

1. [管理ポータル](https://manage.windowsazure.com/)にサインインします。

2. 左側のナビゲーション ウィンドウで **[Recovery Services]** をクリックし、作成したバックアップ コンテナーを選択します。

3.  クラウド アイコンをクリックして、バックアップ コンテナーの *[クイック スタート]* ビューに移動します。

    ![Quick view](./media/backup-configure-vault/quickview.png)

4. **[クイック スタート]** ページで **[コンテナー資格情報のダウンロード]** をクリックします。

    ![ダウンロード](./media/backup-configure-vault/downloadvc.png)

    ポータルは、資格情報コンテナーの名前と現在の日付の組み合わせを使用して、資格情報コンテナーの資格情報を生成します。コンテナーの資格情報ファイルは登録ワークフロー中しか使用されず、48 時間後に期限切れとなります。

    コンテナーの資格情報ファイルはポータルからダウンロードできます。

5. **[保存]** をクリックして、資格情報コンテナーの資格情報をローカル アカウントのダウンロード フォルダにダウンロードするか、**[保存]** メニューから *[名前を付けて保存]* を選択して、保存場所を指定します。

    この時点でコンテナーの資格情報を開く必要はありません。

    コンテナーの資格情報は、ご自身のコンピューターからアクセスできる場所に保存してください。ファイル共有 / SMB に格納されている場合は、アクセス許可を確認します。

## Azure Backup エージェントのダウンロード、インストール、および登録
Azure Backup コンテナーを作成したら、エージェントを各 Windows コンピューター (Windows Server、Windows クライアント、System Center Data Protection Manager サーバー、Azure Backup サーバー コンピューター) にインストールする必要があります。このエージェントにより、データやアプリケーションを Azure にバックアップできるようになります。

**エージェントをダウンロード、インストール、登録するには:**

1. [管理ポータル](https://manage.windowsazure.com/)にサインインします。

2. **[Recovery Services]** をクリックし、サーバーに登録するバックアップ資格情報コンテナーを選択します。

3. [クイック スタート] ページで、**[Windows Server、System Center Data Protection Manager、Windows クライアント向けエージェント]、[保存]** の順にクリックします。

    ![エージェントの保存](./media/backup-configure-vault/agent.png)

4. *MARSagentinstaller.exe* のダウンロードが完了したら、**[実行]** をクリック (または保存場所から **MARSAgentInstaller.exe** をダブルクリック) します。エージェントに必要な*インストール フォルダー*と*キャッシュ フォルダー*を選択し、**[次へ]** をクリックします。

    キャッシュ フォルダーとして指定する場所には、バックアップ データの 5% 以上の空き領域が必要です。

    ![Scratch and Cache](./media/backup-configure-vault/recovery-services-agent-setup-wizard-1.png)

5. プロキシ サーバーを使用してインターネットに接続する場合、**[プロキシ構成]** 画面で、プロキシ サーバーの詳細を入力します。認証済みのプロキシを使用する場合は、ユーザー名とパスワードの詳細を入力し、**[次へ]** をクリックします。

    Azure Backup エージェントは、.NET Framework 4.5 と Windows PowerShell を (まだインストールされていない場合は) インストールして、インストールを完了します。

6. エージェントがインストールされたら、**[登録処理を続行]** をクリックして、ワークフローを続行します。

    ![登録](./media/backup-configure-vault/register.png)

7. **[資格情報コンテナーの識別]** 画面で、以前にダウンロードした*コンテナー資格情報ファイル*を参照して選択します。

    ![コンテナー資格情報](./media/backup-configure-vault/vc.png)

    コンテナーの資格情報ファイルは (ポータルからダウンロード後) 48 時間のみ有効です。この画面で何らかのエラー (例:"指定されたコンテナーの資格情報ファイルは期限切れです。") が発生した場合は、Azure ポータルにログインし、コンテナーの資格情報ファイルを再度ダウンロードします。

    セットアップ アプリケーションがアクセスできる場所に、コンテナーの資格情報ファイルがあることを確認します。アクセス関連のエラーが発生した場合は、資格情報コンテナーの資格情報ファイルをこのコンピューターの一時的な場所にコピーし、操作をやり直してください。

    コンテナーの資格情報の資格情報が無効であるというエラー (例: "無効なコンテナーの資格情報が指定されました。ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません。") が発生した場合、ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません。ポータルから新しい資格情報コンテナーの資格情報ファイルをダウンロードしてから操作をやり直してください。通常、このエラーはユーザーが *[コンテナー資格情報のダウンロード]* オプションを連続でクリックした場合に発生します。この場合、最後のコンテナーの資格情報ファイルだけが有効です。

8. **[暗号化の設定]** 画面で、パスフレーズを*生成*するか、パスフレーズ (16 文字以上) を*指定*できます。必ず安全な場所にパスフレーズを保存してください。

    ![暗号化](./media/backup-configure-vault/encryption.png)

    > [AZURE.WARNING] パスフレーズを紛失または忘れてしまった場合、Microsoft はバックアップ データの回復を支援することはできません。エンド ユーザーは暗号化パスフレーズを所有していますが、Microsoft はエンド ユーザーが使用するパスフレーズを確認できません。回復操作中に必要になる場合もあるため、ファイルは安全な場所に保存してください。

9. **[完了]** をクリックします。

    これで、コンピューターがコンテナーに正しく登録され、Microsoft Azure へのバックアップを開始できる状態になりました。

## 次のステップ
- [Azure 無料アカウント](https://azure.microsoft.com/free/)にサインアップします。
- [Windows Server または Windows クライアントをバックアップします](backup-azure-backup-windows-server.md)。
- 不明点がある場合は、「[Azure Backup サービス - FAQ](backup-azure-backup-faq.md)」を参照します。
- [Azure Backup フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=290933)を参照します。

<!---HONumber=AcomDC_0302_2016-->