1. モバイル アプリのバックエンドを「[モバイル アプリを Azure Active Directory で構成する方法]」トピックに従って Azure Active Directory テナントに登録します。

2. [Azure 管理ポータル]の **Active Directory** に移動します。

   ![](./media/app-service-mobile-adal-register-app/app-service-navigate-aad.png)

3. ディレクトリを選択し、上部の **[アプリケーション]** タブをクリックします。新しいアプリケーションの登録を作成するには、下部にある **[追加]** をクリックします。 

4. **[組織で開発中のアプリケーションを追加]** をクリックします。

5. アプリケーションの追加ウィザードで、アプリケーションの**名前**を入力し、種類として **[ネイティブ クライアント アプリケーション]** をクリックします。その後、クリックして続行します。

6. **[リダイレクト URI]** ボックスで、App Service ゲートウェイ用の /login/done エンドポイントを入力します。この値は、https://contoso.azurewebsites.net/login/done のようにする必要があります。

7. ネイティブ アプリケーションが追加されたら **[構成]** タブをクリックします。**クライアント ID** をコピーします。この情報は後で必要になります。

8. ページを **[他のアプリケーションに対するアクセス許可]** セクションが表示されるまで下にスクロールし、**[アプリケーションの追加]** ボタンをクリックします。

9. 前に登録した Web アプリケーションを検索し、プラス アイコンをクリックします。次に、チェックをクリックしてダイアログ ボックスを閉じます。

10. 追加したばかりの新しいエントリで、**[アクセス許可の委任]** ドロップダウンを開き、**[アクセス (appName)]** をクリックします。その後、**[保存]** をクリックします。

   ![](./media/app-service-mobile-adal-register-app/aad-native-client-add-permissions.png)

これで、アプリケーションが AAD 内に構成され、ユーザーは AAD シングル サインオンを使用してログインできます。

[Azure 管理ポータル]: https://manage.windowsazure.com/
[モバイル アプリをAzure Active Directory で構成する方法]: ../articles/app-service-how-to-configure-active-directory-authentication-preview.md

<!--HONumber=49-->