<properties
	pageTitle="インメモリ OLTP を使用して Azure SQL のトランザクション パフォーマンスを向上させる | Microsoft Azure"
	description="インメモリ OLTP を導入して、既存の SQL Database のトランザクション パフォーマンスを向上させます。"
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# インメモリを使用して Azure SQL アプリケーションのパフォーマンスを向上させる (プレビュー)

[インメモリ](sql-database-in-memory.md)を使用して既存の Premium Azure SQL Database のトランザクション パフォーマンスを最適化するには、次の手順に従います。

比較のために、実際の運用ワークロードに近いワークロードを選択し、同時接続数と読み取り/書き込みの比率を設定してください。ネットワーク待ち時間を最小限に抑えるために、データベースと同じ Azure リージョンでテスト ワークロードを実行することをお勧めします。

## 手順 1. 新しい Premium データベースに実際のデータをコピーする
1.	次のいずれかを使用して、bacpac に実際の運用データベースをエクスポートします。

	A.[ポータル](https://portal.azure.com/)のエクスポート機能

	B.SQL Server Management Studio のデータ層アプリケーションのエクスポート機能

2.	V12 サーバー内の新しい Premium データベースに bacpac をインポートします。

	A.ポータルの場合: サーバーに移動し、[データベースのインポート] オプションを選択します。必ず、Premium 価格レベルを選択してください。

	B.SQL Server Management studio (SSMS) の場合: サーバーに接続し、[データベース] ノードを右クリックして、[データ層アプリケーションのインポート] を選択します。


## 手順 2. インメモリ OLTP に移行するオブジェクトを特定する
SQL Server Management Studio (SSMS) には、アクティブなワークロードがあるデータベースに対して実行できるトランザクション パフォーマンス分析レポートが含まれています。これを使用して、インメモリ OLTP への移行の候補となるテーブルとストアド プロシージャを特定できます。詳細については、「[テーブルまたはストアド プロシージャをインメモリ OLTP に移植する必要があるかどうかの確認](https://msdn.microsoft.com/library/dn205133.aspx)」を参照してください。

1.	SSMS を使用して運用データベースに接続します。新しいテスト データベースに対して実行されるワークロードがある場合は、そのデータベースに接続することもできます。
2.	データベースを右クリックし、[レポート]、[標準レポート]、[トランザクション パフォーマンス分析レポート] の順に選択します。このレポートを使用して、インメモリ OLTP の恩恵を受ける可能性があるテーブルとストアド プロシージャを使用状況に基づいて特定できます。


## 手順 3. テーブルを移行する
1.	SSMS を使用して、新しいテスト データベースに接続します。クエリで WITH (SNAPSHOT) オプションを使用しなくても済むように、データベース オプション MEMORY\_OPTIMIZED\_ELEVATE\_TO\_SNAPSHOT を設定することをお勧めします。
2.	新しいテスト データベースに接続できたら、次のコマンドを実行します。

   	    ALTER DATABASE CURRENT SET MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT=ON

3.	次の方法のいずれかを使用して、ディスク ベース テーブルをメモリ最適化テーブルに移行します。

	A.SSMS のメモリ最適化ウィザード: テスト データベースに接続しているときに、テーブルを右クリックし、[メモリ最適化アドバイザー] を選択します。このアドバイザーを使用して、テーブルにメモリ最適化でサポートされていない機能があるかどうかを判断できます。ない場合は、アドバイザーで実際のスキーマとデータ移行を実行できます。詳細については、[MSDN のメモリ最適化アドバイザーに関するトピック](https://msdn.microsoft.com/library/dn284308.aspx)を参照してください。

	B.手動による移行: SSMS を使用して、新しいテスト データベースに接続します。

次の手順に従って、テーブルを移行します。

1.	テーブルのスクリプトを作成します。そのために、テーブルを右クリックし、[テーブルをスクリプト化]、[CREATE]、[新しいクエリ ウィンドウ] の順に選択します。
2.	CLUSTERED インデックスを NONCLUSTERED に変更し、オプション WITH (MEMORY\_OPTIMIZED=ON) を追加します。
3.	サポートされていない機能がテーブルで使用されている場合は、回避策を実装します。MSDN で、[サポートされていない一般的な機能](https://msdn.microsoft.com/library/dn247639.aspx)に対処する方法が説明されています。
4.	sp\_rename を使用して、既存のテーブルの名前を変更します。
5.	CREATE TABLE スクリプトを実行して、新しいメモリ最適化テーブルを作成します。
6.	ステートメント INSERT INTO <new_memory_optimized_table> SELECT * FROM <old_disk_based_table> を実行して、データをコピーします。

## 手順 4 (省略可能). ストアド プロシージャを移行する

[SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 2015 年 9 月版プレビュー以降を使用して、新しいテスト データベースに接続します。

元のプロシージャに対して[ネイティブ コンパイル アドバイザー](https://msdn.microsoft.com/library/dn284308.aspx)を実行して、ネイティブにコンパイルされたストアド プロシージャでサポートされないすべての機能を特定します。サポートされない一般的な機能に対する回避策については、[MSDN](https://msdn.microsoft.com/library/dn296678.aspx) を参照してください。

ストアド プロシージャをネイティブに移行する際、次の 2 つの点を考慮してください。

- ネイティブ モジュールに次のオプションが必要です。

	- NATIVE\_COMPILATION
	- SCHEMABINDING



- ネイティブ モジュールでは、トランザクションの管理に [ATOMIC ブロック](https://msdn.microsoft.com/library/dn452281.aspx)が使用されます。明示的な BEGIN TRAN/COMMIT/ROLLBACK ステートメントは必要ありません。

一般的なネイティブ コンパイル ストアド プロシージャは次のようになります。


   	    CREATE PROCEDURE schemaname.procedurename
   		@param1 type1, …
   		WITH NATIVE_COMPILATION, SCHEMABINDING
   		AS
   		BEGIN ATOMIC WITH (TRANSACTION ISOLATION LEVEL = SNAPSHOT, LANGUAGE = N'your_language')

   		…

   		END



SNAPSHOT はメモリ最適化テーブルで最もよく使用されている分離レベルですが、REPEATABLE READ と SERIALIZABLE もサポートされています。

##### 次の手順に従って、ストアド プロシージャを移行します。

1.	ストアド プロシージャのスクリプトを作成します。そのために、ストアド プロシージャを右クリックし、[ストアド プロシージャをスクリプト化]、[CREATE]、[新しいクエリ ウィンドウ] の順に選択します。
2.	上記のテンプレートを使用してプロシージャ ヘッダーを書き換え、すべての BEGIN TRAN/ROLLBACK/COMMIT ステートメントを削除します。
3.	サポートされていない機能がストアド プロシージャで使用されている場合は、回避策を実装します。MSDN で、[サポートされていない一般的な機能](https://msdn.microsoft.com/library/dn296678.aspx)に対処する方法が説明されています。
4.	プロシージャを DROP するか、sp\_rename を使用して元のプロシージャの名前を変更します。
5.	CREATE PROCEDURE スクリプトを実行して、新しいネイティブ コンパイル ストアド プロシージャを作成します。

## 手順 5. ワークロードを実行する
メモリ最適化テーブルおよびネイティブ コンパイル ストアド プロシージャに対してテスト ワークロードを実行し、パフォーマンスの向上を測定します。

## 次のステップ

[インメモリ ストレージを監視する](https://azure.microsoft.com/documentation/articles/sql-database-in-memory-oltp-monitoring/)

[動的管理ビューを使用した Azure SQL Database の監視](sql-database-monitoring-with-dmvs.md)

<!---HONumber=Nov15_HO1-->