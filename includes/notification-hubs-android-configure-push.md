

1. [Azure 管理ポータル](https://manage.windowsazure.com/)にログオンし、画面の下部にある **[+新規]** をクリックします。

2. **[App Services]**、**[Service Bus]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

3. 通知ハブの名前を入力して、目的のリージョンを選択し、**[新しい通知ハブを作成する]** をクリックします。

   	![Set notification hub properties](./media/notification-hubs-android-configure-push/notification-hub-create-from-portal2.png)

4. **Service Bus** で前の手順で作成した名前空間 (通常は ***通知ハブ名*-ns**) をクリックします。

5. 名前空間で、上部にある **[Notification Hubs]** タブをクリックし、前の手順で作成した通知ハブをクリックします。

6. 上部にある **[構成]** タブをクリックし、前のセクションで取得した **API キー**値を入力して、**[保存]** をクリックします。

   	![Set the GCM API key in the Configure tab](./media/notification-hubs-android-configure-push/notification-hub-configure-android.png)

7. 上部にある **[ダッシュボード]** タブを選択し、**[接続文字列の表示]** をクリックします。2 つの接続文字列をメモします。

これで、通知ハブが GCM と連動するように構成されました。接続文字列により、アプリが通知を受信すると共に、プッシュ通知を送信するように登録されます。

<!---HONumber=July15_HO3-->