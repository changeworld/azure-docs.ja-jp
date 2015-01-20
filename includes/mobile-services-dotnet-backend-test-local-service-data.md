このセクションでは Visual Studio を使用して IIS Express の開発ワークステーションでモバイル サービスをローカルでホストします。次に、アプリケーションとバックエンド サービスをテストします。

1.  Visual Studio で、F7 キーを押すか、**[ビルド]** メニューの **[ビルド ソリューション]** をクリックして、Windows ストア アプリとモバイル サービスの両方をビルドします。両方のプロジェクトが Visual Studio の出力ウィンドウにエラーが表示されることなくビルドされることを確認します。

2.  Visual Studio で F5 キーを押すか、**[デバッグ]** メニューの **[デバッグ開始]** をクリックしてアプリケーションを実行し、IIS Express でモバイル サービスをローカルでホストします。

3.  新しい todoitem のテキストを入力します。その後、**[保存]** をクリックします。これによって、IIS Express でローカルにホストされているモバイル サービスで作成されたデータベースに新しい todoItem が挿入されます。

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/new-local-todoitem.png)

4.  項目のうちの 1 つのチェックボックスをオンにして、完了マークを付けます。

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/local-item-checked.png)

5.  Visual Studio で、サーバー エクスプ ローラーを開き、データ接続を展開して、バックエンド サービスに作成されたデータベースの変更を表示できます。**MS\_TableConnectionString** の TodoItems テーブルを右クリックし、**[テーブル データの表示]** をクリックします。

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/vs-show-local-table-data.png)

