
1. [Azure ポータル]、**[Mobile Services]**、モバイル サービス、**[ダッシュボード]** の順にクリックして、**[モバイル サービス URL]** の値をメモします。

2. 次の 1 つ以上の認証プロバイダーにアプリを登録します。
   * [Google](mobile-services-how-to-register-google-authentication.md)
   * [Facebook](mobile-services-how-to-register-facebook-authentication.md)
   * [Twitter](mobile-services-how-to-register-twitter-authentication.md)
   * [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
   * [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md) 
   
   プロバイダーで生成されるクライアント ID とクライアント シークレット値をメモしておいてください。クライアント シークレットを配布したり、共有したりしないでください。

3. Azure ポータルで、**[Mobile Services]**、目的のモバイル サービス、**[ID]**、ID プロバイダー設定の順にクリックし、クライアント ID とプロバイダーのシークレット値を入力します。 
 
これで、使用している認証プロバイダーを扱えるように、アプリとモバイル サービスの両方が構成されました。必要に応じて、サポートする必要がある他の ID プロバイダーごとに、すべての手順を繰り返します。

    > [AZURE.IMPORTANT] Verify that you've set the correct redirect URI on your identity provider's developer site. As described in the linked instructions for each provider above, the redirect URI may be different for a .NET backend service vs. for a JavaScript backend service. An incorrectly configured redirect URI may result in the login screen not being displayed properly and the app malfunctioning in unexpected ways.

<!---HONumber=September15_HO1-->