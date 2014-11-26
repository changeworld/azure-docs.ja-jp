## <a name="register-mobile-service-aad"></a>モバイル サービスを Azure Active Directory に登録する

このセクションでは、Azure Active Directory にモバイル サービスを登録し、シングル サインオン権限の借用を許可するアクセス許可を構成します。

1.  「[How to Register with the Azure Active Directory (Azure Active Directory に登録する方法)][How to Register with the Azure Active Directory (Azure Active Directory に登録する方法)]」のトピックに従って、Azure Active Directory にアプリケーションを登録します。

2.  [Azure の管理ポータル][Azure の管理ポータル]で、Azure Active Directory の展開画面に戻り、目的のアクティブ ディレクトリをクリックします。

3.  **[アプリケーション]** タブをクリックし、目的のアプリケーションをクリックします。

4.  **[マニフェストの管理]** をクリックします。その後、**[マニフェストのダウンロード]** をクリックし、アプリケーション マニフェストをローカル ディレクトリに保存します。

![][0]

1.  Visual Studio でアプリケーション マニフェスト ファイルを開きます。ファイルの最上部で、次のように表示されたアプリケーションのアクセス許可の行を見つけます。

        "appPermissions": [],

    その行を、次のアプリケーション アクセス許可で置き換え、ファイルを保存します。

        "appPermissions": [
            {
                "claimValue": "user_impersonation",
                "description": "Allow the application access to the mobile service",
                "directAccessGrantTypes": [],
                "displayName": "Have full access to the mobile service",
                "impersonationAccessGrantTypes": [
                    {
                        "impersonated": "User",
                        "impersonator": "Application"
                    }
                ],
                "isDisabled": false,
                "origin": "Application",
                "permissionId": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
                "resourceScopeType": "Personal",
                "userConsentDescription": "Allow the application full access to the mobile service on your behalf",
                "userConsentDisplayName": "Have full access to the mobile service"
            }
        ],

2.  Azure の管理ポータルで、再度、アプリケーションの **[マニフェストの管理]** をクリックし、**[マニフェストのアップロード]** をクリックします。更新したアプリケーション マニフェストの場所を参照し、マニフェストをアップロードします。



  [How to Register with the Azure Active Directory (Azure Active Directory に登録する方法)]: /ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-dotnet-adal-register-service/mobile-services-aad-app-manage-manifest.png
