<properties
    pageTitle="モバイル アプリ (Xamarin iOS) のオフライン同期を有効にする"
    description="App Service Mobile App を使用して、Xamarin iOS アプリケーションのオフライン データをキャッシュおよび同期する方法を説明します。"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="03/26/2015"
    ms.author="donnam"/>

# Xamarin iOS モバイル アプリケーションのオフライン同期を有効にする

<!-- [AZURE.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]
 -->

このチュートリアルでは、iOS 向けのモバイル アプリのオフライン同期機能について説明します。オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。変更は、ローカル データベースに格納されます。デバイスがオンラインに戻ると、これらの変更は、リモート サービスと同期されます。

オフライン同期には、いくつかの潜在的な用途があります。

* サーバー データをデバイスにローカルでキャッシュすることにより、アプリケーションの応答性を向上させる。
* 断続的なネットワーク接続に対してアプリケーションに弾力性を持たせる。
* エンド ユーザーがネットワークにアクセスできなくてもデータを作成および変更できるようにすることで、接続がほとんどまたはまったく得られないような状況をサポートする。
* 複数のデバイス間でデータを同期させ、同じレコードが 2 つのデバイスによって変更されたときに競合を検出する。

モバイル アプリケーションを初めて使用する場合は、最初に [Xamarin iOS アプリの作成]に関するチュートリアルを完了することをお勧めします。

このチュートリアルには、次のものが必要です。

* Visual Studio 2013
* Visual Studio の [Xamarin 拡張機能]**または OS X の** [Xamarin Studio]

## モバイル アプリ同期コードの確認

モバイル アプリのオフライン同期により、ネットワークにアクセスできないときでも、エンド ユーザーはローカル データベースとやり取りできます。アプリケーションでこれらの機能を使用するには、 `MobileServiceClient.SyncContext` をローカル ストアに初期化します。その後、 `IMobileServiceSyncTable` インターフェイスを使用してテーブルを参照します。
このセクションでは、 `QSTodoService.cs` のオフライン同期に関連するコードについて説明します。

1. Visual Studio で、[モバイル アプリの使用]に関するチュートリアルで完了したプロジェクトを開きます。ファイル  `QSTodoService.cs` を開きます。

2. メンバー  `todoTable` の種類が  `IMobileServiceSyncTable` であることに注意してください。オフライン同期では、 `IMobileServiceTable` の代わりにこの同期テーブル インターフェイスを使用します。同期テーブルが使用されると、すべての操作はローカル ストアを参照し、明示的なプッシュ操作とプル操作を使用したリモート サービスとのみ同期されます。

    同期テーブルへの参照を取得するには、メソッド  `GetSyncTable()` を使用します。オフライン同期機能を解除するには、代わりに  `GetTable()` を使用します。

3. テーブル操作を実行する前に、ローカル ストアを初期化する必要があります。これは、 `InitializeStoreAsync` メソッドで実行します。

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }

    これで、クラス  `MobileServiceSQLiteStore` を使用するローカル ストアが作成されます。このクラスはモバイル アプリ SDK で提供されます。また、 `IMobileServiceLocalStore` を実装することにより、別のローカル ストアを実装することもできます。

     `DefineTable` メソッドを実行すると、提供された型のフィールドに一致するテーブルがローカル ストアに作成されます。この例では、 `ToDoItem` になります。この型に、リモート データベース内のすべての列を含める必要はありません。列のサブセットのみ格納できます。

<!--     この  `InitializeAsync` のオーバーロードでは既定の競合ハンドラーを使用しますが、これは競合が発生するたびに失敗します。カスタム競合ハンドラーを提供するには、 [Mobile Services のオフライン サポートでの競合の処理]に関するチュートリアルを参照してください。
 -->
4. メソッド  `SyncAsync` は、実際の同期操作を次のようにトリガーします。

        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

    最初に、 `IMobileServiceSyncContext.PushAsync()` への呼び出しがあります。このメソッドは、すべての変更に対して変更をプッシュするため、同期テーブルではなく  `IMobileServicesSyncContext` のメンバーです。何らかの方法で (CUD 操作により) ローカルで変更されたレコードだけが、サーバー宛てに送信されます。

    次に、このメソッドは  `IMobileServiceSyncTable.PullAsync()` を呼び出し、サーバー上のテーブルのデータをプルしてアプリケーションに渡します。同期のコンテキストに保留中の変更があると、プルは常にプッシュを最初に実行することに注意してください。これは、ローカル ストア内でリレーションシップを持つすべてのテーブルに一貫性があることを確認するためです。この場合は、明示的にプッシュを呼び出しました。

    この例では、リモートの  `TodoItem` テーブルにあるすべてのレコードを取得していますが、クエリを渡すことでレコードをフィルター処理することもできます。 `PullAsync()` の最初のパラメーターは、増分同期に使用されるクエリ ID です。増分同期では  `UpdatedAt` タイムスタンプを使用して、前回の同期以降に変更されたレコードのみを取得します。クエリ ID は、アプリケーションの論理クエリごとに一意のわかりやすい文字列にする必要があります。増分同期を解除するには、 `null` をクエリ ID として渡します。これによってプル操作ごとにすべてのレコードを取得することになり、効率が悪くなる可能性があります。

<!--     >[AZURE.NOTE] モバイル サービスのデータベースからレコードが削除された場合に、デバイスのローカル ストアからレコードを削除するには、[論理削除]を有効にする必要があります。有効にしない場合は、アプリケーションで定期的に  `IMobileServiceSyncTable.PurgeAsync()` を呼び出して、ローカル ストアを削除する必要があります。

     `MobileServicePushFailedException` はプッシュ操作とプル操作の両方で発生する場合があることに注意してください。次のチュートリアル [モバイル サービスのオフライン サポートでの競合の処理] では、こうした同期に関連する例外を処理する方法について説明します。
-->

5. クラス  `QSTodoService` では、メソッド  `SyncAsync()` は、データ変更操作である  `InsertTodoItemAsync()` や  `CompleteItemAsync` の後に呼び出されます。また、 `RefreshDataAsync()` からも呼び出されるため、ユーザーは、更新ジェスチャを実行するたびに最新のデータを取得します。 `QSTodoListViewController.ViewDidLoad()` は  `RefreshDataAsync()` を呼び出すため、アプリケーションは起動時に同期も実行します。

    データが変更されるたびに  `SyncAsync()` が呼び出されることから、このアプリケーションは、データが編集されるたびにユーザーがオンラインであると想定します。次のセクションでは、ユーザーがオフラインの場合でも編集できるようにアプリケーションを更新します。

## アプリケーションの同期の動作を更新する

このセクションでは、アプリケーションの起動または挿入と更新の操作ではアプリケーションが同期されず、更新ジェスチャが実行された場合にのみ同期されるように、アプリケーションを変更します。その後、オフラインのシナリオをシミュレートするために、モバイル バックエンドに対するアプリケーションの接続を解除します。データ項目を追加すると、追加したデータ項目はローカル ストアに保持されますが、モバイル バックエンドのデータ ストアに即時に同期されるわけではありません。

1.  `QSTodoService.cs` を開きます。次のメソッドで  `SyncAsync()` への呼び出しをコメントにします。

    - `InsertTodoItemAsync`
    - `CompleteItemAsync`
    - `RefreshAsync`

    これで、 `RefreshAsync()` はローカル ストアからデータを読み込むだけで、アプリケーションのバックエンドには接続しません。

2.  `QSTodoService.cs` で  `applicationURL` の定義を変更して、無効なモバイル アプリケーションの URI を参照するようにします。

        const string applicationURL = @"https://your-service.azurewebsites.xxx/"; // invalid URI

3. 更新ジェスチャの実行時にデータが同期されることを確認するため、メソッド  `QSTodoListViewController.RefreshAsync()` を編集します。 `RefreshDataAsync()` への呼び出しの前に、 `SyncAsync()` への呼び出しを追加します。

        private async Task RefreshAsync ()
        {
            RefreshControl.BeginRefreshing ();

            await todoService.SyncAsync();
            await todoService.RefreshDataAsync (); // add this line

            RefreshControl.EndRefreshing ();

            TableView.ReloadData ();
        }

4. アプリケーションをビルドし、実行します。新しい todo 項目を追加します。これらの新しい項目は、モバイル バックエンドにプッシュされるまでは、ローカル ストア内にのみ存在します。クライアント アプリケーションは、バックエンドに接続されているかのように動作し、作成、読み取り、更新、削除 (CRUD) 操作のすべてをサポートします。

5. アプリケーションを終了し、再起動して、作成した新しい項目がローカル ストアに保存されていることを確認します。

## モバイル バックエンドに再接続するようにアプリケーションを更新する

このセクションでは、アプリケーションをモバイル バックエンドに再接続して、アプリケーションがオンライン状態に戻ったときの動作をシミュレーションします。更新ジェスチャを実行すると、データがモバイル バックエンドに同期されます。

1.  `QSTodoService.cs` を開きます。無効なモバイル アプリケーションの URL を削除し、正しい URL とアプリケーション キーを再度追加します。

2. アプリケーションを再構築して実行します。アプリケーションがモバイル バックエンドに接続されていても、データは変更されていないことに注意してください。これは、このアプリケーションが、ローカル ストアを示す  `IMobileServiceSyncTable` を常に使用するためです。

3. バックエンド SQL データベースに接続して、格納されているデータを表示します。Visual Studio で、**[サーバー エクスプローラー]**、**[Azure]**、**[SQL データベース]** の順に移動します。データベースを右クリックし、**[SQL Server オブジェクト エクスプローラーで開く]** を選択します。

    データベースとローカル ストアの間でデータがまだ同期されていないことを確認します。 *not*

4. アプリケーションで、項目の一覧をプルダウンして更新操作を実行します。これにより、アプリケーションは  `RefreshDataAsync()` を呼び出し、次に  `SyncAsync()` を呼び出します。これにより、プッシュ操作とプル操作が実行されます。まず、ローカル ストアの項目がモバイル バックエンドに送信され、次にバックエンドから新しいデータが取得されます。

5. データベース ビューを更新し、変更が同期されていることを確認します。

<!-- ##まとめ

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../includes/mobile-services-offline-summary-csharp.md)]

## 次のステップ

* [Mobile Services のオフライン サポートでの競合の処理]に関するページ

* [Azure モバイル サービス向け Xamarin コンポーネント クライアントを使用する方法]
 -->

<!-- Images -->

<!-- URLs. -->
[Xamarin iOS アプリの作成]: app-service-mobile-dotnet-backend-xamarin-ios-get-started.md

[Azure モバイル サービス向け Xamarin コンポーネント クライアントを使用する方法]: partner-xamarin-mobile-services-how-to-use-client-library.md

[Xamarin Studio]: http://xamarin.com/download
[Xamarin 拡張機能]: http://xamarin.com/visual-studio

<!--HONumber=49-->