<properties
	pageTitle="Mobile Apps でユニバーサル Windows プラットフォーム (UWP) アプリのオフライン同期を有効にする | Azure App Service"
	description="Azure Mobile App を使用して、ユニバーサル Windows プラットフォーム (UWP) アプリでオフライン データをキャッシュおよび同期する方法について説明します。"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="wesmc"/>

# Windows アプリのオフライン同期を有効にする

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Overview

このチュートリアルでは、Azure Mobile App バックエンドを使用して、ユニバーサル Windows プラットフォーム (UWP) アプリにオフライン サポートを追加する方法について説明します。オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリとやり取りできます。変更はローカル データベースに格納され、デバイスが再びオンラインになると、これらの変更がリモート バックエンドと同期されます。

このチュートリアルでは、「[Create a Windows app (Windows アプリの作成)]」チュートリアルからの UWP アプリ プロジェクトを更新し、Azure Mobile Apps のオフライン機能をサポートできるようにします。ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、データ アクセス拡張機能パッケージをプロジェクトに追加する必要があります。サーバーの拡張機能パッケージの詳細については、「[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

オフラインの同期機能の詳細については、トピック「[Azure Mobile Apps でのオフライン データ同期]」をご覧ください。

## 必要条件

このチュートリアルには、次のものが必要です。

* Windows 8.1 で実行されている Visual Studio 2013。
* 「[Create a Windows app (Windows アプリの作成)][create a windows app]」の完了。
* [Azure Mobile Services SQLite Store][sqlite store nuget]
* [ユニバーサル Windows プラットフォーム開発用 SQLite](http://www.sqlite.org/downloads)

## オフライン機能をサポートするようにクライアント アプリを更新する

Azure モバイル アプリのオフライン機能を使用すると、オフラインになっている状況でも、ローカル データベースとやり取りすることができます。アプリケーションでこれらの機能を使用するには、[SyncContext][synccontext] をローカル ストアに初期化します。その後、[IMobileServiceSyncTable][IMobileServiceSyncTable] インターフェイスを使用してテーブルを参照します。SQLite は、デバイス上のローカル ストアとして使用されます。

1. [ユニバーサル Windows プラットフォーム用の SQLite ランタイム](http://sqlite.org/2016/sqlite-uwp-3120200.vsix)をインストールします。

2. 「[Create a Windows app (Windows アプリの作成)]」チュートリアルで作成した UWP アプリ プロジェクトの NuGet パッケージ マネージャーを Visual Studio で開き、**Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet パッケージを検索してインストールします。

4. ソリューション エクスプローラーで、**[参照]** を右クリックし、**[参照の追加...]**、**[ユニバーサル Windows]**、**[拡張機能]** の順にクリックして、**[SQLite for Universal Windows Platform]** と **[Visual C++ 2015 Runtime for Universal Windows Platform apps]** の両方を有効にします。

    ![SQLite UWP リファレンスを追加する][1]

5. MainPage.xaml.cs ファイルを開き、ファイルの先頭にある次の `using` ステートメントをコメント解除します。

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  
        using Microsoft.WindowsAzure.MobileServices.Sync;         

6. **IMobileServiceTable** として `todoTable` を初期化するコード行をコメント化し、**IMobileServiceSyncTable** として `todoTable` を初期化するコード行をコメント解除します。

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); 

7. MainPage.xaml.cs の `Offline sync` 領域で、次のメソッドをコメント解除します。

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

	`SyncAsync` では、プッシュ操作が [SyncContext][synccontext] から開始された後、増分同期が行われます。同期コンテキストは、クライアントがすべてのテーブルに対して行った変更を追跡します。この動作については、「[Azure Mobile Apps でのオフライン データ同期]」を参照してください。

8. `OnNavigatedTo` イベント ハンドラーで、`InitLocalStoreAsync` の呼び出しをコメント解除します。[認証チュートリアル](app-service-mobile-windows-store-dotnet-get-started-users.md)を既に完了している場合、代わりに `AuthenticateAsync` メソッドでこれを行う必要があります。

9. `InsertTodoItem` および `UpdateCheckedTodoItem` メソッドでの [PushAsync] の呼び出しをコメント解除した後、`ButtonRefresh_Click` メソッドで `SyncAsync` の呼び出しをコメント解除します。

10. `SyncAsync` メソッドに次の例外ハンドラーを追加します。

        private async Task SyncAsync()
        {
            String errorString = null;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); 
            }

            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors. You may be offine.\nMessage: " +
                  ex.Message + "\nPushResult.Status: " + ex.PushResult.Status.ToString();
            }
            catch (Exception ex)
            {
                errorString = "Pull failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your pull again when connected with your backend.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }
        }

	オフライン状況では、[PullAsync][PullAsync] は [PushResult.Status][Status] が [CancelledByNetworkError][CancelledByNetworkError] の [MobileServicePushFailedException][MobileServicePushFailedException] になる可能性があります。この例外は、暗黙のプッシュがプルの前に保留中の更新をプッシュしようとして失敗したときに発生します。詳細については、「[Azure Mobile Apps でのオフライン データ同期]」を参照してください。

11. Visual Studio で、**F5** キーを押してクライアント アプリをリビルドして実行します。アプリは、オフライン同期を有効にする前と同じように動作します。ただし今度は、オフライン シナリオで使用できるデータがローカル データベースに格納されます。次に、オフライン シナリオを作成し、ローカルに保存されているデータを使用してアプリを開始します。

## <a name="update-sync"></a>アプリを更新してバックエンドから切断する

ここでは、オフライン状況をシミュレートするために、モバイル アプリ バックエンドへの接続を解除します。データ項目を追加すると、例外ハンドラーによって、アプリがオフライン モードであることが通知されます。この状態では、新しい項目はローカル ストアに追加され、プッシュが次に接続状態で実行したときに、モバイル アプリ バックエンドに同期されます。

1. 共有プロジェクトで App.xaml.cs を編集します。**MobileServiceClient** の初期化をコメント アウトし、無効なモバイル アプリ URL を使用する次の行を追加します。

         public static MobileServiceClient MobileService =
				new MobileServiceClient("https://your-service.azurewebsites.fail");

	アプリでも認証が使用されている場合、それが原因でサインインは失敗するので注意してください。また、デバイス上で Wi-Fi および移動体通信ネットワークを無効にしてオフライン動作をデモンストレーションすることも、機内モードを使用することもできます。

2. **F5** キーを押し、アプリケーションをビルドして実行します。アプリを起動した際の更新時には同期が失敗することに注意してください。
 
3. 新しい項目を入力し、**[保存]** をクリックするたびに [CancelledByNetworkError] ステータスでプッシュが失敗することを確認します。ただし、新しい todo 項目は、モバイル アプリ バックエンドにプッシュされるまでは、ローカル ストア内に存在します。

	運用アプリでは、これらの例外を抑制した場合、クライアント アプリはモバイル アプリ バックエンドにまだ接続されているかのように動作します。

4. アプリケーションを終了し、再起動して、作成した新しい項目がローカル ストアに保存されていることを確認します。

5. (省略可能) Visual Studio で、**サーバー エクスプローラー**を開きます。**[Azure]**、**[SQL Databases]** の順に選択して、データベースに移動します。データベースを右クリックし、**[SQL Server オブジェクト エクスプローラーで開く]** を選択します。これで SQL データベースのテーブルとその内容を参照できます。バックエンド データベース内のデータが変更されていないことを確認します。

6. (省略可能) Fiddler や Postman などの REST ツールを使用して、モバイルのバックエンドをクエリします。その際、`https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem` の形式で、GET クエリを使用します。

## <a name="update-online-app"></a>モバイル アプリ バックエンドに再接続するようにアプリケーションを更新する

ここでは、アプリケーションをモバイル アプリ バックエンドに再接続します。これは、アプリケーションがオフライン状態から、モバイル アプリ バックエンドとのオンライン状態に移行したことをシミュレートします。初めてアプリケーションを実行すると、`OnNavigatedTo` イベント ハンドラーが `InitLocalStoreAsync` を呼び出します。これが次に `SyncAsync` を呼び出し、ローカル ストアとバックエンドのデータベースを同期します。そのため、アプリはスタートアップ時に同期を試みることになります。

1. 共有プロジェクトで App.xaml.cs を開き、正しいモバイル アプリ URL を使用するために以前の `MobileServiceClient` の初期化をコメント解除します。

2. **F5** キーを押して、アプリケーションをリビルドして実行します。アプリは、プッシュとプルの操作によって、`OnNavigatedTo` イベント ハンドラーが実行されるとすぐに、ローカルでの変更と Azure Mobile Apps バックエンドを同期します。

3. (省略可能) SQL Server Object Explorer または Fiddler などの REST ツールを使用して、更新データを表示します。データが同期されるのは、Azure モバイル アプリのバックエンドのデータベースとローカル ストアの間であることに注意してください。

4. アプリケーションで、ローカル ストアで完了させる項目の横にあるチェック ボックスをクリックします。

  `UpdateCheckedTodoItem` は `SyncAsync` を呼び出し、完了した各項目をモバイル アプリ バックエンドと同期します。`SyncAsync` はプッシュとプルの両方を呼び出します。ただし、**クライアントが変更を行ったテーブルに対してプルを実行すると、クライアントの同期コンテキストへのプッシュが常に最初に自動的に実行される**ことに注意してください。これは、ローカル ストアのすべてのテーブルとリレーションシップの一貫性を確実に保つためです。そのため、ここでは `PushAsync` への呼び出しを削除できます。呼び出しはプルを実行すれば自動で行われるためです。この動作は、認識をしていない場合、予期せぬプッシュを引き起こすことがあります。この動作については、「[Azure Mobile Apps でのオフライン データ同期]」を参照してください。


##API の概要

モバイル サービスのオフライン機能をサポートするために、[IMobileServiceSyncTable] インターフェイスを使用して、ローカル SQLite データベースで [MobileServiceClient.SyncContext][synccontext] を初期化しました。オフラインのときは、モバイル アプリに対する通常の CRUD 操作は、アプリケーションはまだ接続されているが、操作はローカル ストアに対して発生したかのように動作します。ローカル ストアをサーバーと同期するには、次のメソッドを使用します。

*  **[PushAsync]**  
   このメソッドは [IMobileServicesSyncContext] のメンバーなので、すべてのテーブルに対する変更はバックエンドにプッシュされます。ローカルに変更されたレコードのみが、サーバーに送信されます。

* **[PullAsync]**   
   プルは [IMobileServiceSyncTable] から開始されます。テーブルに追跡されている変更がある場合は、ローカル ストア内のすべてのテーブルとリレーションシップの一貫性が保持されるように、暗黙のプッシュが実行されます。*pushOtherTables* パラメーターは、コンテキスト内の他のテーブルが暗黙のプッシュでプッシュされるかどうかを制御します。*query* パラメーターは、[IMobileServiceTableQuery&lt;U&gt;][IMobileServiceTableQuery] または OData クエリ文字列を受け取り、返されたデータをフィルター処理します。*queryId* パラメーターは、増分同期の定義に使用されます。詳細については、「[Azure Mobile Apps でのオフライン データ同期](app-service-mobile-offline-data-sync.md#how-sync-works)」を参照してください。

* **[PurgeAsync]**  
   アプリはこのメソッドを定期的に呼び出して、ローカル ストアから古いデータを消去する必要があります。まだ同期されていないすべての変更を消去する必要がある場合は、*force* パラメーターを使用します。

これらの概念の詳細については、「[Azure Mobile Apps でのオフライン データ同期](app-service-mobile-offline-data-sync.md#how-sync-works)」を参照してください。

## 詳細情報

Mobile Apps のオフライン同期機能の詳しい背景情報については、以下のトピックを参照してください。

* [Azure Mobile Apps でのオフライン データ同期]
* [Cloud Cover: Azure Mobile Services でのオフライン同期] \(注: このビデオは Mobile Services に関するものですが、オフライン同期は Azure Mobile Apps でも同様に機能します)
* [Azure Friday: Azure Mobile Services のオフライン対応アプリ]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Azure Mobile Apps でのオフライン データ同期]: app-service-mobile-offline-data-sync.md
[create a windows app]: app-service-mobile-windows-store-dotnet-get-started.md
[Create a Windows app (Windows アプリの作成)]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[Cloud Cover: Azure Mobile Services でのオフライン同期]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Azure Mobile Services のオフライン対応アプリ]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0824_2016-->