
このチュートリアルの最後の手順 (省略可能) は、モバイル サービスと関連付けられた SQL Database で格納されているデータを確認することです。

1. Azure 管理ポータルで、モバイル サービスに関連付けられたデータベースの [管理] をクリックします。
 
	![サインインして SQL Database を管理する](./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png)

2. 管理ポータルで、クエリを実行して Windows ストア アプリによって加えられた変更を表示します。クエリは次のようになりますが、<code>todolist</code> の代わりにデータベースの名前を使用します。</p>

        SELECT * FROM [todolist].[todoitems]

    ![SQL Database に格納された項目をクエリする](./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png)

	テーブルに Id、\_\_createdAt、\_\_updatedAt、\_\_version の各列があることに注意してください。これらの列はオフライン データ同期をサポートし、基本クラス [EntityData](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx) に実装されます。詳細については「[オフライン データの同期の使用]」を参照してください。

<!------HONumber=Oct15_HO3-->