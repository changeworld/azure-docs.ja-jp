<properties
    pageTitle="Azure モバイル アプリ (Xamarin Android) に対するオフライン同期の有効化"
    description="App Service モバイル アプリを使用して、Xamarin Android アプリケーションのオフライン データをキャッシュおよび同期する方法を説明します。"
    documentationCenter="xamarin"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="05/05/2016"
    ms.author="wesmc"/>

# Xamarin Android モバイル アプリのオフライン同期を有効にする

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## 概要

このチュートリアルでは、Xamarin.Android 向けのAzure Mobile Apps のオフライン同期機能について説明します。オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。変更は、ローカル データベースに格納されます。デバイスがオンラインに戻ると、これらの変更は、リモート サービスと同期されます。

このチュートリアルでは、「[Create a Xamarin Android app (Xamarin Android アプリの作成)]」チュートリアルからクライアント プロジェクトを更新し、Azure Mobile Apps のオフライン機能をサポートできるようにします。ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、データ アクセス拡張機能パッケージをプロジェクトに追加する必要があります。サーバーの拡張機能パッケージの詳細については、「[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

オフラインの同期機能の詳細については、トピック「[Azure Mobile Apps でのオフライン データ同期]」をご覧ください。

## クライアント同期コードの確認

チュートリアル「[Xamarin Android アプリの作成]」を完了した際にダウンロードした Xamarin クライアント プロジェクトには、ローカルの SQLite データベースを使用したオフライン同期をサポートするコードが既に含まれてます。チュートリアルのコードにすでに含まれているものの概要を示します。機能の概念的な概要については、「[Azure Mobile Apps でのオフライン データ同期]」をご覧ください。

* テーブル操作を実行する前に、ローカル ストアを初期化する必要があります。`ToDoActivity.OnCreate()` が `ToDoActivity.InitLocalStoreAsync()` を実行すると、ローカル ストアのデータベースが初期化されます。これにより、Azure Mobile Apps クライアント SDK で提供される `MobileServiceSQLiteStore` クラスを使用して、新しいローカルの SQLite データベースが作成されます。

	`DefineTable` メソッドを実行すると、提供された型のフィールドに一致するテーブルがローカル ストアに作成されます。この例では、`ToDoItem` になります。この型に、リモート データベース内のすべての列を含める必要はありません。列のサブセットのみ格納できます。

		// ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
				.GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
			// For more details, see http://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }


* `ToDoActivity` の `toDoTable` メンバーは、`IMobileServiceTable` ではなく、`IMobileServiceSyncTable` 型です。これは、テーブルの作成、読み取り、更新、および削除 (CRUD) といったすべての操作がローカル ストア データベースに行われるようにします。

	これらの変更がいつ Azure Mobile Apps バックエンドにプッシュされるかを決定するには、クライアント接続のための同期コンテキストを使用して `IMobileServiceSyncContext.PushAsync()` を呼び出すことで行います。同期コンテキストは、`PushAsync` が呼び出されたときに、クライアント アプリが変更を行ったすべてのテーブルで、変更を追跡およびプッシュすることで、テーブルの関係を保持するのに役立ちます。

	todoitem リストの更新、または todoitem の追加や完了があれば、提供されているコードは `ToDoActivity.SyncAsync()` を呼び出して同期します。同期コンテキストへのプッシュや同期テーブルへのプルを実行するようなローカルの変更があれば毎回同期が行われます。ただし、コンテキストによって追跡された保留中のローカル更新のあるテーブルに対してプルが実行される場合、そのプルの処理は自動的にコンテキストのプッシュを最初にトリガーします。これら (項目の更新、追加、完了) のケースでは、明示的な `PushAsync` の呼び出しを省略できます。冗長となるからです。

    提供されたコードでは、リモートの `TodoItem` テーブルのすべてのレコードはクエリされますが、クエリ ID やクエリを `PushAsync` に渡すことでレコードをフィルター処理することも可能です。詳細は、「[ Azure Mobile Apps でのオフライン データ同期]」の「*増分同期*」セクションを参照してください。

	<!-- Need updated conflict handling info : `InitializeAsync` uses the default conflict handler, which fails whenever there is a conflict. To provide a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services].
	-->


		// ToDoActivity.cs
        private async Task SyncAsync()
        {
			try {
	            await client.SyncContext.PushAsync();
	            await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
			} catch (Java.Net.MalformedURLException) {
				CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
			} catch (Exception e) {
				CreateAndShowDialog (e, "Error");
			}
        }


## クライアント アプリの実行

ローカル ストアのデータベースの設定をするために、少なくとも 1 回はクライアント アプリを実行してください。次のセクションでは、オフラインのシナリオをシミュレートし、アプリがオフラインの間にローカル ストアのデータを変更します。

## クライアント アプリの同期の動作を更新する

このセクションでは、バックエンドに無効なアプリケーション URL を使用することで、クライアント アプリを変更して、オフラインのシナリオをシミュレートします。データ項目を追加または変更すると、これらの変更はローカル ストアに保持されますが、接続が再確立されるまでは、バックエンドのデータ ストアには同期されません。

1. `ToDoActivity.cs` の上部で、次のように、`applicationURL` の初期化を変更して無効な URL を設定します。

        const string applicationURL = @"https://your-service.azurewebsites.fail/";

	アプリでも認証が使用されている場合、それが原因でサインインは失敗するので注意してください。また、デバイス上で Wi-Fi および移動体通信ネットワークを無効にしてオフライン動作をデモンストレーションすることも、機内モードを使用することもできます。

2. `ToDoActivity.SyncAsync` を更新することで、`MobileServicePushFailedException` を取得し、オフラインであるという前提が単純に無視されます。

        private async Task SyncAsync()
        {
			try {
	            await client.SyncContext.PushAsync();
	            await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
			} catch (Java.Net.MalformedURLException) {
				CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
			} catch (MobileServicePushFailedException)
            {
                // Not reporting this exception. Assuming the app is offline for now
            }
            catch (Exception e) {
				CreateAndShowDialog (e, "Error");
			}
        }


3. アプリケーションをビルドし、実行します。名前解決の例外を報告する例外ダイアログが出た場合は、閉じてください。新しい todo 項目を追加すると、`MobileServicePushFailedException` はダイアログを表示しないで処理されるため、接続がなされているようにアプリが動作することに注意してください。

4. 追加した新しい項目は、モバイルのバックエンドにプッシュできるようになるまでローカル ストアにのみ存在します。アプリケーションを終了し、再起動して、作成した新しい項目がローカル ストアに保存されていることを確認します。

5. (省略可能) Visual Studio を使用して、Azure SQL Database テーブルを表示し、バックエンドのデータベースのデータが変更されていないことを確認します。

   	Visual Studio で、**サーバー エクスプローラー**を開きます。**[Azure]**、**[SQL Databases]** の順に選択して、データベースに移動します。データベースを右クリックし、**[SQL Server オブジェクト エクスプローラーで開く]** を選択します。これで SQL データベースのテーブルとその内容を参照できます。

6. (省略可能) Fiddler や Postman などの REST ツールを使用して、モバイルのバックエンドをクエリします。その際、`https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem` の形式で、GET クエリを使用します。


## クライアント アプリを更新し、モバイルのバックエンドを再接続します。

このセクションでは、アプリケーションをモバイル バックエンドに再接続して、アプリケーションがオンライン状態に戻ったときの動作をシミュレーションします。更新ジェスチャを実行すると、データがモバイル バックエンドに同期されます。

1. `ToDoActivity.cs` を開きます。`applicationURL` を修正して、正しい URL に設定します。

2. アプリケーションを再構築して実行します。起動後、アプリは Azure モバイル アプリのバックエンドとの同期を試みます。例外ダイアログが作成されないことを確認してください。

3. (省略可能) SQL Server Object Explorer または Fiddler などの REST ツールを使用して、更新データを表示します。データが同期されるのは、Azure モバイル アプリのバックエンドのデータベースとローカル ストアの間であることに注意してください。

    データがデータベースとローカル ストアの間で同期されており、オフラインの状況で追加した項目が含まれていることを確認してください。

## その他のリソース

* [Azure Mobile Apps でのオフライン データ同期]

* [Cloud Cover: Azure Mobile Services でのオフライン同期] (注: このビデオは Mobile Services に関するものですが、オフライン同期は Azure Mobile Apps でも同様に機能します)

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Create a Xamarin Android app (Xamarin Android アプリの作成)]: ../app-service-mobile-xamarin-android-get-started.md
[Xamarin Android アプリの作成]: ../app-service-mobile-xamarin-android-get-started.md
[ Azure Mobile Apps でのオフライン データ同期]: ../app-service-mobile-offline-data-sync.md
[Azure Mobile Apps でのオフライン データ同期]: ../app-service-mobile-offline-data-sync.md

[How to use the Xamarin Component client for Azure Mobile Services]: ../partner-xamarin-mobile-services-how-to-use-client-library.md

[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio

[Cloud Cover: Azure Mobile Services でのオフライン同期]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri

<!---HONumber=AcomDC_0511_2016-->