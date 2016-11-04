### 前提条件
[Service Bus](https://azure.microsoft.com/services/service-bus/) アカウントが必要です。

ロジック アプリで Azure Service Bus アカウントを使用するには、Service Bus アカウントに接続するロジック アプリを承認する必要があります。これは、Azure ポータルのロジック アプリ内から簡単に実行できます。

Service Bus アカウントに接続するロジック アプリを承認する手順を次に示します。

1. Service Bus への接続を作成するには、Logic Apps デザイナーのドロップダウン リストから **[Show Microsoft managed APIs (Microsoft Managed API を表示)]** を選択し、検索ボックスに「*service bus*」と入力します。使用するトリガーまたはアクションを選択します。  
    ![Service Bus の接続イメージ 1](./media/connectors-create-api-servicebus/servicebus-1.png)
2. これまでに Service Bus への接続を作成したことがない場合は、Service Bus の資格情報の入力を求められます。この資格情報を使用して、接続するロジック アプリの承認と、Service Bus アカウントのデータへのアクセスが行われます。Service Bus コネクタが、Service Bus 名前空間に接続文字列を必要し、**[管理]** の許可も必要になります。接続文字列が名前空間または特定のエンティティに適しているかを知るよい方法は、それに `EntityPath` パラメーターを含む場合、ロジック アプリの正しい接続文字列ではありません。  
    ![Service Bus の接続文字列](./media/connectors-create-api-servicebus/connectionstring.png)
3. 名前空間の接続文字列を受信した場合は、Logic Apps で API 接続に使用できます。  
    ![Service Bus の接続イメージ 2](./media/connectors-create-api-servicebus/servicebus-2.png)
4. 接続が作成されたら、ロジック アプリで他の手順を自由に実行できるようになります。  
    ![Service Bus の接続イメージ 3](./media/connectors-create-api-servicebus/servicebus-3.png)

<!---HONumber=AcomDC_0810_2016-->