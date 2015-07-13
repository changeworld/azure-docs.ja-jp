
最初に、ID プロバイダーのサイトで ID プロバイダーにアプリを登録してから、モバイル サービスでこれらの資格情報を設定する必要があります。

1. [Microsoft Azure 管理ポータル]でモバイル サービスに移動し、**[ダッシュボード]** をクリックして、**[モバイル サービス URL]** の値をメモします。

2. サポートされている次のいずれかの ID プロバイダーにアプリを登録します。

	* [Google](mobile-services-how-to-register-google-authentication.md)
	* [Facebook](mobile-services-how-to-register-facebook-authentication.md)
	* [Twitter](mobile-services-how-to-register-twitter-authentication.md)
	* [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
	* [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md)  
	
    >[AZURE.IMPORTANT]モバイル サービスのリダイレクト URI を、ID プロバイダーの開発者向けサイトで正しく設定するようにしてください。上記リンクの各プロバイダーに関する手順で説明しているように、リダイレクト URL のパスは、.NET バックエンド モバイル サービス用 (`/signin-<provider>`) と JavaScript バックエンド サービス用 (`/login/<provider>`) で異なります。正しく構成されていないリダイレクト URI により、クライアントはアプリにサインインできません。<br/>クライアント シークレットを配布または共有しないでください。

3. [Microsoft Azure 管理ポータル]でモバイル サービスに戻り、**[ID]** タブをクリックして、ID プロバイダーから取得したアプリ ID とシークレットの値を入力します。

これで、認証用に 1 つの ID プロバイダーをサポートするようにアプリとモバイル サービスの両方が構成されました。この手順を繰り返すと、他の ID プロバイダーのサポートを追加できます。

[Microsoft Azure 管理ポータル]: https://manage.windowsazure.com/

<!---HONumber=July15_HO1-->