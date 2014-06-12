<properties linkid="notification-hubs-how-to-guides-howto-notify-users-mobileservices" urlDisplayName="ユーザーへの通知" pageTitle="通知ハブによるモバイル サービスのユーザーへの通知" metaKeywords="" description="このチュートリアルでは、通知ハブを使用してモバイル サービスからの通知を受け取るように登録を行う方法を示します。" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="通知ハブによるユーザーへの通知" authors=""  solutions="" writer="glenga" manager="" editor=""  />
# <a name="getting-started"> </a>通知ハブによるユーザーへの通知

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ja-jp/manage/services/notification-hubs/notify-users" title="モバイル サービス" class="current">モバイル サービス</a><a href="/ja-jp/manage/services/notification-hubs/notify-users-aspnet" title="ASP.NET">ASP.NET</a>
</div> 

このチュートリアルでは、Azure 通知ハブを使用して特定のデバイスの特定のアプリケーション ユーザーにプッシュ通知を送信する方法について説明します。Azure のモバイル サービス バックエンドを使用して、クライアントを認証し、通知を生成します。このチュートリアルは、1 つ前のチュートリアル「**通知ハブの使用**」で作成した通知ハブが基になっています。通知登録コードは、クライアントからバックエンド サービスに移動します。これにより、クライアントがサービスによって正しく認証された後のみ登録が完了するようになります。さらに、通知ハブの資格情報がクライアント アプリケーションに配布されることもありません。登録時に要求されたタグも、このサービスによって制御されます。

このチュートリアルでは、次の基本的な手順について説明します。

+ [通知に登録できるようにモバイル サービスを更新する]
+ [ログインして登録を要求できるようにアプリケーションを更新する]
+ [通知を送信できるようにモバイル サービスを更新する]

## 前提条件

このチュートリアルの前に、次のチュートリアルを完了している必要があります。

+ **通知ハブの使用** ([Windows ストア C##][Get started Windows Store]/[iOS][Get started iOS]/[Android][Get started Android])

+ **モバイル サービスでの認証の使用** ([Windows ストア C##][Get started auth Windows Store]/[iOS][Get started auth iOS]/[Android][Get started auth Android])

このチュートリアルは、「**通知ハブの使用**」で作成したアプリケーションと通知ハブが基になっています。「**モバイル サービスでの認証の使用**」で構成した、認証されたモバイル サービスも使用します。

<div class="dev-callout"><b>メモ</b>
	<p>既定では、「<strong>モバイル サービスでの認証の使用</strong>」のチュートリアルでは Facebook 認証が使用されます。2 つの Windows ストア アプリケーションが 1 つの Live Connect 登録を共有することはできないため、このチュートリアルでは Microsoft アカウント認証を使用することができません。Microsoft アカウント認証を使用するには、Live Connect でモバイル サービスと通知ハブを同じアプリケーションに登録する必要があります。</p>
</div>

<h2><a name="register-notification"></a><span class="short-header">通知への登録</span>通知に登録できるようにモバイル サービスを更新する</h2>

通知への登録は、クライアントがサービスによって正しく認証された後のみ実行する必要があるため、モバイル サービスで定義されたカスタム API を使用して登録が実行されます。このカスタム API は、通知登録を要求するため、認証されたクライアントにより呼び出されます。このセクションでは、「**モバイル サービスでの認証の使用**」のチュートリアルを実行したときに定義した、認証されたモバイル サービスを更新します。

1. [Azure の管理ポータル][Management Portal]にログインして、**[Service Bus]**、名前空間、**[通知ハブ]** の順にクリックし、通知ハブを選択して **[接続情報]** をクリックします。

	![][6]

2. 通知ハブの名前をメモし、**DefaultFullSharedAccessSignature** の接続文字列をコピーします。

	![][7]

	この接続文字列は、通知への登録の通知の送信の両方で、通知ハブ名と共に使用します。

2. 管理ポータルを表示したまま、**[モバイル サービス]** をクリックしてアプリをクリックします。

   	![][0]

2. **[API]** タブをクリックし、**[カスタム API の作成]** をクリックします。

   	![][1]

   	**[新しいカスタム API の作成]** ダイアログ ボックスが表示されます。

3. **[API 名]** に「<em>register_notifications</em>」と入力し、**[POST アクセス許可]** で **[認証されたユーザーのみ]** を選択して、チェック ボタンをオンにします。

   	![][2]

  	この結果、HTTP POST メソッドを使用して呼び出す前にユーザーの認証を求める API が作成されます。他の HTTP メソッドは実装しないため、設定する必要はありません。

4. API テーブル内の新しい **register_notifications** エントリをクリックします。

	![][3]

5. **[スクリプト]** タブをクリックし、既存のコードを次のコードに置き換えます。

		exports.post = function(request, response) {

			// Create a notification hub instance.
		    var azure = require('azure');
		    var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
				'<FULL_SAS_CONNECTION_STRING>');
		
		    // Get the registration info that we need from the request. 
		    var platform = request.body.platform;
		    var userId = request.user.userId;
		    var installationId = request.header('X-ZUMO-INSTALLATION-ID');
		    
		    // Function called when registration is completed.
		    var registrationComplete = function(error, registration) {
		        if (!error) {
		            // Return the registration.
		            response.send(200, registration);
		        } else {
		            response.send(500, 'Registration failed!');
		        }
		    }
		    // Function called to log errors.
		    var logErrors = function(error) {
		        if (error) {
		            console.error(error)
		        }
		    }
		    // Get existing registrations.
		    hub.listRegistrationsByTag(installationId, function(error, existingRegs) {
		        var firstRegistration = true;
		        if (existingRegs.length > 0) {
		             for (var i = 0; i < existingRegs.length; i++) {
		                if (firstRegistration) {
		                    // Update an existing registration.
		                    if (platform === 'win8') {
		                        existingRegs[i].ChannelUri = request.body.channelUri;                        
		                        hub.updateRegistration(existingRegs[i], registrationComplete);                        
		                    } else if (platform === 'ios') {
		                        existingRegs[i].DeviceToken = request.body.deviceToken;
		                        hub.updateRegistration(existingRegs[i], registrationComplete);
		                    } else {
		                        response.send(500, 'Unknown client.');
		                    }
		                    firstRegistration = false;
		                } else {
		                    // We shouldn't have any extra registrations; delete if we do.
		                    hub.deleteRegistration(existingRegs[i].RegistrationId, logErrors);
		                }
		            }
                } else {
                    // Create a new registration.
                    if (platform === 'win8') {                
                        hub.wns.createNativeRegistration(request.body.channelUri, 
                        [userId, installationId], registrationComplete);
                    } else if (platform === 'ios') {
                        hub.apns.createNativeRegistration(request.body.deviceToken, 
                        [userId, installationId], registrationComplete);
                    } else {
                        response.send(500, 'Unknown client.');
                    }
                }
            });
        }

	このコードは、メッセージ本文からプラットフォームおよび deviceID 情報を取得します。このデータは、要求ヘッダーから取得されたインストール ID およびログインしているユーザーのユーザー ID と共に、登録の更新 (登録が存在する場合) または新しい登録の作成に使用されます。この登録には、ユーザー ID とインストール ID がタグ付けされます。

6. スクリプトを更新して _`<NOTIFICATION_HUB_NAME>`_ と _`<FULL_SAS_CONNECTION_STRING>`_ を通知ハブ用の値に置き換え、**[保存]** をクリックします。

	<div class="dev-callout"><b>メモ</b>
		<p><em><code>&lt;FULL_SAS_CONNECTION_STRING&gt;</code></em> で必ず <strong>DefaultFullSharedAccessSignature</strong> を使用してください。この要求により、カスタム API メソッドが登録を作成および更新できるようになります。</p>
	</div>

<h2><a name="update-app"></a><span class="short-header">アプリケーションの更新</span>ログインして登録を要求できるようにアプリケーションを更新する</h2>

次に、TodoList アプリケーションを更新し、新しいカスタム API を呼び出すことで通知の登録を要求する必要があります。

1. 使用しているクライアント プラットフォームに応じて、「**モバイル サービスを使用した現在のユーザーのプッシュ通知への登録**」で説明されている次のいずれかのバージョンの手順に従います。

	+ [Windows ストア C# バージョン][Client topic Windows Store C# version]
	+ [iOS バージョン][Client topic iOS version]

2. 更新されたアプリケーションを実行し、Facebook を使用してログインした後、通知に割り当てられた登録 ID が表示されることを確認します。

<h2><a name="send-notifications"></a><span class="short-header">通知の送信</span>通知を送信できるようにモバイル サービスを更新する</h2>

最後の手順では、モバイル サービスで通知を送信するコードを追加します。この通知コードは、**TodoItem** テーブルの挿入ハンドラーに登録されたサーバー スクリプトに追加されます。

1. 管理ポータルに戻り、**[データ]** タブ、**TodoItem** テーブルの順にクリックします。

   	![][4]

2. **todoitem** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。
   
  	![][5]

   	**TodoItem** テーブルで挿入が発生したときに呼び出される関数が表示されます。

3. insert 関数を次のコードに置き換えます。

		function insert(item, user, request) {
		    var azure = require('azure');
		    var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
		    '<FULL_SAS_CONNECTION_STRING>');
		
 		   // Create the payload for a Windows Store app.
		    var wnsPayload = '<toast><visual><binding template="ToastText02"><text id="1">New item added:</text><text id="2">' + item.text + '</text></binding></visual></toast>';
		
		    // Execute the request and send notifications.
		    request.execute({
		        success: function() {
		            // Write the default response and send a notification 
		            // to the user on all devices by using the userId tag.
		            request.respond();
		            // Send to Windows Store apps.
		            hub.wns.send(user.userId, wnsPayload, 'wns/toast', function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		            // Send to iOS apps.
		            hub.apns.send(user.userId, {
		                alert: item.text
		            }, function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		        }
		    });
		}
	
	Windows ストア アプリケーションと iOS アプリケーションの両方の現在ログインしているユーザーのタグに対して、通知の送信が試みられます。
		
4. スクリプトを更新して _`<NOTIFICATION_HUB_NAME>`_ と _`<FULL_SAS_CONNECTION_STRING>`_ を通知ハブ用の値に置き換え、**[保存]** をクリックします。

   	これで、新しい insert スクリプトが登録されます。このスクリプトは通知ハブを使用して、挿入要求で指定されたチャネルにプッシュ通知 (挿入されたテキスト) を送信します。

	<div class="dev-callout"><b>メモ</b>
		<p><em><code>&lt;FULL_SAS_CONNECTION_STRING&gt;</code></em> で必ず <strong>DefaultFullSharedAccessSignature</strong> を使用してください。この要求により、フル アクセス権 (登録されたクライアントに通知を送信することも可能) を持つ insert スクリプトが生成されます。</p>
	</div>

<h2><a name="test"></a><span class="short-header">アプリケーションのテスト</span>アプリケーションでプッシュ通知をテストする</h2>

通知が構成されたので、データを挿入して通知を生成することでアプリケーションをテストします。

1. アプリケーションを実行します。

	起動時に登録通知がもう一度表示されます。

2. 前回と同様にテキストを入力し、新しい項目を追加します。

	挿入が完了すると、アプリケーションは通知ハブからプッシュ通知を受け取ります。

	<div class="dev-callout"><b>メモ</b>
		<p>要求された通信の送信先プラットフォームに登録がない場合、バックエンドでエラーが発生します。その場合、このエラーは無視できます。テンプレートを使用してこの状況を回避する方法については、「<a href="/ja-jp/manage/services/notification-hubs/notify-users-xplat-mobile-services/" target="_blank">通知ハブによるユーザーへのクロスプラットフォーム通知の送信</a>」を参照してください。</p>
	</div>

3. (省略可能) クライアント アプリケーションを 2 つ目のデバイスにデプロイし、アプリケーションを実行してテキストを挿入します。

	各デバイスに通知が表示されます。

## <a name="next-steps"> </a>次のステップ
このチュートリアルが完了したら、次のチュートリアルを実行することをお勧めします。

+ **通知ハブを使用したニュース速報の送信 ([Windows ストア C#][Breaking news .NET] / [iOS][Breaking news iOS])** <br/>このプラットフォーム固有のチュートリアルでは、タグを使用して、ユーザーが興味を持つ種類の通知を購読できるようにする方法について説明します。

+ **[通知ハブによるユーザーへのクロスプラットフォーム通知の送信]**<br/>このチュートリアルでは、この「**通知ハブによるユーザーへの通知**」のチュートリアルを拡張し、プラットフォーム固有のテンプレートを使用して通知に登録します。これにより、サーバー側コードの 1 つのメソッドから通知を送信できるようになります。

通知ハブの詳細については、「[Azure 通知ハブ]」を参照してください。

<!-- Anchors. -->
[通知に登録できるようにモバイル サービスを更新する]: #register-notification
[ログインして登録を要求できるようにアプリケーションを更新する]: #update-app
[通知を送信できるようにモバイル サービスを更新する]: #send-notifications

<!-- Images. -->
[0]: ./media/notification-hubs-mobile-services-notify-users/mobile-services-selection.png
[1]: ./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create.png
[2]: ./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create2.png
[3]: ./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-select.png
[4]: ./media/notification-hubs-mobile-services-notify-users/mobile-portal-data-tables.png
[5]: ./media/notification-hubs-mobile-services-notify-users/mobile-insert-script-push2.png
[6]: ./media/notification-hubs-mobile-services-notify-users/notification-hub-select-hub-connection.png
[7]: ./media/notification-hubs-mobile-services-notify-users/notification-hub-connection-strings.png

<!-- URLs. -->
[Get started Windows Store]: /ja-jp/manage/services/notification-hubs/getting-started-windows-dotnet
[Get started iOS]: /ja-jp/manage/services/notification-hubs/get-started-notification-hubs-ios
[Get started Android]: /ja-jp/manage/services/notification-hubs/get-started-notification-hubs-android
[Get started auth Windows Store]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet/
[Get started auth iOS]: /ja-jp/develop/mobile/tutorials/get-started-with-users-ios/
[Get started auth Android]: /ja-jp/develop/mobile/tutorials/get-started-with-users-android/
[Client topic Windows Store C# version]: /ja-jp/manage/services/notification-hubs/register-users-mobile-services-dotnet 
[Client topic iOS version]: /ja-jp/manage/services/notification-hubs/register-users-ios 
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Management Portal]: https://manage.windowsazure.com/
[通知ハブによるユーザーへのクロスプラットフォーム通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users-xplat-mobile-services
[Breaking news .NET]: /ja-jp/manage/services/notification-hubs/breaking-news-dotnet
[Breaking news iOS]: /ja-jp/manage/services/notification-hubs/breaking-news-ios
[Azure 通知ハブ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj927170.aspx

