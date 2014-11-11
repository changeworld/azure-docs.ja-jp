<properties linkid="mobile-services-dotnet-backend-windows-store-dotnet-get-started-data" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# モバイル サービスでのデータの使用

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
<a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title=".NET バックエンド" class="current">.NET バックエンド</a> | 
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/" title="JavaScript バックエンド">JavaScript バックエンド</a>
</div>

このトピックでは、Azure のモバイル サービスを Windows ストア アプリのバックエンド データソースとして使用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションの Visual Studio 2013 プロジェクトをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合し、アプリケーションの実行時にデータに加えられた変更を表示します。

このチュートリアルで作成するモバイル サービスは .NET バックエンド モバイル サービスです。.NET バックエンドによってモバイル サービスでサーバー側のビジネス ロジックに .NET 言語と Visual Studio を使用できます。また、ローカル コンピューター上でモバイル サービスを実行し、デバッグできます。JavaScript でサーバー側ビジネス ロジックを記述できるモバイル サービスを作成する方法については、このトピックの「JavaScript バックエンド バージョン」を参照してください。

> [WACOM.NOTE] このトピックでは、Azure モバイル サービスを Windows ストア プロジェクトに追加する方法を説明します。Visual Studio 2013 ツールを使用して同じ .NET バックエンド モバイル サービスをユニバーサル Windows アプリ プロジェクトに追加できます。詳細情報は、このチュートリアルの「[ユニバーサル Windows アプリ バージョン][ユニバーサル Windows アプリ バージョン]」を参照してください。

このチュートリアルでは、次の基本的な手順について説明します。

1.  [Windows ストア アプリ プロジェクトのダウンロード][Windows ストア アプリ プロジェクトのダウンロード]
2.  [新しいモバイル サービスを作成する][新しいモバイル サービスを作成する]
3.  [モバイル サービスをローカルにダウンロードする][モバイル サービスをローカルにダウンロードする]
4.  [Windows ストア アプリを更新して、モバイル サービスを使用する][Windows ストア アプリを更新して、モバイル サービスを使用する]
5.  [ローカルでホストされているサービスに対して、Windows ストア アプリをテストする][ローカルでホストされているサービスに対して、Windows ストア アプリをテストする]
6.  [モバイル サービスを Azure に発行する][モバイル サービスを Azure に発行する]
7.  [Azure でホストされているサービスに対して、Windows ストア アプリをテストする][Azure でホストされているサービスに対して、Windows ストア アプリをテストする]

このチュートリアルを完了するには、以下が必要です。

-   アクティブな Azure アカウントアカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。
-   [Visual Studio Professional 2013][Visual Studio Professional 2013]。無料評価版が利用できます。

## <a name="download-app"></a>GetStartedWithData プロジェクトをダウンロードする

このチュートリアルは、Visual Studio 2013 の Windows ストア アプリ プロジェクトの [GetStartedWithMobileServices アプリケーション][GetStartedWithMobileServices アプリケーション]に基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービスのクイック スタートで生成したアプリケーションに類似しています。

1.  C# バージョンの GetStartedWithMobileServices サンプル アプリケーションを[デベロッパー サンプル コード集のサイト][GetStartedWithMobileServices アプリケーション]からダウンロードします。

2.  Visual Studio を右クリックし、**[管理者として実行]** をクリックして、管理特権で Visual Studio 2013 を実行します。

3.  Visual Studio 2013 で、ダウンロードしたプロジェクトを開き、MainPage.xaml.cs ファイルを確認します。

    追加された **TodoItem** オブジェクトはメモリ内の **ObservableCollection\<TodoItem\>** に格納されます。

4.  **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

5.  アプリケーションで、**[Insert a TodoItem]** に任意のテキストを入力し、**[Save]** をクリックします。

   	![][0]

   	保存したテキストが、**[Query and update data]** の下の 2 番目の列に表示されます。

## <a name="create-service"></a> 新しいモバイル サービスを作成する

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service-locally"></a>モバイル サービス プロジェクトをダウンロードしてソリューションに追加する

1.  [Azure の管理ポータル][Azure の管理ポータル]で、新しいモバイル サービスまたはそのクラウド アイコン タブをクリックして、[概要] ページに移動します。

    ![][1]

2.  **Windows ストア** プラットフォームをクリックします。**[作業の開始]** セクションで **[既存の Windows ストア アプリに接続する]** を展開し、**[ダウンロード]** をクリックして、モバイル サービスの個人用のスタート プロジェクトをダウンロードします。

    ![][2]

3.  **[作業の開始]** セクションの一番下の **[クラウドへのサービスの発行]** というステップまでスクロールします。下のスクリーン ショットに表示されるリンクをクリックして、ダウンロードしたモバイル サービスの発行プロファイル ファイルをダウンロードします。

    > [WACOM.NOTE] Azure アカウントに関連する機密性の高い情報が含まれているために、ファイルは安全な場所に保存します。このファイルは、このチュートリアルで後でモバイル サービスを発行した後、削除されます。

    ![][3]

4.  ダウンロードした個人用のサービス スタート プロジェクトを解凍します。zip ファイルにあったフォルダーを Get Started with Data ソリューション ファイル (.sln) が置かれているのと同じ **C#** ディレクトリにコピーします。これにより、NuGet パッケージ マネージャーはすべてのパッケージの同期を簡単に保てるようになります。

    ![][4]

5.  次に Visual Studio のソリューション エクスプ ローラーで、Windows ストア アプリのデータの使用のためのソリューションを右クリックします。**[追加]** をクリックし、**[既存のプロジェクト]** をクリックします。

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-service-project-to-solution.png)

6.  [既存プロジェクトの追加] ダイアログで、**C#** ディレクトリに移動したモバイル サービス プロジェクト フォルダーに移動します。サービスのサブディレクトリの C# プロジェクト ファイル (.csproj) を選択します。**[開く]** をクリックして、プロジェクトをソリューションに追加します。

    ![][6]

7.  Visual Studio のソリューション エクスプ ローラーで、追加したサービス プロジェクトを右クリックし、**[ビルド]** をクリックして、エラーなしでビルドされることを確認します。ビルド中に NuGet パッケージ マネージャーは、プロジェクトで参照されている一部の NuGet パッケージの復元が必要になる場合があります。

    ![][7]

8.  サービス プロジェクトをもう一度右クリックします。今回は、**デバッグ** コンテキスト メニューの **[新しいインスタンスを開始]** をクリックします。

    ![][8]

    Visual Studio では、サービスの既定の Web ページが表示されます。既定の Web ページで **[今すぐ試す]** をクリックして、モバイル サービスのメソッドをテストできます。

    ![][9]

    Visual Studio によって、モバイル サービスは既定で IIS Express にローカルでホストされました。タスク バーの IIS Express のトレイ アイコンを右クリックして、これを確認できます。

    ![][10]

# <a name="update-app"></a>Windows ストア アプリを更新して、モバイル サービスを使用する

このセクションでは、Windows ストア アプリを更新してモバイル サービスをアプリケーションのバックエンド サービスとして使用します。

1.  Visual Studio のソリューション エクスプローラーで、Windows ストア アプリ プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

    ![][11]

2.  [NuGet パッケージの管理] ダイアログで、オンライン パッケージ コレクションの **WindowsAzure.MobileServices** を検索し、クリックして Azure のモバイル サービス Nuget パッケージをインストールします。次に、ダイアログを閉じます。

    ![][12]

3.  Azure の管理ポータルに戻り、「**アプリケーションを接続してサービスにデータを保存する**」というステップを検索します。**C#** 言語が選択されていることを確認します。`MobileServiceClient` 接続を作成するコード スニペットをコピーします。

    ![][13]

4.  Visual Studio で App.xaml.cs を開きます。コード スニペットを `App` クラス定義の先頭に貼り付けます。また、そのファイルの先頭に次の `using` ステートメントを追加して、ファイルを保存します。

        using Microsoft.WindowsAzure.MobileServices;

    ![][14]

5.  Visual Studio で MainPage.xaml.cs を開き、using ステートメントを追加します。

        using Microsoft.WindowsAzure.MobileServices;

6.  Visual Studio の MainPage.xaml.cs ファイルで、`MainPage` クラス定義を次の定義に置き換え、ファイルを保存します。

    このコードはモバイル サービス SDK を使用して、アプリケーションがそのデータをローカルでメモリ内に保存する代わりにサービスによって提供されるテーブルに保存できるようにします。主な方法には、`InsertTodoItem`、`RefreshTodoItems`、`UpdateCheckedTodoItem` の 3 つがあります。これら 3 つの方法では、データ コレクションを Azure のテーブルに非同期的に挿入、照会、および更新できます。

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

## <a name="test-locally-hosted"></a>ローカルでホストされているサービスで、Windows ストア アプリケーションをテストする

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-data](../includes/mobile-services-dotnet-backend-test-local-service-data.md)]

## <a name="publish-mobile-service"></a>モバイル サービスを Azure に発行する

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="test-azure-hosted"></a>Azure に発行したモバイル サービスをテストする

1.  Visual Studio で App.xaml.cs を開きます。ローカルでホストされているモバイル サービスに接続する `MobileServiceClient` を作成するコードをコメント アウトします。Azure のサービスに接続する `MobileServiceClient` を作成するコードをコメント解除します。ファイルの変更内容を保存します。

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

2.  Visual Studio で F5 キーを押すか、[デバッグ] メニューの **[デバッグ開始]** をクリックします。これにより、Azure でリモートでホストされるモバイル サービスに接続するためにアプリケーションを実行する前に、Windows ストア アプリは前の変更で再ビルドされます。

3.  新しい todoitems をいくつか入力し、それぞれについて **[保存]** をクリックします。チェック ボックスをオンにして、いくつかの新しい項目を完了します。それぞれの新しい todoItem は、Azure 管理ポータルでモバイル サービスに以前に構成した SQL データベースで保存および更新されます。

    ![][15]

    アプリケーションを再起動して、変更内容が Azure のデータベースに保持されたことを確認できます。

## <a name="view-stored-data"></a>SQL データベースに保存したデータを表示する

[WACOM.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../includes/mobile-services-dotnet-backend-view-sql-data.md)]

これで、**データの使用**に関するチュートリアルはおしまいです。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

-   [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更][サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]
    Mobile Services でサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

-   [ページングを使用したモバイル サービス クエリの改善][ページングを使用したモバイル サービス クエリの改善]
    クエリ内でページングを使用して、1 回の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示す他のチュートリアルのいずれかを行うことをお勧めします。

-   [認証の使用][認証の使用]
    アプリケーションのユーザーを認証する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]
    アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

-   [モバイル サービス .NET の使用方法の概念リファレンス][モバイル サービス .NET の使用方法の概念リファレンス]
    .NET で Mobile Services を使用する方法について説明します。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [.NET バックエンド]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/ ".NET バックエンド"
  [JavaScript バックエンド]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/ "JavaScript バックエンド"
  [ユニバーサル Windows アプリ バージョン]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data
  [Windows ストア アプリ プロジェクトのダウンロード]: #download-app
  [新しいモバイル サービスを作成する]: #create-service
  [モバイル サービスをローカルにダウンロードする]: #download-the-service-locally
  [Windows ストア アプリを更新して、モバイル サービスを使用する]: #update-app
  [ローカルでホストされているサービスに対して、Windows ストア アプリをテストする]: #test-locally-hosted
  [モバイル サービスを Azure に発行する]: #publish-mobile-service
  [Azure でホストされているサービスに対して、Windows ストア アプリをテストする]: #test-azure-hosted
  [Azure の無料評価版サイト]: http://azure.microsoft.com/ja-jp/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-jp%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [GetStartedWithMobileServices アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=328660
  [0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/app-view.png
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-service-overview-page.png
  [2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-service-project.png
  [3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-publishing-profile.png
  [4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-service-and-packages-folder.png
  [6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-existing-project-dialog.png
  [7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-service-project.png
  [8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-start-debug-service-project.png
  [9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/service-welcome-page.png
  [10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/iis-express-tray.png
  [11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-manage-nuget-packages.png
  [12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-nuget-packages.png
  [13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-mobileserviceclient-snippet.png
  [14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-pasted-mobileserviceclient.png
  [15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/azure-items.png
  [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-dotnet
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
  [プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
