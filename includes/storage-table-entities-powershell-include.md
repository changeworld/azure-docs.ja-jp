<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>テーブル エンティティの管理

テーブルが準備できたので、エンティティ、またはテーブル内の行を管理する方法を見てみましょう。 

1 個のエンティティは、最大 255 個のプロパティを含むことができます。これには、**PartitionKey**、**RowKey**、**Timestamp** の 3 個のシステム プロパティも含まれます。 **PartitionKey** と **RowKey** の値を挿入または更新することはユーザーが担当します。 一方、サーバーは **Timestamp** の値を管理します。この値は変更できません。 **PartitionKey** と **RowKey** が組み合わさって、テーブル内の各エンティティを一意に識別します。

* **PartitionKey**: エンティティが格納されるパーティションを決定します。
* **RowKey**: パーティション内のエンティティを一意に識別します。

1 個のエンティティに対して最大 252 個のカスタム プロパティを定義できます。 

### <a name="add-table-entities"></a>テーブル エンティティの追加

**Add-StorageTableRow** を使用してテーブルにエンティティを追加します。 これらの例では、"partition1" と "partition2" の値を持つパーティション キーおよび州コードと等しい行キーを使用します。 各エンティティのプロパティは、username と userid です。 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>テーブル エンティティのクエリ

テーブル内のエンティティをクエリする方法はいくつかあります。

#### <a name="retrieve-all-entities"></a>すべてのエンティティの取得

すべてのエンティティを取得するには、**Get-AzureStorageTableRowAll** を使用します。

```powershell
Get-AzureStorageTableRowAll -table $storageTable | ft
```

このコマンドによって、次の表のような結果が生成されます。

| userid | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>特定のパーティションのエンティティの取得

特定のパーティション内のすべてのエンティティを取得するには、**Get-AzureStorageTableRowByPartitionKey** を使用します。

```powershell
Get-AzureStorageTableRowByPartitionKey -table $storageTable -partitionKey $partitionKey1 | ft
```
結果は次の表のようになります。

| userid | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>特定の列の特定の値のエンティティの取得

特定の列の値が特定の値に等しいエンティティを取得するには、**Get-AzureStorageTableRowByColumnName** を使用します。

```powershell
Get-AzureStorageTableRowByColumnName -table $storageTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

このクエリは、1 つのレコードを取得します。

|フィールド|value|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>カスタム フィルターを使用したエンティティの取得 

カスタム フィルターを使用してエンティティを取得するには、**Get-AzureStorageTableRowByCustomFilter** を使用します。

```powershell
Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter "(userid eq '1')"
```

このクエリは、1 つのレコードを取得します。

|フィールド|value|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>エンティティの更新 

エンティティを更新するには 3 つの手順があります。 まず、変更するエンティティを取得します。 次に、変更を加えます。 3 つ目に、**Update-AzureStorageTableRow** を使用して変更をコミットします。

username = 'Jessie' のエンティティを username = 'Jessie2' になるように更新します。 この例では、.NET 型を使用してカスタム フィルターを作成する別の方法も示しています。 

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2" 

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzureStorageTableRow -table $storageTable 

# To see the new record, query the table.
Get-AzureStorageTableRowByCustomFilter -table $storageTable `
    -customFilter "(username eq 'Jessie2')"
```

結果には、Jessie2 レコードが表示されます。

|フィールド|value|
|----|----|
| userid | 2 |
| username | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>テーブル エンティティの削除

1 つのエンティティまたはテーブル内のすべてのエンティティを削除することができます。

#### <a name="deleting-one-entity"></a>1 つのエンティティの削除

1 つのエンティティを削除するには、そのエンティティへの参照を取得し、**Remove-AzureStorageTableRow** にパイプします。

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter 
$userToDelete | Remove-AzureStorageTableRow -table $storageTable 

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzureStorageTableRowAll -table $storageTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>テーブル内のすべてのエンティティの削除 

テーブル内のすべてのエンティティを削除するには、それらのエンティティを取得し、結果を削除コマンドレットにパイプします。 

```powershell
# Get all rows and pipe it into the remove cmdlet.
Get-AzureStorageTableRowAll `
    -table $storageTable | Remove-AzureStorageTableRow -table $storageTable 

# List entities in the table (there won't be any).
Get-AzureStorageTableRowAll -table $storageTable | ft
```