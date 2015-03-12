<properties 
	pageTitle="プッシュ通知の使用 (Android JavaScript) | モバイル デベロッパー センター" 
	description="Azure モバイル サービスを使用して Android JavaScript アプリにプッシュ通知を送信する方法について説明します。" 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	writer="ricksal" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="ricksal"/>

# モバイル サービス アプリへのプッシュ通知の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push-EC.md)]

このトピックでは、Azure モバイル サービスで Google Cloud Messaging (GCM) を使用して Android アプリにプッシュ通知を送信する方法について説明します。このチュートリアルでは、クイック スタート プロジェクトへの Azure 通知ハブを使用したプッシュ通知を有効にします。完了すると、モバイル サービスは、レコードが挿入されるたびにプッシュ通知を送信します。

このチュートリアルでは、プッシュ通知を有効にするための、次の基本的な手順について説明します。

1. [Google Cloud Messaging を有効にする](#register)
2. [モバイル サービスを構成する](#configure)
3. [アプリケーションにプッシュ通知を追加する](#add-push)
4. [プッシュ通知を送信するようにスクリプトを更新する](#update-scripts)
5. [データを挿入して通知を受け取る](#test)


>[AZURE.NOTE] アプリケーション全体のソース コードを参照するには、 <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithPush/Android" target="_blank">ここ</a>。

## 前提条件

[AZURE.INCLUDE [mobile-services-android-prerequisites](../includes/mobile-services-android-prerequisites-EC.md)]

## <a id="register"></a>Google Cloud Messaging を有効にする

>[AZURE.NOTE]この手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>。

[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

次に、この API キー値を使用して、モバイル サービスが GCM で認証し、アプリケーションの代わりにプッシュ通知を送信できるようにします。

## <a id="configure"></a>プッシュ要求を送信するようにモバイル サービスを構成する

1. [Azure 管理ポータル] にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

   	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2. **[プッシュ]** タブをクリックし、前の手順で GCM から取得した **API キー**値を入力して、**[保存]** をクリックします。

	>[AZURE.NOTE]古いモバイル サービスを使用してこのチュートリアルを実行する場合は、**[プッシュ]** タブの下に、**[拡張プッシュの有効化]** というリンクが表示される場合があります。このリンクをクリックすると、モバイル サービスが更新され、通知ハブと統合されます。この変更を元に戻すことはできません。運用環境のモバイル サービスで拡張プッシュ通知を有効にする方法の詳細については、 <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">このガイダンスを参照してください</a>。

   	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

	> [AZURE.IMPORTANT] ポータルの [プッシュ] タブで拡張プッシュ通知に対応する GCM の資格情報を設定した場合は、アプリに対して通知ハブを構成する目的で、それらの資格情報が通知ハブと共有されます。


これで、GCM および通知ハブと連携するようにモバイル サービスとアプリケーションが構成されました。

## <a id="add-push"></a>アプリケーションにプッシュ通知を追加する

### Android SDK バージョンの検証

[AZURE.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version-EC.md)]

次の手順は、Google Play サービスをインストールすることです。Google Cloud Messaging には、マニフェストの **minSdkVersion** プロパティが準拠する必要がある、開発およびテストに関する最小 API レベル要件があります。 

古いデバイスを使用している場合は、[Google Play Services SDK のセットアップに関するページ]を参考に、どれだけ小さな値を設定できるか判断し、適切に設定してください。

### プロジェクトへの Google Play Services の追加

[AZURE.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services-EC.md)]

### コードの追加

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push-EC.md)]


## <a id="update-scripts"></a>管理ポータルで登録されている挿入スクリプトを更新する

1. 管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。 

   	![](./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png)

2. **[todoitem]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。
   
  	![](./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png)

   	**TodoItem** テーブルで挿入が発生したときに呼び出される関数が表示されます。

3. insert 関数を次のコードに置き換え、**[保存]** をクリックします。

		function insert(item, user, request) {
		// Define a payload for the Google Cloud Messaging toast notification.
		var payload = {
		    "data": {
		        "message": item.text 
		    }
		};		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		        push.gcm.send(null, payload, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse, payload);
		                request.respond();
		                },              
		            error: function (pushResponse) {
		                console.log("Error Sending push:", pushResponse);
		                request.respond(500, { error: pushResponse });
		                }
		            });
		        },
		    error: function(err) {
		        console.log("request.execute error", err)
		        request.respond();
		    }
		  });
		}

   	これで、新しい insert スクリプトが登録されます。このスクリプトは [gcm オブジェクト]を使用して、挿入が成功した後に、登録されているすべてのデバイスにプッシュ通知を送信します。 

## <a id="test"></a>アプリケーションでプッシュ通知をテストする

Android フォンを USB ケーブルで直接接続するか、エミュレーターで仮想デバイスを使用する方法により、アプリケーションをテストできます。

### テスト用のエミュレーターの設定

このアプリケーションをエミュレーターで実行する場合は、Google API をサポートしている Android Virtual Device (AVD) を使用してください。

1. Eclipse を再開し、Package Explorer でプロジェクトを右クリックします。**[Properties]**、**[Android]** の順にクリックし、**[Google APIs]** をオンにしてから **[OK]** をクリックします。

	![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

  	これで、プロジェクトの対象が Google API になります。

2. **[Window]** で **[Android Virtual Device Manager]** を選択し、デバイスを選択してから **[Edit]** をクリックします。

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

3. **[Target]** で **[Google APIs]** を選択し、[OK] をクリックします。

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	これで、AVD が Google API を使用するようになります。

### テストの実行

1. Eclipse で **[Run]** メニューの **[Run]** をクリックして、アプリケーションを開始します。

2. アプリケーションで、意味のあるテキスト (たとえば、「新しいモバイル サービス タスク」) を入力し、**[Add]** ボタンをクリックします。

  	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

3. 画面の上端から下へスワイプし、通知を表示するためのデバイスの通知センターを開きます。


これで、このチュートリアルは終了です。


## <a name="next-steps"> </a>次のステップ

<!---このチュートリアルでは、Android アプリケーションでモバイル サービスと通知ハブを使用してプッシュ通知を送信できるようにする方法の基本について説明しました。次は、タグを使用して、プッシュ通知をモバイル サービスから認証ユーザーにのみ送信する方法を説明した、次のチュートリアル「[認証されたユーザーへのプッシュ通知の送信]」を行うことをお勧めします。

+ [認証されたユーザーへのプッシュ通知の送信]
	<br/>タグを使用してモバイル サービスから認証されたユーザーのみにプッシュ通知を送信する方法について説明します。

+ [登録者へのブロードキャスト通知の送信]
	<br/>ユーザーが興味のあるカテゴリに関してプッシュ通知を登録して、プッシュ通知を受信できるようにする方法について説明します。

+ [登録者へのテンプレートベース通知の送信]
	<br/>テンプレートを使用して、バックエンドでプラットフォームに固有のペイロードを作成する必要なくモバイル サービスからプッシュ通知を送信する方法について説明します。
-->

モバイル サービスと通知ハブについては次のトピックを参照してください。

* [データの使用]
  <br/>モバイル サービスを使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用]
  <br/>モバイル サービスを使用して、別のアカウントの種類のアプリケーションのユーザーを認証する方法について説明します。

* [通知ハブとは]
  <br/>通知ハブがすべての主要なクライアント プラットフォーム全体のアプリケーションに通知を配信するための動作を説明します。

* [通知ハブのデバッグ](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>通知ハブ ソリューションのトラブルシューティングおよびデバッグのガイダンスについて説明します。 

* [モバイル サービス向け Android クライアント ライブラリの使用方法]
  <br/>Android でモバイル サービスを使用する方法について説明します。

* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>モバイル サービスでビジネス ロジックを実装する方法を説明します。


<!-- Anchors. -->
[プッシュ通知用のアプリケーションを登録してモバイル サービスを構成する]: #register
[生成されたプッシュ通知コードを更新する]: #update-scripts
[データを挿入して通知を受け取る]: #test
[次のステップ]:#next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-android-get-started/
[データの使用]: /ja-jp/documentation/articles/mobile-services-android-get-started-data/
[認証の使用]: /ja-jp/documentation/articles/mobile-services-android-get-started-users
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js
[アプリケーション ユーザーへのプッシュ通知]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-js
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js
[Google Play Services SDK のセットアップに関するページ]: http://go.microsoft.com/fwlink/?LinkId=389801
[Azure 管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス向け Android クライアント ライブラリの使用方法]: /ja-jp/documentation/articles/mobile-services-android-how-to-use-client-library

[gcm オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293

[認証されたユーザーへのプッシュ通知の送信]: /ja-jp/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/

[通知ハブとは]: /ja-jp/documentation/articles/notification-hubs-overview/
[登録者へのブロードキャスト通知の送信]: /ja-jp/documentation/articles/notification-hubs-android-send-breaking-news/
[登録者へのテンプレートベース通知の送信]: /ja-jp/documentation/articles/notification-hubs-android-send-localized-breaking-news/

<!--HONumber=45--> 
