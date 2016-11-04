## <a name="register-mobile-service-aad"></a>モバイル サービスを Azure Active Directory に登録する
このセクションでは、Azure Active Directory にモバイル サービスを登録し、シングル サインオン権限の借用を許可するアクセス許可を構成します。

1. 「[How to Register with the Azure Active Directory (Azure Active Directory に登録する方法)]」のトピックに従って、Azure Active Directory にアプリケーションを登録します。
2. [Azure クラシック ポータル](https://manage.windowsazure.com/)で、Azure Active Directory 拡張機能に戻り、目的のアクティブ ディレクトリをクリックします。
3. **[アプリケーション]** タブをクリックし、目的のアプリケーションをクリックします。
4. **[マニフェストの管理]** をクリックします。その後、**[マニフェストのダウンロード]** をクリックし、アプリケーション マニフェストをローカル ディレクトリに保存します。
   
   ![](./media/mobile-services-dotnet-adal-register-service/mobile-services-aad-app-manage-manifest.png)
5. Visual Studio でアプリケーション マニフェスト ファイルを開きます。ファイルの最上部で、次のように表示されたアプリケーションのアクセス許可の行を見つけます。
   
        "oauth2Permissions": [],
   
    その行を、次のアプリケーション アクセス許可で置き換え、ファイルを保存します。
   
        "oauth2Permissions": [
            {
                "adminConsentDescription": "Allow the application access to the mobile service",
                "adminConsentDisplayName": "Have full access to the mobile service",
                "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
                "isEnabled": true,
                "origin": "Application",
                "type": "User",
                "userConsentDescription": "Allow the application full access to the mobile service on your behalf",
                "userConsentDisplayName": "Have full access to the mobile service",
                "value": "user_impersonation"
            }
        ],
6. [Azure クラシック ポータル](https://manage.windowsazure.com/)で、再度、アプリケーションの **[マニフェストの管理]** をクリックし、**[マニフェストのアップロード]** をクリックします。更新したアプリケーション マニフェストの場所を参照し、マニフェストをアップロードします。

<!-- URLs. -->
[How to Register with the Azure Active Directory (Azure Active Directory に登録する方法)]: ../articles/mobile-services/mobile-services-how-to-register-active-directory-authentication.md

<!---HONumber=AcomDC_0128_2016-->