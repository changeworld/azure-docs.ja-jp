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
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# 移行する行の選択にフィルター述語を使用する (Stretch Database)

別個のテーブルにコールド データを保存する場合、テーブル全体を移行するように Stretch Database を設定できます。その一方で、テーブルにホット データとコールド データ両方のデータが含まれている場合、移行する行を選択するフィルター述語を指定できます。フィルター述語は、インライン テーブル値関数です。このトピックでは、移行する行を選択するインライン テーブル値関数を記述する方法について説明します。

>   [AZURE.NOTE] 指定したフィルター述語のパフォーマンスが悪いと、データ移行のパフォーマンスも悪くなります。Stretch Database は CROSS APPLY 演算子を利用し、テーブルにフィルター述語を適用します。

フィルター述語を指定しない場合、テーブル全体が移行されます。

Stretch Database の有効化ウィザードを実行する際に、テーブル全体を移行することも、ウィザードで単純な述語を指定することもできます。別の種類のフィルター述語を使用して、移行する行を選択する場合は、次のいずれかの操作を行います。

-   ウィザードを終了し、ALTER TABLE ステートメントを実行してテーブルの Stretch を有効にし、述語を指定します。

-   ウィザードを終了してから、ALTER TABLE ステートメントを実行して、述語を指定します。

述語を追加するための ALTER TABLE 構文については、このトピックの後の方で説明しています。

## フィルター述語の基本的な要件
Stretch Database フィルター述語に必要なインライン テーブル値関数は次の例のようになります。

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
関数が空ではない結果を返す場合、行は移行の対象となります。それ以外の場合、つまり結果が返されない場合、行は移行の対象となりません。

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

    次の例では、*date* 列の値が &lt; 1/1/2016 であるかどうかが確認されます。

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

テーブルでそのフィルター述語として関数が使用されている限り、インライン テーブル値関数は削除できません。

>   [AZURE.NOTE] フィルター関数のパフォーマンスを向上させるには、関数で使用される列にインデックスを作成します。

### フィルター述語に列名を渡す
テーブルにフィルター関数を割り当てる場合は、フィルター関数に渡す列名を 1 部構成の名前で指定します。列名を渡す際に 3 部構成の名前を指定すると、Stretch が有効なテーブルに対するその後のクエリが失敗します。

たとえば、次の例に示すような 3 部構成の名前を指定すると、ステートメントは正常に実行されますが、テーブルに対するその後のクエリが失敗します。

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(dbo.SensorTelemetry.ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

代わりに、次の例に示すような 1 部構成の名前のフィルター関数を指定 します。

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON  (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

## <a name="addafterwiz"></a>ウィザードの実行後にフィルター述語を追加する  

**[Stretch Database を有効にする] ** ウィザードで作成できない述語を使用する場合は、ウィザードを終了してから ALTER TABLE ステートメントを実行して述語を指定します。ただしこの場合は、述語を適用する前に、既に進行中のデータ移行を停止して、移行されたデータを元に戻す必要があります。(これが必要な理由の詳細については、「[Replace an existing filter predicate](#replacePredicate)」(既存のフィルター述語を置き換える)を参照してください。

1. 移行の方向を逆にして、既に移行されたデータを元に戻します。開始後にこの操作をキャンセルすることはできません。また、Azure での送信データ転送 (送信) の料金が発生します。詳細については「[Azure の料金体系について](https://azure.microsoft.com/pricing/details/data-transfers/)」を参照してください。  

    ```tsql  
    ALTER TABLE <table name>  
         SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;   
    ```  

2. データ移行が完了するまで待ちます。SQL Server Management Studio から**Stretch Database Monitor**でデータ移行の状態を確認するか、もしくは **sys.dm\_db\_rda\_migration\_status** ビューをクエリします。詳細については、「[Monitor and troubleshoot data migration ](sql-server-stretch-database-monitor.md) 」(データ移行の監視とトラブルシューティング) または 「[sys.dm\_db\_rda\_migration\_status](https://msdn.microsoft.com/library/dn935017.aspx)」を参照してください。

3. テーブルに適用するフィルター述語を作成します。

4. テーブルに述語を追加して Azure へのデータ移行を再開します。

    ```tsql  
    ALTER TABLE <table name>  
        SET ( REMOTE_DATA_ARCHIVE  
            (           
                FILTER_PREDICATE = <predicate>,  
                MIGRATION_STATE = OUTBOUND  
            )  
        );   
    ```  

## 日付で行をフィルター処理する
次の例では、**date** 列に 2016 年 1 月 1 日より前の値を含む列が移行されます。

```tsql
-- Filter by date
--
CREATE FUNCTION dbo.fn_stretch_by_date(@date datetime2)
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @date < CONVERT(datetime2, '1/1/2016', 101)
GO
```

## status 列の値で行をフィルター処理する
次の例では、**status** 列に指定された値のいずれかを含む列が移行されます。

```tsql
-- Filter by status column
--
CREATE FUNCTION dbo.fn_stretch_by_status(@status nvarchar(128))
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @status IN (N'Completed', N'Returned', N'Cancelled')
GO
```

## スライディング ウィンドウを使用して行をフィルター処理する
スライディング ウィンドウを使用して行をフィルター処理する場合は、フィルター関数の以下の要件を考慮してください。

-   関数は確定的である必要があります。このため、スライディング ウィンドウを時間の経過に伴い自動的に再計算する関数は作成できません。

-   関数はスキーマ バインドを使用します。このため、スライディング ウィンドウを移動するのに、ALTER FUNCTION を呼び出して "所定の場所にある" 関数を毎日更新することはできません。

次に示すフィルター述語の例では、**systemEndTime** 列に 2016 年 1 月 1 日より前の値を含む列が移行されます。

```tsql
CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160101(@systemEndTime datetime2)
RETURNS TABLE
WITH SCHEMABINDING  
AS  
RETURN SELECT 1 AS is_eligible
  WHERE @systemEndTime < CONVERT(datetime2, '2016-01-01T00:00:00', 101) ;
```

テーブルにフィルター述語を適用します。

```tsql
ALTER TABLE <table name>
SET (
        REMOTE_DATA_ARCHIVE = ON
                (
                        FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160101 (SysEndTime)
                                , MIGRATION_STATE = OUTBOUND
                )
        )
;
```

スライディング ウィンドウを更新するには、次のようにします。

1.  新しいスライディング ウィンドウを指定する新しい関数を作成します。次の例では、2016 年 1 月 1 日ではなく、2016 年 1 月 2 日より前の日付を選択します。

2.  次の例に示すように、ALTER TABLE を呼び出して、以前のフィルター述語を新しい述語に置き換えます。

3. 必要に応じて、DROP FUNCTION を呼び出して不要になった前のフィルター関数を削除します (この手順は例に含まれていません)。

```tsql
BEGIN TRAN
GO
        /*(1) Create new predicate function definition */
        CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160102(@systemEndTime datetime2)
        RETURNS TABLE
        WITH SCHEMABINDING
        AS
        RETURN SELECT 1 AS is_eligible
               WHERE @systemEndTime < CONVERT(datetime2,'2016-01-02T00:00:00', 101)
        GO

        /*(2) Set the new function as filter predicate */
        ALTER TABLE <table name>
        SET
        (
               REMOTE_DATA_ARCHIVE = ON
               (
                       FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160102(SysEndTime),
                       MIGRATION_STATE = OUTBOUND
               )
        )
COMMIT ;
```

## 有効なフィルター述語のその他の例

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

## 無効なフィルター述語の例

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

## <a name="replacePredicate"></a>既存のフィルター述語を置き換える
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

## フィルター述語をテーブルから削除する
選択した行ではなく、テーブル全体を移行する場合、既存の FILTER\_PREDICATE を null に設定し、削除します。次に例を示します。

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = NULL,
	MIGRATION_STATE = <desired_migration_state>
) )
```
フィルター述語を削除すると、テーブルのすべての行が移行の対象になります。このため、先に Azure からテーブルのすべてのリモート データを戻しておかない限り、後で同じテーブルにフィルター述語を指定することはできません。この制限は、新しいフィルター述語を指定したときに移行対象外となっている行が既に Azure に移行されているという状況を避けるために設けられています。

## テーブルに適用されたフィルター述語を確認する
テーブルに適用されたフィルター述語を確認するには、カタログ ビュー **sys.remote\_data\_archive\_tables** を開き、**filter\_predicate** 列の値を確認します。値が null の場合、テーブル全体がアーカイブの対象になります。詳細については、「[sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx)」を参照してください。

## フィルター述語のセキュリティに関する注意事項  
db\_owner 権限を持つ危害を受けたアカウントによって、次のことが行われる可能性があります。

-   大量のサーバー リソースの消費、あるいは長期間の待機後に、サービス拒否を発生させるテーブル値関数の作成と適用。  

-   ユーザーの読み取りアクセスが明示的に拒否される原因となる、テーブル内容の推測を可能にするテーブル値関数の作成と適用。

## 関連項目

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

<!---HONumber=AcomDC_0622_2016-->