---
title: Azure SQL Edge (プレビュー) で T-SQL ストリーミング ジョブを作成する
description: Azure SQL Edge (プレビュー) で Stream Analytics ジョブを作成する方法について説明します
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5e0043ebba1a317dcc6798d6be74aac051d97012
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595391"
---
# <a name="create-stream-analytics-job-in-azure-sql-edge-preview"></a>Azure SQL Edge (プレビュー) で Stream Analytics ジョブを作成する 

この記事では、Azure SQL Edge (プレビュー) で T-SQL ストリーミング ジョブを作成する方法を説明します。 SQL Edge でストリーミング ジョブを作成するには、次の手順を実行する必要があります

1. 外部ストリームの入力オブジェクトと出力オブジェクトを作成する
2. ストリーミング ジョブの作成の一部として、ストリーミング ジョブ クエリを定義する。

## <a name="configure-an-external-stream-input-and-output-object"></a>外部ストリームの入力オブジェクトと出力オブジェクトを構成する

T-SQL ストリーミングでは、SQL Server の外部データ ソース機能を使用して、ストリーミング ジョブの外部ストリームの入力と出力に関連付けるデータ ソースを定義します。 外部ストリームの入力オブジェクトまたは出力オブジェクトを作成するには、次の T-SQL コマンドが必要です。

[CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

[CREATE EXTERNAL STREAM (Transact-SQL)](#example-create-an-external-stream-object-sql-database)

さらに、SQL Edge (または SQL Server、Azure SQL) を出力ストリームとして使用する場合は、SQL Database にアクセスするための資格情報を定義するために、[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) T-SQL コマンドが必要です。

### <a name="supported-input-and-output-stream-data-sources"></a>サポートされている入力ストリームと出力ストリームのデータ ソース

現在、Azure SQL Edge でストリーム入力および出力としてサポートされているのは、次のデータ ソースのみです。

| データ ソースの種類 | 入力 | 出力 | 説明 |
|------------------|-------|--------|------------------|
| Azure IoT Edge ハブ | Y | Y | Azure IoT Edge ハブに対するストリーミング データの読み書きを行うためのデータ ソース。 Azure IoT Edge ハブの詳細については、「[IoT Edge ハブ](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)」を参照してください|
| SQL Database | N | Y | SQL Database にストリーミング データを書き込むためのデータ ソース接続。 SQL Database には、ローカルの SQL Edge データベースまたはリモートの SQL Server や Azure SQL Database を使用できます|
| Azure Blob Storage | N | Y | Azure ストレージ アカウントの BLOB にデータを書き込むためのデータ ソース。 |
| Kafka | Y | N | Kafka トピックからストリーミング データを読み取るためのデータ ソース。 このアダプターは、現在、Intel/AMD バージョンの Azure SQL Edge でのみ使用でき、ARM64 バージョンの SQL Edge では使用できません。|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>例:Azure IoT Edge ハブ用の外部ストリームの入力オブジェクトと出力オブジェクトを作成する

次の例では、Edge ハブ用の外部ストリーム オブジェクトを作成します。 Azure IoT Edge ハブ用の外部ストリームの入力および出力データ ソースを作成するには、最初に SQL 用の外部ファイル形式を作成して、読み書きされるデータのレイアウトを理解する必要もあります。

1. JSON の format 型を使用して外部ファイル形式を作成します。

    ```sql
    Create External file format InputFileFormat
    WITH (  
       format_type = JSON,
    )
    go
    ```

2. IoT Edge ハブに対する外部データ ソースを作成します。 次の T-SQL スクリプトでは、SQL Edge と同じ Docker ホストで実行されている Edge ハブへのデータ ソース接続が作成されます。

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput WITH (
    LOCATION = 'edgehub://'
    )
    go
    ```

3. IoT Edge ハブに対する外部ストリーム オブジェクトを作成します。 次の T-SQL スクリプトでは、Edge ハブ用のストリーム オブジェクトが作成されます。 Edge ハブのストリーム オブジェクトの場合、LOCATION パラメーターは、読み取り元または書き込み先である Edge ハブのトピックおよびチャネルの名前です。

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors WITH (
    DATA_SOURCE = EdgeHubInput,
    FILE_FORMAT = InputFileFormat,
    LOCATION = N'TemperatureSensors',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    go
    ```

### <a name="example-create-an-external-stream-object-sql-database"></a>例:SQL Database への外部ストリーム オブジェクトを作成する

次の例では、ローカル SQL Edge データベースに対する外部ストリーム オブジェクトを作成します。 

1. データベースにマスター キーを作成します。 これは、資格情報のシークレットの暗号化に必須です。

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. SQL Server ソースにアクセスするために、データベース スコープ資格情報を作成します。 次の例では、IDENTITY = 'username' と SECRET = 'password' を使用して、外部データ ソースに対する資格情報を作成します。

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. CREATE EXTERNAL DATA SOURCE を使用して、外部データ ソースを作成します。 次に例を示します。

    * LocalSQLOutput という名前の外部データ ソースを作成します
    * 外部データ ソースを識別します (LOCATION = '<vendor>://<server>[:<port>]'). この例では、SQL Edge のローカル インスタンスを指しています。
    * 最後に、この例では、前に作成した資格情報を使用します。

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
    LOCATION = 'sqlserver://tcp:.,1433'
    ,CREDENTIAL = SQLCredential
    )
    go
    ```

4. 外部ストリーム オブジェクトを作成します。 次の例では、データベース *MySQLDatabase* の *dbo.TemperatureMeasurements* テーブルを指す外部ストリーム オブジェクトを作成します。

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements WITH (
    DATA_SOURCE = LocalSQLOutput,
    LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>ストリーミング ジョブとストリーミング クエリを作成する

**sys.sp_create_streaming_job** システム ストアド プロシージャを使用して、ストリーミング クエリを定義し、ストリーミング ジョブを作成します。 **sp_create_streaming_job** ストアド プロシージャは 2 つのパラメーターを受け取ります

- job_name - ストリーミング ジョブの名前。 ストリーミング ジョブの名前は、インスタンス全体で一意です。
- statement - [Stream Analytics クエリ言語](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)ベースのストリーミング クエリ ステートメント。

次の例では、1 つのストリーミング クエリを使用して簡単なストリーミング ジョブを作成します。 このクエリでは、Edge ハブから入力を読み取り、データベースの *dbo.TemperatureMeasurements* に書き込みます。

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

次の例では、複数の異なるクエリを含むより複雑なストリーミング ジョブを作成します。これには、組み込み AnomalyDetection_ChangePoint 関数を使用して気温データの異常を特定するクエリが含まれます。

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly,
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>ストリーミング ジョブを開始、停止、削除、監視する

SQL Edge でストリーミング ジョブを開始するには、**sys.sp_start_streaming_job** ストアド プロシージャを実行します。 このストアド プロシージャでは、開始するストリーミング ジョブと同じものが入力として必要です。

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

SQL Edge でストリーミング ジョブを停止するには、**sys.sp_stop_streaming_job** ストアド プロシージャを実行します。 このストアド プロシージャでは、停止するストリーミング ジョブと同じものが入力として必要です。

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

SQL Edge でストリーミング ジョブをドロップ (削除) するには、**sys.sp_drop_streaming_job** ストアド プロシージャを実行します。 このストアド プロシージャでは、削除するストリーミング ジョブと同じものが入力として必要です。

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

SQL Edge でストリーミング ジョブの現在の状態を取得するには、**sys.sp_get_streaming_job** ストアド プロシージャを実行します。 このストアド プロシージャでは、削除するストリーミング ジョブと同じものが入力として必要でがあり、ストリーミング ジョブの名前と現在の状態が出力されます。

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256)
       )
)
```

ストリーミング ジョブの状態は次のいずれかです

| Status | 説明 |
|--------| ------------|
| 作成済み | ストリーミング ジョブは作成されましたが、まだ開始されていません |
| 開始中 | ストリーミング ジョブは開始フェーズになっています |
| アイドル | ストリーミング ジョブは実行中ですが、処理する入力がありません |
| 処理中 | ストリーミング ジョブは実行中であり、入力を処理しています。 この状態は、ストリーミング ジョブが正常な状態であることを示します |
| 低下しています | ストリーミング ジョブは実行中ですが、入力処理中に、致命的でない入出力のシリアル化または逆シリアル化エラーが発生しました。 入力ジョブは実行を続けますが、エラーが発生する入力は削除されます |
| 停止済み | ストリーミング ジョブは停止されました |
| 失敗 | ストリーミング ジョブは失敗しました。 これは、通常、処理中に致命的なエラーが発生したことを示します |

## <a name="next-steps"></a>次の手順

- [Azure SQL Edge (プレビュー) でストリーム ジョブに関連付けられたメタデータを表示する](streaming-catalog-views.md) 
- [外部ストリームを作成する](create-external-stream-transact-sql.md)
