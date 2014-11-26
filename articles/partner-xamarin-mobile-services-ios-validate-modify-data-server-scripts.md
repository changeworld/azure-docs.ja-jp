<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-Xamarin-iOS" urlDisplayName="" pageTitle="Use server scripts to validate and modify data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Xamarin iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="donnam" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# サーバー スクリプトを使用したモバイル サービスのデータの検証および変更

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a></div>


このトピックでは、Azure のモバイル サービスでサーバー スクリプトを活用する方法について説明します。サーバー スクリプトは、モバイル サービスに登録され、挿入や更新が行われるデータでの広範な操作 (検証やデータの修正を含む) の実行に使用できます。このチュートリアルでは、検証およびデータの修正を行うサーバー スクリプトを定義して登録します。多くの場合、サーバー側スクリプトの動作がクライアントに影響を与えるため、これらの新しい動作の利点を活用できるように iOS アプリも更新します。完成したコードは、[ValidateModifyData アプリケーション][ValidateModifyData アプリケーション] サンプルで参照できます。

このチュートリアルでは、次の基本的な手順について説明します。

1.  [文字列の長さの検証の追加][文字列の長さの検証の追加]
2.  [検証をサポートするためのクライアントの更新][検証をサポートするためのクライアントの更新]
3.  [挿入時のタイムスタンプの追加][挿入時のタイムスタンプの追加]
4.  [タイムスタンプを表示するためのクライアントの更新][タイムスタンプを表示するためのクライアントの更新]

このチュートリアルは、前の[データの使用][データの使用]に関するチュートリアルの手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、[データの使用][データの使用]に関するチュートリアルを完了している必要があります。

## <a name="string-length-validation"></a>検証の追加

ユーザーにより送信されたデータの長さを検証することをお勧めします。最初に、モバイル サービスに送信された文字列データの長さを検証するスクリプトを登録し、長すぎる文字列 (この場合は 10 文字を超える) を拒否します。

1.  [Azure の管理ポータル][Azure の管理ポータル]にログインし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

    ![][0]

2.  **[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

    ![][1]

3.  **[スクリプト]** をクリックし、**[挿入]** 操作を選択します。

    ![][2]

4.  既存のスクリプトを次の関数に置き換え、**[保存]** をクリックします。

    function insert(item, user, request) {
     if (item.text.length \> 10) {
     request.respond(statusCodes.BAD\_REQUEST, 'Text length must be 10 characters or less.');
     } else {
     request.execute();
     }
     }

    このスクリプトは、**text** プロパティの長さをチェックし、長さが 10 文字を超えた場合にエラー応答を送信します。それ以外の場合、**execute** メソッドが呼び出されて挿入を完了します。

    <div class="dev-callout">

    <b>注</b>
    <p>登録したスクリプトを <b>[スクリプト]</b> タブで削除できます。<b>[クリア]</b> をクリックし、<b>[保存]</b> をクリックします。

    </div>

## <a name="update-client-validation"></a>クライアントの更新

モバイル サービスはデータを検証してエラー応答を送信するため、検証からのエラー応答を処理できるようにアプリケーションを更新する必要があります。

1.  Xamarin Studio で、「[モバイル サービスでのデータの使用][データの使用]」チュートリアルを実行したときに修正したプロジェクトを開きます。

2.  **[Run]** を押して、プロジェクトをビルドし、アプリケーションを開始します。次に、テキスト ボックスに 11 文字以上のテキストを入力し、プラス (**[+]**) アイコンをクリックします。

    モバイル サービスから返された応答 400 (正しくない要求) の結果として、処理されないエラーが発生します。

3.  TodoService.cs ファイルで、**InsertTodoItemAsync** メソッドの現在の `try/catch` 例外処理を探して、`catch` を次のコードに置き換えます。

    catch (Exception ex) {
     var exDetail = (ex.InnerException.InnerException as MobileServiceInvalidOperationException);
     Console.WriteLine(exDetail.Message);

        UIAlertView alert = new UIAlertView() { 
                Title = "Error", 
                Message = exDetail.Message
        } ;
        alert.AddButton("Ok");
        alert.Show();

        return -1;
        }

    これにより、ユーザーにエラーを表示するポップアップ ウィンドウが開きます。

4.  **TodoListViewController.cs** で **OnAdd** メソッドを探します。このメソッドを更新して、返される `index` が、**InsertTodoItemAsync** の例外処理で返される `-1` とは異なるようにします。この場合、`TableView` に新しい行を追加しないようにします。

    if (index != -1) {
     TableView.InsertRows(new [] { NSIndexPath.FromItemSection(index, 0) },
     UITableViewRowAnimation.Top);
     itemText.Text = "";
    }

5.  アプリケーションをリビルドして開始します。

    ![][3]

    エラーが処理され、エラー メッセージがユーザーに表示されることに注目してください。

## <a name="next-steps"> </a>次のステップ

このチュートリアルが完了したため、データ シリーズの最終チュートリアルに進むことを検討してください。[ページングを使用したクエリの改善][ページングを使用したクエリの改善]。

サーバー スクリプトは、ユーザーを認証するときに、およびプッシュ通知の送信のためにも使用されます。詳細については、次のチュートリアルを参照してください。

-   [スクリプトを使用したユーザーの認証][スクリプトを使用したユーザーの認証]
    <br/>認証されたユーザーの ID に基づきデータをフィルター処理する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]
    <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

-   [モバイル サービスのサーバー スクリプト リファレンス][モバイル サービスのサーバー スクリプト リファレンス]
    <br/>サーバー スクリプトの登録および使用について説明します。

 
 


  [ValidateModifyData アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=331330
  [文字列の長さの検証の追加]: #string-length-validation
  [検証をサポートするためのクライアントの更新]: #update-client-validation
  [挿入時のタイムスタンプの追加]: #add-timestamp
  [タイムスタンプを表示するためのクライアントの更新]: #update-client-timestamp
  [データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [0]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [3]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png
  [ページングを使用したクエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
  [スクリプトを使用したユーザーの認証]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
  [プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-ios
  [モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
