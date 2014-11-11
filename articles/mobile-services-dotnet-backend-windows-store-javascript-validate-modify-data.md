<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-javascript" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Javascript Windows Store app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# .NET バックエンドを使用したモバイル サービスのデータの検証および変更



[Windows ストア C#][Windows ストア C#] [Windows ストア JavaScript][Windows ストア JavaScript] [Windows Phone][Windows Phone][iOS][iOS] [Android][Android] [HTML][HTML][Xamarin.iOS][Xamarin.iOS] [Xamarin.Android][Xamarin.Android]
[.NET バックエンド][.NET バックエンド] | [JavaScript バックエンド][JavaScript バックエンド]

</div>

このトピックでは、.NET バックエンド Azure モバイル サービス内でコードを使用してデータを検証および変更する方法について説明します。.NET バックエンド サービスは、Web API フレームワークを使用して構築した HTTP サービスです。Web API フレームワークで定義されている `ApiController` クラスについて理解している場合、モバイル サービスによって提供されている `TableController` クラスは非常に直感的に把握できます。`TableController` は、`ApiController` クラスから派生したもので、データベース テーブルとやり取りするための追加機能を提供します。挿入や更新の対象となるデータを操作する目的で使用でき、このチュートリアルで示す検証やデータの変更もその中に含まれます。

このチュートリアルでは、次の基本的な手順について説明します。

1.  [文字列の長さの検証の追加][文字列の長さの検証の追加]
2.  [検証をサポートするためのクライアントの更新][検証をサポートするためのクライアントの更新]
3.  [長さの検証テスト][長さの検証テスト]
4.  [CompleteDate に関するタイムスタンプ フィールドの追加][CompleteDate に関するタイムスタンプ フィールドの追加]
5.  [CompleteDate を表示するためのクライアントの更新][CompleteDate を表示するためのクライアントの更新]

このチュートリアルは、以前のチュートリアルである「[作業の開始][作業の開始]」または「[データの使用][データの使用]」に関する手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、「[作業の開始][作業の開始]」または「[データの使用][データの使用]」を完了している必要があります。

## <a name="string-length-validation"></a>モバイル サービスへの検証コードの追加

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]

## <a name="update-client-validation"></a>クライアントの更新

今度は、データを検証し、テキストの長さが無効な場合はエラー応答を送信するようにモバイル サービスを設定するため、検証からのエラー応答を処理できるようにアプリケーションを更新する必要があります。エラーは、クライアント アプリケーションによる `IMobileServiceTable<TodoItem].InsertAsync()` の呼び出しからキャッチされます。

1.  Visual Studio のソリューション エクスプローラー ウィンドウで、JavaScript クライアント プロジェクトに移動し、**js** フォルダーを展開します。その後、default.js ファイルを開きます。

2.  default.js で、既存の **insertTodoItem** 関数を次の関数定義に置き換えます。

        var insertTodoItem = function (todoItem) {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the Binding List
            todoTable.insert(todoItem)
                .then(function (item) {
                  todoItems.push(item);
                },
                function (error) {
                  var msgDialog =
                    new Windows.UI.Popups.MessageDialog(JSON.parse(error.request.responseText).message,
                    error.request.statusText + "(" + error.request.status + ")");
                  msgDialog.showAsync();
                });
        };

    このバージョンの関数には、エラー処理が含まれています。また、応答から取得したエラー メッセージ、ステータス テキスト、およびステータス コードを示す `MessageDialog` を表示します。

## <a name="test-length-validation"></a>長さの検証テスト

1.  Visual Studio のソリューション エクスプローラー ウィンドウで JavaScript クライアント アプリケーション プロジェクトを右クリックし、**[デバッグ]**、**[新しいインスタンスを開始]** の順にクリックします。

2.  新しい todo 項目に対して 10 文字を超える長さのテキストを入力し、**[保存]** をクリックします。

    ![][0]

3.  無効なテキストへの応答として、次のようなメッセージ ダイアログが表示されます。

    ![][1]

## <a name="add-timestamp"></a>CompleteDate に関するタイムスタンプ フィールドの追加

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

## <a name="update-client-timestamp"></a>CompleteDate を表示するためのクライアントの更新

最後の手順は、クライアントを更新して新しい **completeDate** データを表示することです。

1.  Visual Studio のソリューション エクスプローラーで JavaScript クライアント プロジェクトを使用し、default.html ファイルを開きます。バインディング テンプレートの `div` タグ要素を、次の定義に置き換えます。その後、ファイルを保存します。これにより、`div` タグに対して、**completeDate** にバインドされている innerText プロパティが追加されます。

        <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
          <div style="display: -ms-grid; -ms-grid-columns: 3">
            <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; 
              dataContext: this" />
            <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px" 
              data-win-bind="innerText: text">
            </div>
            <div style="-ms-grid-column: 3; -ms-grid-row-align: center; margin-left: 10px" 
              data-win-bind="innerText: completeDate">
            </div>
          </div>
        </div>

2.  default.js で、既存の **refreshTodoItems** 関数内にある `.Where` 句関数を削除します。その結果、完了した todoitems が結果の中に含まれるようになります。

            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view be querying the TodoItems table.
                // The query excludes completed TodoItems
                todoTable.read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listItems.winControl.itemDataSource = todoItems.dataSource;
                    });
            };

3.  default.js 内で、**updateCheckedTodoItem** 関数を次のように更新します。その結果、更新を行った後に項目の表示が更新され、完了した項目はリストから削除されなくなります。その後、ファイルを保存します。

            var updateCheckedTodoItem = function (todoItem) {
                // This code takes a freshly completed TodoItem and updates the database. 
                todoTable.update(todoItem).done(function () {
                    refreshTodoItems();
                });
            };

4.  Visual Studio の ソリューション エクスプローラー ウィンドウで **[ソリューション]** を右クリックし、**[ソリューションのリビルド]** をクリックして、クライアントと .NET バックエンド サービスの両方をリビルドします。両方のプロジェクトがエラーなしでビルドされることを確認します。

    ![][2]

5.  **F5** キーを押して、クライアント アプリケーションとサービスをローカルで実行します。いくつかの新しい項目を追加し、いくつかの項目に完了マークを付けて、**CompleteDate** タイムスタンプが更新されていることを確認します。

    ![][3]

6.  Visual Studio のソリューション エクスプローラーで、todolist サービス プロジェクトを右クリックし、**[発行]** をクリックします。Azure ポータルからダウンロードした発行設定ファイルを使用して、.NET バックエンド サービスを Microsoft Azure に発行します。

7.  モバイル サービス アドレスへの接続をコメント解除して、クライアント プロジェクトに対応する default.js ファイルを更新します。Azure アカウント内でホストされている .NET バックエンドに対してアプリケーションをテストします。

## <a name="next-steps"> </a>次のステップ

このチュートリアルが完了したため、データ シリーズの最終チュートリアルに進むことを検討してください。[ページングを使用したクエリの改善][ページングを使用したクエリの改善]。

サーバー スクリプトは、ユーザーを認証するときに、およびプッシュ通知の送信のためにも使用されます。詳細については、次のチュートリアルを参照してください。

-   [ユーザーのサービス側の認証][ユーザーのサービス側の認証]
    認証されたユーザーの ID に基づきデータをフィルター処理する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]
    アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

-   [モバイル サービス .NET の使用方法の概念リファレンス][モバイル サービス .NET の使用方法の概念リファレンス]
    .NET で Mobile Services を使用する方法について説明します。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows ストア C#]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ "Windows ストア C#"
  [Windows ストア JavaScript]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ "Windows ストア JavaScript"
  [Windows Phone]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ "Windows Phone"
  [iOS]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [.NET バックエンド]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ ".NET バックエンド"
  [JavaScript バックエンド]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/ "JavaScript バックエンド"
  [文字列の長さの検証の追加]: #string-length-validation
  [検証をサポートするためのクライアントの更新]: #update-client-validation
  [長さの検証テスト]: #test-length-validation
  [CompleteDate に関するタイムスタンプ フィールドの追加]: #add-timestamp
  [CompleteDate を表示するためのクライアントの更新]: #update-client-timestamp
  [作業の開始]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
  [データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
  [mobile-services-dotnet-backend-add-validation]: ../includes/mobile-services-dotnet-backend-add-validation.md
  [0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png
  [1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
  [mobile-services-dotnet-backend-add-completedate]: ../includes/mobile-services-dotnet-backend-add-completedate.md
  [2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png
  [3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png
  [ページングを使用したクエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-dotnet
  [ユーザーのサービス側の認証]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/
  [プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
  [モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library
