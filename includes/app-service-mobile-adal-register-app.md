1. [Azure Active Directory でモバイル アプリを構成する方法]に従って、モバイル アプリのバックエンドを Azure Active Directory テナントに登録します。

2. [Microsoft Azure の管理ポータル]の **[Active Directory]** に移動します。

   ![](./media/app-service-mobile-adal-register-app/app-service-navigate-aad.png)

3. ディレクトリを選択し、上部の **[アプリケーション]** タブを選択します。下部の **[追加]** をクリックして、新しいアプリの登録を作成します。 

4. **[組織で開発中のアプリケーションを追加]** をクリックします。

5. アプリケーションの追加ウィザードで、アプリケーションの**名前**を入力し、種類として **[ネイティブ クライアント アプリケーション]** をクリックします。その後、クリックして続行します。

6. **[リダイレクト URI]** ボックスで、App Service ゲートウェイ用の /login/done エンドポイントを入力します。この値は、https://contoso.azurewebsites.net/login/done と同様です。

7. ネイティブ アプリケーションが追加されたら **[構成]** タブをクリックします。**クライアント ID** をコピーします。この情報は後で必要になります。

8. ページを **[他のアプリケーションに対するアクセス許可]** セクションが表示されるまで下へスクロールし、**[アプリケーションの追加]** ボタンをクリックします。

9. 前に登録した Web アプリケーションを検索し、プラス アイコンをクリックします。次に、チェックをクリックしてダイアログ ボックスを閉じます。

10. 追加したばかりの新しいエントリで、**[デリゲートされたアクセス許可]** ドロップダウンを開き、**[<アプリ名> にアクセス]** を選択します。その後、**[保存]** をクリックします。

   ![](./media/app-service-mobile-adal-register-app/aad-native-client-add-permissions.png)

これで、アプリケーションが AAD 内に構成され、ユーザーは AAD サインオンを使用してログインできます。

[Microsoft Azure の管理ポータル]: https://manage.windowsazure.com/
[Azure Active Directory でモバイル アプリを構成する方法]: ../articles/app-service-how-to-configure-active-directory-authentication.md

<!---HONumber=Nov15_HO1-->