---
title: Azure データベース リソースを Azure Germany からグローバル Azure に移行する
description: この記事では、Azure データベース リソースの Azure Germany からグローバル Azure への移行に関する情報を提供します。
ms.topic: article
ms.date: 03/29/2021
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurepowershell
ms.openlocfilehash: 0843adb3060ca4ce8769b79eef3f05e043d4dc5d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130259601"
---
# <a name="migrate-database-resources-to-global-azure"></a>データベース リソースをグローバル Azure に移行する

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

この記事には、Azure データベース リソースの Azure Germany からグローバル Azure への移行に役立つ可能性のある情報が含まれています。

## <a name="sql-database"></a>SQL Database

移行されるデータベースをオンラインのままにしないで、小さい Azure SQL Database ワークロードを移行するには、エクスポート関数を使用して BACPAC ファイルを作成します。 BACPAC ファイルは、メタデータと SQL Server データベースからのデータを含む圧縮 (zip 形式) ファイルです。 BACPAC ファイルを作成した後では、(たとえば、AzCopy を使用して) ターゲット環境にファイルをコピーし、インポート関数を使用してデータベースを再構築することができます。 次の考慮事項に注意してください。

- エクスポートでトランザクション上の一貫性が保たれるようにするには、次のいずれかの条件に当てはまることを確認します。
  - エクスポート中に、書き込み操作は行われません。
  - ご利用の SQL データベースのトランザクション上一貫性のあるコピーからエクスポートします。
- Azure Blob ストレージにエクスポートする場合、BACPAC ファイルのサイズは最大 200 GB に制限されています。 これより大きな BACPAC ファイルについては、ローカル ストレージにエクスポートします。
- SQL Database からのエクスポート操作にかかる時間が 20 時間を超える場合、操作はキャンセルされる可能性があります。 パフォーマンスを向上する方法のヒントについては、次の記事を確認してください。

> [!NOTE]
> エクスポート中にサーバーの DNS 名が変更されるため、接続文字列はエクスポート操作の後に変更されます。

詳細情報:

- [BACPAC ファイルにデータベースをエクスポート](../azure-sql/database/database-export.md)する方法を学習してください。
- [BACPAC ファイルをデータベースにインポート](../azure-sql/database/database-import.md)する方法を学習してください。
- [Azure SQL Database のドキュメント](/azure/sql-database/)を確認してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="migrate-sql-database-using-active-geo-replication"></a>アクティブ geo レプリケーションを使用して SQL Database を移行する

データベースが BACPAC ファイルには大きすぎる場合、または最小限のダウンタイムでオンラインを維持しながらクラウド間で移行する場合は、Azure Germany からグローバル Azure へのアクティブ geo レプリケーションを構成できます。

> [!IMPORTANT]
> データベースをグローバル Azure に移行するためのアクティブ geo レプリケーションの構成は、Transact-SQL (T-SQL) を使用する場合にのみサポートされており、移行する前に、サブスクリプションによるグローバル Azure への移行のサポートの有効化を要求する必要があります。 要求を送信するには、[このサポート リクエスト リンク](#requesting-access)を使用する必要があります。 

> [!Note]
> Azure Germany クラウドでアクティブ geo レプリケーションがサポートされている Azure グローバル クラウド リージョンは、ドイツ中西部とドイツ北部です。 最終的なデータベースの移行先として別のグローバル Azure リージョンが必要な場合は、グローバル Azure への移行が完了した後で、ドイツ中西部またはドイツ北部から必要な Azure グローバル クラウド リージョンへの追加の geo レプリケーション リンクを構成することをお勧めします。

アクティブ geo レプリケーションのコストの詳細については、「[Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/single/)」で **アクティブ geo レプリケーション** に関するセクションを参照してください。

アクティブ geo レプリケーションを使用してデータベースを移行するには、グローバル Azure に Azure SQL の論理サーバーが必要です。 サーバーはポータル、Azure PowerShell、Azure CLI などを使用して作成できますが、Azure Germany からグローバル Azure に移行するためのアクティブ geo レプリケーションの構成は、Transact-SQL (T-SQL) を使用する場合にのみサポートされます。

> [!IMPORTANT]
> クラウド間で移行する場合は、プライマリ (Azure Germany) とセカンダリ (グローバル Azure) のサーバー名プレフィックスが異なる必要があります。 サーバー名が同じ場合、ALTER DATABASE ステートメントの実行は成功しますが、移行は失敗します。 たとえば、プライマリ サーバー名のプレフィックスが `myserver` (`myserver.database.cloudapi.de`) の場合、グローバル Azure のセカンダリ サーバー名のプレフィックスを `myserver` にすることはできません。


`ALTER DATABASE` ステートメントを使用すると、ターゲット側で完全修飾 DNS サーバー名を使用することにより、グローバル Azure のターゲット サーバーを指定できます。 


```sql
ALTER DATABASE [sourcedb] add secondary on server [public-server.database.windows.net]
```


- *`sourcedb`* は、Azure Germany での Azure SQL サーバーのデータベース名を表します。 
- *`public-server.database.windows.net`* は、データベースを移行する必要があるグローバル Azure に存在する Azure SQL サーバー名を表します。 名前空間 "database.windows.net" が必要です。*public-server* は、グローバル Azure での論理 SQL サーバーの名前に置き換える必要があります。 グローバル Azure のサーバーには、Azure Germany のプライマリ サーバーとは異なる名前が必要です。


コマンドは、移行されるローカル データベースがホストされている Azure Germany サーバーの master データベースで実行されます。 
- パブリック クラウド サーバー内のログイン ユーザーの認証は、T-SQL start-copy API により、そのサーバーの master データベースで同じ SQL ログインとユーザー名を持つユーザーを検索されることによって行われます。 この方法はクラウドに依存しないため、クラウド間コピーを開始するには T-SQL API が使用されます。 このトピックのアクセス許可と詳細については、「[アクティブ geo レプリケーションの作成と使用](../azure-sql/database/active-geo-replication-overview.md)」および「[ALTER DATABASE (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql/)」を参照してください。
- グローバル Azure で Azure SQL 論理サーバーを示す最初の T-SQL コマンド拡張機能を除き、アクティブ geo レプリケーション プロセスの残りの部分は、ローカル クラウドでの既存の実行と同じです。 アクティブ geo レプリケーションを作成する詳細な手順については、「[アクティブ geo レプリケーションの作成と使用](../azure-sql/database/active-geo-replication-overview.md)」を参照してください。ただし、セカンダリ データベースがグローバル Azure に作成されるセカンダリ論理サーバーに作成される点は異なります。 
- セカンダリ データベースがグローバル Azure に (Azure Germany データベースのオンライン コピーとして) 存在するようになったら、ALTER DATABASE T-SQL コマンド (下の表を参照) を使用して、Azure Germany からグローバル Azure へのこのデータベースのフェールオーバーを開始できます。
- フェールオーバーの後、グローバル Azure でセカンダリがプライマリ データベースになったら、アクティブ geo レプリケーションを停止し、いつでも Azure Germany 側のセカンダリ データベースを削除できます (下の表と、図に示す手順を参照してください)。 
- Azure Germany のセカンダリ データベースには、フェールオーバーの後も削除するまで引き続きコストが発生します。
      
- Azure Germany データベースをグローバル Azure に移行するためのアクティブ geo レプリケーションを設定するには、`ALTER DATABASE` コマンドを使用するのが唯一の方法です。 
- Azure portal、Azure Resource Manager、PowerShell、または CLI を使用して、この移行用にアクティブ geo レプリケーションを構成することはできません。 

データベースを Azure Germany からグローバル Azure に移行するには:   

1.  Azure Germany でユーザー データベースを選択します (例: `azuregermanydb`)
2.  グローバル Azure (パブリック クラウド) に論理サーバーを作成します (例: `globalazureserver`)。 その完全修飾ドメイン名 (FQDN) は `globalazureserver.database.windows.net` です。
3.  Azure Germany のサーバーでこの T-SQL コマンドを実行して、Azure Germany からグローバル Azure へのアクティブ geo レプリケーションを開始します。 完全修飾 DNS 名がパブリック サーバー `globalazureserver.database.windows.net` に使用されていることに注意してください。 これは、ターゲット サーバーが Azure Germany ではなくグローバル Azure 内であることを示すためです。

    ```sql
    ALTER DATABASE [azuregermanydb] ADD SECONDARY ON SERVER [globalazureserver.database.windows.net];
    ```

4.  レプリケーションで読み書きワークロードをグローバル Azure サーバーに移動する準備ができたら、グローバル Azure サーバーでこの T-SQL コマンドを実行して、グローバル Azure への計画フェールオーバーを開始します。

    ```sql
    ALTER DATABASE [azuregermanydb] FAILOVER;
    ```

5.  アクティブ geo レプリケーション リンクは、フェールオーバー プロセスの前でも後でも終了できます。 計画フェールオーバーの後で次の T-SQL コマンドを実行すると、グローバル Azure 内のデータベースとの読み書きコピーである geo レプリケーション リンクが削除されます。 これは、現在の geo プライマリ データベースの論理サーバー上 (つまり、グローバル Azure サーバー上) で実行する必要があります。 これによって移行プロセスが完了します。

    ```sql
    ALTER DATABASE [azuregermanydb] REMOVE SECONDARY ON SERVER [azuregermanyserver];
    ```

    計画フェールオーバーの前に次の T-SQL コマンドを実行しても移行プロセスは停止しますが、この状況では、Azure Germany のデータベースは読み書きコピーのまま残ります。 この T-SQL コマンドも、現在の geo プライマリ データベースの論理サーバー上 (この場合は Azure Germany サーバー上) で実行する必要があります。

    ```sql
    ALTER DATABASE [azuregermanydb] REMOVE SECONDARY ON SERVER [globalazureserver];
    ```

Azure SQL データベースを Azure Germany からグローバル Azure に移行するこれらの手順は、アクティブ geo レプリケーションを使用して行うこともできます。


次の表では、フェールオーバーを管理するための T-SQL コマンドの詳細を示します。 次のコマンドは、Azure Germany とグローバル Azure の間のクラウド間のアクティブ geo レプリケーションについてサポートされます。 
 
|command |説明|
|:--|:--| 
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |ADD SECONDARY ON SERVER 引数を使用して、既存のデータベースのセカンダリ データベースを作成し、データ レプリケーションを開始します。| 
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |FAILOVER または FORCE_FAILOVER_ALLOW_DATA_LOSS を使用して、セカンダリ データベースをプライマリに切り替え、フェールオーバーを開始します  |
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |REMOVE SECONDARY ON SERVER を使用して、SQL Database と指定されたセカンダリ データベース間でのデータ レプリケーションを終了します。  |
 
### <a name="active-geo-replication-monitoring-system-views"></a>アクティブ geo レプリケーションの監視システム ビュー 
 
|command |説明|
|:--|:--| 
|[sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database?view=azuresqldb-current&preserve-view=true)|Azure SQL Database サーバー上の各データベースの、既存のレプリケーション リンクの情報をすべて返します。 |
|[sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database?view=azuresqldb-current&preserve-view=true) |最新のレプリケーション時刻、最後のレプリケーションの遅延、および指定された SQL データベースのレプリケーション リンクに関する他の情報を取得します。 |
|[sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?view=azuresqldb-current&preserve-view=true) | レプリケーション リンクの状態を含むすべてのデータベース操作の状態が表示されます。 | 
|[sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync?view=azuresqldb-current&preserve-view=true) | コミットされたすべてのトランザクションがレプリケートされ、アクティブ セカンダリ データベースによって認識されるまで、アプリケーションが待機状態になります。 |
 

## <a name="migrate-sql-database-long-term-retention-backups"></a>SQL Database の長期保有バックアップを移行する

geo レプリケーションまたは BACPAC ファイルを使用してデータベースを移行しても、Azure Germany のデータベースにある可能性がある長期保有バックアップはコピーされません。 既存の長期保有バックアップをターゲットのグローバル Azure リージョンに移行するには、COPY 長期保有バックアップ手順を使用できます。 

>[!Note]
>ここで説明する LTR バックアップ コピー方法で Azure Germany からグローバル Azure にコピーできるのは、LTR バックアップのみです。 これらの方法を使用した PITR バックアップのコピーはサポートされていません。 
>

### <a name="pre-requisites"></a>前提条件

1. LTR バックアップをコピーするグローバル Azure の ターゲット データベースは、バックアップのコピーを開始する前に存在している必要があります。 最初に[アクティブ geo レプリケーション](#migrate-sql-database-using-active-geo-replication)を使用してソース データベースを移行した後で、LTR バックアップのコピーを始めることをお勧めします。 これにより、データベース バックアップが正しいコピー先データベースに確実にコピーされます。 削除されたデータベースの LTR バックアップをコピーする場合、このステップは必要ありません。 削除されたデータベースの LTR バックアップをコピーすると、ターゲット リージョンにダミーの DatabaseID が作成されます。 
2. この [PowerShell Az モジュール](https://www.powershellgallery.com/packages/Az.Sql/3.0.0-preview)をインストールします
3. 始める前に、必要な [Azure RBAC ロール](../azure-sql/database/long-term-backup-retention-configure.md#prerequisites)が **サブスクリプション** または **リソース グループ** のスコープで付与されていることを確認します。 注: 削除されたサーバーに属する LTR バックアップにアクセスするには、そのサーバーのサブスクリプション スコープでアクセス許可を付与する必要があります。 . 


### <a name="limitations"></a>制限事項  

- フェールオーバー グループはサポートされません。 つまり、Azure Germany のデータベースを移行するお客様は、フェールオーバーの間に接続文字列を自分で管理する必要があります。
- Azure portal、Azure Resource Manager API、PowerShell、または CLI はサポートされていません。 つまり、Azure Germany の各移行では、T-SQL を使用してアクティブ geo レプリケーションのセットアップとフェールオーバーを管理する必要があります。
- お客様は、Azure Germany のデータベース用にグローバル Azure に複数の geo セカンダリを作成することはできません。
- geo セカンダリの作成は、Azure Germany リージョンから開始する必要があります。
- お客様は、グローバル Azure に対してだけ、Azure Germany のデータベースを移行できます。 現在、他のクラウド間移行はサポートされていません。 
- Azure Germany ユーザー データベースの Azure AD ユーザーは移行されますが、移行されたデータベースが存在する新しい Azure AD テナントでは使用できません。 これらのユーザーを有効にするには、ユーザーを手動で削除し、新しく移行されたデータベースが存在する新しい Azure AD テナントで使用できる現在の Azure AD ユーザーを使用して作成し直す必要があります。  


### <a name="copy-long-term-retention-backups-using-powershell"></a>PowerShell を使用して長期保有バックアップをコピーする

新しく導入された PowerShell コマンド **Copy-AzSqlDatabaseLongTermRetentionBackup** を使用すると、Azure Germany から Azure グローバル リージョンに長期保有バックアップをコピーできます。 

1. **バックアップの名前を使用して LTR バックアップをコピーする** 次の例では、バックアップ名を使用して Azure Germany から Azure グローバル リージョンに LTR バックアップをコピーする方法を示します。  

```powershell
# Source database and target database info
$location = "<location>"
$sourceRGName = "<source resourcegroup name>"
$sourceServerName = "<source server name>"
$sourceDatabaseName = "<source database name>"
$backupName = "<backup name>"
$targetDatabaseName = "<target database name>"
$targetSubscriptionId = "<target subscriptionID>"
$targetRGName = "<target resource group name>"
$targetServerFQDN = "<targetservername.database.windows.net>"

Copy-AzSqlDatabaseLongTermRetentionBackup 
    -Location $location 
    -ResourceGroupName $sourceRGName 
    -ServerName $sourceServerName 
    -DatabaseName $sourceDatabaseName
    -BackupName $backupName
    -TargetDatabaseName $targetDatabaseName 
    -TargetSubscriptionId $targetSubscriptionId
    -TargetResourceGroupName $targetRGName
    -TargetServerFullyQualifiedDomainName $targetServerFQDN 
```

2. **バックアップの resourceID を使用して LTR バックアップをコピーする** 次の例では、バックアップの resourceID を使用して Azure Germany から Azure グローバル リージョンに LTR バックアップをコピーする方法を示します。 この例は、削除されたデータベースのバックアップのコピーにも使用できます。 

```powershell
$location = "<location>"
# list LTR backups for All databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $location -DatabaseState All

# select the LTR backup you want to copy
$ltrBackup = $ltrBackups[0]
$resourceID = $ltrBackup.ResourceId

# Source Database and target database info
$targetDatabaseName = "<target database name>"
$targetSubscriptionId = "<target subscriptionID>"
$targetRGName = "<target resource group name>"
$targetServerFQDN = "<targetservername.database.windows.net>"

Copy-AzSqlDatabaseLongTermRetentionBackup 
    -ResourceId $resourceID 
    -TargetDatabaseName $targetDatabaseName 
    -TargetSubscriptionId $targetSubscriptionId
    -TargetResourceGroupName $targetRGName
    -TargetServerFullyQualifiedDomainName $targetServerFQDN
```


### <a name="limitations"></a>制限事項  

- [ポイントインタイム リストア (PITR)](../azure-sql/database/recovery-using-backups.md#point-in-time-restore) バックアップは、プライマリ データベースでのみ作成されます。これは設計によるものです。 Geo-DR を使用して Azure Germany からデータベースを移行すると、フェールオーバー後に新しいプライマリで PITR バックアップが開始されます。 ただし、既存の PITR バックアップ (Azure Germany での前のプライマリ上) は移行されません。 PITR バックアップでポイントインタイム リストアのシナリオをサポートする必要がある場合は、Azure Germany で PITR バックアップからデータベースを復元した後、回復されたデータベースをグローバル Azure に移行する必要があります。 
- 長期保有ポリシーは、データベースと一緒に移行されません。 Azure Germany のデータベースに[長期保有 (LTR)](../azure-sql/database/long-term-retention-overview.md) ポリシーがある場合は、LTR ポリシーを手動でコピーし、移行後に新しいデータベースで作成し直す必要があります。 


### <a name="requesting-access"></a>アクセス権の要求

geo レプリケーションを使用して Azure Germany からグローバル Azure にデータベースを移行するには、クラウド間の移行を正常に構成するために *Azure Germany* のサブスクリプションを有効にする必要があります。

Azure Germany のサブスクリプションを有効にするには、次のリンクを使用して移行サポート リクエストを作成する必要があります。   

1. 次の[移行サポート リクエスト](https://portal.microsoftazure.de/#create/Microsoft.Support/Parameters/%7B%0D%0A++++%22pesId%22%3A+%22f3dc5421-79ef-1efa-41a5-42bf3cbb52c6%22%2C%0D%0A++++%22supportTopicId%22%3A+%229fc72ed5-805f-3894-eb2b-b1f1f6557d2d%22%2C%0D%0A++++%22contextInfo%22%3A+%22Migration+from+cloud+Germany+to+Azure+global+cloud+%28Azure+SQL+Database%29%22%2C%0D%0A++++%22caller%22%3A+%22NoSupportPlanCloudGermanyMigration%22%2C%0D%0A++++%22severity%22%3A+%223%22%0D%0A%7D)を参照してください。

2. [基本] タブで、 **[概要]** に「*Geo-DR migration*」と入力してから、 **[次へ: 解決策]** を選択します
 
   :::image type="content" source="media/germany-migration-databases/support-request-basics.png" alt-text="新しいサポート リクエスト フォーム":::

3. **[推奨される手順]** を確認してから、 **[次: 詳細]** を選択します。 

   :::image type="content" source="media/germany-migration-databases/support-request-solutions.png" alt-text="必要なサポート リクエスト情報":::

4. 詳細ページで、以下を設定します。

   1. [説明] ボックスに、移行先のグローバル Azure サブスクリプション ID を入力します。 データベースを複数のサブスクリプションに移行するには、データベースを移行する先のグローバル Azure ID のリストを追加します。
   1. 連絡先情報 (名前、会社名、メール、電話番号) を指定します。
   1. フォームを完成してから、 **[次: 確認および作成]** を選択します。

   :::image type="content" source="media/germany-migration-databases/support-request-details.png" alt-text="サポート リクエストの詳細":::


5. サポート リクエストを確認して、 **[作成]** を選択します。 


要求が処理されると、連絡を受け取ります。



## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB データ移行ツールを使用して Azure Cosmos DB にデータを移行することができます。 Azure Cosmos DB データ移行ツールは、JSON ファイル、MongoDB、SQL Server、CSV ファイル、Azure Table Storage、Amazon DynamoDB、HBase、Azure Cosmos コンテナーなど、さまざまなソースから Azure Cosmos DB にデータをインポートするオープンソース ソリューションです。


Azure Cosmos DB データ移行ツールは、グラフィカル インターフェイス ツールまたはコマンド ライン ツールとして使用できます。 ソース コードは、[Azure Cosmos DB データ移行ツール](https://github.com/azure/azure-documentdb-datamigrationtool) GitHub リポジトリで入手できます。 [ツールのコンパイル済みバージョン](https://www.microsoft.com/download/details.aspx?id=46436)は、Microsoft ダウンロード センターで入手できます。

Azure Cosmos DB リソースを移行するには、次の手順を完了することをお勧めします。

1. アプリケーションのアップタイム要件およびアカウント構成を確認して、最適なアクション プランを決定します。
1. データ移行ツールを実行して、Azure Germany から新しいリージョンにアカウント構成を複製します。
1. メンテナンス ウィンドウを使用できる場合は、データ移行ツールを実行することによって、ソースから宛先にデータをコピーします。
1. メンテナンス ウィンドウを使用できない場合は、ツールを実行してソースから宛先にデータをコピーしてから、次の手順を完了します。
   1. 構成主導のアプローチを使用して、アプリケーションでの読み取り/書き込みに変更を加えます。
   1. 初回の同期を完了します。
   1. 増分同期を設定し、変更フィードを反映します。
   1. 新しいアカウントの読み取りを指示し、アプリケーションを確認します。
   1. 古いアカウントへの書き込みを停止し、変更フィードが反映されていることを確認してから、新しいアカウントに対する書き込みを指示します。
   1. このツールを停止し、古いアカウントを削除します。
1. 新旧のアカウント間でデータが一貫性のあることを検証するツールを実行します。

詳細情報:

- データ移行ツールを使用する方法については、「[チュートリアル: データ移行ツールを使用して Azure Cosmos DB にデータを移行する](../cosmos-db/import-data.md)」を参照してください。
- Cosmos DB については、「[Azure Cosmos DB の概要](../cosmos-db/introduction.md)」を参照してください。


## <a name="azure-cache-for-redis"></a>Azure Cache for Redis

Azure Cache for Redis インスタンスを Azure Germany からグローバル Azure に移行する場合には、いくつかのオプションがあります。 選択するオプションは、要件によって異なります。

### <a name="option-1-accept-data-loss-create-a-new-instance"></a>オプション 1: データ損失を受け入れ、新しいインスタンスを作成する

次の両方の条件が該当する場合、このアプローチは最も道理にかなっています。

- 一時的なデータ キャッシュとして Azure Cache for Redis を使用している。
- アプリケーションが新しいリージョンにキャッシュ データを自動的に再取り込みする。

データ損失とともに移行し、新しいインスタンスを作成するには、次のようにします。

1. 新しいターゲット リージョンに新しい Azure Cache for Redis インスタンスを作成します。
1. 新しいリージョンの新しいインスタンスを使用するようにアプリケーションを更新します。
1. ソース リージョンで古い Azure Cache for Redis インスタンスを削除します。

### <a name="option-2-copy-data-from-the-source-instance-to-the-target-instance"></a>オプション 2: ソース インスタンスからターゲット インスタンスにデータをコピーする

Azure Cache for Redis チームのメンバーは、Azure Cache for Redis のあるインスタンスから別のインスタンスにインポートまたはエクスポート機能を必要とせずデータをコピーする、オープン ソース ツールを作成しました。 そのツールについては、以下の手順の手順 4 を参照してください。

対象インスタンスにソース インスタンスからデータをコピーするには、次のようにします。

1. ソース リージョンに VM を作成します。 Azure Cache for Redis のデータセットが大きい場合は、コピー時間を最小限にするために、必ず比較的強力な VM サイズを選択するようにします。
1. ターゲット リージョンに新しい Azure Cache for Redis インスタンスを作成します。
1. **ターゲット** インスタンスからデータをフラッシュします。 (**ソース** インスタンスからはフラッシュ *しないでください*。 フラッシュが必要なのは、コピー ツールがターゲットの場所の既存のキーを *上書きしない* からです)。
1. ソースの Azure Cache for Redis インスタンスからターゲットの Azure Cache for Redis インスタンスにデータを自動的にコピーするには、次のツールを使用します: [ツール ソース](https://github.com/deepakverma/redis-copy)と[ツール ダウンロード](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip)。

> [!NOTE]
> このプロセスは、データセットのサイズに応じて長時間かかる場合があります。

### <a name="option-3-export-from-the-source-instance-import-to-the-destination-instance"></a>オプション 3: ソース インスタンスからエクスポートして、ターゲット インスタンスにインポートする

このアプローチでは、Premium レベルでのみ利用できる機能を利用します。

ソース インスタンスからエクスポートし、宛先インスタンスにインポートするには、次のようにします。

1. ターゲット リージョンに、新しい Premium レベルの Azure Cache for Redis を作成します。 ソースの Azure Cache for Redis インスタンスと同じサイズを使用します。
1. [ソースのキャッシュからデータをエクスポート](../azure-cache-for-redis/cache-how-to-import-export-data.md)するか、または [Export-AzRedisCache PowerShell コマンドレット](/powershell/module/az.rediscache/export-azrediscache)を使用します。

   > [!NOTE]
   > エクスポート Azure Storage アカウントは、キャッシュ インスタンスと同じリージョンに存在する必要があります。

1. エクスポートされた BLOB を宛先リージョンのストレージ アカウントに (たとえば AzCopy を使用して) コピーします。
1. [宛先キャッシュにデータをインポート](../azure-cache-for-redis/cache-how-to-import-export-data.md)するか、または [Import-AzRedisCAche PowerShell コマンドレット](/powershell/module/az.rediscache/import-azrediscache)を使用します。
1. ターゲットの Azure Cache for Redis を使用するように、アプリケーションを再構成します。

### <a name="option-4-write-data-to-two-azure-cache-for-redis-instances-read-from-one-instance"></a>オプション 4: データを 2 つの Azure Cache for Redis インスタンスに書き込み、1 つのインスタンスから読み取る

このアプローチの場合、アプリケーションを変更する必要があります。 アプリケーションは、いずれかのキャッシュ インスタンスからの読み取り中に、複数のキャッシュ インスタンスにデータを書き込む必要があります。 このアプローチは、Azure Cache for Redis に格納されたデータが次の条件を満たしている場合に有効です。
- データは定期的に更新されます。 
- すべてのデータがターゲットの Azure Cache for Redis に書き込まれる。
- すべてのデータを更新するのに十分な時間がある。

詳細情報:

- 「[Azure Cache for Redis の概要](../azure-cache-for-redis/cache-overview.md)」を確認してください。

## <a name="postgresql-and-mysql"></a>PostgreSQL と MySQL

詳細については、[PostgreSQL](../postgresql/index.yml) および [MySQL](../mysql/index.yml) のデータのバックアップと移行に関するセクションの記事を参照してください。

![PostgreSQL と MySQL](./media/germany-migration-main/databases.png)

## <a name="next-steps"></a>次のステップ

次のサービス カテゴリのリソースを移行するためのツール、テクニック、および推奨事項について学習します。

- [Compute](./germany-migration-compute.md)
- [ネットワーク](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [統合](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [Security](./germany-migration-security.md)
- [管理ツール](./germany-migration-management-tools.md)
- [メディア](./germany-migration-media.md)
