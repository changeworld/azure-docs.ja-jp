<properties linkid="develop-mobile-tutorials-push-notifications-to-users-android" urlDisplayName="" pageTitle="Push notifications to users (Android ) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# モバイル サービスによるユーザーへのプッシュ通知

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/tutorials/push-notifications-to-users-android" title="Android" class="current">Android</a>
</div>

<div class="dev-onpage-left-content">
<p>このトピックは、<a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-android">以前のプッシュ通知のチュートリアル</a>を拡充したもので、Google Cloud Messaging (GCM) 登録 URI を格納するテーブルが新たに追加されています。このテーブルを使用することで、Android アプリケーションの複数のユーザーにプッシュ通知を送信できます。このチュートリアルでは、ToDoList テーブルへの挿入が実行されるたびに、1 回の更新によってすべての登録デバイスに対するプッシュ通知を生成します。前のチュートリアルでは、挿入を実行するデバイスに対してのみ通知が送信されていました。</p>
</div>

このチュートリアルでは、アプリケーションでプッシュ通知を更新するための次の手順について説明します。

1.  [Registration テーブルを作成する][Registration テーブルを作成する]
2.  [アプリケーションを更新する][アプリケーションを更新する]
3.  [サーバー スクリプトを更新する][サーバー スクリプトを更新する]
4.  [プッシュ通知の動作を確認する][プッシュ通知の動作を確認する]

このチュートリアルは、モバイル サービスのクイック スタートと、1 つ前のチュートリアル「[モバイル サービスでのプッシュ通知の使用][以前のプッシュ通知のチュートリアル]」の内容を前提としています。このチュートリアルを開始する前に、「[モバイル サービスでのプッシュ通知の使用][以前のプッシュ通知のチュートリアル]」を完了している必要があります。

## <a name="create-table"></a>新しいテーブルを作成する

1.  [Azure の管理ポータル][Azure の管理ポータル]にログインし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

    ![][]

2.  **[データ]** タブをクリックし、**[作成]** をクリックします。

    ![][1]

    **[新しいテーブルの作成]** ダイアログ ボックスが表示されます。

3.  すべてのアクセス許可について既定の **[アプリケーション キーを持つユーザー]** 設定をそのままにし、**[テーブル名]** に「*Registration*」と入力してチェック ボタンをクリックします。

    ![][2]

    **Registration** テーブルが作成されます。このテーブルには、項目データとは別にプッシュ通知を送信するために使用される登録 URI が格納されます。

次は、**TodoItem** テーブルの代わりにこの新しいテーブルに登録データを格納するようにプッシュ通知アプリケーションを変更します。

## <a name="update-app"></a>アプリケーションを更新する

1.  Package Explorer の Eclipse で (`src` ノードの下にある) パッケージを右クリックし、**[新規]**、**[クラス]** の順にクリックします。

2.  **[Name]** に「`Registration`」と入力してを **[Finish]** クリックします。

    ![][3]

    これで、新しい Registration クラスが作成されます。

3.  ファイル ToDoItem.java を開き、次のコードを切り取ります。

        @com.google.gson.annotations.SerializedName("handle")
        private String mHandle;

        public String getHandle() {
            return mHandle;
        }

        public final void setHandle(String handle) {
            mHandle = handle;
        }

4.  前の手順で切り取ったコードを、作成済みの **Registration** クラスの本文に貼り付けます。

5.  **Registration** クラスに、次のコードを追加します。

        @com.google.gson.annotations.SerializedName("id")
        private int mId;

        /**
         * Returns the item id
         */
        public int getId() {
            return mId;
        }

        /**
         * Sets the item id
         * 
         * @param id : id to set
         */
        public final void setId(int id) {
            mId = id;
        }

6.  **ToDoActivity.java** ファイルを開き、`addItem` メソッドの次の行を削除します。

        item.setHandle(MyHandler.getHandle());

7.  `mClient` プロパティを探して、次のコードと置き換えます。

        /**
         * Mobile Service Client reference
         */
        private static MobileServiceClient mClient;

        /**
         * Returns the client reference
         */
        public static MobileServiceClient getClient() {
            return mClient;
        }

8.  **MyHandler** ファイルで、次の import ステートメントを追加します。

        import android.util.Log;

        import com.microsoft.windowsazure.notifications.NotificationsHandler;

        import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
        import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
        import com.microsoft.windowsazure.mobileservices.TableOperationCallback;

9.  次のコードを `onRegistered` メソッドの最終行に追加します。

        MobileServiceClient client = ToDoActivity.getClient();
        MobileServiceTable<Registration> registrations = client.getTable(Registration.class);

        // Create a new Registration
        Registration registration = new Registration();
        registration.setHandle(gcmRegistrationId);

        // Insert the new Registration
        registrations.insert(registration, new TableOperationCallback<Registration>() {

            public void onCompleted(Registration entity, Exception exception, ServiceFilterResponse response) {

                if (exception != null) {
                    Log.e("MyHandler", exception.getMessage());
                } else {
                    Log.e("MyHandler", "Registration OK");
                }
            }
        });

これで、アプリケーションが複数のユーザーに対するプッシュ通知をサポートするように更新されました。

## <a name="update-scripts"></a>サーバー スクリプトを更新する

1.  管理ポータルで、**[データ]** タブをクリックし、**Registration** テーブルをクリックします。

    ![][4]

2.  **[Registration]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。

    ![][5]

    **Registration** テーブルで挿入が発生したときに呼び出される関数が表示されます。

3.  insert 関数を次のコードに置き換え、**[保存]** をクリックします。

        function insert(item, user, request) {
            var registrationTable = tables.getTable('Registration');
            registrationTable
                .where({ handle: item.handle })
                .read({ success: insertRegistrationIfNotFound });
            function insertRegistrationIfNotFound(existingRegistrations) {
                if (existingRegistrations.length > 0) {
                    request.respond(200, existingRegistrations[0]);
                } else {
                    request.execute();
                }
            }
        }

    このスクリプトは、**Registration** テーブルに同じ URI トークンを持つ既存の登録があるかどうかを調べます。insert の実行は、一致する登録が見つからなかった場合にのみ、先に進みます。これにより、登録レコードの重複が防止されます。

4.  **[TodoItem]** をクリックし、**[スクリプト]** タブをクリックして、**[挿入]** を選択します。

    ![][6]

5.  insert 関数を次のコードに置き換え、**[保存]** をクリックします。

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    // Write to the response and then send the notification in the background
                    request.respond();
                    sendNotifications(item.text);

                }
            });

        function sendNotifications(item_text) {
            var registrationTable = tables.getTable('Registration');
            registrationTable.read({
                success: function(registrations) {
                    registrations.forEach(function(registration) {
                        push.gcm.send(registration.handle, item_text, {
                            success: function(response) {
                                console.log('Push notification sent: ', response);
                            }, error: function(error) {
                                console.log('Error sending push notification: ', error);
                            }
                        });
                    });
                }
            });
        }


    この insert スクリプトでは、**Registration** テーブルに格納されているすべての登録にプッシュ通知が (挿入された項目のテキストと共に) 送信されます。

## <a name="test-app"></a> アプリケーションをテストする

1.  Eclipse で、**Run** メニューの **[Run]** をクリックして、アプリケーションを開始します。

2.  アプリケーションで、意味のあるテキスト (たとえば、「*新しいモバイル サービス タスク*」) を入力し、**[Add]** をクリックします。

3.  画面の下部に、一時的に空の通知ボックスが表示されます。

    ![][7]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.      ![][27]-->

これで、このチュートリアルは終了です。

## 次のステップ

これで、プッシュ通知の基本について説明するチュートリアルは終了です。次のモバイル サービスのトピックの詳細を確認することをお勧めします。

-   [データの使用][データの使用]
    Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

-   [認証の使用][認証の使用]
    Windows アカウントを使用してアプリケーションのユーザーを認証する方法について説明します。

-   [モバイル サービスのサーバー スクリプト リファレンス][モバイル サービスのサーバー スクリプト リファレンス]
    サーバー スクリプトの登録および使用について説明します。

-   [モバイル サービス向け Android クライアント ライブラリの使用方法][モバイル サービス向け Android クライアント ライブラリの使用方法]
    .NET と共に Mobile Services を使用する方法を説明します。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows Phone]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone"
  [iOS]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-ios "iOS"
  [Android]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-android "Android"
  [以前のプッシュ通知のチュートリアル]: /ja-jp/develop/mobile/tutorials/get-started-with-push-android
  [Registration テーブルを作成する]: #create-table
  [アプリケーションを更新する]: #update-app
  [サーバー スクリプトを更新する]: #update-scripts
  [プッシュ通知の動作を確認する]: #test-app
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-table.png
  [3]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-class.png
  [4]: ./media/mobile-services-android-push-notifications-app-users/mobile-portal-data-tables-registration.png
  [5]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-registration.png
  [6]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-push2.png
  [7]: ./media/mobile-services-android-push-notifications-app-users/mobile-push-icon.png
  [データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-android
  [認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-android
  [モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
  [モバイル サービス向け Android クライアント ライブラリの使用方法]: /ja-jp/develop/mobile/how-to-guides/work-with-android-client-library
