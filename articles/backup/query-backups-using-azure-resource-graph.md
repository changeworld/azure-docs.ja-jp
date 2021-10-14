---
title: Azure Resource Graph (ARG) を使用してバックアップのクエリを実行する
description: Azure Resource Group (ARG) を使用した Azure リソースのバックアップに関するクエリの詳細について説明します。
ms.topic: conceptual
ms.date: 05/21/2021
ms.openlocfilehash: e9caa1d4d8de77efe9acb31c0cec3be5741b69c7
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236050"
---
# <a name="query-your-backups-using-azure-resource-graph-arg"></a>Azure Resource Graph (ARG) を使用してバックアップのクエリを実行する

[Azure Resource Graph (ARG)](../governance/resource-graph/overview.md) を使用すれば、追加費用なしで、Azure リソースのバックアップ情報に対してクエリを実行することができます。 ARG は、Azure Resource Management を拡張するように設計された Azure サービスです。 これは、指定した一連のサブスクリプションにわたって大規模にクエリを実行する機能を備えた、効率的でパフォーマンスに優れたリソース探索を実現することを目的としています。 ARG を使用してバックアップ メタデータのクエリを実行する主な利点を次に示します。

- 複雑なフィルター処理、グループ化、およびリソースのプロパティでの並べ替えによりリソースのクエリを大規模に行う機能。
- 進行中のバックアップジョブを含め、バックアップに関する情報をリアルタイムに取得することができる機能。
- Azure Virtual Machines やストレージ アカウントなど、関連する Azure リソースに関する有用な情報とバックアップ関連データを組み合わせる機能。

## <a name="getting-started"></a>作業の開始

ARG を使用してバックアップに対するクエリの実行を開始するには、次の手順に従います。

1. Azure portal で _Resource Graph Explorer_ と検索します。 これを選択すると、ARG クエリ エディターにリダイレクトされます。
    
    ![Azure リソース グループの検索](./media/query-backups-using-azure-resource-graph/search-resource-graph-explorer.png)

    左側のウィンドウには、クエリを実行できるテーブル (および関連付けられているスキーマ) が表示されます。
    
    - **RecoveryServicesResources** テーブルには、ジョブの詳細、バックアップ インスタンスの詳細など、バックアップ関連のレコードのほとんどが含まれます。  その他にもあります。
    - **Resources** テーブルには、Recovery Services コンテナー、Azure Virtual Machines、ストレージ アカウントなど、すべての最上位の Azure リソースに関する情報が含まれています。

    ![クエリに使用できるテーブルと関連スキーマ](./media/query-backups-using-azure-resource-graph/tables-and-associated-schemas.png)

1. これらのテーブルのデータを探索するには、クエリ エディターに **Kusto** クエリを記述し、 **[クエリの実行]** をクリックします。

    これらのクエリの出力は、**Resource Graph Explorer** から CSV でダウンロードできます。 これらのクエリは、[PowerShell](../governance/resource-graph/first-query-powershell.md)、[CLI](../governance/resource-graph/first-query-azurecli.md)、[SDK](../governance/resource-graph/first-query-python.md) など、ARG でサポートされている任意のオートメーション クライアントを使用して、カスタム オートメーションで使用することもできます。 また、ARG をデータ ソースとして使用して、Azure portal で[カスタム ブック](../azure-monitor/visualize/workbooks-overview.md)を作成することもできます。

>[!NOTE] 
>- ARG では、14 日前までのバックアップと復元ジョブをクエリすることが可能です。 履歴レコードのクエリを実行する場合は、**Azure Monitor ログ** を使用することをお勧めします。
>- ARG を使用すると、適切な RBAC 権限を持っているリソースに対してクエリを実行できます。

## <a name="sample-queries"></a>サンプル クエリ

カスタム ダッシュボードと自動化で使用できる、バックアップ データに対する ARG クエリのサンプルを次に示します。

### <a name="list-all-azure-vms-that-have-been-configured-for-backup"></a>バックアップ用に構成されているすべての Azure VM を一覧表示する

```kusto
RecoveryServicesResources 
| where type in~ ('Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupInstances',split(split(id, '/Microsoft.DataProtection/backupVaults/')[1],'/')[0],type =~ 'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend dataSourceType = case(type=~'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupInstances',properties.dataSourceSetInfo.datasourceType,'--')
| extend friendlyName = properties.friendlyName
| extend dsResourceGroup = split(split(properties.dataSourceInfo.resourceID, '/resourceGroups/')[1],'/')[0]
| extend dsSubscription = split(split(properties.dataSourceInfo.resourceID, '/subscriptions/')[1],'/')[0]
| extend lastRestorePoint = properties.lastRecoveryPoint
| extend primaryLocation = properties.dataSourceInfo.resourceLocation
| extend policyName = case(type =~ 'Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems',properties.policyName, type =~ 'microsoft.dataprotection/backupVaults/backupInstances', properties.policyInfo.name, '--')
| extend protectionState = properties.currentProtectionState
| where protectionState in~ ('ConfiguringProtection','ProtectionConfigured','ConfiguringProtectionFailed','ProtectionStopped','SoftDeleted','ProtectionError')
| where (dsSubscription in~ ('00000000-0000-0000-0000-000000000000')) and (dataSourceType in~ ('AzureIaasVM')) //add the relevant subscription ids you wish to query to this line

```

### <a name="list-all-backup-jobs-on-azure-databases-for-postgresql-servers-in-the-last-one-week"></a>過去 1 週間に行われた Azure Databases for PostgreSQL Servers のすべてのバックアップ ジョブを一覧表示する

```kusto
RecoveryServicesResources 
| where type in~ ('Microsoft.DataProtection/backupVaults/backupJobs')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.vaultName,type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend friendlyName = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs',strcat(properties.dataSourceSetName , '/', properties.dataSourceName),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs', properties.entityFriendlyName, '--')
| extend dataSourceType = case(type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupJobs',properties.dataSourceType,'--')
| extend backupInstanceName = properties.backupInstanceId
| extend dsResourceGroup = split(split(properties.dataSourceId, '/resourceGroups/')[1],'/')[0]| extend dsSubscription = split(split(properties.dataSourceId, '/subscriptions/')[1],'/')[0]
| extend status = properties.status
| extend dataSourceId = properties.dataSourceId
| extend primaryLocation = properties.dataSourceLocation
| extend jobStatus = case (properties.status == 'Completed' or properties.status == 'CompletedWithWarnings','Succeeded',properties.status == 'Failed','Failed',properties.status == 'InProgress', 'Started', properties.status), operation = case(type =~ 'microsoft.dataprotection/backupVaults/backupJobs' and tolower(properties.operationCategory) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operationCategory),type =~ 'microsoft.dataprotection/backupVaults/backupJobs', tolower(properties.operationCategory), type =~ 'Microsoft.RecoveryServices/vaults/backupJobs' and tolower(properties.operation) =~ 'backup' and properties.isUserTriggered == 'true',strcat('adhoc',properties.operation),type =~ 'Microsoft.RecoveryServices/vaults/backupJobs',tolower(properties.operation), '--'),startTime = todatetime(properties.startTime),endTime = properties.endTime, duration = properties.duration
| project id, name, friendlyName, resourceGroup, vaultName, dataSourceType, operation, jobStatus, startTime, duration, backupInstanceName, dsResourceGroup, dsSubscription, status, primaryLocation, dataSourceId
| where  (dsSubscription in~ ('00000000-0000-0000-0000-000000000000')) and (dataSourceType in~ ('Microsoft.DBforPostgreSQL/servers/databases')) and (status in~ ('Started','InProgress','Succeeded','Completed','Failed','CompletedWithWarnings')) and (operation in~ ('adhocBackup','backup')) //add the relevant subscription ids you wish to query to this line
| where (startTime >= ago(7d))

```

### <a name="list-all-azure-vms-that-have-not-been-configured-for-backup"></a>バックアップ用に構成されていないすべての Azure VM を一覧表示する

```kusto
Resources
| where type in~ ('microsoft.compute/virtualmachines','microsoft.classiccompute/virtualmachines') 
| extend resourceId=tolower(id) 
| join kind = leftouter ( RecoveryServicesResources
| where type == "microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems"
| where properties.backupManagementType == "AzureIaasVM"
| project resourceId = tolower(tostring(properties.sourceResourceId)), backupItemid = id, isBackedUp = isnotempty(id) ) on resourceId 
| extend isProtected = isnotempty(backupItemid)
| where (isProtected == (0))
| project id,name,resourceGroup,location,tags

```

### <a name="list-all-backup-policies-used-for-azure-vms"></a>Azure VM で使用するすべてのバックアップ ポリシーを一覧表示する

```kusto
RecoveryServicesResources
| where type == 'microsoft.recoveryservices/vaults/backuppolicies'
| extend vaultName = case(type == 'microsoft.recoveryservices/vaults/backuppolicies', split(split(id, 'microsoft.recoveryservices/vaults/')[1],'/')[0],type == 'microsoft.recoveryservices/vaults/backuppolicies', split(split(id, 'microsoft.recoveryservices/vaults/')[1],'/')[0],'--')
| extend datasourceType = case(type == 'microsoft.recoveryservices/vaults/backuppolicies', properties.backupManagementType,type == 'microsoft.dataprotection/backupVaults/backupPolicies',properties.datasourceTypes[0],'--')
| project id,name,vaultName,resourceGroup,properties,datasourceType
| where datasourceType == 'AzureIaasVM'
```

### <a name="list-all-vms-associated-with-a-given-backup-policy"></a>特定のバックアップ ポリシーに関連付けられているすべての VM を一覧表示する

```kusto
RecoveryServicesResources
| where type == "microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems"
| project propertiesJSON = parse_json(properties)
| where propertiesJSON.backupManagementType == "AzureIaasVM"
| project VMID=propertiesJSON.sourceResourceId, PolicyID=propertiesJSON.policyId
| where PolicyID == "<ARM ID of the given policy>"
```

### <a name="list-all-backup-policies-used-for-azure-databases-for-postgresql-servers"></a>Azure database for PostgreSQL サーバーで使用されるすべてのバックアップ ポリシーを一覧表示する

```kusto
RecoveryServicesResources 
| where type in~ ('Microsoft.DataProtection/BackupVaults/backupPolicies')
| extend vaultName = case(type =~ 'microsoft.dataprotection/backupVaults/backupPolicies', split(split(id, '/Microsoft.DataProtection/backupVaults/')[1],'/')[0],type =~ 'microsoft.recoveryservices/vaults/backupPolicies', split(split(id, '/Microsoft.RecoveryServices/vaults/')[1],'/')[0],'--')
| extend datasourceType = case(type =~ 'Microsoft.RecoveryServices/vaults/backupPolicies', properties.backupManagementType,type =~ 'microsoft.dataprotection/backupVaults/backupPolicies',properties.datasourceTypes[0],'--')
| project id,name,vaultName,resourceGroup,properties,datasourceType
| where (datasourceType in~ ('Microsoft.DBforPostgreSQL/servers/databases'))

```

## <a name="next-steps"></a>次のステップ

[Azure Resource Graph の詳細についてさらに学習します](../governance/resource-graph/overview.md)
