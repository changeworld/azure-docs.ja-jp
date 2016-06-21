1. Azure クラシック ポータルにログオンします。

2. ポータルの左のナビゲーション ウィンドウで、**[Service Bus]** をクリックします。

3. ポータルの下のウィンドウで、**[作成]** をクリックします。

    ![Select Create][select-create]
   
4. **[新しい名前空間を追加する]** ダイアログで、名前空間の名前を入力します。その名前が使用できるかどうかがすぐに自動で確認されます。

    ![Namespace name][namespace-name]
  
5. 名前空間の名前が有効であることを確認できたら、名前空間をホストする国またはリージョンを選択します。

6. ダイアログ ボックスの他のフィールドは、既定値 (**[メッセージング]** と **[標準階層]**) のままにして、[OK] チェック マークをクリックします。これで、システムによってサービス名前空間が作成され、有効になります。システムがアカウントのリソースを準備し 終わるまでに、数分間かかる場合があります。
 
    ![Created successfully][created-successfully]

###資格情報を取得する
1. 左側のナビゲーション ウィンドウで **[Service Bus]** ノードをクリックして、利用可能な名前空間の一覧を表示します。
 
    ![Select service bus][select-service-bus]
  
2. 表示された一覧から先ほど作成した名前空間を選択します。
 
    ![Select namespace][select-namespace]
 
3. **[接続情報]** をクリックします。

    ![Connection information][connection-information]
  
4. **[接続情報へのアクセス]** ウィンドウで、SAS キーとキー名を含む接続文字列を見つけます。

    ![接続情報にアクセスする][access-connection-information]
  
5. キーを書き留めておくか、クリップボードにコピーしておいてください。

<!--Image references-->

[select-create]: ./media/service-bus-create-namespace-portal/select-create.png
[namespace-name]: ./media/service-bus-create-namespace-portal/namespace-name.png
[created-successfully]: ./media/service-bus-create-namespace-portal/created-successfully.png
[select-service-bus]: ./media/service-bus-create-namespace-portal/select-service-bus.png
[select-namespace]: ./media/service-bus-create-namespace-portal/select-namespace.png
[connection-information]: ./media/service-bus-create-namespace-portal/connection-information.png
[access-connection-information]: ./media/service-bus-create-namespace-portal/access-connection-information.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[classic-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0615_2016-->