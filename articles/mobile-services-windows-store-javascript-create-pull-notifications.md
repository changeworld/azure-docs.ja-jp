<properties linkid="develop-mobile-tutorials-create-pull-notifications-js" urlDisplayName="Define a custom API that supports pull notifications" pageTitle="Define a custom API that supports pull notifications - Azure Mobile Services" metaKeywords="" description="Learn how to Define a custom API that supports periodic notifications in Windows Store apps that use Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Define a custom API that supports periodic notifications" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 定期的な通知をサポートするカスタム API を定義します。

<div class="dev-center-tutorial-selector sublanding">

[Windows ストア C#][Windows ストア C#][Windows ストア JavaScript][Windows ストア JavaScript]

</div>

このトピックでは、Windows ストア アプリでカスタム API を使用し、定期的な通知をサポートする方法について説明します。定期的な通知が有効になっている場合、Windows が定期的にカスタム API のエンドポイントにアクセスし、返された XML (タイル固有の形式) を使用して、スタート メニューのアプリケーションのタイルを更新します。詳細については、「[定期的な通知の概要][定期的な通知の概要]」を参照してください。

チュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」または「[データの使用][データの使用]」の最後に作成したアプリケーションが、この機能の追加対象となります。次の手順を実行します。

1.  [カスタム API を定義する][カスタム API を定義する]
2.  [アプリケーションを更新して定期的な通知を有効にする][アプリケーションを更新して定期的な通知を有効にする]
3.  [アプリケーションをテストする][アプリケーションをテストする]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用][モバイル サービスの使用]」または「[データの使用][データの使用]」を完了している必要があります。

## <a name="define-custom-api"></a>カスタム API を定義する

1.  [Azure の管理ポータル][Azure の管理ポータル]にログインし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

    ![][0]

2.  **[API]** タブをクリックし、**[カスタム API の作成]** をクリックします。

    ![][1]

    **[新しいカスタム API の作成]** ダイアログ ボックスが表示されます。

3.  **[Get アクセス許可]** を **[すべてのユーザー]** に変更し、**[API 名]** に「*tiles*」と入力して、チェック ボタンをクリックします。

    ![][2]

    これで、パブリックな GET アクセス権を持つ新しい API が作成されます。

4.  API テーブル内の新しい tiles エントリをクリックします。

    ![][3]

5.  **[スクリプト]** タブをクリックし、既存のコードを次のコードに置き換えます。

        exports.get = function(request, response) {
            var wns = require('wns');
            var todoItems = request.service.tables.getTable('TodoItem');
            todoItems.where({
                complete: false
            }).read({
                success: sendResponse
            });

            function sendResponse(results) {
                var tileText = {
                    text1: "My todo list"
                };
                var i = 0;
                console.log(results)
                results.forEach(function(item) {
                    tileText["text" + (i + 2)] = item.text;
                    i++;
                });
                var xml = wns.createTileSquareText01(tileText);
                response.set('content-type', 'application/xml');
                response.send(200, xml);
            }
        };

    このコードは、TodoItem テーブルから未完了の項目上位 3 件を返し、**wns**.**createTileSquareText01** 関数に渡された JSON オブジェクトに読み込みます。この関数から返されるタイル テンプレート XML は次のとおりです。

        <tile>
            <visual>
                <binding template="TileSquareText01">
                    <text id="1">My todo list</text>
                    <text id="2">Task 1</text>
                    <text id="3">Task 2</text>
                    <text id="4">Task 3</text>
                </binding>
            </visual>
        </tile>

    クライアントは、GET 要求を送信してタイル テンプレートにアクセスすることになるため、**exports.get** 関数が使用されます。

    <div class="dev-callout">

    <b>注</b>
    <p>このカスタム API スクリプトには、Node.js の <a href="http://go.microsoft.com/fwlink/p/?LinkId=306750">wns モジュール </a>が使用されており、<strong>require</strong> 関数を使って参照されています。このモジュールは、<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/jj554217.aspx">push オブジェクト</a>から返される <a href="http://go.microsoft.com/fwlink/p/?LinkId=260591">wns オブジェクト</a>とは異なります。push オブジェクトから返される wns オブジェクトは、サーバー スクリプトからプッシュ通知を送信する目的に使用されます。</p>

    </div>

次に、クイック スタート アプリケーションに変更を加えます。ライブ タイルを更新する定期的な通知を開始するために、新しいカスタム API を要求します。

## <a name="update-app"></a><span class="short-header">アプリケーションの更新</span>アプリケーションを更新して定期的な通知を有効にする

1.  前のチュートリアルで使用したクイック スタート アプリケーションを実行します。Visual Studio で、F5 キーを押してください。

2.  少なくとも 1 個の項目が表示されることを確認します。1 つも項目が表示されない場合は、アプリケーションで、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

3.  Visual Studio のソリューション エクスプローラーで `\js` フォルダーを展開し、default.js プロジェクトを開いて、**client** 変数を定義するコードの後に次のコード行を追加します。

        var notifications = Windows.UI.Notifications;
        var recurrence = notifications.PeriodicUpdateRecurrence.hour;
        var url = new Windows.Foundation.Uri(client.applicationUrl, "/api/tiles");

        notifications.TileUpdateManager.createTileUpdaterForApplication().startPeriodicUpdate(url, recurrence);

    このコードによって定期的な通知が有効となり、新しいカスタム API (**tiles**) からタイル テンプレート データが要求されます。実際のデータの更新頻度に応じて最適な [PeriodicUpdateRecurrance] 値を選択してください。

## <a name="test-app"></a> アプリケーションをテストする

1.  Visual Studio で、F5 キーを押して再度アプリケーションを実行します。

    これで定期的な通知が有効になります。

2.  スタート画面に移動してアプリケーションのライブ タイルを探し、項目データが表示されていることを確認します。

    ![][4]

## 次のステップ

定期的な通知を作成したら、モバイル サービスに関連した次のトピックも参考にしてください。

-   [プッシュ通知の使用][プッシュ通知の使用]
   
    定期的な通知は、Windows によって管理され、事前定義されたスケジュールでのみ実行されます。プッシュ通知は、モバイル サービスからオンデマンドで送信することができます。トースト、タイル、直接通知のいずれかの形式が利用できます。

-   [モバイル サービスのサーバー スクリプト リファレンス][モバイル サービスのサーバー スクリプト リファレンス]
   
    カスタム API の作成について説明します。



  [Windows ストア C#]: /ja-jp/develop/mobile/tutorials/create-pull-notifications-dotnet "Windows ストア C#"
  [Windows ストア JavaScript]: /ja-jp/develop/mobile/tutorials/create-pull-notifications-js "Windows ストア JavaScript"
  [定期的な通知の概要]: http://msdn.microsoft.com/ja-jp/library/windows/apps/jj150587.aspx
  [モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
  [データの使用]: /ja-jp/develop/mobile/tutorials/started-with-data-js
  [カスタム API を定義する]: #define-custom-api
  [アプリケーションを更新して定期的な通知を有効にする]: #update-app
  [アプリケーションをテストする]: #test-app
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create.png
  [2]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create-dialog.png
  [3]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-select.png
  [4]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-live-tile.png
  [プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js
  [モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
