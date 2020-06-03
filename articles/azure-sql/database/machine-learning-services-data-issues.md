---
title: R および SQL データ型とオブジェクトの処理
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Machine Learning Services (プレビュー) を使用して Azure SQL Database で R のデータ型とデータ オブジェクトを処理する方法について、発生する可能性のある一般的な問題も含めて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f784d6ef56ad5cb800c0061fbb5d0d4ca3252fa0
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84035663"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Azure SQL Database Machine Learning Services (プレビュー) での R および SQL データの処理
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事では、[Azure SQL Database の Machine Learning Services (R を使用)](machine-learning-services-overview.md) でデータを R と SQL Database 間で移動するときに発生する可能性のある、一般的な問題についていくつか説明します。 この演習で得られるエクスペリエンスは、独自のスクリプトでデータを扱うときの重要な背景知識を提供します。

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

発生する可能性がある問題には次のものがあります。

- データ型が一致しないときがある
- 暗黙的な変換が発生することがある
- cast および convert 操作が必要な場合がある
- R と SQL で異なるデータ オブジェクトが使用される

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/free/)してください。

- 以降の演習のサンプル コードを実行するには、あらかじめ、[Machine Learning Services (R を使用) が有効になった Azure SQL データベース](machine-learning-services-overview.md)を用意しておく必要があります。

- 最新の [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) をインストールしていることを確認してください。 他のデータベース管理またはクエリ ツールを使用して R スクリプトを実行することはできますが、このクイック スタートでは SSMS を使用します。

## <a name="working-with-a-data-frame"></a>データ フレームの処理

スクリプトで R からの結果を SQL に返す場合、データは **data.frame** として返される必要があります。 リスト、ファクター、ベクター、バイナリ データなど、スクリプトで生成するその他の種類のオブジェクトは、ストアド プロシージャ結果の一部として出力する場合はデータ フレームに変換する必要があります。 幸い、その他のオブジェクトのデータ フレームへの変更をサポートする複数の R 関数が存在します。 バイナリ モデルをシリアル化し、データ フレームで返すこともできます。これはこの記事の後半で行います。

まず、いくつかの基本的な R オブジェクト (ベクター、マトリックス、リスト) で試して、データ フレームへの変換により SQL に渡される出力がどのように変化するかを見てみましょう。

次の 2 つの R で書かれた "Hello World" スクリプトを比較します。これらのスクリプトはほぼ同じものに見えますが、1 つ目は 3 つの値が含まれる 1 つの列を返し、2 つ目は 1 つの値が含まれる列を 3 つ返します。

**例 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**例 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

なぜ結果がこれほど異なるのでしょうか。

答えは通常、R の `str()` コマンドを使用すればわかります。 指定の R オブジェクトのデータ スキーマが情報メッセージとして返されるよう、R スクリプトの任意の場所に関数 `str(object_name)` を追加します。 このメッセージは SSMS の **[メッセージ]** タブで表示することができます。

なぜ例 1 と例 2 の結果がこれほど異なるのかを理解するため、各ステートメントの `@script` 変数定義の最後に、行 `str(OutputDataSet)` を次のように挿入します。

**str 関数が追加された例 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**str 関数を追加した例 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

次に、 **[メッセージ]** 内のテキストを確認して、なぜ出力が異なるのかを調べます。

**結果 - 例 1**

```text
STDOUT message(s) from external script:
'data.frame':    3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**結果 - 例 2**

```text
STDOUT message(s) from external script:
'data.frame':    1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

ご覧のように、R 構文をわずかに変更するだけで結果のスキーマに大きな影響がありました。 すべての詳細については、["Advanced R" by Hadley Wickham](http://adv-r.had.co.nz)の「*Data Structures*」セクションで R データ型の相違点が詳細に説明されています。

現時点では、R オブジェクトをデータ フレームに強制変換するときは予想される結果を確認する必要がある、という点のみ注意してください。

> [!TIP]
> `is.matrix`、`is.vector` などの R ID 関数を使用して内部データ構造に関する情報を返すこともできます。

## <a name="implicit-conversion-of-data-objects"></a>データ オブジェクトの暗黙的な変換

各 R オブジェクトには、他のデータ オブジェクトと結合されたときに、その 2 つのデータ オブジェクトのディメンションの数が同じ場合、またはいずれかのデータ オブジェクトに異種データ型が含まれている場合、値がどのように扱われるのかについて独自のルールがあります。

たとえば、R を使用した行列乗算を実行したいとします。4 つの値を持つ配列で 3 つの値を持つ 1 列の行列を乗算し、結果として 4 x 3 行列を予想します。

まず、テスト データの小さなテーブルを作成します。

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

今度は、次のスクリプトを実行します。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

これにより、3 つの値の列が 1 列の行列に変換されます。 行列は単に R における配列の特殊なケースであるため、配列 `y` は暗黙的に、2 つの引数を一致させるために 1 列の行列に強制変換されます。

**結果**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

ただし、配列 `y` のサイズを変更した場合に起こることについて注意してください。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

この場合、結果として単一の値が R から返されます。

**結果**
    
|Col1|
|---|
|1542|

なぜですか? この場合、2 つの引数を同じ長さのベクターとして扱うことができるため、内積が 1 つの行列として R から返されます。  これは、線形代数のルールに応じた正しい動作です。 ただし、結果のスキーマが不変であるとダウンストリーム アプリケーションで期待されている場合は、問題が生じることがあります。

## <a name="merge-or-multiply-columns-of-different-length"></a>異なる長さの列のマージまたは乗算

R は、異なるサイズのベクトルを使用するため、およびそれらの列のような構造をデータ フレームに結合するために、優れた柔軟性を提供します。 ベクターのリストはテーブルのように見えますが、データベース テーブルに適用されるどのルールにも従いません。

たとえば、次のスクリプトでは、長さが 6 の数値の配列を定義し、R 変数 `df1` に格納します。 この数値配列は、(上記で作成した) 3 つの値を含む RTestData テーブルの整数値と結合され、新しいデータ フレーム `df2` が作成されます。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

データ フレームを埋めるために、R は RTestData から取得した要素を、配列 `df1` 内の要素数と一致するために必要な数だけ繰り返します。

**結果**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

データ フレームはテーブルのように見えるだけであり、実際はベクターのリストであることに注意してください。

## <a name="cast-or-convert-sql-data"></a>SQL データのキャストまたは変換

R と SQL では同じデータ型は使用されません。そのため、SQL でクエリを実行してデータを取得し、R ランタイムに渡すときには、通常は一種の暗黙的な変換が行われます。 R から SQL にデータを返すときには、別の一連の変換が行われます。

- SQL でクエリからのデータが R プロセスにプッシュされ、効率を向上させるために内部表現に変換されます。
- R ランタイムでデータが data.frame 変数に読み込まれ、データに対して独自の操作が実行されます。
- データベース エンジンにより、セキュリティで保護された内部接続を使用して SQL にデータが返され、データは SQL データ型の形で表示されます。
- データは、SQL クエリの発行と表形式データ セットの処理ができるクライアントまたはネットワーク ライブラリを使用して SQL に接続することで、取得します。 このクライアント アプリケーションは、他の方法でデータに影響する可能性があります。

この動作を確認するには、このようなクエリを [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) データ ウェアハウス上で実行します。 このビューは、予測の作成に使用する売上データを返します。

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> 任意のバージョンの AdventureWorks を使用することも、ご使用のデータベースを使用して別のクエリを作成することもできます。 ポイントは、テキスト、日時、数の値を含むデータを処理してみるということです。

次に、ストアド プロシージャへの入力としてこのクエリを使用してみます。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

エラーが発生した場合、クエリ テキストの編集が必要になることがあります。 たとえば、WHERE 句内の文字列熟語は、2 つの一重引用符のセットで囲む必要があります。

クエリが動作したら、R で入力データがどのように扱われているかについて、`str` 関数の結果で確認します。

**結果**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- 日時列は、R データ型 **POSIXct** を使用して処理されています。
- テキスト列 "ProductSeries" は、**factor**、つまりカテゴリ変数として識別されています。 文字列値は、既定ではファクターとして処理されます。 文字列を R に渡すと、内部使用のために整数に変換され、出力では文字列にマップし直されます。

## <a name="summary"></a>まとめ

このような短い例であっても、SQL クエリを入力として渡す場合はデータ変換の影響を確認する必要があることがわかります。 一部の SQL データ型は R ではサポートされていないため、以下の方法を検討してエラーを回避してください。

- データを事前にテストし、R コードに渡したときに問題となる可能性がある、スキーマ内の列または値を確認します。
- 入力データ ソース内の列を指定する場合、`SELECT *` を使用するよりは、個別に指定して、各列がどのように処理されるかを理解します。
- 予想外の問題を回避するために、入力データを準備するときに、必要に応じて明示的なキャストを実行します。
- エラー発生の原因となりモデリングの役に立たない (GUID や rowguid などの) データの列を渡すことを回避します。

サポートされている R データ型とサポートされていない R データ型の詳細については、[R ライブラリとデータ型](/sql/advanced-analytics/r/r-libraries-and-data-types)に関する記事を参照してください。
