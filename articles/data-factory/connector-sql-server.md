---
title: データを SQL Server 間でコピー、および変換する
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Azure Synapse Analytics パイプラインを使用して、オンプレミスまたは Azure VM の SQL Server データベースとの間でデータをコピーおよび変換する方法について説明します。
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 575f6ff04e2e3ea006ec700e43205bce2d42c7a4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319550"
---
# <a name="copy-and-transform-data-to-and-from-sql-server-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Azure Data Factory または Azure Synapse Analytics を使用して SQL Server との間でデータをコピーおよび変換する

> [!div class="op_single_selector" title1="使用している Azure Data Factory のバージョンを選択してください:"]
> * [Version 1](v1/data-factory-sqlserver-connector.md)
> * [現在のバージョン](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory および Azure Synapse パイプラインで Copy アクティビティを使用して、SQL Server データベースとの間でデータをコピーし、Data Flow を使用して SQL Server データベースのデータを変換する方法について説明します。  詳細については、[Azure Data Factory](introduction.md) または [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) の概要記事を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

この SQL Server コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)

SQL Server データベースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 または、任意のサポートされているソース データ ストアから SQL Server データベースにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この SQL Server コネクタは以下をサポートします。

- SQL Server バージョン 2005 以降。
- SQL または Windows 認証を使用したデータのコピー。
- ソースとして、SQL クエリまたはストアド プロシージャを使用してデータを取得する。 SQL Server ソースからの並列コピーを選択することもできます。詳細については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)」セクションを参照してください。
- シンクとして、ソースのスキーマに基づいて、宛先テーブルが存在しない場合はこれを自動的に作成する。テーブルにデータを追加するか、コピー中にカスタム ロジックを使用してストアド プロシージャを呼び出す。 

[SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb) はサポートされていません。


## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-sql-server-linked-service-using-ui"></a>UI を使用して SQL Server のリンク サービスを作成する

次の手順を使用して、Azure portal UI で SQL Server のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成するスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用した新しいリンク サービスの作成を示すスクリーンショット。":::

2. SQL を検索し、SQL Server コネクタを選択します。

    :::image type="content" source="media/connector-sql-server/sql-server-connector.png" alt-text="SQL Server コネクタのスクリーンショット。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-sql-server/configure-sql-server-linked-service.png" alt-text="SQL Server のリンク サービスの構成のスクリーンショット。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

以下のセクションでは、SQL Server データベース コネクタに固有の Data Factory および Synapse パイプライン エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SQL Server のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **SqlServer** に設定する必要があります。 | はい |
| connectionString |SQL 認証または Windows 認証を使用して、SQL Server データベースに接続するために必要な **connectionString** 情報を指定します。 以下のサンプルを参照してください。<br/>また、Azure Key Vault にパスワードを格納することもできます。 それが SQL 認証である場合は、接続文字列から `password` 構成を取得します。 詳細については、この表の後にある JSON の例および「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。 |はい |
| userName |Windows 認証を使用しする場合は、ユーザー名を指定します。 例: **domainname\\username**。 |いいえ |
| password |ユーザー名に指定したユーザー アカウントのパスワードを指定します。 安全に保存するには、このフィールドを **SecureString** としてマークします。 また、[Azure Key Vault に格納されているシークレットを参照する](store-credentials-in-key-vault.md)こともできます。 |いいえ |
| alwaysEncryptedSettings | マネージド ID またはサービス プリンシパルを使用して、SQL サーバーに格納されている機密データを保護する Always Encrypted を有効にするために必要な **alwaysencryptedsettings** 情報を指定します。 詳細については、この表の後にある JSON の例および「[Always Encrypted の使用](#using-always-encrypted)」を参照してください。 指定されていない場合、既定の always encrypted 設定は無効になります。 |いいえ |
| connectVia | この[統合ランタイム](concepts-integration-runtime.md)は、データ ストアに接続するために使用されます。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

> [!NOTE]
> - SQL Server の [**Always Encrypted**](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-ver15&preserve-view=true) は、データ フローではサポートされていません。 
> - データ フローでは、Windows 認証はサポートされません。

>[!TIP]
>エラー コード "UserErrorFailedToConnectToSqlServer" および "The session limit for the database is XXX and has been reached" (データベースのセッション制限 XXX に達しました) のようなメッセージのエラーが発生する場合は、`Pooling=false` を接続文字列に追加して、もう一度試してください。

**例 1: SQL 認証を使用する**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
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
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
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
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
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
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、SQL Server データセットでサポートされるプロパティの一覧を示します。

SQL Server データベースをコピー元またはコピー先にしたデータ コピーについては、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは **SqlServerTable** に設定する必要があります。 | はい |
| schema | スキーマの名前。 |ソースの場合はいいえ、シンクの場合ははい  |
| table | テーブル/ビューの名前。 |ソースの場合はいいえ、シンクの場合ははい  |
| tableName | スキーマがあるテーブル/ビューの名前。 このプロパティは下位互換性のためにサポートされています。 新しいワークロードでは、`schema` と `table` を使用します。 | ソースの場合はいいえ、シンクの場合ははい |

**例**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
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

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SQL Server のソースおよびシンクでサポートされるプロパティの一覧を示します。

### <a name="sql-server-as-a-source"></a>ソースとしての SQL Server

>[!TIP]
>データ パーティション分割を使用して、SQL Server からデータを効率的に読み込む方法の詳細については、[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)に関するセクションを参照してください。

SQL Server からデータをコピーするには、コピー アクティビティのソースの種類を **SqlSource** に設定します。 コピー アクティビティの source セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **SqlSource** に設定する必要があります。 | はい |
| sqlReaderQuery |カスタム SQL クエリを使用してデータを読み取ります。 たとえば `select * from MyTable` です。 |いいえ |
| sqlReaderStoredProcedureName |このプロパティは、ソース テーブルからデータを読み取るストアド プロシージャの名前です。 最後の SQL ステートメントはストアド プロシージャの SELECT ステートメントにする必要があります。 |いいえ |
| storedProcedureParameters |これらのパラメーターは、ストアド プロシージャ用です。<br/>使用可能な値は、名前または値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ |
| isolationLevel | SQL ソースのトランザクション ロック動作を指定します。 使用できる値は、次のとおりです。**ReadCommitted**、**ReadUncommitted**、**RepeatableRead**、**Serializable**、**Snapshot**。 指定しなかった場合は、データベースの既定の分離レベルが使用されます。 詳細については[こちらのドキュメント](/dotnet/api/system.data.isolationlevel)をご覧ください。 | いいえ |
| partitionOptions | SQL Server からデータを読み込むときに使用するデータ パーティション分割オプションを指定します。 <br>使用できる値は、以下のとおりです。**None** (既定値)、**PhysicalPartitionsOfTable**、および **DynamicRange**。<br>パーティション オプションが有効になっている (つまり、`None` ではない) 場合、SQL Server から同時にデータを読み込む並列度は、コピー アクティビティの [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) の設定によって制御されます。 | いいえ |
| partitionSettings | データ パーティション分割の設定のグループを指定します。 <br>パーティション オプションが `None` でない場合に適用されます。 | いいえ |
| ***`partitionSettings` の下:*** | | |
| partitionColumnName | 並列コピーの範囲パーティション分割で使用される **整数型または日付/日時型** (`int`、`smallint`、`bigint`、`date`、`smalldatetime`、`datetime`、`datetime2`、または `datetimeoffset`) のソース列の名前を指定します。 指定されない場合は、テーブルのインデックスまたは主キーが自動検出され、パーティション列として使用されます。<br>パーティション オプションが `DynamicRange` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfDynamicRangePartitionCondition ` をフックします。 例については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)」セクションを参照してください。 | いいえ |
| partitionUpperBound | パーティション範囲の分割のための、パーティション列の最大値。 この値は、テーブル内の行のフィルター処理用ではなく、パーティションのストライドを決定するために使用されます。 テーブルまたはクエリ結果に含まれるすべての行がパーティション分割され、コピーされます。 指定しない場合、コピー アクティビティによって値が自動検出されます。  <br>パーティション オプションが `DynamicRange` である場合に適用されます。 例については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)」セクションを参照してください。 | いいえ |
| partitionLowerBound | パーティション範囲の分割のための、パーティション列の最小値。 この値は、テーブル内の行のフィルター処理用ではなく、パーティションのストライドを決定するために使用されます。 テーブルまたはクエリ結果に含まれるすべての行がパーティション分割され、コピーされます。 指定しない場合、コピー アクティビティによって値が自動検出されます。<br>パーティション オプションが `DynamicRange` である場合に適用されます。 例については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)」セクションを参照してください。 | いいえ |

**以下の点に注意してください。**

- **SqlSource** に **sqlReaderQuery** が指定されている場合、コピー アクティビティでは、データを取得するために SQL Server ソースに対してこのクエリを実行します。 **sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することもできます (ストアド プロシージャでパラメーターを使用する場合)。
- ソースのストアド プロシージャを使用してデータを取得する場合、異なるパラメーター値が渡されたときに別のスキーマを返すようにストアド プロシージャが設計されていると、UI からスキーマをインポートするときや、テーブルの自動作成を使用して SQL データベースにデータをコピーするときに、エラーが発生したり、予期しない結果になったりする可能性があります。

**例:SQL クエリを使用する**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
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
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
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

### <a name="sql-server-as-a-sink"></a>シンクとしての SQL Server

> [!TIP]
> サポートされる書き込み動作、構成、およびベスト プラクティスの詳細については、「[SQL Server にデータを読み込むときのベスト プラクティス](#best-practice-for-loading-data-into-sql-server)」を参照してください。

SQL Server にデータをコピーするには、コピー アクティビティのシンクの種類を **SqlSink** に設定します。 コピー アクティビティの sink セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは、**SqlSink** に設定する必要があります。 | はい |
| preCopyScript |このプロパティでは、コピー アクティビティで SQL Server にデータを書き込む前に実行する SQL クエリを指定します。 これは、コピー実行ごとに 1 回だけ呼び出されます。 このプロパティを使用して、事前に読み込まれたデータをクリーンアップできます。 |いいえ |
| tableOption | ソースのスキーマに基づいて[自動的にシンク テーブルを作成する](copy-activity-overview.md#auto-create-sink-tables)かどうかを指定します (存在しない場合)。 シンクでストアド プロシージャが指定されている場合、テーブルの自動作成はサポートされません。 使用できる値は `none` (既定値)、`autoCreate` です。 |いいえ |
| sqlWriterStoredProcedureName | ターゲット テーブルにソース データを適用する方法を定義しているストアド プロシージャの名前です。 <br/>このストアド プロシージャは *バッチごとに呼び出されます*。 1 回だけ実行され、ソース データとは関係がない操作 (削除/切り詰めなど) の場合は、`preCopyScript` プロパティを使用します。<br>例については、「[SQL シンクからのストアド プロシージャの呼び出し](#invoke-a-stored-procedure-from-a-sql-sink)」を参照してください。 | いいえ |
| storedProcedureTableTypeParameterName |ストアド プロシージャで指定されたテーブル型のパラメーター名。  |いいえ |
| sqlWriterTableType |ストアド プロシージャで使用するテーブル型の名前。 コピー アクティビティでは、このテーブル型の一時テーブルでデータを移動できます。 その後、ストアド プロシージャのコードにより、コピーされたデータを既存のデータと結合できます。 |いいえ |
| storedProcedureParameters |ストアド プロシージャのパラメーター。<br/>使用可能な値は、名前と値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 | いいえ |
| writeBatchSize |SQL テーブルに挿入する "*バッチあたりの*" 行数。<br/>使用可能な値は、行数の場合整数です。 既定では行のサイズに基づいて、サービスにより適切なバッチ サイズが動的に決定されます。 |いいえ |
| writeBatchTimeout |このプロパティは、タイムアウトする前に一括挿入操作の完了を待つ時間を指定します。<br/>使用可能な値は期間に対する値です。 たとえば "00:30:00" (30 分) を指定できます。 値を指定しなかった場合、タイムアウトの既定値は "02:00:00" です。 |いいえ |
| maxConcurrentConnections |アクティビティの実行中にデータ ストアに対して確立されたコンカレント接続数の上限。 コンカレント接続を制限する場合にのみ、値を指定します。| いいえ |

**例 1: データを追加する**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**例 2: コピー中にストアド プロシージャを呼び出す**

詳しくは、「[SQL シンクからのストアド プロシージャの呼び出し](#invoke-a-stored-procedure-from-a-sql-sink)」をご覧ください。

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-database"></a>SQL データベースからの並列コピー

SQL Server コネクタでは、コピー アクティビティの際に、データを並列でコピーするための組み込みのデータ パーティション分割が提供されます。 データ パーティション分割オプションは、コピー アクティビティの **[ソース]** タブにあります。

:::image type="content" source="./media/connector-sql-server/connector-sql-partition-options.png" alt-text="パーティションのオプションのスクリーンショット":::

パーティション分割でのコピーを有効にすると、コピー アクティビティによってユーザーの SQL Server ソースに対して並列クエリが実行され、パーティションごとにデータが読み込まれます。 並列度は、コピー アクティビティの [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 設定によって制御されます。 たとえば、`parallelCopies` を 4 に設定した場合、指定したパーティション オプションと設定に基づいて 4 つのクエリが同時に生成され、実行されます。各クエリでは、SQL Server からデータの一部を取得します。

特に、自分の SQL Server から大量のデータを読み込む場合は、データのパーティション分割を使用した並列コピーを有効にすることをお勧めします。 さまざまなシナリオの推奨構成を以下に示します。 ファイルベースのデータ ストアにデータをコピーする場合は、複数のファイルとしてフォルダーに書き込む (フォルダー名のみを指定する) ことをお勧めします。この場合、1 つのファイルに書き込むよりもパフォーマンスが優れています。

| シナリオ                                                     | 推奨設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 物理パーティションに分割された大きなテーブル全体から読み込む。        | **パーティション オプション**: テーブルの物理パーティション。 <br><br/>実行中に、サービスによって物理パーティションが自動的に検出され、パーティションごとにデータがコピーされます。 <br><br/>テーブルに物理パーティションがあるかどうかを確認するには、[こちらのクエリ](#sample-query-to-check-physical-partition)を参照してください。 |
| 物理パーティションがなく、データ パーティション分割用の整数または日時の列がある大きなテーブル全体から読み込む。 | **パーティション オプション**: 動的範囲パーティション。<br>**パーティション列** (省略可能):データのパーティション分割に使用される列を指定します。 指定されていない場合は、主キー列が使用されます。<br/>**パーティションの上限** と **パーティションの下限** (省略可能):パーティションのストライドを決定する場合に指定します。 これは、テーブル内の行のフィルター処理用ではなく、テーブル内のすべての行がパーティション分割されてコピーされます。 指定されていない場合は、Copy アクティビティによって値が自動検出されます。最小値と最大値によっては時間がかかることがあります。 上限と下限を指定することをお勧めします。 <br><br>たとえば、パーティション列「ID」の値の範囲が 1 ～ 100 で、下限を 20 に、上限を 80 に設定し、並列コピーを 4 にした場合、サービスによって 4 つのパーティションでデータが取得されます。ID の範囲は、それぞれ、20 以下、21 ～ 50、51 ～ 80、81 以上となります。 |
| 物理パーティションがなく、データ パーティション分割用の整数列または日付/日時列がある大量のデータを、カスタム クエリを使用して読み込む。 | **パーティション オプション**: 動的範囲パーティション。<br>**クエリ**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br>**パーティション列**: データのパーティション分割に使用される列を指定します。<br>**パーティションの上限** と **パーティションの下限** (省略可能):パーティションのストライドを決定する場合に指定します。 これは、テーブル内の行のフィルター処理用ではなく、クエリ結果のすべての行がパーティション分割されてコピーされます。 指定されていない場合は、コピー アクティビティによって値が自動検出されます。<br><br>実行中に、`?AdfRangePartitionColumnName` が各パーティションの実際の列名および値の範囲に置き換えられ、SQL Server に送信されます。 <br>たとえば、パーティション列「ID」の値の範囲が 1 ～ 100 で、下限を 20 に、上限を 80 に設定し、並列コピーを 4 にした場合、サービスによって 4 つのパーティションでデータが取得されます。ID の範囲は、それぞれ、20 以下、21 ～ 50、51 ～ 80、81 以上となります。 <br><br>さまざまなシナリオのサンプル クエリを次に示します。<br> 1.テーブル全体に対してクエリを実行する: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2.列の選択と追加の where 句フィルターが含まれるテーブルからのクエリ: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3.サブクエリを使用したクエリ: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4.サブクエリにパーティションがあるクエリ: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

パーティション オプションを使用してデータを読み込む場合のベスト プラクティス:

1. データ スキューを回避するため、パーティション列 (主キーや一意キーなど) には特徴のある列を選択します。 
2. テーブルに組み込みパーティションがある場合は、パフォーマンスを向上させるためにパーティション オプションとして "テーブルの物理パーティション" を使用します。    
3. Azure Integration Runtime を使用してデータをコピーする場合は、より大きな (4 より大きい) "[データ統合単位 (DIU)](copy-activity-performance-features.md#data-integration-units)" を設定すると、より多くのコンピューティング リソースを利用できます。 そこで、該当するシナリオを確認してください。
4. パーティション数は、"[コピーの並列処理の次数](copy-activity-performance-features.md#parallel-copy)" によって制御されます。この数値を大きくしすぎるとパフォーマンスが低下するため、この数値は、(DIU またはセルフホステッド IR ノードの数) x (2 から 4) に設定することをお勧めします。

**例: 複数の物理パーティションがある大きなテーブル全体から読み込む**

```json
"source": {
    "type": "SqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**例: 動的範囲パーティションを使用してクエリを実行する**

```json
"source": {
    "type": "SqlSource",
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

## <a name="best-practice-for-loading-data-into-sql-server"></a>SQL Server にデータを読み込むときのベスト プラクティス

SQL Server にデータをコピーするときは、さまざまな書き込み動作が必要になることがあります。

- [追加](#append-data): ソース データには新しいレコードのみが含まれている。
- [アップサート](#upsert-data): ソース データには挿入と更新の両方が含まれている。
- [上書き](#overwrite-the-entire-table): 毎回ディメンション テーブル全体を再度読み込みたい。
- [カスタム ロジックでの書き込み](#write-data-with-custom-logic): 宛先テーブルへの最終挿入の前に追加の処理が必要である。

構成方法とベスト プラクティスについては、対応するセクションを参照してください。

### <a name="append-data"></a>データを追加する

データの追加は、この SQL Server シンク コネクタの既定の動作です。 サービスでは、テーブルに効率的に書き込むために一括挿入が実行されます。 コピー アクティビティで、それに応じてソースとシンクを構成できます。

### <a name="upsert-data"></a>データをアップサートする

**オプション 1:** 大量のデータをコピーする場合は、コピー アクティビティを使用してすべてのレコードをステージング テーブルに一括読み込みしてから、ストアド プロシージャ アクティビティを実行して、[MERGE](/sql/t-sql/statements/merge-transact-sql) または INSERT/UPDATE ステートメントを 1 回で適用します。 

コピー アクティビティでは現在、データベース一時テーブルへのデータの読み込みはネイティブでサポートされていません。 複数のアクティビティを組み合わせて設定するための高度な方法があります。[SQL Database の一括 upsert シナリオの最適化](https://github.com/scoriani/azuresqlbulkupsert)に関するページを参照してください。 以下に、永続的テーブルをステージングとして使用する例を示します。

例として、**Copy アクティビティ** と **ストアド プロシージャ アクティビティ** を連結させたパイプラインを作成できます。 前者では、ソース ストアから SQL Server ステージング テーブル (たとえば、データセット内のテーブル名 **UpsertStagingTable**) にデータがコピーされます。 その後、後者でストアド プロシージャが呼び出され、ステージング テーブルのソース データがターゲット テーブルにマージされて、ステージング テーブルがクリーンアップされます。

:::image type="content" source="./media/connector-azure-sql-database/azure-sql-database-upsert.png" alt-text="Upsert":::

データベースで、前のストアド プロシージャ アクティビティから指し示されている、次の例に示すような MERGE ロジックを含むストアド プロシージャを定義します。 ターゲットは **Marketing** テーブルであり、そこには 3 つの列 (**ProfileID**、**State**、**Category**) があるものとします。 **ProfileID** 列に基づいて、アップサートを実行します。

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING UpsertStagingTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE UpsertStagingTable
END
```

**オプション 2:** [コピー アクティビティ内でのストアド プロシージャの呼び出し](#invoke-a-stored-procedure-from-a-sql-sink)を選択できます。 このアプローチでは、コピー アクティビティでの既定のアプローチとしての一括挿入を使用する代わりに、ソース テーブル内の (`writeBatchSize` プロパティで管理される) 各バッチを実行します。

### <a name="overwrite-the-entire-table"></a>テーブル全体を上書きする

コピー アクティビティ シンクで **preCopyScript** プロパティを構成できます。 この場合、実行される Copy アクティビティごとに、サービスで最初にスクリプトが実行されます。 次に、コピーが実行されてデータが挿入されます。 たとえば、テーブル全体を最新のデータで上書きするには、ソースから新しいデータを一括で読み込む前に、すべてのレコードを最初に削除するスクリプトを指定します。

### <a name="write-data-with-custom-logic"></a>カスタム ロジックでデータを書き込む

カスタム ロジックでデータを書き込む手順は、「[データをアップサートする](#upsert-data)」セクションで説明されている手順に似ています。 宛先テーブルへのソース データの最終挿入の前に追加の処理を適用する必要がある場合は、ステージング テーブルに読み込んだ後、ストアド プロシージャ アクティビティを呼び出すか、コピー アクティビティのシンクのストアド プロシージャを呼び出してデータを適用することができます。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> SQL シンクからのストアド プロシージャの呼び出し

SQL Server データベースにデータをコピーするときに、ユーザーが指定したストアド プロシージャを構成し、ソース テーブルの各バッチに関する追加のパラメーターと共に呼び出すこともできます。 ストアド プロシージャ機能は [テーブル値パラメーター](/dotnet/framework/data/adonet/sql/table-valued-parameters)を利用しています。  このサービスでは、ストアド プロシージャがそれ自体のトランザクションでラップされることにご留意ください。つまり、ストアド プロシージャ内で作成されたトランザクションはすべて入れ子になり、例外処理を意味することがあります。

組み込みのコピー メカニズムでは目的を達成できない場合は、ストアド プロシージャを使用できます。 1 つの例は、宛先テーブルへのソース データの最終挿入の前に追加の処理を適用する場合です。 その他の処理の例をいくつか挙げると、列のマージ、追加の値の検索、複数のテーブルへの挿入があります。

次の例では、SQL Server データベース内のテーブルに upsert を行うストアド プロシージャを使用する方法を示します。 入力データとシンクの **Marketing** テーブルには、それぞれ 3 つの列 (**ProfileID**、**State**、**Category**) があるものとします。 **ProfileID** 列に基づいてアップサートを行い、"ProductA" という特定のカテゴリに対してのみ適用します。

1. データベースで、**sqlWriterTableType** と同じ名前のテーブル型を定義します。 テーブル型のスキーマは、入力データから返されるスキーマと同じです。

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. データベース内で、**sqlWriterStoredProcedureName** と同じ名前のストアド プロシージャを定義します。 これによって指定したソースの入力データが処理され、出力テーブルにマージされます。 ストアド プロシージャ内のテーブル型のパラメーター名は、データセットで定義されている **tableName** と同じです。

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. コピー アクティビティの **SQL シンク** セクションを次のように定義します。

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

マッピング データ フローでデータを変換する場合、SQL Server Database からテーブルの読み取りと書き込みを実行できます。 詳細については、マッピング データ フローの[ソース変換](data-flow-source.md)と[シンク変換](data-flow-sink.md)に関する記事をご覧ください。

> [!NOTE]
> オンプレミスの SQL Server にアクセスするには、プライベート エンドポイントを使用して、Azure Data Factory または Synapse ワークスペースの[マネージド仮想ネットワーク](managed-virtual-network-private-endpoint.md)を使用する必要があります。 詳細な手順については、この[チュートリアル](tutorial-managed-virtual-network-on-premise-sql-server.md)を参照してください。

### <a name="source-transformation"></a>ソース変換

次の表に、SQL Server ソースでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[ソース オプション]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| テーブル | [テーブル] を入力として選択した場合、データセットで指定されたテーブルからすべてのデータがデータ フローによってフェッチされます。 | いいえ | - |- |
| クエリ | [クエリ] を入力として選択した場合は、ソースからデータをフェッチする SQL クエリを指定します。これにより、データセットで指定したテーブルがオーバーライドされます。 テストまたはルックアップ対象の行を減らすうえで、クエリの使用は有効な手段です。<br><br>**Order By** 句はサポートされていませんが、完全な SELECT FROM ステートメントを設定することができます。 ユーザー定義のテーブル関数を使用することもできます。 **select * from udfGetData()** は、データ フローで使用できるテーブルを返す SQL の UDF です。<br>クエリの例: `Select * from MyTable where customerId > 1000 and customerId < 2000`| いいえ | String | query |
| バッチ サイズ | 大量データを読み取りにまとめるバッチ サイズを指定します。 | いいえ | Integer | batchSize |
| Isolation Level | 次のいずれかの分離レベルを選択します。<br>- コミットされたものを読み取り<br>- コミットされていないものを読み取り (既定値)<br>- 反復可能読み取り<br>- シリアル化可能<br>- なし (分離レベルを無視) | いいえ | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>NONE</small> |isolationLevel |

#### <a name="sql-server-source-script-example"></a>SQL Server ソース スクリプトの例

ソースの種類として SQL Server を使用すると、関連付けられているデータ フロー スクリプトは次のようになります。

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from MYTABLE',
    format: 'query') ~> SQLSource
```

### <a name="sink-transformation"></a>シンク変換

次の表に、SQL Server シンクでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[シンク オプション]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 更新方法 | 対象となるデータベースに対して許可される操作を指定します。 既定では、挿入のみが許可されます。<br>行を更新、アップサート、または削除するには、それらのアクションに対して行をタグ付けするために[行の変更変換](data-flow-alter-row.md)が必要になります。 | はい | `true` または `false` | deletable <br/>insertable <br/>updateable <br/>upsertable |
| [キー列] | 更新、upsert、削除の場合、キー列 (複数可) を設定して、変更する行を決定する必要があります。<br>キーとして選択する列の名前は、後続の更新、upsert、削除の一部として使用されます。 そのため、シンク マッピングに存在する列を選択する必要があります。 | いいえ | Array | キー |
| Skip writing key columns\(キー列の書き込みをスキップする) | キー列に値を書き込まない場合は、[Skip writing key columns]\(キー列の書き込みをスキップする\) を選択します。 | いいえ | `true` または `false` | skipKeyWrites |
| テーブル アクション |書き込み前に変換先テーブルのすべての行を再作成するか削除するかを指定します。<br>- **なし**: テーブルに対してアクションは実行されません。<br>- **Recreate**:テーブルが削除され、再作成されます。 新しいテーブルを動的に作成する場合に必要です。<br>- **Truncate**:ターゲット テーブルのすべての行が削除されます。 | いいえ | `true` または `false` | recreate<br/>truncate |
| バッチ サイズ | 各バッチで書き込まれる行の数を指定します。 バッチ サイズを大きくすると、圧縮とメモリの最適化が向上しますが、データをキャッシュする際にメモリ不足の例外が発生するリスクがあります。 | いいえ | Integer | batchSize |
| 事前および事後の SQL スクリプト | データがシンク データベースに書き込まれる前 (前処理) と書き込まれた後 (後処理) に実行される複数行の SQL スクリプトを指定します。 | いいえ | String | preSQLs<br>postSQLs |

#### <a name="sql-server-sink-script-example"></a>SQL Server シンク スクリプトの例

シンクの種類として SQL Server を使用すると、関連付けられているデータ フロー スクリプトは次のようになります。

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SQLSink
```

## <a name="data-type-mapping-for-sql-server"></a>SQL Server のデータ型のマッピング

SQL Server との間でデータをコピーするとき、SQL Server のデータ型から Azure Data Factory の中間データ型への、以下のマッピングが使用されます。 Data Factory を実装する Synapse パイプラインでは、同じマッピングが使用されます。  コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| SQL Server のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |ブール型 |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal (10 進数型) |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| INT |Int32 |
| money |Decimal (10 進数型) |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal (10 進数型) |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal (10 進数型) |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| UNIQUEIDENTIFIER |GUID |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |String |

>[!NOTE]
> 10 進の中間型にマップされるデータ型の場合、コピー アクティビティでは、現在、最大 28 の有効桁数がサポートされています。 28 よりも大きな有効桁数を必要とするデータがある場合は、SQL クエリで文字列に変換することを検討してください。

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="getmetadata-activity-properties"></a>GetMetadata アクティビティのプロパティ

プロパティの詳細については、[GetMetadata アクティビティ](control-flow-get-metadata-activity.md)に関するページを参照してください。 

## <a name="using-always-encrypted"></a>Always Encrypted の使用

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) を使用して SQL Server との間でデータをコピーする場合は、次の手順に従います。 

1. [列マスター キー (CMK)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true) を [Azure Key Vault](../key-vault/general/overview.md) に保存します。 詳細については、[Azure Key Vault を使用して Always Encrypted を構成する方法](../azure-sql/database/always-encrypted-azure-key-vault-configure.md?tabs=azure-powershell)に関する記事を参照してください

2. [列マスター キー (CMK)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true) が格納されているキー コンテナーへのアクセス権を付与します。 必要なアクセス許可については、こちらの[記事](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted?view=sql-server-ver15&preserve-view=true#key-vaults)を参照してください。

3. リンク サービスを作成して SQL データベースに接続し、マネージド ID またはサービス プリンシパルを使用して "Always Encrypted" 機能を有効にします。 


>[!NOTE]
>SQL Server の [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) では、次のシナリオがサポートされています。 
>1. ソース データ ストアまたはシンク データ ストアのいずれかで、キー プロバイダー認証の種類としてマネージド ID またはサービス プリンシパルを使用する。
>2. ソース データ ストアとシンク データ ストアの両方で、キー プロバイダー認証の種類としてマネージド ID を使用する。
>3. ソース データ ストアとシンク データ ストアの両方で、キー プロバイダー認証の種類として同じサービス プリンシパルを使用する。

## <a name="troubleshoot-connection-issues"></a>接続の問題のトラブルシューティング

1. リモート接続を受け付けるように、SQL Server インスタンスを構成します。 **SQL Server Management Studio** を開始し、**サーバー** を右クリックして、 **[プロパティ]** を選択します。 一覧から **[接続]** を選択し、 **[このサーバーへのリモート接続を許可する]** チェック ボックスをオンにします。

    :::image type="content" source="media/copy-data-to-from-sql-server/AllowRemoteConnections.png" alt-text="リモート接続の有効化":::

    詳細な手順については、「[remote access サーバー構成オプションの構成](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option)」をご覧ください。

2. **SQL Server 構成マネージャー** を開始します。 目的のインスタンスの **[SQL Server ネットワークの構成]** を展開し、 **[MSSQLSERVER のプロトコル]** を選択します。 右側のウィンドウにプロトコルが表示されます。 **[TCP/IP]** を右クリックして **[有効化]** を選択し、TCP/IP を有効にします。

    :::image type="content" source="./media/copy-data-to-from-sql-server/EnableTCPProptocol.png" alt-text="TCP/IP を有効にする":::

    TCP/IP プロトコルの有効化の詳細および別の方法については、「[サーバー ネットワーク プロトコルの有効化または無効化](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol)」をご覧ください。

3. 同じウィンドウで、 **[TCP/IP]** をダブルクリックして、 **[TCP/IP のプロパティ]** ウィンドウを起動します。
4. **[IP アドレス]** タブに切り替えます。下へスクロールして **[IPAll]** セクションを表示します。 **[TCP ポート]** を書き留めます。 既定値は **1433** です。
5. コンピューターに **Windows Firewall のルール** を作成し、このポート経由の受信トラフィックを許可します。 
6. **接続の確認**: 完全修飾名を使って SQL Server に接続するには、別のコンピューターから SQL Server Management Studio を使用します。 たとえば `"<machine>.<domain>.corp.<company>.com,1433"` です。

## <a name="next-steps"></a>次のステップ
コピー アクティビティによってソース、シンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
