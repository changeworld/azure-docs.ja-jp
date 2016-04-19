<properties
   pageTitle="SQL Data Warehouse の容量制限 | Microsoft Azure"
   description="接続、クエリ、Transact-SQL DDL および DML、SQL Data Warehouse のシステム ビューの最大値です。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/23/2016"
   ms.author="barbkess;jrj;sonyama"/>

# SQL Data Warehouse の容量制限

個々のクエリが使用できるよう保証しながら、最も要求の厳しい分析ワークロードをサポートするための最大値には、最適なパフォーマンスを発揮するのに必要なリソースがあります。

## サービス

| カテゴリ | 説明 | 最大値 |
| :---------------- | :------------------------------------------- | :----------------- |
| データベース | 同時に開かれるセッション数 | 1024<br/><br/>各 SQL Data Warehouse データベースに同時に要求を送信できる、最大で 1024 のアクティブな接続をサポートします。実際に同時に実行できるクエリ数については、制限があるので注意してください。制限を超えた場合、要求は内部キューに送られ、処理の順番が来るのを待機します。|
| データベース接続 | 準備されたステートメントに対する最大メモリ容量 | 20 MB |


## クエリの処理

| カテゴリ | 説明 | 最大値 |
| :---------------- | :------------------------------------------- | :----------------- |
| クエリ | ユーザー テーブルに対する同時クエリ数 | 32<br/><br/>これは、同時に実行できるクエリの最大数です。特定の時点での実際の数は、データベースのサービス レベル目標とクエリのリソース クラスによって異なります。リソースが利用できない場合、クエリは内部キューで待機します。詳細については、「[SQL Data Warehouse での同時実行とワークロード管理][]」をご覧ください。|
| クエリ | ユーザー テーブルに対する、キューに置かれるクエリ数 | 1,000 |
| クエリ | システム ビューに対する同時クエリ数 | 100 |
| クエリ | システム ビューに対する、キューに置かれるクエリ数 | 1,000 |
| クエリ | パラメーターの最大個数 | 2098 |
| Batch  | 最大サイズ | 65,536*4096 |


## データ定義言語 (DDL)

| カテゴリ | 説明 | 最大値 |
| :---------------- | :------------------------------------------- | :----------------- |
| テーブル | データベースあたりのテーブル数 | 20 億 |
| テーブル | テーブルあたりの列数 | 1,024 列 |
| テーブル | 列あたりのバイト数 | 8000 バイト |
| テーブル | 行あたりのバイト数 (定義されたサイズ) | 8060 バイト<br/><br/>行あたりのバイト数は、ページ圧縮を有効にした SQL Server の場合と同様に計算されます。SQL Server のように、SQL Data Warehouse は、行外にプッシュされる可変長列を可能にする行オーバーフロー ストレージをサポートします。行外にプッシュされる可変長列の場合、メイン レコードには 24 バイト ルートのみが格納されます。詳細については、SQL Server オンライン ブックの「[8 KB を超える場合の行オーバーフロー データ](https://msdn.microsoft.com/library/ms186981.aspx)」をご覧ください。<br/><br/>SQL Data Warehouse のデータ型のサイズの一覧については、「[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)](https://msdn.microsoft.com/library/mt203953.aspx)」をご覧ください。 |
| テーブル | 行あたりのバイト数 (データ移動のための内部バッファーのサイズ) | 32,768<br/><br/>注: この制限はまもなくなくなります。<br/><br/>SQL Data Warehouse では、分散型の SQL Data Warehouse システム内で行を移動するときに内部バッファーを使用します。行を移動するサービスはデータ移動サービス (DMS) と呼ばれており、SQL Server とは異なる形式で行を格納します。<br/><br/>行が内部バッファーに収まりきれない場合は、クエリ コンパイル エラーか、または内部データ移動エラーが返されます。この問題を回避するには、「[DMS バッファー サイズの詳細](#details-about-the-dms-buffer-size)」を参照してください。|
| テーブル | テーブルあたりのパーティション数 | 15,000<br/><br/>高パフォーマンスを実現するには、ビジネス要件を満たしながら、必要なパーティション数を最小限に抑えることをお勧めします。パーティションの数が増えるに従い、データ定義言語 (DDL) およびデータ操作言語 (DML) の操作のオーバーヘッドが拡大し、パフォーマンスの低下を引き起こします。|
| テーブル | パーティション境界値あたりの文字数| 4000 |
| Index | テーブルあたりの非クラスター化インデックス数 | 999<br/><br/>行ストア テーブルのみに適用されます。|
| Index | テーブルあたりのクラスター化インデックス数 | 1<br><br/>行ストア テーブルと列ストア テーブルの両方に適用されます。|
| Index | 列ストア インデックスの行グループ内の行数 | 1,024<br/><br/>各列ストア インデックスは、複数の列ストア インデックスとして実装されます。SQL Data Warehouse の列ストア インデックスに 1,024 個の行を挿入した場合、そのすべての行が同じ行グループに入るわけではありません。|
| Index | クラスター化列ストア インデックスの同時実行ビルド数 | 32<br/><br/>クラスター化列ストア インデックスがすべて別々のテーブルでビルドされる場合に適用されます。1 つのテーブルにつき許可されるクラスター化列ストア インデックスのビルドは、1 つだけです。追加の要求は、キュー内で待機します。|
| Index | インデックス キーのサイズ | 900 バイト<br/><br/>行ストア インデックスにのみ適用されます。<br/><br/>インデックスの作成時には varchar 列の既存のデータが 900 バイトを超えていない場合でも、最大サイズが 900 バイトを超える、varchar 列のインデックスが作成されることがあります。ただし、その後の列に対する INSERT または UPDATE 操作は、合計サイズが 900 バイトを超えてしまう場合、失敗します。|
| Index | インデックスあたりのキー列数 | 16<br/><br/>行ストア インデックスにのみに適用されます。クラスター化列ストア インデックスには、すべての列が含まれます。|
| 統計 | 組み合わせ列値のサイズ | 900 バイト |
| 統計 | 統計オブジェクトあたりの列数 | 32 |
| 統計 | テーブルごとに、列に対して作成される統計 | 30,000 |
| ストアド プロシージャ | 入れ子レベルの最大数 | 8 |
| 表示 | ビューあたりの列数 | 1,024 |


## データ操作言語 (DML)

| カテゴリ | 説明 | 最大値 |
| :---------------- | :------------------------------------------- | :----------------- |
| SELECT の結果セット | 行あたりの列数 | 4096<br/><br/>SELECT の結果セットで、許容される行あたりの列数の上限は 4096 個です。　常に 4096 個が保証されるわけではありません。　クエリ プランで一時テーブルが必要な場合、テーブルあたりの列数の最大値として 1024 が適用される可能性があります。|
| SELECT | 入れ子になったサブクエリの数 | 32<br/><br/>SELECT ステートメントで許容される入れ子になったサブクエリの数は 32 個までです。常に 32 個が保証されるわけではありません。たとえば、JOIN によって、クエリ プランにサブクエリが導入されることがあります。サブクエリの数はまた、使用できるメモリによって制限される場合があります。|
| SELECT | JOIN あたりの列数 | 1,024 列<br/><br/>JOIN で許容される列数は 1,024 列までです。常に 1024 列が保証されるわけではありません。JOIN プランで、列数が JOIN の結果を上回る一時テーブルが必要な場合、一時テーブルには 1024 の制限が適用されます。 |
| SELECT | GROUP BY の列あたりのバイト数 | 8060<br/><br/>GROUP BY 句内の列に許容されるバイト数は、最大 8,060 バイトです。|
| SELECT | ORDER BY 列あたりのバイト数 | 8060バイト<br/><br/>ORDER BY 句内の列に許容されるバイト数は、最大 8,060 バイトです。|
| ステートメントあたりの識別子と定数 | 参照される識別子と定数の数 | 65,535<br/><br/>SQL Data Warehouse では、1 つのクエリ式に含めることができる識別子と定数の数が制限されます。上限は 65,535 個です。この数を超えると、SQL Server エラー 8632 が発生します。詳細については、"[内部エラー: Expression Service の制限に達しました](http://support.microsoft.com/kb/913050/)" に関するページをご覧ください。|

## システム ビュー

| システム ビュー | 最大行数 |
| :--------------------------------- | :------------|
| sys.dm\_pdw\_component\_health\_alerts | 10,000 |
| sys.dm\_pdw\_dms\_cores | 100 |
| sys.dm\_pdw\_dms\_workers | 最新の 1000 個の SQL 要求に対する DMS ワーカーの総数 |
| sys.dm\_pdw\_errors | 10,000 |
| sys.dm\_pdw\_exec\_requests | 10,000 |
| sys.dm\_pdw\_exec\_sessions | 10,000 |
| sys.dm\_pdw\_request\_steps | sys.dm\_pdw\_exec\_requests に格納された最新の 1000 個の SQL 要求に対するステップの総数 |
| sys.dm\_pdw\_os\_event\_logs | 10,000 |
| sys.dm\_pdw\_sql\_requests | sys.dm\_pdw\_exec\_requests に格納された最新の 1000 個の SQL 要求 |


## DMS バッファー サイズの詳細

SQL Data Warehouse では、内部バッファーを使用して、バックエンドのコンピューティング ノード間で行を移動します。行を移動するサービスはデータ移動サービス (DMS) と呼ばれており、SQL Server とは異なる格納形式を使用します。

DMS では、並列クエリのパフォーマンスを向上させるために、SQL データベースで定義された最大サイズに達するまであらゆる可変長データを埋め込みます。たとえば、`nvarchar(2000) NOT NULL` の値 "hello" の場合、DMS バッファーで実際に使用されるのは 4,002 バイトです。2,000 文字の各文字に対して 2 バイト、さらに NULL 終端文字に 2 バイトが使用されます。

> [AZURE.NOTE] DMS が、32,768 バイトの DMS バッファー サイズを超える行を移動しようとすると、内部エラーが発生します。行のサイズが DMS バッファー サイズを超えている場合は、行が DMS バッファーに収まるようにテーブルの定義を変更する必要があります。

### DMS バッファーに合わせて行のサイズを決定する方法
この例では、DMS で定義された可変長のデータ サイズについて説明し、行に対応する DMS バッファー サイズを計算する方法を示します。
 
DMS バッファーでは、可変長データ サイズは以下の合計となります。

- 定義された文字サイズ。
- NULL 型では、NULL インジケーターに 8 バイトを使用します。
- ASCII 型では、NULL 終端文字に 1 文字を使用します。
- Unicode 型では、NULL 終端文字に 2 文字を使用します。
             
| データ型 | DMS バッファー サイズ |
| :---------------------- | :-------------------------- |                
| char(1000) NULL | 1009 バイト = 1000 + 8 + 1 |
| char(1000) NOT NULL | 1001 バイト = 1000 + 1 |
| nchar(1000 NULL | 2010 バイト = 2000 + 8 + 2 |
| varchar(1000) NULL | 1009 バイト = 1000 + 8 + 1 |
| varchar(1000) NOT NULL | 1009 バイト = 1000 + 1 |
| nvarchar(1000) NULL | 2010 バイト = 2000 + 8 + 2 |
| nvarchar(1000) NOT NULL | 2010 バイト = 2000 + 2 |
                
DMS バッファーの固定幅列では、SQL Server のネイティブ サイズを使用します。型が null 許容である場合、DMS には追加の 8 バイトが必要です。SQL Server のサイズについては、sys.types の max\_length フィールドを参照してください。

次に例を示します。

| 固定幅のデータ型 | DMS バッファー サイズ |
| :-------------------- | :----------------- |
| int NULL | 12 バイト = 4 + 8 |
| int NOT NULL | 4 バイト = 4 + 0 | 
                
まとめると、次の行に対する DMS バッファーの定義サイズは 31,134 バイトであり、DMS バッファーに収まります。

| 分割 | データ型 | 列サイズ |
| :----- | :------------------ | :------------------------ |
| col1 | datetime2 (7) NULL | 20 バイト = 8 + 8 |
| col2 | float (4) NULL | 12 バイト = 4 + 8 |
| col3 | nchar (6) NULL | 22 バイト = 12 + 8 + 2 |
| col4 | char (7000) NULL | 7009 バイト = 7000 + 8 + 1 |
| col5 | nvarchar (4000) | 8002 バイト = 8000 + 2. |
| col6 | varchar (8000) NULL | 8009 バイト = 8000 + 8 + 1 |
| col7 | varbinary (8000) | 8000 バイト |
| col8 | binary (60) | 60 バイト |
                
              
### DMS バッファー サイズを超過する例

この例では、どうすれば SQL Data Warehouse に行を正常に挿入できるかを示すと共に、DMS が、ディストリビューションに関して互換性のない結合のために行を移動する必要がある場合、どのようにして DMS オーバーフロー エラーが発生するかを説明します。このレッスンでは、DMS バッファーに収まるように、より小さな行を作成します。

次の例では、テーブル T1 を作成します。すべての nvarchar 変数が Unicode 文字を 4,000 文字保持している場合、行のサイズは最大で 40,000 バイトになる可能性があります。この値は、最大 DMS バッファー サイズを超えています。

nvarchar の実際の定義サイズでは 26 バイトを使用するため、行の定義は 8,060 バイト未満となり、SQL Server ページに収まります。したがって、この行を DMS バッファーに読み込もうとしたときに DMS が失敗したとしても、CREATE TABLE ステートメントは成功します。

```sql
CREATE TABLE T1
  (
    c0 int NOT NULL,
    CustomerKey int NOT NULL,
    c1 nvarchar(4000),
    c2 nvarchar(4000),
    c3 nvarchar(4000),
    c4 nvarchar(4000),
    c5 nvarchar(4000)
  )
WITH ( DISTRIBUTION = HASH (c0) )
;
```

次のステップでは、INSERT を使用すると、テーブルにデータを正常に挿入できることを示します。このステートメントでは、DMS を使用しないで SQL Server にデータを直接読み込むので、DMS バッファーのオーバーフロー エラーは発生しません。Integration Services でもこの行を正常に読み込むことができます。</para>

```sql
--The INSERT operation succeeds because the row is inserted directly into SQL Server without requiring DMS to buffer the row.
INSERT INTO T1
VALUES (
    25,
    429817,
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.'
  )
```

データの移動方法を説明するための準備として、ディストリビューション列に CustomerKey を使用して 2 つ目のテーブルを作成します。

```sql
--This second table is distributed on CustomerKey. 
CREATE TABLE T2
  (
    c0 int NOT NULL,
    CustomerKey int NOT NULL,
    c1 nvarchar(4000),
    c2 nvarchar(4000),
    c3 nvarchar(4000),
    c4 nvarchar(4000),
    c5 nvarchar(4000)
  )
WITH ( DISTRIBUTION = HASH (CustomerKey) )
;

INSERT INTO T2
VALUES (
    32,
    429817,
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.'
  )
```
両方のテーブルは CustomerKey でディストリビュートされないため、CustomerKey での T1 と T2 との結合は、ディストリビューションに関して互換性がありません。DMS は、少なくとも 1 つの行を読み込み、別のディストリビューションにコピーする必要があります。

```sql
SELECT * FROM T1 JOIN T2 ON T1.CustomerKey = T2.CustomerKey;
```

想定どおり、すべての nvarchar 列が埋め込まれると、行のサイズは 32,768 バイトの DMS バッファー サイズより大きくなるため、DMS は結合を実行することができません。次のエラー メッセージが表示されます。

```sql
Msg 110802, Level 16, State 1, Line 126

An internal DMS error occurred that caused this operation to fail. Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Workers.DmsSqlNativeException, Message: SqlNativeBufferReader.ReadBuffer, error in OdbcReadBuffer: SqlState: , NativeError: 0, 'COdbcReadConnection::ReadBuffer: not enough buffer space for one row | Error calling: pReadConn-&gt;ReadBuffer(pBuffer, bufferOffset, bufferLength, pBytesRead, pRowsRead) | state: FFFF, number: 81, active connections: 8', Connection String: Driver={SQL Server Native Client 11.0};APP=DmsNativeReader:P13521-CMP02\sqldwdms (4556) - ODBC;Trusted_Connection=yes;AutoTranslate=no;Server=P13521-SQLCMP02,1500
```


## 次のステップ
詳細な参照情報については、[SQL Data Warehouse のリファレンス概要][]に関するページを参照してください。

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse のリファレンス概要]: sql-data-warehouse-overview-reference.md
[SQL Data Warehouse での同時実行とワークロード管理]: sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->

<!---HONumber=AcomDC_0406_2016-->