Azure Portal を表示しているブラウザーのウィンドウに戻ります。

#### <a name="add-hostname"></a>ホスト名を追加する

**[ホスト名の追加]** の横の **+** アイコンをクリックします。

![ホスト名の追加](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

#### <a name="validate-hostname"></a>ホスト名を検証する

先ほど CNAME レコードを構成した完全修飾ドメイン名 (例: `www.contoso.com`) を入力し、**[検証]** をクリックします。

**[ホスト名レコード タイプ]** ヘッダーでは、**[CNAME (www.example.com または任意のサブドメイン)]** オプションを選択します。

**[ホスト名の追加]** をクリックして、アプリに DNS 名を追加します。

![アプリに DNS 名を追加する](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

アプリの **[カスタム ドメイン]** ページに新しいホスト名が反映されるまでには、多少の時間がかかることがあります。 データを更新するには、ブラウザーを最新の情報に更新してみてください。