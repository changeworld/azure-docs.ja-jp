<properties linkid="develop-mobile-tutorials-handle-conflcits-offline-data-dotnet" urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Handle Conflicts with offline data in Mobile Services (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services handle conflicts when syncing offline data in your Windows phone application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling conflicts with offline data in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# モバイル サービスでのオフライン データの同期との競合の処理

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="Windows ストア C#">Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data" title="Windows Phone" class="current">Windows Phone</a>
</div>

このトピックでは、Azure Mobile Services のオフライン機能を使用しているときに、データを同期し、競合を処理する方法について説明します。このチュートリアルでは、オフライン データとオンライン データの両方をサポートするアプリケーションをダウンロードして、モバイル サービスをそのアプリケーションに統合します。その後に、Azure の管理ポータルにログインして、アプリケーションの実行中にデータベースを表示および更新します。

このチュートリアルは、前の「[オフライン データの使用][]」チュートリアルの手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、「[オフライン データの使用][]」チュートリアルを完了している必要があります。

このチュートリアルでは、次の基本的な手順について説明します。

1.  [Windows Phone プロジェクトのダウンロード][]
2.  [期日列のデータベースへの追加][]

-   [.NET バックエンド モバイル サービスのデータベースの更新][]
-   [JavaScript モバイル サービスのデータベースの更新][]

1.  [モバイル サービスに対するアプリケーションのテスト][]
2.  [競合を作成するためのバックエンドでのデータの手動更新][]

このチュートリアルには、Visual Studio 2012 および [Windows Phone 8 SDK][] が必要です。

## <a name="download-app"></a>サンプル プロジェクトのダウンロード

このチュートリアルは、Visual Studio 2012 の Windows Phone 8 プロジェクトである、「[競合コード サンプルの処理][]」に基づいて構築されています。
このアプリケーションの UI は、各 TodoItem に新しいデータ列があることを除き、「[オフライン データの使用][]」チュートリアルのアプリケーションと似ています。

![][]

1.  「[競合コード サンプルの処理][]」の Windows Phone バージョンをダウンロードします。

2.  インストールされていない場合は、[SQLite for Windows Phone 8][] をインストールします。

3.  Visual Studio 2012 で、ダウンロードしたプロジェクトを開きます。**[Windows Phone]**\>**[Extensions]** で、**SQLite for Windows Phone** への参照を追加します。

4.  Visual Studio 2012 で、**F5** キーを押してアプリケーションを構築し、デバッガーで実行します。

5.  アプリケーションで、新しい todo 項目にテキストを入力し、**[保存]** をクリックします。追加する todo 項目の期日を変更することもできます。

アプリケーションはモバイル サービスにまだ接続されていないため、**[プッシュ]** および **[プル]** は例外をスローすることに注意してください。

## <a name="add-column"></a>データ モデルへの列の追加

このセクションでは、モバイル サービスのデータベースを更新して、期日列のある TodoItem テーブルを含めます。このアプリケーションによって、このチュートリアルの後のセクションで同期の競合を生成できるように実行時に項目の期日を変更できます。

`TodoItem` クラスはサンプルで使用されていて、MainPage.xaml.cs で定義されています。このクラスは、そのテーブルに対する同期操作を対象とする次の属性を持っていることに注意してください。

        [DataTable("TodoWithDate")]

データベースを更新してこのテーブルを含めます。

### <a name="dotnet-backend"></a>.NET バックエンド モバイル サービスのデータベースの更新

モバイル サービスに .NET バックエンドを使用している場合、データベースのスキーマを更新するには、次の手順に従います。

1.  Visual Studio で、.NET バックエンド モバイル サービス プロジェクトを開きます。
2.  Visual Studio のソリューション エクスプローラーのサービス プロジェクトで、**Models** フォルダーを展開し、ToDoItem.cs を開きます。`DueDate` プロパティを次のように追加します。

          public class TodoItem : EntityData
          {
            public string Text { get; set; }
            public bool Complete { get; set; }
            public System.DateTime DueDate { get; set; }
          }

3.  Visual Studio のソリューション エクスプローラーで **App\_Start** フォルダーを展開し、WebApiConfig.cs ファイルを開きます。

    WebApiConfig.cs ファイルで、既定のデータベース初期化子クラスが `DropCreateDatabaseIfModelChanges` クラスから派生していることに注意してください。つまり、モデルへの変更により、テーブルが削除され、新しいモデルを格納するために再作成されることになります。したがって、テーブル内のデータは失われ、テーブルは再シードされます。次のように `Seed()` 初期化関数が新しい DueDate 列を初期化するように、データベース初期化子の Seed メソッドを変更します。WebApiConfig.cs ファイルを保存します。

    > [WACOM.NOTE] データベースの既定の初期化子を使用する場合は、Code First のモデル定義内でのデータ モデルの変更が検出されるたびに、Entity Framework がデータベースを削除して再作成します。このようなデータ モデルの変更を行ってデータベース内で既存のデータを保持するには、Code First Migrations を使用する必要があります。詳細については、[Code First Migrations を使用してデータ モデルを更新する方法に関するページ][]を参照してください。

        new TodoItem { Id = "1", Text = "First item", Complete = false, DueDate = DateTime.Today },
        new TodoItem { Id = "2", Text = "Second item", Complete = false, DueDate = DateTime.Today },

4.  Visual Studio のソリューション エクスプローラーで、**Controllers** フォルダーを展開し、ToDoItemController.cs を開きます。`TodoItemController` クラスの名前を `TodoWithDateController` に変更します。この結果、テーブル操作のための REST エンドポイントが変更されます。

        public class TodoWithDateController : TableController<TodoItem>

5.  Visual Studio のソリューション エクスプ ローラーで、.NET バックエンド モバイル サービス プロジェクトを右クリックし、**[発行]** をクリックして変更を発行します。

### <a name="javascript-backend"></a>JavaScript バックエンド モバイル サービスのデータベースの更新

JavaScript バックエンド モバイル サービスでは、**TodoWithDate** という名前の新しいテーブルを追加します。JavaScript バックエンド モバイル サービスに **TodoWithDate** テーブルを追加するには、次の手順に従います。

1.  [Azure の管理ポータル][]にログインします。

2.  モバイル サービスの **[データ]** タブに移動します。

3.  ページの下部で、**[作成]** をクリックし、**TodoWithDate** という名前の新しいテーブルを作成します。

## <a name="test-app"></a>モバイル サービスに対するアプリケーションのテスト

次に、Mobile Services に対してアプリケーションをテストします。

1.  Azure の管理ポータルで、**[ダッシュボード]** タブのコマンド バーの **[キーの管理]** をクリックしてモバイル サービスのアプリケーション キーを検索します。**アプリケーション キー**をコピーします。

2.  Visual Studio のソリューション エクスプローラーで、クライアント サンプル プロジェクトの App.xaml.cs ファイルを開きます。**MobileServiceClient** の初期化を変更して、モバイル サービス URL およびアプリケーション キーを使用します。

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3.  Visual Studio で、**F5** キーを押してアプリケーションを構築して実行します。

4.  前と同様に、テキスト ボックスにテキストを入力し、**[保存]** をクリックして新しい todo 項目を保存します。これによってデータはローカルの同期テーブルには保存されますが、サーバーには保存されません。

    ![][0]

5.  データベースの現在の状態を表示するには、[Azure の管理ポータル][]にログインし、**[モバイル サービス]** をクリックして、目的のモバイル サービスをクリックします。

-   モバイル サービスの JavaScript バックエンドを使用している場合は、**[データ]** タブをクリックし、**TodoWithDate** テーブルをクリックします。アプリケーションからサーバーに変更をプッシュしていないため、**[参照]** をクリックして、テーブルがまだ空であることを確認します。

      ![][1]

-   モバイル サービスの .NET バックエンドを使用している場合は、**[構成]** タブをクリックし、SQL データベースをクリックします。画面の下部にある **[管理]** をクリックして、SQL Azure の管理ポータルにログインし、次のように SQL クエリを実行してデータベースを表示します。

            SELECT * FROM todolist.todowithdate

       ![][2]

1.  アプリケーションに戻り、**[プッシュ]** をクリックします。

2.  管理ポータルで、**TodoItem** テーブルの **[最新の情報に更新]** をクリックします。アプリケーションで入力したデータが表示されます。

    ![][1]

3.  **Emulator WVGA 512 MB** を稼働状態にして、次のセクションで実行します。ここでは、競合を生成するために 2 つのエミュレーターでアプリケーションを実行します。

## <a name="handle-conflict"></a>競合を作成するためのバックエンドでのデータの更新

現実のシナリオでは、1 つのアプリケーションがデータベースのレコードに更新をプッシュし、次に別のアプリケーションがそのレコードの使用していないバージョンを使用して同じレコードへプッシュしようとする場合に同期の競合が発生します。アプリケーションのインスタンスが更新されたレコードでプルせずに同じレコードを更新しようとすると、競合が発生し、アプリケーションで `MobileServicePreconditionFailedException` として捕捉されます。

このセクションでは、アプリケーションの 2 つのインスタンスを同時に実行して競合を生成します。

1.  **Emulator WVGA 512 MB** がまだ稼働および実行されていない場合、**Ctrl + F5** を押して再起動します。

2.  Visual Studio で、出力デバイスを **Emulator WVGA** に変更して、**F5** を押して新しいエミュレーターでアプリケーションの 2 つ目のインスタンスを実行します。

    ![][2]

3.  アプリケーションの 2 つ目のインスタンスで、**[プル]** をクリックして、ローカル ストアをモバイル サービス データベースに同期します。アプリケーションの両方のインスタンスが同じデータを持ちます。

    ![][3]

4.  アプリケーションの 2 つ目のインスタンスで、チェック ボックスをオンにして項目のうちの 1 つに入力し、**[プッシュ]** をクリックしてリモート データベースに変更をプッシュします。次のスクリーン ショットでは、「**Pick up James**」と入力され、ジェームズが既に迎えられていることが示されています。アプリケーションの 1 つ目のインスタンスには現在使用されていないレコードがあります。

    ![][4]

5.  アプリケーションの 1 つ目のインスタンスで、使用していないレコードの日付を変更するために **[プッシュ]** をクリックし、使用していないレコードでリモート データベースの更新を試みます。下のスクリーン ショットでは、ジェームズが **2014 年 5 月 10 日**に迎えられるようにスケジュール設定を試みています。

    ![][5]

6.  日付の変更をコミットするために **[プッシュ]** をクリックすると、競合が検出されたことがダイアログ ボックスに示されます。競合を解決する方法が求められます。いずれかのオプションを選択して、競合を解決します。

    下に示されているシナリオでは、ジェームズは既に迎えられています。そのため、**2014 年 5 月 10 日**に迎えるようにスケジュール設定する必要はありません。**[Use server version (サーバー バージョンの使用)]** オプションが選択されていると、アプリケーションの 1 つ目のインスタンスではそのレコードがサーバーのレコードで更新されます。

    ![][6]

## 同期の競合を処理するためのコードの確認

オフライン機能を設定して競合を検出するために、ローカル データベースとデータ転送オブジェクトの両方に version の列を含める必要があります。`TodoItem` クラスには、次のメンバーが含まれます。

        [Version]
        public string Version { get; set; }

列 `__version` は、`OnNavigatedTo()` メソッドのローカル データベース設定でも指定されます。

コードでオフラインの同期の競合を処理するには、`IMobileServiceSyncHandler` を実装するクラスを作成します。呼び出しのこの型のオブジェクトを `InitializeAsync` に渡します。

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

**MainPage.xaml.cs** の `SyncHandler` クラスは `IMobileServiceSyncHandler` を実装します。メソッド `ExecuteTableOperationAsync` は、各プッシュ操作がサーバーに送信されるときに呼び出されます。型 `MobileServicePreconditionFailedException` の例外がスローされると、項目のローカルとリモートのバージョン間で競合が発生していることを意味します。

ローカル項目を優先して競合を解決するには、操作を再試行します。競合が発生すると、ローカル項目のバージョンはサーバーのバージョンと一致するように更新されるため、操作をもう一度実行するとサーバーの変更はローカルの変更で上書きされます。

    await operation.ExecuteAsync(); 

サーバー項目を優先して競合を解決するには、`ExecuteTableOperationAsync` から戻ります。オブジェクトのローカル バージョンは破棄され、サーバーの値に置き換えられます。

プッシュ操作を停止する (ただし、キューに設定された変更は保持) には、`AbortPush()` メソッドを使用します。

    operation.AbortPush();

これによって現在のプッシュ操作は停止しますが、`AbortPush` が `ExecuteTableOperationAsync` から呼び出される場合は、現在の操作を含めて、すべての保留中の変更は保存されます。次回 `PushAsync()` が呼び出されると、これらの変更はサーバーに送信されます。

プッシュがキャンセルされると、`PushAsync` は `MobileServicePushFailedException` をスローし、例外プロパティ `PushResult.Status` の値は `MobileServicePushStatus.CancelledByOperation` となります。

<!-- Anchors. --> 
<!-- URLs -->

[Windows ストア C\#]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data "Windows ストア C#"
[Windows Phone]: /ja-jp/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data "Windows Phone"
[オフライン データの使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-offline-data
[Windows Phone プロジェクトのダウンロード]: #download-app
[期日列のデータベースへの追加]: #add-column
[.NET バックエンド モバイル サービスのデータベースの更新]: #dotnet-backend
[JavaScript モバイル サービスのデータベースの更新]: #javascript-backend
[モバイル サービスに対するアプリケーションのテスト]: #test-app
[競合を作成するためのバックエンドでのデータの手動更新]: #handle-conflict
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?linkid=268374
[競合コード サンプルの処理]: http://go.microsoft.com/fwlink/?LinkId=398257

[SQLite for Windows Phone 8]: http://go.microsoft.com/fwlink/?LinkId=397953
[Code First Migrations を使用してデータ モデルを更新する方法に関するページ]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations
[Azure の管理ポータル]: https://manage.windowsazure.com/

<!-- Images -->
[0]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-push1.png
[2]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/vs-emulator-wvga.png
[3]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-synced.png
[4]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-item-completed.png
[5]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-date-change.png
[6]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-conflict-detected.png
