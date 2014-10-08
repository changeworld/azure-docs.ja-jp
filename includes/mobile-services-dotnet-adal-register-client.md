## <a name="register-app-aad"></a>Azure Active Directory へのクライアント アプリケーションの登録

1. [Azure の管理ポータル][]の **[Active Directory]** に移動し、目的のディレクトリをクリックします。

 ![][]

1. 上部にある **[アプリケーション]** タブをクリックし、アプリケーションの **[追加]** をクリックします。

 ![][1]

1. **[組織で開発中のアプリケーションを追加]** をクリックします。

2. アプリケーションの追加ウィザードで、アプリケーションの**名前**を入力し、種類として **[ネイティブ クライアント アプリケーション]** をクリックします。その後、クリックして続行します。

 ![][2]

1. **[リダイレクト URI]** ボックスで、モバイル サービスの /login/done エンドポイントを入力します。この値は、<https://todolist.azure-mobile.net/login/done> と同様です。

 ![][3]

1. ネイティブ アプリケーションの **[構成]** タブをクリックし、**クライアント ID** をコピーします。この情報は後で必要になります。

 ![][4]

1. **[他のアプリケーションに対するアクセス許可]** セクションが表示されるまでページを下へスクロールし、先ほど登録したモバイル サービス アプリケーションに対するフル アクセスを付与します。その後、**[保存]** をクリックします。

 ![][5]

これで、モバイル サービスが、アプリケーションからシングル サインオン ログインを受信するように AAD で構成されます。

  [Azure の管理ポータル]: https://manage.windowsazure.com/
  []: ./media/mobile-services-dotnet-adal-register-client/mobile-services-select-aad.png
  [1]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-aad-applications-tab.png
  [2]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-selection.png
  [3]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-redirect-uri.png
  [4]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-client-id.png
  [5]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-add-permissions.png
