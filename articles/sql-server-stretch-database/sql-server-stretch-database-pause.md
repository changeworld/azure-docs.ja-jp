<properties
    pageTitle="Pause and resume data migration (Stretch Database) | Microsoft Azure"
    description="Learn how to pause or resume data migration to Azure."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/14/2016"
    ms.author="douglasl"/>


# <a name="pause-and-resume-data-migration-(stretch-database)"></a>Pause and resume data migration (Stretch Database)

To pause or resume data migration to Azure, select **Stretch** for a table in SQL Server Management Studio, and then select **Pause** to pause data migration or **Resume** to resume data migration. You can also use Transact\-SQL to pause or resume data migration.

Pause data migration on individual tables when you want to troubleshoot problems on the local server or to maximize the available network bandwidth.

## <a name="pause-data-migration"></a>Pause data migration

### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>Use SQL Server Management Studio to pause data migration

1.  In SQL Server Management Studio, in Object Explorer, select the Stretch\-enabled table for which you want to pause data migration.

2.  Right\-click and select **Stretch**, and then select **Pause**.

### <a name="use-transact\-sql-to-pause-data-migration"></a>Use Transact\-SQL to pause data migration
Run the following command.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>Resume data migration

### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>Use SQL Server Management Studio to resume data migration

1.  In SQL Server Management Studio, in Object Explorer, select the Stretch\-enabled table for which you want to resume data migration.

2.  Right\-click and select **Stretch**, and then select **Resume**.

### <a name="use-transact\-sql-to-resume-data-migration"></a>Use Transact\-SQL to resume data migration
Run the following command.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>Check whether migration is active or paused

### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>Use SQL Server Management Studio to check whether migration is active or paused
In SQL Server Management Studio, open **Stretch Database Monitor** and check the value of the **Migration State** column. For more info, see [Monitor and troubleshoot data migration](sql-server-stretch-database-monitor.md).

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>Use Transact-SQL to check whether migration is active or paused
Query the catalog view **sys.remote_data_archive_tables** and check the value of the **is_migration_paused** column. For more info, see [sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="see-also"></a>See also

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[Monitor and troubleshoot data migration](sql-server-stretch-database-monitor.md)



<!--HONumber=Oct16_HO2-->


