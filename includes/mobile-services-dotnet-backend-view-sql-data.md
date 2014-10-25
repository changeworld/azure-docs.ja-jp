このチュートリアルの最後の手順 (省略可能) は、モバイル サービスと関連付けられた SQL データベースで格納されているデータを確認することです。

1.  Azure の管理ポータルで、モバイル サービスに関連付けられたデータベースの [管理] をクリックします。

    ![サインインして SQL データベースを管理する][サインインして SQL データベースを管理する]

2.  管理ポータルで、クエリを実行して Windows ストア アプリによって加えられた変更を表示します。クエリは次のようになりますが、`todolist` の代わりにデータベースの名前を使用します。</p>

        SELECT * FROM [todolist].[todoitems]

    ![SQL データベースに格納された項目をクエリする][SQL データベースに格納された項目をクエリする]

    テーブルに Id、\_\_createdAt、\_\_updatedAt、\_\_version の各列があることに注意してください。これらの列はオフライン データ同期をサポートし、基本クラス [EntityData][EntityData] に実装されます。詳細については「オフライン データ同期の使用」を参照してください。

  [サインインして SQL データベースを管理する]: ./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png
  [SQL データベースに格納された項目をクエリする]: ./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png
  [EntityData]: http://msdn.microsoft.com/ja-jp/library/microsoft.windowsazure.mobile.service.entitydata.aspx
