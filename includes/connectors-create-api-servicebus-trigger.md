この例では、新しい項目が Service Bus キューに対するものである場合、ロジック アプリ ワークフローを開始するために、**[Service Bus - When a message is received in a queue (Service Bus - メッセージをキューで受信する場合)]** トリガーがどのように使用されるのかを示します。

>[AZURE.NOTE]まだ Service Bus に *"接続"* を作成していない場合は、Service Bus 接続文字列で署名するよう求められます。

1. Logic Apps デザイナーの検索ボックスに「*service bus*」と入力し、**[Service Bus - When a message is received in a queue (Service Bus - メッセージをキューで受信する場合)]** トリガーを選択します。  
![Service Bus トリガー イメージ 1](./media/connectors-create-api-servicebus/trigger-1.png)   
- **[When a message is received in a queue (メッセージをキューで受信する場合)]** コントロールが表示されます。  
![Service Bus トリガー イメージ 2](./media/connectors-create-api-servicebus/trigger-2.png)   
- 監視するトリガーのService Bus キューの名前を入力します。  
![Service Bus トリガー イメージ 3](./media/connectors-create-api-servicebus/trigger-3.png)   

これで、選択したキューで新しい項目を受信するときに、ワークフローでその他のトリガーとアクションの実行を開始するトリガーのあるロジック アプリが構成されました。

<!---HONumber=AcomDC_0727_2016-->
