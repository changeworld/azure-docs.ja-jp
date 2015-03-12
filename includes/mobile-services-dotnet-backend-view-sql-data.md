
このチュートリアルの最後の手順 (省略可能) は、モバイル サービスと関連付けられた SQL データベースで格納されているデータを確認することです。 

1. Azure の管理ポータルで、モバイル サービスに関連付けられたデータベースの [管理] をクリックします。
 
	![sign-in to manage SQL Database](./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png)

2. 管理ポータルで、クエリを実行して Windows ストア アプリによって加えられた変更を表示します。クエリは次のようになりますが、<code>todolist</code> の代わりにデータベースの名前を使用します。</p>

        SELECT * FROM [todolist].[todoitems]

    ![query SQL Database for stored items](./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png)

	テーブルに Id、__createdAt、__updatedAt、__version の各列があることに注意してください。これらの列はオフライン データ同期をサポートし、基本クラス [EntityData](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx) に実装されます。詳細については「[オフライン データ同期の使用]」を参照してください。<!--HONumber=42-->
