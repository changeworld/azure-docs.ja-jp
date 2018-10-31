---
title: Azure Data Factory を使用して Azure SQL Database Managed Instance をコピー先またはコピー元としてデータをコピーする | Microsoft Docs
description: Azure Data Factory を使用して Azure SQL Database Managed Instance に、または Azure SQL Database Managed Instance からデータを移動する方法を説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: jingwang
ms.openlocfilehash: 87ca7c10095f12c82137b6287fbb895c97676062
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458998"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-using-azure-data-factory"></a>Azure Data Factory を使用して Azure SQL Database Managed Instance をコピー先またはコピー元としてデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure SQL Database Managed Instance をコピー先またはコピー元としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Azure SQL Database Managed Instance からのデータをサポートされる任意のソース データ ストアにコピーしたり、サポートされる任意のシンク データ ストアからのデータを Managed Instance にコピーしたりできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Azure SQL Database Managed Instance コネクタは以下の機能をサポートします。

- **SQL** または **Windows** 認証を使用したデータのコピー
- ソースとしての SQL クエリまたはストアド プロシージャを使用したデータの取得。
- シンクとして、宛先テーブルにデータを追記する、またはコピー中にカスタム ロジックを使用してストアド プロシージャを起動する。

## <a name="prerequisites"></a>前提条件

VNET にある Azure SQL Database Managed Instance からのコピー データを使用するには、そのデータベースにアクセスできる同じ VNET にセルフホステッド統合ランタイムを設定する必要があります。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、Azure SQL Database Managed Instance コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure SQL Database Managed Instance のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **SqlServer** に設定する必要があります | [はい] |
| connectionString |SQL 認証または Windows 認証を使用して、Managed Instance に接続するために必要な connectionString 情報を指定します。 以下のサンプルを参照してください。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |[はい] |
| userName |Windows 認証を使用している場合は、ユーザー名を指定します。 例: **domainname\\username**。 |いいえ  |
| password |userName に指定したユーザー アカウントのパスワードを指定します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |いいえ  |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Managed Instance と同じ VNET にセルフホステッド統合ランタイムをプロビジョニングします。 |[はい] |

>[!TIP]
>エラー コード "UserErrorFailedToConnectToSqlServer" および "The session limit for the database is XXX and has been reached." (データベースのセッション制限 XXX に達しました) のようなメッセージのエラーが発生する場合は、`Pooling=false` を接続文字列に追加して、もう一度試してください。

**例 1: SQL 認証の使用**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例 2: Windows 認証の使用**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
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

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、Azure SQL Database Managed Instance データセットでサポートされるプロパティの一覧を示します。

Azure SQL Database Managed Instance をコピー元またはコピー先としてデータをコピーするには、データセットの type プロパティを **SqlServerTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは **SqlServerTable** に設定する必要があります。 | [はい] |
| tableName |リンクされたサービスが参照するデータベース インスタンスのテーブルまたはビューの名前です。 | [はい] |

**例**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Azure SQL Database Managed Instance のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-sql-database-managed-instance-as-source"></a>ソースとしての Azure SQL Database Managed Instance

Azure SQL Database Managed Instance からデータをコピーする場合は、コピー アクティビティのソースの種類を **SqlSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **SqlSource** に設定する必要があります。 | [はい] |
| SqlReaderQuery |カスタム SQL クエリを使用してデータを読み取ります。 例: `select * from MyTable`. |いいえ  |
| sqlReaderStoredProcedureName |ソース テーブルからデータを読み取るストアド プロシージャの名前。 最後の SQL ステートメントはストアド プロシージャの SELECT ステートメントにする必要があります。 |いいえ  |
| storedProcedureParameters |ストアド プロシージャのパラメーター。<br/>使用可能な値: 名前/値ペア。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ  |

**注意する点**

- SqlSource に **sqlReaderQuery** が指定されている場合、コピー アクティビティでは、データを取得するために Managed Instance ソースに対してこのクエリを実行します。 または、**sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することができます (ストアド プロシージャでパラメーターを使用する場合)。
- "sqlReaderQuery" や "sqlReaderStoredProcedureName" プロパティを指定しない場合は、Managed Instance に対して実行するクエリ (`select column1, column2 from mytable`) を作成するために、データセット JSON の "構造" セクションで定義された列が使用されます。 データセット定義に "構造" がない場合は、すべての列がテーブルから選択されます。
- **sqlReaderStoredProcedureName** を使用する場合でも、ダミーの **tableName** プロパティをデータセット JSON に指定する必要があります。

**例: SQL クエリの使用**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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

**例: ストアド プロシージャの使用**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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

### <a name="azure-sql-database-managed-instance-as-sink"></a>シンクとしての Azure SQL Database Managed Instance

Azure SQL Database Managed Instance にデータをコピーする場合は、コピー アクティビティのシンクの種類を **SqlSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは **SqlSink** に設定する必要があります | [はい] |
| writeBatchSize |バッファー サイズが writeBatchSize に達したときに SQL テーブルにデータを挿入します。<br/>使用可能な値: 整数 (行数)。 |いいえ (既定値: 10000) |
| writeBatchTimeout |タイムアウトする前に一括挿入操作の完了を待つ時間です。<br/>使用可能な値: 期間。 例: "00:30:00" (30 分)。 |いいえ  |
| preCopyScript |コピー アクティビティでデータを Managed Instance に書き込む前に実行する SQL クエリを指定します。 これは、コピー実行ごとに 1 回だけ呼び出されます。 このプロパティを使用して、事前に読み込まれたデータをクリーンアップできます。 |いいえ  |
| sqlWriterStoredProcedureName |ソース データをターゲット テーブルに適用する方法、たとえば、独自のビジネス ロジックを使用してアップサートまたは変換を実行する方法を定義するストアド プロシージャの名前です。 <br/><br/>このストアド プロシージャは**バッチごとに呼び出される**ことに注意してください。 1 回だけ実行され、ソース データとは関係がない操作 (削除/切り詰めなど) を実行する場合は、`preCopyScript` プロパティを使用します。 |いいえ  |
| storedProcedureParameters |ストアド プロシージャのパラメーター。<br/>使用可能な値: 名前/値ペア。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ  |
| sqlWriterTableType |ストアド プロシージャで使用するテーブル型の名前を指定します。 コピー アクティビティでは、このテーブル型の一時テーブルでデータを移動できます。 その後、ストアド プロシージャのコードにより、コピーされたデータを既存のデータと結合できます。 |いいえ  |

> [!TIP]
> Azure SQL Database Managed Instance にデータをコピーすると、既定では、コピー アクティビティによりデータがシンク テーブルに付加されます。 UPSERT または追加のビジネス ロジックを実行するには、SqlSink でストアド プロシージャを使用します。 詳細については、「[SQL シンクのストアド プロシージャの呼び出し](#invoking-stored-procedure-for-sql-sink)」を参照してください。

**例 1: データの付加**

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
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**例 2: upsert でコピー中にストアド プロシージャを呼び出す**

詳細については、「[SQL シンクのストアド プロシージャの呼び出し](#invoking-stored-procedure-for-sql-sink)」を参照してください。

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
                "referenceName": "<Managed Instance output dataset name>",
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
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>ターゲット データベースの ID 列

このセクションでは、ID 列がないソース テーブルから ID 列がある対象テーブルにデータをコピーする例を示します。

**ソース テーブル**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**ターゲット テーブル**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

ターゲット テーブルには ID 列があることに注意してください。

**ソース データセット JSON の定義**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**対象データセット JSON の定義**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

ソースとターゲット テーブルには異なるスキーマがあることに注意してください (ターゲットには ID を持つ追加の列があります)。 このシナリオでは、ターゲット データセット定義で **structure** プロパティを指定する必要があります。ここでは、ID 列は含みません。

## <a name="invoking-stored-procedure-for-sql-sink"></a> SQL シンクからのストアド プロシージャの呼び出し

データの Azure SQL Database Managed Instance へのコピー時に、ユーザーが指定したストアド プロシージャを構成し、追加のパラメーターと共に呼び出すことができます。

組み込みのコピー メカニズムが目的どおり機能しない場合は、ストアド プロシージャを使用できます。 通常は、宛先テーブルでのソース データの最終挿入前に、upsert (挿入 + 更新) または追加処理 (列の結合、追加の値の検索、複数のテーブルへの挿入など) を実行する必要があるときに使用します。

次の例では、Managed Instance 内のテーブルに upsert を行うストアド プロシージャを使用する方法を示します。 入力データと、シンクの "Marketing" テーブルのそれぞれに 3 つの列 (ProfileID、State、Category) があると仮定します。 “ProfileID” 列に基づいて upsert を実行し、特定のカテゴリに対してのみ適用します。

**出力データセット**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

次のように、コピー アクティビティの SqlSink セクションを定義します。

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

データベース内で、SqlWriterStoredProcedureName と同じ名前のストアド プロシージャを定義します。 これによって指定したソースの入力データが処理され、出力テーブルに結合されます。 ストアド プロシージャ内のテーブル型のパラメーター名は、データセットで定義された "tableName" と同じにする必要があります。

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
      VALUES (source.ProfileID, source.State, source.Category)
END
```

データベースで、sqlWriterTableType と同じ名前のテーブル型を定義します。 テーブル型のスキーマは、入力データから返されるスキーマと同じにする必要があります。

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

ストアド プロシージャ機能は [テーブル値パラメーター](https://msdn.microsoft.com/library/bb675163.aspx)を利用しています。

>[!NOTE]
>ストアド プロシージャを呼び出すことで Money または Smallmoney のデータ型に書き込む場合、値が四捨五入される可能性があります。 緩和するには、Money または Smallmoney の代わりに Decimal として、TVP の対応するデータ型を指定します。 

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance のデータ型のマッピング

Azure SQL Database Managed Instance をコピー元またはコピー先としてデータをコピーするとき、次の Managed Instance のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Azure SQL Database Managed Instance のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| ビット |ブール |
| char |String、Char[] |
| date |Datetime |
| DateTime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM 属性 (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String、Char[] |
| ntext |String、Char[] |
| 数値 |Decimal |
| nvarchar |String、Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| テキスト |String、Char[] |
| time |timespan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String、Char[] |
| xml |xml |

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
