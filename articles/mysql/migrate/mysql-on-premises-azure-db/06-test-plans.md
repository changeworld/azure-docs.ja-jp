---
title: オンプレミスの MySQL から Azure Database for MySQL への移行ガイドのテスト計画
description: WWI では、一連の IT とビジネス タスクが含まれたテスト計画を作成しました。 移行を成功させるには、すべてのテストを実行する必要があります。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/11/2021
ms.openlocfilehash: ad8a0ef111368f81075acaf68fec9e653534f7e4
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112082954"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-test-plans"></a>オンプレミスの MySQL から Azure Database for MySQL への移行ガイドのテスト計画

## <a name="prerequisites"></a>前提条件

[移行の方法](05-migration-methods.md)

## <a name="overview"></a>概要

WWI では、一連の IT とビジネス タスクが含まれたテスト計画を作成しました。 移行を成功させるには、すべてのテストが実行される必要があります。

テストでは、

  - 移行されたデータベースとオンプレミス テーブルの整合性 (同じレコード数とクエリ結果) が確認されます。

  - パフォーマンスが許容範囲内であることが確認されます (オンプレミスで実行されている場合と同等のパフォーマンスである必要があります)。

  - ターゲット クエリのパフォーマンスが、規定された要件を満たしていることが確認されます。

  - オンプレミスと Azure ネットワーク間のネットワーク接続が許容範囲内であることが確認されます。

  - 特定されたすべてのアプリケーションとユーザーが、移行されたデータ インスタンスに接続できることが確認されます。

WWI では、太平洋標準時の午後 10 時に開始されて午前 2 時に終了した移行の週末と時間枠を特定しました。 午前 2 時の目標 (4 時間のダウンタイム目標) までにすべてのテストに合格して移行が完了しなかった場合、ロールバック手順が開始されました。 今後の移行のために問題が文書化されました。 すべての移行時間枠が前倒しされ、許容できるビジネス タイムラインに基づいて再スケジュールされました。

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

次のクエリを使用して、他のすべてのデータベース オブジェクトが考慮されていることを確認します。 これらのクエリではテーブルの行数を計算できますが、特定のデータベース オブジェクトのバージョンが考慮されない場合があります。 たとえば、ストアド プロシージャが存在する場合でも、移行の開始と終了の間に変更されている可能性があります。 移行中はデータベース オブジェクトの開発を凍結することをお勧めします。

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

上記のクエリでは、ロールバックの決定に使用されるオブジェクト名とカウントの一覧が提供されます。 移行ユーザーは、ソースとターゲット オブジェクトの数を確認して、最初のオブジェクト検証手順を実行できます。 オブジェクト数が一致しなくても、必ずしもロールバックが必要であるとは限りません。 詳細な評価を実行すると、その差はわずかであり、簡単に回復できることが判明する場合があります。 いくつかの失敗したオブジェクトを手動で移行することが解決策になる場合があります。 たとえば、すべてのテーブルとデータ行が移行され、一部の関数だけが失われていた場合は、それらの失敗したオブジェクトを修復し、移行を完了します。 データベースが比較的小さい場合は、Azure Database for MySQL インスタンスをクリアし、移行を再開することもできます。 大きなデータベースで不足しているオブジェクトを特定するには、移行期間に使用可能な時間よりも長い時間が必要になることがあります。 移行を停止してロールバックする必要があります。

不足しているデータベース オブジェクトの特定は、移行期間中に迅速に実行する必要があります。 一分一秒が重要です。 1 つの方法としては、環境オブジェクト名をファイルにエクスポートし、データ比較ツールを使用して、不足しているオブジェクトをすばやく特定することができます。 もう 1 つの方法として、ソース データベース オブジェクト名をエクスポートし、そのデータをターゲット データベース環境の一時テーブルにインポートすることもできます。 **スクリプト** 化および **テスト** された SQL ステートメントを使用してデータを比較します。 データの検証速度と正確性は、移行プロセスにとって重要です。 移行期間中にデータベース オブジェクトの長い一覧を手動で読み取ったり検証したりすることは避けてください。 人為的なエラーの可能性が非常に高くなります。 ツールを使用して例外によって管理するのが最良の方法です。

## <a name="wwi-scenario"></a>WWI のシナリオ

WWI の CIO は、すべてのデータベース オブジェクトがオンプレミス データベースから Azure Database for MySQL インスタンスに移行されたことを示す確認レポートを受け取りました。 データベース チームは、移行の開始前にデータベースに対して上記のクエリを実行し、すべての結果をスプレッドシートに保存しました。

ターゲットの移行オブジェクトの忠実性を確認するために、ソースのデータベース スキーマ情報が使用されました。

## <a name="test-plans-checklist"></a>テスト計画のチェックリスト

  - テスト クエリをスクリプト化してテストし、いつでも実行できるようにします。

  - テスト クエリの実行にかかる時間を把握し、ドキュメント化された移行タイムラインの一部にします。

  - さまざまな潜在的結果に対して、軽減策とロールバック戦略を用意します。

  - 移行のためのイベントのタイムラインを明確に定義します。  


> [!div class="nextstepaction"]
> [パフォーマンス ベースライン](./07-performance-baselines.md)