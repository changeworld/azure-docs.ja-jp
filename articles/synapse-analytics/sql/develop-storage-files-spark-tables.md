---
title: サーバーレス SQL プールで Apache Spark の外部テーブル定義を同期する
description: サーバーレス SQL プールを使用して Spark テーブルに対してクエリを実行する方法の概要
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 642024d9554b51bc60df90cf3d5a7bdd799440b5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432092"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-serverless-sql-pool"></a>サーバーレス SQL プールで Apache Spark for Azure Synapse の外部テーブル定義を同期する

サーバーレス SQL プールにより、Apache Spark からメタデータを自動的に同期することができます。 サーバーレス Apache Spark プール内にあるデータベースごとに、サーバーレス SQL プール データベースが作成されます。 

Parquet または CSV に基づく、Azure Storage に配置された各 Spark 外部テーブルに対して、サーバーレス SQL プール データベースに外部テーブルが作成されます。 そのため、Spark プールをシャットダウンしても、サーバーレス SQL プールから Spark 外部テーブルに対するクエリを引き続き実行できます。

テーブルが Spark でパーティション分割されている場合、ストレージ内のファイルはフォルダーごとに整理されます。 サーバーレス SQL プールでは、パーティション メタデータが使用され、クエリに関連するフォルダーとファイルのみが対象になります。

メタデータの同期は、Azure Synapse ワークスペースにプロビジョニングされた各サーバーレス Apache Spark プールに対して自動的に構成されます。 Spark 外部テーブルに対するクエリをすぐに開始できます。

Spark の Azure Storage に配置された各 Parquet または CSV 外部テーブルは、サーバーレス SQL プール データベースに対応する dbo スキーマの外部テーブルで表されます。 

Spark 外部テーブルのクエリの場合は、外部 [spark_table] を対象とするクエリを実行します。 次の例を実行する前に、ファイルが配置されている[ストレージ アカウントへの適切なアクセス許可](develop-storage-files-storage-access-control.md)を持っていることを確認してください。

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Apache Spark データ型から SQL データ型へのマッピング

| Spark データ型 | SQL データ型 | 説明 |
|---|---|---|
| `LongType`, `long`, `bigint`                | `bigint`              | **Spark**: *LongType* は 8 バイト符号付き整数値を表します。 [リファレンス](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql) |
| `BooleanType`, `boolean`                    | `bit` (Parquet)、`varchar(6)` (CSV)  | |
| `DecimalType`, `decimal`, `dec`, `numeric`  | `decimal`             | **Spark**: *DecimalType* は、任意の有効桁数の符号付き 10 進数を表します。 java.math.BigDecimal で内部的にサポートされています。 BigDecimal は、任意の有効桁数の小数点以下なしの整数値と、小数点以下保持の 32 ビットの整数値で構成されます。 <br> **SQL**: 固定長の有効桁数と小数点以下保持の数値です。 最大有効桁数を使用した場合、有効値は - 10^38 +1 から 10^38 - 1 です。 decimal の ISO のシノニムは、dec および dec(p, s) です。 numeric は機能的には decimal と同じです。 [参照](/sql/t-sql/data-types/decimal-and-numeric-transact-sql]) |
| `IntegerType`, `Integer`, `int`             | `int`                 | **Spark** *IntegerType* は 4 バイト符号付き整数値を表します。 [リファレンス](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)|
| `ByteType`, `Byte`, `tinyint`               | `smallint`            | **Spark**: *ByteType* は 1 バイト符号付き整数値 [-128 から 127] を表し、ShortType は 2 バイト符号付き整数値 [-32768 から 32767] を表します。 <br> **SQL**: tinyint は 1 バイト符号付き整数値 [0, 255] を表し、smallint は 2 バイト符号付き整数値 [-32768, 32767] を表します。 [参照](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)|
| `ShortType`, `Short`, `smallint`            | `smallint`            | 上記と同じです。 |
| `DoubleType`, `Double`                      | `float`               | **Spark**: *DoubleType* は 8 バイト倍精度浮動小数点数を表します。 **SQL** については、[このページにアクセス](/sql/t-sql/data-types/float-and-real-transact-sql)してください。|
| `FloatType`, `float`, `real`                | `real`                | **Spark**: *FloatType* は 4 バイト倍精度浮動小数点数を表します。 **SQL** については、[このページにアクセス](/sql/t-sql/data-types/float-and-real-transact-sql)してください。|
| `DateType`, `date`                          | `date`                | **Spark**: *DateType* は、タイムゾーンを指定せずに、フィールドの値 (年、月、日) を構成する値を表します。|
| `TimestampType`, `timestamp`                | `datetime2`           | **Spark**: *TimestampType* は、セッションのローカル タイムゾーンを指定して、フィールドの値 (年、月、日、時、分、秒) を構成する値を表します。 タイムスタンプ値は、絶対的な特定の時点を表します。
| `char`                                      | `char`                |
| `StringType`, `String`, `varchar`           | `Varchar(n)`          | **Spark**: *StringType* は文字列値を表します。 *VarcharType(n)* は、長さの制限がある StringType のバリアントです。 入力文字列が長さの制限を超えた場合、データの書き込みは失敗します。 この型は、関数または演算子ではなく、テーブル スキーマでのみ使用できます。<br> *CharType(n)* は、固定長の *VarcharType(n)* のバリアントです。 *CharType(n)* 型の列を読み取ると、常に長さ n の文字列値が返されます。 Char 型の列の比較では、短い方が長い方の長さまで埋め込まれます。 <br> **SQL**: *Varchar(n)* では、n を最大 8000 に、またパーティション分割された列では n を最大 2048 に設定できます。 <br> 照合順序 `Latin1_General_100_BIN2_UTF8` と共に使用します。 |
| `BinaryType`, `binary`                      | `varbinary(n)`        | **SQL**: *Varbinary(n)* では、n を最大 8000 に、またパーティション分割された列では n を最大 2048 に設定できます。 |
| `array`, `map`, `struct`                    | `varchar(max)`        | **SQL**: 照合順序 `Latin1_General_100_BIN2_UTF8` を使用して JSON にシリアル化します |

\* データベース レベルの照合順序は Latin1_General_100_CI_AS_SC_UTF8 \*、文字列レベルの照合順序は Latin1_General_100_BIN2_UTF8

\** ArrayType、MapType、StructType は JSON として表されます。

## <a name="next-steps"></a>次のステップ

ストレージ アクセス制御の詳細については、[ストレージ アクセス制御](develop-storage-files-storage-access-control.md)に関する記事をご覧ください。
