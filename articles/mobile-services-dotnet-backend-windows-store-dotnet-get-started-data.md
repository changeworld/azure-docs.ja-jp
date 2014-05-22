<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-dotnet-vs2013" urlDisplayName="データの使用" pageTitle="データの使用 (Windows ストア) | モバイル デベロッパー センター" metaKeywords="" description="モバイル サービスを使用して Windows ストア アプリのデータを活用する方法について説明します。" metaCanonical="" services="" documentationCenter="Mobile" title="モバイル サービスでのデータの使用" authors="wesmc" solutions="" manager="" editor="" />



# モバイル サービスでのデータの使用

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Windows ストア C#" class="current">Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Windows ストア JavaScript">Windows ストア JavaScript</a>
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title=".NET バックエンド" class="current">.NET バックエンド</a> |
	<a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet/"  title="JavaScript バックエンド">JavaScript バックエンド</a>
</div>


このトピックでは、Azure のモバイル サービスを Windows ストア アプリのバックエンド データソースとして使用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションの Visual Studio 2013 プロジェクトをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合し、アプリケーションの実行時にデータに加えられた変更を表示します。

このチュートリアルで作成するモバイル サービスは、モバイル サービスの .NET ランタイムをサポートします。これによって、モバイル サービスでサーバー側のビジネス ロジックに .NET 言語と Visual Studio を使用できます。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「[JavaScript バックエンド バージョン]」を参照してください。

<div class="dev-callout"><b>注</b>
<p>このチュートリアルには、Visual Studio 2013 が必要です。</p>
</div>

このチュートリアルでは、次の基本的な手順について説明します。

1. [Windows ストア アプリ プロジェクトのダウンロード]
2. [新しいモバイル サービスを作成する]
3. [モバイル サービスをローカルにダウンロードする]
4. [Windows ストア アプリを更新して、モバイル サービスを使用する]
5. [ローカルでホストされているサービスに対して、Windows ストア アプリをテストする]
6. [モバイル サービスを Azure に発行する]
7. [Azure でホストされているサービスに対して、Windows ストア アプリをテストする]


<div class="dev-callout"><strong>注</strong><p> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

<h2><a name="download-app"></a><span class="short-header">プロジェクトのダウンロード</span>GetStartedWithData プロジェクトのダウンロード</h2>

このチュートリアルは、Visual Studio 2013 の Windows ストア アプリ プロジェクトの [GetStartedWithMobileServices アプリケーション][デベロッパー サンプル コード集のサイト]に基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービスのクイック スタートで生成したアプリケーションに類似しています。

1. C# バージョンの GetStartedWithMobileServices サンプル アプリケーションを[デベロッパー サンプル コード集のサイト]からダウンロードします。

   	![][1]

2. Visual Studio を右クリックし、**[管理者として実行]** をクリックして、管理特権で Visual Studio 2013 を実行します。

3. Visual Studio 2013 で、ダウンロードしたプロジェクトを開き、MainPage.xaml.cs ファイルを確認します。

   	追加された **TodoItem** オブジェクトはメモリ内の **ObservableCollection&lt;TodoItem&gt;** に格納されます。

4. **F5** キーを押してプロジェクトを再ビルドし、アプリケーションを開始します。

5. アプリケーションで、**[Insert a TodoItem]** に任意のテキストを入力し、**[Save]** をクリックします。

   	![][0]  

   	保存したテキストが、**[Query and update data]** の下の 2 番目の列に表示されます。

<h2><a name="create-service"></a><span class="short-header">新しいモバイル サービスを作成する</span>新しいモバイル サービスを作成する</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]



<h2><a name="download-the-service-locally"></a><span class="short-header">サービスをローカルにダウンロードする</span>モバイル サービス プロジェクトをダウンロードし、ソリューションに追加する</h2>

1. [Azure 管理ポータル]で、新しいモバイル サービスまたはそのクラウド アイコン タブをクリックして、[概要] ページに移動します。

    ![][2]

2. **Windows ストア** プラットフォームをクリックします。**[作業の開始]** セクションで **[既存の Windows ストア アプリに接続する]** を展開し、**[ダウンロード]** をクリックして、モバイル サービスの個人用のスタート プロジェクトをダウンロードします。

    ![][3]

3. **[作業の開始]** セクションの一番下の **[クラウドへのサービスの発行]** というステップまでスクロールします。下のスクリーン ショットに表示されるリンクをクリックして、ダウンロードしたモバイル サービスの発行プロファイル ファイルをダウンロードします。

    > [WACOM.NOTE]Azure アカウントに関連する機密性の高い情報が含まれているために、ファイルは安全な場所に保存します。このファイルは、このチュートリアルで後でモバイル サービスを発行した後、削除されます。

    ![][5]


4. ダウンロードした個人用のサービス スタート プロジェクトを解凍します。zip ファイルにあったフォルダーを Get Started with Data ソリューション ファイル (.sln) が置かれているのと同じ **C#** ディレクトリにコピーします。これにより、NuGet パッケージ マネージャーはすべてのパッケージの同期を簡単に保てるようになります。

    ![][26]

5. 次に Visual Studio のソリューション エクスプ ローラーで、Windows ストア アプリのデータの使用のためのソリューションを右クリックします。**[追加]** をクリックし、**[既存のプロジェクト]** をクリックします。

    ![][4]

6. [既存プロジェクトの追加] ダイアログで、**C#** ディレクトリに移動したモバイル サービス プロジェクト フォルダーに移動します。サービスのサブディレクトリの C# プロジェクト ファイル (.csproj) を選択します。**[開く]** をクリックして、プロジェクトをソリューションに追加します。

    ![][6]

7. Visual Studio のソリューション エクスプ ローラーで、追加したサービス プロジェクトを右クリックし、**[ビルド]** をクリックして、エラーなしでビルドされることを確認します。ビルド中に NuGet パッケージ マネージャーは、プロジェクトで参照されている一部の NuGet パッケージの復元が必要になる場合があります。

    ![][20]

8. サービス プロジェクトをもう一度右クリックします。今回は、**デバッグ** コンテキスト メニューの **[新しいインスタンスを開始]** をクリックします。

    ![][21]

    Visual Studio では、サービスの既定の Web ページが表示されます。既定の Web ページで **[今すぐ試す]** をクリックして、モバイル サービスのメソッドをテストできます。

    ![][22]

    Visual Studio によって、モバイル サービスは既定で IIS Express にローカルでホストされました。タスク バーの IIS Express のトレイ アイコンを右クリックして、これを確認できます。

    ![][23]




<h2><a name="update-app"></a><span class="short-header">Windows ストア アプリの更新</span>モバイル サービスを使用するための Windows ストア アプリの更新</h2>

このセクションでは、Windows ストア アプリを更新してモバイル サービスをアプリケーションのバックエンド サービスとして使用します。


1. Visual Studio のソリューション エクスプローラーで、Windows ストア アプリ プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

    ![][7]

2. [NuGet パッケージの管理] ダイアログで、オンライン パッケージ コレクションの **WindowsAzure.MobileServices** を検索し、クリックして Azure のモバイル サービス Nuget パッケージをインストールします。次に、ダイアログを閉じます。

    ![][8]

3. Azure 管理ポータルに戻り、**アプリケーションを接続してサービスにデータを保存する**というステップを検索します。**C#** 言語が選択されていることを確認します。`MobileServiceClient` 接続を作成するコード スニペットをコピーします。

    ![][9]

4. Visual Studio で App.xaml.cs を開きます。コード スニペットを `App` クラス定義の先頭に貼り付けます。また、そのファイルの先頭に次の `using` ステートメントを追加して、ファイルを保存します。

		using Microsoft.WindowsAzure.MobileServices;

    ![][10]


5. Visual Studio で MainPage.xaml.cs を開き、using ステートメントを追加します。

		using Microsoft.WindowsAzure.MobileServices;

6. Visual Studio の MainPage.xaml.cs ファイルで、`MainPage` クラス定義を次の定義に置き換え、ファイルを保存します。

    このコードはモバイル サービス SDK を使用して、アプリケーションがそのデータをローカルでメモリ内に保存する代わりにサービスによって提供されるテーブルに保存できるようにします。主な方法には、`InsertTodoItem`、`RefreshTodoItems`、および `UpdateCheckedTodoItem` の 3 つがあります。これら 3 つの方法では、データ コレクションを Azure のテーブルに非同期的に挿入、照会、および更新できます。

        public sealed partial class MainPage : Page
        {
            private MobileServiceCollection<TodoItem, TodoItem> items;
            private IMobileServiceTable<TodoItem> todoTable = 
                App.MobileService.GetTable<TodoItem>();            
            public MainPage()
            {
                this.InitializeComponent();
            }
            private async void InsertTodoItem(TodoItem todoItem)
            {
                await todoTable.InsertAsync(todoItem); 
                items.Add(todoItem);
            }
            private async void RefreshTodoItems()
            {
                items = await todoTable.ToCollectionAsync(); 
                ListItems.ItemsSource = items;
            }
            private async void UpdateCheckedTodoItem(TodoItem item)
            {
                await todoTable.UpdateAsync(item);      
            }
            private void ButtonRefresh_Click(object sender, RoutedEventArgs e)
            {
                RefreshTodoItems();
            }
            private void ButtonSave_Click(object sender, RoutedEventArgs e)
            {
                var todoItem = new TodoItem { Text = TextInput.Text };
                InsertTodoItem(todoItem);
            }
            private void CheckBoxComplete_Checked(object sender, RoutedEventArgs e)
            {
                CheckBox cb = (CheckBox)sender;
                TodoItem item = cb.DataContext as TodoItem;
                UpdateCheckedTodoItem(item);
            }
            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
                RefreshTodoItems();
            }
        }




<h2><a name="test-locally-hosted"></a><span class="short-header">Windows ストア アプリをローカルでテストする</span>Windows ストア アプリをローカルにホストされているサービスでテストする</h2>

このセクションでは Visual Studio を使用して IIS Express の開発ワークステーションでモバイル サービスをローカルでホストします。次に、アプリケーションとバックエンド サービスをテストします。


1. Visual Studio で、F7 キーを押すか、または [ビルド] メニューの **[ビルド ソリューション]** をクリックして、Windows ストア アプリとモバイル サービスの両方をビルドします。両方のプロジェクトが Visual Studio の出力ウィンドウにエラーが表示されることなくビルドされることを確認します。

    ![][11]

2. Visual Studio で F5 キーを押すか、または [デバッグ] メニューの **[デバッグ開始]** をクリックしてアプリケーションを実行し、IIS Express でモバイル サービスをローカルでホストします。


3. 新しい todoitem のテキストを入力します。その後、**[保存]** をクリックします。これによって、IIS Express でローカルにホストされているモバイル サービスで作成されたデータベースに新しい todoItem が挿入されます。

    ![][13]

4. 項目のうちの 1 つのチェックボックスをオンにして、完了マークを付けます。

    ![][15]

5. Visual Studio で、サーバー エクスプ ローラーを開き、データ接続を展開して、バックエンド サービスに作成されたデータベースの変更を表示できます。**MS_TableConnectionString** で TodoItems テーブルを右クリックして、**[テーブル データの表示]** をクリックします。

    ![][14]



<h2><a name="publish-mobile-service"></a><span class="short-header">モバイル サービスを Azure に発行する</span>モバイル サービスを Azure に発行する</h2>


[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


<h2><a name="test-azure-hosted"></a><span class="short-header">Azure でモバイル サービスをテストする</span>Azure に発行されたモバイル サービスをテストする</h2>

1. Visual Studio で App.xaml.cs を開きます。ローカルでホストされているモバイル サービスに接続する `MobileServiceClient` を作成するコードをコメント アウトします。Azure のサービスに接続する `MobileServiceClient` を作成するコードをコメント解除します。ファイルの変更内容を保存します。

        sealed partial class App : Application
        {
            //public static MobileServiceClient MobileService = new MobileServiceClient(
            //          "http://localhost:59226"
            //);
            // Use this constructor instead after publishing to the cloud
            public static MobileServiceClient MobileService = new MobileServiceClient(
                 "https://todolist.preview.azure-mobile-preview.net/",
                 "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
            );        
            ....


2. Visual Studio で F5 キーを押すか、または [デバッグ] メニューの **[デバッグ開始]** をクリックします。これにより、Azure でリモートでホストされるモバイル サービスに接続するためにアプリケーションを実行する前に、Windows ストア アプリは前の変更で再ビルドされます。


3. 新しい todoitems をいくつか入力し、それぞれについて **[保存]** をクリックします。チェック ボックスをオンにして、いくつかの新しい項目を完了します。それぞれの新しい todoItem は、Azure 管理ポータルでモバイル サービスに以前に構成した SQL データベースで保存および更新されます。

    ![][16]

    アプリケーションを再起動して、変更内容が Azure のデータベースに保持されたことを確認できます。また、Azure 管理ポータルまたは Visual Studio の SQL Server オブジェクト エクスプ ローラーを使用して、データベースを確認することもできます。次の 2 つのステップでは、Azure 管理ポータルを使用してデータベースの変更を表示します。


4. Azure 管理ポータルで、モバイル サービスに関連付けられたデータベースの [管理] をクリックします。

    ![][17]

5. 管理ポータルで、クエリを実行して Windows ストア アプリによって加えられた変更を表示します。クエリは次のようになりますが、`todolist` の代わりにデータベースの名前を使用します。

        SELECT * FROM [todolist].[todoitems]

    ![][18]

これで、**データの使用**に関するチュートリアルはおしまいです。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

* [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]
  <br/>モバイル サービスでサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* [ページングを使用したモバイル サービス クエリの改善]
  <br/>クエリ内でページングを使用して、単一の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示す他のチュートリアルのいずれかを行うことをお勧めします。

* [認証の使用]
  <br/>アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [モバイル サービス .NET の使用方法の概念リファレンス]
  <br/>.NET でモバイル サービスを使用する方法について説明します
  
<!-- Anchors. -->

[Windows ストア アプリ プロジェクトのダウンロード]: #download-app
[新しいモバイル サービスを作成する]: #create-service
[モバイル サービスをローカルにダウンロードする]: #download-the-service-locally
[Windows ストア アプリを更新して、モバイル サービスを使用する]: #update-app
[ローカルでホストされているサービスに対して、Windows ストア アプリをテストする]: #test-locally-hosted
[モバイル サービスを Azure に発行する]: #publish-mobile-service
[Azure でホストされているサービスに対して、Windows ストア アプリをテストする]: #test-azure-hosted
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-data-sample-download-dotnet-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-run-solution.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/new-local-todoitem.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-show-local-table-data.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/local-item-checked.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png

[20]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-service-and-packages-folder.png


<!-- URLs. -->
[サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-dotnet
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/get-started-with-data-js
[JavaScript バックエンド バージョン]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet

[Azure 管理ポータル]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[デベロッパー サンプル コード集のサイト]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library
[MobileServiceClient クラス]: http://go.microsoft.com/fwlink/p/?LinkId=302030

