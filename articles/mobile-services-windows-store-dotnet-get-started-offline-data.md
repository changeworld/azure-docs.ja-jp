<properties urlDisplayName="Using Offline Data" pageTitle="Mobile Services でのオフライン データの使用 (Windows ストア) | モバイル デベロッパー センター" metaKeywords="" description="Azure Mobile Services を使用して、Windows ストア アプリケーションのオフライン データをキャッシュおよび同期する方法を説明します。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data sync in Mobile Services" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# Mobile Services でのオフライン データの同期の使用

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このチュートリアルでは、Azure Mobile Services を使用して Windows Universal ストア アプリにオフライン サポートを追加する方法を説明します。オフライン サポートを使用すると、アプリがオフラインの状況でもローカル データベースと対話できます。アプリがバックエンドのデータベースとオンラインになったときに、オフライン機能を使用したローカルの変更を同期します。 
</p>
<p>右側のクリップを見ると、このチュートリアルと同じ手順をビデオで確認できます。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">14:36</span></div>
</div>


このチュートリアルでは、「[モバイル サービスの使用]」チュートリアルで使用した Universal アプリケーション プロジェクトを更新して Azure Mobile Services のオフライン機能をサポートできるようにします。その後、切断されたオフラインの状況でデータを追加し、それらの項目をオンライン データベースに同期してから、Azure の管理ポータルにログインして、アプリケーションを実行したときにデータに加えた変更を表示します。


>[WACOM.NOTE] このチュートリアルの目的は、Mobile Services が Windows ストア アプリのデータを Azure に格納および取得できるようにするしくみを説明することにあります。Mobile Services を初めて使用する場合は、チュートリアル「[モバイル サービスの使用]」を完了する必要があります。
>
>このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合、Azure 評価版にサインアップして、最大 10 件の無料モバイル サービスを入手できます。このサービスは評価終了後も使用できます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure の無料評価版サイト</a>を参照してください。 
>
>以前の Visual Studio 2012 用 Windows Phone 8 チュートリアルも、「[Visual Studio 2012 用 Windows Phone 8 チュートリアル]」で引き続き利用可能です。


このチュートリアルでは、次の基本的な手順について説明します。

1. [オフライン機能をサポートするようにアプリケーションを更新する]
2. [オフラインの状況でアプリケーションをテストする] 
3. [モバイル サービスに再接続するようにアプリケーションを更新する]
4. [モバイル サービスに接続されているアプリケーションをテストする]

このチュートリアルには、次のものが必要です。

* Windows 8.1 で実行されている Visual Studio 2013。
* 「[モバイル サービスの使用]」を完了している。
* [Azure Mobile Services SDK バージョン 1.3.0-beta2 (またはこれ以降)][Mobile Services SDK Nuget]
* [Azure Mobile Services SQLite Store バージョン 1.0.0-beta2 (またはこれ以降))][SQLite store nuget]
* SQLite for Windows 8.1

>[AZURE.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure の無料評価版サイト</a>を参照してください。 

## <a name="enable-offline-app"></a>オフライン機能をサポートするようにアプリケーションを更新する

Azure Mobile Services のオフライン機能を使用すると、Mobile Services がオフラインになっている状況でも、ローカル データベースとやり取りすることができます。アプリケーションでこれらの機能を使用するには、`MobileServiceClient.SyncContext` をローカル ストアに初期化します。その後、`IMobileServiceSyncTable` インターフェイスを使用してテーブルを参照します。このチュートリアルでは、ローカル ストアとして SQLite を使用します。

>[AZURE.NOTE]このセクションをスキップして、既にオフラインをサポートしているプロジェクト例を、Github の Mobile Services のサンプル リポジトリから取得することもできます。サポートが有効になっているプロジェクトは、「[Todo offline sample - Windows Universal (Todo オフライン サンプル - Windows Universal)]」にあります。


1. SQLite for Windows Runtime (Windows 8.1) および Windows Phone 8.1 をインストールします。 

    * **SQLite for Windows Runtime (Windows 8.1): **Windows 8.1 用 SQLite ランタイムは、リンク「[SQLite for Windows Runtime (Windows 8.1)]」からインストールできます。
    * **Windows Phone 8.1: **Windows Phone 8.1 用 SQLite ランタイムは、リンク「[SQLite for Windows Phone 8.1]」からインストールできます。

    >[AZURE.NOTE] Internet Explorer を使用している場合は、SQLite をインストールするためにこのリンクをクリックすると、.vsix を .zip ファイルとしてダウンロードするためのプロンプトが表示されることがあります。ファイルに .zip ではなく .vsix 拡張子を付けて、ハード ドライブ上の場所に保存します。エクスプローラーで .vsix ファイルをダブルクリックすると、インストールが実行されます。

2. Visual Studio で、「[モバイル サービスの使用]」チュートリアルで完了したプロジェクトを開きます。ソリューション エクスプローラーで、Windows 8.1 ランタイムおよび Windows Phone 8.1 のプラットフォーム プロジェクト用にある **[参照設定]** を右クリックし、**[Extensions]** セクションで、SQLite への参照を追加します。 

    ![][1]
    </br>**SQLite for Windows Runtime (Windows 8.1): **

    ![][11]
    </br>**Windows Phone 8.1**

3.  SQLite ランタイムでは、ビルド対象のプロジェクトのプロセッサ アーキテクチャを **[x86]**、**[x64]**、または **[ARM]** に変更する必要があります。**[任意の CPU]**  はサポートされません。Visual Studio のソリューション エクスプ ローラーで、最上部の [ソリューション] をクリックし、ドロップ ダウン ボックスに表示されているプロセッサ アーキテクチャを、テストするサポート対象の設定に変更します。

    ![][13]

4. Windows 8.1 ランタイムと Windows Phone 8.1 のプロジェクト用に  **WindowsAzure.MobileServices.SQLiteStore** NuGet パッケージをインストールします。

    * **Windows 8.1: **ソリューション エクスプローラーで、Windows 8.1 プロジェクトを右クリックし、**[Nuget パッケージの管理]** をクリックして NuGet パッケージ マネージャーを実行します。**SQLiteStore**  を見つけ、WindowsAzure.MobileServices.SQLiteStore パッケージをインストールします。
    * **Windows Phone 8.1: **Windows Phone 8.1 プロジェクトを右クリックし、**[Nuget パッケージの管理]**  をクリックして NuGet パッケージ マネージャーを実行します。**SQLiteStore**  を見つけ、WindowsAzure.MobileServices.SQLiteStore パッケージをインストールします。     

    >[WACOM.NOTE]インストールで以前のバージョンの SQLite への参照が作成された場合は、その重複した参照のみ削除することができます。 

    ![][2]

5. Visual Studio のソリューション エクスプローラーで、共有プロジェクトの MainPage.cs ファイルを開きます。次の using ステートメントをファイルの先頭に追加します。

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;

6. 共有ファイル、Mainpage.cs 内で、`todoTable` の宣言を、`MobileServicesClient.GetSyncTable()` を呼び出して初期化する `IMobileServicesSyncTable` 型の宣言に置き換えます。

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();


7. 共有ファイル、MainPage.cs で、`OnNavigatedTo` イベント ハンドラーを更新し、SQLite ストアを使用してクライアントの同期コンテキストを初期化するようにします。この SQLite ストアは、モバイル サービス テーブルのスキーマに一致するテーブルを使用して作成しますが、この中には、次の手順で追加する **Version** システム プロパティが含まれている必要があります。

        protected async override void OnNavigatedTo(NavigationEventArgs e)
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localsync.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }
            await RefreshTodoItems();
        }


8. Visual Studio のソリューション エクスプローラーの共有プロジェクトで、**DataModel** を展開し、TodoItem.cs を開きます。MobileServices 名前空間用に `using` ステートメントを追加します。これは Version システム プロパティのバージョン属性を解決するために必要です。

        using Microsoft.WindowsAzure.MobileServices;

      次に `TodoItem` クラスを更新し、次のようにこのクラスに **Version** システム プロパティが含まれるようにします。次に示すように、テーブル スキーマに **Version** システム プロパティが含まれ、オフライン機能がサポートされます。

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




9. 次に、Windows 8.1 および Windows Phone 8.1 の各プロジェクトのユーザー インターフェイスを更新して、ローカルのオフライン データベースと Azure でのモバイル サービス データベースとの間のオフライン同期操作をサポートするボタンが含まれるようにします。 

    * **Windows 8.1: **Visual Studio のソリューション エクスプローラーで、Windows 8.1 プロジェクトにある MainPage.xaml を開きます。**ButtonRefresh** という名前のボタンを見つけます。このボタン要素を、次に示すボタンのスタック パネルに置き換えます。 

            <StackPanel Orientation="Horizontal">
                <Button Margin="72,0,0,0" Height="44" Name="ButtonRefresh" Click="ButtonRefresh_Click">
                	Refresh
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPull" Click="ButtonPull_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Sync"/>
                    </StackPanel>
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPush" Click="ButtonPush_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
            </StackPanel>

    * **Windows Phone 8.1: **Visual Studio のソリューション エクスプローラーで、Windows Phone 8.1 プロジェクトにある MainPage.xaml を開きます。**ButtonRefresh** という名前のボタンを見つけます。このボタン要素を、次に示すボタンのスタック パネルに置き換えます。 

            <StackPanel Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2">
                <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Margin="10,0,0,0">
                    Refresh
                </Button>
                <Button Name="ButtonPull" Click="ButtonPull_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Sync"/>
                    </StackPanel>
                </Button>
                <Button Name="ButtonPush" Click="ButtonPush_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
        </StackPanel>
        


10. 共有ファイル MainPage.cs で、**[Push]** および **[Pull]** の各ボタンに対応するクリック イベント ハンドラーを追加します。その後、ファイルを保存します。 
        
    `MobileServicePushFailedException` は、プッシュとプルの両方の操作に発生する可能性があることに注意してください。リレーションシップを持つすべてのテーブルが同期していることを確認するために、プルの操作で内部的にプッシュが実行されることから、この例外はプルで発生する可能性があります。次のチュートリアル「[モバイル サービスのオフライン サポートでの競合を処理する]」では、これらの同期関連の例外の処理方法について説明します。

    オフライン データの同期で削除されたレコードの同期をサポートするには、[論理削除](/ja-jp/documentation/articles/mobile-services-using-soft-delete/)を有効にする必要があります。それ以外の場合、ローカル ストアでレコードを手動で削除するか、`IMobileServiceSyncTable::PurgeAsync()` を呼び出してローカル ストアを消去する必要があります。

    この例では、`PullAsync` メソッドの呼び出しにクエリを渡して増分同期をサポートします。同期の際にテーブル全体をプルしたくない場合にこのサポートは有効です。このシナリオでは、todoitems へのテキストの変更については考慮していません。完了済みの項目をプルして todolist から除外するためのマークを付けることのみが目的です。

        private async void ButtonPull_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Pull is in progress
            // and visibly show the action is in progress. 
            ButtonPull.Focus(FocusState.Programmatic);
            ButtonPull.IsEnabled = false;

            try
            {
                // In this example scenario we are demonstrating incremental sync. We only want to 
                // sync the uncomplete todoitems. If another client updated the text or any other 
                // field after completing the item, it doesn't concern us. So we pass a query to the 
                // PullAsync() method to pull uncomplete todoitems instead of the entire table.
                await todoTable.PullAsync(todoTable.Where(todoItem => todoItem.Complete == false));

                await RefreshTodoItems();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Internal Push operation during pull request failed because of sync errors: " +
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

            ButtonPull.IsEnabled = true; 
        }


        private async void ButtonPush_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Push is in progress
            // and visibly show the action is in progress. 
            ButtonPush.Focus(FocusState.Programmatic);
            ButtonPush.IsEnabled = false;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " + 
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Push failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Push again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }

            ButtonPush.IsEnabled = true;
        }

11. ソリューションをビルドし、いずれのプロジェクトでもビルドのエラーが発生していないことを確認します。


## <a name="test-offline-app"></a>オフラインの状況でアプリケーションをテストする

ここでは、オフラインの状況をシミュレートするために、モバイル サービスに対するアプリケーションの接続を解除します。その後、いくつかのデータ項目を追加します。これらはローカル ストア内に保持されます。

このセクションでは、アプリケーションをどのモバイル サービスにも接続してはならないことに注意してください。したがって、**Push** と **Pull** の各ボタンをテストすると、例外がスローされます。次のセクションでは、このクライアント アプリケーションをモバイル サービスに再度接続し、ストアをモバイル サービスのデータベースに同期する目的で、**Push** と **Pull** の各操作をテストします。


1. Visual Studio のソリューション エクスプローラーで、共有プロジェクトにある App.xaml.cs を開きます。URLとして **azure mobile.net** を **azure mobile.xxx** に置き換えることによって、**MobileServiceClient** の初期化を無効なアドレスに変更します。その後、ファイルを保存します。

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2. Visual Studio で、**F5** キーを押してアプリケーションをビルドして実行します。新しい todo 項目をいくつか入力し、それぞれについて **[保存]** をクリックします。モバイル サービスにプッシュされるまで、新しい todo 項目はローカル ストア内にのみ存在します。クライアント アプリケーションは、まるで作成、読み取り、更新、削除 (CRUD) 操作のすべてをサポートするモバイル サービスに接続されているかのように動作します。

    ![][4]

3. アプリケーションを終了し、再起動して、作成した新しい項目がローカル ストアに保存されていることを確認します。

## <a name="update-online-app"></a>モバイル サービスに再接続するようにアプリケーションを更新する

ここでは、アプリケーションをモバイル サービスに再接続します。これは、アプリケーションがオフライン状態から、モバイル サービスとのオンライン状態に移行したことをシミュレートします。


1. Visual Studio のソリューション エクスプローラーで、共有プロジェクトにある App.xaml.cs を開きます。URLとして **azure mobile.xxx** を **azure mobile.net** に置き換えることによって、**MobileServiceClient** の初期化を元の正しいアドレスに変更します。その後、ファイルを保存します。

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );


## <a name="test-online-app"></a>モバイル サービスに接続されているアプリケーションをテストする


ここではプッシュ操作とプル操作をテストし、ローカル ストアをモバイル サービス データベースに同期します。Windows ストア 8.1 クライアント、または Windows Phone 8.1 クライアントをテストすることができます。次のスクリーン ショットは、Windows ストア 8.1 クライアントを示しています。 

1. Visual Studio で、**F5** キーを押してアプリケーションをリビルドして実行します。アプリケーションは現在モバイル サービスに接続されていますが、オフラインの状況と同じ表示になっていることに注意してください。この原因は、このアプリケーションが常に、ローカル ストアを指している `IMobileServiceSyncTable` との組み合わせで動作していることにあります。

    ![][4]

2. Microsoft Azure 管理ポータルにログインし、モバイル サービスに対応するデータベースを参照します。開発中のサービスが、モバイル サービスとして JavaScript バックエンドを使用している場合は、モバイル サービスの **[データ]** タブからデータを参照できます。モバイル サービスとして .NET バックエンドを使用している場合は、SQL Azure 拡張機能内にあるデータベースに対応する **[管理]** ボタンをクリックして、テーブルに対してクエリを実行することができます。

    データベースとローカル ストアの間でデータがまだ同期されていないことを確認します。

    ![][6]

3. アプリケーションで、 **[Push]** ボタンをクリックします。これにより、アプリケーションは `MobileServiceClient.SyncContext.PushAsync` を呼び出します。このプッシュ操作により、モバイル サービス データベースはストアからデータを受信します。ただし、ローカル ストアは、モバイル サービス データベースから項目を受信しません。

    プッシュ操作は `IMobileServicesSyncTable` の代わりに、`MobileServiceClient.SyncContext` を実行し、同期コンテキストに関連付けられているすべてのテーブルに対して変更をプッシュします。これは、テーブル間にリレーションシップがある状況に対応することを目的としています。

    ![][7]

4. アプリケーションで、項目の横にあるチェック ボックスをクリックして、ローカル ストアで完了済みになるようにします。 

    ![][8]

5. 今度は、アプリケーションの **Pull** ボタンをクリックします。アプリケーションは、`IMobileServiceSyncTable.PullAsync()` と `RefreshTodoItems` を呼び出します。モバイル サービス データベースから取得したすべてのデータがローカル ストアにプルされ、アプリケーション内で表示されることに注意してください。ただし、ローカル ストア内のすべてのデータが既にモバイル サービス データベースに対してプッシュされていることに注意してください。これは、**プルは必ず、最初にプッシュを実行する**ことが原因です。これは、ローカル ストア内のリレーションシップを持つすべてのテーブルが同期していることを確認するためです。
 
    ![][9]

    ![][10] 
  

##まとめ

モバイル サービスのオフライン機能をサポートするために、`IMobileServiceSyncTable` インターフェイスを使用して、ローカル ストアにより `MobileServiceClient.SyncContext` を初期化しました。この場合は、ローカル ストアは、SQLite データベースでした。

モバイル サービスに対する通常の CRUD 操作は、まるでアプリケーションが接続されているかのように機能しますが、すべての操作はローカル ストアに対して実施されます。

ローカル ストアをサーバーと同期しようとする場合は、`IMobileServiceSyncTable.PullAsync` と `MobileServiceClient.SyncContext.PushAsync` の各メソッドを使用しました。

*    変更内容をサーバーにプッシュするために、`IMobileServiceSyncContext.PushAsync()` を呼び出しました。このメソッドは、すべてのテーブルに対して変更をプッシュするため、同期テーブルではなく `IMobileServicesSyncContext` のメンバーです。

    何らかの方法で (CRUD 操作により) ローカルで変更されたレコードだけが、サーバー宛てに送信されます。
   
*  サーバー上のテーブルからアプリケーションにデータをプルするために、`IMobileServiceSyncTable.PullAsync` を呼び出しました。

    プルは必ず、最初にプッシュを実行します。これは、ローカル ストア内のリレーションシップを持つすべてのテーブルが同期していることを確認するためです。

    増分同期をサポートするためのクエリを指定することを許可する、`PullAsync()` のオーバー ロードも存在します。クエリが渡されない場合、`PullAsync()` は対応するテーブル (またはクエリ) のすべての行をプルします。アプリケーションと同期する必要がある変更のみをフィルター処理するクエリを渡すことができます。


## 次のステップ

* [モバイル サービスのオフライン サポートでの競合を処理する]

<!-- Anchors. -->
[オフライン機能をサポートするようにアプリケーションを更新する]: #enable-offline-app
[オフラインの状況でアプリケーションをテストする]: #test-offline-app
[モバイル サービスに再接続するようにアプリケーションを更新する]: #update-online-app
[モバイル サービスに接続されているアプリケーションをテストする]: #test-online-app
[次のステップ]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[3]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitepcl-nuget.png
[4]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-offline-app-run1.png
[5]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run1.png
[6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
[9]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run3.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[モバイル サービスのオフライン サポートでの競合を処理する]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/ 
[Todo offline sample - Windows Universal (Todo オフライン サンプル - Windows Universal)]: http://go.microsoft.com/fwlink/?LinkId=394777
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
[Getting Started (概要)]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-get-started/
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953
[Visual Studio 2012 用 Windows Phone 8 チュートリアル]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-offline-data/


[Mobile Services SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-beta2
[Azure Mobile Services SQLiteStore 1.0.0-beta2]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-beta2
