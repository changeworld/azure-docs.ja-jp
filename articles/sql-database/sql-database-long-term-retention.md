---
title: "最大で 10 年間の Azure SQL Database のバックアップを格納する | Microsoft Docs"
description: "Azure SQL Database が最大で 10 年間バックアップの格納をサポートする方法について説明します。"
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 66fdb8b8-5903-4d3a-802e-af08d204566e
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: carlrab; sashan
translationtype: Human Translation
ms.sourcegitcommit: 92a2cca71380ac609e900d223908eda1a40be635
ms.openlocfilehash: 17de9fd999b904b66c4e9d574fb4754069ae133a


---
# <a name="storing-azure-sql-database-backups-for-up-to-10-years"></a>最大で 10 年間の Azure SQL Database のバックアップを格納する
多くのアプリケーションには規制、コンプライアンス、またはその他のビジネス上の目的があり、SQL Database の[自動バックアップ](sql-database-automated-backups.md)によって提供される、7 - 35 日間を経過した完全なデータベースの自動バックアップを保持する必要があります。

**バックアップの長期保存**機能では、最大で 10 年間の Azure SQL Database のバックアップを Azure Recovery Services の資格情報コンテナーに保存できます。 資格情報コンテナーごとに最大 1000 個のデータベースを格納できます。 新しいデータベースとして復元する任意のバックアップを資格情報コンテナーで選択できます。

> [!NOTE]
> 資格情報コンテナーあたり 24 時間で最大 200 個のデータベースを有効にできます。 したがって、この制限の影響を最小限に抑えるため、サーバーごとに個別の資格情報コンテナーを使用することをお勧めします。 
> 
> 

## <a name="how-does-sql-database-long-term-retention-work"></a>SQL Database の長期保存はどのように機能しますか。

バックアップの長期保存を使用すると、Azure SQL Database サーバーを Azure Recovery Services の資格情報コンテナーに関連付けられます。 

* 資格情報コンテナーは、SQL Server を作成した Azure サブスクリプションで、同じ地理的領域およびリソース グループで作成する必要があります。 
* その後、任意のデータベースの保持ポリシーを構成します。 このポリシーにより、週単位の完全データベース バックアップが Recovery Services コンテナーにコピーされ、指定された保有期間 (最大で 10 年間) 保持されます。 
* サブスクリプション内のあらゆるサーバーの新しいデータベースに、これらのバックアップのいずれかから復元することができます。 コピーは、Azure ストレージによって既存のバックアップから実行され、既存のデータベースのパフォーマンスには影響を与えません。

## <a name="how-do-i-enable-long-term-retention"></a>長期保存を有効にするにはどうすればよいですか。

データベースのバックアップの長期保存を構成するには、次の手順を実行します。

1. SQL Database サーバーと同じリージョン、サブスクリプション、およびリソース グループで Azure Recovery Services コンテナーを作成します。 
2. サーバーを資格情報コンテナーに登録します。
3. Azure Recovery Services の保護ポリシーを作成します。
4. 長期的なバックアップの保有を必要とするデータベースに、保護ポリシーを適用します。

## <a name="how-do-i-restore-a-database-stored-with-the-long-term-retention-feature"></a>長期保存機能と共に格納されているデータベースはどうやって復元できますか。

長期保存のバックアップから回復するには、次の手順を実行します。

1. バックアップが格納されている資格情報コンテナーを一覧表示します
2. 論理サーバーにマップされる資格情報コンテナーを一覧表示します
3. データベースにマップされている資格情報コンテナー内のデータ ソースを一覧表示します
4. 復元に使用できる回復ポイントを一覧表示します
5. サブスクリプション内で、回復ポイントからターゲット サーバーに復元します

## <a name="how-much-does-long-term-retention-cost"></a>長期保存にはどのくらいコストがかかりますか。

Azure SQL Database の長期保存は、[Azure バックアップ サービスの価格レート](https://azure.microsoft.com/pricing/details/backup/)に従って課金されます。

Azure SQL Database サーバーを資格情報コンテナーに登録した後は、資格情報コンテナーに格納されている週単位のバックアップで使用されている合計ストレージに対して課金されます。

## <a name="configuring-long-term-retention-in-the-azure-portal"></a>Azure Portal での長期保存の構成

Azure SQL Database サーバー ブレードで、長期保存を構成し、必要に応じて、Azure Recovery Services コンテナーを作成できます。

## <a name="configuring-long-term-retention-using-powershell"></a>PowerShell を使用した長期保存の構成

次の手順を使用して、PowerShell を使用した長期保存を構成できます。
1. Recovery Services コンテナーを作成する
   
   ```
   New-AzureRmResourceGroup -Name $ResourceGroupName –Location 'WestUS' 
   $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName $ResourceGroupName -Location 'WestUS' 
   Set-AzureRmRecoveryServicesBackupProperties   -BackupStorageRedundancy LocallyRedundant  -Vault $vault
   ```
2. 資格情報コンテナーに Azure SQL Database サーバーを登録して、サーバー内のデータベースのバックアップが長期間保存されるようにできます。
   
   ```
   Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName 'RG1' -ServerName 'Server1' –ResourceId $vault.Id
   ```
3. バックアップを格納するための保持ポリシーを作成します。
   
   ```
   #retrieve the default in-memory policy object for AzureSQLServer workload and set the retention period
   $RP1 = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase
   #Sets the retention value to two years
   $RP1.RetentionDurationType='Years'
   $RP1.RetentionCount=2
   #register the policy for use with any SQL database
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name 'SQLBackup1' –WorkloadType AzureSQLDatabase -retentionPolicy $RP1
   ```
4. 資格情報コンテナーにバックアップを格納する SQL Database の長期保存を有効にします。
   
   ```
   #for your database you can select any policy created in the vault with which your server is registered
   Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName 'RG1' –ServerName 'Server1' -DatabaseName 'DB1' -State 'enabled' -ResourceId $policy.Id
   ```
5. 資格情報コンテナーに関連付けられているサーバーを一覧表示します。 サーバーはそれぞれ、資格情報コンテナー内の特定のコンテナーに関連付けられます。 次のコマンドを実行して、登録済みサーバーを一覧表示できます。
   
   ```
   #each server has an associated container in the vault
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL   
   #each database has an associated backup item in the respective container
   Get-AzureRmRecoveryServicesBackupItem –container $container
   ```
6. コンテナー内に保持ポリシーがあるデータベースを一覧表示します。 各データベースには、関連付けられているバックアップ項目がそれぞれのコンテナーにあります。 バックアップ項目の名前は、データベース名から派生しています。
   
    ```
    #list the backup items in the container
    Get-AzureRmRecoveryServicesBackupItem –container $container
    ```

## <a name="restore-from-a-long-term-retention-backup"></a>長期保存のバックアップから復元する

Azure Recovery Service コンテナーのバックアップからデータベースを復元するには、次の手順を使用します。

1. SQL Server と関連付けられている Recovery Service コンテナーを検索します。
   
   ```
   #the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -Name 'Sql;myresourcegroup;myserver'
   ```
2. データベースに関連付けられているバックアップ項目を見つけます。
   
    ``` 
    #the following command finds the backup item associated with the database 'mydb'
    $item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name 'mydb' 
    ```
3. データベースを復元する元となるデータベースを見つけます。
   
   ```
   #The following command lists the backups (also known as the “recovery points”) created in the specific time period.
   $RP=Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item –StartDate '2016-02-01' -EndDate '2016-02-20'
   ```
4. 復旧ポイントから新しい Azure SQL Database に復元します。
   
   ```
   #This command restores from a selected backup. If there are multiple recovery points in the specified range $RP[0] refers to the first one.
   Restore-AzureRMSqlDatabase –FromLongTermRetentionBackup –ResourceId $RP[0].ID TargetResourceGroupName 'RG2' -TargetServerName 'Server2' -TargetDatabaseName 'DB2' [-Edition <String>] [-ServiceObjectiveName <String>] [-ElasticPoolName <String>] [<CommonParameters>]
   ```

## <a name="disabling-long-term-retention"></a>長期保存の無効化

Recovery Service は、指定した保持ポリシーに基づいてバックアップのクリーンアップを自動的に処理します。 

* 特定のデータベースのバックアップの資格情報コンテナーへの送信を停止するには、そのデータベースの保持ポリシーを削除します。
  
    ```
    Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName 'RG1' –ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
    ```

> [!NOTE]
> 既に資格情報コンテナーにあるバックアップは影響を受けません。 保有期間を超過すると、これらのデータベースはRecovery Service によって自動的に削除されます。
> 
> 

## <a name="removing-backups-from-the-azure-recovery-services-vault"></a>Azure Recovery Services コンテナーからのバックアップの削除

資格情報コンテナーからバックアップを手動で削除するには、次の手順に従います。

1. 資格情報コンテナー内で 'myserver' のコンテナーを見つけます。
   
    ```
    Set-AzureRMRecoveryServicesVaultContext -Vault $vault 
    $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -FriendlyName 'myserver'
    ```
2. 削除するバックアップ項目を特定します。
   
    ``` 
    $item=Get-AzureRmRecoveryServicesBackupItem –container $container -Name 'mydb'
    ```
3. バックアップ項目 (データベース 'mydb' のすべてのバックアップ) を削除します。
   
    ```
    $job = Disable-AzureRmRecoveryServicesBackupProtection –item $item -Removerecoverypoints 
    Wait-AzureRmRecoveryServicesBackupJob $job
    ```
4. 'myserver' に関連付けられているコンテナーを削除します。
   
    ```
    Unregister-AzureRmRecoveryServicesBackupContainer –Container $container $container
    ```

## <a name="long-term-retention-faq"></a>長期保存に関する FAQ:

1. Q: 資格情報コンテナー内の特定のバックアップを手動で削除することはできますか。
   A: 現時点ではできません。資格情報コンテナーは、保有期間を超過したバックアップを自動的にクリーンアップします。
2. Q: 複数の資格情報コンテナーにバックアップを保存するためにサーバーを登録することはできますか。
   A: いいえ。現時点では、一度にバックアップを格納できるのは 1 つの資格情報コンテナーのみです。
3. Q: 資格情報コンテナーとサーバーを異なるサブスクリプションに配置することはできますか。
   A: いいえ。現時点では、資格情報コンテナーとサーバーは同じサブスクリプションとリソース グループに配置する必要があります。
4. Q: サーバーのリージョンとは異なるリージョンで作成した資格情報コンテナーを使用することはできますか。
   A: いいえ。コピーにかかる時間を最小限にしてトラフィック料金が発生しないようにするため、資格情報コンテナーとサーバーのリージョンは同じである必要があります。
5. Q: 1 つの資格情報コンテナーにはいくつのデータベースを格納できますか。
   A: 現時点では、資格情報コンテナーあたり最大 1000 個のデータベースがサポートされています。 
6. Q. サブスクリプションごとに作成できる資格情報コンテナーはいくつですか。A: サブスクリプションあたり最大 25 個のコンテナーを作成できます。
7. Q. 資格情報コンテナーごとに、1 日あたりいくつのデータベースを構成できますか。A: 資格情報コンテナーごとに 設定できるデータベースは 1 日あたり 200 個のみです。
8. Q: 長期保存は、エラスティック データベース プールと連携しますか。
   A: はい。 プール内のどのデータベースも保持ポリシーで構成できます。
9. Q: バックアップが作成される時刻を選択できますか。
   A: いいえ。SQL Database は、データベースのパフォーマンスに与える影響を最小限に抑えるために、バックアップ スケジュールを制御します。
10. Q: データベースに対して TDE が有効化されています。 これを資格情報コンテナーから復元することはできますか。A: はい。TDE はサポートされています。 元のデータベースが存在しない場合でも、資格情報コンテナーからデータベースを復元できます。
11. Q. サブスクリプションを中断した場合、資格情報コンテナー内のバックアップはどうなりますか。A: サブスクリプションが中断されている場合、既存のデータベースとバックアップは保持されますが、新しいバックアップは資格情報コンテナーにコピーされません。 サブスクリプションを再有効化すると、資格情報コンテナーへのバックアップのコピーが再開されます。 資格情報コンテナーは、サブスクリプションが中断される前に資格情報コンテナーにコピーされていたバックアップを使用して、復元操作でアクセス可能になります。 
12. Q: SQL Server からダウンロード、または SQL Server に復元するため、SQL Database のバックアップ ファイルにアクセスすることはできますか。
   A: 現時点ではできません。
13. Q: SQL の保存ポリシー内で複数のスケジュール (日単位、週単位、月単位、年単位) を設定することはできますか。
   A: いいえ。現時点では、これは仮想マシンのバックアップでのみ可能です。

## <a name="next-steps"></a>次のステップ
データの不慮の破損または削除から保護するデータベース バックアップは、ビジネス継続性および障害復旧戦略の最も重要な部分です。 その他の Azure SQL Database ビジネス継続性ソリューションの概要については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。




<!--HONumber=Nov16_HO4-->


