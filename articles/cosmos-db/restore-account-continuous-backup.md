---
title: 継続的バックアップ モードを使用する Azure Cosmos DB アカウントを復元します。
description: 復元時間を特定し、ライブまたは削除された Azure Cosmos DB アカウントを復元する方法について説明します。 ここでは、イベント フィードを使用して復元時間を特定し、Azure portal、PowerShell、CLI、または Resource Manager テンプレートを使用してアカウントを復元する方法を示します。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/03/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3161971323ebda6b55ec0fb423089d3115cd9c01
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562126"
---
# <a name="restore-an-azure-cosmos-db-account-that-uses-continuous-backup-mode"></a>継続的バックアップ モードを使用する Azure Cosmos DB アカウントを復元する
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB のポイントインタイム リストア機能を使用すると、コンテナー内で誤って行われた変更から回復し、削除されたアカウント、データベース、またはコンテナーの復元や、任意のリージョン (バックアップが存在していた場所) への復元ができます。 継続的バックアップ モードを使用すると、過去 30 日以内の任意の時点に復元できます。

この記事では、復元時間を特定し、ライブまたは削除された Azure Cosmos DB アカウントを復元する方法について説明します。 ここでは、[Azure portal](#restore-account-portal)、[PowerShell](#restore-account-powershell)、[CLI](#restore-account-cli)、または [Resource Manager テンプレート](#restore-arm-template) を使用したアカウントの復元を示します。

## <a name="restore-an-account-using-azure-portal"></a><a id="restore-account-portal"></a>Azure portal を使用してアカウントを復元する

### <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>誤った変更からライブ アカウントを復元する

Azure portal を使用して、ライブ アカウント全体、またはその下にある選択したデータベースやコンテナーを復元できます。 次の手順に従って、データを復元します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. Azure Cosmos DB アカウントに移動して、 **[ポイントインタイム リストア]** ブレードを開きます。

   > [!NOTE]
   > Azure portal の [復元] ブレードは、`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` アクセス許可を持っている場合にのみ設定されます。 このアクセス許可を設定する方法の詳細については、[バックアップとアクセス許可の復元](continuous-backup-restore-permissions.md)に関する記事を参照してください。

1. 復元するには、次の詳細を入力します。

   * **復元ポイント (UTC)** – 過去 30 日以内のタイムスタンプ。 アカウントは、そのタイムスタンプの時点で存在している必要があります。 復元ポイントは UTC で指定できます。 復元する時刻の秒数にできるだけ近づけることができます。 [復元ポイントの特定](#event-feed)に関するヘルプについては、**こちらをクリック** リンクを選択してください。

   * **場所** – アカウントが復元される宛先リージョン。 アカウントは、指定されたタイムスタンプ (たとえば、米国西部または米国東部) でこのリージョンに存在する必要があります。 アカウントは、ソース アカウントが存在していたリージョンにのみ復元できます。

   * **Restore Resource (復元リソース)** – **アカウント全体** か、**選択したデータベースまたはコンテナー** を復元するかを選択できます。 データベースとコンテナーは、指定されたタイムスタンプの時点で存在している必要があります。 選択された復元ポイントと場所に基づいて、復元リソースが設定されます。これによりユーザーは、復元する必要がある特定のデータベースまたはコンテナーを選択できるようになります。

   * **リソース グループ** - ターゲット アカウントが作成および復元されるリソース グループ。 このリソース グループは、既に存在している必要があります。

   * **Restore Target Account (復元ターゲット アカウント)** - ターゲット アカウント名。 ターゲット アカウント名は、新しいアカウントを作成するときと同じガイドラインに従う必要があります。 このアカウントは、復元プロセスによって、ソース アカウントが存在するのと同じリージョンに作成されます。
 
   :::image type="content" source="./media/restore-account-continuous-backup/restore-live-account-portal.png" alt-text="Azure portal で誤った変更からライブ アカウントを復元します。" border="true" lightbox="./media/restore-account-continuous-backup/restore-live-account-portal.png":::

1. 上のパラメーターを選択したら、 **[送信]** ボタンを選択して復元を開始します。 復元コストは、指定したリージョン内のバックアップ ストレージのデータ量とコストに基づく 1 回限りの料金です。 詳細については、[価格](continuous-backup-restore-introduction.md#continuous-backup-pricing)に関するセクションを参照してください。

復元中にソース アカウントを削除すると、復元に失敗することがあります。

### <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>イベント フィードを使用して復元時刻を特定する

Azure portal で復元ポイントの時刻を入力する際に、復元ポイントを特定するのにヘルプが必要な場合は、 **[Click here]\(ここをクリック\)** リンクを選択すると、イベント フィードのブレードが表示されます。 イベント フィードには、ソース アカウントのデータベースおよびコンテナーに対する作成、置換、削除イベントに関する完全に忠実な一覧が表示されます。 

たとえば、特定のコンテナーが削除または更新される前の時点に復元する必要がある場合は、このイベント フィードを確認してください。 イベントは、発生時刻順 (降順) に時系列で表示され、最近のイベントが上部に表示されます。 結果を参照して、イベントの前後の時間を選択することで、時刻をさらに絞り込むことができます。

:::image type="content" source="./media/restore-account-continuous-backup/event-feed-portal.png" alt-text="イベント フィードを使用して復元ポイントの時刻を特定します。" border="true" lightbox="./media/restore-account-continuous-backup/event-feed-portal.png":::

> [!NOTE]
> イベント フィードには、項目リソースへの変更は表示されません。 復元のために、いつでも過去 30 日以内の任意のタイムスタンプ (その時点でアカウントが存在する限り) を手動で指定できます。

### <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>削除されたアカウントを復元する

Azure portal を使用すると、削除されたアカウントを削除後 30 日以内に完全に復元できます。 次の手順に従って、削除されたアカウントを復元します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. グローバル検索バーで「*Azure Cosmos DB*」リソースを検索します。 すべての既存のアカウントが一覧表示されます。
1. 次に、 **[復元]** ボタンを選択します。 [復元] ブレードに、保持期間 (削除時刻から 30 日間) 以内に復元できる削除済みアカウントの一覧が表示されます。
1. 復元するアカウントを選択します。

   :::image type="content" source="./media/restore-account-continuous-backup/restore-deleted-account-portal.png" alt-text="削除されたアカウントを Azure portal から復元します。" border="true" lightbox="./media/restore-account-continuous-backup/restore-deleted-account-portal.png":::

   > [!NOTE]
   > Azure portal の [復元] ブレードは、`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` アクセス許可を持っている場合にのみ設定されます。 このアクセス許可を設定する方法の詳細については、[バックアップとアクセス許可の復元](continuous-backup-restore-permissions.md)に関する記事を参照してください。

1. 復元するアカウントを選択し、削除されたアカウントを復元するために次の詳細情報を入力します。

   * **復元ポイント (UTC)** – 過去 30 日以内のタイムスタンプ。 アカウントは、そのタイムスタンプの時点で存在している必要があります。 復元ポイントは UTC で指定します。 復元する時刻の秒数にできるだけ近づけることができます。

   * **場所** – アカウントを復元する必要がある宛先リージョン。 ソース アカウントは、指定されたタイムスタンプの時点でこのリージョンに存在している必要があります。 例: 米国西部または米国東部。  

   * **リソース グループ** - ターゲット アカウントが作成および復元されるリソース グループ。 このリソース グループは、既に存在している必要があります。

   * **Restore Target Account (復元ターゲット アカウント)** – ターゲット アカウント名は、新しいアカウントを作成するときと同じガイドラインに従う必要があります。 このアカウントは、復元プロセスによって、ソース アカウントが存在するのと同じリージョンに作成されます。

### <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>復元操作の状態を追跡する

復元操作を開始した後、ポータルの右上隅にある **[通知]** ベル アイコンを選択します。 これにより、復元しているアカウントの状態を示すリンクが表示されます。 復元の進行中は、アカウントの状態は "*Creating*" (作成中) になります。復元操作が完了すると、アカウントの状態は "*Online*" (オンライン) に変わります。

:::image type="content" source="./media/restore-account-continuous-backup/track-restore-operation-status.png" alt-text="操作が完了すると、復元されたアカウントの状態は、作成中からオンラインに変わります。" border="true" lightbox="./media/restore-account-continuous-backup/track-restore-operation-status.png":::

### <a name="get-the-restore-details-from-the-restored-account"></a>復元されたアカウントから復元の詳細を取得する

復元操作が完了したら、復元元のソース アカウントの詳細や復元時刻を確認する必要があります。

Azure portal から復元の詳細を取得するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) にサインインし、復元されたアカウントに移動します。

1. **[テンプレートのエクスポート]** ペインに移動します。 これにより、復元されたアカウントに対応する JSON テンプレートが開きます。

1. **resources** > **properties** > **restoreParameters** オブジェクトに、復元の詳細が含まれています。 **restoreTimestampInUtc** で、アカウントが復元された時刻を確認できます。**databasesToRestore** には、アカウントが復元された特定のデータベースとコンテナーが示されています。

## <a name="restore-an-account-using-azure-powershell"></a><a id="restore-account-powershell"></a>Azure PowerShell を使用してアカウントを復元する

アカウントを復元する前に、[Azure PowerShell の最新バージョン](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true)または 6.2.0 以降のバージョンをインストールしてください。 次に、Azure アカウントに接続し、次のコマンドを使用して必要なサブスクリプションを選択します。

1. 次のコマンドを使用して、Azure にサインインします。

   ```azurepowershell
   Connect-AzAccount
   ```

1. 次のコマンドを使用して、特定のサブスクリプションを選択します。

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>

### <a id="trigger-restore-ps"></a>Trigger a restore operation

The following cmdlet is an example to trigger a restore operation with the restore command by using the target account, source account, location, resource group, and timestamp:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "RestoredAccountName" `
  -SourceDatabaseAccountName "SourceDatabaseAccountName" `
  -RestoreTimestampInUtc "UTCTime" `
  -Location "AzureRegionName"

```

**例 1:** アカウント全体を復元する。

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "Pitracct" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**例 2:** 特定のコレクションとデータベースを復元する。 この例では、*MyDB1* のコレクション *MyCol1*、*MyCol2* と、データベース *MyDB2* 全体 (その下にあるすべてのコンテナーが含まれます) を復元します。

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "MyDB1" -CollectionName "MyCol1", "MyCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "MyDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "Pitracct" `
  -SourceDatabaseAccountName "SourceSql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

### <a name="get-the-restore-details-from-the-restored-account"></a>復元されたアカウントから復元の詳細を取得する

`Az.CosmosDB` モジュールをインポートし、次のコマンドを実行して復元の詳細を取得します。 restoreTimestamp は restoreParameters オブジェクトの下にあります。

```azurepowershell
Get-AzCosmosDBAccount -ResourceGroupName MyResourceGroup -Name MyCosmosDBDatabaseAccount 
```

### <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>SQL API の復元可能なリソースを列挙する

列挙コマンドレットは、さまざまなタイムスタンプで復元に使用できるリソースの検出に役立ちます。 また、復元可能なアカウント、データベース、コンテナー リソースでの主要イベントのフィードも示されます。

#### <a name="list-all-the-accounts-that-can-be-restored-in-the-current-subscription"></a>現在のサブスクリプションで復元可能なすべてのアカウントを一覧表示する

現在のサブスクリプションで復元可能なすべてのアカウントを一覧表示するには、`Get-AzCosmosDBRestorableDatabaseAccount` PowerShell コマンドを実行します。

応答には、復元可能なすべてのデータベース アカウント (ライブと削除済みの両方) と、それらの復元元として指定できるリージョンが含まれます。

```json
{
    "accountName": "SampleAccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "location": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "location": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

アカウントの `CreationTime` または `DeletionTime` と同様に、リージョンにも `CreationTime` または `DeletionTime` があります。 これらの時間によって、そのリージョンに復元する適切なリージョンと有効な時間範囲を選択できます。

#### <a name="list-all-the-versions-of-sql-databases-in-a-live-database-account"></a>ライブ データベース アカウント内のすべてのバージョンの SQL データベースを一覧表示する

データベースのすべてのバージョンを一覧表示すると、データベースの実際の存在時間が不明なシナリオで、正しいデータベースを選択できます。

データベースのすべてのバージョンを一覧表示するには、次の PowerShell コマンドを実行します。 このコマンドは、ライブ アカウントでのみ機能します。 `DatabaseAccountInstanceId` と `Location` パラメーターは、`Get-AzCosmosDBRestorableDatabaseAccount` コマンドレットの応答の `name` および `location` プロパティから取得されます。 `DatabaseAccountInstanceId` 属性は、復元されるソース データベース アカウントの `instanceId` プロパティを参照します。

```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -Location "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

#### <a name="list-all-the-versions-of-sql-containers-of-a-database-in-a-live-database-account"></a>ライブ データベース アカウント内のデータベースの SQL コンテナーのすべてのバージョンを一覧表示する

SQL コンテナーのすべてのバージョンを一覧表示するには、次のコマンドを使用します。 このコマンドは、ライブ アカウントでのみ機能します。 `DatabaseRId` パラメーターは復元するデータベースの `ResourceId` です。 これは、`Get-AzCosmosdbSqlRestorableDatabase` コマンドレットの応答で検出される `ownerResourceid` 属性の値です。 この応答には、このデータベース内のすべてのコンテナーで実行された操作の一覧も含まれています。

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRId "AoQ13r==" `
  -Location "West US"

```

#### <a name="find-databases-or-containers-that-can-be-restored-at-any-given-timestamp"></a>特定のタイムスタンプで復元可能なデータベースやコンテナーを見つける

特定のタイムスタンプで復元可能なデータベースやコンテナーの一覧を取得するには、次のコマンドを使用します。 このコマンドは、ライブ アカウントでのみ機能します。

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

### <a name="enumerate-restorable-resources-in-api-for-mongodb"></a><a id="enumerate-mongodb-api"></a>MongoDB 用 API で復元可能なリソースを列挙する

下記の列挙コマンドは、さまざまなタイムスタンプで復元に使用できるリソースを検出するのに役立ちます。 また、復元可能なアカウント、データベース、コンテナー リソースでの主要イベントのフィードも示されます。 これらのコマンドは、ライブ アカウントでのみ機能し、SQL API コマンドに似ていますが、コマンド名には `sql` ではなく `MongoDB` を使用します。

#### <a name="list-all-the-versions-of-mongodb-databases-in-a-live-database-account"></a>ライブ データベース アカウント内の MongoDB データベースのすべてのバージョンを一覧表示する

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US"

```

#### <a name="list-all-the-versions-of-mongodb-collections-of-a-database-in-a-live-database-account"></a>ライブ データベース アカウント内のデータベースの mongodb コレクションのすべてのバージョンを一覧表示する

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRId "AoQ13r==" `
  -Location "West US"
```

#### <a name="list-all-the-resources-of-a-mongodb-database-account-that-are-available-to-restore-at-a-given-timestamp-and-region"></a>指定されたタイムスタンプとリージョンで復元に使用可能な mongodb データベース アカウントのすべてのリソースを一覧表示する

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="restore-an-account-using-azure-cli"></a><a id="restore-account-cli"></a>Azure CLI を使用してアカウントを復元する

アカウントを復元する前に、次の手順に従って Azure CLI をインストールします。

1. Azure CLI の最新バージョンをインストールします

   * [Azure CLI](/cli/azure/install-azure-cli) の最新バージョンまたは 2.26.0 以降のバージョンをインストールします。
   * CLI を既にインストールしている場合は、`az upgrade` コマンドを使用して、最新バージョンに更新します。 このコマンドは、2.11 より上位の CLI バージョンでのみ機能します。 以前のバージョンがある場合は、上記のリンクを使用して最新バージョンをインストールします。

1. サインインして、使用するサブスクリプションを選択する

   * `az login` コマンドで Azure アカウントにサインインします。
   * `az account set -s <subscriptionguid>` コマンドを使用して、必要なサブスクリプションを選択します。

### <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore-cli"></a>CLI による復元操作のトリガー

復元をトリガーする最も簡単な方法は、ターゲット アカウントの名前、ソース アカウント、場所、リソース グループ、タイムスタンプ (UTC)、および必要に応じてデータベースとコンテナーの名前を指定して、復元コマンドを発行することです。 復元操作をトリガーする例を次に示します。

1. 既存のアカウントから復元して、新しい Azure Cosmos DB アカウントを作成します。

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. 既存のデータベース アカウントから選択したデータベースとコンテナーのみを復元して、新しい Azure Cosmos DB アカウントを作成します。

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=Collection1 Collection2 \
    --databases-to-restore name=MyDB2 collections=Collection3 Collection4

   ```

### <a name="get-the-restore-details-from-the-restored-account"></a>復元されたアカウントから復元の詳細を取得する

次のコマンドを実行して、復元の詳細を取得します。 restoreTimestamp は restoreParameters オブジェクトの下にあります。

```azurecli-interactive
az cosmosdb show --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup
```

### <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>SQL API の復元可能なリソースの列挙

下記の列挙コマンドは、さまざまなタイムスタンプで復元に使用できるリソースを検出するのに役立ちます。 また、復元可能なアカウント、データベース、コンテナー リソースでの主要イベントのフィードも示されます。

#### <a name="list-all-the-accounts-that-can-be-restored-in-the-current-subscription"></a>現在のサブスクリプションで復元可能なすべてのアカウントを一覧表示する

現在のサブスクリプションで復元可能なすべてのアカウントを一覧表示するには、次の CLI コマンドを実行します

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "Pitracct"
```

応答には、復元可能なすべてのデータベース アカウント (ライブと削除済みの両方) と、復元可能なリージョンが含まれます。

```json
{
    "accountName": "Pitracct",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

アカウントの `CreationTime` または `DeletionTime` と同様に、リージョンにも `CreationTime` または `DeletionTime` があります。 これらの時間によって、そのリージョンに復元する適切なリージョンと有効な時間範囲を選択できます。

#### <a name="list-all-the-versions-of-databases-in-a-live-database-account"></a>ライブ データベース アカウント内のデータベースのすべてのバージョンを一覧表示する

データベースのすべてのバージョンを一覧表示すると、データベースの存在の実際の時間が不明なシナリオで、正しいデータベースを選択できます。

データベースのすべてのバージョンを一覧表示するには、次の CLI コマンドを実行します。 このコマンドは、ライブ アカウントでのみ機能します。 `instance-id` と `location` パラメーターは、`az cosmosdb restorable-database-account list` コマンドの応答の `name` および `location` プロパティから取得されます。 instanceId 属性は、復元されるソース データベース アカウントのプロパティでもあります。

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

このコマンドの出力に、データベースが作成および削除されたタイミングが表示されるようになります。

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

#### <a name="list-all-the-versions-of-sql-containers-of-a-database-in-a-live-database-account"></a>ライブ データベース アカウント内のデータベースの SQL コンテナーのすべてのバージョンを一覧表示する

SQL コンテナーのすべてのバージョンを一覧表示するには、次のコマンドを使用します。 このコマンドは、ライブ アカウントでのみ機能します。 `database-rid` パラメーターは復元するデータベースの `ResourceId` です。 これは、`az cosmosdb sql restorable-database list` コマンドの応答で検出される `ownerResourceid` 属性の値です。

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

このコマンドの出力には、このデータベース内のすべてのコンテナーで実行された操作の一覧が含まれています。

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

#### <a name="find-databases-or-containers-that-can-be-restored-at-any-given-timestamp"></a>特定のタイムスタンプで復元可能なデータベースやコンテナーを見つける

特定のタイムスタンプで復元可能なデータベースやコンテナーの一覧を取得するには、次のコマンドを使用します。 このコマンドは、ライブ アカウントでのみ機能します。

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

### <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>MongoDB API アカウントの復元可能なリソースの列挙

下記の列挙コマンドは、さまざまなタイムスタンプで復元に使用できるリソースを検出するのに役立ちます。 また、復元可能なアカウント、データベース、およびコンテナー リソースでのキー イベントのフィードも提供されます。 これらのコマンドは、ライブ アカウントでのみ機能します。

#### <a name="list-all-the-versions-of-mongodb-databases-in-a-live-database-account"></a>ライブ データベース アカウント内の mongodb データベースのすべてのバージョンを一覧表示する

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

#### <a name="list-all-the-versions-of-mongodb-collections-of-a-database-in-a-live-database-account"></a>ライブ データベース アカウント内のデータベースの mongodb コレクションのすべてのバージョンを一覧表示する

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

#### <a name="list-all-the-resources-of-a-mongodb-database-account-that-are-available-to-restore-at-a-given-timestamp-and-region"></a>指定されたタイムスタンプとリージョンで復元に使用可能な mongodb データベース アカウントのすべてのリソースを一覧表示する

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore-arm-template"></a>Resource Manager テンプレートを使用した復元

また、Resource Manager テンプレートを使用してアカウントを復元することもできます。 テンプレートを定義するときは、次のパラメーターを指定します。

* `createMode` パラメーターを *Restore* に設定します。
* `restoreParameters` を定義します。`restoreSource` の値は、ソース アカウントの `az cosmosdb restorable-database-account list` コマンドの出力から抽出されます。 アカウント名のインスタンス ID 属性は、復元の実行に使用されます。
* `restoreMode` パラメーターを *PointInTime* に設定し、`restoreTimestampInUtc` 値を構成します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

次に、Azure PowerShell または CLI を使用してテンプレートをデプロイします。 次の例は、CLI コマンドを使用してテンプレートをデプロイする方法を示しています。  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>次のステップ

* [Azure portal](provision-account-continuous-backup.md#provision-portal)、[PowerShell](provision-account-continuous-backup.md#provision-powershell)、[CLI](provision-account-continuous-backup.md#provision-cli)、または [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template) を使用して継続的バックアップをプロビジョニングします。
* [定期的なバックアップから継続的バックアップにアカウントを移行する方法](migrate-continuous-backup.md)。
* [継続的バックアップ モードのリソース モデル。](continuous-backup-restore-resource-model.md)
* 継続的バックアップ モードでデータを復元するために必要な[アクセス許可を管理](continuous-backup-restore-permissions.md)します。
