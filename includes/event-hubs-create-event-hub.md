## Event Hub を作成する

1. [Azure Portal][] にログインし、画面の左上にある **[新規]** をクリックします。

2. **[データ + 分析]** をクリックし、**[イベント ハブ]** をクリックします。

	![](./media/event-hubs-create-event-hub/create-event-hub9.png)

3. **[名前空間の作成]** ブレードで、名前空間の名前を入力します。その名前が使用できるかどうかがすぐに自動で確認されます。

	![](./media/event-hubs-create-event-hub/create-event-hub1.png)

4. 入力した名前空間の名前が使用できることを確認したら、価格レベル (Basic または Standard) を選択します。Azure サブスクリプション、リソース グループ、リソースが作成される場所を選択します。

2. **[作成]** をクリックして、名前空間を作成します。

6. Event Hubs 名前空間の一覧で、新しく作成された名前空間をクリックします。

	![](./media/event-hubs-create-event-hub/create-event-hub2.png)

7. 名前空間ブレードで **[Event Hubs]** をクリックします。

	![](./media/event-hubs-create-event-hub/create-event-hub3.png)

8. ブレード上部の **[イベント ハブの追加]** をクリックします。

	![](./media/event-hubs-create-event-hub/create-event-hub4.png)

3. イベント ハブの名前を入力し、**[作成]** をクリックします。

	![](./media/event-hubs-create-event-hub/create-event-hub5.png)

4. Event Hubs の一覧で、新しく作成したイベント ハブの名前をクリックします。

	![](./media/event-hubs-create-event-hub/create-event-hub6.png)

5. 名前空間ブレード (特定のイベント ハブ ブレードではなく) に戻り、**[共有アクセス ポリシー]** をクリックし、**[RootManageSharedAccessKey]** をクリックします。

	![](./media/event-hubs-create-event-hub/create-event-hub7.png)

5. コピー ボタンをクリックして **RootManageSharedAccessKey** 接続文字列をクリップボードにコピーします。この接続文字列を保存し、後でこのチュートリアルの中で使用します。

	![](./media/event-hubs-create-event-hub/create-event-hub8.png)

イベント ハブが作成され、イベントの送受信に必要な接続文字列が手元にあります。

[Azure portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0921_2016-->