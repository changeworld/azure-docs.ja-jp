
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

> [AZURE.IMPORTANT]ID プロバイダーの開発者向けサイトに、正しいリダイレクト URI が設定されていることをご確認ください。上記リンクの各プロバイダーに関する手順で説明しているように、リダイレクト URI は .NET バックエンド サービス用と JavaScript バックエンド サービス用とで異なる場合があります。リダイレクト URI が正しく構成されていないと、ログイン画面が正しく表示されず、アプリが予期しない異常を起こす場合があります。

<!---HONumber=Oct15_HO2-->