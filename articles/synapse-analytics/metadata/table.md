---
title: 共有メタデータ テーブル
description: Azure Synapse Analytics には共有メタデータ モデルが用意されていて、サーバーレス Apache Spark プールでテーブルを作成することにより、データを複製しなくても、サーバーレス SQL プールと専用 SQL プールからそのテーブルにアクセスできるようになります。
services: sql-data-warehouse
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 10/13/2021
author: ma77b
ms.author: maburd
ms.reviewer: wiassaf
ms.custom: devx-track-csharp
ms.openlocfilehash: 7bb66b9fedb0f1e906a522f393ffde32ee9e2e3e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263175"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Azure Synapse Analytics の共有メタデータ テーブル


Azure Synapse Analytics では、さまざまなワークスペース計算エンジンが、Apache Spark プールとサーバーレス SQL プールの間でデータベースとテーブルを共有できます。

Spark ジョブによってデータベースが作成されると、Spark を使用してその内部にテーブルを作成できます (ストレージ形式として Parquet または CSV を使用)。 テーブル名は小文字に変換されるため、小文字の名前を使用してクエリを実行する必要があります。 これらのテーブルではすぐに、任意の Azure Synapse ワークスペース Spark プールによってクエリを実行できるようになります。 これらは、アクセス許可の対象となる任意の Spark ジョブから使用することもできます。

Spark で作成、管理される外部テーブルは、サーバーレス SQL プールの対応する同期済みデータベースと同じ名前の外部テーブルとして使用することもできます。 テーブルの同期については、「[SQL での Spark テーブルの公開](#expose-a-spark-table-in-sql)」で詳しく説明します。

テーブルはサーバーレス SQL プールに非同期的に同期されるため、表示されるまでにわずかな遅延が発生します。

## <a name="manage-a-spark-created-table"></a>Spark で作成されたテーブルの管理

Spark を使用して、Spark で作成されたデータベースを管理します。 たとえば、サーバーレス Apache Spark プール ジョブを使用してそれを削除したり、Spark からその内部にテーブルを作成したりします。

同期されたデータベース内のオブジェクトは、サーバーレス SQL プールから変更できません。

## <a name="expose-a-spark-table-in-sql"></a>SQL での Spark テーブルの公開

### <a name="shared-spark-tables"></a>共有 Spark テーブル

Spark には、Azure Synapse によって自動的に SQL で公開されるテーブルが 2 種類あります。

- マネージド テーブル

  Spark には、テキスト、CSV、JSON、JDBC、PARQUET、ORC、HIVE、DELTA、LIBSVM など、マネージド テーブルにデータを格納するためのオプションが多数用意されています。 これらのファイルは通常、マネージド テーブル データが格納される `warehouse` ディレクトリに格納されます。

- 外部テーブル

  Spark では、`LOCATION` オプションを指定するか、Hive 形式を使用することにより、既存のデータに対して外部テーブルを作成することもできます。 このような外部テーブルは、Parquet などのさまざまなデータ形式にできます。

Azure Synapse では現在、SQL エンジンを使用して Parquet または CSV 形式でデータを格納するマネージドおよび外部 Spark テーブルのみが共有されます。 他の形式に基づくテーブルは自動的に同期されません。 SQL エンジンがテーブルの基になる形式をサポートしている場合は、自分の SQL データベースの外部テーブルとして、このようなテーブルを明示的に同期することができます。

> [!NOTE] 
> 現時点では、Parquet と CSV 形式だけがサーバーレス SQL プールに同期されます。 Delta テーブルでスナップショットのストレージ形式として Parquet が使用されている場合でも、Spark デルタ テーブルのメタデータは SQL エンジンと同期されません。 Spark の外部テーブルは、現在、専用 SQL プール データベースに同期されません。

### <a name="share-spark-tables"></a>Spark テーブルを共有する

次のプロパティを使用して、SQL エンジンで外部テーブルとして公開される共有可能なマネージドおよび外部 Spark テーブル:

- SQL 外部テーブルのデータ ソースは、Spark テーブルの場所フォルダーを表すデータ ソースです。
- SQL 外部テーブルのファイル形式は Parquet または CSV です。
- SQL 外部テーブルのアクセス資格情報はパススルーです。

すべての Spark テーブル名は有効な SQL テーブル名であり、すべての Spark 列名は有効な SQL 列名であるため、SQL 外部テーブルには Spark テーブルおよび列の名前が使用されます。

Spark テーブルのデータ型は、Synapse SQL エンジンのものとは異なります。 次の表は、Spark テーブルのデータ型を SQL 型に対応させた対照表です。

| Spark データ型 | SQL データ型 | 説明 |
|---|---|---|
| `LongType`, `long`, `bigint`                | `bigint`              | **Spark**: *LongType* は 8 バイト符号付き整数値を表します。<BR>**SQL**: 「[int、bigint、smallint、および tinyint](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)」を参照してください。|
| `BooleanType`, `boolean`                    | `bit` (Parquet)、`varchar(6)` (CSV)  | **Spark**: ブール値。<BR>**SQL**: [/sql/t-sql/data-types/bit-transact-sql) を参照してください。|
| `DecimalType`, `decimal`, `dec`, `numeric`  | `decimal`             | **Spark**: *DecimalType* は、任意の有効桁数の符号付き 10 進数を表します。 java.math.BigDecimal で内部的にサポートされています。 BigDecimal は、任意の有効桁数の小数点以下なしの整数値と、小数点以下保持の 32 ビットの整数値で構成されます。 <br> **SQL**: 固定長の有効桁数と小数点以下保持の数値です。 最大有効桁数を使用した場合、有効値は - 10^38 +1 から 10^38 - 1 です。 decimal の ISO のシノニムは、dec および dec(p, s) です。 numeric は機能的には decimal と同じです。 「[decimal 型と numeric 型](/sql/t-sql/data-types/decimal-and-numeric-transact-sql)」を参照してください。 |
| `IntegerType`, `Integer`, `int`             | `int`                 | **Spark** *IntegerType* は 4 バイト符号付き整数値を表します。 <BR>**SQL**: 「[int、bigint、smallint、および tinyint](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)」を参照してください。|
| `ByteType`, `Byte`, `tinyint`               | `smallint`            | **Spark**: *ByteType* は 1 バイト符号付き整数値 [-128 から 127] を表し、ShortType は 2 バイト符号付き整数値 [-32768 から 32767] を表します。 <br> **SQL**: tinyint は 1 バイト符号付き整数値 [0, 255] を表し、smallint は 2 バイト符号付き整数値 [-32768, 32767] を表します。 「[int、bigint、smallint、および tinyint](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)」を参照してください。|
| `ShortType`, `Short`, `smallint`            | `smallint`            | 上記と同じです。 |
| `DoubleType`, `Double`                      | `float`               | **Spark**: *DoubleType* は 8 バイト倍精度浮動小数点数を表します。 **SQL**: 「[float 型と real 型](/sql/t-sql/data-types/float-and-real-transact-sql)」を参照してください。|
| `FloatType`, `float`, `real`                | `real`                | **Spark**: *FloatType* は 4 バイト倍精度浮動小数点数を表します。 **SQL**: 「[float 型と real 型](/sql/t-sql/data-types/float-and-real-transact-sql)」を参照してください。|
| `DateType`, `date`                          | `date`                | **Spark**: *DateType* は、タイムゾーンを指定せずに、フィールドの値 (年、月、日) を構成する値を表します。<BR>**SQL**: 「[date](/sql/t-sql/data-types/date-transact-sql)」を参照してください。|
| `TimestampType`, `timestamp`                | `datetime2`           | **Spark**: *TimestampType* は、セッションのローカル タイムゾーンを指定して、フィールドの値 (年、月、日、時、分、秒) を構成する値を表します。 タイムスタンプ値は、絶対的な特定の時点を表します。<BR>**SQL**: 「[datetime2](/sql/t-sql/data-types/datetime2-transact-sql)」を参照してください。 |
| `char`                                      | `char`                |
| `StringType`, `String`, `varchar`           | `Varchar(n)`          | **Spark**: *StringType* は文字列値を表します。 *VarcharType(n)* は、長さの制限がある StringType のバリアントです。 入力文字列が長さの制限を超えた場合、データの書き込みは失敗します。 この型は、関数または演算子ではなく、テーブル スキーマでのみ使用できます。<br> *CharType(n)* は、固定長の *VarcharType(n)* のバリアントです。 *CharType(n)* 型の列を読み取ると、常に長さ n の文字列値が返されます。 *CharType(n)* の列の比較では、短い方が長い方の長さまで埋め込まれます。 <br> **SQL**: Spark から提供されている長さがある場合、*varchar(n)* の n はその長さに設定されます。 パーティション分割された列の場合、n は最大 2048 になります。 それ以外の場合は、*varchar(max)* になります。 「[char および varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql)」を参照してください。<br> 照合順序 `Latin1_General_100_BIN2_UTF8` と共に使用します。 |
| `BinaryType`, `binary`                      | `varbinary(n)`        | **SQL**: Spark から提供されている長さがある場合、*Varbinary(n)* の `n` はその長さに設定されます。 パーティション分割された列の場合、n は最大 2048 になります。 それ以外の場合は、*Varbinary(max)* になります。  「[binary と varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)」を参照してください。|
| `array`, `map`, `struct`                    | `varchar(max)`        | **SQL**: 照合順序 `Latin1_General_100_BIN2_UTF8` を使用して JSON にシリアル化します。 [JSON データ](/sql/relational-databases/json/json-data-sql-server)に関するページを参照してください。|

>[!NOTE]
>データベース レベルの照合順序は `Latin1_General_100_CI_AS_SC_UTF8` です。

## <a name="security-model"></a>セキュリティ モデル

Spark データベースおよびテーブルは、SQL エンジン内のそれらの同期された表現と同様に、基になるストレージ レベルで保護されます。 現在、それらにはオブジェクト自体に対するアクセス許可がないため、オブジェクト エクスプローラーでオブジェクトを表示できます。

マネージド テーブルを作成するセキュリティ プリンシパルは、そのテーブルの所有者と見なされ、テーブルと基になるフォルダーおよびファイルに対するすべての権限を付与されます。 さらに、データベースの所有者は、自動的にテーブルの共同所有者になります。

認証パススルーを使用して Spark または SQL の外部テーブルを作成する場合、データはフォルダーとファイルのレベルでのみ保護されます。 だれかがこの種類の外部テーブルに対してクエリを実行する場合は、クエリ送信者のセキュリティ ID がファイル システムに渡され、アクセス権が確認されます。

フォルダーとファイルに対するアクセス許可を設定する方法の詳細については、「[Azure Synapse Analytics の共有データベース](database.md)」を参照してください。

## <a name="examples"></a>例

### <a name="create-a-managed-table-in-spark-and-query-from-serverless-sql-pool"></a>Spark でマネージド テーブルを作成し、サーバーレス SQL プールでクエリを実行する

このシナリオでは、`mytestdb` という名前の Spark データベースを用意します。 「[サーバーレス SQL プールを使用して Spark データベースを作成して接続する](database.md#create-and-connect-to-spark-database-with-serverless-sql-pool)」を参照してください。

次のコマンドを実行し、SparkSQL を使用してマネージド Spark テーブルを作成します。

```sql
    CREATE TABLE mytestdb.myparquettable(id int, name string, birthdate date) USING Parquet
```

このコマンドにより、データベース `mytestdb` にテーブル `myparquettable` が作成されます。 テーブル名は小文字に変換されます。 少し時間が経過すると、サーバーレス SQL プールでテーブルを確認できるようになります。 たとえば、サーバーレス SQL プールから次のステートメントを実行します。

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

結果に `myparquettable` が含まれていることを確認します。

>[!NOTE]
>ストレージ形式として Parquet または CSV が使用されていないテーブルは同期されません。

次に、Spark からテーブルに値を挿入します。たとえば、C# ノートブックで次の C# Spark ステートメントを使用します。

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myparquettable");
```

これで、次のようにサーバーレス SQL プールからデータを読み取ることができます。

```sql
SELECT * FROM mytestdb.dbo.myparquettable WHERE name = 'Alice';
```

次の行が結果として表示されます。

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-in-spark-and-query-from-serverless-sql-pool"></a>Spark で外部テーブルを作成し、サーバーレス SQL プールでクエリを実行する

この例では、前の例でマネージド テーブル用に作成した Parquet データ ファイルに対して外部 Spark テーブルを作成します。

たとえば、SparkSQL を使用して次を実行します。

```sql
CREATE TABLE mytestdb.myexternalparquettable
    USING Parquet
    LOCATION "abfss://<storage-name>.dfs.core.windows.net/<fs>/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

プレースホルダー `<storage-name>` は、使用している ADLS Gen2 ストレージ アカウント名に、`<fs>` は、使用しているファイル システム名に、プレースホルダー `<synapse_ws>` は、この例を実行するために使用している Azure Synapse ワークスペースの名前に置き換えてください。

前の例では、`mytestdb` データベースに `myextneralparquettable` テーブルが作成されます。 少し時間が経過すると、サーバーレス SQL プールでテーブルを確認できるようになります。 たとえば、サーバーレス SQL プールから次のステートメントを実行します。

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

結果に `myexternalparquettable` が含まれていることを確認します。

これで、次のようにサーバーレス SQL プールからデータを読み取ることができます。

```sql
SELECT * FROM mytestdb.dbo.myexternalparquettable WHERE name = 'Alice';
```

次の行が結果として表示されます。

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics の共有メタデータについての詳細情報](overview.md)
- [Azure Synapse Analytics の共有メタデータ データベースの詳細情報](database.md)
