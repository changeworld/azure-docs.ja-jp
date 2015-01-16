<properties urlDisplayName="Get Started with Data" pageTitle="データの使用 (Windows Phone) |モバイル デベロッパー センター" metaKeywords="" description="Mobile Services を使用して Windows Phone アプリでデータを活用する方法について説明します。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# 既存のアプリケーションへの Mobile Services の追加

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

このトピックでは、Azure Mobile Services をWindows Phone 8.0 または Windows Phone 8.1 Silverlight アプリのバックエンド データ ソースとして使用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションの Visual Studio プロジェクトをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合し、アプリケーションの実行時にデータに加えられた変更を表示します。

このチュートリアルで作成するモバイル サービスは、Mobile Services の .NET ランタイムをサポートします。これによって、モバイル サービスでサーバー側のビジネス ロジックに .NET 言語と Visual Studio を使用できます。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの [JavaScript バックエンド バージョン]を参照してください。


このチュートリアルでは、次の基本的な手順について説明します。

1. [Windows Phone 8 アプリ プロジェクトのダウンロード]
2. [新しいモバイル サービスを作成する]
3. [モバイル サービスをローカルにダウンロードする]
4. [Windows Phone アプリを更新して、モバイル サービスを使用する]
5. [ローカルでホストされているサービスに対して、Windows Phone アプリをテストする]
6. [モバイル サービスを Azure に発行する]
7. [Azure でホストされているサービスに対して、Windows Phone アプリをテストする]

このチュートリアルには、次のものが必要です。

+  Windows 8 上で動作する Visual Studio 2013 および [Windows Phone 8 SDK]。このチュートリアルに従って Windows Phone 8.1 Silverlight アプリを作成するには、Visual Studio 2013 Update 2 以降が必要です。
+ Microsoft Azure アカウント。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div> 

##<a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

このチュートリアルは、Windows Phone Silverlight 8 アプリケーション プロジェクトである[ GetStartedWithMobileServices アプリ][デベロッパー サンプル コード集のサイト]に基づいています。  

1. C# バージョンの GetStartedWithMobileServices サンプル アプリケーションを[デベロッパー サンプル コード集のサイト]からダウンロードします。 

   	![][1]

	>[WACOM.NOTE]Windows Phone Silverlght 8.1 アプリケーションを作成するには、ダウンロードした Windows Phone Silverlight 8 アプリケーション プロジェクトの対象 OS を Windows Phone 8.1 に変更します。Windows Phone Store アプリケーションを作成するには、GetStartedWithData サンプル アプリケーション プロジェクトの [Windows Phone Store アプリケーション バージョン](http://go.microsoft.com/fwlink/p/?LinkId=397372)をダウンロードします。

2. Visual Studio を右クリックし、**[管理者として実行]** をクリックして、管理特権で Visual Studio を実行します。

3. Visual Studio で、ダウンロードしたプロジェクトを開き、MainPage.xaml.cs ファイルを確認します。

   	追加された **TodoItem** オブジェクトはメモリ内の **ObservableCollection<TodoItem>** に格納されます。

4. Visual Studio で、アプリケーションの展開ターゲットを選択します。Windows Phone デバイスまたは Windows Phone SDK に付属するエミュレーターに展開できます。このチュートリアルでは、エミュレーターへの展開方法を示します。

    ![][19]

5. **F5** キーを押します。これにより、デバッグ用にアプリケーションがビルド、展開、開始されます。

6. アプリケーションで、テキスト ボックスにテキストを入力し、**[保存]** をクリックしていくつかの項目をアプリケーションのメモリ内に保存します。

   	![][0]  

   	更新ボタンの下に、各 `TodoItem` のテキストが、項目に完了マークを付けるチェック ボックスと並んで表示されます。

<h2><a name="create-service"></a>新しいモバイル サービスを作成する</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]


<h2><a name="download-the-service-locally"></a>モバイル サービス プロジェクトをダウンロードしてソリューションに追加する</h2>

1. [Azure の管理ポータル]で、新しいモバイル サービスまたはそのクラウド アイコン タブをクリックして、[概要] ページに移動します。

    ![][2]

2. **Windows Phone 8** プラットフォームをクリックします。**[作業の開始]** セクションで **[既存の Windows Phone 8 アプリに接続する]** を展開し、**[ダウンロード]** をクリックして、モバイル サービスの個人用のスタート プロジェクトをダウンロードします。 

    ![][3]

3. 同じくそのセクションで、下のスクリーン ショットに示されたリンクをクリックして、ダウンロードしたモバイル サービスの発行プロファイル ファイルをダウンロードします。 

    > [WACOM.NOTE] Azure アカウントに関連する機密性の高い情報が含まれているために、ファイルは安全な場所に保存します。このファイルは、このチュートリアルで後でモバイル サービスを発行した後、削除されます。 

    ![][5]


4. ダウンロードした個人用のサービス スタート プロジェクトを解凍します。zip ファイルにあったフォルダーを Get Started with Data ソリューション ファイル (.sln) が置かれているのと同じ **C#** ディレクトリにコピーします。これにより、NuGet パッケージ マネージャーはすべてのパッケージの同期を簡単に保てるようになります。

    ![][26]


5. Visual Studio のソリューション エクスプ ローラーで、Windows ストア アプリのデータの使用のためのソリューションを右クリックします。**[追加]**、**[既存のプロジェクト]** の順にクリックします。

    ![][4]

6. [既存プロジェクトの追加] ダイアログで、**C#** ディレクトリに移動したモバイル サービス プロジェクト フォルダーに移動します。サービスのサブディレクトリの C# プロジェクト ファイル (.csproj) を選択します。**[開く]** をクリックして、プロジェクトをソリューションに追加します。

    ![][6]

7. Visual Studio のソリューション エクスプ ローラーで、追加したサービス プロジェクトを右クリックし、**[ビルド]** をクリックして、エラーなしでビルドされることを確認します。ビルド中に NuGet パッケージ マネージャーは、プロジェクトで参照されている一部の NuGet パッケージの復元が必要になる場合があります。

    ![][20]

8. サービス プロジェクトをもう一度右クリックします。今回は、**[デバッグ]** コンテキスト メニューの **[新しいインスタンスを開始]** をクリックします。

    ![][21]

    Visual Studio によってサービスの既定の Web ページが表示されます。既定の Web ページで **[今すぐ試す]** をクリックして、モバイル サービスのメソッドをテストできます。

    ![][22]

    Visual Studio によって、モバイル サービスは既定で IIS Express にローカルでホストされました。タスク バーの IIS Express のトレイ アイコンを右クリックして、これを確認できます。

    ![][23]


##<a name="update-app"></a>Windows Phone アプリを更新して、モバイル サービスを使用する

このセクションでは、Windows Phone アプリを更新してモバイル サービスをアプリケーションのバックエンド サービスとして使用します。


1. Visual Studio のソリューション エクスプローラーで、Windows Phone アプリ プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

    ![][7]

2. [NuGet パッケージの管理] ダイアログで、オンライン パッケージ コレクションの **WindowsAzure.MobileServices** を検索し、クリックして Azure のモバイル サービス Nuget パッケージをインストールします。次に、ダイアログを閉じます。

    ![][8]

3. Azure 管理ポータルに戻り、**アプリケーションを接続してサービスにデータを保存する**というステップを検索します。`MobileServiceClient` 接続を作成するコード スニペットをコピーします。

    ![][9]

4. Visual Studio で App.xaml.cs を開きます。コード スニペットを `App` クラス定義の先頭に貼り付けます。また、そのファイルの先頭に次の `using` ステートメントを追加して、ファイルを保存します。

		using Microsoft.WindowsAzure.MobileServices;

    ![][10]


5. Visual Studio で MainPage.xaml.cs を開き、ファイルの先頭に using ステートメントを追加します。 

		using Microsoft.WindowsAzure.MobileServices;

6. Visual Studio の MainPage.xaml.cs ファイルで、`MainPage` クラス定義を次の定義に置き換え、ファイルを保存します。

    このコードはモバイル サービス SDK を使用して、アプリケーションがそのデータをローカルでメモリ内に保存する代わりにサービスによって提供されるテーブルに保存できるようにします。主な方法には、`InsertTodoItem`、`RefreshTodoItems`、および `UpdateCheckedTodoItem` の 3 つがあります。 これら 3 つの方法では、データ コレクションを Azure のテーブルに非同期的に挿入、照会、および更新できます。

        public sealed partial class MainPage : PhoneApplicationPage
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
                items = await todoTable 
                    .ToCollectionAsync(); 
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
                var todoItem = new TodoItem { Text = InputText.Text };
                InsertTodoItem(todoItem);
            }
            private void CheckBoxComplete_Checked(object sender, RoutedEventArgs e)
            {
                CheckBox cb = (CheckBox)sender;
                TodoItem item = cb.DataContext as TodoItem;
                item.Complete = (bool)cb.IsChecked;
                UpdateCheckedTodoItem(item);
            }
            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
                RefreshTodoItems();
            }
        }




##<a name="test-locally-hosted"></a>ローカルでホストされているサービスで、Windows Phone アプリケーションをテストする</h2>

このセクションでは Visual Studio を使用して開発用コンピューターでアプリケーションとモバイル サービスをローカルでテストします。IIS Express でローカルにホストされているモバイル サービスを Windows Phone デバイスまたは Windows Phone エミュレーターからテストするには、コンピューターの IP アドレスとポートへの接続を許可するように IIS Express とコンピューターを構成する必要があります。Windows Phone デバイスとエミュレーターは、非ローカル ネットワーク クライアントとして接続します。

#### リモート接続を許可するように IIS Express を構成する

[WACOM.INCLUDE [mobile-services-how-to-configure-iis-express](../includes/mobile-services-how-to-configure-iis-express.md)]

#### IIS Express でモバイル サービスに対してアプリケーションをテストする

6. Visual Studio で App.xaml.cs ファイルを開き、最近ファイルに貼り付けた `MobileService` 定義をコメント アウトします。コンピューターで構成した IP アドレスとポートに基づいて接続するように、新しい定義を追加します。その後、ファイルを保存します。コードは次のようになります。

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "http://192.168.111.11:54321");

        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "https://todolist.azure-mobile.net/",
        //    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
        //);        


7. Visual Studio で、F7 キーを押すか、または [ビルド] メニューの **[ビルド ソリューション]** をクリックして、Windows Phone アプリとモバイル サービスの両方をビルドします。両方のプロジェクトが Visual Studio の出力ウィンドウにエラーが表示されることなくビルドされることを確認します。

    ![][11]

8. Visual Studio で F5 キーを押すか、または [デバッグ] メニューの **[デバッグ開始]** をクリックしてアプリケーションを実行し、IIS Express でモバイル サービスをローカルでホストします。 

    >[WACOM.NOTE]  **[管理者として実行]** オプションを使用して Visual Studio を実行したことを確認します。それ以外の場合、IIS Express が applicationhost.config の変更内容を読み込まない可能性があります。

    ![][12]


9. 新しい todoitem のテキストを入力します。その後、**[保存]** をクリックします。これによって、IIS Express でローカルにホストされているモバイル サービスで作成されたデータベースに新しい todoItem が挿入されます。項目のうちの 1 つのチェックボックスをオンにして、完了マークを付けます。

    ![][15]

10. Visual Studio でアプリケーションのデバッグを終了します。サーバー エクスプ ローラーを開き、データ接続を展開して、バックエンド サービスに作成されたデータベースの変更を表示できます。**MS_TableConnectionString** の TodoItems テーブルを右クリックし、**[テーブル データの表示]** をクリックします。

    ![][14]

11. ローカルにホストされたモバイル サービスのテストが完了したら、コンピューターのポートを開くために作成した Windows ファイアウォール ルールを削除します。


##<a name="publish-mobile-service"></a>モバイル サービスを Azure に発行する

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="test-azure-hosted"></a>Azure に発行したモバイル サービスをテストする

1. Visual Studio で App.xaml.cs を開きます。ローカルでホストされているモバイル サービスに接続する `MobileServiceClient` を作成するコードをコメント アウトします。Azure のサービスに接続する `MobileServiceClient` を作成するコードをコメント解除します。ファイルの変更内容を保存します。

        sealed partial class App : Application
        {
            //public static MobileServiceClient MobileService = new MobileServiceClient(
            //          "http://192.168.111.11:54321");

            // Use this constructor instead after publishing to the cloud
            public static MobileServiceClient MobileService = new MobileServiceClient(
                 "https://todolist.azure-mobile.net/",
                 "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
            );        
            ....

2. Visual Studio で F5 キーを押すか、または [デバッグ] メニューの **[デバッグ開始]** をクリックします。これにより、Azure でリモートでホストされるモバイル サービスに接続するためにアプリケーションを実行する前に、アプリケーションは前の変更で再ビルドされます。 

    ![][12]

3. 新しい todoitems をいくつか入力し、それぞれについて **[保存]** をクリックします。チェック ボックスをオンにして、いくつかの新しい項目を完了します。それぞれの新しい todoItem は、Azure の管理ポータルでモバイル サービスに以前に構成した SQL データベースで保存および更新されます。 

    ![][16]

    アプリケーションを再起動して、変更内容が Azure のデータベースに保持されたことを確認できます。また、Azure 管理ポータルまたは Visual Studio の SQL Server オブジェクト エクスプ ローラーを使用して、データベースを確認することもできます。次の 2 つのステップでは、Azure の管理ポータルを使用してデータベースの変更を表示します。


4. Azure の管理ポータルで、モバイル サービスに関連付けられたデータベースの [管理] をクリックします。

    ![][17]

5. 管理ポータルでクエリを実行して、アプリケーションによって加えられた変更を表示します。クエリは次のようになりますが、`todolist` の代わりにデータベースの名前を使用します。

        SELECT * FROM [todolist].[todoitems]

    ![][18]

これで、**データの使用**に関するチュートリアルはおしまいです。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、.NET ランタイムを使用してビルドされたモバイル サービスのデータを Windows Phone 8 アプリで操作できるようにするための基本について説明しました。次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

* [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]
  <br/>モバイル サービスでサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* [ページングを使用したモバイル サービス クエリの改善]
  <br/>クエリ内でページングを使用して、単一の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示す他のチュートリアルのいずれかを行うことをお勧めします。

* [認証の使用]
  <br/>アプリケーションのユーザーを認証する方法について説明します。

<!--
* [Get started with push notifications] 
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。
-->

* [モバイル サービス .NET の使用方法の概念リファレンス]
  <br/>.NET で Mobile Services を使用する方法について説明します。
  
<!-- Anchors. -->

[Windows Phone 8 アプリ プロジェクトのダウンロード]: #download-app
[新しいモバイル サービスを作成する]: #create-service
[モバイル サービスをローカルにダウンロードする]: #download-the-service-locally
[Windows Phone アプリを更新して、モバイル サービスを使用する]: #update-app
[ローカルでホストされているサービスに対して、Windows Phone アプリをテストする]: #test-locally-hosted
[モバイル サービスを Azure に発行する]: #publish-mobile-service
[Azure でホストされているサービスに対して、Windows Phone アプリをテストする]: #test-azure-hosted
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/mobile-data-sample-download-wp8-vs12.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-run-solution.png
[13]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/new-local-todoitem.png
[14]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-show-local-table-data.png
[15]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/local-item-checked.png
[16]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/sql-azure-query.png
[19]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-deployment-target.png
[20]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/iis-express-tray.png
[26]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/copy-service-and-packages-folder.png



<!-- URLs. -->
[サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-wp8
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/get-started-with-data-js
[JavaScript バックエンド バージョン]: /ja-jp/develop/mobile/tutorials/get-started-with-data-wp8

[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?linkid=268374
[Azure 管理ポータル]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[デベロッパー サンプル コード集のサイト]:  http://go.microsoft.com/fwlink/p/?linkid=271146
[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library
[MobileServiceClient クラス]: http://go.microsoft.com/fwlink/p/?LinkId=302030
[ローカル コンピューターにポート規則を作成するには]:  http://go.microsoft.com/fwlink/?LinkId=392240
  
