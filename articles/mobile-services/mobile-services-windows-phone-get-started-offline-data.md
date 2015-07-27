<properties 
	pageTitle="Mobile Services でのオフライン データの使用 (Windows Phone) | モバイル デベロッパー センター" 
	description="Azure Mobile Services を使用して、Windows Phone アプリケーションのオフライン データを同期する方法を説明します。" 
	documentationCenter="mobile-services" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="wesmc;donnam"/>

# Mobile Services でのオフライン データの同期の使用

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]


このトピックでは、Azure モバイル サービスのオフライン機能を使用する方法を説明します。Azure モバイル サービスのオフライン機能を使用すると、モバイル サービスに対してオフラインになっている状況でも、ローカル データベースとやり取りすることができます。再びオンライン状態に復帰したときに、オフライン機能により、ローカルの変更をモバイル サービスに同期させることができます。

このチュートリアルでは、「[データの使用]」チュートリアルで使用したアプリケーションを更新し、Azure Mobile Services のオフライン機能をサポートできるようにします。その後、切断されたオフラインの状況でデータを追加し、それらの項目をオンライン データベースに同期してから、Azure の管理ポータルにログインして、アプリケーションを実行したときにデータに加えた変更を表示します。


>[AZURE.NOTE]このチュートリアルの目的は、Azure を使用して Windows Phone アプリケーションのデータを格納および取得できるようにするための Mobile Services のしくみを説明することにあります。Mobile Services を初めて使用する場合は、最初にチュートリアル「[Mobile Services の使用]」および「[データの使用]」を完了することをお勧めします。

このチュートリアルでは、次の基本的な手順について説明します。

1. [オフライン機能をサポートするようにアプリケーションを更新する]
2. [オフラインの状況でアプリケーションをテストする] 
3. [モバイル サービスに再接続するようにアプリケーションを更新する]
4. [モバイル サービスに接続されているアプリケーションをテストする]

このチュートリアルには、次のものが必要です。

* Visual Studio 2012
* [Windows Phone 8 SDK]
* 「[データの使用]」チュートリアルを完了していること
* [Azure Mobile Services SDK バージョン 1.3.0 (またはこれ以降)][Mobile Services SDK Nuget]
* [Azure Mobile Services SQLite Store バージョン 1.0.0 (またはこれ以降)][SQLite store nuget]
* [SQLite for Windows Phone 8]

>[AZURE.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、「<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure の無料評価版サイト</a>」をご覧ください。

## <a name="enable-offline-app"></a>オフライン機能をサポートするようにアプリケーションを更新する

Azure モバイル サービスのオフライン機能を使用すると、モバイル サービスに対してオフラインになっている状況でも、ローカル データベースとやり取りすることができます。アプリケーションでこれらの機能を使用するには、`MobileServiceClient.SyncContext` をローカル ストアに初期化します。その後、`IMobileServiceSyncTable` インターフェイスを使用してテーブルを参照します。

このセクションでは、オフライン機能のローカル ストアとして SQLite を使用します。

>[AZURE.NOTE]このセクションをスキップし、既にオフラインをサポートしているバージョンのプロジェクトをダウンロードすることもできます。オフライン サポートが有効になっているプロジェクトをダウンロードするには、「[Getting Started Offline Sample for Windows Phone (Windows Phone のオフライン サンプルの使用)]」を参照してください。


1. SQLite for Windows Phone 8 プロジェクトをインストールします。このリンク [SQLite for Windows Phone 8] からインストールできます。

    >[AZURE.NOTE]Internet Explorer を使用している場合は、SQLite をインストールするためにこのリンクをクリックすると、.vsix を .zip ファイルとしてダウンロードするためのプロンプトが表示されることがあります。ファイルに .zip ではなく .vsix 拡張子を付けて、ハード ドライブ上の場所に保存します。エクスプローラーで .vsix ファイルをダブルクリックすると、インストールが実行されます。

2. Visual Studio で、「[モバイル サービスの使用]」または「[データの使用]」のチュートリアルで完成したプロジェクトを開きます。ソリューション エクスプローラーで、プロジェクトにある [**参照設定**] を右クリックし、[**Windows Phone**]>[**Extensions**] で **SQLite for Windows Phone** への参照を追加します。

    ![][1]

3. SQLite ランタイムでは、ビルド対象のプロジェクトのプロセッサ アーキテクチャを **[x86]**、**[x64]**、または **[ARM]** に変更する必要があります。**[任意の CPU]** はサポートされません。プロセッサ アーキテクチャを、サポートされているテスト対象のいずれかの設定に変更します。

    ![][11]

4. Visual Studio のソリューション エクスプローラーで、クライアント アプリケーション プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックして NuGet パッケージ マネージャーを実行します。**SQLiteStore** を見つけ、**WindowsAzure.MobileServices.SQLiteStore** パッケージをインストールします。

    ![][2]

5. Visual Studio のソリューション エクスプローラーで、MainPage.xaml.cs ファイルを開きます。次の using ステートメントをファイルの先頭に追加します。

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Newtonsoft.Json.Linq;

6. Mainpage.xaml.cs 内で、`todoTable` の宣言を、`IMobileServicesSyncTable` の型の宣言に置き換えます。これは、`MobileServicesClient.GetSyncTable()` の呼び出しによって初期化されます。

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();

7. MainPage.xaml.cs で、`TodoItem` クラスを更新し、次のようにこのクラスに **Version** システム プロパティが含まれるようにします。

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


8. MainPage.xaml.cs で、`OnNavigatedTo` イベント ハンドラーを更新し、これが `async` メソッドとなり、SQLite ストアを使用してクライアントの同期コンテキストを初期化するようにします。この SQLite ストアは、モバイル サービス テーブルのスキーマに一致するテーブルを使用して作成しますが、この中には、前の手順で追加した **Version** システム プロパティが含まれている必要があります。

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

9. Visual Studio のソリューション エクスプローラーで、MainPage.xaml ファイルを開きます。**[最新の情報に更新]** ボタンの定義を探します。これを次のスタック パネルの定義に置き換えます。

    このコードは、**Push** と **Pull** の各操作に対応するクリック イベント ハンドラーを持つ 2 つのボタン コントロールも追加します。これらのボタンは [最新の情報に更新] ボタンの場所で水平に並びます。ファイルを保存します。

        <StackPanel  Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2" HorizontalAlignment="Center">
          <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Width="160">Refresh</Button>
          <Button Name="ButtonPush" Click="ButtonPush_Click" Width="160">Push</Button>
          <Button Name="ButtonPull" Click="ButtonPull_Click" Width="160">Pull</Button>
        </StackPanel>

    または、次のスクリーンショットに合わせて、テキストブロックのテキストを変更します。

    ![][12]
        


10. MainPage.xaml.cs で、**Push** と **Pull** の各ボタンに対応するボタン クリック イベント ハンドラーを追加し、ファイルを保存します。

        private async void ButtonPull_Click(object sender, RoutedEventArgs e)
        {
            Exception pullException = null;
            try
            {
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
                RefreshTodoItems();
            }
            catch (Exception ex)
            {
                pullException = ex;
            }
            if (pullException != null) {
                MessageBox.Show("Pull failed: " + pullException.Message +
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Pull again when connected with your Mobile Serice.");
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
                MessageBox.Show(errorString + 
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Push again when connected with your Mobile Serice.");
            }
        }

11. このアプリケーションをまだ実行しないでください。**F7** キーを押してプロジェクトをリビルドします。ビルド エラーが発生しないことを確認します。

## <a name="test-offline-app"></a>オフラインの状況でアプリケーションをテストする

ここでは、オフラインの状況をシミュレートするために、モバイル サービスに対するアプリケーションの接続を解除します。その後、いくつかのデータ項目を追加します。これらはローカル ストア内に保持されます。

このセクションでは、アプリケーションをどのモバイル サービスに接続してはならないことに注意してください。したがって、**Push** と **Pull** の各ボタンをテストすると、例外がスローされます。次のセクションでは、このクライアント アプリケーションをモバイル サービスに再度接続し、ストアをモバイル サービスのデータベースに同期する目的で、**Push** と **Pull** の各操作をテストします。


1. Visual Studio のソリューション エクスプローラーで App.xaml.cs を開きます。URLとして "**azure-mobile.net**" を "**azure-mobile.xxx**" に置き換えることによって、**MobileServiceClient** の初期化を無効なアドレスに変更します。その後、ファイルを保存します。

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2. Visual Studio で、**F5** キーを押してアプリケーションをビルドして実行します。新しい todo 項目を入力し、**[Save]** をクリックします。モバイル サービスにプッシュされるまで、新しい todo 項目はローカル ストア内にのみ存在します。クライアント アプリケーションは、まるで作成、読み取り、更新、削除 (CRUD) 操作のすべてをサポートするモバイル サービスに接続されているかのように動作します。

    ![][4]

3. アプリケーションを終了し、再起動して、作成した新しい項目がローカル ストアに保存されていることを確認します。

## <a name="update-online-app"></a>モバイル サービスに再接続するようにアプリケーションを更新する

ここでは、アプリケーションをモバイル サービスに再接続します。これは、アプリケーションがオフライン状態から、モバイル サービスとのオンライン状態に移行したことをシミュレートします。


1. Visual Studio のソリューション エクスプローラーで App.xaml.cs を開きます。URLとして "**azure-mobile.xxx**" を "**azure-mobile.net**" に置き換えることによって、**MobileServiceClient** の初期化を元の正しいアドレスに変更します。その後、ファイルを保存します。

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );


## <a name="test-online-app"></a>モバイル サービスに接続されているアプリケーションをテストする


ここではプッシュ操作とプル操作をテストし、ローカル ストアをモバイル サービス データベースに同期します。

1. Visual Studio で、**F5** キーを押してアプリケーションをリビルドして実行します。アプリケーションは現在モバイル サービスに接続されていますが、オフラインの状況と同じ表示になっていることに注意してください。この原因は、このアプリケーションが常に、ローカル ストアを指している `IMobileServiceSyncTable` との組み合わせで動作していることにあります。

    ![][4]

2.  Microsoft Azure の管理ポータルにログインし、モバイル サービスに対応するデータベースを参照します。開発中のサービスが、モバイル サービスとして JavaScript バックエンドを使用している場合は、モバイル サービスの **[データ]** タブからデータを参照できます。

    モバイル サービスの .NET バックエンドを使用している場合は、Visual Studio で、[**サーバー エクスプローラー**]、[**Azure**]、[**SQL データベース**] の順に移動します。データベースを右クリックし、[**SQL Server オブジェクト エクスプローラーで開く**] を選択します。

    データベースとローカル ストアの間でデータがまだ同期されていないことを確認します。

    ![][6]

3. アプリケーションで、**[Push]** をクリックします。これにより、アプリケーションは `MobileServiceClient.SyncContext.PushAsync` を呼び出し、次に `RefreshTodoItems` を呼び出して、ローカル ストアから取得した項目を使用してアプリケーション内のデータを更新します。このプッシュ操作により、モバイル サービス データベースはストアからデータを受信します。ただし、ローカル ストアは、モバイル サービス データベースから項目を受信しません。

    プッシュ操作は `IMobileServicesSyncTable` の代わりに、`MobileServiceClient.SyncContext` を実行し、同期コンテキストに関連付けられているすべてのテーブルに対して変更をプッシュします。これは、テーブル間にリレーションシップがある状況に対応することを目的としています。

    ![][7]

4. アプリケーション内には、ローカル ストアに渡す必要のあるいくつかの新しい項目が存在します。

    ![][8]

5. 今度は、アプリケーションの **[Pull]** をクリックします。アプリケーションは、`IMobileServiceSyncTable.PullAsync()` と `RefreshTodoItems` のみを呼び出します。モバイル サービス データベースから取得したすべてのデータがローカル ストアにプルされ、アプリケーション内で表示されることに注意してください。ただし、ローカル ストア内のすべてのデータが既にモバイル サービス データベースに対してプッシュされていることに注意してください。これは、**プルは必ず、最初にプッシュを実行する**ことが原因です。
 
    この例では、リモート `todoTable` ですべてのレコードを取得しますが、クエリを渡すことによりレコードをフィルタリングすることも可能です。`PullAsync` の最初のパラメーターは増分同期に使用されるクエリ ID ですが、この同期では、`UpdatedAt` タイムスタンプを使用して最後の同期から変更があったレコードのみを取得します。クエリ ID は、アプリ内の各論理クエリに対して一意の、わかりやすい文字列にする必要があります。増分同期を解除するには、`null` をクエリ ID として渡します。これによってプル操作ごとにすべてのレコードを取得することになり、効率が悪くなる可能性があります。

    >[AZURE.NOTE]オフライン データの同期で削除されたレコードの同期をサポートするには、[論理削除]を有効にする必要があります。有効にしない場合は、`IMobileServiceSyncTable.PurgeAsync()` を呼び出してローカル ストアを消去する必要があります。

 
    ![][9]

    ![][10]
  

##概要

##概要

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## 次のステップ

* [Mobile Services のオフライン サポートでの競合を処理する]

* [Mobile Services での論理削除の使用方法][Soft Delete]

<!-- Anchors. -->
[オフライン機能をサポートするようにアプリケーションを更新する]: #enable-offline-app
[オフラインの状況でアプリケーションをテストする]: #test-offline-app
[モバイル サービスに再接続するようにアプリケーションを更新する]: #update-online-app
[モバイル サービスに接続されているアプリケーションをテストする]: #test-online-app
[Next Steps]: #next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-phone-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[3]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-sqlitepcl-nuget.png
[4]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-offline-app-run1.png
[5]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run1.png
[6]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run2.png
[9]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run3.png
[10]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse3.png
[11]: ./media/mobile-services-windows-phone-get-started-offline-data/vs-select-processor-architecture.png
[12]: ./media/mobile-services-windows-phone-get-started-offline-data/ui-screenshot.png

<!-- URLs. -->
[Mobile Services のオフライン サポートでの競合を処理する]: mobile-services-windows-phone-handling-conflicts-offline-data.md
[Getting Started Offline Sample for Windows Phone (Windows Phone のオフライン サンプルの使用)]: http://go.microsoft.com/fwlink/?LinkId=397952
[Mobile Services の使用]: ../mobile-services-windows-phone-get-started.md
[モバイル サービスの使用]: ../mobile-services-windows-phone-get-started.md
[データの使用]: mobile-services-windows-phone-get-started-data.md
[SQLite for Windows Phone 8]: http://go.microsoft.com/fwlink/?LinkId=397953
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?linkid=268374
[Soft Delete]: mobile-services-using-soft-delete.md
[論理削除]: mobile-services-using-soft-delete.md

[Mobile Services SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[SQLite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0
 

<!---HONumber=July15_HO3-->