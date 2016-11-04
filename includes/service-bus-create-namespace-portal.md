1. [Azure ポータル][Azure ポータル]にログオンします。
2. ポータルの左側のナビゲーション ウィンドウで **[新規]** をクリックし、**[Enterprise Integration]**、**[Service Bus]** の順にクリックします。
3. **[名前空間の作成]** ダイアログで、名前空間の名前を入力します。その名前が使用できるかどうかがすぐに自動で確認されます。
4. 入力した名前空間の名前が使用できることを確認したら、価格レベル (Basic、Standard、Premium) を選択します。
5. **[サブスクリプション]** フィールドで、名前空間を作成する Azure サブスクリプションを選択します。
6. **[リソース グループ]** フィールドで、名前空間を追加する既存のリソース グループを選択するか、新しいリソース グループを作成します。
7. **[場所]** で、名前空間をホストする国またはリージョンを選択します。
   
    ![Create namespace][create-namespace]
8. **[作成]** をクリックします。これで、システムによってサービス名前空間が作成され、有効になります。システムがアカウントのリソースを準備し 終わるまでに、数分間かかる場合があります。

### 管理資格情報の取得
1. 名前空間の一覧で、新しく作成した名前空間の名前をクリックします。
2. 名前空間ブレードで、**[共有アクセス ポリシー]** をクリックします。
3. **[共有アクセス ポリシー]** ブレードで、**[RootManageSharedAccessKey]** をクリックします。
   
    ![connection-info][connection-info]
4. **[ポリシー: RootManageSharedAccessKey]** ブレードで、**[Connection string–primary key (接続文字列 - プライマリ キー)]** の横にあるコピー ボタンをクリックし、後で使用するために接続文字列をクリップボードにコピーします。この値をメモ帳などに一時的に貼り付けます。
   
    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[Azure ポータル]: https://portal.azure.com

<!---HONumber=AcomDC_0921_2016-->