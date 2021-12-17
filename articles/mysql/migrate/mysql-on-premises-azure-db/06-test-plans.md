---
title: 'オンプレミスの MySQL を Azure Database for MySQL に移行する: テスト計画'
description: WWI は、一連の IT タスクとビジネス タスクを含むテスト計画を作成しました。 移行を成功させるには、すべてのテストを実行する必要があります。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: d13a6b63b297aff9504b00449f660f3ba55b6591
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084986"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-test-plans"></a>オンプレミスの MySQL を Azure Database for MySQL に移行する: テスト計画

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>前提条件

[移行の方法](05-migration-methods.md)

## <a name="overview"></a>概要

WWI は、一連の IT タスクとビジネス タスクを含むテスト計画を作成しました。 移行を成功させるには、すべてのテストを実行する必要があります。

テストでは以下を確認します。

  - 移行されたデータベースとオンプレミス テーブルの整合性 (同じレコード数とクエリ結果) を確認します。

  - パフォーマンスが許容できることを確認します (オンプレミスで実行されている場合と同等のパフォーマンスである必要があります)。

  - ターゲット クエリのパフォーマンスが、規定された要件を満たしていることを確認します。

  - オンプレミスと Azure ネットワークの間のネットワーク接続が許容できることを確認します。

  - 指定されたすべてのアプリケーションとユーザーが、移行されたデータ インスタンスに接続できることを確認します。

WWI は、週末に移行を行い、太平洋標準時の午後 10 時から午前 2 時までの時間枠を特定しました。 午前 2 時の目標 (4 時間のダウンタイム目標) までにすべてのテストに成功して移行が完了しなかった場合は、ロールバック手順が開始されました。 問題は今後の移行のために文書化されました。 すべての移行時間枠が前倒しされ、許容できるビジネス タイムラインに基づいて再スケジュールされました。

## <a name="sample-queries"></a>サンプル クエリ

データベースの移行が成功したかどうかを確認するために、ソースとターゲットで一連のクエリが実行されました。 次のクエリとスクリプトは、移行操作によって、必要なすべてのデータベース オブジェクトがソースからターゲットに移動されたかどうかを判断するのに役立ちます。

### <a name="table-rows"></a>テーブルの行

このクエリを使用すると、すべてのテーブルと推定行数を取得できます。

```
SELECT SUM(TABLE_ROWS)
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_SCHEMA = '{SchemaName}';
```

> [!NOTE]
> `INFORMATION_SCHEMA` テーブルでは、テーブル全体の推定値セットが提供されます。 テーブル サイズなどのメトリックをより正確に表示するには、`innodb_stats_transient_sample_pages` サーバー パラメーターを使用してページのサンプル サイズを増加します。 このサーバー値を増加すると、より多くのページが分析され、より正確な結果が得られます。

正確な行数を取得するには、すべてのテーブルに対して `count(*)` SQL ステートメントを実行します。 このコマンドを実行すると、大きなテーブルでは多くの時間がかかることがあります。 次のスクリプトでは、正確な数を取得するために実行できる SQL ステートメントのセットが生成されます。

```
SELECT CONCAT( 
    'SELECT "', 
    table_name, 
    '" AS table_name, COUNT(*) AS exact_row_count FROM `', 
    table_schema, 
    '`.`', 
    table_name, 
    '` UNION ' 
)  
FROM INFORMATION_SCHEMA.TABLES 
WHERE table_schema = '**my_schema**';
```

### <a name="table-fragmentation"></a>テーブルの断片化

データ テーブルは、継続的なアプリケーションの使用によって拡大し続ける可能性があります。 場合によっては、データが増加することはなくても、削除や更新によって常に変更されることがあります。 その場合は、データベース ファイルに多くの断片化が発生する可能性があります。 MySQL OPTIMIZE TABLE ステートメントを使用すると、物理ストレージ領域のニーズを軽減し、I/O 効率を向上させることができます。

[MySQL のテーブルを最適化](https://dev.mysql.com/doc/refman/8.0/en/optimize-table.html)するには、次を実行します。

`optimize table TABLE_NAME`

### <a name="database-objects"></a>データベース オブジェクト

次のクエリを使用して、他のすべてのデータベース オブジェクトが考慮されていることを確認します。 これらのクエリはテーブルの行数を計算できますが、特定のデータベース オブジェクトのバージョンを考慮していない場合があります。 たとえば、ストアド プロシージャが存在する場合でも、移行の開始と終了の間に変更される可能性があります。 移行中はデータベース オブジェクトの開発を止めることをお勧めします。

**ユーザー**

```sql
SELECT DISTINCT 
        USER 
FROM 
        mysql.user 
WHERE 
        user <> '' order by user
```

**インデックス**

```sql
SELECT DISTINCT 
        INDEX_NAME 
FROM 
        information_schema.STATISTICS 
WHERE 
        TABLE_SCHEMA = '{SchemaName}'
```

**制約**

```sql
SELECT DISTINCT 
        CONSTRAINT_NAME 
FROM 
        information_schema.TABLE_CONSTRAINTS 
WHERE 
        CONSTRAINT_SCHEMA = '{SchemaName}'
```

**ビュー**

```sql
SELECT 
        TABLE_NAME 
FROM 
        information_schema.VIEWS 
WHERE 
        TABLE_SCHEMA = '{SchemaName}'
```

**ストアド プロシージャ**

```sql
SELECT 
        ROUTINE_NAME 
FROM 
        information_schema.ROUTINES 
WHERE 
        ROUTINE_TYPE = 'FUNCTION' and 
        ROUTINE_SCHEMA = '{SchemaName}'
```

**関数**

```sql
SELECT 
        ROUTINE_NAME 
FROM 
        information_schema.ROUTINES 
WHERE 
        ROUTINE_TYPE = 'PROCEDURE' and 
        ROUTINE_SCHEMA = '{SchemaName}'
```

**イベント**

```sql
SELECT 
        EVENT_NAME 
FROM 
        INFORMATION_SCHEMA.EVENTS 
WHERE 
        EVENT_SCHEMA = '{SchemaName}'
```

## <a name="rollback-strategies"></a>ロールバック戦略

上記のクエリでは、ロールバックの決定に使用されるオブジェクト名とカウントの一覧が提供されます。 移行ユーザーは、ソース オブジェクトとターゲット オブジェクトの数を確認して、最初のオブジェクト検証手順を実行できます。 オブジェクト数が一致しなくても、必ずしもロールバックが必要であるとは限りません。 詳細な評価を実行すると、その差はわずかで簡単に回復できる場合があります。 いくつかの失敗したオブジェクトを手動で移行することが解決策になる場合があります。 たとえば、すべてのテーブルとデータ行が移行され、一部の関数だけが失われている場合は、これらの失敗したオブジェクトを修復し、移行を完了してください。 データベースが比較的小さい場合は、Azure Database for MySQL インスタンスをクリアし、移行を再開することができます。 大きなデータベースで不足しているオブジェクトを特定するには、移行期間に使用可能な時間よりも長い時間が必要になることがあります。 移行を停止してロールバックする必要があります。

不足しているデータベース オブジェクトの特定は、移行期間中に迅速に実行する必要があります。 1 分 1 分が重要です。 1 つの方法として、環境オブジェクト名をファイルにエクスポートし、データ比較ツールを使用して、不足しているオブジェクトをすばやく特定することができます。 もう 1 つの方法として、ソース データベース オブジェクト名をエクスポートし、そのデータをターゲット データベース環境の一時テーブルにインポートすることもできます。 **スクリプト化** および **テスト** された SQL ステートメントを使用してデータを比較します。 データの検証速度と正確性は、移行プロセスにとって重要です。 移行期間中に、データベース オブジェクトの長い一覧を手動で読み取ったり検証したりすることは避けてください。 人為的なエラーの可能性が非常に高くなります。 ツールを使用して例外によって管理するのが最良の方法です。

## <a name="wwi-scenario"></a>WWI のシナリオ

WWI の CIO は、すべてのデータベース オブジェクトがオンプレミス データベースから Azure Database for MySQL インスタンスに移行されたことを示す確認レポートを受け取りました。 データベース チームは、移行の開始前にデータベースに対して上記のクエリを実行し、すべての結果をスプレッドシートに保存しました。

ターゲットの移行オブジェクトの忠実性を確認するために、ソースのデータベース スキーマ情報が使用されました。

## <a name="test-plans-checklist"></a>テスト計画のチェックリスト

  - テスト クエリをスクリプト化してテストし、いつでも実行できるようにします。

  - テスト クエリの実行にかかる時間を把握し、ドキュメント化された移行タイムラインの一部として使用します。

  - さまざまな可能性のある結果に対して、軽減策とロールバック戦略を用意します。

  - 移行のためのイベントのタイムラインを明確に定義します。  


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [パフォーマンス ベースライン](./07-performance-baselines.md)