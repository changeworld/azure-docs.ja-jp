---
title: Azure Cosmos DB のポイントインタイム リストア機能のリソース モデル。
description: この記事では、Azure Cosmos DB のポイントインタイム リストア機能のリソース モデルについて説明します。 継続的バックアップをサポートするパラメーターと、SQL および MongoDB 用の Azure Cosmos DB API アカウントで復元できるリソースについて説明されています。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: e4fffd12b72b41c45b2718e96c34a03e28eeca29
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733178"
---
# <a name="resource-model-for-the-azure-cosmos-db-point-in-time-restore-feature"></a>Azure Cosmos DB のポイントインタイム リストア機能のリソース モデル
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

この記事では、Azure Cosmos DB のポイントインタイム リストア機能のリソース モデルについて説明されています。 ここでは、継続的バックアップをサポートするパラメーターと、SQL および MongoDB アカウント用の Azure Cosmos DB API で復元できるリソースについて説明します。

## <a name="database-accounts-resource-model"></a>データベース アカウントのリソース モデル

データベース アカウントのリソース モデルは、新しい復元シナリオをサポートするためのいくつかの追加プロパティで更新されました。 これらのプロパティは、**BackupPolicy、CreateMode、および RestoreParameters** です。

### <a name="backuppolicy"></a>BackupPolicy

`backuppolicy` パラメーターの下にある `Type` という名前のアカウント レベルのバックアップ ポリシーの新しいプロパティにより、継続的バックアップとポイントインタイム リストア機能が有効になります。 このモードは **継続的バックアップ** と呼ばれます。 このモードは、アカウントを作成するとき、または[アカウントを定期モードから継続モードに移行する](migrate-continuous-backup.md)ときに設定できます。 継続モードを有効にすると、このアカウント内で作成されたすべてのコンテナーとデータベースで、継続的バックアップとポイントインタイム リストア機能が既定で有効になります。

> [!NOTE]
> 現時点では、ポイントインタイム リストア機能は、MongoDB および SQL アカウント用の Azure Cosmos DB API で使用できます。 継続的モードでアカウントを作成した後は、それを定期的モードに切り替えることはできません。

### <a name="createmode"></a>CreateMode

このプロパティは、アカウントがどのように作成されたかを示します。 指定できる値は *Default* と *Restore* です。 復元を実行するには、この値を *Restore* に設定し、`RestoreParameters` プロパティに適切な値を指定します。

### <a name="restoreparameters"></a>RestoreParameters

`RestoreParameters` リソースには、アカウント ID、復元する時間、復元が必要なリソースなど、復元操作の詳細が含まれています。

|プロパティ名 |説明  |
|---------|---------|
|restoreMode  | 復元モードは *PointInTime* にする必要があります。 |
|restoreSource   |  復元が開始されるソース アカウントの instanceId。       |
|restoreTimestampInUtc  | アカウントの復元先となる特定の時点 (UTC)。 |
|databasesToRestore   | 復元するデータベースとコンテナーを指定する `DatabaseRestoreResource` オブジェクトの一覧。 各リソースは、1 つのデータベースと、そのデータベースにあるすべてのコレクションを表します。詳細については、「[復元可能な SQL リソース](#restorable-sql-resources)」セクションを参照してください。 この値が空の場合は、アカウント全体が復元されます。   |

### <a name="sample-resource"></a>サンプル リソース

次の JSON は、継続的バックアップが有効になっているサンプル データベース アカウント リソースです。

```json
{
  "location": "westus",
  "properties": {
    "databaseAccountOfferType": "Standard",
    "locations": [
      {
        "failoverPriority": 0,
        "locationName": "southcentralus",
        "isZoneRedundant": false
      }
    ],
    "createMode": "Restore",
    "restoreParameters": {
      "restoreMode": "PointInTime",
      "restoreSource": "/subscriptions/subid/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/1a97b4bb-f6a0-430e-ade1-638d781830cc",
      "restoreTimestampInUtc": "2020-06-11T22:05:09Z",
      "databasesToRestore": [
        {
          "databaseName": "db1",
          "collectionNames": [
            "collection1",
            "collection2"
          ]
        },
        {
          "databaseName": "db2",
          "collectionNames": [
            "collection3",
            "collection4"
          ]
        }
      ]
    },
    "backupPolicy": {
      "type": "Continuous"
    }
}
```

## <a name="restorable-resources"></a>復元可能なリソース

新しいリソースと API のセットを使用して、復元可能なリソース、復元の開始元として可能な場所、これらのリソースに対してキー操作が実行されたときのタイムスタンプに関する重要な情報を見つけることができます。

> [!NOTE]
> これらのリソースを列挙するために使用されるすべての API には、次のアクセス許可が必要です。
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read`

### <a name="restorable-database-account"></a>復元可能なデータベース アカウント

このリソースには、復元できるデータベース アカウント インスタンスが含まれています。 データベース アカウントは、削除済みまたはライブ状態のアカウントのいずれかになります。 これには、復元するソース データベース アカウントを検索するための情報が含まれています。

|プロパティ名 |説明  |
|---------|---------|
| id | リソースの一意識別子。 |
| accountName | グローバル データベース アカウント名。 |
| creationTime | アカウントが作成または移行された時刻 (UTC)。  |
| deletionTime | アカウントが削除された時刻 (UTC)。  アカウントがライブ状態の場合、この値は空になります。 |
| apiType | Azure Cosmos DB アカウントの API の種類。 |
| restorableLocations | アカウントが存在していた場所の一覧。 |
| restorableLocations: locationName | リージョン アカウントのリージョン名。 |
| restorableLocations: regionalDatabaseAccountInstanceId | リージョン アカウントの GUID。 |
| restorableLocations: creationTime | リージョン アカウントが作成または移行された時刻 (UTC)。|
| restorableLocations: deletionTime | リージョン アカウントが削除された時刻 (UTC)。 リージョン アカウントがライブ状態の場合、この値は空になります。|

復元可能なすべてのアカウントの一覧を取得するには、[復元可能なデータベース アカウント - 一覧](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-database-accounts/list)または[復元可能なデータベース アカウント - 場所別の一覧](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-database-accounts/list-by-location)に関する記事を参照してください。

### <a name="restorable-sql-database"></a>復元可能な SQL データベース

各リソースには、SQL Database で発生した作成や削除などの変更イベントの情報が含まれています。 この情報は、データベースが誤って削除されたときに、そのイベントがいつ発生したのかを調べる必要がある場合に役立ちます。

|プロパティ名 |説明  |
|---------|---------|
| eventTimestamp | データベースが作成または削除された時刻 (UTC)。 |
| ownerId | SQL データベースの名前。 |
| ownerResourceId | SQL データベースのリソース ID|
| operationType | このデータベース イベントの操作の種類。 設定できる値は次のとおりです。<br/><ul><li>Create: データベース作成イベント</li><li>Delete: データベース削除イベント</li><li>Replace: データベース変更イベント</li><li>SystemOperation: システムによってトリガーされたデータベース変更イベント。 このイベントはユーザーによって開始されません</li></ul> |
| database |イベント発生時の SQL データベースのプロパティ|

すべてのデータベース変更の一覧を取得するには、[復元可能な SQL データベース - 一覧](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-sql-databases/list)に関する記事を参照してください。

### <a name="restorable-sql-container"></a>復元可能な SQL コンテナー

各リソースには、SQL コンテナーで発生した作成や削除などの変更イベントの情報が含まれています。 この情報は、コンテナーが変更または削除されたときに、そのイベントがいつ発生したかを調べる必要がある場合に役立ちます。

|プロパティ名 |説明  |
|---------|---------|
| eventTimestamp    | このコンテナー イベントが発生した時刻 (UTC)。|
| ownerId| SQL コンテナーの名前。|
| ownerResourceId   | SQL コンテナーのリソース ID。|
| operationType | このコンテナー イベントの操作の種類。 設定できる値は次のとおりです。 <br/><ul><li>Create: コンテナー作成イベント</li><li>Delete: コンテナー削除イベント</li><li>Replace: コンテナー変更イベント</li><li>SystemOperation: システムによってトリガーされたコンテナー変更イベント。 このイベントはユーザーによって開始されません</li></ul> |
| container | イベント発生時の SQL コンテナーのプロパティ。|

同じデータベースにあるすべてのコンテナーの変更の一覧を取得するには、[復元可能な SQL コンテナー - 一覧](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-sql-containers/list)に関する記事を参照してください。

### <a name="restorable-sql-resources"></a>復元可能な SQL リソース

各リソースは、1 つのデータベースと、そのデータベースにあるすべてのコンテナーを表します。

|プロパティ名 |説明  |
|---------|---------|
| databaseName  | SQL データベースの名前。
| collectionNames   | このデータベースにある SQL コンテナーの一覧。|

指定のタイムスタンプと場所にあるアカウントに存在する SQL データベースとコンテナーの組み合わせの一覧を取得するには、[復元可能な SQL リソース - 一覧](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-sql-resources/list)に関する記事を参照してください。

### <a name="restorable-mongodb-database"></a>復元可能な MongoDB データベース

各リソースには、MongoDB データベースで発生した作成や削除などの変更イベントの情報が含まれています。 この情報は、データベースが誤って削除されたときに、そのイベントがいつ発生したのかをユーザーが調べる必要がある場合に役立ちます。

|プロパティ名 |説明  |
|---------|---------|
|eventTimestamp| このデータベース イベントが発生した時刻 (UTC)。|
| ownerId| MongoDB データベースの名前。 |
| ownerResourceId   | MongoDB データベースのリソース ID。 |
| operationType |   このデータベース イベントの操作の種類。 設定できる値は次のとおりです。<br/><ul><li> Create: データベース作成イベント</li><li> Delete: データベース削除イベント</li><li> Replace: データベース変更イベント</li><li> SystemOperation: システムによってトリガーされたデータベース変更イベント。 このイベントはユーザーによって開始されません </li></ul> |

すべてのデータベース変更の一覧を取得するには、[復元可能な Mongodb データベース - 一覧](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-mongodb-databases/list)に関する記事を参照してください。

### <a name="restorable-mongodb-collection"></a>復元可能な MongoDB コレクション

各リソースには、MongoDB コレクションで発生した作成や削除などの変更イベントの情報が含まれています。 この情報は、コレクションが変更または削除されたときに、そのイベントがいつ発生したのかをユーザーが調べる必要がある場合に役立ちます。

|プロパティ名 |説明  |
|---------|---------|
| eventTimestamp |このコレクション イベントが発生した時刻 (UTC)。 |
| ownerId| MongoDB コレクションの名前。 |
| ownerResourceId   | MongoDB コレクションのリソース ID。 |
| operationType |このコレクション イベントの操作の種類。 設定できる値は次のとおりです。<br/><ul><li>Create: コレクション作成イベント</li><li>Delete: コレクション削除イベント</li><li>Replace: コレクション変更イベント</li><li>SystemOperation: システムによってトリガーされたコレクション変更イベント。 このイベントはユーザーによって開始されません</li></ul> |

同じデータベースにあるすべてのコンテナーの変更の一覧を取得するには、[復元可能な Mongodb コレクション - 一覧](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-mongodb-collections/list)に関する記事を参照してください。

### <a name="restorable-mongodb-resources"></a>復元可能な MongoDB リソース

各リソースは、1 つのデータベースと、そのデータベースにあるすべてのコレクションを表します。

|プロパティ名 |説明  |
|---------|---------|
| databaseName  |MongoDB データベースの名前。 |
| collectionNames | このデータベースにある MongoDB コレクションの一覧。 |

指定のタイムスタンプと場所にあるアカウントに存在するすべての MongoDB データベースとコレクションの組み合わせの一覧を取得するには、[復元可能な Mongodb リソース - 一覧](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-mongodb-resources/list)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure portal](provision-account-continuous-backup.md#provision-portal)、[PowerShell](provision-account-continuous-backup.md#provision-powershell)、[CLI](provision-account-continuous-backup.md#provision-cli)、または [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template) を使用して継続的バックアップをプロビジョニングします。
* [Azure portal](restore-account-continuous-backup.md#restore-account-portal)、[PowerShell](restore-account-continuous-backup.md#restore-account-powershell)、[CLI](restore-account-continuous-backup.md#restore-account-cli)、または [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template) を使用してアカウントを復元します。
* [定期的なバックアップから継続的バックアップにアカウントを移行します](migrate-continuous-backup.md)。
* 継続的バックアップ モードでデータを復元するために必要な[アクセス許可を管理](continuous-backup-restore-permissions.md)します。
