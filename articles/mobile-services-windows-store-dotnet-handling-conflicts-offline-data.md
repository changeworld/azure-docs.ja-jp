

<properties linkid="develop-mobile-tutorials-handle-conflcits-offline-data-dotnet" urlDisplayName="オフライン データとの競合の処理" pageTitle="Mobile Services のオフライン データとの競合の処理 (Windows ストア) | モバイル デベロッパー センター" metaKeywords="" description="Windows ストア アプリケーションでオフライン データとの競合を処理する方法について説明します。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Mobile Services のオフライン データとの競合の処理" authors="wesmc" />


# Mobile Services のオフライン データとの競合の処理

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="Windows ストア C#" class="current">Windows ストア C#</a>
</div>


<p>このトピックでは、Windows Azure Mobile Services のオフライン機能を使用する場合にデータを同期して、競合を処理する方法について説明します。このチュートリアルでは、オフライン データとオンライン データの両方をサポートするアプリケーションをダウンロードした後、モバイル サービスをそのアプリケーションに統合します。その後、Windows Azure の管理ポータルにログインして、アプリケーションの実行中にデータベースを表示して更新します。
</p>

このチュートリアルは、前の「[オフライン データの使用]」チュートリアルの手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、「[オフライン データの使用]」チュートリアルを完了している必要があります。


このチュートリアルでは、次の基本的な手順について説明します。

1. [Windows ストア アプリ プロジェクトのダウンロード]
2. [期日列のデータベースへの追加]
  * [.NET バックエンド モバイル サービスのデータベースの更新]
  * [JavaScript モバイル サービスのデータベースの更新]
3. [モバイル サービスに対するアプリケーションのテスト]
4. [競合を作成するためのバックエンドでのデータの手動更新]

このチュートリアルでは、Windows 8.1 で実行されている Visual Studio 2013 が必要です。


## <a name="download-app"></a>サンプル プロジェクトのダウンロード

このチュートリアルは、Visual Studio 2013 の Windows ストア アプリ プロジェクトである、[競合コード サンプルの処理]に基づいて構築されています。このアプリケーションの UI は、各 TodoItem に新しいデータ列があることを除き、「[オフライン データの使用]」チュートリアルのアプリケーションと似ています。

1. [競合コード サンプルの処理]の C# バージョンをダウンロードします。

2. インストールされていない場合は、[SQLite for Windows 8.1] をインストールします。

3. Visual Studio 2013 でダウンロードしたプロジェクトを開きます。**F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

4. アプリケーションで、**[Insert a TodoItem]** に任意のテキストを入力し、**[Save]** をクリックします。追加する todo 項目の期日を変更することもできます。

アプリケーションはモバイル サービスにまだ接続されていないため、**[プッシュ]** および **[プル]** は例外をスローすることに注意してください。


## <a name="add-column"></a>列のデータ モデルへの追加

このセクションでは、モバイル サービスのデータベースを更新して、期日列のある TodoItem テーブルを含めます。このアプリケーションによって、このチュートリアルの後のセクションで同期の競合を生成できるように実行時に項目の期日を変更できます。

サンプルの `TodoItem` クラスは、MainPage.xaml.cs で定義されます。クラスは、そのテーブルに対する同期操作を対象とする次の属性を持っていることに注意してください。

        [DataTable("TodoWithDate")]

データベースを更新してこのテーブルを含めます。

### <a name="dotnet-backend"></a>.NET バックエンド モバイル サービスのデータベースの更新

モバイル サービスに .NET バックエンドを使用している場合、データベースのスキーマを更新するには、次の手順に従います。

1. Visual Studio で、.NET バックエンド モバイル サービス プロジェクトを開きます。
2. Visual Studio のソリューション エクスプローラーのサービス プロジェクトで、**[モデル]** フォルダーを展開し、ToDoItem.cs を開きます。次のように、`DueDate` プロパティを追加します。

          public class TodoItem : EntityData
          {
            public string Text { get; set; }
            public bool Complete { get; set; }
            public System.DateTime DueDate { get; set; }
          }


3. Visual Studio のソリューション エクスプローラーで **App_Start** フォルダーを展開し、WebApiConfig.cs ファイルを開きます。

    WebApiConfig.cs ファイルで、既定のデータベース初期化子クラスが `DropCreateDatabaseIfModelChanges` クラスから派生していることに注意してください。つまり、モデルへの変更により、テーブルが削除され、新しいモデルを格納するために再作成されることになります。したがって、テーブル内のデータは失われ、テーブルは再シードされます。次のように `Seed()` 初期化関数が新しい DueDate 列を初期化するように、データベース初期化子の Seed メソッドを変更します。WebApiConfig.cs ファイルを保存します。

    >[WACOM.NOTE] データベースの既定の初期化子を使用する場合は、Code First のモデル定義内でのデータ モデルの変更が検出されるたびに、Entity Framework がデータベースを削除して再作成します。このようなデータ モデルの変更を行ってデータベース内で既存のデータを保持するには、Code First Migrations を使用する必要があります。詳細については、「[How to Use Code First Migrations to Update the Data Model (Code First Migrations を使用してデータ モデルを更新する方法)](./articles/mobile-services-dotnet-backend-use-code-first-migrations)」を参照してください。


        new TodoItem { Id = "1", Text = "First item", Complete = false, DueDate = DateTime.Today },
        new TodoItem { Id = "2", Text = "Second item", Complete = false, DueDate = DateTime.Today },

          

4. Visual Studio のソリューション エクスプローラーで、**Controllers** フォルダーを展開し、ToDoItemController.cs を開きます。`TodoItemController` クラスの名前を `TodoWithDateController` に変更します。この結果、テーブル操作のための REST エンドポイントが変更されます。

        public class TodoWithDateController : TableController<TodoItem>
    

5. Visual Studio のソリューション エクスプ ローラーで、.NET バックエンド モバイル サービス プロジェクトを右クリックし、**[発行]** をクリックして変更を発行します。


### <a name="javascript-backend"></a>JavaScript バックエンド モバイル サービスのデータベースの更新

JavaScript バックエンド モバイル サービスでは、**TodoWithDate** という名前の新しいテーブルを追加します。JavaScript バックエンド モバイル サービスに **TodoWithDate** テーブルを追加するには、次の手順に従います。

  1. [Azure の管理ポータル]にログインします。

  2. モバイル サービスの **[データ]** タブに移動します。

  3. ページの下部で、**[作成]** をクリックし、**TodoWithDate** という名前の新しいテーブルを作成します。


## <a name="test-app"></a>モバイル サービスに対するアプリケーションのテスト

次に、Mobile Services に対してアプリケーションをテストします。

1. Azure の管理ポータルで、コマンド バーの **[キーの管理]** をクリックしてモバイル サービスのアプリケーション キーを検索します。**アプリケーション キー**をコピーします。

2. Visual Studio のソリューション エクスプローラーで、クライアント サンプル プロジェクトの App.xaml.cs ファイルを開きます。**MobileServiceClient** の初期化を変更して、モバイル サービス URL およびアプリケーション キーを使用します。

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3. Visual Studio で、**F5** キーを押してアプリケーションをビルドおよび実行します。

4. 前と同様に、テキスト ボックスにテキストを入力し、**[Save]** をクリックします。これによってデータはローカルの同期テーブルには保存されますが、サーバーには保存されません。

    ![][0]

5. データベースの現在の状態を表示するには、[Azure の管理ポータル] にログインし、**[Mobile Services]** をクリックして、目的のモバイル サービスをクリックします。

  * モバイル サービスの JavaScript バックエンドを使用している場合は、**[データ]** タブをクリックし、**TodoWithDate** テーブルをクリックします。アプリケーションからサーバーに変更をプッシュしていないため、**[参照]** をクリックして、テーブルがまだ空であることを確認します。

        ![][1]

  *  モバイル サービスの .NET バックエンドを使用している場合は、**[構成]** タブをクリックし、SQL データベースをクリックします。画面の下部にある **[管理]** をクリックして、SQL Azure の管理ポータルにログインし、データベースを表示します。  

        ![][2]
   	 

7. アプリケーションに戻り、**[プッシュ]** をクリックします。

8. 管理ポータルで、**TodoItem** テーブルの **[最新の情報に更新]** をクリックします。アプリケーションで入力したデータが表示されます。

   	![][3]

## <a name="handle-conflict"></a>競合を作成するためのバックエンドでのデータの更新

現実のシナリオでは、1 つのアプリケーションがデータベースのレコードに更新をプッシュし、次に別のアプリケーションがそのレコードの使用していないバージョンに基づく変更を同じレコードへプッシュしようとする場合に同期の競合が発生します。アプリケーションのインスタンスが更新されたレコードでプルせずに同じレコードを更新しようとすると、競合が発生し、アプリケーションで `MobileServicePreconditionFailedException` として捕捉されます。

アプリケーションを別のコンピューターに展開し、アプリケーションの 2 つのインスタンスを実行して、競合を生成する場合は、「[データベースの競合の処理]」チュートリアルの展開の手順に従います。

次の手順では、Visual Studio でデータベースを更新して競合を発生させる方法を示しています。

1. Visual Studio でサーバー エクスプ ローラーを実行し、Azure アカウントに接続します。Azure アカウントの **SQL Databases** を展開します。
 
    ![][5]
 
   
2. リストの SQL データベースを右クリックし、**[SQL Server オブジェクト エクスプローラーで開く]** をクリックします。
3. SQL Server オブジェクト エクスプ ローラーで、データベースを展開し、**テーブル**を展開します。**TodoWithDate** テーブルを右クリックし、**[データの表示]** をクリックします。 


4. 項目のうちの 1 つの **complete** フィールドを True に変更します。

    ![][6]

5. Todo アプリケーションに戻り、データベースで変更した項目と同じ項目を直接編集します。

    ![][7]

6. **[プッシュ]** をクリックします。競合を解決する方法を指示するダイアログ ボックスが表示されます。いずれかのオプションを選択して、競合を解決します。

    ![][8]

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

プッシュがキャンセルされると、`PushAsync` は `MobileServicePushFailedException` をスローし、例外プロパティ `PushResult.Status` は値 `MobileServicePushStatus.CancelledByOperation` を含みます。


<!-- Anchors. -->
[Windows ストア アプリ プロジェクトのダウンロード]: #download-app
[モバイル サービスの作成]: #create-service
[期日列のデータベースへの追加]: #add-column
[.NET バックエンド モバイル サービスのデータベースの更新]: #dotnet-backend  
[JavaScript モバイル サービスのデータベースの更新]: #javascript-backend
[モバイル サービスに対するアプリケーションのテスト]: #test-app
[競合を作成するためのバックエンドでのデータの手動更新]: #handle-conflict
[次のステップ]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-empty.png
[2]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-empty-sql.png
[3]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-push1.png
[4]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-design-edit.png
[5]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-server-explorer.png
[6]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-sql-server-object-explorer-update-data.png
[7]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run2.png
[8]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run3.png




<!-- URLs -->
[競合コード サンプルの処理]: http://go.microsoft.com/fwlink/?LinkId=394787
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-get-started/
[オフライン データの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[Azure の管理ポータル]: https://manage.windowsazure.com/
[データベースの競合の処理]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/#test-app

