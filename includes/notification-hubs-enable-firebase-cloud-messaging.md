

1. [Firebase コンソール](https://firebase.google.com/console/)にサインインします。 Firebase プロジェクトがまだない場合は、新しく作成します。
2. プロジェクトを作成した後、**[Add Firebase to your Android app]\(Android アプリに Firebase を追加する\)** を選択します。 次に、表示される手順に従います。

    ![Android アプリへの Firebase の追加](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Firebase コンソールで、プロジェクトの歯車アイコンを選択します。 次に、**[Project Settings]\(プロジェクト設定\)** を選択します。

    ![[Project Settings]\(プロジェクト設定\) の選択](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. プロジェクト設定で **[General]\(一般\)** タブを選択します。 サーバーの API キーとクライアント ID が含まれた **google-services.json** ファイルをダウンロードします。
5. プロジェクト設定の **[Cloud Messaging]\(クラウド メッセージ\)** タブを選択し、**[Legacy server key]\(レガシ サーバー キー\)** の値をコピーします。 この値を使用して通知ハブのアクセス ポリシーを構成します。
