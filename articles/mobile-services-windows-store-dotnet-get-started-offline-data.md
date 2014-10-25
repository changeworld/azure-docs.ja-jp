<properties linkid="develop-mobile-tutorials-get-started-offline-data-dotnet" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Windows Store application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data sync in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# モバイル サービスでのオフライン データの同期の使用

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="Windows ストア C#" class="current">Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/ja-jp/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS">iOS</a>
<a href="/ja-jp/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/ja-jp/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このチュートリアルでは、Azure モバイル サービスを使用して Windows ストア アプリにオフライン サポートを追加する方法を説明します。オフライン サポートを追加すると、オフラインになっている状況でも、ローカル データベースとやり取りすることができます。再びオンライン状態に復帰したときに、この機能により、ローカルの変更をモバイル サービスに同期させることができます。 
</p>
<p>右側のクリップを見ると、このチュートリアルと同じ手順をビデオで確認できます。</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">14:36:00</span></div>

</div>

このチュートリアルでは、[モバイル サービスの使用][モバイル サービスの使用] または [データの使用][データの使用] いずれかのチュートリアルで使用したアプリケーションを更新し、Azure モバイル サービスのオフライン機能をサポートできるようにします。その後、切断されたオフラインの状況でデータを追加し、それらの項目をオンライン データベースに同期してから、Azure の管理ポータルにログインして、アプリケーションを実行したときにデータに加えた変更を表示します。

> [WACOM.NOTE] このチュートリアルの目的は、Azure を使用して Windows ストア アプリのデータを格納および取得できるようにするためのモバイル サービスのしくみを説明することにあります。したがって、このトピックでは、モバイル サービスのクイック スタートで完了している手順の多くについても説明します。モバイル サービスを初めて使用する場合は、最初にチュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を完了することをお勧めします。
>
> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。

このチュートリアルでは、次の基本的な手順について説明します。

1.  [オフライン機能をサポートするようにアプリケーションを更新する][オフライン機能をサポートするようにアプリケーションを更新する]
2.  [オフラインの状況でアプリケーションをテストする][オフラインの状況でアプリケーションをテストする]
3.  [モバイル サービスに再接続するようにアプリケーションを更新する][モバイル サービスに再接続するようにアプリケーションを更新する]
4.  [モバイル サービスに接続されているアプリケーションをテストする][モバイル サービスに接続されているアプリケーションをテストする]

このチュートリアルには、次のものが必要です。

-   Windows 8.1 で実行されている Visual Studio 2013。
-   「[モバイル サービスの使用][モバイル サービスの使用]」または「[データの使用][データの使用]」チュートリアルを完了している。
-   [Azure Mobile Services SDK バージョン 1.3.0-beta2 (またはこれ以降)][Azure Mobile Services SDK バージョン 1.3.0-beta2 (またはこれ以降)]
-   [Azure Mobile Services SQLite Store バージョン 1.0.0-beta2 (またはこれ以降)][Azure Mobile Services SQLite Store バージョン 1.0.0-beta2 (またはこれ以降)]
-   SQLite for Windows 8.1

> [WACOM.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。

## <a name="enable-offline-app"></a>オフライン機能をサポートするようにアプリケーションを更新する

Azure モバイル サービスのオフライン機能を使用すると、モバイル サービスに対してオフラインになっている状況でも、ローカル データベースとやり取りすることができます。アプリケーションでこれらの機能を使用するには、`MobileServiceClient.SyncContext` をローカル ストアに初期化します。その後、`IMobileServiceSyncTable` インターフェイスを使用してテーブルを参照します。

このセクションでは、オフライン機能のローカル ストアとして SQLite を使用します。

> [WACOM.NOTE] このセクションをスキップし、既にオフラインをサポートしている Getting Started プロジェクトのバージョンをダウンロードすることもできます。オフライン サポートが有効になっているプロジェクトをダウンロードするには、[オフライン サンプルの使用][オフライン サンプルの使用]を参照してください。

1.  SQLite をインストールします。次のリンク [SQLite for Windows 8.1][SQLite for Windows 8.1] からインストールできます。

    > [WACOM.NOTE] Internet Explorer を使用している場合は、SQLite をインストールするためにこのリンクをクリックすると、.vsix を .zip ファイルとしてダウンロードするためのプロンプトが表示されることがあります。ファイルに .zip ではなく .vsix 拡張子を付けて、ハード ドライブ上の場所に保存します。エクスプローラーで .vsix ファイルをダブルクリックすると、インストールが実行されます。

2.  Visual Studio で、「[モバイル サービスの使用][モバイル サービスの使用]」または「[データの使用][データの使用]」のチュートリアルで完成したプロジェクトを開きます。ソリューション エクスプローラーで、プロジェクトにある **[参照設定]** を右クリックし、**[Windows]** \> **[Extensions]** で **SQLite for Windows Runtime** への参照を追加します。

    ![][]

3.  SQLite ランタイムでは、ビルド対象のプロジェクトのプロセッサ アーキテクチャを **[x86]**、**[x64]**、または **[ARM]** に変更する必要があります。**[任意の CPU]** はサポートされません。プロセッサ アーキテクチャを、サポートされているテスト対象のいずれかの設定に変更します。

4.  Visual Studio のソリューション エクスプローラーで、クライアント アプリケーション プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックして NuGet パッケージ マネージャーを実行します。**SQLiteStore** を見つけ、**WindowsAzure.MobileServices.SQLiteStore** パッケージをインストールします。

    ![][1]

5.  Visual Studio のソリューション エクスプローラーで、MainPage.xaml.cs ファイルを開きます。次の using ステートメントをファイルの先頭に追加します。

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Newtonsoft.Json.Linq;

6.  Mainpage.xaml.cs 内で、`todoTable` の宣言を、`IMobileServicesSyncTable` の型の宣言に置き換えます。これは、`MobileServicesClient.GetSyncTable()` の呼び出しによって初期化されます。

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();

7.  MainPage.xaml.cs で、`TodoItem` クラスを更新し、次のようにこのクラスに **Version** システム プロパティが含まれるようにします。

        public class TodoItem
        {
          public string Id { get; set; }
          [JsonProperty(PropertyName = "text")]
          public string Text { get; set; }
          [JsonProperty(PropertyName = "complete")]
          public bool Complete { get; set; }
          [Version]
          public string Version { get; set; }
        }

8.  MainPage.xaml.cs で、`OnNavigatedTo` イベント ハンドラーを更新し、これが `async` メソッドとなり、SQLite ストアを使用してクライアントの同期コンテキストを初期化するようにします。この SQLite ストアは、モバイル サービス テーブルのスキーマに一致するテーブルを使用して作成しますが、この中には、前の手順で追加した **Version** システム プロパティが含まれている必要があります。

        protected async override void OnNavigatedTo(NavigationEventArgs e)
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localsync12.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store, new MobileServiceSyncHandler());
            }
            RefreshTodoItems();
        }

9.  Visual Studio のソリューション エクスプローラーで、MainPage.xaml ファイルを開きます。**Query and Update Data** というタイトルの StackPanel を含む Grid 要素を見つけます。行の定義から ListView の開始タグに至るまでの要素を置き換える、次の UI コードを追加します。

    このコードは、要素をレイアウトするために、グリッドに行と列の定義を追加します。また、**Push** と **Pull** の各操作に対応するクリック イベント ハンドラーを持つ 2 つのボタン コントロールも追加します。これらのボタンは、`ListView` という名前の ListItems のすぐ上に配置されます。ファイルを保存します。

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <StackPanel Grid.Row="0" Grid.ColumnSpan="2">
            <local:QuickStartTask Number="2" Title="Query, Update, and Synchronize Data" 
              Description="Use the Pull and Push buttons to synchronize the local store with the server" />
        </StackPanel>
        <Button Grid.Row="1" Grid.Column="0" Margin="5,5,0,0" Name="ButtonPush"
            Click="ButtonPush_Click" Width="80" Height="34">Push</Button>
        <Button Grid.Row="1" Grid.Column="1" Margin="5,5,0,0"  Name="ButtonPull" 
            Click="ButtonPull_Click" Width="80" Height="34">Pull</Button>
        <ListView Name="ListItems" SelectionMode="None" Margin="0,10,0,0" Grid.ColumnSpan="2" Grid.Row="2">

10. MainPage.xaml.cs で、**Push** と **Pull** の各ボタンに対応するボタン クリック イベント ハンドラーを追加し、ファイルを保存します。

        private async void ButtonPull_Click(object sender, RoutedEventArgs e)
        {
            Exception pullException = null;
            try
            {
                await todoTable.PullAsync();
                RefreshTodoItems();
            }
            catch (Exception ex)
            {
                pullException = ex;
            }
            if (pullException != null) {
                MessageDialog d = new MessageDialog("Pull failed: " + pullException.Message +
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Pull again when connected with your Mobile Serice.");
                await d.ShowAsync();
            }
        }
        private async void ButtonPush_Click(object sender, RoutedEventArgs e)
        {
            string errorString = null;
            try
            {
                await App.MobileService.SyncContext.PushAsync();
                RefreshTodoItems();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " + 
                  ex.PushResult.Errors.Count() + ", message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Push failed: " + ex.Message;
            }
            if (errorString != null) {
                MessageDialog d = new MessageDialog(errorString + 
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Push again when connected with your Mobile Serice.");
                await d.ShowAsync();
            }
        }

11. このアプリケーションをまだ実行しないでください。**F7** キーを押してプロジェクトをリビルドします。ビルド エラーが発生しないことを確認します。

## <a name="test-offline-app"></a>オフラインの状況でアプリケーションをテストする

ここでは、オフラインの状況をシミュレートするために、モバイル サービスに対するアプリケーションの接続を解除します。その後、いくつかのデータ項目を追加します。これらはローカル ストア内に保持されます。

このセクションでは、アプリケーションをどのモバイル サービスに接続してはならないことに注意してください。したがって、**Push** と **Pull** の各ボタンをテストすると、例外がスローされます。次のセクションでは、このクライアント アプリケーションをモバイル サービスに再度接続し、ストアをモバイル サービスのデータベースに同期する目的で、**Push** と **Pull** の各操作をテストします。

1.  Visual Studio のソリューション エクスプローラーで App.xaml.cs を開きます。URL として **azure mobile.net** を **azure mobile.xxx** に置き換えることによって、**MobileServiceClient** の初期化を無効なアドレスに変更します。その後、ファイルを保存します。

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2.  Visual Studio で、**F5** キーを押してアプリケーションをビルドして実行します。新しい todo 項目を入力し、**[Save]** をクリックします。モバイル サービスにプッシュされるまで、新しい todo 項目はローカル ストア内にのみ存在します。クライアント アプリケーションは、まるで作成、読み取り、更新、削除 (CRUD) 操作のすべてをサポートするモバイル サービスに接続されているかのように動作します。

    ![][2]

3.  アプリケーションを終了し、再起動して、作成した新しい項目がローカル ストアに保存されていることを確認します。

## <a name="update-online-app"></a>モバイル サービスに再接続するようにアプリケーションを更新する

ここでは、アプリケーションをモバイル サービスに再接続します。これは、アプリケーションがオフライン状態から、モバイル サービスとのオンライン状態に移行したことをシミュレートします。

1.  Visual Studio のソリューション エクスプローラーで App.xaml.cs を開きます。URL として **azure-mobile.xxx** を **azure-mobile.net** に置き換えることによって、**MobileServiceClient** の初期化を正しいアドレスに戻して変更します。その後、ファイルを保存します。

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

## <a name="test-online-app"></a>モバイル サービスに接続されているアプリケーションをテストする

ここではプッシュ操作とプル操作をテストし、ローカル ストアをモバイル サービス データベースに同期します。

1.  Visual Studio で、**F5** キーを押してアプリケーションをリビルドして実行します。アプリケーションは現在モバイル サービスに接続されていますが、オフラインの状況と同じ表示になっていることに注意してください。この原因は、このアプリケーションが常に、ローカル ストアを指している `IMobileServiceSyncTable` との組み合わせで動作していることにあります。

    ![][3]

2.  Microsoft Azure の管理ポータルにログインし、モバイル サービスに対応するデータベースを参照します。開発中のサービスが、モバイル サービスとして JavaScript バックエンドを使用している場合は、モバイル サービスの **[データ]** タブからデータを参照できます。モバイル サービスとして .NET バックエンドを使用している場合は、SQL Azure 拡張機能内にあるデータベースに対応する **[管理]** ボタンをクリックして、テーブルに対してクエリを実行することができます。

    データベースとローカル ストアの間でデータがまだ同期されていないことを確認します。

    ![][4]

3.  アプリケーションで、**[Push]** をクリックします。これにより、アプリケーションは `MobileServiceClient.SyncContext.PushAsync` を呼び出し、次に `RefreshTodoItems` を呼び出して、ローカル ストアから取得した項目を使用してアプリケーション内のデータを更新します。このプッシュ操作により、モバイル サービス データベースはストアからデータを受信します。ただし、ローカル ストアは、モバイル サービス データベースから項目を受信しません。

    プッシュ操作は `IMobileServicesSyncTable` の代わりに、`MobileServiceClient.SyncContext` を実行し、同期コンテキストに関連付けられているすべてのテーブルに対して変更をプッシュします。これは、テーブル間にリレーションシップがある状況に対応することを目的としています。

    ![][5]

4.  アプリケーション内には、ローカル ストアに渡す必要のあるいくつかの新しい項目が存在します。

    ![][6]

5.  今度は、アプリケーションの **[Pull]** をクリックします。アプリケーションは、`IMobileServiceSyncTable.PullAsync()` と `RefreshTodoItems` のみを呼び出します。モバイル サービス データベースから取得したすべてのデータがローカル ストアにプルされ、アプリケーション内で表示されることに注意してください。ただし、ローカル ストア内のすべてのデータが既にモバイル サービス データベースに対してプッシュされていることに注意してください。これは、**プルは必ず、最初にプッシュを実行する**ことが原因です。

    ![][7]

    ![][8]

## まとめ

モバイル サービスのオフライン機能をサポートするために、`IMobileServiceSyncTable` インターフェイスを使用して、ローカル ストアにより `MobileServiceClient.SyncContext` を初期化しました。この場合は、ローカル ストアは、SQLite データベースでした。

モバイル サービスに対する通常の CRUD 操作は、まるでアプリケーションが接続されているかのように機能しますが、すべての操作はローカル ストアに対して実施されます。

ローカル ストアをサーバーと同期しようとする場合は、`IMobileServiceSyncTable.PullAsync` と `MobileServiceClient.SyncContext.PushAsync` の各メソッドを使用しました。

-   変更内容をサーバーにプッシュするために、`IMobileServiceSyncContext.PushAsync()` を呼び出しました。このメソッドは、すべてのテーブルに対して変更をプッシュするため、同期テーブルではなく `IMobileServicesSyncContext` のメンバーです。

    何らかの方法で (CRUD 操作により) ローカルで変更されたレコードだけが、サーバー宛てに送信されます。

-   サーバー上のテーブルからアプリケーションにデータをプルするために、`IMobileServiceSyncTable.PullAsync` を呼び出しました。

    プルは必ず、最初にプッシュを実行します。

    クエリを指定することを許可する、**PullAsync()** のオーバー ロードも存在します。モバイル サービスのオフライン サポートに関するプレビュー リリースでは、**PullAsync** は、対応するテーブル (またはクエリ) 内にあるすべての行を読み取る予定です。たとえば、前回の同期より新しい行のみを読み取ろうとすることはありません。行が既にローカルの同期テーブル内に存在している場合は、それらの行は変化しないままです。

## 次のステップ

-   [モバイル サービスのオフライン サポートでの競合を処理する][モバイル サービスのオフライン サポートでの競合を処理する]



  [Windows ストア C#]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data "Windows ストア C#"
  [Windows Phone]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-offline-data "Windows Phone"
  [iOS]: /ja-jp/documentation/articles/mobile-services-ios-get-started-offline-data "iOS"
  [Xamarin.iOS]: /ja-jp/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data "Xamarin.iOS"
  [Xamarin.Android]: /ja-jp/documentation/articles/mobile-services-xamarin-android-get-started-offline-data "Xamarin.Android"
  [チュートリアルを見る]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-get-started/
  [データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  [Azure の無料評価版サイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=AE564AB28
  [オフライン機能をサポートするようにアプリケーションを更新する]: #enable-offline-app
  [オフラインの状況でアプリケーションをテストする]: #test-offline-app
  [モバイル サービスに再接続するようにアプリケーションを更新する]: #update-online-app
  [モバイル サービスに接続されているアプリケーションをテストする]: #test-online-app
  [Azure Mobile Services SDK バージョン 1.3.0-beta2 (またはこれ以降)]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-beta2
  [Azure Mobile Services SQLite Store バージョン 1.0.0-beta2 (またはこれ以降)]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-beta2
  [オフライン サンプルの使用]: http://go.microsoft.com/fwlink/?LinkId=394777
  [SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
  []: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
  [2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-offline-app-run1.png
  [3]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run1.png
  [4]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
  [5]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
  [6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
  [7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run3.png
  [8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png
  [モバイル サービスのオフライン サポートでの競合を処理する]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/
