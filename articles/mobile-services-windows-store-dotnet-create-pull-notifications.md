<properties urlDisplayName="Define a custom API that supports pull notifications" pageTitle="プル通知をサポートするカスタム API を定義する - Azure Mobile Services" metaKeywords="" description="Learn how to Define a custom API that supports periodic notifications in Windows Store apps that use Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Define a custom API that supports periodic notifications" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />

# 定期的な通知をサポートするカスタム API を定義します。

<div class="dev-center-tutorial-selector"> 
	<a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-create-pull-notifications/" title="Windows Store C#" class="current">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications/" title="Windows Store JavaScript">Windows ストア JavaScript</a>
</div>

このトピックでは、Windows ストア アプリでカスタム API を使用し、定期的な通知をサポートする方法について説明します。定期的な通知が有効になっている場合、Windows が定期的にカスタム API のエンドポイントにアクセスし、返された XML (タイル固有の形式) を使用して、スタート メニューのアプリケーションのタイルを更新します。詳細については、「[定期的な通知の概要]」を参照してください。 

チュートリアル「[モバイル サービスの使用]」または「[既存のアプリケーションへの Mobile Services の追加]」の最後に作成したアプリケーションが、この機能の追加対象となります。次の手順を実行します。

1. [カスタム API を定義する]
2. [アプリケーションを更新して定期的な通知を有効にする]
3. [アプリケーションをテストする] 

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[Mobile Services の使用]」または「[既存のアプリケーションへの Mobile Services の追加]」を完了しておく必要があります。  

## <a name="define-custom-api"></a>カスタム API を定義する

1. [Azure の管理ポータル]にログインし、**[Mobile Services]** をクリックして、アプリケーションをクリックします。

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




				</binding>
			</visual>
		</tile>

	クライアントは、GET 要求を送信してタイル テンプレートにアクセスすることになるため、**exports.get** 関数が使用されます。

   	<div class="dev-callout"><b>注</b>
   		<p>このカスタム API スクリプトは、Node.js <a href="http://go.microsoft.com/fwlink/p/?LinkId=306750">wns モジュールを使用します。</a>このモジュールは、 <strong>require</strong> 関数を使用して参照されます。このモジュールは、サーバー スクリプトからプッシュ通知を送信するために使用される、 <a href="http://go.microsoft.com/fwlink/p/?LinkId=260591">push オブジェクトによって</a> 返される <a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/jj554217.aspx">wns オブジェクト</a>とは異なります。</p>
   	</div>

次に、クイック スタート アプリケーションに変更を加えます。ライブ タイルを更新する定期的な通知を開始するために、新しいカスタム API を要求します。

<h2><a name="update-app"></a>アプリケーションを更新して定期的な通知を有効にする</h2>

1. 前のチュートリアルで使用したクイック スタート アプリケーションを実行します。Visual Studio で、F5 キーを押してください。

2. 少なくとも 1 個の項目が表示されることを確認します。1 つも項目が表示されない場合は、アプリケーションで、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

3. Visual Studio で App.xaml.cs プロジェクト ファイルを開き、次の using ステートメントを追加します。

		using Windows.UI.Notifications;

4. **OnLaunched** イベント ハンドラーに次のコードを追加します。

        TileUpdateManager.CreateTileUpdaterForApplication().StartPeriodicUpdate(
            new System.Uri(MobileService.ApplicationUri, "/api/tiles"),
            PeriodicUpdateRecurrence.Hour
        );

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

* [Mobile Services .NET の使用方法の概念リファレンス]
  <br/>.NET でモバイル サービスを使用する方法について説明します

<!-- Anchors. -->
[カスタム API を定義する]: #define-custom-api
[アプリケーションを更新して定期的な通知を有効にする]: #update-app
[アプリケーションをテストする]: #test-app
[次のステップ]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create.png
[2]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create-dialog.png
[3]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-select.png
[4]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-live-tile.png

<!-- URLs. -->
[Windows プッシュ通知および Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[マイ アプリ ダッシュボード]: http://go.microsoft.com/fwlink/?LinkId=262039
[Mobile Services の使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started
[既存のアプリケーションへの Mobile Services の追加]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push

[Azure の管理ポータル]: https://manage.windowsazure.com/
[定期的な通知]: http://msdn.microsoft.com/ja-jp/library/windows/apps/jj150587.aspx

[Mobile Services .NET の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


<!--HONumber=35.1-->
