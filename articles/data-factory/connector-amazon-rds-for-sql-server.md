---
title: Amazon RDS for SQL Server からデータをコピーする
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Azure Synapse Analytics パイプラインを使用して、オンプレミスまたは Azure VM の Amazon RDS for SQL Server データベースからデータをコピーする方法について説明します。
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 10/18/2021
ms.openlocfilehash: b75a59d758c5447cec0eb66855866e1349fbd085
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176239"
---
# <a name="copy-data-from-amazon-rds-for-sql-server-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Azure Data Factory または Azure Synapse Analytics を使用して Amazon RDS for SQL Server からデータをコピーする

この記事では、Azure Data Factory および Azure Synapse パイプラインのコピー アクティビティを使用して、Amazon RDS for SQL Server データベースからデータをコピーする方法について説明します。 詳細については、[Azure Data Factory](introduction.md) または [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) の概要記事を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

この Amazon RDS for SQL Server コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)

Amazon RDS for SQL Server データベースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションの表を参照してください。

具体的には、この Amazon RDS for SQL Server コネクタでは以下がサポートされています。

- SQL Server バージョン 2005 以降。
- SQL または Windows 認証を使用したデータのコピー。
- ソースとして、SQL クエリまたはストアド プロシージャを使用してデータを取得する。 Amazon RDS for SQL Server ソースからの並列コピーを選択することもできます。詳細については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)」を参照してください。

[SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb) はサポートされていません。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-an-amazon-rds-for-sql-server-linked-service-using-ui"></a>UI を使用して Amazon RDS for SQL Server のリンク サービスを作成する

次の手順を使用して、Azure portal UI で Amazon RDS for SQL Server のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成するスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI で新しいリンク サービスを作成するスクリーンショット。":::

2. Amazon RDS for SQL Server を検索し、Amazon RDS for SQL Server コネクタを選択します。

    :::image type="content" source="media/connector-amazon-rds-for-sql-server/amazon-rds-for-sql-server-connector.png" alt-text="Amazon RDS for SQL Server コネクタのスクリーンショット。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-amazon-rds-for-sql-server/configure-amazon-rds-for-sql-server-linked-service.png" alt-text="Amazon RDS for SQL Server のリンク サービスの構成のスクリーンショット。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

以下のセクションでは、Amazon RDS for SQL Server データベース コネクタに固有の Data Factory および Synapse パイプライン エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Amazon RDS for SQL Server のリンク サービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、**AmazonRdsForSqlServer** に設定する必要があります。 | はい |
| connectionString |SQL 認証または Windows 認証を使用して、Amazon RDS for SQL Server データベースに接続するために必要な **connectionString** 情報を指定します。 以下のサンプルを参照してください。<br/>また、Azure Key Vault にパスワードを格納することもできます。 それが SQL 認証である場合は、接続文字列から `password` 構成を取得します。 詳細については、この表の後にある JSON の例および「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。 |はい |
| userName |Windows 認証を使用しする場合は、ユーザー名を指定します。 例: **domainname\\username**。 |いいえ |
| password |ユーザー名に指定したユーザー アカウントのパスワードを指定します。 安全に保存するには、このフィールドを **SecureString** としてマークします。 また、[Azure Key Vault に格納されているシークレットを参照する](store-credentials-in-key-vault.md)こともできます。 |いいえ |
| alwaysEncryptedSettings | マネージド ID またはサービス プリンシパルを使用して、Amazon RDS for SQL Server に格納されている機密データを保護する Always Encrypted を有効にするために必要な **alwaysencryptedsettings** 情報を指定します。 詳細については、この表の後にある JSON の例および「[Always Encrypted の使用](#using-always-encrypted)」を参照してください。 指定されていない場合、既定の always encrypted 設定は無効になります。 |いいえ |
| connectVia | この[統合ランタイム](concepts-integration-runtime.md)は、データ ストアに接続するために使用されます。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

> [!NOTE]
> Amazon RDS for SQL Server の [**Always Encrypted**](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-ver15&preserve-view=true) は、データ フローではサポートされていません。 

>[!TIP]
>エラー コード "UserErrorFailedToConnectToSqlServer" および "The session limit for the database is XXX and has been reached" (データベースのセッション制限 XXX に達しました) のようなメッセージのエラーが発生する場合は、`Pooling=false` を接続文字列に追加して、もう一度試してください。

**例 1: SQL 認証を使用する**

```json
{
    "name": "AmazonSqlLinkedService",
    "properties": {
        "type": "AmazonRdsForSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例 2: Azure Key Vault 内のパスワードで SQL 認証を使用する**

```json
{
    "name": "AmazonSqlLinkedService",
    "properties": {
        "type": "AmazonRdsForSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例 3: Windows 認証を使用する**

```json
{
    "name": "AmazonSqlLinkedService",
    "properties": {
        "type": "AmazonRdsForSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例 4: Always Encrypted を使用する**

```json
{
    "name": "AmazonSqlLinkedService",
    "properties": {
        "type": "AmazonRdsForSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "alwaysEncryptedSettings": {
            "alwaysEncryptedAkvAuthType": "ServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Amazon RDS for SQL Server データセットでサポートされるプロパティの一覧を示します。

Amazon RDS for SQL Server データベースからデータをコピーするために、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**AmazonRdsForSqlServerTable** に設定する必要があります。 | はい |
| schema | スキーマの名前。 |いいえ |
| table | テーブル/ビューの名前。 |いいえ |
| tableName | スキーマがあるテーブル/ビューの名前。 このプロパティは下位互換性のためにサポートされています。 新しいワークロードでは、`schema` と `table` を使用します。 | いいえ |

**例**

```json
{
    "name": "AmazonRdsForSQLServerDataset",
    "properties":
    {
        "type": "AmazonRdsForSqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Amazon RDS for SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Amazon RDS for SQL Server ソースでサポートされるプロパティの一覧を示します。

### <a name="amazon-rds-for-sql-server-as-a-source"></a>ソースとしての Amazon RDS for SQL Server

>[!TIP]
>データ パーティション分割を使用して、Amazon RDS for SQL Server からデータを効率的に読み込む方法の詳細については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)」を参照してください。

Amazon RDS for SQL Server からデータをコピーするには、コピー アクティビティのソースの種類を **AmazonRdsForSqlServerSource** に設定します。 コピー アクティビティの source セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、**AmazonRdsForSqlServerSource** に設定する必要があります。 | はい |
| sqlReaderQuery |カスタム SQL クエリを使用してデータを読み取ります。 たとえば `select * from MyTable` です。 |いいえ |
| sqlReaderStoredProcedureName |このプロパティは、ソース テーブルからデータを読み取るストアド プロシージャの名前です。 最後の SQL ステートメントはストアド プロシージャの SELECT ステートメントにする必要があります。 |いいえ |
| storedProcedureParameters |これらのパラメーターは、ストアド プロシージャ用です。<br/>使用可能な値は、名前または値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ |
| isolationLevel | SQL ソースのトランザクション ロック動作を指定します。 使用できる値は、次のとおりです。**ReadCommitted**、**ReadUncommitted**、**RepeatableRead**、**Serializable**、**Snapshot**。 指定しなかった場合は、データベースの既定の分離レベルが使用されます。 詳細については[こちらのドキュメント](/dotnet/api/system.data.isolationlevel)をご覧ください。 | いいえ |
| partitionOptions | Amazon RDS for SQL Server からのデータの読み込みに使用されるデータ パーティション分割オプションを指定します。 <br>使用できる値は、以下のとおりです。**None** (既定値)、**PhysicalPartitionsOfTable**、および **DynamicRange**。<br>パーティション オプションが有効になっている場合 (つまり、`None` ではない場合)、Amazon RDS for SQL Server から同時にデータを読み込む並列処理の次数は、コピー アクティビティの [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 設定によって制御されます。 | いいえ |
| partitionSettings | データ パーティション分割の設定のグループを指定します。 <br>パーティション オプションが `None` でない場合に適用されます。 | いいえ |
| ***`partitionSettings` の下:*** | | |
| partitionColumnName | 並列コピーの範囲パーティション分割で使用される **整数型または日付/日時型** (`int`、`smallint`、`bigint`、`date`、`smalldatetime`、`datetime`、`datetime2`、または `datetimeoffset`) のソース列の名前を指定します。 指定されない場合は、テーブルのインデックスまたは主キーが自動検出され、パーティション列として使用されます。<br>パーティション オプションが `DynamicRange` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfDynamicRangePartitionCondition ` をフックします。 例については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)」セクションを参照してください。 | いいえ |
| partitionUpperBound | パーティション範囲の分割のための、パーティション列の最大値。 この値は、テーブル内の行のフィルター処理用ではなく、パーティションのストライドを決定するために使用されます。 テーブルまたはクエリ結果に含まれるすべての行がパーティション分割され、コピーされます。 指定されていない場合は、コピー アクティビティによって値が自動検出されます。  <br>パーティション オプションが `DynamicRange` である場合に適用されます。 例については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)」セクションを参照してください。 | いいえ |
| partitionLowerBound | パーティション範囲の分割のための、パーティション列の最小値。 この値は、テーブル内の行のフィルター処理用ではなく、パーティションのストライドを決定するために使用されます。 テーブルまたはクエリ結果に含まれるすべての行がパーティション分割され、コピーされます。 指定しない場合、コピー アクティビティによって値が自動検出されます。<br>パーティション オプションが `DynamicRange` である場合に適用されます。 例については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)」セクションを参照してください。 | いいえ |

**以下の点に注意してください。**

- **AmazonRdsForSqlServerSource** に **sqlReaderQuery** が指定されている場合、コピー アクティビティでは、データを取得するために Amazon RDS for SQL Server ソースに対してこのクエリを実行します。 **sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することもできます (ストアド プロシージャでパラメーターを使用する場合)。
- ソースのストアド プロシージャを使用してデータを取得する場合、異なるパラメーター値が渡されたときに別のスキーマを返すようにストアド プロシージャが設計されていると、UI からスキーマをインポートするときや、テーブルの自動作成を使用して SQL データベースにデータをコピーするときに、エラーが発生したり、予期しない結果になったりする可能性があります。

**例:SQL クエリを使用する**

```json
"activities":[
    {
        "name": "CopyFromAmazonRdsForSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon RDS for SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRdsForSqlServerSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**例:ストアド プロシージャの使用**

```json
"activities":[
    {
        "name": "CopyFromAmazonRdsForSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon RDS for SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRdsForSqlServerSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**ストアド プロシージャの定義**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

## <a name="parallel-copy-from-sql-database"></a>SQL データベースからの並列コピー

Amazon RDS for SQL Server コネクタでは、コピー アクティビティの際に、データを並列でコピーするための組み込みのデータ パーティション分割が提供されます。 データ パーティション分割オプションは、コピー アクティビティの **[ソース]** タブにあります。

:::image type="content" source="./media/connector-amazon-rds-for-sql-server/connector-amazon-rds-for-sql-partition-options.png" alt-text="パーティションのオプションのスクリーンショット":::

パーティション分割されたコピーを有効にすると、コピー アクティビティによって Amazon RDS for SQL Server ソースに対する並列クエリが実行され、パーティションごとにデータが読み込まれます。 並列度は、コピー アクティビティの [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 設定によって制御されます。 たとえば、`parallelCopies` を 4 に設定した場合、指定したパーティション オプションと設定に基づいて 4 つのクエリが同時に生成され、実行されます。各クエリでは、Amazon RDS for SQL Server からデータの一部を取得します。

特に、Amazon RDS for SQL Server から大量のデータを読み込む場合は、データ パーティション分割を使用した並列コピーを有効にすることをお勧めします。 さまざまなシナリオの推奨構成を以下に示します。 ファイルベースのデータ ストアにデータをコピーする場合は、複数のファイルとしてフォルダーに書き込む (フォルダー名のみを指定する) ことをお勧めします。この場合、1 つのファイルに書き込むよりもパフォーマンスが優れています。

| シナリオ                                                     | 推奨設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 物理パーティションに分割された大きなテーブル全体から読み込む。        | **パーティション オプション**: テーブルの物理パーティション。 <br><br/>実行中に、サービスによって物理パーティションが自動的に検出され、パーティションごとにデータがコピーされます。 <br><br/>テーブルに物理パーティションがあるかどうかを確認するには、[こちらのクエリ](#sample-query-to-check-physical-partition)を参照してください。 |
| 物理パーティションがなく、データ パーティション分割用の整数または日時の列がある大きなテーブル全体から読み込む。 | **パーティション オプション**: 動的範囲パーティション。<br>**パーティション列** (省略可能):データのパーティション分割に使用される列を指定します。 指定されていない場合は、主キー列が使用されます。<br/>**パーティションの上限** と **パーティションの下限** (省略可能):パーティションのストライドを決定する場合に指定します。 これは、テーブル内の行のフィルター処理用ではなく、テーブル内のすべての行がパーティション分割されてコピーされます。 指定されていない場合は、Copy アクティビティによって値が自動検出されます。最小値と最大値によっては時間がかかることがあります。 上限と下限を指定することをお勧めします。 <br><br>たとえば、パーティション列「ID」の値の範囲が 1 ～ 100 で、下限を 20 に、上限を 80 に設定し、並列コピーを 4 にした場合、サービスによって 4 つのパーティションでデータが取得されます。ID の範囲は、それぞれ、20 以下、21 ～ 50、51 ～ 80、81 以上となります。 |
| 物理パーティションがなく、データ パーティション分割用の整数列または日付/日時列がある大量のデータを、カスタム クエリを使用して読み込む。 | **パーティション オプション**: 動的範囲パーティション。<br>**クエリ**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br>**パーティション列**: データのパーティション分割に使用される列を指定します。<br>**パーティションの上限** と **パーティションの下限** (省略可能):パーティションのストライドを決定する場合に指定します。 これは、テーブル内の行のフィルター処理用ではなく、クエリ結果のすべての行がパーティション分割されてコピーされます。 指定しない場合、コピー アクティビティによって値が自動検出されます。<br><br>実行中に、サービスによって `?AdfRangePartitionColumnName` が各パーティションの実際の列名および値の範囲に置き換えられ、Amazon RDS for SQL Server に送信されます。 <br>たとえば、パーティション列「ID」の値の範囲が 1 ～ 100 で、下限を 20 に、上限を 80 に設定し、並列コピーを 4 にした場合、サービスによって 4 つのパーティションでデータが取得されます。ID の範囲は、それぞれ、20 以下、21 ～ 50、51 ～ 80、81 以上となります。 <br><br>さまざまなシナリオのサンプル クエリを次に示します。<br> 1.テーブル全体に対してクエリを実行する: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2.列の選択と追加の where 句フィルターが含まれるテーブルからのクエリ: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3.サブクエリを使用したクエリ: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4.サブクエリにパーティションがあるクエリ: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

パーティション オプションを使用してデータを読み込む場合のベスト プラクティス:

1. データ スキューを回避するため、パーティション列 (主キーや一意キーなど) には特徴のある列を選択します。 
2. テーブルに組み込みパーティションがある場合は、パフォーマンスを向上させるためにパーティション オプションとして "テーブルの物理パーティション" を使用します。    
3. Azure Integration Runtime を使用してデータをコピーする場合は、より大きな (4 より大きい) "[データ統合単位 (DIU)](copy-activity-performance-features.md#data-integration-units)" を設定すると、より多くのコンピューティング リソースを利用できます。 そこで、該当するシナリオを確認してください。
4. パーティション数は、"[コピーの並列処理の次数](copy-activity-performance-features.md#parallel-copy)" によって制御されます。この数値を大きくしすぎるとパフォーマンスが低下するため、この数値は、(DIU またはセルフホステッド IR ノードの数) x (2 から 4) に設定することをお勧めします。

**例: 複数の物理パーティションがある大きなテーブル全体から読み込む**

```json
"source": {
    "type": "AmazonRdsForSqlServerSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**例: 動的範囲パーティションを使用してクエリを実行する**

```json
"source": {
    "type": "AmazonRdsForSqlServerSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>物理パーティションを確認するためのサンプル クエリ

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

テーブルに物理パーティションがある場合、次のように、"HasPartition" は "yes" と表示されます。

:::image type="content" source="./media/connector-azure-sql-database/sql-query-result.png" alt-text="SQL クエリの結果":::

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="getmetadata-activity-properties"></a>GetMetadata アクティビティのプロパティ

プロパティの詳細については、[GetMetadata アクティビティ](control-flow-get-metadata-activity.md)に関するページを参照してください。 

## <a name="using-always-encrypted"></a>Always Encrypted の使用

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) を使用して Amazon RDS for SQL Server との間でデータをコピーする場合は、次の手順に従います。 

1. [列マスター キー (CMK)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true) を [Azure Key Vault](../key-vault/general/overview.md) に保存します。 詳細については、[Azure Key Vault を使用して Always Encrypted を構成する方法](../azure-sql/database/always-encrypted-azure-key-vault-configure.md?tabs=azure-powershell)に関する記事を参照してください

2. [列マスター キー (CMK)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true) が格納されているキー コンテナーへのアクセス権を付与します。 必要なアクセス許可については、こちらの[記事](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true#key-vaults)を参照してください。

3. リンク サービスを作成して SQL データベースに接続し、マネージド ID またはサービス プリンシパルを使用して "Always Encrypted" 機能を有効にします。 

## <a name="troubleshoot-connection-issues"></a>接続の問題のトラブルシューティング

1. リモート接続を受け入れるように、Amazon RDS for SQL Server インスタンスを構成します。 **Amazon RDS for SQL Server Management Studio** を起動し、**サーバー** を右クリックして、 **[プロパティ]** を選択します。 一覧から **[接続]** を選択し、 **[このサーバーへのリモート接続を許可する]** チェック ボックスをオンにします。

    :::image type="content" source="media/copy-data-to-from-sql-server/AllowRemoteConnections.png" alt-text="リモート接続の有効化":::

    詳細な手順については、「[remote access サーバー構成オプションの構成](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option)」をご覧ください。

2. **Amazon RDS for SQL Server 構成マネージャー** を起動します。 目的のインスタンスの **[Amazon RDS for SQL Server ネットワークの構成]** を展開し、 **[MSSQLSERVER のプロトコル]** を選択します。 右側のウィンドウにプロトコルが表示されます。 **[TCP/IP]** を右クリックして **[有効化]** を選択し、TCP/IP を有効にします。

    :::image type="content" source="./media/copy-data-to-from-sql-server/EnableTCPProptocol.png" alt-text="TCP/IP を有効にする":::

    TCP/IP プロトコルの有効化の詳細および別の方法については、「[サーバー ネットワーク プロトコルの有効化または無効化](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol)」をご覧ください。

3. 同じウィンドウで、 **[TCP/IP]** をダブルクリックして、 **[TCP/IP のプロパティ]** ウィンドウを起動します。
4. **[IP アドレス]** タブに切り替えます。下へスクロールして **[IPAll]** セクションを表示します。 **[TCP ポート]** を書き留めます。 既定値は **1433** です。
5. コンピューターに **Windows Firewall のルール** を作成し、このポート経由の受信トラフィックを許可します。 
6. **接続の確認**: 完全修飾名を使って Amazon RDS for SQL Server に接続するには、別のマシンから Amazon RDS for SQL Server Management Studio を使用します。 たとえば `"<machine>.<domain>.corp.<company>.com,1433"` です。

## <a name="next-steps"></a>次のステップ
コピー アクティビティによってソース、シンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
