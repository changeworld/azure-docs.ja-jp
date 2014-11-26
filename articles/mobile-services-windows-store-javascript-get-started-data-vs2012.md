<properties linkid="develop-mobile-tutorials-get-started-with-data-js" urlDisplayName="Get Started with Data" pageTitle="Get started with data (JavaScript) - Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="https://www.windowsazure.com/ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Visual Studio 2012 を使用したモバイル サービスでのデータの使用

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-js-vs2012" title="Windows ストア JavaScript" class="current">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-ios" title="iOS" class="current">iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-android" title="Android" class="current">Android</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-html" title="HTML" class="current">HTML</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

このトピックでは、Azure のモバイル サービスを使用して Windows ストア アプリケーションのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにログインして、アプリケーションの実行中にデータに加えられた変更を表示します。

<div class="dev-callout"><b>注</b>
<p>このチュートリアルでは、Visual Studio 2012 で作成された Windows ストア アプリにモバイル サービス機能を追加します。Visual Studio 2013 には、Windows ストア アプリをモバイル サービスに簡単に接続できる新機能が含まれています。詳細については、「<a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-js/">モバイル サービスでのデータの使用</a>」を参照してください。</p>
</div>

このチュートリアルでは、次の基本的な手順について説明します。

1.  [Windows ストア アプリ プロジェクトのダウンロード][Windows ストア アプリ プロジェクトのダウンロード]
2.  [モバイル サービスの作成][モバイル サービスの作成]
3.  [ストレージのデータ テーブルの追加][ストレージのデータ テーブルの追加]
4.  [モバイル サービスを使用するためのアプリケーションの更新][モバイル サービスを使用するためのアプリケーションの更新]
5.  [モバイル サービスに対するアプリケーションのテスト][モバイル サービスに対するアプリケーションのテスト]

<div class="dev-callout"><strong>注</strong> <p>このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F" target="_blank">Azure の無料評価版サイト</a>を参照してください。</p></div>

## <a name="download-app"></a><span class="short-header">プロジェクトのダウンロード</span>GetStartedWithData プロジェクトのダウンロード

このチュートリアルは、Windows ストア アプリケーションである [GetStartedWithData アプリケーション][GetStartedWithData アプリケーション]に基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービスのクイック スタートで生成したアプリケーションと同じです。

1.  JavaScript バージョンの GetStartedWithData サンプル アプリケーションを[デベロッパー サンプル コード集のサイト][GetStartedWithData アプリケーション]からダウンロードします。

    ![][0]

2.  Visual Studio 2012 Express for Windows 8 で、ダウンロードしたプロジェクトを開き、**js** フォルダーを展開して、default.js ファイルを確認します。

    追加された **TodoItem** オブジェクトはメモリ内の **List** オブジェクトに格納されます。

3.  **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

4.  アプリケーションで、**[Insert a TodoItem]** に任意のテキストを入力し、**[Save]** をクリックします。

    ![][1]

    保存したテキストが、**[Query and update data]** の下の 2 番目の列に表示されます。

## <a name="create-service"></a><span class="short-header">モバイル サービスの作成</span>管理ポータルでの新しいモバイル サービスの作成

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">新しいテーブルの追加</span>モバイル サービスへの新しいテーブルの追加

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a><span class="short-header">アプリケーションの更新</span>モバイル サービスをデータ アクセスに使用するためのアプリケーションの更新

モバイル サービスの準備が整ったら、ローカル コレクションの代わりにモバイル サービスに項目を格納するようにアプリケーションを更新します。

1.  Visual Studio の**ソリューション エクスプローラー**で、プロジェクト名を右クリックし、**[NuGet パッケージの管理]** をクリックします。

2.  左側のウィンドウで、**[オンライン]** カテゴリを選択し、`WindowsAzure.MobileServices.WinJS` を探します。**Azure Mobile Services for WinJS** パッケージで **[インストール]** をクリックし、使用許諾契約に同意します。

    ![][2]

    これにより、モバイル サービス クライアント ライブラリがプロジェクトに追加されます。

3.  default.html プロジェクト ファイルで、次のスクリプト参照をページ ヘッダーに追加します。

        <script type="text/javascript" src="/js/MobileServices.js"></script>

4.  管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

5.  **[ダッシュボード]** タブをクリックし、**サイトの URL** をメモに記録します。次に、**[キーの管理]** をクリックし、**アプリケーション キー**をメモに記録します。

    ![][3]

    これらの値は、アプリケーション コードからモバイル サービスにアクセスするときに必要になります。

6.  Visual Studio で、default.js ファイルを開き、**client** 変数を定義する次のコードをコメント解除します。また、**MobileServiceClient** コンストラクターに、モバイル サービスで入手した URL とアプリケーション キーを (この順序で) 指定します。

            var client = new WindowsAzure.MobileServiceClient(
                "AppUrl",
                "appKey"
            );

    これで、モバイル サービスにアクセスするために使用される MobileServiceClient の新しいインスタンスが作成されます。

7.  次のコード行をコメント解除します。

        var todoTable = client.getTable('TodoItem');

    このコードでは、新しいデータベース テーブルのプロキシ オブジェクト (**todoTable**) を作成します。

8.  **InsertTodoItem** 関数を次のコードに置き換えます。

        var insertTodoItem = function (todoItem) {
            // Inserts a new row into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the binding list.
            todoTable.insert(todoItem).done(function (item) {
                todoItems.push(item);
            });
        };

    このコードでは、新しい項目をテーブルに挿入します。

9.  **RefreshTodoItems** 関数を次のコードで置き換えます。

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the TodoItems table. 
            todoTable.read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };      

    これにより、todoTable 内で、モバイル サービスから返されたすべての完了済み項目が格納される、項目のコレクションへのバインディングが設定されます。

10. **UpdateCheckedTodoItem** 関数を次のコードで置き換えます。

        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
        };

    これにより、項目の更新がモバイル サービスに送信されます。

バックエンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、モバイル サービスに対してアプリケーションをテストします。

## <a name="test-app"></a><span class="short-header">アプリケーションのテスト</span>新しいモバイル サービスに対するアプリケーションのテスト

1.  Visual Studio で、F5 キーを押してアプリケーションを実行します。

2.  ここでも、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

    これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3.  [管理ポータル][管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4.  **[データ]** タブをクリックし、**[参照]** をクリックします。

    ![][4]

    **TodoItem** テーブルに、モバイル サービスによって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

5.  アプリケーションで、リストの項目の 1 つをチェックします。ポータルの **[参照]** タブに戻り、[最新の情報に更新] をクリックします。

    "complete" 値が **false** から **true** に変更されます。

6.  default.js プロジェクト ファイルで、既存の **RefreshTodoItems** 関数を、完了済み項目をフィルター処理で除外する次のコード行で置き換えます。

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7.  アプリケーションで、リストの項目をもう 1 つチェックし、**[最新の情報に更新]** をクリックします。

    リスト内のチェックされた項目が非表示になります。最新の情報への更新のたびにモバイル サービスへの往復が発生し、その結果、フィルター処理されたデータが返されます。

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



  [Windows ストア アプリ プロジェクトのダウンロード]: #download-app
  [モバイル サービスの作成]: #create-service
  [ストレージのデータ テーブルの追加]: #add-table
  [モバイル サービスを使用するためのアプリケーションの更新]: #update-app
  [モバイル サービスに対するアプリケーションのテスト]: #test-app
  [GetStartedWithData アプリケーション]: http://go.microsoft.com/fwlink/?LinkId=262308
  [0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-data-sample-download-js.png
  [1]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-quickstart-startup.png
  [2]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-add-nuget-package-js.png
  [3]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-dashboard-tab.png
  [管理ポータル]: https://manage.windowsazure.com/
  [4]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-todoitem-data-browse.png
  [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-js
  [ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-js
  [認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js
  [プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js-vs2012
