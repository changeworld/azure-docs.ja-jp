<properties
	pageTitle="Azure Mobile App でのオフライン データの使用 (Windows ストア) | モバイル デベロッパー センター"
	description="Azure Mobile App を使用して、Windows ストア アプリケーションのオフライン データをキャッシュして同期する方法を説明します。"
	documentationCenter="windows"
	authors="christopheranderson"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/22/2015"
	ms.author="chrande"/>

# Azure Mobile App でのオフライン データ同期の使用

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../includes/app-service-mobile-selector-offline-preview.md)]


このチュートリアルでは、Azure Mobile App バックエンドを使用して、Windows ユニバーサル ストアのアプリケーションにオフライン サポートを追加する方法について説明します。オフライン サポートにより、使用するアプリケーションがオフラインの状況でローカル データベースとやり取りすることができるようになります。アプリケーションがバックエンドのデータベースでオンラインになると、オフライン機能を使用してローカルの変更を同期します。



このチュートリアルでは、「[Create a Windows app (Windows アプリをの作成)]」チュートリアルからのユニバーサル アプリケーション プロジェクトを更新し、Azure Mobile App のオフライン機能をサポートできるようにします。その後、切断されたオフラインの状況でデータを追加し、それらの項目をオンライン データベースに同期してから、Azure 管理ポータルにログインして、アプリケーションを実行したときにデータに加えた変更を表示します。

このチュートリアルでは、次の基本的な手順について説明します。

1. [オフライン機能をサポートするようにアプリケーションを更新する]
2. [アプリケーションの同期の動作を更新する] 
3. [モバイル アプリ バックエンドに再接続するようにアプリケーションを更新する]

このチュートリアルには、次のものが必要です。

* Windows 8.1 で実行されている Visual Studio 2013。
* 「[Create a Windows app (Windows アプリの作成)]」の完了。
* [Azure Mobile Services SDK version 2.0.0 (またはそれ以降)][Azure Mobile App SDK Nuget]
* [Azure Mobile Services SQLite ストア Version 1.0.2 (またはそれ以降)][SQLite ストア NuGet]
* [SQLite for Windows 8.1](www.sqlite.org/downloads)

>[AZURE.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成できます。詳細については、<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure の無料評価版サイト</a>をご覧ください

## <a name="enable-offline-app"></a>オフライン機能をサポートするようにアプリケーションを更新する

Azure Mobile App のオフライン機能を使用すると、Mobile Services に対してオフラインになっている状況でも、ローカル データベースとやり取りすることができます。アプリケーションでこれらの機能を使用するには、 `MobileServiceClient.SyncContext` をローカル ストアに初期化します。その後、 `IMobileServiceSyncTable` インターフェイスを使用してテーブルを参照します。このチュートリアルでは、ローカル ストアに SQLite を使用します。

1. Windows 8.1 および Windows Phone 8.1 の SQLite ランタイムをインストールします。

    * **Windows 8.1 Runtime:** [SQLite for Windows 8.1] をインストール。
    * **Windows Phone 8.1:** [SQLite for Windows Phone 8.1] をインストール。

    >[AZURE.NOTE] Internet Explorer を使用している場合は、SQLite をインストールするためにこのリンクをクリックすると、.vsix を .zip ファイルとしてダウンロードするためのプロンプトが表示されることがあります。ファイルに .zip ではなく .vsix 拡張子を付けて、ハード ドライブ上の場所に保存します。エクスプローラーで .vsix ファイルをダブルクリックすると、インストールが実行されます。

2. Visual Studio で、「[Create a Windows app (Windows アプリの作成)]」チュートリアルで完了したプロジェクトを開きます。Windows 8.1 ランタイムおよび Windows Phone 8.1 プロジェクト向けの **WindowsAzure.MobileServices.SQLiteStore** NuGet パッケージをインストールします。

    * **Windows 8.1:** ソリューション エクスプローラーで Windows 8.1 プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックして NuGet パッケージ マネージャーを実行します。**SQLiteStore** を見つけて `WindowsAzure.MobileServices.SQLiteStore` パッケージをインストールします。
    * **Windows Phone 8.1:** Windows Phone 8.1 プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックして NuGet パッケージ マネージャーを実行します。**SQLiteStore** を見つけて `WindowsAzure.MobileServices.SQLiteStore` パッケージをインストールします。

    >[AZURE.NOTE] インストールで古いバージョンの SQLite への参照が作成される場合は、重複した参照だけを削除できます。

3. ソリューション エクスプローラーで Windows 8.1 ランタイムおよび Windows Phone 8.1 プラットフォーム プロジェクトの **[参照設定]** を右クリックし、**[拡張]** セクションにある SQLite への参照を追加します。

    ![][1]
    </br>

    **Windows 8.1 ランタイム**

    ![][11]
    </br>

    **Windows Phone 8.1**

4. SQLite ランタイムでは、ビルド対象のプロジェクトのプロセッサ アーキテクチャを **[x86]**、**[x64]**、または **[ARM]** に変更する必要があります。**[任意の CPU]** はサポートされません。ソリューション エクスプ ローラーで、上部にある [ソリューション] をクリックし、プロセッサ アーキテクチャのドロップ ダウン ボックスをテストしたいサポート済みの設定の 1 つに変更します。

    ![][13]

5. ソリューション エクスプローラーで共有プロジェクトを使用し、MainPage.cs ファイルを開きます。ファイルの先頭にある次の using ステートメントをコメント解除します。

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. MainPage.cs で、 `todoTable` の定義をコメント アウトし、次の `MobileServicesClient.GetSyncTable()` を呼び出す行をコメント解除します。

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. MainPage.cs 内の `Offline sync` とマークされている部分で、 `InitLocalStoreAsync` および `SyncAsync` メソッドをコメント解除します。 `InitLocalStoreAsync` メソッドは、SQLite ストアを使用してクライアントの同期コンテキストを初期化します。

        private async Task InitLocalStoreAsync()
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localstore.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }

            await SyncAsync();
        }

        private async Task SyncAsync()
        {
            await App.MobileService.SyncContext.PushAsync();
            await todoTable.PullAsync("todoItems", todoTable.CreateQuery());
        }

8.  `OnNavigatedTo` イベント ハンドラーで、 `InitLocalStoreAsync` の呼び出しをコメント解除します。

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await InitLocalStoreAsync(); // offline sync
            await RefreshTodoItems();
        }

9.  `InsertTodoItem`、 `UpdateCheckedTodoItem`、および `ButtonRefresh_Click` にある 3 つの `SyncAsync` の呼び出しをコメント解除します。

        private async Task InsertTodoItem(TodoItem todoItem)
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);

            await SyncAsync(); // offline sync
        }

        private async Task UpdateCheckedTodoItem(TodoItem item)
        {
            await todoTable.UpdateAsync(item);
            items.Remove(item);
            ListItems.Focus(Windows.UI.Xaml.FocusState.Unfocused);

            await SyncAsync(); // offline sync
        }

        private async void ButtonRefresh_Click(object sender, RoutedEventArgs e)
        {
            ButtonRefresh.IsEnabled = false;

            await SyncAsync(); // offline sync
            await RefreshTodoItems();

            ButtonRefresh.IsEnabled = true;
        }

10.  `SyncAsync` メソッドに例外ハンドラーを追加します。

        private async Task SyncAsync()
        {
            String errorString = null;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
            }

            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " +
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Pull failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Pull again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }
        }

    この例では、リモート `todoTable` ですべてのレコードを取得しますが、クエリを渡すことによりレコードをフィルタリングすることも可能です。 `PullAsync` の最初のパラメーターは増分同期に使用されるクエリ ID ですが、この同期では、 `UpdatedAt` タイムスタンプを使用して最後の同期から変更があったレコードのみを取得します。クエリ ID は、アプリ内の各論理クエリに対して一意の、わかりやすい文字列にする必要があります。増分同期を解除するには、 `null` をクエリ ID として渡します。これによってプル操作ごとにすべてのレコードを取得することになり、効率が悪くなる可能性があります。

     `MobileServicePushFailedException` はプッシュ操作とプル操作の両方で発生する場合があることに注意してください。リレーションシップを持つすべてのテーブルに一貫性があることを確認するために、プル操作で内部的にプッシュが実行されることから、この例外はプルで発生する可能性があります。

11. Visual Studio で、**F5** キーを押してアプリケーションをリビルドして実行します。アプリケーションは、前にオフライン同期の変更を実施したときと同様に動作しますが、これは挿入や更新の操作時にアプリケーションが同期の動作をするためです。

## <a name="update-sync"></a>アプリケーションの同期の動作を更新する

このセクションでは、挿入や更新の操作時ではなく、**[Refresh]** ボタンが押された場合にのみ同期を実行するようアプリケーションを変更します。その後、オフラインのシナリオをシミュレートするために、モバイル アプリ バックエンドに対するアプリケーションの接続を解除します。データ項目を追加すると、それらはローカル ストア内に保持されますが、モバイル アプリ バックエンドには同期されません。

1. 共有プロジェクトで App.xaml.cs を開きます。 `InsertTodoItem` および `UpdateCheckedTodoItem` メソッドを編集し、 `SyncAsync` の呼び出しをコメント アウトします。

2. 共有プロジェクトで App.xaml.cs を編集します。**MobileServiceClient** の初期化をコメント アウトし、無効なモバイル アプリ URL を使用する次の行を追加します。

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://<your-mobile-service>-code.azurewebsites.fail",
            "https://<your-mobile-service>gateway.azurewebsites.fail",
            "AppKey"
        );

3.  `InitLocalStoreAsync()` で、 `SyncAsync()` の呼び出しをコメント アウトします。これで、アプリケーションは起動時に同期を実行しません。

4. **F5** キーを押し、アプリケーションをビルドして実行します。新しい todo 項目を入力し、それぞれで **[保存]** をクリックします。新しい todo 項目は、モバイル アプリ バックエンドにプッシュされるまでは、ローカル ストア内にのみ存在します。クライアント アプリケーションは、モバイル アプリ バックエンドに接続されているかのように動作し、作成、読み取り、更新、削除 (CRUD) 操作のすべてをサポートします。

5. アプリケーションを終了し、再起動して、作成した新しい項目がローカル ストアに保存されていることを確認します。

## <a name="update-online-app"></a>モバイル アプリ バックエンドに再接続するようにアプリケーションを更新する

ここでは、アプリケーションをモバイル アプリ バックエンドに再接続します。これは、アプリケーションがオフライン状態から、モバイル アプリ バックエンドとのオンライン状態に移行したことをシミュレートします。[Refresh] をクリックすると、データがモバイル アプリ バックエンドに同期されます。

1. 共有プロジェクトで App.xaml.cs を開きます。先の `MobileServiceClient` の初期化をコメント解除し、正しいモバイル アプリ サービス バックエンドの URL、ゲートウェイの URL、アプリケーション キーを追加し直します。

2. **F5** キーを押して、アプリケーションをリビルドして実行します。アプリケーションは現在モバイル アプリ バックエンドに接続されていますが、オフラインの状況と同じ表示になっていることに注意してください。この原因は、このアプリケーションが常に、ローカル ストアを指している `IMobileServiceSyncTable` との組み合わせで動作していることにあります。

3. Azure ポータルにログインし、モバイル アプリ バックエンドに対応するデータベースを参照します。

    Visual Studio で、**[サーバー エクスプローラー]**、**[Azure]**、**[SQL データベース]** の順に移動します。データベースを右クリックし、**[SQL Server オブジェクト エクスプローラーで開く]** を選択します。

    データベースとローカル ストアの間でデータがまだ同期されていないことを確認します。

4. アプリケーションで、**[Refresh]** を押します。これにより、アプリケーションは `PushAsync` および `PullAsync` を呼び出します。このプッシュ操作により、ローカル ストアの項目が Mobile Services に送信され、Mobile Services から新しいデータが取得されます。

    プッシュ操作は `IMobileServicesSyncTable` の代わりに `MobileServiceClient.SyncContext` を実行し、同期コンテキストに関連付けられているすべてのテーブルに対して変更をプッシュします。これは、テーブル間にリレーションシップがある状況に対応することを目的としています。

5. アプリケーションで、ローカル ストアで完了させる項目の横にあるチェック ボックスをクリックします。

6. **[Refresh]** をもう一度クリックすると、 `SyncAsync` が呼び出されます。 `SyncAsync` はプッシュとプルの両方を呼び出しますが、この場合は `PushAsync` の呼び出しを削除することもできました。これは、**プルは必ず、最初にプッシュを実行する**ことが原因です。これは、ローカル ストアのすべてのテーブルとリレーションシップの一貫性を確実に保つためです。


## まとめ

Mobile Services のオフライン機能をサポートするために、 `IMobileServiceSyncTable` インターフェイスを使用して、ローカル ストアにより  `MobileServiceClient.SyncContext` を初期化しました。この場合は、ローカル ストアは、SQLite データベースでした。

Mobile Services に対する通常の CRUD 操作は、まるでアプリケーションが接続されているかのように機能しますが、すべての操作はローカル ストアに対して実施されます。

ローカル ストアをサーバーと同期しようとする場合は、 `IMobileServiceSyncTable.PullAsync` と  `MobileServiceClient.SyncContext.PushAsync` の各メソッドを使用しました。

*  変更内容をサーバーにプッシュするために、 `IMobileServiceSyncContext.PushAsync()` を呼び出しました。このメソッドは、すべてのテーブルに対して変更をプッシュするため、同期テーブルではなく  `IMobileServicesSyncContext` のメンバーです。

    何らかの方法で (CUD 操作により) ローカルで変更されたレコードだけが、サーバー宛てに送信されます。

* サーバー上のテーブルからアプリケーションにデータをプルするために、 `IMobileServiceSyncTable.PullAsync` を呼び出しました。

    プルは必ず、最初にプッシュを実行します。これは、ローカル ストアのすべてのテーブルとリレーションシップの一貫性を確実に保つためです。

    クライアントに格納されているデータをフィルターするためのクエリを指定できる  `PullAsync()` のオーバーロードもあります。クエリが渡されない場合、 `PullAsync()` は、対応するテーブル (またはクエリ) 内のすべての行をプルします。アプリケーションと同期する必要がある変更のみをフィルター処理するクエリを渡すことができます。

* 増分同期を有効にするには、クエリ ID を  `PullAsync()` に渡します。クエリ ID は、最後のプル操作の結果の最後に更新されたタイムスタンプを格納するのに使用されます。クエリ ID は、アプリ内の各論理クエリに対して一意の、わかりやすい文字列にする必要があります。クエリにパラメーターがある場合は、同じパラメーター値をクエリ ID の一部に含める必要があります。

    たとえば、userid でフィルターする場合は、次のようにクエリ ID に含めます。

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    増分同期を無効にする場合は、 `null` をクエリ ID として渡します。この場合、 `PullAsync` への呼び出しごとにすべてのレコードが再取得され、場合によっては非効率となります。

* アプリで定期的に  `IMobileServiceSyncTable.PurgeAsync()` を呼び出して、ローカル ストアを削除する必要があります。


<!-- Anchors. -->
[オフライン機能をサポートするようにアプリケーションを更新する]: #enable-offline-app
[アプリケーションの同期の動作を更新する]: #update-sync
[Mobile App に再接続するようにアプリケーションを更新する]: #update-online-app
[モバイル アプリ バックエンドに再接続するようにアプリケーションを更新する]: #update-online-app
[次のステップ]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/cpu-architecture.png


<!-- URLs. -->
[TodoList オフライン サンプル]: http://go.microsoft.com/fwlink/?LinkId=394777
[Create a Windows app (Windows アプリの作成)]: /documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953

[Create a Windows app (Windows アプリをの作成)]: /documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/
[Azure Mobile App SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/2.0.0-beta

[SQLite ストア NuGet]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.2

<!--HONumber=52-->