<properties
   pageTitle="SQL Data Warehouse のトランザクション | Microsoft Azure"
   description="ソリューション開発のための、Azure SQL Data Warehouse でのトランザクションの実装に関するヒント。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL Data Warehouse のトランザクション

SQL Data Warehouse では、すべてのトランザクション プロパティをサポートしています。ただし、SQL Data Warehouse のパフォーマンスを大規模に維持できるように、SQL Server と比べて一部の機能が制限されています。この記事では、相違点について説明し、その他の制限事項を示します。

## トランザクション分離レベル
SQL Data Warehouse では、ACID トランザクションを実装しています。ただし、トランザクション サポートの分離は、`READ UNCOMMITTED` に制限されており、これを変更することはできません。データのダーティ リードが問題である場合、これを防ぐためのさまざまなコーディング方法を実装できます。最も一般的な方法では、CTAS とテーブル パーティションの切り替え (スライディング ウィンドウ パターンとよく呼ばれます) の両方を利用して、ユーザーがまだ準備中のデータを照会できないようにします。データを事前にフィルター処理するビューも、よく使用される方法です。

## トランザクションの状態
SQL Data Warehouse では、XACT_STATE() 関数の値 -2 を使用して、失敗したトランザクションを報告します。これは、トランザクションが失敗し、ロールバックのためにのみマークされていることを意味します。

> [AZURE.NOTE]失敗したトランザクションを示すために XACT_STATE 関数の -2 を使用するのは、SQL Server とは異なる動作です。SQL Server では、コミットできないトランザクションを表すために値 -1 を使用します。SQL Server では、コミット不可としてマークしなくても、トランザクション内で一部のエラーを許容できます。たとえば、SELECT 1/0 はエラーになりますが、トランザクションを強制的にコミット不可状態にすることはありません。また、SQL Server では、コミットできないトランザクションでの読み取りも許可されます。しかし、SQLDW ではこれは許可されません。SELECT 1/0 のエラーも含め、SQLDW トランザクション内でエラーが発生すると、自動的に -2 状態になります。そのため、アプリケーション コードを調べて、XACT_STATE() を使用しているかどうかを確認することが重要です。

SQL Server では、次のようなコード フラグメントを目にすることがあります。

```
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        ROLLBACK TRAN;

    END CATCH;
```

`ROLLBACK` ステートメントの前に `SELECT` ステートメントがあることに注意してください。また、`@xact` 変数の設定で、`SELECT` ではなく DECLARE が使用されています。

SQL Data Warehouse では、このコードは次のように記述する必要があります。

```
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        ROLLBACK TRAN;

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

SELECT @xact;
```

`CATCH` ブロック内のエラー情報を読み取る前に、トランザクションのロールバックが発生しなければなりません。

## Error_Line() 関数
SQL Data Warehouse では、ERROR_LINE() 関数を実装およびサポートしていないことにも注意してください。この関数がコードに含まれている場合は、SQL Data Warehouse に準拠するために削除する必要があります。代わりに、コードでクエリ ラベルを使用して同等の機能を実装します。この機能の詳細については、[クエリ ラベル] に関する記事をご覧ください。

## THROW と RAISERROR の使用
THROW は、SQL Data Warehouse で例外を発生させるための最新の実装ですが、RAISERROR もサポートされています。ただし、注意が必要な相違点がいくつかあります。

- THROW では、ユーザー定義のエラー メッセージの番号として、100,000 ～ 150,000 の範囲の番号を指定することはできません。
- RAISERROR のエラー メッセージは 50,000 に固定されています。
- sys.messages の使用はサポートされていません。

## 制限事項
SQL Data Warehouse には、トランザクションに関連する他の制限事項があります。

制限事項は次のとおりです。

- 分散トランザクションは使用できません。
- 入れ子になったトランザクションは使用できません。
- セーブ ポイントは使用できません。

## 次のステップ
開発のその他のヒントについては、[開発の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[開発の概要]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO4-->