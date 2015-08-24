<properties 
	pageTitle="プル通知をサポートするカスタム API の定義 | Microsoft Azure" 
	description="Azure Mobile Services を使用する Windows ストア アプリの定期的な通知をサポートするカスタム API の定義方法について説明します。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/08/2015" 
	ms.author="glenga"/>

# 定期的な通知をサポートするカスタム API を定義します。

このトピックでは、Windows ストア アプリでカスタム API を使用し、定期的な通知をサポートする方法について説明します。定期的な通知が有効になっている場合、Windows が定期的にカスタム API のエンドポイントにアクセスし、返された XML (タイル固有の形式) を使用して、スタート メニューのアプリケーションのタイルを更新します。詳細については、「[定期的な通知の概要]」を参照してください。

チュートリアル「[モバイル サービスの使用]」または「[既存のアプリケーションへの Mobile Services の追加]」の最後に作成したアプリケーションが、この機能の追加対象となります。次の手順を実行します。

1. [カスタム API を定義する]
2. [アプリケーションを更新して定期的な通知を有効にする]
3. [アプリケーションをテストする] 

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」または「[既存のアプリケーションへの Mobile Services の追加]」を完了しておく必要があります。

## <a name="define-custom-api"></a>カスタム API を定義する

1. [Azure の管理ポータル]にログインし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

   	![][0]

2. **[API]** タブをクリックし、**[カスタム API の作成]** をクリックします。

   	![][1]

	**[新しいカスタム API の作成]** ダイアログ ボックスが表示されます。

3. **[Get アクセス許可]** を **[すべてのユーザー]** に変更し、_[API 名]_ に「**tiles**」と入力して、チェック ボタンをクリックします。

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

   	> [AZURE.NOTE]このカスタム API スクリプトには、Node.js の [wns モジュール](http://go.microsoft.com/fwlink/p/?LinkId=306750)が使用されており、**require** 関数を使って参照されています。このモジュールは、[push オブジェクト](http://go.microsoft.com/fwlink/p/?LinkId=260591)から返される [wns オブジェクト](http://msdn.microsoft.com/library/windowsazure/jj554217.aspx)とは異なります。push オブジェクトから返される wns オブジェクトは、サーバー スクリプトからプッシュ通知を送信する目的に使用されます。

次に、クイック スタート アプリケーションに変更を加えます。ライブ タイルを更新する定期的な通知を開始するために、新しいカスタム API を要求します。

##<a name="update-app"></a>アプリケーションを更新して定期的な通知を有効にする

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

* [プッシュ通知の使用] <br/>定期的な通知は、Windows によって管理され、事前定義されたスケジュールでのみ実行されます。プッシュ通知は、モバイル サービスからオンデマンドで送信することができます。トースト、タイル、直接通知のいずれかの形式が利用できます。

* [Mobile Services のサーバー スクリプト リファレンス] <br/>カスタム API の作成について説明します。

* [Mobile Services .NET の使用方法の概念リファレンス] <br/>.NET で Mobile Services を使用する方法について説明します。

<!-- Anchors. -->
[カスタム API を定義する]: #define-custom-api
[アプリケーションを更新して定期的な通知を有効にする]: #update-app
[アプリケーションをテストする]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create.png
[2]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create-dialog.png
[3]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-select.png
[4]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-live-tile.png

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Mobile Services のサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[モバイル サービスの使用]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[既存のアプリケーションへの Mobile Services の追加]: ../mobile-services-windows-store-dotnet-get-started.md
[プッシュ通知の使用]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md

[Azure の管理ポータル]: https://manage.windowsazure.com/
[定期的な通知の概要]: http://msdn.microsoft.com/library/windows/apps/jj150587.aspx

[Mobile Services .NET の使用方法の概念リファレンス]: mobile-services-windows-dotnet-how-to-use-client-library.md

 

<!---HONumber=August15_HO7-->