## Event Hub を作成する

1. [Azure クラシック ポータル][]にログオンし、画面の下部にある **[新規]** をクリックします。

2. **[アプリケーション サービス]**、**[Service Bus]**、**[Event Hub]**、**[簡易作成]** の順にクリックします。

	![][1]

3. Event Hub の名前を入力して、目的のリージョンを選択し、**[Create a new Event Hub (新しい Event Hub を作成する)]** をクリックします。

	![][2]

4. 特定のリージョンで既存の名前空間を明示的に選択していない場合は、ポータルによって名前空間が作成されます (通常は***イベント ハブ名*-ns**)。その名前空間 (この例では **eventhub-ns**) をクリックします。

	![][3]

5. ページの下部にある **[接続情報]** をクリックします。コピー ボタン (下図) をクリックして **RootManageSharedAccessKey** 接続文字列をクリップボードにコピーします。この接続文字列を保存し、後でこのチュートリアルの中で使用します。

	![][4]

イベント ハブが作成され、イベントの送受信に必要な接続文字列が手元にあります。 

[1]: ./media/event-hubs-create-event-hub/create-event-hub1.png
[2]: ./media/event-hubs-create-event-hub/create-event-hub2.png
[3]: ./media/event-hubs-create-event-hub/create-event-hub3.png
[4]: ./media/event-hubs-create-event-hub/create-conn-str1.png


