<properties linkid="develop-net-tutorials-push-notifications-to-users-ios" urlDisplayName="Push Notifications to Users (iOS)" pageTitle="Push notifications to users (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# モバイル サービスによるユーザーへのプッシュ通知

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/ja-jp/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS" class="current">iOS</a><a href="/ja-jp/develop/mobile/tutorials/push-notifications-to-users-android" title="Android">Android</a>
</div>

このトピックは、Apple Push Notification Service (APNS) トークンを格納する新しいテーブルを追加して、[以前のプッシュ通知のチュートリアル][以前のプッシュ通知のチュートリアル]を拡張したものです。これらのトークンを使用して、iPhone または iPad アプリケーションのユーザーにプッシュ通知を送信できます。

このチュートリアルでは、アプリケーションでプッシュ通知を更新するための次の手順について説明します。

1.  [Devices テーブルを作成する][Devices テーブルを作成する]
2.  [アプリケーションを更新する][アプリケーションを更新する]
3.  [サーバー スクリプトを更新する][サーバー スクリプトを更新する]
4.  [プッシュ通知の動作を確認する][プッシュ通知の動作を確認する]

このチュートリアルは、モバイル サービスのクイック スタートと、1 つ前のチュートリアル「[モバイル サービスでのプッシュ通知の使用][以前のプッシュ通知のチュートリアル]」の内容を前提としています。このチュートリアルを開始する前に、「[モバイル サービスでのプッシュ通知の使用][以前のプッシュ通知のチュートリアル]」を完了している必要があります。

## <a name="create-table"></a>

## <span class="short-header">テーブルの作成</span>新しい Devices テーブルを作成します


1.  [Azure の管理ポータル][Azure の管理ポータル]にログインし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

    ![][0]

2.  **[データ]** タブをクリックし、**[作成]** をクリックします。

    ![][1]

    **[新しいテーブルの作成]** ダイアログ ボックスが表示されます。

3.  すべてのアクセス許可について既定の **[アプリケーション キーを持つユーザー]** 設定をそのままにし、**[テーブル名]** に「*Devices*」と入力してチェック ボタンをクリックします。

    ![][2]

    **Devices** テーブルが作成されます。このテーブルには、項目データとは別にプッシュ通知を送信するために使用されるデバイス トークンが格納されます。

次は、**TodoItem** テーブルの代わりにこの新しいテーブルにデータを格納するようにプッシュ通知アプリケーションを変更します。

## <a name="update-app"></a>アプリケーションを更新する

1.  Xcode で、QSTodoService.h ファイルを開き、次のメソッド宣言を追加します。

        // Declare method to register device token for other users
        - (void)registerDeviceToken:(NSString *)deviceToken;

    これにより、AppDelegate が deviceToken をモバイル サービスに登録できるようになります。

2.  QSTodoService.m で、次のインスタンス メソッドを追加します。

        // Instance method to register deviceToken in Devices table.
        // Called in AppDelegate.m when APNS registration succeeds.
        - (void)registerDeviceToken:(NSString *)deviceToken
        {
            MSTable *devicesTable = [self.client tableWithName:@"Devices"]; 
            NSDictionary *device = @{ @"deviceToken" : deviceToken };

            // Insert the item into the devices table and add to the items array on completion
            [devicesTable insert:device completion:^(NSDictionary *result, NSError *error) {
                if (error) {
                    NSLog(@"ERROR %@", error);
                }
            }];
        }

    これにより、他の呼び出し元がデバイス トークンをモバイル サービスに登録できるようになります。

3.  QSAppDelegate.m ファイルで、次の import ステートメントを追加します。

        #import "QSTodoService.h"

    このコードにより、AppDelegate が TodoService の実装を認識できるようになります。

4.  QSAppDelegate.m で、**didRegisterForRemoteNotificationsWithDeviceToken** メソッドを次のコードに置き換えます。

        // We have registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {

           // Register the APNS deviceToken with the Mobile Service Devices table.
           NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
           NSString *token = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];

           QSTodoService *instance = [QSTodoService defaultService];
           [instance registerDeviceToken:token];
        }

5.  QSTodoListViewController.m で、**(IBAction)onAdd** メソッドを見つけ、次のコードを*削除*します。

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

    これを次のコードに置き換えます。

        // We removed the delegate; this application no longer passes the deviceToken here.
        // Remove the device token from the payload
        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };

アプリケーションは、プッシュ通知をデバイスに送り返すために使用されるデバイス トークンを、新しい Devices テーブルを使用して格納するように更新されました。

## <a name="update-scripts"></a>サーバー スクリプトを更新する

1.  管理ポータルで、**[データ]** タブをクリックし、**Devices** テーブルをクリックします。

    ![][3]

2.  **[devices]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。

    ![][4]

    **Devices** テーブルで挿入が発生したときに呼び出される関数が表示されます。

3.  insert 関数を次のコードに置き換え、**[保存]** をクリックします。

        function insert(item, user, request) {
           var devicesTable = tables.getTable('Devices');
           devicesTable.where({
               token: item.token
           }).read({
               success: insertTokenIfNotFound
           });

           function insertTokenIfNotFound(existingTokens) {
               if (existingTokens.length > 0) {
                   request.respond(200, existingTokens[0]);
               } else {
                   request.execute();
               }
           }
        }

    このスクリプトは、**Devices** テーブルに同じトークンを持つ既存のデバイスがないかチェックします。insert は、一致するデバイスが見つからないときだけ先に進みます。これにより、デバイス レコードの重複が防止されます。

4.  **[TodoItem]** をクリックし、**[スクリプト]** タブをクリックして、**[挿入]** を選択します。

    ![][5]

5.  insert 関数を次のコードに置き換え、**[保存]** をクリックします。

        function insert(item, user, request) {
          request.execute({
              success: function() {
                  request.respond();
                  sendNotifications();
              }
          });

          function sendNotifications() {
              var devicesTable = tables.getTable('Devices');
              devicesTable.read({
                  success: function(devices) {
                      // Set timeout to delay the notifications, 
                      // to provide time for the app to be closed 
                      // on the device to demonstrate toast notifications.
                      setTimeout(function() {
                          devices.forEach(function(device) {

                              push.apns.send(device.deviceToken, {
                                  alert: "Toast: " + item.text,
                                  payload: {
                                      inAppMessage: 
                                      "Hey, a new item arrived: '" + 
                                      item.text + "'"
                                  }
                              });
                          });
                      }, 2500);
                  }
              });
          }

        }

    この挿入スクリプトは、**Devices** テーブルに格納されているすべてのデバイスにプッシュ通知 (挿入された項目のテキストと共に) を送信します。

## <a name="test"></a><span class="short-header">アプリケーションのテスト</span>アプリケーションでプッシュ通知をテストする

1.  **[Run]** を押して、プロジェクトをビルドし、iOS 対応のデバイスでアプリケーションを開始します。アプリケーションで、意味のあるテキスト (たとえば、「*新しいモバイル サービス タスク*」) を入力し、プラス (**[+]**) アイコンをクリックします。

    ![][6]

2.  通知が受信されたことを確認し、**[OK]** をクリックして通知を破棄します。

    ![][7]

3.  手順 2 を繰り返してすぐにアプリケーションを閉じ、次のトーストが表示されることを確認します。

    ![][8]

これで、このチュートリアルは終了です。

## 次のステップ

これで、プッシュ通知の基本について説明するチュートリアルは終了です。次のモバイル サービスのトピックの詳細を確認することをお勧めします。

-   [データの使用][データの使用]
    Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

-   [認証の使用][認証の使用]
    Windows アカウントを使用してアプリケーションのユーザーを認証する方法について説明します。

-   [モバイル サービスのサーバー スクリプト リファレンス][モバイル サービスのサーバー スクリプト リファレンス]
    サーバー スクリプトの登録および使用について説明します。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [以前のプッシュ通知のチュートリアル]: /ja-jp/develop/mobile/tutorials/get-started-with-push-ios
  [Devices テーブルを作成する]: #create-table
  [アプリケーションを更新する]: #update-app
  [サーバー スクリプトを更新する]: #update-scripts
  [プッシュ通知の動作を確認する]: #test-app
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-ios-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-ios-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-ios-push-notifications-app-users/mobile-create-devices-table.png
  [3]: ./media/mobile-services-ios-push-notifications-app-users/mobile-portal-data-tables-devices.png
  [4]: ./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-devices.png
  [5]: ./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-push2.png
  [6]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push2-ios.png
  [7]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push3-ios.png
  [8]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push4-ios.png
  [データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-ios
  [認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-ios
  [モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
