<properties linkid="develop-mobile-tutorials-create-pull-notifications-js" urlDisplayName="プル通知をサポートするカスタム API の定義" pageTitle="プル通知をサポートするカスタム API の定義 - Azure モバイル サービス" metaKeywords="" description="Azure モバイル サービスを使用した Windows ストア アプリケーションの定期的な通知をサポートするカスタム API の定義方法について説明します。" metaCanonical="" services="" documentationCenter="" title="定期的な通知をサポートするカスタム API の定義" authors="glenga" solutions="" manager="" editor="" />




# 定期的な通知をサポートするカスタム API の定義

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ja-jp/develop/mobile/tutorials/create-pull-notifications-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/create-pull-notifications-js" title="Windows ストア JavaScript" class="current">Windows ストア JavaScript</a>
</div>

このトピックでは、Windows ストア アプリでカスタム API を使用し、定期的な通知をサポートする方法について説明します。定期的な通知が有効になっている場合、Windows が定期的にカスタム API のエンドポイントにアクセスし、返された XML (タイル固有の形式) を使用して、スタート メニューのアプリケーションのタイルを更新します。詳細については、「[定期的な通知の概要]」を参照してください。

チュートリアル「[モバイル サービスの使用]」または「[データの使用]」の最後に作成したアプリケーションが、この機能の追加対象となります。次の手順を実行します。

1. [カスタム API を定義する]
2. [アプリケーションを更新して定期的な通知を有効にする]
3. [アプリケーションをテストする]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」または「[データの使用]」を完了している必要があります。

## <a name="define-custom-api"></a>カスタム API を定義する

1. [Azure 管理ポータル]にログインし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

   	![][0]

2. **[API]** タブをクリックし、**[カスタム API の作成]** をクリックします。

   	![][1]

   	**[新しいカスタム API の作成]** ダイアログ ボックスが表示されます。

3. **[Get アクセス許可]** を **[すべてのユーザー]** に変更し、**[API 名]** に「_tiles_」と入力して、チェック ボタンをクリックします。

   	![][2]

	これで、パブリックな GET アクセス権を持つ新しい API が作成されます。

4. API テーブル内の新しい tiles エントリをクリックします。

	![][3]

5. **[スクリプト]** タブをクリックし、既存のコードを次のコードに置き換えます。

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

   	<div class="dev-callout"><b>注</b>
   		<p>このカスタム API スクリプトには、Node.js の <a href="http://go.microsoft.com/fwlink/p/?LinkId=306750">wns モジュール</a>が使用されており、<strong>require</strong> 関数を使って参照されています。このモジュールは、<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/jj554217.aspx">push オブジェクト</a>から返される <a href="http://go.microsoft.com/fwlink/p/?LinkId=260591">wns オブジェクト</a>とは異なります。push オブジェクトから返される wns オブジェクトは、サーバー スクリプトから push 通知を送信する目的に使用されます。</p>
   	</div>

次に、クイック スタート アプリケーションに変更を加えます。ライブ タイルを更新する定期的な通知を開始するために、新しいカスタム API を要求します。

<h2><a name="update-app"></a><span class="short-header">アプリケーションを更新する</span>アプリケーションを更新して定期的な通知を有効にする</h2>

1. Visual Studio で F5 キーを押して、前のチュートリアルで使用したクイック スタート アプリケーションを実行します。

2. 少なくとも 1 個の項目が表示されることを確認します。1 つも項目が表示されない場合は、アプリケーションで、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

3. Visual Studio のソリューション エクスプローラーで `\js` フォルダーを展開し、default.js プロジェクトを開いて、**client** 変数を定義するコードの後に次のコード行を追加します。

        var notifications = Windows.UI.Notifications;
        var recurrence = notifications.PeriodicUpdateRecurrence.hour;
        var url = new Windows.Foundation.Uri(client.applicationUrl, "/api/tiles");

        notifications.TileUpdateManager.createTileUpdaterForApplication().startPeriodicUpdate(url, recurrence);

	このコードによって定期的な通知が有効となり、新しいカスタム API (**tiles**) からタイル テンプレート データが要求されます。実際のデータの更新頻度に応じて最適な [PeriodicUpdateRecurrance] 値を選択してください。

## <a name="test-app"></a>アプリケーションをテストする

1. Visual Studio で、F5 キーを押して再度アプリケーションを実行します。

	これで定期的な通知が有効になります。

2. スタート画面に移動してアプリケーションのライブ タイルを探し、項目データが表示されていることを確認します。

 	![][4]

## 次のステップ

定期的な通知を作成したら、モバイル サービスに関連した次のトピックも参考にしてください。

* [プッシュ通知の使用]
	<br/>定期的な通知は、Windows によって管理され、事前定義されたスケジュールでのみ実行されます。プッシュ通知は、モバイル サービスからオンデマンドで送信することができます。トースト、タイル、直接通知のいずれかの形式が利用できます。

* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>カスタム API の作成について説明します。

<!-- Anchors. -->
[カスタム API を定義する]: #define-custom-api
[アプリケーションを更新して定期的な通知を有効にする]: #update-app
[アプリケーションをテストする]: #test-app
[次のステップ]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create.png
[2]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create-dialog.png
[3]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-select.png
[4]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-live-tile.png

<!-- URLs. -->
[Windows プッシュ通知および Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[マイ アプリケーション ダッシュボード]: http://go.microsoft.com/fwlink/?LinkId=262039
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
[データの使用]: /ja-jp/develop/mobile/tutorials/started-with-data-js
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js
[JavaScript と HTML]: mobile-services-win8-javascript/

[Azure の管理ポータル]: https://manage.windowsazure.com/
[定期的な通知]: http://msdn.microsoft.com/ja-jp/library/windows/apps/jj150587.aspx


