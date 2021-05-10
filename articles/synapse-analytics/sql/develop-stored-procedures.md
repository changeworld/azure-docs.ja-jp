---
title: ストアド プロシージャの使用
description: ソリューション開発のための Azure Synapse Analytics での Synapse SQL を使用したストアド プロシージャの実装に関するヒント。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/03/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 083077b2b29bc415e83d6f5a76941fa83fa853e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674149"
---
# <a name="stored-procedures-using-synapse-sql-in-azure-synapse-analytics"></a>Azure Synapse Analytics での Synapse SQL を使用したストアド プロシージャ

Synapse SQL のプロビジョニングされたおよびサーバーレスのプールを使用すると、複雑なデータ処理ロジックを SQL ストアド プロシージャに配置できます。 ストアド プロシージャは、SQL コードをカプセル化し、データ ウェアハウスのデータの近くに格納するための優れた方法です。 ストアド プロシージャは、開発者がコードを管理しやすい単位にカプセル化して、ソリューションをモジュール化することに役立ち、コードの再利用性が大幅に促進されます。 ストアド プロシージャの柔軟性をさらに高めるために、各ストアド プロシージャはパラメーターを受け入れることもできます。
この記事では、ソリューション開発のための Synapse SQL プールでのストアド プロシージャの実装に関するヒントをいくつか紹介します。

## <a name="what-to-expect"></a>ウィザードの内容

Synapse SQL では、SQL Server で使用される多数の T-SQL 機能がサポートされています。 さらに重要なのは、ソリューションのパフォーマンスを最大限にするために使用できる、スケールアウト専用の機能が用意されていることです。 この記事では、ストアド プロシージャに配置できる機能について説明します。

> [!NOTE]
> プロシージャ本体では、Synapse SQL のセキュリティでサポートされている機能のみを使用できます。 [この記事](overview-features.md)では、ストアド プロシージャで使用できるオブジェクトとステートメントを確認します。 これらの記事の例では、サーバーレスおよび専用の両方の領域で使用できる汎用機能が使用されています。 この記事の最後にある[プロビジョニングおよびサーバーレスの Synapse SQL プールに関する追加の制限事項](#limitations)を参照してください。

SQL プールのスケールとパフォーマンスを維持するために、動作が異なる機能もあれば、サポートされていない機能もあります。

## <a name="stored-procedures-in-synapse-sql"></a>Synapse SQL でのストアド プロシージャ

次の例では、外部オブジェクトがデータベースに存在する場合に削除するプロシージャを確認できます。

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

これらのプロシージャは、プロシージャ名とパラメーターを指定できる `EXEC` ステートメントを使用して実行できます。

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

Synapse SQL では、簡素化され、合理化されたストアド プロシージャの実装が提供されます。 SQL Server との最大の違いは、ストアド プロシージャがプリコンパイル済みコードではないことです。 データ ウェアハウスでは、大量のデータに対してクエリを実行するのにかかる時間に比べて、短いコンパイル時間で済みます。 より重要なことは、ストアド プロシージャのコードが大量のクエリに対して正しく最適化されることを確認することです。 目標は、数時間、数分、(数ミリ秒ではなく) 数秒節約することです。 したがって、ストアド プロシージャは、SQL ロジックのコンテナーと考えるとよいでしょう。

Synapse SQL でストアド プロシージャが実行されると、実行時に SQL ステートメントが解析され、変換され、最適化されます。 このプロセスの中で、各ステートメントが分散クエリに変換されます。 データに対して実行される SQL コードは、送信されるクエリとは異なります。

## <a name="encapsulate-validation-rules"></a>検証規則をカプセル化する

ストアド プロシージャを使用すると、SQL データベースに格納されている単一のモジュールで検証ロジックを見つけることができます。 次の例では、パラメーターの値を検証し、それらの既定値を変更する方法を確認できます。

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

プロシージャが呼び出されると、sql プロシージャのロジックによって入力パラメーターが検証されます。

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>ストアド プロシージャの入れ子

ストアド プロシージャが他のストアド プロシージャを呼び出すか、動的 SQL を実行する場合、内側のストアド プロシージャまたはコードの呼び出しは "入れ子になっている" といわれます。
入れ子になったプロシージャの例を次のコードに示します。

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

このプロシージャは、何らかの名前を表すパラメーターを受け取り、他のプロシージャを呼び出して、この名前を持つオブジェクトを削除します。
Synapse SQL プールでは、最大 8 レベルの入れ子がサポートされます。 この機能は SQL Server とは少し異なります。 SQL Server の入れ子レベルは 32 です。

最上位のストアド プロシージャの呼び出しは、入れ子レベル 1 になります。

```sql
EXEC clean_up 'mytest'
```

ストアド プロシージャから別の EXEC の呼び出しも行う場合、入れ子レベルは 2 に増えます。

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

次に 2 つ目の手順で何らかの動的 SQL を実行すると、入れ子レベルは 3 に増えます。

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> 現在、Synapse SQL では、[@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?view=azure-sqldw-latest&preserve-view=true) はサポートされていません。 入れ子レベルを追跡する必要があります。 8 つの入れ子レベルの制限を超過する可能性はほとんどありませんが、もし超過した場合は、入れ子レベルがこの制限内に収まるように、コードを修正する必要があります。

## <a name="insertexecute"></a>INSERT..EXECUTE

プロビジョニングされた Synapse SQL プールでは、INSERT ステートメントでストアド プロシージャの結果セットを使用することはできません。 使用できる別の方法があります。 例については、プロビジョニングされた Synapse SQL プールの[一時テーブル](develop-tables-temporary.md)に関する記事を参照してください。

## <a name="limitations"></a>制限事項

Synapse SQL に実装されていない Transact-SQL ストアド プロシージャには、次のようないくつかの側面があります。

| 機能/オプション | プロビジョニング済み | サーバーレス |
| --- | --- |
| 一時ストアド プロシージャ | いいえ | はい |
| 番号付きストアド プロシージャ | いいえ | いいえ |
| 拡張ストアド プロシージャ | いいえ | いいえ |
| CLR ストアド プロシージャ | いいえ | いいえ |
| 暗号化オプション | いいえ | はい |
| レプリケーション オプション | いいえ | いいえ |
| テーブル値パラメーター | いいえ | いいえ |
| 読み取り専用パラメーター | いいえ | いいえ |
| 既定のパラメーター | いいえ | はい |
| 実行コンテキスト | いいえ | いいえ |
| Return ステートメント | いいえ | はい |
| INSERT INTO .. EXEC | いいえ | はい |

## <a name="next-steps"></a>次のステップ

開発についてのその他のヒントは、[開発の概要](develop-overview.md)に関するページをご覧ください。
