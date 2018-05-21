---
title: Data Factory を使用して SQL Database をコピー先またはコピー元としてデータをコピーする | Microsoft Docs
description: Data Factory を使用して、サポートされるソース データ ストアから Azure SQL Database にデータをコピーしたり、SQL Database からサポートされるシンク ストアにコピーしたりする方法を説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2018
ms.author: jingwang
ms.openlocfilehash: 0503b355089fe6bbcc7632ac93fd21e71f268032
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Azure Data Factory を使用した Azure SQL Database との間でのデータのコピー
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-azure-sql-connector.md)
> * [バージョン 2 - プレビュー](connector-azure-sql-database.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure SQL Database をコピー先またはコピー元としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 の Azure SQL Database コネクタ](v1/data-factory-azure-sql-connector.md)に関する記事を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

サポートされる任意のソース データ ストアのデータを、Azure SQL Database にコピーしたり、Azure SQL Database のデータを、サポートされる任意のシンク データ ストアにコピーしたりできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この SQL Database コネクタは以下をサポートします。

- **SQL 認証**を使って、およびサービス プリンシパルまたは管理対象サービス ID (MSI) で **Azure Active Directory アプリケーション トークン認証**を使って、データをコピーする。
- ソースとしての SQL クエリまたはストアド プロシージャを使用したデータの取得。
- シンクとして、宛先テーブルにデータを追記する、またはコピー中にカスタム ロジックを使用してストアド プロシージャを起動する。

> [!IMPORTANT]
> Azure Integration Runtime を使ってデータをコピーする場合は、[Azure サービスがサーバーにアクセスするのを許可する](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)ように [Azure SQL Server ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)を構成します。 セルフホステッド Integration Runtime を使用してデータをコピーする場合は、Azure SQL Database への接続に使われるコンピューターの IP アドレスを含む適切な IP 範囲を許可するように、Azure SQL Server ファイアウォールを構成します。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、Azure SQL Database コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure SQL Database のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | [説明] | 必須 |
|:--- |:--- |:--- |
| 型 | type プロパティを **AzureSqlDatabase** | [はい] |
| connectionString |connectionString プロパティの Azure SQL Database インスタンスに接続するために必要な情報を指定します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |[はい] |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | サービス プリンシパルで AAD 認証を使う場合は、はい。 |
| servicePrincipalKey | アプリケーションのキーを取得します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | サービス プリンシパルで AAD 認証を使う場合は、はい。 |
| テナント | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure Portal の右上隅をマウスでポイントすることにより取得できます。 | サービス プリンシパルで AAD 認証を使う場合は、はい。 |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイム (データ ストアがプライベート ネットワークにある場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ  |

さまざまな認証の種類の前提条件と JSON サンプルについては、以下のセクションをご覧ください。

- [SQL 認証の使用](#using-sql-authentication)
- [AAD アプリケーション トークン認証の使用 - サービス プリンシパル](#using-service-principal-authentication)
- [AAD アプリケーション トークン認証の使用 - 管理対象サービス ID](#using-managed-service-identity-authentication)

### <a name="using-sql-authentication"></a>SQL 認証の使用

**SQL 認証を使用するリンクされたサービスの例:**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-service-principal-authentication"></a>サービス プリンシパル認証の使用

サービス プリンシパル ベースの AAD アプリケーション トークン認証を使うには、以下の手順のようにします。

1. **[Azure Portal から Azure Active Directory アプリケーションを作成します](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)。**  リンクされたサービスを定義するときに使うアプリケーション名と次の値を記録しておきます。

    - アプリケーション ID
    - アプリケーション キー
    - テナント ID

2. まだ行っていない場合は、Azure Portal で Azure SQL Server の **[Azure Active Directory 管理者をプロビジョニングします](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**。 AAD 管理者は AAD ユーザーまたは AAD グループである必要がありますが、サービス プリンシパルであることはできません。 このステップは、後続のステップで AAD ID を使ってサービス プリンシパルの包含データベース ユーザーを作成できるようにするために行われます。

3. SSMS などのツールを使ってデータをコピーするデータベースに接続し、少なくとも ALTER ANY USER アクセス許可を持つ AAD ID を使って、次の T-SQL を実行することにより、**サービス プリンシパルの包含データベース ユーザーを作成**します。 包含データベース ユーザーについて詳しくは、[こちら](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)をご覧ください。
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. SQL ユーザーに対する通常の方法 (たとえば次のコマンドの実行) で、**サービス プリンシパルに必要なアクセス許可を付与**します。

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. ADF で、Azure SQL Database のリンクされたサービスを構成します。


**サービス プリンシパル認証を使うリンクされたサービスの例:**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-managed-service-identity-authentication"></a>管理対象のサービス ID の認証の使用

データ ファクトリは、この特定のデータ ファクトリを表す[管理対象のサービス ID (MSI)](data-factory-service-identity.md) に関連付けることができます。 Azure SQL Database 認証にこのサービス ID を使ことができ、そうすると、この指定されたファクトリは、データベースとの間で双方向にアクセスしたりデータをコピーしたりできるようになります。

MSI ベースの AAD アプリケーション トークン認証を使うには、以下の手順のようにします。

1. **Azure AD にグループを作成し、ファクトリの MSI をそのグループのメンバーにします**。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 Azure Portal でデータ ファクトリのサービス ID を調べます。 データ ファクトリの [プロパティ] で **[サービス ID]** をコピーします。

    b. [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) モジュールをインストールし、`Connect-AzureAD` コマンドを使ってサインインし、次のコマンドを実行してグループを作成し、データ ファクトリの MSI をメンバーとして追加します。
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. まだ行っていない場合は、Azure Portal で Azure SQL Server の **[Azure Active Directory 管理者をプロビジョニングします](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**。 AAD 管理者には、AAD ユーザーまたは AAD グループを指定できます。 グループに MSI と管理者ロールを付与する場合は、管理者は DB へのフル アクセス権を持っているので、以下のステップ 3 と 4 をスキップします。

3. SSMS などのツールを使ってデータをコピーするデータベースに接続し、少なくとも ALTER ANY USER アクセス許可を持つ AAD ID を使って、次の T-SQL を実行することにより、**AAD グループの包含データベース ユーザーを作成**します。 包含データベース ユーザーについて詳しくは、[こちら](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)をご覧ください。
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. SQL ユーザーに対する通常の方法 (たとえば次のコマンドの実行) で、**AAD グループに必要なアクセス許可を付与**します。

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

5. ADF で、Azure SQL Database のリンクされたサービスを構成します。

**MSI 認証を使うリンクされたサービスの例:**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、Azure SQL Database データセットでサポートされるプロパティの一覧を示します。

Azure SQL Database をコピー元またはコピー先としてデータをコピーするには、データセットの type プロパティを **AzureSqlTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | [説明] | 必須 |
|:--- |:--- |:--- |
| 型 | データセットの type プロパティは、**AzureSqlTable** に設定する必要があります。 | [はい] |
| tableName |リンクされたサービスが参照する Azure SQL Database インスタンスのテーブルまたはビューの名前です。 | [はい] |

**例:**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Azure SQL Database のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-sql-database-as-source"></a>ソースとしての Azure SQL Database

Azure SQL Database からデータをコピーする場合は、コピー アクティビティのソースの種類を **SqlSource** を設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | [説明] | 必須 |
|:--- |:--- |:--- |
| 型 | コピー アクティビティのソースの type プロパティを **SqlSource** に設定する必要があります。 | [はい] |
| SqlReaderQuery |カスタム SQL クエリを使用してデータを読み取ります。 例: `select * from MyTable`. |いいえ  |
| sqlReaderStoredProcedureName |ソース テーブルからデータを読み取るストアド プロシージャの名前。 最後の SQL ステートメントはストアド プロシージャの SELECT ステートメントにする必要があります。 |いいえ  |
| storedProcedureParameters |ストアド プロシージャのパラメーター。<br/>使用可能な値: 名前/値ペア。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ  |

**注意する点:**

- SqlSource に **sqlReaderQuery** が指定されている場合、コピー アクティビティでは、データを取得するために Azure SQL Database ソースに対してこのクエリを実行します。 または、**sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することができます (ストアド プロシージャでパラメーターを使用する場合)。
- sqlReaderQuery や sqlReaderStoredProcedureName を指定しない場合は、Azure SQL Database に対して実行するクエリ (`select column1, column2 from mytable`) を作成するために、データセット JSON の構造セクションで定義された列が使用されます。 データセット定義に "構造" がない場合は、すべての列がテーブルから選択されます。
- **sqlReaderStoredProcedureName** を使用する場合でも、ダミーの **tableName** プロパティをデータセット JSON に指定する必要があります。

**例: SQL クエリの使用**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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

**ストアド プロシージャの定義:**

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

### <a name="azure-sql-database-as-sink"></a>シンクとしての Azure SQL Database

Azure SQL Database にデータをコピーする場合は、コピー アクティビティのシンクの種類を **SqlSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | [説明] | 必須 |
|:--- |:--- |:--- |
| 型 | コピー アクティビティのシンクの type プロパティは **SqlSink** に設定する必要があります | [はい] |
| writeBatchSize |バッファー サイズが writeBatchSize に達したときに SQL テーブルにデータを挿入します。<br/>使用可能な値: 整数 (行数)。 |いいえ (既定値は 10000) |
| writeBatchTimeout |タイムアウトする前に一括挿入操作の完了を待つ時間です。<br/>使用可能な値: 期間。 例: "00:30:00" (30 分)。 |いいえ  |
| preCopyScript |コピー アクティビティがデータを Azure SQL Database に書き込む前に実行する SQL クエリを指定します。 これは、コピー実行ごとに 1 回だけ呼び出されます。 このプロパティを使用して、事前に読み込まれたデータをクリーンアップできます。 |いいえ  |
| sqlWriterStoredProcedureName |ソース データをターゲット テーブルに適用する方法、たとえば、独自のビジネス ロジックを使用してアップサートまたは変換を実行する方法を定義するストアド プロシージャの名前です。 <br/><br/>このストアド プロシージャは**バッチごとに呼び出される**ことに注意してください。 1 回だけ実行され、ソース データとは関係がない操作 (削除/切り詰めなど) を実行する場合は、`preCopyScript` プロパティを使用します。 |いいえ  |
| storedProcedureParameters |ストアド プロシージャのパラメーター。<br/>使用可能な値: 名前/値ペア。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ  |
| sqlWriterTableType |ストアド プロシージャで使用するテーブル型の名前を指定します。 コピー アクティビティでは、このテーブル型の一時テーブルでデータを移動できます。 その後、ストアド プロシージャのコードにより、コピーされたデータを既存のデータと結合できます。 |いいえ  |

> [!TIP]
> Azure SQL Database にデータをコピーすると、既定では、コピー アクティビティによりデータがシンク テーブルに付加されます。 UPSERT または追加のビジネス ロジックを実行するには、SqlSink でストアド プロシージャを使用します。 詳細については、「[SQL シンクのストアド プロシージャの呼び出し](#invoking-stored-procedure-for-sql-sink)」を参照してください。

**例 1: データの付加**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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

**ソース テーブル:**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**対象テーブル:**

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
        "type": " AzureSqlTable",
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
        "type": "AzureSqlTable",
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

データの Azure SQL Database へのコピー時に、ユーザーが指定したストアド プロシージャを構成し、追加のパラメーターと共に呼び出すことができます。

組み込みのコピー メカニズムが目的どおり機能しない場合は、ストアド プロシージャを使用できます。 通常は、宛先テーブルでのソース データの最終挿入前に、upsert (挿入 + 更新) または追加処理 (列の結合、追加の値の検索、複数のテーブルへの挿入など) を実行する必要があるときに使用します。

次の例では、Azure SQL Database 内のテーブルに upsert を行うストアド プロシージャを使用する方法を示します。 入力データと、シンクの "Marketing" テーブルのそれぞれに 3 つの列 (ProfileID、State、Category) があると仮定します。 “ProfileID” 列に基づいて upsert を実行し、特定のカテゴリに対してのみ適用します。

**出力データセット**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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

データベース内で、SqlWriterStoredProcedureName と同じ名前のストアド プロシージャを定義します。 これによって指定したソースの入力データが処理され、出力テーブルに結合されます。 ストアド プロシージャのパラメーター名は、データセットで定義された "tableName" と同じにする必要があります。

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

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL Database のデータ型のマッピング

Azure SQL Database をコピー元またはコピー先としてデータをコピーするとき、次の Azure SQL Database のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Azure SQL Database のデータ型 | Data Factory の中間データ型 |
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
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String、Char[] |
| xml |xml |

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。