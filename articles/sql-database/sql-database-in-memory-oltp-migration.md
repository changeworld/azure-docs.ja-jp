---
title: インメモリ OLTP による SQL のトランザクション パフォーマンスの向上 | Microsoft Docs
description: インメモリ OLTP を導入して、既存の SQL Database のトランザクション パフォーマンスを向上させます。
services: sql-database
documentationcenter: ''
author: jodebrui
manager: jhubbard
editor: MightyPen

ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2016
ms.author: jodebrui

---
# インメモリ OLTP (プレビュー) を使用して SQL Database のアプリケーション パフォーマンスを向上させる
[インメモリ OLTP](sql-database-in-memory.md) を使用すると、パフォーマンス レベルを上げることなく、[Premium](sql-database-service-tiers.md) Azure SQL Databases で OLTP ワークロードのパフォーマンスを高めることができます。

既存のデータベースでインメモリ OLTP を採用するには、以下の手順に従います。

## 手順 1. Premium データベースでインメモリ OLTP がサポートされていることを確認する
2015 年 11 月以降に作成された Premium データベースは、インメモリ機能をサポートしています。Premium データベースがインメモリ機能をサポートしているかどうかを確認するには、次の Transact-SQL ステートメントを実行します。返された結果が (0 ではなく) 1 である場合は、インメモリがサポートされています。

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* は、*Extreme Transaction Processing* (大量トランザクション処理) の略です。

既存のデータベースを新しい V12 Premium データベースに移行する必要がある場合は、次の手順に従って、データをいったんエクスポートしてからインポートできます。

#### エクスポートの手順
次のいずれかを使用して、bacpac に実際の運用データベースをエクスポートします。

* [ポータル](https://portal.azure.com/)の[エクスポート](sql-database-export.md)機能
* [最新の SSMS.exe](http://msdn.microsoft.com/library/mt238290.aspx) (SQL Server Management Studio) の**データ層アプリケーションのエクスポート**機能
  
  1. **オブジェクト エクスプローラー**で、**[データベース]** ノードを展開します。
  2. 使用するデータベース ノードを右クリックします。
  3. **[タスク]**、**[データ層アプリケーションのエクスポート]** の順にクリックします。
  4. 表示されたウィザード ウィンドウの指示に従います。

#### インポートの手順
新しい Premium データベースに bacpac をインポートします。

1. Azure [ポータル](https://portal.azure.com/)で次の操作を行います。
   
   * サーバーに移動します。
   * [[データベースのインポート]](sql-database-import.md) オプションを選択します。
   * Premium 価格レベルを選択します。
2. SSMS を使用して、bacpac をインポートします。
   
   * **オブジェクト エクスプローラー**で、**[データベース]** ノードを右クリックします。
   * **[データ層アプリケーションのインポート]** をクリックします。
   * 表示されたウィザード ウィンドウの指示に従います。

## 手順 2. インメモリ OLTP に移行するオブジェクトを特定する
SSMS には、アクティブなワークロードがあるデータベースに対して実行できる**トランザクション パフォーマンス分析の概要**レポートが用意されています。このレポートを使用して、インメモリ OLTP への移行の候補となるテーブルとストアド プロシージャを特定できます。

SSMS でこのレポートを生成するには、

* **オブジェクト エクスプローラー**でデータベース ノードを右クリックし、
* **[レポート]**、**[標準レポート]**、**[トランザクション パフォーマンス分析の概要]** の順にクリックします。

詳細については、「[テーブルまたはストアド プロシージャをインメモリ OLTP に移植する必要があるかどうかの確認](http://msdn.microsoft.com/library/dn205133.aspx)」を参照してください。

## 手順 3. 比較用のテスト データベースを作成する
メモリ最適化テーブルに変換するとメリットが得られるテーブルがデータベース内にあるとレポートに記載されていたとします。このような場合は、まずテストを実施して、指摘された点について確認することをお勧めします。

そのためには、運用データベースのテスト コピーが必要です。テスト データベースは、運用データベースと同じレベルのサービス層に配置する必要があります。

テストを容易にするために、テスト データベースを次のように調整します。

1. SSMS を使用して、テスト データベースに接続します。
2. クエリで WITH (SNAPSHOT) オプションを使用しなくても済むように、次の T-SQL ステートメントに示すようにデータベース オプションを設定します。
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## 手順 4. テーブルを移行する
テストするテーブルのメモリ最適化コピーを作成し、データを入力する必要があります。次のいずれかを使用して作成できます。

* SSMS の便利なメモリ最適化ウィザード
* 手動による T-SQL の実行

#### SSMS のメモリ最適化ウィザード
この移行オプションを使用する手順は、以下のとおりです。

1. SSMS を使用して、テスト データベースに接続します。
2. **オブジェクト エクスプローラー**で、テーブルを右クリックし、**メモリ最適化アドバイザー**をクリックします。
   
   * **テーブル メモリ オプティマイザー アドバイザー** ウィザードが表示されます。
3. ウィザードで、**[移行の検証]** (または **[次へ]** ボタン) をクリックし、メモリ最適化テーブルでサポートされていない機能がテーブルに含まれているかどうかを確認します。詳細については、次を参照してください。
   
   * 「[メモリ最適化アドバイザー](http://msdn.microsoft.com/library/dn284308.aspx)」の*メモリ最適化のチェック リスト*
   * [インメモリ OLTP でサポートされていない Transact-SQL の構造](http://msdn.microsoft.com/library/dn246937.aspx)
   * [インメモリ OLTP への移行](http://msdn.microsoft.com/library/dn247639.aspx)
4. サポートされていない機能がテーブルになければ、アドバイザーで実際のスキーマとデータの移行を実行できます。

#### 手動による T-SQL の実行
この移行オプションを使用する手順は、以下のとおりです。

1. SSMS (または同様のユーティリティ) を使用して、テスト データベースに接続します。
2. 使用するテーブルとそのインデックスに対応した完全な T-SQL スクリプトを取得します。
   
   * SSMS で、テーブル ノードを右クリックします。
   * **[テーブルをスクリプト化]**、**[作成先]**、**[新しいクエリ ウィンドウ]** の順にクリックします。
3. スクリプト ウィンドウで、WITH (MEMORY\_OPTIMIZED = ON) を CREATE TABLE ステートメントに追加します。
4. CLUSTERED インデックスがある場合は、NONCLUSTERED に変更します。
5. SP\_RENAME を使用して、既存のテーブルの名前を変更します。
6. 編集した CREATE TABLE スクリプトを実行して、テーブルのメモリ最適化コピーを新規作成します。
7. INSERT...SELECT * INTO を使用して、このメモリ最適化テーブルにデータをコピーします。

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## 手順 5 (省略可能). ストアド プロシージャを移行する
インメモリ機能では、ストアド プロシージャに変更を加えて、パフォーマンスを向上することもできます。

### ネイティブ コンパイル ストアド プロシージャに関する考慮事項
ネイティブ コンパイル ストアド プロシージャでは、T-SQL WITH 句に次のオプションが必要です。

* NATIVE\_COMPILATION
* SCHEMABINDING: これを指定されたテーブルでは、ストアド プロシージャにより、そのストアド プロシージャを削除しない限り、ストアド プロシージャに影響を与えるような変更を列の定義に加えることができません。

ネイティブ モジュールでは、トランザクション管理用に大きな [ATOMIC ブロック](http://msdn.microsoft.com/library/dn452281.aspx)を 1 つ使用する必要があります。明示的 BEGIN TRANSACTION、または ROLLBACK TRANSACTION の役割はありません。コードでビジネス規則違反が検出されると、[THROW](http://msdn.microsoft.com/library/ee677615.aspx) ステートメントを含むアトミック ブロックが停止する可能性があります。

### ネイティブ コンパイル向けの一般的な CREATE PROCEDURE
ネイティブ コンパイル ストアド プロシージャを作成する T-SQL は、一般的に、次のテンプレートのようになります。

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* TRANSACTION\_ISOLATION\_LEVEL は、SNAPSHOT がネイティブ コンパイル ストアド プロシージャで最も一般的な値です。ただし、次のような他の値も少数ながらサポートされています。
  
  * REPEATABLE READ
  * SERIALIZABLE
* LANGUAGE の値は、sys.languages ビューに存在している必要があります。

### ストアド プロシージャを移行する方法
移行の手順は次のとおりです。

1. 通常の解釈されたストアド プロシージャを作成する CREATE PROCEDURE スクリプトを取得します。
2. 前述のテンプレートと一致するようにヘッダーを書き直します。
3. ネイティブ コンパイル ストアド プロシージャでサポートされていない機能がこのストアド プロシージャの T-SQL コードで使用されているかどうかを確認します。必要な場合は、回避策を実装します。
   
   * 詳細については、「[ネイティブ コンパイル ストアド プロシージャの移行に関する問題](http://msdn.microsoft.com/library/dn296678.aspx)」を参照してください。
4. SP\_RENAME を使用して、元のストアド プロシージャの名前を変更します。または、削除します。
5. 編集した CREATE PROCEDURE T-SQL スクリプトを実行します。

## 手順 6. ワークロードをテスト実行する
運用データベースで実行するワークロードと同様のワークロードをテスト データベースで実行します。これにより、テーブルとストアド プロシージャにインメモリ機能を使用したことでパフォーマンスがどれほど向上したかがわかります。

ワークロードの主な属性は次のとおりです。

* 同時接続数
* 読み取り/書き込みの比率

テスト ワークロードを調整して実行するには、[このページ](sql-database-in-memory.md)で説明されている、便利な ostress.exe ツールの使用を検討してください。

ネットワーク待ち時間を最小限に抑えるために、データベースが存在するのと同じ Azure リージョンでテストを実行してください。

## 手順 7. 実装後の監視
運用環境でインメモリ機能の実装によるパフォーマンスへの影響を監視することを検討してください。

* [インメモリ ストレージを監視する](sql-database-in-memory-oltp-monitoring.md)
* [動的管理ビューを使用した Azure SQL Database の監視](sql-database-monitoring-with-dmvs.md)

## 関連リンク
* [インメモリ OLTP (インメモリ最適化)](http://msdn.microsoft.com/library/dn133186.aspx)
* [ネイティブ コンパイル ストアド プロシージャの概要](http://msdn.microsoft.com/library/dn133184.aspx)
* [メモリ最適化アドバイザー](http://msdn.microsoft.com/library/dn284308.aspx)

<!---HONumber=AcomDC_0720_2016-->