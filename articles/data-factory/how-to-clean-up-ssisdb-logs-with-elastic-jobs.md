---
title: SSISDB ログを自動的にクリーンアップする方法
description: この記事では、Azure Data Factory、Azure SQL Managed Instance エージェント、Elastic Database ジョブを使用して、関連する SSISDB ストアド プロシージャを自動的に呼び出すことで、SSISDB 内に格納されている SSIS プロジェクトのデプロイ ログとパッケージ実行ログをクリーンアップする方法について説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 78b6c754fe2b49ceabf940aa76b86ddd90dfc91e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843660"
---
# <a name="how-to-clean-up-ssisdb-logs-automatically"></a>SSISDB ログを自動的にクリーンアップする方法

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory (ADF) 内で Azure-SQL Server Integration Services (SSIS) 統合ランタイム (IR) をプロビジョニングしたら、それを使用して、次の場所にデプロイされた SSIS パッケージを実行できます。

- Azure SQL Database サーバー/Managed Instance をホストとする SSIS カタログ (SSISDB) (プロジェクト デプロイ モデル)
- Azure SQL Managed Instance をホストとするファイル システム、Azure Files、SQL Server データベース (MSDB) (パッケージ デプロイ モデル)

プロジェクト デプロイ モデル内で、お使いの Azure-SSIS IR によって SSIS プロジェクトが SSISDB にデプロイされ、SSIS パッケージがフェッチされて SSISDB から実行されます。また、パッケージ実行ログが SSISDB に書き戻されます。 SSISDB は、SSIS ジョブと IR 操作のログの格納にも使用されます。 蓄積されたログを管理するために、関連する SSISDB プロパティとストアド プロシージャが提供され、ADF、Azure SQL Managed Instance エージェント、Elastic Database ジョブを使用して、スケジュールに基づいて自動的に呼び出すことができます。

## <a name="ssisdb-log-clean-up-properties-and-stored-procedures"></a>SSISDB ログのクリーンアップ プロパティとストアド プロシージャ
SSIS パッケージ実行ログを管理するために、SQL Server Management Studio (SSMS) を使用して Azure SQL Database サーバー/Managed Instance をホストとする SSISDB に接続し、SSISDB のログのクリーンアップ プロパティを構成できます。[SSISDB への接続](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial?view=sql-server-ver15&preserve-view=true#connect-to-the-ssisdb-database)に関するページをご覧ください。 接続されたら、SSMS の **オブジェクト エクスプローラー** ウィンドウ上で、**Integration Services カタログ** ノードを展開して **[SSISDB]** サブノードを右クリックし、 **[プロパティ]** メニュー項目を選択すると、 **[カタログのプロパティ]** ダイアログ ボックスが開きます。 **[カタログのプロパティ]** ダイアログ ボックスには、次の SSISDB ログのクリーンアップ プロパティがあります。

- **定期的にログを消去**: パッケージ実行ログのクリーンアップを有効にします。既定では *True* に設定されています。
- **保持期間 (日数)** : 保持ログの最大期間 (日数) を指定します。既定では *365* に設定され、関連する SSISDB ストアド プロシージャが呼び出されると、古いログが削除されます。
- **古いバージョンを定期的に削除**: 格納されているプロジェクト バージョンのクリーンアップを有効にします。既定では *True* に設定されています。
- **プロジェクトあたりのバージョンの最大数**: 格納されているプロジェクト バージョンの最大数を指定します。既定では *10* に設定され、関連する SSISDB ストアド プロシージャが呼び出されると、古いバージョンが削除されます。

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/clean-up-logs-ssms-ssisdb-properties.png" alt-text="SSISDB ログのクリーンアップ プロパティ":::

SSISDB ログのクリーンアップ プロパティが構成されたら、関連する SSISDB ストアド プロシージャ `[internal].[cleanup_server_retention_window_exclusive]` を呼び出して、SSIS パッケージ実行ログをクリーンアップできます。

SSIS ジョブ ログをクリーンアップするには、関連する SSISDB ストアド プロシージャ `[internal].[cleanup_completed_jobs_exclusive]` を呼び出します。 保持期間は既定では 60 分に設定され、ストアド プロシージャが呼び出されると、古いログが削除されます。

SSIS IR 操作ログをクリーンアップするには、関連する SSISDB ストアド プロシージャ `[internal].[cleanup_expired_worker]` を呼び出します。 保持期間は既定では 168 時間に設定され、ストアド プロシージャが呼び出されると、古いログが削除されます。

これらの SSISDB ストアド プロシージャによって、さまざまな SSISDB テーブルがクリーンアップされます。

| SSISDB ストアド プロシージャ | クリーンアップする SSISDB テーブル |
|--------------------------|---------------------------|
| `[internal].[cleanup_server_retention_window_exclusive]` | `[internal].[event_message_context_scaleout]`<br/>`[internal].[event_messages_scaleout]`<br/>`[internal].[executable_statistics]`<br/>`[internal].[execution_component_phases]`<br/>`[internal].[execution_data_statistics]`<br/>`[internal].[execution_data_taps]`<br/>`[internal].[execution_parameter_values]`<br/>`[internal].[execution_parameter_values_noncatalog]`<br/>`[internal].[execution_property_override_values]`<br/>`[internal].[execution_property_override_values_noncatalog]`<br/>`[internal].[executions]`<br/>`[internal].[executions_noncatalog]`<br/>`[internal].[extended_operation_info]`<br/>`[internal].[operation_messages]`<br/>`[internal].[operation_messages_scaleout]`<br/>`[internal].[operation_permissions]`<br/>`[internal].[operations]`<br/>`[internal].[validations]` |
| `[internal].[cleanup_completed_jobs_exclusive]` | `[internal].[job_worker_agents]`<br/>`[internal].[jobs]`<br/>`[internal].[tasks]` |
| `[internal].[cleanup_expired_worker]` | `[internal].[worker_agents]` |

これらの SSISDB ストアド プロシージャは、ADF、Azure SQL Managed Instance エージェント、Elastic Database ジョブを使用して、スケジュールに基づいて自動的に呼び出すこともできます。

## <a name="clean-up-ssisdb-logs-automatically-via-adf"></a>ADF を使用して SSISDB ログを自動的にクリーンアップする
SSISDB のホストに Azure SQL データベース サーバー/Managed Instance を使用するかどうかに関係なく、常に ADF を使用して、スケジュールに基づいて SSISDB ログを自動的にクリーンアップできます。 これを行うには、関連する SSISDB ストアド プロシージャを呼び出す単一の SQL 実行タスクを含む埋め込みパッケージを使用して、ADF パイプライン内で SSIS パッケージの実行アクティビティを準備できます。 ブログ「[Azure Data Factory の SSIS を使用して、3 つの簡単な手順で任意の場所で任意の SQL を実行する](https://techcommunity.microsoft.com/t5/sql-server-integration-services/run-any-sql-anywhere-in-3-easy-steps-with-ssis-in-azure-data/ba-p/2457244)」の例 4) を参照してください。

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/run-sql-ssis-activity-ssis-parameters-ssisdb-clean-up.png" alt-text="ADF を使用した SSISDB ログのクリーンアップ":::

**SQLStatementSource** パラメーターについては、`EXEC internal.cleanup_server_retention_window_exclusive` を入力して、SSIS パッケージの実行ログをクリーンアップできます。 

SSIS ジョブのログをクリーンアップするには、`EXEC internal.cleanup_completed_jobs_exclusive [@minutesToKeep=’Number of minutes to set as retention period’]` を追加します。 

SSIS IR 操作のログをクリーンアップするには、`EXEC internal.cleanup_expired_worker [@hoursToKeep=’Number of hours to set as retention period’] ` を追加します。

ADF パイプラインの準備ができたら、スケジュール トリガーをアタッチして定期的に実行できます。[スケジュールに基づいて ADF パイプラインをトリガーする方法](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)に関するページをご覧ください。

## <a name="clean-up-ssisdb-logs-automatically-via-azure-sql-managed-instance-agent"></a>Azure SQL Managed Instance エージェントを使用して SSISDB ログを自動的にクリーンアップする
Azure SQL Managed Instance を使用して SSISDB をホストする場合は、その組み込みのジョブ オーケストレーター/スケジューラである Azure SQL Managed Instance エージェントを使用して、スケジュールに基づいて SSISDB ログを自動的にクリーンアップすることもできます。 お使いの Azure SQL Managed Instance 内に SSISDB が最近作成された場合は、特に SSIS パッケージの実行ログをクリーンアップするために、Azure SQL Managed Instance エージェントの下に **SSIS サーバー メンテナンス ジョブ** と呼ばれる T-SQL ジョブも作成されています。 これは既定では無効になっており、毎日実行されるようにスケジュールで構成されます。  これを有効にし、スケジュールを再構成する場合は、SSMS を使用して、Azure SQL Managed Instance に接続します。 接続されたら、SSMS の **オブジェクト エクスプローラー** ウィンドウで、**SQL Server エージェント** ノードを展開し、**Jobs** サブノードを展開してから、**SSIS サーバー メンテナンス ジョブ** をダブルクリックして、有効化/再構成します。

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/clean-up-logs-ssms-maintenance-job.png" alt-text="Azure SQL Managed Instance エージェントを使用した SSISDB ログのクリーンアップ":::

お使いの Azure SQL Managed Instance エージェントに **SSIS サーバー メンテナンス ジョブ** がまだ作成されていない場合、そのジョブを手動で追加するには、お使いの Azure SQL Managed Instance 上で次の T-SQL スクリプトを実行します。

```sql
USE msdb
IF EXISTS(SELECT * FROM sys.server_principals where name = '##MS_SSISServerCleanupJobLogin##')
   DROP LOGIN ##MS_SSISServerCleanupJobLogin##

DECLARE @loginPassword nvarchar(256)
SELECT @loginPassword = REPLACE (CONVERT( nvarchar(256), CRYPT_GEN_RANDOM( 64 )), N'''', N'''''')
EXEC ('CREATE LOGIN ##MS_SSISServerCleanupJobLogin## WITH PASSWORD =''' +@loginPassword + ''', CHECK_POLICY = OFF')
ALTER LOGIN ##MS_SSISServerCleanupJobLogin## DISABLE

USE master
GRANT VIEW SERVER STATE TO ##MS_SSISServerCleanupJobLogin##

USE SSISDB
IF EXISTS (SELECT name FROM sys.database_principals WHERE name = '##MS_SSISServerCleanupJobUser##')
    DROP USER ##MS_SSISServerCleanupJobUser##
CREATE USER ##MS_SSISServerCleanupJobUser## FOR LOGIN ##MS_SSISServerCleanupJobLogin##
GRANT EXECUTE ON [internal].[cleanup_server_retention_window_exclusive] TO ##MS_SSISServerCleanupJobUser##
GRANT EXECUTE ON [internal].[cleanup_server_project_version] TO ##MS_SSISServerCleanupJobUser##

USE msdb
EXEC dbo.sp_add_job
    @job_name = N'SSIS Server Maintenance Job', 
    @enabled = 0,
    @owner_login_name = '##MS_SSISServerCleanupJobLogin##',
    @description = N'Runs every day. The job removes operation records from the database that are outside the retention period and maintains a maximum number of versions per project.'

DECLARE @IS_server_name NVARCHAR(30)
SELECT @IS_server_name = CONVERT(NVARCHAR, SERVERPROPERTY('ServerName'))
EXEC sp_add_jobserver  @job_name = N'SSIS Server Maintenance Job',
                       @server_name = @IS_server_name

EXEC sp_add_jobstep
    @job_name = N'SSIS Server Maintenance Job',
    @step_name = N'SSIS Server Operation Records Maintenance',
    @subsystem = N'TSQL',
    @command = N'
       DECLARE @role int
       SET @role = (SELECT [role] FROM [sys].[dm_hadr_availability_replica_states] hars INNER JOIN [sys].[availability_databases_cluster] adc ON hars.[group_id] = adc.[group_id] WHERE hars.[is_local] = 1 AND adc.[database_name] =''SSISDB'')
       IF DB_ID(''SSISDB'') IS NOT NULL AND (@role IS NULL OR @role = 1)
              EXEC [SSISDB].[internal].[cleanup_server_retention_window_exclusive]',
    @database_name = N'msdb',
    @on_success_action = 3,
    @retry_attempts = 3,
    @retry_interval = 3;

EXEC sp_add_jobstep
    @job_name = N'SSIS Server Maintenance Job',
    @step_name = N'SSIS Server Max Version Per Project Maintenance',
    @subsystem = N'TSQL',
    @command = N'
       DECLARE @role int
       SET @role = (SELECT [role] FROM [sys].[dm_hadr_availability_replica_states] hars INNER JOIN [sys].[availability_databases_cluster] adc ON hars.[group_id] = adc.[group_id] WHERE hars.[is_local] = 1 AND adc.[database_name] =''SSISDB'')
       IF DB_ID(''SSISDB'') IS NOT NULL AND (@role IS NULL OR @role = 1)
              EXEC [SSISDB].[internal].[cleanup_server_project_version]',
    @database_name = N'msdb',
    @retry_attempts = 3,
    @retry_interval = 3;

EXEC sp_add_jobschedule
    @job_name = N'SSIS Server Maintenance Job',
    @name = 'SSISDB Scheduler',
    @enabled = 1,
    @freq_type = 4, /*daily*/
    @freq_interval = 1,/*every day*/
    @freq_subday_type = 0x1,
    @active_start_date = 20001231,
    @active_end_date = 99991231,
    @active_start_time = 0,
    @active_end_time = 120000
```

関連する SSISDB ストアド プロシージャを呼び出して SSIS ジョブ/IR 操作ログを追加でクリーンアップするように、既存の **SSIS サーバー\メンテナンス ジョブ** を構成したり、上記の T-SQL スクリプトを変更したりすることもできます。

## <a name="clean-up-ssisdb-logs-automatically-via-elastic-database-jobs"></a>Elastic Database ジョブを使用して SSISDB のログを自動的にクリーンアップする
Azure SQL Database サーバーを使用して SSISDB をホストする場合は、組み込みのジョブ オーケストレーター/スケジューラが存在しないので、ADF (上記を参照) や Elastic Database ジョブ (このセクションの残りの部分を参照) などの外部コンポーネントを使用して、スケジュールに基づいて SSISDB ログを自動的にクリーンアップする必要があります。

Elastic Database ジョブは、単一のデータベースまたは一連のデータベースに対するジョブを自動化して実行できる Azure サービスです。 これらのジョブは、Azure portal、Azure PowerShell、T-SQL、REST API のいずれかを使用してスケジュール、実行、監視できます。 Elastic Database ジョブを使用して、ログのクリーンアップのために、関連する SSISDB ストアド プロシージャを 1 回、またはスケジュールに基づいて呼び出します。 データベースに高い負荷がかかるのを避けるために、スケジュールの間隔は SSISDB のリソース使用量に基づいて選ぶことができます。

詳細については、「[Elastic Database ジョブを使ってデータベースのグループを管理する](../azure-sql/database/elastic-jobs-overview.md)」を参照してください。

以降のセクションでは、特定の保持期間外の SSISDB ログを削除する、関連するストアド プロシージャ `[internal].[cleanup_server_retention_window_exclusive]`/`[internal].[cleanup_completed_jobs_exclusive]`/`[internal].[cleanup_expired_worker]` を呼び出す方法について説明します。

### <a name="configure-elastic-database-jobs-using-azure-powershell"></a>Azure PowerShell を使用して Elastic Database ジョブを構成する

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

次の Azure PowerShell スクリプトにより、選択した SSISDB ログのクリーンアップ ストアド プロシージャを呼び出す新しいエラスティック ジョブが作成されます。 詳細については、「[PowerShell を使用したエラスティック ジョブ エージェントの作成](../azure-sql/database/elastic-jobs-powershell-create.md)」を参照してください。

#### <a name="create-parameters"></a>パラメーターを作成する

``` powershell
# Parameters needed to create your job database
param(
$ResourceGroupName = $(Read-Host "Please enter an existing resource group name"),
$AgentServerName = $(Read-Host "Please enter the name of an existing Azure SQL Database server, for example myjobserver, to hold your job database"),
$SSISDBLogCleanupJobDB = $(Read-Host "Please enter a name for your job database to be created in the given Azure SQL Database server"),
$StoredProcName = $(Read-Host "Please enter the name of SSISDB log clean-up stored procedure to be invoked by your job (internal.cleanup_server_retention_window_exclusive/internal.cleanup_completed_jobs_exclusive/internal.cleanup_expired_worker)"), 

# Your job database should be a clean, empty S0 or higher service tier. We set S0 as default.
$PricingTier = "S0",

# Parameters needed to create your Elastic Job agent
$SSISDBLogCleanupAgentName = $(Read-Host "Please enter a name for your Elastic Job agent"),

# Parameters needed to create credentials in your job database for connecting to SSISDB
$PasswordForSSISDBCleanupUser = $(Read-Host "Please provide a new password for the log clean-up job user to connect to SSISDB"),

# Parameters needed to create the login and user for SSISDB
$SSISDBServerEndpoint = $(Read-Host "Please enter the name of target Azure SQL Database server that contains SSISDB, for example myssisdbserver") + '.database.windows.net',
$SSISDBServerAdminUserName = $(Read-Host "Please enter the target server admin username for SQL authentication"),
$SSISDBServerAdminPassword = $(Read-Host "Please enter the target server admin password for SQL authentication"),
$SSISDBName = "SSISDB",

# Parameters needed to set the job schedule for invoking SSISDB log clean-up stored procedure
$RunJobOrNot = $(Read-Host "Please indicate whether you want to run your job that cleans up SSISDB logs outside their retention period immediately (Y/N). Make sure the specific retention period is set properly before running the following scripts as deleted logs cannot be recovered."),
$IntervalType = $(Read-Host "Please enter the interval type for SSISDB log clean-up schedule: Year, Month, Day, Hour, Minute, Second are supported."),
$IntervalCount = $(Read-Host "Please enter the count of interval type for SSISDB log clean-up schedule."),

# The start time for SSISDB log clean-up schedule is set to current time by default. 
$StartTime = (Get-Date)
```

#### <a name="invoke-ssisdb-log-clean-up-stored-procedure"></a>SSISDB ログのクリーンアップ ストアド プロシージャを呼び出す

```powershell
# Install the latest PowerShell PackageManagement module that PowerShellGet v1.6.5 depends on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# You may need to restart your PowerShell session
# Install the latest PowerShellGet module that adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Install AzureRM.Sql preview cmdlets side by side with the existing AzureRM.Sql version
Install-Module -Name AzureRM.Sql -AllowPrerelease -Force

# Sign in to your Azure account
Connect-AzureRmAccount

# Create your job database for defining SSISDB log clean-up job and tracking the job history
Write-Output "Creating a blank SQL database to be used as your job database ..."
$JobDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $SSISDBLogCleanupJobDB -RequestedServiceObjectiveName $PricingTier
$JobDatabase

# Enable Elastic Database Jobs preview in your Azure subscription
Register-AzureRmProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql

# Create your Elastic Job agent
Write-Output "Creating your Elastic Job agent..."
$JobAgent = $JobDatabase | New-AzureRmSqlElasticJobAgent -Name $SSISDBLogCleanupAgentName
$JobAgent

# Create job credentials in your job database for connecting to SSISDB in target server
Write-Output "Creating job credentials for connecting to SSISDB..."
$JobCredSecure = ConvertTo-SecureString -String $PasswordForSSISDBCleanupUser -AsPlainText -Force
$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "SSISDBLogCleanupUser", $JobCredSecure
$JobCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "SSISDBLogCleanupUser" -Credential $JobCred

# Create the job user login in master database of target server
Write-Output "Grant permissions on the master database of target server..."
$Params = @{
  'Database' = 'master'
  'ServerInstance' = $SSISDBServerEndpoint
  'Username' = $SSISDBServerAdminUserName
  'Password' = $SSISDBServerAdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '" + $PasswordForSSISDBCleanupUser + "'"
}
Invoke-SqlCmd @Params

# Create SSISDB log clean-up user from login in SSISDB and grant it permissions to invoke SSISDB log clean-up stored procedure
Write-Output "Grant appropriate permissions on SSISDB..."
$TargetDatabase = $SSISDBName
$CreateJobUser = "CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser"
$GrantStoredProcedureExecution = "GRANT EXECUTE ON " + $StoredProcName + " TO SSISDBLogCleanupUser" 

$TargetDatabase | ForEach-Object -Process {
  $Params.Database = $_
  $Params.Query = $CreateJobUser
  Invoke-SqlCmd @Params
  $Params.Query = $GrantStoredProcedureExecution
  Invoke-SqlCmd @Params
}

# Create your target group that includes only SSISDB to clean up
Write-Output "Creating your target group that includes only SSISDB to clean up..."
$SSISDBTargetGroup = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name "SSISDBTargetGroup"
$SSISDBTargetGroup | Add-AzureRmSqlElasticJobTarget -ServerName $SSISDBServerEndpoint -Database $SSISDBName 

# Create your job to invoke SSISDB log clean-up stored procedure
Write-Output "Creating your job to invoke SSISDB log clean-up stored procedure..."
$JobName = "CleanupSSISDBLog"
$Job = $JobAgent | New-AzureRmSqlElasticJob -Name $JobName -RunOnce
$Job

# Add your job step to invoke SSISDB log clean-up stored procedure
Write-Output "Adding your job step to invoke SSISDB log clean-up stored procedure..."
$SqlText = "EXEC " + $StoredProcName 
$Job | Add-AzureRmSqlElasticJobStep -Name "Step to invoke SSISDB log clean-up stored procedure" -TargetGroupName $SSISDBTargetGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText

# Run your job to immediately invoke SSISDB log clean-up stored procedure once
if ($RunJobOrNot -eq 'Y')
{
Write-Output "Invoking SSISDB log clean-up stored procedure immediately..."
$JobExecution = $Job | Start-AzureRmSqlElasticJob
$JobExecution
}

# Schedule your job to invoke SSISDB log clean-up stored procedure periodically, deleting SSISDB logs outside their retention period
Write-Output "Starting your schedule to invoke SSISDB log clean-up stored procedure periodically..."
$Job | Set-AzureRmSqlElasticJob -IntervalType $IntervalType -IntervalCount $IntervalCount -StartTime $StartTime -Enable
```

### <a name="configure-elastic-database-jobs-using-t-sql"></a>T-SQL を使用して Elastic Database ジョブを構成する

次の T-SQL スクリプトにより、選択した SSISDB ログのクリーンアップ ストアド プロシージャを呼び出す新しいエラスティック ジョブが作成されます。 詳細については、[T-SQL を使用した Elastic Database ジョブの作成および管理](../azure-sql/database/elastic-jobs-tsql-create-manage.md)に関するページをご覧ください。

1. Azure SQL Database の空の S0 以上のサービス レベルを特定するか、ご自身のジョブ データベースに対して新しいサービス レベルを作成します。 次に、[Azure portal](https://ms.portal.azure.com/#create/Microsoft.SQLElasticJobAgent) 内で、エラスティック ジョブ エージェントを作成します。

2. ご自身のジョブ データベース内で、お使いのターゲット サーバー内の SSISDB に接続するための資格情報を作成します。

   ```sql
   -- Connect to the job database specified when creating your job agent.
   -- Create a database master key if one doesn't already exist, using your own password.
   CREATE MASTER KEY ENCRYPTION BY PASSWORD= '<EnterStrongPasswordHere>';

   -- Create credentials for SSISDB log clean-up.
   CREATE DATABASE SCOPED CREDENTIAL SSISDBLogCleanupCred WITH IDENTITY = 'SSISDBLogCleanupUser', SECRET = '<EnterStrongPasswordHere>'; 
   ```

3. クリーンアップする SSISDB のみを含むターゲット グループを定義します。

   ```sql
   -- Connect to your job database.
   -- Add your target group.
   EXEC jobs.sp_add_target_group 'SSISDBTargetGroup'

   -- Add SSISDB to your target group
   EXEC jobs.sp_add_target_group_member 'SSISDBTargetGroup',
   @target_type = 'SqlDatabase',
   @server_name = '<EnterSSISDBTargetServerName>',
   @database_name = 'SSISDB'

   -- View your recently created target group and its members.
   SELECT * FROM jobs.target_groups WHERE target_group_name = 'SSISDBTargetGroup';
   SELECT * FROM jobs.target_group_members WHERE target_group_name = 'SSISDBTargetGroup';
   ```

4. SSISDB 内のログインから SSISDB ログのクリーンアップ ユーザーを作成し、SSISDB ログのクリーンアップ ストアド プロシージャを呼び出すアクセス許可を付与します。 詳しいガイダンスについては、[ログインの管理](../azure-sql/database/logins-create-manage.md)に関するページを参照してください。

   ```sql
   -- Connect to the master database of target server that hosts SSISDB 
   CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '<strong_password>';

   -- Connect to SSISDB
   CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser;
   GRANT EXECUTE ON '<internal.cleanup_server_retention_window_exclusive/internal.cleanup_completed_jobs_exclusive/internal.cleanup_expired_worker>' TO SSISDBLogCleanupUser 
   ```

5. ジョブを作成し、SSISDB ログのクリーンアップ ストアド プロシージャを呼び出すジョブ ステップを追加します。

   ```sql
   -- Connect to your job database.
   -- Add your job to invoke the relevant SSISDB log clean-up stored procedure.
   EXEC jobs.sp_add_job @job_name='CleanupSSISDBLog', @description='Remove SSISDB logs outside their specific retention period'

   -- Add your job step to invoke the relevant SSISDB log clean-up stored procedure
   EXEC jobs.sp_add_jobstep @job_name='CleanupSSISDBLog',
   @command=N'<EXEC internal.cleanup_server_retention_window_exclusive/EXEC internal.cleanup_completed_jobs_exclusive/EXEC internal.cleanup_expired_worker>',
   @credential_name='SSISDBLogCleanupCred',
   @target_group_name='SSISDBTargetGroup'
   ```

6. 続行する前に、特定の保持期間を適切に設定してください。 この期間外の SSISDB ログは削除され、復旧できません。 その後、ご自身のジョブをすぐに実行して、SSISDB ログのクリーンアップを開始できます。

   ```sql
   -- Connect to your job database.
   -- Run your job immediately to invoke SSISDB log clean-up stored procedure.
   declare @je uniqueidentifier
   exec jobs.sp_start_job 'CleanupSSISDBLog', @job_execution_id = @je output

   -- Watch SSISDB log clean-up results 
   select @je
   select * from jobs.job_executions where job_execution_id = @je
   ```

7. 必要に応じて、スケジュールに基づいて、保持期間外の SSISDB ログを削除できます。 次のように、ご自身のジョブ パラメーターを構成します。

   ```sql
   -- Connect to your job database.
   EXEC jobs.sp_update_job
   @job_name='CleanupSSISDBLog',
   @enabled=1,
   @schedule_interval_type='<EnterIntervalType(Month,Day,Hour,Minute,Second)>',
   @schedule_interval_count='<EnterDetailedIntervalValue>',
   @schedule_start_time='<EnterProperStartTimeForSchedule>',
   @schedule_end_time='<EnterProperEndTimeForSchedule>'
   ```

### <a name="monitor-ssisdb-log-clean-up-job-using-azure-portal"></a>Azure portal を使用して SSISDB ログのクリーンアップ ジョブを監視する

Azure portal 内で SSISDB ログのクリーンアップ ジョブを監視できます。 実行ごとに、その状態、開始時刻、終了時刻を確認することができます。

:::image type="content" source="media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/monitor-cleanup-job-portal.png" alt-text="Azure portal 内で SSISDB ログのクリーンアップ ジョブを監視する":::

### <a name="monitor-ssisdb-log-clean-up-job-using-t-sql"></a>T-SQL を使用して SSISDB ログのクリーンアップ ジョブを監視する

T-SQL を使用して、SSISDB ログのクリーンアップ ジョブの実行履歴を表示することもできます。

```sql
-- Connect to your job database.
-- View all SSISDB log clean-up job executions.
SELECT * FROM jobs.job_executions WHERE job_name = 'CleanupSSISDBLog' 
ORDER BY start_time DESC

-- View all active executions.
SELECT * FROM jobs.job_executions WHERE is_active = 1
ORDER BY start_time DESC
```

## <a name="next-steps"></a>次のステップ

ご自身の Azure-SSIS IR を管理および監視するには、次の記事を参照してください。

- [Azure-SSIS 統合ランタイムの再構成](manage-azure-ssis-integration-runtime.md)

- [Azure-SSIS Integration Runtime の監視](monitor-integration-runtime.md#azure-ssis-integration-runtime)
