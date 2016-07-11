

## SSMS を使用して新しいデータベース ユーザーを作成する

SSMS を使用して既存のデータベースに新しいデータベース ユーザーを作成するには、以下の手順に従います。

これらの手順は、SSMS のオブジェクト エクスプローラーで SQL Database に接続していること、また、サーバー レベルのプリンシパル管理者として、または新規ユーザーの作成権限を持つユーザー アカウントで SQL Database 論理サーバーに接続していることを前提としています。

1. オブジェクト エクスプローラーで [データベース] ノードを展開し、新しいユーザー アカウントを作成するデータベースを選択します。

     ![SQL Server Management Studio: SQL Database サーバーへの接続](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)

2. 選択したデータベースを右クリックし、**[クエリ]** をクリックします。

     ![SQL Server Management Studio: SQL Database サーバーへの接続](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)

3. クエリ ウィンドウで、次の Transact-SQL ステートメントを編集して使用し、ユーザー データベースに含まれるユーザーを作成します。

    ```CREATE USER user1 WITH PASSWORD ='p@ssw0rd1';
    ```

     ![SQL Server Management Studio: SQL Database サーバーへの接続](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-3.png)

<!---HONumber=AcomDC_0629_2016-->