

1. [Azure 管理ポータル](https://manage.windowsazure.com/)にログオンし、画面の下部にある **[+新規]** をクリックします。

2. **[App Services]**、**[Service Bus]**、**[通知ハブ]**、**[簡易作成]** の順にクリックします。

   	![](./media/notification-hubs-portal-create-new-hub/notification-hubs-create-from-portal.png)


3. **通知ハブ名**を入力します。該当する**リージョン**と**サブスクリプション**を選択します。
 
	ハブの作成先となる Service Bus 名前空間が既に存在する場合は、その**名前空間名**を選択します。それ以外の場合、ハブ名に基づいて作成される既定の**名前空間名**を必要に応じて使用してください。

	**[新しい通知ハブを作成する]** をクリックします。

   	![通知ハブのプロパティを設定](./media/notification-hubs-portal-create-new-hub/notification-hubs-create-from-portal2.png)

4. 名前空間と通知ハブが作成されると、Service Bus の名前空間が表示されます。前の手順で作成したハブの名前空間 (通常は ***通知ハブ名*-ns**) をクリックします。

5. 該当する名前空間ページで、上部にある **[Notification Hubs]** タブをクリックし、前の手順で作成した通知ハブをクリックします。これにより、新しい通知ハブのダッシュボードが開きます。

6. 新しいハブのダッシュボードで、**[接続文字列の表示]** をクリックします。2 つの接続文字列をメモします。これらは、後で使用します。

   	![](./media/notification-hubs-portal-create-new-hub/notification-hubs-view-connection-strings.png)

	![](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings.png)

<!---HONumber=Oct15_HO4-->