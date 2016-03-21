<properties
	pageTitle="Azure SQL Database のバックアップから 1 つのテーブルを復元する | Microsoft Azure"
	description="Azure SQL Database のバックアップから 1 つのテーブルを復元する方法を説明します。"
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="daleche"/>


# Azure SQL Database バックアップの 1 つのテーブルを復元する方法

SQL Database のデータを誤って変更してしまい、影響を受けた 1 つのテーブルを回復することが必要になる場合があります。この記事では、選択したパフォーマンス レベルに基づいて、Azure SQL Database によって自動的に実行されるバックアップからデータベースの 1 つのテーブルを復元する方法について説明します。

## 準備手順: テーブルの名前を変更し、データベースのコピーを復元する
1. Azure SQL Database で、復元したコピーで置き換えるテーブルを識別します。テーブルの名前を変更するには、Microsoft SQL Management Studio を使用します。たとえば、&lt;テーブル名&gt;\_old のような名前に変更します。

	**注意** ブロックされるのを防ぐため、名前を変更するテーブルでアクティビティが実行されていないことを確認します。問題が発生する場合は、メンテナンス期間中にこの手順を実行するようにしてください。

2. 復旧したい時点にデータベースのバックアップを復元します。手順については、「[ユーザー エラーからの Azure SQL Database の復旧](../sql-database/sql-database-user-error-recovery.md)」を参照してください。

	**注**:
	- 復元されたデータベースの名前は、"データベース名+タイムスタンプ" という形式になります (例: **Adventureworks2012\_2016-01-01T22-12Z**)。この手順では、サーバー上の既存のデータベース名は上書きされません。これは安全のためであり、ユーザーは現在のデータベースを削除して実稼働用に復元されたデータベースの名前を変更する前に、復元されたデータベースを確認できます。
	- Basic から Premium までのすべてのパフォーマンス レベルが、サービスによって自動的にバックアップされます。バックアップ リテンション期間メトリックは、レベルによって異なります。

| DB 復元 | Basic レベル | Standard レベル | Premium レベル |
| :-- | :-- | :-- | :-- |
| ポイントインタイム リストア | 7 日間以内のあらゆる復元ポイント|14 日間以内のあらゆる復元ポイント| 35 日間以内のあらゆる復元ポイント|

## SQL Database 移行ツールを使用して、復元されたデータベースからテーブルをコピーする
1. [SQL Database 移行ウィザード](https://sqlazuremw.codeplex.com)をダウンロードしてインストールします。

2. SQL Database 移行ウィザードを開き、**[プロセスの選択]** ページの **[分析/移行対象データベース]** を選択して、**[次へ]** をクリックします。![SQL Database 移行ウィザード - プロセスの選択](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)
3. **[サーバーへの接続]** ダイアログで、次の設定を適用します。
 - **[サーバー名]**: SQL Azure インスタンス
 - **[認証]**: **[SQL Server 認証]**。ログイン資格情報を入力します。
 - **[データベース]**: **[マスター DB (すべてのデータベースを一覧表示)]**。
 - **注意** 既定では、ウィザードはログイン情報を保存します。保存したくない場合は、**[ログイン情報を保存しない]** を選択します。 ![SQL Database 移行ウィザード - ソースの選択 - ステップ 1](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. **[ソースの選択]** ダイアログ ボックスの **[準備手順]** セクションで復元されたデータベース名をソースとして選択し、**[次へ]** をクリックします。

	![SQL Database 移行ウィザード - ソースの選択 - ステップ 2](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)

5. **[オブジェクトの選択]** ダイアログ ボックスで **[特定のデータベース オブジェクトの選択]** オプションを選択し、ターゲット サーバーに移行するテーブル (複数選択可) を選択します。 ![SQL Database 移行ウィザード - オブジェクトの選択](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)

6. **[スクリプト作成ウィザードの概要]** ページで、SQL スクリプト生成の準備ができたことの確認を求められたら、**[はい]** をクリックします。後で使用するために TSQL スクリプトを保存するオプションもあります。 ![SQL Database 移行ウィザード - スクリプト ウィザードの概要](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)

7. **[結果の概要]** ページで、**[次へ]** をクリックします。 ![SQL Database 移行ウィザード - 結果の概要](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)

8. **[ターゲット サーバー接続のセットアップ]** ページで、**[サーバーへの接続]** をクリックし、詳細を次のように入力します。
	- **[サーバー名]**: ターゲット サーバーのインスタンス
	- **[認証]**: **[SQL Server 認証]**。ログイン資格情報を入力します。
	- **[データベース]**: **[マスター DB (すべてのデータベースを一覧表示)]**。ターゲット サーバー上のすべてのデータベースが一覧表示されます。

	![SQL Database 移行ウィザード - ターゲット サーバー接続のセットアップ](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)

9. **[接続]** をクリックし、テーブル移動先のターゲット データベースを選択して、**[次へ]** をクリックします。以前に生成されたスクリプトの実行が終了し、ターゲット データベースに新たにコピーされたテーブルが表示されます。

## 確認手順
1. 新しくコピーしたテーブルをクエリしてテストし、データが正しいことを確認します。確認できたら、**[準備手順]** セクションで名前を変更したテーブルを削除できます (例: &lt;テーブル名&gt;\_old)。

<!---HONumber=AcomDC_0309_2016-->