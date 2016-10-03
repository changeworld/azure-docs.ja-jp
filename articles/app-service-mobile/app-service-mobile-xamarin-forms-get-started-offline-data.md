<properties
    pageTitle="Azure モバイル アプリ (Xamarin.Forms) に対するオフライン同期の有効化 | Microsoft Azure"
    description="App Service Mobile Apps を使用して、Xamarin.Forms アプリケーションのオフライン データをキャッシュおよび同期する方法を説明します。"
    documentationCenter="xamarin"
    authors="ggailey777"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="06/18/2016"
    ms.author="glenga"/>

# Xamarin.Forms モバイル アプリのオフライン同期の有効化

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## 概要
このチュートリアルでは、Xamarin.Forms 向けの Azure Mobile Apps のオフライン同期機能について説明します。オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリとやり取りできます。変更は、ローカル データベースに格納されます。デバイスがオンラインに戻ると、これらの変更は、リモート サービスと同期されます。

このチュートリアルは、チュートリアル「[Xamarin.iOS アプリを作成する]」を完了した際に作成される Mobile Apps 用の Xamarin.Forms クイック スタート ソリューションに基づいています。この Xamarin.Forms のクイック スタート ソリューションには、オフライン同期をサポートするためのコードが含まれています。これを有効にする必要があります。このチュートリアルでは、クイック スタート ソリューションを更新して、Azure Mobile Apps のオフライン機能をオンにします。また、アプリのオフライン固有のコードについても取り上げます。ダウンロードしたクイック スタート ソリューションを使用しない場合は、データ アクセス拡張機能パッケージをプロジェクトに追加する必要があります。サーバーの拡張機能パッケージの詳細については、「[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

オフラインの同期機能の詳細については、トピック「[Azure Mobile Apps でのオフライン データ同期]」をご覧ください。

## クイック スタート ソリューションでのオフライン同期機能の有効化

オフライン同期コードは、C# プリプロセッサ ディレクティブを使用してプロジェクトにインクルードされます。**OFFLINE\_SYNC\_ENABLED** シンボルを定義すると、これらのコード パスがビルドにインクルードされます。Windows アプリの場合、SQLite プラットフォームもインストールする必要があります。

1. Visual Studio で、ソリューション、**[ソリューションの NuGet パッケージの管理...]** の順に右クリックし、ソリューション内のすべてのプロジェクトの **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet パッケージを探してインストールします。

2. ソリューション エクスプローラーで、ポータブル クラス ライブラリ プロジェクトである **Portable** というプロジェクトから TodoItemManager.cs ファイルを開き、次のプリプロセッサ ディレクティブをコメント解除します。

		#define OFFLINE_SYNC_ENABLED

3. ソリューション エクスプローラーで **Portable** プロジェクトから TodoList.xaml.cs ファイルを開き、**OnAppearing** メソッドを見つけて、**RefreshItems** を呼び出すときに *syncItems* に `true` が渡されるようにします。これは次のようになります。

		await RefreshItems(true, syncItems: true);
	これで、アプリが起動時にバックエンドとの同期を試みるようになります。
 
4. (省略可能) iOS デバイスをサポートする場合、**iOS** プロジェクトから AppDelegate.cs ファイルを開き、**FinishedLaunching** メソッド内の次のコード行をコメント解除します。

		SQLitePCL.CurrentPlatform.Init();

	これにより、iOS デバイス上の SQLite ストアが初期化されます。残りのタスクは、Windows デバイスのサポートにのみ必要です。

5. (省略可能) Windows デバイスをサポートする場合、次の SQLite ランタイム パッケージのいずれかをインストールします。

	* **Windows 8.1 Runtime:** [SQLite for Windows 8.1](http://go.microsoft.com/fwlink/p/?LinkID=716919) をインストール。
    * **Windows Phone 8.1**: [SQLite for Windows Phone 8.1](http://go.microsoft.com/fwlink/p/?LinkID=716920) をインストール。
    * **ユニバーサル Windows プラットフォーム**: [ユニバーサル Windows プラットフォーム用 SQLite](http://sqlite.org/2016/sqlite-uwp-3120200.vsix) をインストール。クイック スタートには現在、ユニバーサル Windows プラットフォーム (UWP) プロジェクトは含まれていません。

6. (省略可能) 各 Windows アプリ プロジェクトで、**[References (参照)]**、**[参照の追加]** の順に右クリックします。**Windows** フォルダー、**[拡張機能]** の順に展開してから、**Visual C++ 2013 Runtime for Windows** SDK と共に適切な **SQLite for Windows Runtime** SDK を有効にします。各 Windows プラットフォームによって SQLite SDK の名前がわずかに異なることに注意してください。UWP プロジェクトの場合、**Visual C++ 2015 Runtime for Universal Windows Platform Apps** SDK をインストールします。


## クライアント同期コードの確認

ここでは、`#if OFFLINE_SYNC_ENABLED` ディレクティブ内のチュートリアル コードに既に含まれているものの概要を説明します。オフライン同期機能は、ポータブル クラス ライブラリ プロジェクト内の TodoItemManager.cs プロジェクト ファイル内にあることに注意してください。機能の概念的な概要については、「[Azure Mobile Apps でのオフライン データ同期]」をご覧ください。

* テーブル操作を実行する前に、ローカル ストアを初期化する必要があります。ローカル ストア データベースは、次のコードを使用して **TodoItemManager** クラス コンストラクターで初期化されます。

	    var store = new MobileServiceSQLiteStore("localstore.db");
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

	これにより、**MobileServiceSQLiteStore** クラスを使用して新しいローカルの SQLite データベースが作成されます。**DefineTable** メソッドを実行すると、提供された型のフィールドに一致するテーブルがローカル ストアに作成されます。この例では、`ToDoItem` になります。この型に、リモート データベース内のすべての列を含める必要はありません。列のサブセットのみ格納できます。

* **TodoItemManager** の **todoTable** フィールドは、**IMobileServiceTable** 型ではなく **IMobileServiceSyncTable** 型です。このクラスでは、作成、読み取り、更新、削除 (CRUD) のすべてのテーブル操作にローカル データベースを使用します。これらの変更がいつモバイル アプリ バックエンドにプッシュされるかを決定するには、クライアントによって使用される **IMobileServiceSyncContext** で **PushAsync** を呼び出します。この同期コンテキストは、**PushAsync** が呼び出されたときに、クライアント アプリが変更を行ったすべてのテーブルで、変更を追跡およびプッシュすることで、テーブルの関係を保持するのに役立ちます。

	次の **SyncAsync** メソッドは、モバイル アプリ バックエンドと同期するために呼び出されます。

		public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling. 
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
						error.TableName, error.Item["id"]);
                }
            }
        }

	このサンプルでは、既定の同期ハンドラーを使った単純なエラー処理を使用しています。実際のアプリケーションでは、ネットワーク状態、サーバーの競合などの各種エラーが、カスタム **IMobileServiceSyncHandler** の実装を使って処理されます。

##オフライン同期に関する考慮事項

このサンプルでは、**SyncAsync** メソッドは起動時と同期が明示的に要求された場合にのみ呼び出されます。Android アプリまたは iOS アプリで同期を開始するには、項目一覧でプルダウンします。Windows アプリでは、**[同期]** ボタンを使用します。実際のアプリケーションでは、ネットワークの状態が変更されたときにこの同期機能がトリガーされるように設定することもできます。

コンテキストによって追跡された保留中のローカル更新のあるテーブルに対してプルが実行される場合、そのプル操作は前のコンテキストのプッシュを自動的にトリガーします。このサンプルの項目を更新、追加、完了するとき、明示的な **PushAsync** の呼び出しは省略できます。冗長であるためです。

提供されたコードでは、リモートの TodoItem テーブルのすべてのレコードが照会されますが、クエリ ID とクエリを **PushAsync** に渡すことでレコードをフィルター処理することも可能です。詳細は、「[ Azure Mobile Apps でのオフライン データ同期]」の「*増分同期*」セクションを参照してください。


## クライアント アプリの実行

オフライン同期が有効になったので、ローカル ストア データベースに入力するために、各プラットフォームで少なくとも 1 回クライアント アプリケーションを実行できます。後で、オフラインのシナリオをシミュレートし、アプリがオフラインの間にローカル ストアのデータを変更します。

## クライアント アプリの同期の動作を更新する

このセクションでは、バックエンドに無効なアプリケーション URL を使用することで、クライアント プロジェクトを変更して、オフラインのシナリオをシミュレートします。データ項目を追加または変更すると、これらの変更はローカル ストアに保持されますが、接続が再確立されるまでは、バックエンドのデータ ストアには同期されません。

2. ソリューション エクスプローラーで、**Portable** プロジェクトから Constants.cs プロジェクト ファイルを開き、`ApplicationURL` の値を次のような無効な URL を指すように変更します。

        publis static string ApplicationURL = @"https://your-service.azurewebsites.xxx/";


2. **Portable** プロジェクトから TodoItemManager.cs ファイルを開き、ベース **Exception** クラスの **catch** を、**SyncAsync** 内の **try...catch** ブロックに追加します。この **catch** ブロックによってコンソールに例外メッセージが書き込まれます。これは次のようになります。

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }

3. クライアント アプリをビルドして実行し、いくつか新しい項目を追加します。バックエンドとの同期を試みるたびに、コンソールに例外がログ記録されることに注意してください。これらの新しい項目は、モバイル バックエンドにプッシュされるまでは、ローカル ストア内にのみ存在します。クライアント アプリケーションは、バックエンドに接続されているかのように動作し、作成、読み取り、更新、削除 (CRUD) 操作のすべてをサポートします。

4. アプリケーションを終了し、再起動して、作成した新しい項目がローカル ストアに保存されていることを確認します。

5. (省略可能) Visual Studio を使用して、Azure SQL Database テーブルを表示し、バックエンドのデータベースのデータが変更されていないことを確認します。

	Visual Studio で、**サーバー エクスプローラー**を開きます。**[Azure]**、**[SQL Databases]** の順に選択して、データベースに移動します。データベースを右クリックし、**[SQL Server オブジェクト エクスプローラーで開く]** を選択します。これで SQL データベースのテーブルとその内容を参照できます。


## クライアント アプリを更新し、モバイルのバックエンドを再接続します。

このセクションでは、アプリケーションをモバイル バックエンドに再接続して、アプリケーションがオンライン状態に戻ったときの動作をシミュレーションします。更新ジェスチャを実行すると、データがモバイル バックエンドに同期されます。

1. Constants.cs をもう一度開き、`applicationURL` を修正して、正しい URL に設定します。

2. クライアント アプリの再構築と実行起動後、アプリはモバイル アプリ バックエンドとの同期を試みます。デバッグ コンソールに例外がログ記録されていないことを確認してください。

3. (省略可能) SQL Server Object Explorer または Fiddler などの REST ツールを使用して、更新データを表示します。バックエンド データベースとローカル ストアの間でデータが同期されていることを確認します。

    データがデータベースとローカル ストアの間で同期されており、アプリが接続されていない状況で追加した項目が含まれていることを確認してください。

## その他のリソース

* [Azure Mobile Apps でのオフライン データ同期]

* [Cloud Cover: Azure Mobile Services でのオフライン同期] \(注: このビデオは Mobile Services に関するものですが、オフライン同期は Azure Mobile Apps でも同様に機能します)

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Xamarin.iOS アプリを作成する]: app-service-mobile-xamarin-ios-get-started.md
[ Azure Mobile Apps でのオフライン データ同期]: app-service-mobile-offline-data-sync.md
[Azure Mobile Apps でのオフライン データ同期]: app-service-mobile-offline-data-sync.md
[How to use the Xamarin Component client for Azure Mobile Services]: partner-xamarin-mobile-services-how-to-use-client-library.md
[Cloud Cover: Azure Mobile Services でのオフライン同期]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri

<!---HONumber=AcomDC_0629_2016-->