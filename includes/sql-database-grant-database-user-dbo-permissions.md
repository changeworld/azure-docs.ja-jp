## SSMS を使用して新しいデータベース ユーザーを作成する

次の手順では、SSMS を使用しており、オブジェクト エクスプローラーで SQL Database に接続していて、サーバー レベルのプリンシパル管理者として、またはユーザーにアクセス許可を付与するアクセス許可を持つユーザー アカウントで、SQL Database 論理サーバーに接続しているものとします。さらに、dbo アクセス許可を付与するユーザーがデータベースに存在するものとします。

1. オブジェクト エクスプローラーで [データベース] ノードを展開し、dbo アクセス許可を付与するユーザーのデータベースを選択します。

     ![SQL Server Management Studio: SQL Database サーバーへの接続](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)

2. 選択したデータベースを右クリックし、**[クエリ]** をクリックします。

     ![SQL Server Management Studio: SQL Database サーバーへの接続](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)

3. クエリ ウィンドウで、次の Transact-SQL ステートメントを編集して使用し、dbo アクセス許可を指定したユーザーに付与します。

    '''ALTER ROLE db\_owner ADD MEMBER user1;

     ![SQL Server Management Studio: SQL Database サーバーへの接続](./media/sql-database-grant-database-user-dbo-permissions/sql-database-grant-database-user-dbo-permissions-1.png)

<!---HONumber=AcomDC_0420_2016-->