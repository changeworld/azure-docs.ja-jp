<properties
	pageTitle="Stretch Database の管理とトラブルシューティング | Microsoft Azure"
	description="Stretch Database の管理とトラブルシューティングの方法について説明します。"
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

# Stretch Database の管理とトラブルシューティング

Stretch Database を管理し、問題を解決するには、このトピックで説明するツールと方法を利用します。

## ローカル データを管理する

### <a name="LocalInfo"></a>Stretch Database が有効になっているローカルのデータベースとテーブルに関する情報を取得する
Stretch が有効になっている SQL Server のデータベースとテーブルに関する情報を確認するには、カタログ ビューの **sys.databases** と **sys.tables** を開きます。詳細については、「[sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx)」と「[sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx)」を参照してください。

SQL Server で Stretch を有効にしたテーブルにより使用されている領域の量を表示するには、次のステートメントを実行します。

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## データ移行を管理する

### テーブルに適用されたフィルター述語を確認する
カタログ ビューの **sys.remote\_data\_archive\_tables** を開き、**filter\_predicate** 列の値を確認し、移行する行を選択するために Stretch Database で使用されている関数を特定します。値が null の場合、テーブル全体が移行の対象になります。詳細については、「[sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx)」を参照してください。

### <a name="Migration"></a>データ移行の状態を確認する
SQL Server Management Studio でデータベースに **[タスク]、[Stretch]、[監視]** を選択し、Stretch Database Monitor でデータ移行を監視します。詳細については、「[データ移行の監視とトラブルシューティング (Stretch Database)](sql-server-stretch-database-monitor.md)」を参照してください。

あるいは、動的管理ビューの **sys.dm\_db\_rda\_migration\_status** を開き、移行されたデータのバッチ数と行数を確認します。

### <a name="Firewall"></a>データ移行のトラブルシューティング
トラブルシューティングのヒントについては、「[Monitor and troubleshoot data migration (Stretch Database)](sql-server-stretch-database-monitor.md)」 (データ移行の監視とトラブルシューティング (Stretch Database)) を参照してください。

## リモート データを管理する

### <a name="RemoteInfo"></a>Stretch Database で使用されるリモートのデータベースとテーブルに関する情報を取得する
移行されたデータが保存されているリモートのデータベースとテーブルに関する情報を確認するには、カタログ ビューの **sys.remote\_data\_archive\_databases** と **sys.remote\_data\_archive\_tables** を開きます。詳細については、「[sys.remote\_data\_archive\_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx)」と「[sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx)」を参照してください。

Azure で Stretch を有効にしたテーブルにより使用されている領域の量を表示するには、次のステートメントを実行します。

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### 移行したデータを削除する  
既に Azure に移行されているデータを削除するには、[sys.sp\_rda\_reconcile\_batch](https://msdn.microsoft.com/library/mt707768.aspx) の手順に従います。

## テーブル スキーマを管理する

### リモート テーブルのスキーマは変更しないでください。
Stretch Database に構成された SQL Server テーブルに関連付けられているリモート Azure テーブルのスキーマは変更しないでください。特に、列の名前とデータ型は変更しないでください。Stretch Database 機能では、さまざまな場面で、SQL Server テーブルのスキーマとの関連でリモート テーブルのスキーマを推測します。リモート スキーマを変更すると、Stretch Database は変更されたテーブルの動作を停止します。

### テーブル列を調整する  
リモート テーブルから列を間違って削除してしまった場合、**sp\_rda\_reconcile\_columns** を実行し、Stretch が有効な SQL Server テーブルにあり、リモート テーブルにない列を追加します。詳細については、「[sys.sp\_rda\_reconcile\_columns](https://msdn.microsoft.com/library/mt707765.aspx)」を参照してください。

  > [!重要] 間違ってリモート テーブルから削除した列が **sp\_rda\_reconcile\_columns** により再作成されるとき、削除した列に以前に存在したデータは復元されません。

**sp\_rda\_reconcile\_columns** を実行しても、リモート テーブルにあり、Stretch が有効な SQL Server テーブルにない列がリモート テーブルから削除されることはありません。Stretch が有効な SQL Server テーブルにはもう存在しない列がリモート Azure テーブルにあるとしても、そのような列により Stretch Database の通常動作が妨げられることはありません。余分な列は必要に応じて手動で削除できます。

## パフォーマンスとコストを管理する  

### クエリ パフォーマンスのトラブルシューティング
Stretch 対応テーブルが含まれるクエリのパフォーマンスは、テーブルの Stretch を有効にする前に比べて遅くなります。クエリのパフォーマンスが大幅に低下する場合、次の問題が考えられます。

-   ご利用の Azure サーバーと SQL Server の地理的リージョンが異なっていませんか。 Azure サーバーと SQL Server の地理的リージョンを同じに設定し、ネットワークの待ち時間を減らしてください。

-   ネットワークの状態が低下している可能性があります。ネットワーク管理者に問い合わせ、最近、問題や機能停止が発生していないか確認してください。

### Azure のパフォーマンス レベルを上げ、インデックス作成など、リソースが集中的に利用される操作に対応する
Stretch Database に構成された大規模テーブルでインデックスを作成、再作成、再編成するとき、Azure でその間、移行されたデータに対して大量のクエリが予想される場合、その操作が続く間、対応するリモート Azure データベースのパフォーマンス レベルを上げることを検討してください。パフォーマンス レベルと価格設定の詳細については、[SQL Server Stretch Database の価格](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)ページを参照してください。

### Azure で SQL Server Stretch Database サービスを一時停止することはできません  
 適切なパフォーマンス レベルと価格を選択してください。リソースを集中的に利用する操作のために一時的にパフォーマンス レベルを上げる場合、操作の完了後、前のレベルに戻します。パフォーマンス レベルと価格設定の詳細については、[SQL Server Stretch Database の価格](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)ページを参照してください。

## クエリの範囲を変更する  
 Stretch が有効なテーブルにクエリを実行すると、既定ではローカル データとリモート データの両方が返されます。すべてのユーザーによるすべてのクエリまたは管理者による 1 回だけのクエリを対象にクエリの範囲を変更できます。

### すべてのユーザーによるすべてのクエリのクエリ範囲を変更する  
 すべてのユーザーによるすべてのクエリの範囲を変更するには、ストアド プロシージャの **sys.sp\_rda\_set\_query\_mode** を実行します。ローカル データのみにクエリ範囲を制限したり、すべてのクエリを無効にしたり、初期設定を復元したりできます。詳細については、「[sys.sp\_rda\_set\_query\_mode](https://msdn.microsoft.com/library/mt703715.aspx)」を参照してください。

### <a name="queryHints"></a>管理者による 1 回だけのクエリのクエリ範囲を変更する  
 db\_owner ロールのメンバーによる 1 回だけのクエリの範囲を変更するには、SELECT ステートメントに **WITH ( REMOTE\_DATA\_ARCHIVE\_OVERRIDE = *value* )** クエリ ヒントを追加します。REMOTE\_DATA\_ARCHIVE\_OVERRIDE クエリ ヒントには、次の値が入ります。
 -   **LOCAL\_ONLY**.ローカル データだけにクエリを実行します。  

 -   **REMOTE\_ONLY**.リモート データだけにクエリを実行します。

 -   **STAGE\_ONLY**.Stretch Database が移行対象の行をステージングし、移行後、移行された行を指定の期間だけ保持するテーブルにあるデータのみにクエリを実行します。このクエリ ヒントは、ステージング テーブルにクエリを実行する唯一の方法です。

たとえば、次のクエリはローカル結果のみを返します。

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="adminHints"></a>管理上の更新と削除  
 既定では、Stretch が有効なテーブルで、移行対象の行や既に移行されている行を更新したり、削除したりすることはできません。問題を解消する必要があるとき、ステートメントに **WITH ( REMOTE\_DATA\_ARCHIVE\_OVERRIDE = *value* )** クエリ ヒントを追加することで、db\_owner ロールのメンバーは更新操作や削除操作を実行できます。REMOTE\_DATA\_ARCHIVE\_OVERRIDE クエリ ヒントには、次の値が入ります。
 -   **LOCAL\_ONLY**.ローカル データのみを更新または削除します。  

 -   **REMOTE\_ONLY**.リモート データのみを更新または削除します。

 -   **STAGE\_ONLY**.Stretch Database が移行対象の行をステージングし、移行後、移行された行を指定の期間だけ保持するテーブルにあるデータのみを更新または削除します。

## 関連項目

[Stretch Database の監視](sql-server-stretch-database-monitor.md)

[Stretch 対応のデータベースをバックアップする](sql-server-stretch-database-backup.md)

[Stretch 対応のデータベースを復元する](sql-server-stretch-database-restore.md)

<!---HONumber=AcomDC_0622_2016-->