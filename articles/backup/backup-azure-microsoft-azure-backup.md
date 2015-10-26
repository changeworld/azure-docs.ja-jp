<properties
  pageTitle="Microsoft Azure にワークロードをバックアップするための準備 | Microsoft Azure"
  description="この記事では、Microsoft Azure Backup にアプリケーション ワークロードをバックアップする機能の概要について説明します。"
  services="backup"
  documentationCenter=""
  authors="SamirMehta"
  manager="shreeshd"
  editor=""/>

<tags
  ms.service="backup"
  ms.workload="storage-backup-recovery"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/07/2015"
  ms.author="sammehta; jimpark"/>

# Microsoft Azure にワークロードをバックアップするための準備

> [AZURE.SELECTOR]
- [SCDPM](backup-azure-dpm-introduction.md)
- [Azure Backup](backup-azure-microsoft-azure-backup.md)

この記事では、データを Azure にバックアップできるように、ワークロードをバックアップする環境を準備する方法について説明します。Microsoft Azure Backup サーバーを使用すると、Hyper-V VM、Microsoft SQL Server、SharePoint Server、Microsoft Exchange、Windows クライアントなどのアプリケーションのワークロードを次の対象に保護することができます。

- **ディスク** (D2D)。第 1 層のワークロードに高い RTO を実現します。
- **Azure** (D2D2C)。長期保存用です。

また、オンプレミスの単一のユーザー インターフェイスから、保護対象のさまざまなエンティティ (サーバーとクライアント) の保護を管理することができます。

>[AZURE.NOTE]Microsoft Azure Backup サーバーは、Data Protection Manager (DPM) のワークロード バックアップの機能を継承しています。これらの機能の一部については、DPM ドキュメントへのポインターがあります。ただし、Microsoft Azure Backup サーバーは、テープ上の保護や System Center との統合の機能は提供していません。

Microsoft Azure Backup サーバーは、次の形でデプロイすることができます。

- 物理的なスタンドアロン サーバー。
- Hyper-V 仮想マシン: オンプレミスの Hyper-V ホスト サーバー上でホストされている仮想マシンとして Microsoft Azure Backup サーバーを実行して、オンプレミスのデータをバックアップすることができます。この環境における考慮事項の一覧については、「[DPM を仮想マシンとしてオンプレミス Hyper-V サーバーにインストールする](https://technet.microsoft.com/library/dn440941.aspx)」を参照してください。
- VMWare 内の Windows 仮想マシン: Microsoft Azure Backup サーバーをデプロイして、VMWare 内の Windows 仮想マシンで実行されている Microsoft のワークロードを保護することができます。このシナリオでは、Microsoft Azure Backup サーバーをスタンドアロンの物理サーバー、Hyper-V 仮想マシン、または VMWare 内の Windows 仮想マシンとしてデプロイすることができます。
- Azure の仮想マシン: Azure 内の仮想マシンとして Microsoft Azure Backup サーバーを実行して、Azure の仮想マシンとして実行されているクラウドのワークロードをバックアップすることができます。このデプロイの詳細については、[Azure の仮想マシンとしての DPM のインストール](https://technet.microsoft.com/library/hh758176.aspx)に関するページを参照してください。

また、

- Microsoft Azure Backup は、Windows Server 2008 R2 SP1、2012、および 2012 R2 にインストールすることができます。
- Microsoft Azure Backup サーバーは、SCDPM または SCDPM RA のエージェントがインストールされているコンピューターにはインストールできません。
- Microsoft Azure Backup サーバーは、Microsoft Azure Backup エージェントがインストールされていて、Azure Backup コンテナーに登録されているコンピューターにはインストールできません。



Microsoft Azure Backup をインストールするサーバーを選択したら、次の手順を実行する必要があります。

- Microsoft Azure Backup サーバーの前提条件が満たされていることを確認する
- バックアップ コンテナーを作成する
- コンテナー資格情報をダウンロードする
- Microsoft Azure Backup サーバーのセットアップ ファイルをダウンロードする

これらの各手順について、以下で詳しく説明します。

## 開始する前に

現在、Microsoft Azure Backup サーバーは、英語のロケールのみで使用できます。目下のところ、Microsoft Azure Recovery Services は、Microsoft Azure Government データ センターと Microsoft Azure (中国) 以外のすべての地域で使用できます。

問題なくインストールするには、Microsoft Azure Backup サーバーをインストールする前に、次の前提条件が満たされていることを確認します。

- サーバーがインターネットに接続されている必要があります。Microsoft Azure にサーバーからアクセスできる必要があります。
- Microsoft Azure Backup をインストールするためのサーバーの要件は DPM と同じです。詳細については、[ハードウェア要件](https://technet.microsoft.com/library/dn726764.aspx)に関するページを参照してください。
- Microsoft Azure Backup サーバーがドメインに参加している必要があります。
- Microsoft Azure Backup サーバーには、.Net 3.5、.Net 4.0、.Net 3.5 SP1 の機能がインストールされている必要があります。詳細については、[.Net Framework を有効にする方法の詳細](https://technet.microsoft.com/library/dn482071.aspx)に関するページを参照してください。
- Microsoft Azure Backup サーバーには、Windows Management Framework 4.0 がインストールされている必要があります。[こちら](http://www.microsoft.com/download/details.aspx?id=40855)からダウンロードできます。
- Microsoft Azure Backup サーバー データ ストレージの専用領域として使用するディスクの推奨される記憶域プールのサイズは、保護対象のデータのサイズの 1.5 倍です。詳細については、[このトピック](https://technet.microsoft.com/library/hh758176.aspx#DPMserver) の「ディスクおよび記憶域」のセクションを参照してください。

データをバックアップするために、次の手順で Microsoft Azure Backup サーバーを準備します。

1. **バックアップ コンテナーの作成**: Azure Backup コンソールでコンテナーを作成します。
2. **コンテナー資格情報のダウンロード**: Azure Backup で、作成した管理証明書をコンテナーにダウンロードします。
3. **Microsoft Azure Backup サーバーのダウンロード**: バックアップ コンテナーのクイック スタート ページからアプリケーション ワークロード用の Microsoft Azure Backup サーバーをダウンロードすることができます。

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

## Microsoft Azure Backup サーバーをダウンロードする
コンテナー資格情報と同様に、アプリケーション ワークロード用の Microsoft Azure Backup をバックアップ コンテナーの**クイック スタート ページ**からダウンロードすることができます。

1. **[アプリケーション ワークロード用 (Disk to Disk to Cloud)]** をクリックします。

    ![Microsoft Azure Backup Welcome Screen](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)

2. **[Download]** をクリックします。

    ![Download center 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)

3. すべてのファイルを選択して **[次へ]** をクリックします。![Download center 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    >[AZURE.NOTE]すべてのファイルをまとめてダウンロードするとサイズが 3 G を超えるため、10 Mbps のダウンロード リンクでは、ダウンロードが完了するまでに最大で 60 分かかることがあります。

4. すべてのファイルを同じフォルダーに配置します。

5. Microsoft Azure Backup のダウンロード ページからすべてのファイルをダウンロードします。

## Microsoft Azure Backup サーバーをインストールする
セットアップを開始する前に、前のセクションで説明した前提条件が満たされていることを確認します。

1. すべてのファイルをダウンロードしたら、**MicrosoftAzureBackupInstaller.exe** をクリックします。**Microsoft Azure Backup セットアップ ウィザード**が表示されます。

    ![Microsoft Azure Backup Installer](./media/backup-azure-microsoft-azure-backup/installer-click.png)

2. ファイルを展開するサーバー上の場所を指定して、**[次へ]** をクリックします。

    ![Microsoft Azure Backup Setup Wizard](./media/backup-azure-microsoft-azure-backup/extract-to.png)

3. **[展開]** をクリックしてセットアップ ファイルの展開を開始します。

    ![Microsoft Azure Backup Setup Wizard](./media/backup-azure-microsoft-azure-backup/ready-to-extract.png)

4. setup.exe を起動するためのチェック ボックスをオンにして、Microsoft Azure Backup サーバーのインストールを開始し、**[完了]** をクリックします。

    ![Microsoft Azure Backup Setup Wizard](./media/backup-azure-microsoft-azure-backup/launch-setup.png)

5. **[Microsoft Azure Backup]** をクリックしてセットアップ ウィザードを起動します。

    ![Microsoft Azure Backup Setup Wizard](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)

6. [ようこそ] 画面で、**[次へ]** をクリックします。

    ![Microsoft Azure Backup Welcome Screen](./media/backup-azure-microsoft-azure-backup/welcome-screen.png)

7. **[確認]** をクリックして、Microsoft Azure Backup サーバーのハードウェアとソフトウェアの前提条件が満たされているかどうかを確認します。

    ![Microsoft Azure Backup PreReq1](./media/backup-azure-microsoft-azure-backup/prereq-screen1.png)

8. 前提条件がすべて正常に満たされている場合は、コンピューターが要件を満たしていることを示すメッセージが表示されます。**[次へ]** をクリックします。

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/prereq-screen2.png)

9. Microsoft Azure Backup サーバーには、SQL Server Standard が必要です。既存の SQL Server 2014 Standard ローカル インスタンスを選択するか、ウィザードから SQL Server Standard をインストールします。**[確認してインストール]** をクリックし、必要な SQL の前提条件がサーバーにインストールされているかどうかを確認します。

    ![Microsoft Azure Backup SQL1](./media/backup-azure-microsoft-azure-backup/sql-setup1.png)

    エラーが発生し、コンピューターの再起動を推奨された場合は、コンピューターを再起動して **[再確認]** をクリックします。

    ![SiS Filter Failure](./media/backup-azure-microsoft-azure-backup/sis-filter.png)

10. 前提条件が正常にインストールされたら、**[次へ]** をクリックします。

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/sql-setup3.png)

11. Microsoft Azure Backup サーバーのファイルをインストールする場所を指定し、**[次へ]** をクリックします。

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    >[AZURE.NOTE]スクラッチ場所は、Azure へのバックアップの要件です。スクラッチ場所が、クラウドにバックアップする予定のデータの 5% 以上であることを確認します。ディスクを保護するために、インストールが完了した後で個別のディスクを構成する必要があります。記憶域プールの詳細については、「[記憶域プールおよびディスク記憶域の構成](https://technet.microsoft.com/library/hh758075.aspx)」を参照してください。

12. 制限付きのローカル ユーザー アカウント用に強力なパスワードを指定し、**[次へ]** をクリックします。

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)

    >[AZURE.NOTE]強力なパスワードを設定することは、システムのセキュリティに不可欠です。強力なパスワードとは、長さが 6 文字以上で、ユーザーのアカウント名の一部またはすべてが含まれていない、大文字、小文字、数字、記号 (!、@、# など) の 4 つの文字の種類のうちの 3 つ以上が含まれているパスワードのことです。

13. *Microsoft Update* を使用するかどうかを選択し、更新プログラムを確認して **[次へ]** をクリックします。

    >[AZURE.NOTE]Windows Update を Microsoft Update にリダイレクトすることをお勧めします。Microsoft Update は、Windows と Microsoft Azure Backup サーバーなどのその他の製品のセキュリティ更新プログラムと重要な更新プログラムを提供しています。

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

14. *[設定の概要]* を確認し、**[インストール]** をクリックします。

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)

    インストールは段階的に実施します。最初の段階でサーバーにエージェントをインストールし、この製品と一緒にダウンロードした有効な Azure Backup コンテナー資格情報にこのサーバーを登録する必要があります。インターネットに直接接続できるかどうかがウィザードによって確認されます。インターネット接続を利用可能な場合は、インストールを続行できます。利用可能でない場合は、プロキシの詳細を指定してインターネットに接続する必要があります。

15. **[次へ]** をクリックして Microsoft Azure Recovery Services エージェントのインストール フェーズを開始します。

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/proxy.png)

    前提条件がウィザードでチェックされます。不足している場合はインストールします。

16. **[インストール]** をクリックします。

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/mars-prereq.png)

17. エージェントのインストールが完了したら、**[次へ]** をクリックして、Azure Backup コンテナーにこのサーバーを登録します。

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/mars-successful.png)

18. サーバーを登録する Azure Backup コンテナー資格情報を指定して、**[次へ]** をクリックします。

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/vault-reg-screen.png)

19. Azure に送信されるデータを暗号化および暗号化解除するために使用するパスフレーズを指定し、このパスフレーズを保存する場所を指定します。自動的にパスフレーズを生成するか、最小で 16 文字の独自のパスフレーズを指定することができます。**[次へ]** をクリックします。

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/encrypt1.png)

    >[AZURE.NOTE]機密性を保つために、Microsoft Azure Backup サーバーは Azure へのデータと一緒にこのパスフレーズを送信することはありません。このパスフレーズは、Azure のデータを復元するときに必要となるため、安全な場所に保存する必要があります。

    Microsoft Azure Backup サーバーの登録が正常に完了すると、(選択した場合は) SQL Server 2014 Standard のインストールと構成が続行されます。

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/sql-install-start-screen.png)

    SQL のインストールが完了すると、Microsoft Azure Backup サーバー コンポーネントがインストールされます。

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/venus-installation-screen.png)

20. 製品がインストールされてデスクトップ アイコンが作成されたら、アイコンをダブルクリックして製品を起動します。

    ![Icons](./media/backup-azure-microsoft-azure-backup/icons.png)

## 記憶域プールにディスクを追加する

ワークロードをディスクと Azure にバックアップするには、まず、記憶域プールにディスクを追加する必要があります。ディスクを追加する方法の詳細については、「[記憶域プールおよびディスク記憶域の構成](https://technet.microsoft.com/library/hh758075.aspx)」を参照してください。

記憶域プールが構成されると、ディスクと Azure の両方にアプリケーション ワークロードをバックアップすることができます。

## トラブルシューティング

Microsoft Azure Backup サーバーがセットアップ段階 (またはバックアップや復元) でエラーのため失敗した場合、詳細については、この[エラー コードのドキュメント](https://support.microsoft.com/kb/3041338)を参照してください。[Azure Backup 関連の FAQ](backup-azure-backup-faq.md) に関するページも参照してください。

## FAQ

### インストールとデプロイ

Q: NTFS 圧縮は、ディスク バックアップに使用される Microsoft Azure Backup サーバー ディスクやボリュームでサポートされていますか。 <br>A: NTFS は、Microsoft Azure Backup サーバーに接続されているディスクやボリュームではサポートされて**いません**。

Q: サーバーの Microsoft Azure Backup サーバーをデプロイ後に新しいドメインに移動できますか。 <br>A: いいえ。Microsoft Azure Backup サーバーをデプロイ後に新しいドメインに移動することはサポートされて**いません**。

Q: Microsoft Azure Backup サーバーは、ドメイン コントローラーにインストールされている場合、どのようなリソースを保護することができますか。 <br>A: Microsoft Azure Backup サーバーは、ドメイン コントローラーと同じサーバーにインストールされている場合、ローカル データ ソースのみを保護することができます。Microsoft Azure Backup サーバーで他のサーバーのワークロードを保護するには、ドメイン コントローラーと同じコンピューター上に Microsoft Azure Backup サーバーをインストールしないでください。

Q: Microsoft Azure Backup サーバーでは、リモートの SQL Server インスタンスを使用することができますか。 <br>A: いいえ。Microsoft Azure Backup サーバーは、ローカルの SQL Server インスタンスのみを使用できます。

### Microsoft Azure Backup サーバーの記憶域プール

Microsoft Azure Backup サーバーの記憶域プールに仮想ディスク (VHD/HDx) を指定することはできますか。 <br>A: はい。

Q: Microsoft Azure Backup サーバーの記憶域プールで重複除去することはできますか。 <br>A: はい。重複除去は、Microsoft Azure Backup サーバーの記憶域プールで利用できます。Microsoft Azure Backup サーバーのユーザー エクスペリエンスについては、この [DPM のブログの投稿](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx)で詳しく説明されています。

Q: USB やリムーバブル ドライブを Microsoft Azure Backup サーバーの記憶域プールに使用できますか。 <br>A: いいえ。

### Microsoft Azure Backup サーバーの Azure サービスへの接続性

Q: Microsoft Azure Backup サーバーの Azure サービスへの接続と Azure サブスクリプションの状態は、バックアップと復元にどのような影響を与えますか。 <br>A: この製品が正常に動作するには、Microsoft Azure Backup サーバーが Azure サービスに接続されている必要があります。同時に、Azure のサブスクリプションが正常な状態である必要があります。

次の表では、さまざまな接続と Azure サブスクリプションの状態に関する Microsoft Azure Backup サーバーの機能を示しています。

| 接続状態 | Azure サブスクリプション | Azure へのバックアップ| ディスクへのバックアップ | Azure からの復元 | ディスクからの復元 |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| 接続中 | アクティブ | 許可 | 許可 | 許可 | 許可 |
| 接続中 | 有効期限切れ | 停止済み | 停止済み | 許可 | 許可 |
| Normal | プロビジョニング解除済み | 停止済み | 停止済み | 停止され、Azure の回復ポイントが削除される | 停止済み |
| 切断されている期間が 15 日を越える | アクティブ | 停止済み | 停止済み | 許可 | 許可 |
| 切断されている期間が 15 日を越える | 有効期限切れ | 停止済み | 停止済み | 許可 | 許可 |
| 切断されている期間が 15 日を越える | プロビジョニング解除済み | 停止済み | 停止済み | 停止され、Azure の回復ポイントが削除される | 停止済み |

Q: サブスクリプションの状態が引き続き*アクティブ*であると仮定すると、Microsoft Azure Backup サーバーの接続が復元された場合、どうなりますか。 <br>A: Microsoft Azure Backup サーバーの Azure サービスへの接続が正常に再開され、サブスクリプションの状態が*アクティブ*になると、Microsoft Azure Backup サーバーのすべての操作が許可されます。バックアップはディスクと Azure の両方に対して通常どおりに実行されます。

Q: サブスクリプションの状態が*有効期限が切れ*の状態から*アクティブ*な状態に復元された場合、どうなりますか。 <br>A: Microsoft Azure Backup サーバーの Azure サービスへの接続が正常であると仮定すると、Microsoft Azure Backup サーバーのサブスクリプションの状態が*アクティブ*に復元された場合、Microsoft Azure Backup サーバーのすべての操作が許可されます。バックアップはディスクと Azure の両方に対して通常どおりに実行されるようになります。

Q: サブスクリプションの状態が*プロビジョニング解除済み*の状態から*アクティブ*な状態に復元された場合、どうなりますか。 <br>A: Microsoft Azure Backup サーバーの Azure サービスへの接続が正常であると仮定すると、Microsoft Azure Backup サーバーのサブスクリプションの状態が*アクティブ*に復元された場合、Microsoft Azure Backup サーバーのすべての操作が許可されます。ただし、Azure の回復ポイントが削除され、復元できなくなります。ディスクのバックアップが有効な保有期間の範囲内にある場合は、ディスクの回復ポイントを復元できます。

Q: Microsoft Azure Backup サーバーからパブリックの Azure サービスへの接続を許可する場合、どのような例外を作成する必要があるでしょうか。 <br>A: ファイアウォールのプロファイルで次のドメインのアドレスを許可する必要があります。www.msftncsi.com、*.Microsoft.com、*.WindowsAzure.com、*.microsoftonline.com、*.windows.net

Q: Microsoft Azure Backup サーバーが Azure サービスに接続できるかどうかはどのようにして検証できますか。 <br>A: Microsoft Azure Backup サーバーのコンソールで、次の PowerShell コマンドレットを実行します。

```
Get-DPMCloudConnection
Output:
TRUE - Connected to Azure service
FALSE - Not connected to Azure service
```

Q: なぜサブスクリプションの状態が*アクティブ*でないのでしょうか。 どうすればこれを*アクティブ*に変更できますか。 <br>A: [サブスクリプション ポータル](https://account.windowsazure.com/Subscriptions)にログオンし、サブスクリプションを管理します。

### 課金

Q: Microsoft Azure Backup サーバーの課金モデルについて教えてください。 <br>A: ユーザーは保護するインスタンス モデルを介して課金されます。詳細については、[価格](http://azure.microsoft.com/pricing/details/backup/)のページの FAQ を参照してください。

Q: ディスク上のデータのみを保護している場合、どのような課金モデルになりますか。 <br>A: 課金モデルは保護するインスタンス モデルと同じです。このデータはオンプレミスの記憶域に保護されているため、ディスク ベースのバックアップのみでは Azure Storage の料金は発生しません。この場合、お客様は保護するインスタンスの手数料のみを請求されます。インスタンスの定義とインスタンスごとの料金の詳細については、[価格](http://azure.microsoft.com/pricing/details/backup/)のページの FAQ を参照してください。

Q: 保護するインスタンスごとの料金について教えてください。 <br>A: [価格](http://azure.microsoft.com/pricing/details/backup/)のページを参照してください。

Q: さまざまなアプリケーション ワークロードとその保護するインスタンスの料金を重点的に説明している例はどこで確認できますか。 <br>A: [価格](http://azure.microsoft.com/pricing/details/backup/)のページの「例」のセクションを参照してください。

Q: Microsoft Azure Backup サーバーでは、ディスクとクラウドの両方で保護しているデータソースにはどのように課金されますか。 <br>A: データがディスクとクラウドのどちらにバックアップされているかに関係なく、Microsoft Azure Backup サーバーでは、保護するインスタンスに基づいて課金されます。保護するインスタンスのサイズは、データソースのフロント エンドのサイズに基づいて計算されます。Azure Storage のバックアップ データには、Azure Storage の費用も適用されます。

## 次のステップ

以下の記事により、Microsoft Azure Backup サーバーを使用したワークロードの保護について理解を深めてください。

- [SQL Server のバックアップ](backup-azure-backup-sql.md)
- [SharePoint サーバーのバックアップ](backup-azure-backup-sharepoint.md)
- [代替サーバーのバックアップ](backup-azure-alternate-dpm-server.md)

<!---HONumber=Oct15_HO3-->