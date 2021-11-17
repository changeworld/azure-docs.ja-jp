---
title: Azure VM 上の SQL Server データベースを復元する
description: この記事では、Azure VM 上で実行されており、Azure Backup でバックアップしてある SQL Server データベースを復元する方法について説明します。 [リージョンをまたがる復元] を使用して、データベースをセカンダリ リージョンに復元することもできます。
ms.topic: conceptual
ms.date: 11/02/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 0e687cbae798a480eaf417de04b653b88d2e2c94
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131431114"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Azure VM 上の SQL Server データベースを復元する

この記事では、Azure 仮想マシン (VM) 上で実行されており、[Azure Backup](backup-overview.md) サービスで Azure Backup Recovery Services コンテナーにバックアップしてある SQL Server データベースを復元する方法について説明します。

この記事では、SQL Server データベースを復元する方法について説明します。 詳細については、[Azure VM で SQL Server データベースをバックアップする](backup-azure-sql-database.md)方法に関するページをご覧ください。

## <a name="restore-to-a-time-or-a-recovery-point"></a>特定の時刻または復旧ポイントに復元する

Azure Backup は、Azure VM 上で実行されている SQL Server データベースを次のように復元できます。

- トランザクション ログ バックアップを使用して、特定の日付または時刻 (秒) に復元します。 Azure Backup は、選択された時刻に基づいて復元するために必要な、適切な完全または差分バックアップおよび一連のログ バックアップを自動的に判定します。
- 特定の復旧ポイントに復元するには、特定の完全または差分バックアップを復元します。

## <a name="restore-prerequisites"></a>復元の前提条件

データベースを復元する前に、次のことに注意してください。

- このデータベースを同じ Azure リージョン内の SQL Server のインスタンスに復元することができます。
- 宛先サーバーがソースと同じコンテナーに登録されている必要があります。
- サーバー上で複数のインスタンスを実行している場合は、すべてのインスタンスが稼働している必要があります。 そうでない場合、データベースを復元する対象サーバーの一覧にサーバーが表示されません。 詳細については、[トラブルシューティングの手順](backup-sql-server-azure-troubleshoot.md#faulty-instance-in-a-vm-with-multiple-sql-server-instances)を参照してください。
- TDE で暗号化されたデータベースを別の SQL Server に復元するには、まず[証明書を宛先サーバーに復元する](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)必要があります。
- [CDC](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server) 対応のデータベースは、[[ファイルとして復元]](#restore-as-files) オプションを使用して復元する必要があります。
- "マスター" データベースを復元する前に、スタートアップ オプション **-m AzureWorkloadBackup** を使用して、シングル ユーザー モードで SQL Server インスタンスを起動します。
  - **-m** の値はクライアントの名前です。
  - 指定されたクライアント名のみで接続を開くことができます。
- すべてのシステム データベース (モデル、マスター、msdb) の場合は、復元をトリガーする前に SQL Server Agent サービスを停止してください。
- これらのいずれかのデータベースへの接続を奪おうとする可能性があるアプリケーションはすべて閉じます。

## <a name="restore-a-database"></a>データベースを復元する

復元するには、次のアクセス許可が必要です。

- 復元を実行するコンテナー内の **バックアップ オペレーター** アクセス許可。
- バックアップされるソース VM への **共同作成者 (書き込み)** アクセス。
- ターゲット VM への **共同作成者 (書き込み)** アクセス。
  - 同じ VM に復元する場合は、これがソース VM になります。
  - 別の場所に復元する場合は、これが新しいターゲット VM になります。

次のように復元します。

1. Azure portal で、 **[バックアップ センター]** に移動し、 **[復元]** をクリックします。

   :::image type="content" source="./media/backup-azure-sql-database/backup-center-restore-inline.png" alt-text="復元プロセスの開始を示すスクリーンショット。" lightbox="./media/backup-azure-sql-database/backup-center-restore-expanded.png":::

1. [データソースの種類] として **[Azure VM の SQL]** を選択し、復元するデータベースを選択して **[続行]** をクリックします。

   :::image type="content" source="./media/backup-azure-sql-database/sql-restore.png" alt-text="[データソースの種類] の選択を示すスクリーンショット。":::

1. **[復元の構成]** で、データの復元先 (または復元方法) を指定します。
   
   - **別の場所**:別の場所にデータベースを復元し、元のソース データベースを保持します。
   - **DB の上書き**:元のソースと同じ SQL Server インスタンスにデータを復元します。 このオプションでは、元のデータベースが上書きされます。

        > [!IMPORTANT]
        > 選択したデータベースが Always On 可用性グループに属している場合、SQL Server はデータベースの上書きを許可しません。 **[別の場所]** のみを使用できます。
        >
   - **ファイルとして復元**:データベースとして復元するのではなく、バックアップ ファイルとして復元します。このファイルは、SQL Server Management Studio を使用して、ファイルが存在する任意のマシンに後からデータベースとして復元できます。

### <a name="restore-to-an-alternate-location"></a>別の場所に復元する

1. **[復元の構成]** メニューの **[復元先]** で、 **[別の場所]** を選択します。
1. データベースを復元する先の SQL Server の名前とインスタンスを選択します。
1. **[復元される DB 名]** ボックスに、ターゲット データベースの名前を入力します。
1. 該当する場合は、 **[選択した SQL インスタンスに既に同じ名前の DB が存在する場合に上書きする]** を選択します。
1. **[復元ポイント]** を選択し、[特定の時点に復元する](#restore-to-a-specific-point-in-time)のか、[特定の復旧ポイント](#restore-to-a-specific-restore-point)に復元するのかを選択します。

   :::image type="content" source="./media/backup-azure-sql-database/sql-alternate-location-recovery.png" alt-text="[復元ポイント] の選択を示すスクリーンショット。":::

   :::image type="content" source="./media/backup-azure-sql-database/restore-points-sql-inline.png" alt-text="特定の時点への復元を示すスクリーンショット。" lightbox="./media/backup-azure-sql-database/restore-points-sql-expanded.png":::

1. **[詳細な構成]** メニューで、次の手順を実行します。

    - 復元後にデータベースを非運用状態のままにする場合は、 **[NORECOVERY を使用して復元]** を有効にします。
    - 宛先サーバー上の復元場所を変更する場合は、新しいターゲット パスを入力します。

        ![ターゲット パスを入力する](./media/backup-azure-sql-database/target-paths.png)

1. **[OK]** を選択して復元をトリガーします。 **[通知]** 領域で復元の進行状況を追跡します。または、コンテナーの **[バックアップ ジョブ]** ビューでそれを追跡します。

    > [!NOTE]
    > 特定の時点への復元は、完全復旧モードと一括ログ復旧モードのデータベースのログ バックアップに対してのみ使用できます。

### <a name="restore-and-overwrite"></a>復元および上書き

1. **[復元の構成]** メニューの **[復元先]** で、 **[DB の上書き]**  > 、 **[OK]** の順に選択します。

    ![[DB の上書き] を選択する](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. **[復元ポイントの選択]** で、[特定の時点に復元する](#restore-to-a-specific-point-in-time) **[ログ (特定の時点)]** を選択します。 あるいは、 **[完全および差分]** を選択し、[特定の復旧ポイント](#restore-to-a-specific-restore-point)に復元します。

    > [!NOTE]
    > 特定の時点への復元は、完全復旧モードと一括ログ復旧モードのデータベースのログ バックアップに対してのみ使用できます。

### <a name="restore-as-files"></a>ファイルとして復元

バックアップ データをデータベースとしてではなく .bak ファイルとして復元するには、 **[ファイルとして復元]** を選択します。 指定されたパスにファイルがダンプされると、ファイルをデータベースとして復元したい任意のマシンにこれらのファイルを移すことができます。 これらのファイルを任意のマシンに移動できるので、サブスクリプションやリージョンをまたいでデータを復元できるようになりました。

1. **[復元する場所と方法]** で、 **[ファイルとして復元]** を選択します。
1. バックアップ ファイルを復元する先の SQL Server の名前を選択します。
1. **[サーバー上の宛先パス]** に、手順 2 で選択したサーバー上のフォルダー パスを入力します。 これは、必要なすべてのバックアップ ファイルをサービスがダンプする場所です。 通常、ネットワーク共有パスや、宛先パスとして指定されているマウントされた Azure ファイル共有のパスを使うと、同じネットワーク内の他のマシンや、それらにマウントされている同じ Azure ファイル共有でこれらのファイルに簡単にアクセスできます。<BR>

   >[!Note]
   >ターゲットとなる登録済み VM にマウントされている Azure ファイル共有でデータベース バックアップ ファイルを復元するには、NT AUTHORITY\SYSTEM でファイル共有にアクセスできることを確認します。 下の手順を行い、VM にマウントされている AFS に読み取り/書き込みアクセス許可を付与できます。
   >
   >- `PsExec -s cmd` を実行し、NT AUTHORITY\SYSTEM シェルに入ります
   >   - `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>` を実行します
   >   - `dir \\<storageacct>.file.core.windows.net\<filesharename>` でアクセスの有効性を検証します
   >- バックアップ コンテナーから `\\<storageacct>.file.core.windows.net\<filesharename>` (パス) へのファイルとしての復元を開始します<BR>
   >[[Sysinternals]](/sysinternals/downloads/psexec) ページから PsExec をダウンロードすることができます。

1. **[OK]** を選択します。

    ![[ファイルとして復元] を選択する](./media/backup-azure-sql-database/restore-as-files.png)

1. **[復元ポイント]** を選択し、[特定の時点に復元する](#restore-to-a-specific-point-in-time)のか、[特定の復旧ポイント](#restore-to-a-specific-restore-point)に復元するのかを選択します。

1. 選択した復旧ポイントに関連付けられているすべてのバックアップ ファイルが、この宛先パスにダンプされます。 このファイルは、SQL Server Management Studio を使用して、このファイルが存在する任意のマシンにデータベースとして復元できます。

    ![宛先パスにバックアップ ファイルを復元する](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>特定の時点に復元する

復元の種類として **[ログ (特定の時点)]** を選択した場合は、次の操作を行います。

1. **[復元の日付/時刻]** でカレンダーを開きます。 カレンダーでは、復旧ポイントが含まれる日付が太字で表示され、現在の日付が強調表示されます。
1. 復元ポイントのある日付を選択します。 復元ポイントのない日付は選択できません。

    ![カレンダーを開く](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. 日付を選択すると、タイムライン グラフに連続した範囲の使用可能な復旧ポイントが表示されます。
1. タイムライン グラフで復旧の時刻を指定するか、時刻を選択します。 **[OK]** をクリックします。

### <a name="restore-to-a-specific-restore-point"></a>特定の復元ポイントに復元する

復元の種類として **[完全および差分]** を選択した場合は、次の操作を行います。

1. 一覧から復旧ポイントを選択し、 **[OK]** を選択して復元ポイントの手順を完了します。

    ![完全復旧ポイントを選択する](./media/backup-azure-sql-database/choose-full-recovery-point.png)

    >[!NOTE]
    > 既定では、過去 30 日間の復旧ポイントが表示されます。 **[フィルター]** を選択して範囲をカスタマイズすると、30 日間より前の回復ポイントを表示できます。

### <a name="restore-databases-with-large-number-of-files"></a>多数のファイルがあるデータベースを復元する

データベース内のファイルの文字列サイズの合計が[特定の制限](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)より大きい場合、Azure Backup でデータベース ファイルの一覧が別の PIT コンポーネントに格納されるため、ユーザーは復元操作中にターゲット復元パスを設定できません。 代わりに、ファイルが SQL の既定のパスに復元されます。

  ![大きなファイルがあるデータベースを復元する](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="cross-region-restore"></a>リージョンをまたがる復元

復元オプションの 1 つである、リージョンをまたがる復元 (CRR) を使用すると、セカンダリ リージョン (Azure のペアになっているリージョン) で Azure VM 上でホストされている SQL データベースを復元できます。

この機能にオンボードするには、[開始前の注意に関するセクション](./backup-create-rs-vault.md#set-cross-region-restore)を参照してください。

CRR が有効になっているかどうかを確認するには、「[リージョンをまたがる復元の構成](backup-create-rs-vault.md#configure-cross-region-restore)」の手順に従ってください。

### <a name="view-backup-items-in-secondary-region"></a>セカンダリ リージョンのバックアップ項目を表示する

CRR が有効になっている場合は、セカンダリ リージョンのバックアップ項目を表示できます。

1. ポータルから **[Recovery Services コンテナー]** 、 **[バックアップ項目]** の順に移動します。
1. セカンダリ リージョンの項目を表示するには、 **[セカンダリ リージョン]** を選択します。

>[!NOTE]
>CRR 機能をサポートする種類のバックアップ管理のみが一覧に表示されます。 現時点では、セカンダリ リージョン データをセカンダリ リージョンに復元することのみが許可されています。

![セカンダリ リージョンのバックアップ項目](./media/backup-azure-sql-database/backup-items-secondary-region.png)

![セカンダリ リージョンのデータベース](./media/backup-azure-sql-database/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>セカンダリ リージョンに復元する

セカンダリ リージョンに復元するユーザー エクスペリエンスは、プライマリ リージョンに復元するユーザー エクスペリエンスに似ています。 [復元の構成] ペインで復元の詳細を構成するときに、セカンダリ リージョンのパラメーターのみを指定するように求められます。 コンテナーがセカンダリ リージョンに存在し、SQL サーバーがセカンダリ リージョンのコンテナーに登録されている必要があります。

![復元する場所と方法](./media/backup-azure-sql-database/restore-secondary-region.png)

![復元進行中通知をトリガーする](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>- 復元がトリガーされた後、データ転送フェーズでは、復元ジョブを取り消すことができません。
>- リージョン間で復元操作を実行するために必要なロールまたはアクセス レベルは、サブスクリプションの _バックアップ オペレーター_ ロール、ソースおよびターゲットの仮想マシンでの _共同作成者 (書き込み)_ アクセスです。 バックアップ ジョブを表示するため、サブスクリプションで必要な最小限の権限は _バックアップ閲覧者_ です。

### <a name="monitoring-secondary-region-restore-jobs"></a>セカンダリ リージョンの復元ジョブの監視

1. Azure portal で、 **[バックアップ センター]**  >  **[バックアップ ジョブ]** に移動します。
1. **CrossRegionRestore** のフィルター操作を実行して、セカンダリ リージョンのジョブを表示します。

   :::image type="content" source="./media/backup-azure-sql-database/backup-center-jobs-inline.png" alt-text="フィルター処理されたバックアップ ジョブを示すスクリーンショット。" lightbox="./media/backup-azure-sql-database/backup-center-jobs-expanded.png":::

## <a name="next-steps"></a>次のステップ

Azure Backup によってバックアップされた SQL Server データベースを[管理および監視します](manage-monitor-sql-database-backup.md)。
