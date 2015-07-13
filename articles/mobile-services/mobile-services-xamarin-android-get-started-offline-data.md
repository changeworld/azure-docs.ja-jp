<properties
	pageTitle="Mobile Services でのオフライン データの使用 (Xamarin Android) | モバイル デベロッパー センター"
	description="Azure Mobile Services を使用して、Xamarin Android アプリケーションのオフライン データをキャッシュおよび同期する方法を説明します。"
	documentationCenter="xamarin"
	authors="lindydonna"
	editor="wesmc"
	manager="dwrede"
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/15/2015"
	ms.author="donnam"/>

# Mobile Services でのオフライン データの同期の使用

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

このトピックでは、Azure Mobile Services のオフライン同期機能について、todo リスト クイックスタート アプリケーションを使用して説明します。オフライン同期により、エンド ユーザーがネットワークにアクセスできないときでも使用できるアプリケーションを容易に作成することができます。

オフライン同期には、いくつかの潜在的な用途があります。

* サーバー データをデバイスにローカルでキャッシュすることにより、アプリケーションの応答性を向上させる。
* 断続的なネットワーク接続に対してアプリケーションに弾力性を持たせる。
* エンド ユーザーがネットワークにアクセスできなくてもデータを作成および変更できるようにすることで、接続がほとんどまたはまったく得られないような状況をサポートする。
* 複数のデバイス間でデータを同期させ、同じレコードが 2 つのデバイスによって変更されたときに競合を検出する。

>[AZURE.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、Azure 試用版にサインアップして最大 10 の無料モバイル サービスを取得し、試用期間が終わった後でも使用し続けることができます。詳細については、「<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure の無料評価版サイト</a>」をご覧ください。
>
> Mobile Services を初めて使用する場合には、「[Mobile Services の使用]」を完了しておく必要があります。

このチュートリアルでは、次の基本的な手順について説明します。

1. [Mobile Services 同期コードのレビュー]
2. [アプリケーションの同期の動作を更新する]
3. [モバイル サービスに再接続するようにアプリケーションを更新する]

このチュートリアルには、次のものが必要です。

* Visual Studio と [Xamarin 拡張機能]**または** [Xamarin Studio]
* チュートリアル「[Mobile Services の使用]」を完了していること

## <a name="review-offline"></a>Mobile Services 同期コードのレビュー

ネットワークにアクセスできない場合、エンドユーザーは Azure モバイル サービスのオフライン同期により、ローカル データベースとやり取りできるようになります。アプリケーションでこれらの機能を使用するには、`MobileServiceClient.SyncContext` をローカル ストアに初期化します。その後、`IMobileServiceSyncTable` インターフェイスを使用してテーブルを参照します。このセクションでは、`ToDoActivity.cs` のオフライン同期に関連するコードについて説明します。

1. Visual Studio または Xamarin Studio で、「[Mobile Services の使用]」チュートリアルで完了したプロジェクトを開きます。ファイル `ToDoActivity.cs` を開きます。

2. メンバー `toDoTable` の種類が `IMobileServiceSyncTable` であることに注意してください。オフライン同期では、`IMobileServiceTable` の代わりにこの同期テーブル インターフェイスを使用します。同期テーブルが使用されると、すべての操作はローカル ストアを参照し、明示的なプッシュ操作とプル操作を使用したリモート サービスとのみ同期されます。

    同期テーブルへの参照を取得するには、`GetSyncTable()` メソッドを使用します。オフライン同期機能を解除するには、代わりに `GetTable()` を使用します。

3. テーブル操作を実行する前に、ローカル ストアを初期化する必要があります。これは、`InitLocalStoreAsync` メソッドで実行します。

        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }

    これで、クラス `MobileServiceSQLiteStore` を使用するローカル ストアが作成されます。このクラスは Mobile Services SDK で提供されます。また、`IMobileServiceLocalStore` を実装することにより、別のローカル ストアを実装することもできます。

    `DefineTable` メソッドを実行すると、提供された型のフィールドに一致するテーブルがローカル ストアに作成されます。この例では、`ToDoItem` になります。この型に、リモート データベース内のすべての列を含める必要はありません。列のサブセットのみ格納できます。

    この `InitializeAsync` のオーバーロードでは既定の競合ハンドラーを使用しますが、これは競合が発生するたびに失敗します。カスタム競合ハンドラーを提供するには、[Mobile Services のオフライン サポートでの競合の処理]に関するチュートリアルを参照してください。

4. メソッド `SyncAsync` は、実際の同期操作を次のようにトリガーします。

        private async Task SyncAsync()
        {
            await client.SyncContext.PushAsync();
            await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
        }

    最初に、`IMobileServiceSyncContext.PushAsync()` への呼び出しがあります。このメソッドは、すべてのテーブルに対して変更をプッシュするため、同期テーブルではなく `IMobileServicesSyncContext` のメンバーです。何らかの方法で (CUD 操作により) ローカルで変更されたレコードだけが、サーバー宛てに送信されます。

    次に、このメソッドは `IMobileServiceSyncTable.PullAsync()` を呼び出し、サーバー上のテーブルのデータをプルしてアプリケーションに渡します。同期のコンテキストに保留中の変更があると、プルは常にプッシュを最初に実行することに注意してください。これは、ローカル ストア内でリレーションシップを持つすべてのテーブルに一貫性があることを確認するためです。この場合は、明示的にプッシュを呼び出しました。

    この例では、リモートの `TodoItem` テーブルにあるすべてのレコードを取得していますが、クエリを渡すことでレコードをフィルター処理することもできます。`PullAsync()` の最初のパラメーターは、増分同期に使用されるクエリ ID です。増分同期では `UpdatedAt` タイムスタンプを使用して、前回の同期以降に変更されたレコードのみを取得します。クエリ ID は、アプリ内の各論理クエリに対して一意の、わかりやすい文字列にする必要があります。増分同期を解除するには、`null` をクエリ ID として渡します。これによってプル操作ごとにすべてのレコードを取得することになり、効率が悪くなる可能性があります。

    >[AZURE.NOTE]モバイル サービス データベースでレコードが削除されたときに、デバイスのローカル ストアからレコードを削除するには、[論理的な削除]を有効にする必要があります。有効にしない場合は、アプリで定期的に `IMobileServiceSyncTable.PurgeAsync()` を呼び出して、ローカル ストアを削除する必要があります。

    `MobileServicePushFailedException` はプッシュ操作とプル操作の両方で発生する場合があることに注意してください。次のチュートリアル「[Mobile Services のオフライン サポートでの競合を処理する]」では、これらの同期関連の例外の処理方法について説明します。

5. クラス `ToDoActivity` では、メソッド `SyncAsync()` はデータ変更操作である `AddItem()` や `CheckItem()` の後に呼び出されます。これは `OnRefreshItemsSelected()` からも呼び出され、ユーザーが [**更新**] をクリックするたびに最新データが取得されます。`ToDoActivity.OnCreate()` は `OnRefreshItemsSelected()` を呼び出すため、アプリケーションは起動時に同期も実行します。

    データが変更されるたびに `SyncAsync()` が呼び出されることから、このアプリケーションは、データが編集されるたびにユーザーがオンラインであると想定します。次のセクションでは、ユーザーがオフラインの場合でも編集できるようにアプリケーションを更新します。

## <a name="update-sync"></a>アプリケーションの同期の動作を更新する

このセクションでは、アプリケーションの起動時や、挿入と更新の操作時には同期が実行されず、[更新] がクリックされた場合にのみ同期が実行されるように、アプリケーションを変更します。その後、オフラインの状況をシミュレートするために、モバイル サービスに対するアプリケーションの接続を解除します。データ項目を追加すると、それはローカル ストアに保持されますが、即時にモバイル サービスと同期されることはありません。

1. クラス `ToDoActivity` で `SyncAsync()` への呼び出しをコメント アウトするように、メソッド `AddItem()` と `CheckItem()` を編集します。

2. `ToDoActivity` で、メンバー `applicationURL` と `applicationKey` の定義をコメント アウトします。次の行を追加します。これらの行は無効なモバイル サービスの URL を参照します。

        const string applicationURL = @"https://your-mobile-service.azure-mobile.xxx/";
        const string applicationKey = @"AppKey";

3. `ToDoActivity.OnCreate()` で、`OnRefreshItemsSelected()` への呼び出しを削除し、置き換えます。

        // Load the items from the Mobile Service
        // OnRefreshItemsSelected (); // don't sync on app launch
        await RefreshItemsFromTableAsync(); // load UI only

4. アプリケーションをビルドし、実行します。新しい todo 項目を追加します。これらの新しい項目は、モバイル サービスにプッシュされるまでは、ローカル ストア内にのみ存在します。クライアント アプリケーションは、作成、読み取り、更新、削除 (CRUD) 操作のすべてをサポートするモバイル サービスに接続されているときと同様に動作します。

5. アプリケーションを終了し、再起動して、作成した新しい項目がローカル ストアに保存されていることを確認します。

## <a name="update-online-app"></a>モバイル サービスに再接続するようにアプリケーションを更新する

ここでは、アプリケーションをモバイル サービスに再接続します。これは、アプリケーションがオフライン状態から、モバイル サービスとのオンライン状態に移行したことをシミュレートします。[**更新**] をクリックすると、データがモバイル サービスに同期されます。

1. `ToDoActivity.cs` を開きます。無効なモバイル サービスの URL を削除し、正しい URL とアプリケーション キーを再度追加します。

2. アプリケーションを再構築して実行します。アプリケーションは現在モバイル サービスに接続されていますが、オフラインの状況と同じ表示になっていることに注意してください。これは、このアプリケーションが、ローカル ストアを示す `IMobileServiceSyncTable` を常に使用するためです。

3. Microsoft Azure の管理ポータルにログインし、モバイル サービスに対応するデータベースを参照します。開発中のサービスが JavaScript バックエンドを使用している場合は、モバイル サービスの [**データ**] タブからデータを参照できます。

    モバイル サービスの .NET バックエンドを使用している場合は、Visual Studio で、[**サーバー エクスプローラー**]、[**Azure**]、[**SQL データベース**] の順に移動します。データベースを右クリックし、[**SQL Server オブジェクト エクスプローラーで開く**] を選択します。

    データベースとローカル ストアの間でデータがまだ同期されて*いない*ことを確認します。

4. アプリケーションで、[更新] をクリックします。これにより `OnRefreshItemsSelected()` が呼び出され、その結果 `SyncAsync()` が呼び出されます。これによってプッシュとプルの処理が実行されます。まず、ローカル ストアの項目がモバイル サービスに送信され、次に、新しいデータがサービスから取得されます。

5. モバイル サービスのデータベースで変更が同期されていることを確認してください。

##概要

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## 次のステップ

* [Mobile Services のオフライン サポートでの競合を処理する]

* [Azure Mobile Services 向け Xamarin コンポーネント クライアントを使用する方法]

<!-- Anchors. -->
[Mobile Services 同期コードのレビュー]: #review-offline
[アプリケーションの同期の動作を更新する]: #update-sync
[モバイル サービスに再接続するようにアプリケーションを更新する]: #update-online-app

<!-- Images -->


<!-- URLs. -->
[Mobile Services のオフライン サポートでの競合の処理]: ../mobile-services-xamarin-android-handling-conflicts-offline-data.md
[Mobile Services のオフライン サポートでの競合を処理する]: ../mobile-services-xamarin-android-handling-conflicts-offline-data.md
[Get started with data]: mobile-services-android-get-started-data.md
[Mobile Services の使用]: mobile-services-android-get-started.md
[Azure Mobile Services 向け Xamarin コンポーネント クライアントを使用する方法]: partner-xamarin-mobile-services-how-to-use-client-library.md
[論理的な削除]: mobile-services-using-soft-delete.md

[Mobile Services SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[SQLite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0
[Xamarin Studio]: http://xamarin.com/download
[Xamarin 拡張機能]: http://xamarin.com/visual-studio
[NuGet Addin for Xamarin]: https://github.com/mrward/monodevelop-nuget-addin
 

<!---HONumber=62-->