<properties linkid="develop-mobile-tutorials-get-started-offline-data-android" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (Xamarin Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Xamarin Android application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data in Mobile Services" authors="donnam,wesmc" editor="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam,wesmc"></tags>

# モバイル サービスでのオフライン データの同期の使用

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="Windows ストア C#">Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/ja-jp/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS">iOS</a>
<a href="/ja-jp/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/ja-jp/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

このトピックでは、Azure モバイル サービスのオフライン機能を使用する方法を説明します。Azure モバイル サービスのオフライン機能を使用すると、モバイル サービスに対してオフラインになっている状況でも、ローカル データベースとやり取りすることができます。再びオンライン状態に復帰したときに、オフライン機能により、ローカルの変更をモバイル サービスに同期させることができます。

このチュートリアルでは、[モバイル サービスの使用][モバイル サービスの使用] または [データの使用][データの使用] いずれかのチュートリアルで使用したアプリケーションを更新し、Azure モバイル サービスのオフライン機能をサポートできるようにします。その後、切断されたオフラインの状況でデータを追加し、それらの項目をオンライン データベースに同期してから、Azure の管理ポータルにログインして、アプリケーションを実行したときにデータに加えた変更を表示します。

> [WACOM.NOTE] このチュートリアルの目的は、Azure を使用して Windows ストア アプリケーションのデータを格納および取得できるようにするための Mobile Services のしくみを説明することです。したがって、このトピックでは、モバイル サービスのクイック スタートで完了している手順の多くについても説明します。モバイル サービスを初めて使用する場合は、最初にチュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を完了することをお勧めします。

> [WACOM.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。

このチュートリアルでは、次の基本的な手順について説明します。

1.  [オフライン機能をサポートするようにアプリケーションを更新する][オフライン機能をサポートするようにアプリケーションを更新する]
2.  [モバイル サービスに接続されているアプリケーションをテストする][モバイル サービスに接続されているアプリケーションをテストする]

このチュートリアルには、次のものが必要です。

-   Visual Studio と [Xamarin 拡張機能][Xamarin 拡張機能]**または** [Xamarin Studio][Xamarin Studio]
-   「[モバイル サービスの使用][モバイル サービスの使用]」または「[データの使用][データの使用]」チュートリアルを完了していること
-   [Azure Mobile Services SDK バージョン 1.3.0-alpha3][Azure Mobile Services SDK バージョン 1.3.0-alpha3]
-   [Azure Mobile Services SQLite Store バージョン 1.0.0-alpha2][Azure Mobile Services SQLite Store バージョン 1.0.0-alpha2]

> [WACOM.NOTE] 以下に示す手順では、Visual Studio 2012 以降と Xamarin 拡張機能を使用していると想定しています。Xamarin Studio を使用している場合、ほとんどの手順は同じですが、[NuGet Addin for Xamarin][NuGet Addin for Xamarin] をインストールすると、プロジェクトにプレリリースの Mobile Services NuGet パッケージを簡単に追加できます。

## <a name="enable-offline-app"></a>オフライン機能をサポートするようにアプリケーションを更新する

Azure モバイル サービスのオフライン機能を使用すると、モバイル サービスに対してオフラインになっている状況でも、ローカル データベースとやり取りすることができます。アプリケーションでこれらの機能を使用するには、`MobileServiceClient.SyncContext` をローカル ストアに初期化します。その後、`IMobileServiceSyncTable` インターフェイスを使用してテーブルを参照します。

1.  Visual Studio で、「[モバイル サービスの使用][モバイル サービスの使用]」または「[データの使用][データの使用]」のチュートリアルで完成したプロジェクトを開きます。ソリューション エクスプローラーで、**[コンポーネント]** の **Azure Mobile Services SDK** の参照を削除します。

2.  パッケージ マネージャー コンソールで次のコマンドを使用して、Mobile Services SQLiteStore のプレリリース パッケージをインストールします。

        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    これで、必須の依存関係もすべてインストールされます。

3.  参照ノードで、`System.IO`、`System.Runtime`、および `System.Threading.Tasks` への参照を削除します。

### ToDoActivity.cs を編集する

-   宣言を追加します。

        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using System.IO;

-   メンバー `ToDoActivity.toDoTable` の型を `IMobileServiceTable<>` から `IMobileServiceSyncTable<>` に変更します。

-   メソッド `OnCreate(Bundle)` で、メンバー `client` を初期化する行の後に次のコードを追加します。

        // existing initializer
        client = new MobileServiceClient (applicationURL, applicationKey, progressHandler);

        // new code to initialize the SQLite store
        string path = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "test1.db");

        if (!File.Exists(path))
        {
            File.Create(path).Dispose();
        }

        var store = new MobileServiceSQLiteStore(path);
        store.DefineTable<ToDoItem>();

        await client.SyncContext.InitializeAsync(store, new TodoSyncHandler(this));

-   同じメソッド内で、`toDoTable` を初期化する行を変更して、`GetTable<>` の代わりにメソッド `GetSyncTable<>` を使用します。

        toDoTable = client.GetSyncTable <ToDoItem> ();

-   メソッド `OnRefreshItemsSelected` を変更して、`PushAsync` および `PullAsync` の呼び出しを追加します。

        async void OnRefreshItemsSelected ()
        {
            await client.SyncContext.PushAsync();
            await toDoTable.PullAsync();
            await RefreshItemsFromTableAsync();
        }

### ToDoItem.cs を編集する

-   using ステートメントを追加します。

        using Microsoft.WindowsAzure.MobileServices; 

-   次のメンバーをクラス `ToDoItem` に追加します。

        [Version]
        public string Version { get; set; }


        public override string ToString()
        {
            return "Text: " + Text + "\nComplete: " + Complete + "\n";
        }

## <a name="test-online-app"></a> アプリケーションをテストする

このセクションでは、ローカル ストアをモバイル サービス データベースに同期する `SyncAsync` メソッドをテストします。

1.  Visual Studio で、**[実行]** をクリックしてプロジェクトをビルドし、このプロジェクトの既定である iPhone エミュレーターでアプリケーションを起動します。

2.  アプリケーションの項目一覧が空であることに注意してください。前のセクションでコードを変更したため、アプリケーションは、モバイル サービスからではなく、ローカル ストアから項目を読み取ります。

3.  To Do リストに項目を追加します。

    ![][]

4.  Microsoft Azure の管理ポータルにログインし、モバイル サービスに対応するデータベースを参照します。開発中のサービスが、モバイル サービスとして JavaScript バックエンドを使用している場合は、モバイル サービスの **[データ]** タブからデータを参照できます。モバイル サービスとして .NET バックエンドを使用している場合は、SQL Azure 拡張機能内にあるデータベースに対応する **[管理]** ボタンをクリックして、テーブルに対してクエリを実行することができます。

    データベースとローカル ストアの間でデータがまだ同期されていないことを確認します。

5.  アプリケーションで、**[更新]** をクリックします。これにより、アプリケーションは `MobileServiceClient.SyncContext.PushAsync` と `IMobileServiceSyncTable.PullAsync()` を呼び出し、次に `RefreshTodoItems` を呼び出して、ローカル ストアから取得した項目を使用してアプリケーション内のデータを更新します。

    このプッシュ操作により、モバイル サービス データベースはストアからデータを受信します。`IMobileServicesSyncTable` の代わりに、`MobileServiceClient.SyncContext` を実行し、同期コンテキストに関連付けられているすべてのテーブルに対して変更をプッシュします。これは、テーブル間にリレーションシップがある状況に対応することを目的としています。

    これに対して、プル操作は、指定されたテーブルからだけレコードを取得します。同期コンテキストでこのテーブルに対する操作が保留になると、`PushAsync` 操作が Mobile Services SDK によって暗示的に呼び出されます。

    ![][1]

    ![][2]

## まとめ

モバイル サービスのオフライン機能をサポートするために、`IMobileServiceSyncTable` インターフェイスを使用して、ローカル ストアにより `MobileServiceClient.SyncContext` を初期化しました。この場合は、ローカル ストアは、SQLite データベースでした。

モバイル サービスに対する通常の CRUD 操作は、まるでアプリケーションが接続されているかのように機能しますが、すべての操作はローカル ストアに対して実施されます。

ローカル ストアをサーバーと同期しようとする場合は、`IMobileServiceSyncTable.PullAsync` と `MobileServiceClient.SyncContext.PushAsync` の各メソッドを使用しました。

-   変更内容をサーバーにプッシュするために、`IMobileServiceSyncContext.PushAsync()` を呼び出しました。このメソッドは、すべてのテーブルに対して変更をプッシュするため、同期テーブルではなく `IMobileServicesSyncContext` のメンバーです。

    何らかの方法で (CUD 操作により) ローカルで変更されたレコードだけが、サーバー宛てに送信されます。

-   サーバー上のテーブルからアプリケーションにデータをプルするために、`IMobileServiceSyncTable.PullAsync` を呼び出しました。

    プルは必ず、最初にプッシュを実行します。

    クエリを指定することを許可する、**PullAsync()** のオーバー ロードも存在します。モバイル サービスのオフライン サポートに関するプレビュー リリースでは、**PullAsync** は、対応するテーブル (またはクエリ) 内にあるすべての行を読み取る予定です。たとえば、前回の同期より新しい行のみを読み取ろうとすることはありません。行が既にローカルの同期テーブル内に存在している場合は、それらの行は変化しないままです。

## 次のステップ

<!--* [Handling conflicts with offline support for Mobile Services] -->

-   [Azure Mobile Services 向け Xamarin コンポーネント クライアントを使用する方法][Azure Mobile Services 向け Xamarin コンポーネント クライアントを使用する方法]

<!-- Anchors. --> <!-- Images --> <!-- URLs. -->

  [Windows ストア C#]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data "Windows ストア C#"
  [Windows Phone]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-offline-data "Windows Phone"
  [iOS]: /ja-jp/documentation/articles/mobile-services-ios-get-started-offline-data "iOS"
  [Xamarin.iOS]: /ja-jp/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data "Xamarin.iOS"
  [Xamarin.Android]: /ja-jp/documentation/articles/mobile-services-xamarin-android-get-started-offline-data "Xamarin.Android"
  [モバイル サービスの使用]: /ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started/
  [データの使用]: /ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/
  [Azure の無料評価版サイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=AE564AB28
  [オフライン機能をサポートするようにアプリケーションを更新する]: #enable-offline-app
  [モバイル サービスに接続されているアプリケーションをテストする]: #test-online-app
  [Xamarin 拡張機能]: http://xamarin.com/visual-studio
  [Xamarin Studio]: http://xamarin.com/download
  [Azure Mobile Services SDK バージョン 1.3.0-alpha3]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-alpha3
  [Azure Mobile Services SQLite Store バージョン 1.0.0-alpha2]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-alpha2
  [NuGet Addin for Xamarin]: https://github.com/mrward/monodevelop-nuget-addin
  []: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-startup-android.png
  [1]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-completed-android.png
  [2]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-data-browse.png
  [Azure Mobile Services 向け Xamarin コンポーネント クライアントを使用する方法]: /ja-jp/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/
