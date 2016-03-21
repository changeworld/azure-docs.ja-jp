<properties
	pageTitle="移行する行の選択にフィルター述語を使用する (Stretch Database) | Microsoft Azure"
	description="移行する行の選択にフィルター述語を使用する方法について説明します。"
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# 移行する行の選択にフィルター述語を使用する (Stretch Database)

別個のテーブルに履歴データを保存する場合、テーブル全体を移行するように Stretch Database を設定できます。その一方で、テーブルに過去と現在の両方のデータが含まれている場合、移行する行を選択するフィルター述語を指定できます。フィルター述語では、インライン テーブル値関数を呼び出す必要があります。このトピックでは、移行する行を選択するインライン テーブル値関数を記述する方法について説明します。

CTP 3.1 ～ RC0 では、[Stretch Database を有効にする] ウィザードに述語を指定するオプションがありません。このオプションで Stretch Database を設定するには、ALTER TABLE ステートメントを使用する必要があります。詳細については、「[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)」をご覧ください。

フィルター述語を指定しない場合、テーブル全体が移行されます。

> [!重要] 指定したフィルター述語のパフォーマンスが悪いと、データ移行のパフォーマンスも悪くなります。Stretch Database は CROSS APPLY 演算子を利用し、テーブルにフィルター述語を適用します。

## インライン テーブル値関数の基本要件
Stretch Database フィルター機能に必要なインライン テーブル値関数は次の例のようになります。

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE <predicate>
```
関数のパラメーターはテーブルの列の識別子となる必要があります。

フィルター述語で使用される列が削除または変更されるの回避するには、スキーマ バインディングが必要になります。

### 戻り値
関数が空ではない結果を返すとき、行は移行の対象となります。何の行も返されない場合、行は移行の対象となりません。

### 条件
&lt;*述語*&gt; は 1 つの条件か AND 論理演算子で結合された複数の条件で構成されます。

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
各条件は 1 つのプリミティブ条件か OR 論理演算子で結合された複数のプリミティブ条件で構成されます。

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### プリミティブ条件
プリミティブ条件は次のいずれかの比較を実行できます。

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

-   関数パラメーターと定数式を比較します。たとえば、「`@column1 < 1000`」のように入力します。

    次の例では、*date* 列の値が &lt; 1\\/1\\/2016 であるかどうかが確認されます。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
    GO

    ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   IS NULL または IS NOT NULL 演算子を関数パラメーターに適用します。

-   IN 演算子を使用し、関数パラメーターと定数値の一覧を比較します。

    次の例では、*shipment\_status* 列の値が `IN (N'Completed', N'Returned', N'Cancelled')` であるかどうかが確認されます。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

### 比較演算子
次の比較演算子がサポートされます。

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### 定数式
フィルター述語で使用する定数は、関数の定義時に評価可能なあらゆる決定性の式にすることができます。定数式には次を含めることができます。

-   リテラル。たとえば、「`N’abc’, 123`」のように入力します。

-   代数式。たとえば、「`123 + 456`」のように入力します。

-   確定関数。たとえば、「`SQRT(900)`」のように入力します。

-   CAST または CONVERT を使用する確定的変換。たとえば、「`CONVERT(datetime, '1/1/2016', 101)`」のように入力します。

### その他の式
BETWEEN 演算子と NOT BETWEEN 演算子を同等の AND 式と OR 式で置換した後、結果的に生成される述語がここで説明するルールに準拠する場合、BETWEEN 演算子と NOT BETWEEN 演算子を利用できます。

サブクエリや RAND() または GETDATE() のような非決定性の関数は使用できません。

## 有効な関数の例

-   次の例では、AND 論理演算子を使用して 2 つのプリミティブ条件を結合します。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
      WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   次の例では、いくつかの条件、確定変換、CONVERT が使用されています。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
    GO
    ```

-   次の例では、算術演算子と関数が使用されています。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < SQRT(400) + 10
    GO
    ```

-   次の例では、BETWEEN 演算子と NOT BETWEEN 演算子が使用されています。BETWEEN 演算子と NOT BETWEEN 演算子を同等の AND 式と OR 式で置換した後、結果的に生成される述語がここで説明するルールに準拠するため、この使用方法は有効です。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 BETWEEN 0 AND 100
    			AND (@column2 NOT BETWEEN 200 AND 300 OR @column1 = 50)
    GO
    ```
    BETWEEN 演算子と NOT BETWEEN 演算子を同等の AND 式と OR 式で置換した後、先行する関数は後続の関数と等しくなります。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example4(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
    GO
    ```

## 無効な関数の例

-   非決定性の変換が含まれるため、次の関数は無効です。

    ```tsql
    CREATE FUNCTION dbo.fn_example5(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < CONVERT(datetime, '1/1/2016')
    GO
    ```

-   非決定性の関数呼び出しが含まれるため、次の関数は無効です。

    ```tsql
    CREATE FUNCTION dbo.fn_example6(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < DATEADD(day, -60, GETDATE())
    GO
    ```

-   サブクエリが含まれるため、次の関数は無効です。

    ```tsql
    CREATE FUNCTION dbo.fn_example7(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
    GO
    ```

-   関数の定義時、代数演算子または組み込み関数を使用する式が定数に評価される必要があるため、次の関数は無効です。代数の式や関数呼び出しに列参照を含めることはできません。

    ```tsql
    CREATE FUNCTION dbo.fn_example8(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 % 2 =  0
    GO

    CREATE FUNCTION dbo.fn_example9(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE SQRT(@column1) = 30
    GO
    ```

-   BETWEEN 演算子を同等の AND 式で置換した後、ここで説明するルールに違反するため、次の関数は無効です。

    ```tsql
    CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```
    BETWEEN 演算子を同等の AND 式で置換した後、先行する関数は後続の関数と等しくなります。プリミティブ条件は OR 論理演算子のみを使用できるために、この関数は無効です。

    ```tsql
    CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```

## Stretch Database がフィルター述語を適用するしくみ
Stretch Database は CROSS APPLY 演算子を利用し、テーブルにフィルター述語を適用し、適格な行を決定します。次に例を示します。

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
関数が行に空ではない結果を返す場合、行は移行の対象となります。

## フィルター述語をテーブルに追加する
ALTER TABLE ステートメントを実行し、FILTER\_PREDICATE パラメーターの値として既存のインライン テーブル値関数を指定することでテーブルにフィルター述語を追加します。次に例を示します。

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
	MIGRATION_STATE = <desired_migration_state>
) )
```
関数を述語としてテーブルにバインドすると、次のようになります。

-   次のデータ移行時に、関数が空ではない値を返す行のみが移行されます。

-   関数で使用される列にスキーマがバインドされます。テーブルでそのフィルター述語として関数が使用されている限り、これらの列は変更できません。

## フィルター述語をテーブルから削除する
選択した行ではなく、テーブル全体を移行する場合、既存の FILTER\_PREDICATE を null に設定し、削除します。次に例を示します。

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = NULL,
	MIGRATION_STATE = <desired_migration_state>
) )
```
フィルター述語を削除すると、テーブルのすべての行が移行の対象になります。

## 既存のフィルター述語を置き換える
ALTER TABLE ステートメントをもう一度実行し、FILTER\_PREDICATE パラメーターに新しい値を指定することで、以前に指定したフィルター述語を置換できます。次に例を示します。

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
	MIGRATION_STATE = <desired_migration_state>
```
新しいインライン テーブル値関数には次の要件があります。

-   新しい関数は、前の関数よりも制約を減らす必要があります。

-   古い関数に含まれていたすべての演算子を新しい関数に含める必要があります。

-   古い関数に含まれていない演算子は新しい関数に含めることができません。

-   演算子の引数の順序は変更できません。

-   `<, <=, >, >=` 比較に含まれる定数値のみを、述語の制約が減るように変更できます。

### 有効な置換の例
次の関数が現在のフィルター述語であると仮定します。

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -100 OR @column2 > 100)
GO
```
(遅い決算日を指定する) 新しい日付定数で述語の制約が減るため、次の関数は有効な置換となります。

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
			AND (@column2 < -50 OR @column2 > 50)
GO
```

### 無効な置換の例
(早い決算日を指定する) 新しい日付定数で述語の制約が減らないため、次の関数は有効な置換になりません。

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)
			AND (@column2 < -100 OR @column2 > 100)
GO
```
比較演算子の 1 つが削除されているため、次の関数は有効な置換になりません。

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_2 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -50)
GO
```
AND 論理演算子で新しい条件が追加されているため、次の関数は有効な置換になりません。

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_3 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -100 OR @column2 > 100)
			AND (@column2 <> 0)
GO
```

## フィルター述語を削除する
テーブルでそのフィルター述語として関数が使用されている限り、インライン テーブル値関数は削除できません。

## テーブルに適用されたフィルター述語を確認する
テーブルに適用されたフィルター述語を確認するには、カタログ ビュー **sys.remote\_data\_archive\_tables** を開き、**filter\_predicate** 列の値を確認します。値が null の場合、テーブル全体がアーカイブの対象になります。詳細については、「[sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx)」を参照してください。

## 関連項目
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

<!---HONumber=AcomDC_0309_2016-->