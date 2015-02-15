1. [Azure の管理ポータル](https://manage.windowsazure.com/)でディレクトリ ページの **[アプリケーション]** タブをクリックします。
  
2. 統合アプリケーションの登録をクリックします。

3. アプリケーション ページで **[構成]** をクリックし、ページの **[キー]** セクションまで下にスクロールします。 
4. 新しいキーの期間として、**[1 年]** をクリックします。その後、**[保存]** をクリックすると、ポータルに新しいキー値が表示されます。
5. 保存後に表示された **[クライアント ID]** と **[キー]** をコピーします。キー値は保存後に 1 回しか表示されないことに注意してください。 

    ![](./media/mobile-services-generate-aad-app-registration-access-key/client-id-and-key.png)

6. 統合アプリケーションの構成ページの最下部までスクロールして、アプリケーションの **[ディレクトリ データの読み取り]** アクセス許可を有効にし、**[保存]** をクリックします。

    ![](./media/mobile-services-generate-aad-app-registration-access-key/app-perms.png)


7. [Azure の管理ポータル](https://manage.windowsazure.com/)で、モバイル サービスに戻り、**[構成]** タブをクリックします。**[アプリケーション設定]** セクションまで下にスクロールし、次のアプリケーション設定を追加して、**[保存]** をクリックします。 

    <table border="1">
    <tr>
    <th>アプリケーション設定の名前</th><th>説明</th>
    </tr>
    <tr>
    <td>AAD_CLIENT_ID</td><td>前の手順で統合アプリケーションからコピーしたクライアント ID。</td>
    </tr>
    <tr>
    <td>AAD_CLIENT_KEY</td><td>前の手順で AAD 統合アプリケーションで生成されたアプリケーション キー。</td>
    </tr>
    <tr>
    <td>AAD_TENANT_DOMAIN</td><td>AAD ドメイン名。"mydomain.onmicrosoft.com" のような形式にする必要があります。</td>
    </tr>
    </table><br/>

 
    ![](./media/mobile-services-generate-aad-app-registration-access-key/aad-app-settings.png)
  
<!--HONumber=42-->
