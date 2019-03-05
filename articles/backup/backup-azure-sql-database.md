---
title: Azure への SQL Server データベースのバックアップ | Microsoft Docs
description: このチュートリアルでは、SQL Server を Azure に バックアップする方法について説明します。 また、SQL Server の復旧についても説明します。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 61219fc4e1fc329708a7e58ee6a293e4e25cca31
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887813"
---
# <a name="back-up-sql-server-databases-on-azure-vms"></a>Azure VM 上の SQL Server データベースのバックアップ 

SQL Server データベースは、低い回復ポイントの目標値 (RPO) と長期のリテンション期間を必要とする重要なワークロードです。 [Azure Backup](backup-overview.md) を使用して、Azure VM 上で稼働している SQL Server データベースをバックアップできます。 

この記事では、Azure VM 上で稼働している SQL Server データベースを Azure Backup Recovery Services コンテナーにバックアップする方法について説明します。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> * SQL Server インスタンスをバックアップするための前提条件の確認。
> * コンテナーの作成と構成。
> * データベースの検出とバックアップの設定。
> * データベースに対する自動保護の設定。

> [!NOTE]
> 現在、この機能はパブリック プレビュー段階にあります。

## <a name="before-you-start"></a>開始する前に

開始する前に、以下を確認します。

1. Azure で SQL Server インスタンスを稼働させていることを確認する。 マーケットプレースで [SQL Server インスタンスをすばやく作成](../sql-database/sql-database-get-started-portal.md)できます。
2. 下記のパブリック プレビューの制限事項を確認する。
3. シナリオのサポートを確認する。
4. このシナリオに関する[一般的な質問を確認](faq-backup-sql-server.md)する。


## <a name="preview-limitations"></a>プレビューの制限事項

このパブリック プレビューには、多数の制限があります。

- SQL Server を稼働している VM では、Azure パブリック IP アドレスにアクセスするためにインターネット接続を必要とします。 
- コンテナーあたり最大 2,000 個の SQL Server データベースをバックアップできます。 それより多い場合、別のコンテナーを作成します。 
- [分散可用性グループ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017)のバックアップは、完全には機能しません。
- バックアップ用の SQL Server Always On Failover Cluster Instance (FCI) はサポートされていません。
- SQL Server のバックアップはポータルで構成する必要があります。 現在、Azure PowerShell、CLI、または REST API を使用してバックアップを構成することはできません。
- FCI ミラー データベース、データベース スナップショット、データベースのバックアップ操作および復元操作はサポートされていません。
- 多数のファイルがあるデータベースは保護できません。 サポートされるファイルの最大数は明確ではありません。 これはファイルの数だけでなく、ファイルのパスの長さによっても異なります。 

SQL Server データベースのバックアップに関する [FAQ](faq-backup-sql-server.md) を参照してください。
## <a name="scenario-support"></a>シナリオのサポート

**サポート** | **詳細**
--- | ---
**サポートされているデプロイ** | SQL Marketplace の Azure VM と、Marketplace 以外の (SQL Server が手動でインストールされる) VM がサポートされています。
**サポートされている地域** | オーストラリア東南部 (ASE)、ブラジル南部 (BRS)、カナダ中部 (CNC)、カナダ東部 (CE)、米国中部 (CUS)、東アジア (EA)、オーストラリア東部 (AE)、米国東部 (EUS)、米国東部 2 (EUS2)、インド中部 (INC)、インド南部 (INS)、東日本 (JPE)、西日本 (JPW)、韓国中部 (KRC)、韓国南部 (KRS)、米国中北部 (NCUS)、北ヨーロッパ (NE)、米国中南部 (SCUS)、東南アジア (SEA)、英国南部 (UKS)、英国西部 (UKW)、米国中西部 (WCUS)、西ヨーロッパ (WE)、米国西部 (WUS)、米国西部 2 (WUS 2)
**サポートされているオペレーティング システム** | Windows Server 2016、Windows Server 2012 R2、Windows Server 2012<br/><br/> Linux は現在サポートされていません。
**サポートされる SQL Server のバージョン** | SQL Server 2017、SQL Server 2016、SQL Server 2014、SQL Server 2012。<br/><br/> Enterprise、Standard、Web、Developer、Express。


## <a name="prerequisites"></a>前提条件

SQL Server データベースをバックアップする前に、次の条件を確認してください。

1. SQL Server インスタンスをホストする VM として、同じリージョンまたはロケールの Recovery Services コンテナーを特定または[作成](backup-azure-sql-database.md#create-a-recovery-services-vault)する。
2. SQL データベースをバックアップするのに必要な [VM のアクセス許可をチェック](#fix-sql-sysadmin-permissions)する。
3. VM が[ネットワーク接続](backup-azure-sql-database.md#establish-network-connectivity)を備えていることを確認する。
4. Azure Backup の[命名ガイドライン](backup-azure-sql-database.md)に従って SQL Server データベースが名付けられていることをチェックする。
5. データベースに対して有効になっているバックアップ ソリューションが他にないことを確認する。 このシナリオを設定する前に、他のすべての SQL Server バックアップを無効にします。 競合が発生することなく、VM 上で稼働している SQL Server データベースに対して Azure Backup を有効にするのと同時に、Azure VM に対して Azure Backup を有効にできます。


### <a name="establish-network-connectivity"></a>ネットワーク接続を確立する

すべての操作において、SQL Server VM 仮想マシンでは、Azure パブリック IP アドレスへの接続を必要とします。 パブリック IP アドレスへの接続がないと、VM の操作 (データベースの検出、バックアップの構成、バックアップのスケジュール、復旧ポイントの復元など) が失敗します。 以下のいずれかのオプションを使用して接続を確立します。

- **Azure データセンターの IP 範囲を許可する**。ダウンロードにおける [IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)を許可します。 ネットワーク セキュリティ グループ (NSG) でのアクセスには、**Set-AzureNetworkSecurityRule** コマンドレットを使用します。
- **トラフィックをルーティングするために HTTP プロキシ サーバーをデプロイする**。Azure VM 上の SQL Server データベースをバックアップする場合、VM 上のバックアップ拡張機能によって HTTPS API が使用され、管理コマンドが Azure Backup に送信されてデータが Azure Storage に送信されます。 また、バックアップ拡張機能では、認証に Azure Active Directory (Azure AD) を使用します。 HTTP プロキシ経由でこれらの 3 つのサービスのバックアップ拡張機能のトラフィックをルーティングします。 パブリック インターネットにアクセスできるように構成されたコンポーネントはバックアップ拡張機能のみです。

各オプションには長所と短所があります

**オプション** | **長所** | **短所**
--- | --- | ---
IP 範囲を許可する | 追加のコストが発生しない。 | IP アドレス範囲が時間の経過と共に変化するため、管理が複雑である。 <br/><br/> Azure Storage だけでなく、Azure 全体へのアクセスを提供することになる。
HTTP プロキシを使用する   | 許可するストレージ URL をプロキシで詳細に制御可能。 <br/><br/> VM に対するインターネット アクセスを単一の場所で実現。 <br/><br/> Azure の IP アドレスの変更の影響を受けない。 | プロキシ ソフトウェアで VM を実行するための追加のコストが発生する。 

### <a name="set-vm-permissions"></a>VM のアクセス許可を設定する

SQL Server データベースのバックアップを構成するとき、Azure Backup によって多数の処理が行われます。

- **AzureBackupWindowsWorkload** 拡張機能を追加します。
- 仮想マシン上のデータベースを検出するために、Azure Backup では **NT SERVICE\AzureWLBackupPluginSvc** というアカウントが作成されます。 このアカウントはバックアップと復元に使用され、SQL sysadmin アクセス許可を必要とします。
- Azure Backup では、データベースの検出と照会に **NT AUTHORITY\SYSTEM** アカウントが利用されます。そのため、このアカウントは SQL 上でパブリック ログインである必要があります。

SQL Server VM を Azure Marketplace から作成しなかった場合、エラー **UserErrorSQLNoSysadminMembership** が発生する可能性があります。 これが発生した場合、[こちらの手順に従ってください](#fix-sql-sysadmin-permissions)。

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Azure Backup のデータベース命名ガイドラインを確認する

データベース名には以下を使用しないでください。

  * 末尾/先頭のスペース
  * 末尾の "!"
  * 右大かっこ "]"

Azure テーブルでサポートされていない文字のエイリアス処理は用意されていますが、これらは使用しないことをお勧めします。 [詳細情報](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN)。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server データベースを検出する

VM 上で稼働しているデータベースを検出します。 
1. [Azure portal](https://portal.azure.com) で、データベースをバックアップするために使用する Recovery Services コンテナーを開きます。

2. **[Recovery Services コンテナー]** ダッシュボードで **[バックアップ]** を選択します。 

   ![[バックアップ] を選択して [バックアップの目標] メニューを開く](./media/backup-azure-sql-database/open-backup-menu.png)

3. **[バックアップの目標]** で、**[ワークロードはどこで実行されていますか?]** を **[Azure]** (既定値) に設定します。

4. **[何をバックアップしますか?]** で、**[Azure VM 内の SQL Server]** を選択します。

    ![バックアップ対象として [Azure VM 内の SQL Server] を選択する](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)


5. **[バックアップの目標]** > **[VM 内のデータベースを検出]** で、**[検出の開始]** を選択して、サブスクリプション内にある保護されていない VM を検索します。 サブスクリプション内にある保護されていない仮想マシンの数によっては、少し時間がかかる場合があります。

    ![VM 内にある DB の検索中はバックアップが保留される](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. VM の一覧で、SQL Server データベースを稼働している VM を選択し、**[Discover DBs]\(DB の検出\)** を選択します。

7. **[通知]** 領域でデータベース検出を追跡します。 VM 上にあるデータベースの数によっては、ジョブが完了するまで少し時間がかかる場合があります。 選択したデータベースが検出されたら、成功のメッセージが表示されます。

    ![デプロイの成功メッセージ](./media/backup-azure-sql-database/notifications-db-discovered.png)

    - 保護されていない VM は検出後、名前およびリソース グループ別に一覧に表示されます。
    - VM が予想どおりに一覧表示されない場合、それが既にコンテナーにバックアップされていないかをチェックしてください。
    - 名前の同じ複数の VM が、異なるリソース グループに含まれる可能性があります。 

9. SQL Server データベースを稼働している VM を選択し、**[Discover DBs]\(DB の検出\)** を選択します。 
10. Azure Backup によって、VM 上のすべての SQL Server データベースが検出されます。 検出中、バックグラウンドでは以下が行われます。

    - Azure Backup によって、ワークロード バックアップ用のコンテナーに VM が登録されます。 登録された VM 上のすべてのデータベースは、このコンテナーに対してのみバックアップできます。
    - Azure Backup によって、**AzureBackupWindowsWorkload** 拡張機能が VM にインストールされます。 エージェントは SQL データベースにインストールされません。
    - Azure Backup によって、サービス アカウント **NT Service\AzureWLBackupPluginSvc** が VM 上に作成されます。
      - すべてのバックアップおよび復元操作に、サービス アカウントを使用します。
      - **NT Service\AzureWLBackupPluginSvc** には、SQL sysadmin 権限が必要です。 Azure Marketplace で作成されたすべての SQL Server VM には、**SqlIaaSExtension** が元からインストールされています。 **AzureBackupWindowsWorkload** 拡張機能は **SQLIaaSExtension** を使用して自動的に必要な権限を取得します。
    - VM をマーケットプレースから作成しなかった場合、VM には **SqlIaaSExtension** がインストールされておらず、検出操作はエラー メッセージ **UserErrorSQLNoSysAdminMembership** と共に失敗します。 [#fix-sql-sysadmin-permissions] の手順に従って、この問題を修正してください。

        ![VM とデータベースを選択する](./media/backup-azure-sql-database/registration-errors.png)



## <a name="configure-a-backup-policy"></a>バックアップ ポリシーを構成する 

バックアップの負荷を最適化するために、Azure Backup では、1 つのバックアップ ジョブにおけるデータベースの最大数が 50 個に設定されています。

- 50 個を超えるデータベースを保護するには、複数のバックアップを構成します。
- または、自動保護を有効にできます。 自動保護では、既存のデータベースがまとめて保護されるほか、可用性グループのインスタンスに追加された新しいデータベースが自動的に保護されます。


次のようにバックアップを構成します。

1. コンテナー ダッシュボードで **[バックアップ]** を選択します。 

   ![[バックアップ] を選択して [バックアップの目標] メニューを開く](./media/backup-azure-sql-database/open-backup-menu.png)

3. **[バックアップの目標]** メニューで、**[ワークロードはどこで実行されていますか?]** を **[Azure]** に設定します。

4. **[何をバックアップしますか?]** で、**[Azure VM 内の SQL Server]** を選択します。

    ![バックアップ対象として [Azure VM 内の SQL Server] を選択する](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    
5. **[バックアップの目標]** で **[バックアップの構成]** を選択します。

    ![[バックアップの構成] を選択する](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    ![スタンドアロン データベースであるすべての SQL Server インスタンスの表示](./media/backup-azure-sql-database/list-of-sql-databases.png)

6. 保護したいデータベースをすべて選択して、**[OK]** を選択します。

    ![データベースの保護](./media/backup-azure-sql-database/select-database-to-protect.png)

    - すべての SQL Server インスタンスが表示されます (スタンドアロンおよび可用性グループ)。
    - インスタンス名または可用性グループの左側にある下矢印を選択して、フィルター処理します。

7. **[バックアップ]** メニューで **[バックアップ ポリシー]** を選択します。 

    ![[バックアップ ポリシー] を選択する](./media/backup-azure-sql-database/select-backup-policy.png)

8. **[バックアップ ポリシーの選択]** でポリシーを選択し、**[OK]** をクリックします。

    - 既定のポリシーを選択します:**HourlyLogBackup**。
    - SQL 用に以前に作成された既存のバックアップ ポリシーを選択する。
    - RPO とリテンション期間の範囲に基づいて[新しいポリシーを定義する](backup-azure-sql-database.md#configure-a-backup-policy)。
    - プレビュー期間中は、既存のバックアップ ポリシーを編集できません。
    
9. **[バックアップ] メニュー**で、**[バックアップの有効化]** を選択します。

    ![選択したバックアップ ポリシーを有効にする](./media/backup-azure-sql-database/enable-backup-button.png)

10. ポータルの **[通知]** 領域で構成の進行状況を追跡します。

    ![[通知] 領域](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>バックアップ ポリシーの作成

バックアップ ポリシーでは、バックアップが取得されるタイミングと、それらが保持される期間を定義します。 

- ポリシーはコンテナー レベルで作成されます。
- 複数のコンテナーでは同じバックアップ ポリシーを使用できますが、各コンテナーにバックアップ ポリシーを適用する必要があります。
- バックアップ ポリシーの作成時には、日次での完全バックアップが既定値になっています。
- 差分バックアップを追加することは可能ですが、週次で実行するように完全バックアップを構成する場合だけです。
- さまざまな種類のバックアップ ポリシー[について学習してください](backup-architecture.md#sql-server-backup-types)。


バックアップ ポリシーを作成するには:

1. コンテナーで、**[バックアップ ポリシー]** > **[追加]** の順にクリックします。
2. **[追加]** メニューで **[Azure VM 内の SQL Server]** をクリックします。 これでポリシーの種類が定義されます。

   ![新しいバックアップ ポリシーのポリシーの種類を選択する](./media/backup-azure-sql-database/policy-type-details.png)

3. **[ポリシー名]** に新しいポリシーの名前を入力します。 
4. **完全バックアップのポリシー**で **[バックアップ頻度]** を選択し、**[毎日]** または **[毎週]** を選びます。

    - **[毎日]** を選択する場合は、バックアップ ジョブが開始されるときに、時刻とタイム ゾーンを選択します。
    - 完全バックアップを実行する必要があります。**[完全バックアップ]** オプションをオフにすることはできません。
    - **[完全バックアップ]** をクリックし、ポリシーを表示します。 
    - 日次の完全バックアップを選択する場合は、差分バックアップを作成できません。
    - **[毎週]** を選択する場合は、バックアップ ジョブが開始されるときに、曜日、時刻、およびタイム ゾーンを選択します。

    ![バックアップ ポリシーの新しいフィールド](./media/backup-azure-sql-database/full-backup-policy.png)  

5. **[リテンション期間]** では、すべてのオプションが既定で選択されています。 使用したくない不要なリテンション期間の制限を解除して、使用する間隔を設定します。 

    - 復旧ポイントは、そのリテンション期間の範囲に基づいて、リテンション期間に対してタグ付けされます。 たとえば、日次での完全バックアップを選択した場合、日ごとにトリガーされる完全バックアップは 1 回だけです。
    - 特定の曜日のバックアップがタグ付けされ、週次でのリテンション期間の範囲と週次でのリテンション期間の設定に基づいて保持されます。
    - 月次および年次のリテンション期間の範囲でも、同様の動作になります。

   ![[リテンション期間] の間隔の設定](./media/backup-azure-sql-database/retention-range-interval.png)

    

6. **完全バックアップのポリシー** メニューで、**[OK]** を選択して設定を確定します。
7. 差分バックアップ ポリシーを追加するには、**[差分バックアップ]** を選択します。 

   ![リテンション期間の間隔の設定](./media/backup-azure-sql-database/retention-range-interval.png)
   ![差分バックアップのポリシー メニューを開く](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. **差分バックアップのポリシー**で、**[有効]** を選択して頻度とリテンション期間の制御を開きます。 

    - 最多で、1 日に 1 回の差分バックアップをトリガーできます。
    - 差分バックアップは、最大 180 日間保持できます。 より長いリテンション期間が必要な場合は、完全バックアップを使用する必要があります。

9. **[OK]** を選択してポリシーを保存し、**[バックアップ ポリシー]** のメイン メニューに戻ります。

10. トランザクション ログ バックアップ ポリシーを追加するには、**[ログ バックアップ]** を選択します。
11. **[ログ バックアップ]** で、**[有効]** を選択して、頻度とリテンション期間の制御を設定します。 ログ バックアップは、毎 15 分の頻度で実行でき、最大 35 日間保持することが可能です。
12. **[OK]** を選択してポリシーを保存し、**[バックアップ ポリシー]** のメイン メニューに戻ります。

   ![ログ バックアップ ポリシーを編集する](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. **[バックアップ ポリシー]** メニューで、**SQL バックアップの圧縮**を有効にするかどうかを選択します。
    - 圧縮は既定で、無効になっています。
    - バックエンドでは、Azure Backup は SQL ネイティブ バックアップの圧縮を使用します。

14. バックアップ ポリシーに対する編集が完了したら、**[OK]** を選択します。



## <a name="enable-auto-protection"></a>自動保護を有効にする  

自動保護を有効にして、既存の全データベースのほか、スタンドアロンの SQL Server インスタンスまたは SQL Server Always On 可用性グループに将来追加されるデータベースを自動的に保護します。 

- 自動保護を有効にしてポリシーを選択すると、保護されている既存のデータベースで引き続き以前のポリシーが使用されます。
- 自動保護でまとめて選択できるデータベースの数に制限はありません。

次のように自動保護を有効にします。

1. **[Items to backup]\(バックアップする項目\)** で、自動保護を有効にしたいインスタンスを選択します。
2. **[Autoprotect]\(自動保護\)** の下にあるドロップダウンを選択して、**[オン]** に設定します。 次に、 **[OK]** をクリックします

    ![Always On 可用性グループで自動保護を有効にする](./media/backup-azure-sql-database/enable-auto-protection.png)

3. バックアップはすべてのデータベースに対して一括で構成され、**[バックアップ ジョブ]** で追跡できます。


自動保護を無効にする必要がある場合、**[バックアップの構成]** のインスタンス名をクリックして、インスタンスについて **[自動保護を無効にする]** を選択します。 すべてのデータベースは引き続きバックアップされます。 ただし、将来のデータベースは自動的に保護されません。

![そのインスタンスの自動保護を無効にする](./media/backup-azure-sql-database/disable-auto-protection.png)


## <a name="fix-sql-sysadmin-permissions"></a>SQL sysadmin 権限を修正する

**UserErrorSQLNoSysadminMembership** エラーが理由でアクセス許可を修正する必要がある場合、以下を実行します。

1. SQL Server sysadmin アクセス許可があるアカウントを使用して、SQL Server Management Studio (SSMS) にサインインします。 特殊な権限が必要でない限り、Windows 認証が機能するはずです。
2. SQL Server で、**Security/Logins** フォルダーを開きます。

    ![セキュリティ/ログイン フォルダーを開いてアカウントを表示する](./media/backup-azure-sql-database/security-login-list.png)

3. **[ログイン]** フォルダーを右クリックし、**[新しいログイン]** を選択します。 **[ログイン - 新規作成]** で **[検索]** を選択します。

    ![[ログイン - 新規作成] ダイアログ ボックスで [検索] を選択する](./media/backup-azure-sql-database/new-login-search.png)

3. Windows 仮想サービス アカウント **NT SERVICE\AzureWLBackupPluginSvc** は、仮想マシンの登録と SQL 検出フェーズ中に作成済みです。 **[選択するオブジェクト名を入力します]** に示されているようにアカウント名を入力します。 **[名前の確認]** を選択して名前解決を行います。 Click **OK**.

    ![[名前の確認] を選択して不明なサービス名を解決する](./media/backup-azure-sql-database/check-name.png)


4. **[サーバー ロール]** で、**sysadmin** ロールが選択されていることを確認します。 Click **OK**. この時点で、必要な権限が揃っているはずです。

    ![sysadmin サーバー ロールが選択されていることを確認する](./media/backup-azure-sql-database/sysadmin-server-role.png)

5. 次にデータベースを Recovery Services コンテナーと関連付けます。 Azure portal の **[保護されたサーバー]** 一覧で、エラー状態にあるサーバーを右クリックし、**[DB の再検出]** を選択します。

    ![サーバーに適切な権限があることを確認する](./media/backup-azure-sql-database/check-erroneous-server.png)

6. **[通知]** 領域で進行状況をチェックします。 選択したデータベースが検出されたら、成功のメッセージが表示されます。

    ![デプロイの成功メッセージ](./media/backup-azure-sql-database/notifications-db-discovered.png)

または、**[AUTOPROTECT]\(自動保護\)** 列の対応するドロップダウン **[オン]** オプションを選択することにより、インスタンス全体または Always On 可用性グループで[自動保護](backup-azure-sql-database.md#enable-auto-protection)を有効にすることができます。 [自動保護](backup-azure-sql-database.md#enable-auto-protection)機能では、既存のすべてのデータベースの保護が一度に有効になるだけでなく、それ以降にそのインスタンスまたは可用性グループに追加されるすべての新しいデータベースも自動的に保護されます。  

   ![Always On 可用性グループで自動保護を有効にする](./media/backup-azure-sql-database/enable-auto-protection.png)

## <a name="next-steps"></a>次の手順

- バックアップされた SQL Server データベースの復元に[ついて学習します](restore-sql-database-azure-vm.md)。
- バックアップされた SQL Server データベースの管理に[ついて学習します](manage-monitor-sql-database-backup.md)。

