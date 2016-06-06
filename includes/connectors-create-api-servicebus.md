### 前提条件

- [ServiceBus](https://azure.microsoft.com/services/service-bus/) アカウント  


ロジック アプリで ServiceBus アカウントを使用するには、ServiceBus アカウントに接続するロジック アプリを承認しておく必要があります。これは、Azure ポータルのロジック アプリ内から簡単に実行できます。

ServiceBus アカウントに接続するロジック アプリを承認する手順を次に示します。
1. ServiceBus への接続を作成するには、ロジック アプリ デザイナーのドロップダウン リストから **[Show Microsoft managed APIs (Microsoft Managed API を表示)]** を選択し、検索ボックスに「*ServiceBus*」と入力します。使用するトリガーまたはアクションを選択します。![](./media/connectors-create-api-servicebus/servicebus-1.png)  
2. これまでに ServiceBus への接続を作成したことがない場合は、ServiceBus の資格情報の入力を求められます。この資格情報を使用して、接続するロジック アプリの承認と、ServiceBus アカウントのデータへのアクセスが行われます。![](./media/connectors-create-api-servicebus/servicebus-2.png)  
3. 接続が作成されたら、ロジック アプリで他の手順を自由に実行できるようになります。![](./media/connectors-create-api-servicebus/servicebus-3.png)   

<!---HONumber=AcomDC_0525_2016-->