<properties linkid="develop-mobile-how-to-guides-register-for-active-directory-authentication" urlDisplayName="Azure Active Directory 認証用の登録" pageTitle="Azure Active Directory 認証用の登録 - モバイル サービス" metaKeywords="Azure でのアプリケーションの登録, Azure Active Directory 認証, アプリケーションの認証, モバイル サービスの認証" description="モバイル サービス アプリケーションで Azure Active Directory 認証に登録する方法について説明します。" title="Azure Active Directory アカウント ログインを使用するためのアプリケーションの登録" authors="" />

# Azure Active Directory アカウント ログインを使用するためのアプリケーションの登録

このトピックでは、Azure モバイル サービスの認証プロバイダーとして Azure Active Directory を使用できるようにアプリケーションを登録する方法について説明します。

> [WACOM.NOTE] モバイル サービスの Azure Active Directory 認証プロバイダーは現時点でプレビュー版です。プレビュー版に登録する場合は、<a href="mailto:mobileservices@microsoft.com">mobileservices@microsoft.com</a> までご連絡ください。登録しない場合、この認証プロバイダーはモバイル サービスの [ID] タブに表示されません。

> [WACOM.NOTE] Windows ストア アプリケーションからシングル サインオン (SSO) またはプッシュ通知に対応するためにクライアント主導の認証を提供する場合は、アプリケーションを Windows ストアに登録することも検討します。詳細については、「<a href="/ja-jp/develop/mobile/how-to-guides/register-for-single-sign-on">Windows Live Connect シングル サインオンを使用するための Windows ストア アプリの登録</a>」を参照してください。

1. [Azure 管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、目的のモバイル サービスをクリックします。

    ![][1]

2. モバイル サービスの **[ID]** タブをクリックします。

    ![][2]

3. **Azure Active Directory** ID プロバイダーのセクションまで下にスクロールし、そこに表示されている**アプリケーション URL** をコピーします。

    ![][3]

4. 管理ポータルの **[Active Directory]** に移動し、目的のディレクトリをクリックします。

    ![][4] 

5. 上部にある **[アプリケーション]** タブをクリックし、アプリケーションの **[追加]** をクリックします。

    ![][10]

6. **[組織で開発中のアプリケーションを追加]** をクリックします。

7. アプリケーションの追加ウィザードで、アプリケーションの**名前**を入力し、種類として **[Web アプリケーションや Web API]** をクリックします。その後、クリックして続行します。

    ![][5]

8. **[サインオン URL]** ボックスで、モバイル サービスの Active Directory ID プロバイダーの設定からコピーしたアプリケーション ID を貼り付けます。また、**[アプリケーション ID/URI]** ボックスに一意のリソース識別子を入力します。アプリケーションはこの URI を使用して Azure Active Directory にシングル サインオン要求を送信します。その後、クリックして続行します。

    ![][6]

9. アプリケーションのアクセスの種類として **[シングル サインオン]** をクリックして有効にします。クリックしてアプリケーションの追加ウィザードを終了します。

    ![][7]

10. アプリケーションが追加されたら、1 番下までスクロールします。**[アプリケーションによるディレクトリのデータの読み取りまたは書き込みを有効にします]** セクションをクリックして展開します。その後、アプリケーションの**クライアント ID** をコピーします。

    ![][8]


11. モバイル サービスの **[ID]** タブに戻ります。1 番下で、Azure Active Directory ID プロバイダーの **[クライアント ID]** 設定に貼り付けます。その後、**[保存]** をクリックします。

    ![][9]



これで、アプリケーションで認証に Azure Active Directory を使用する準備ができました。



<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
[2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
[3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
[4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
[5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
[6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
[7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-3-waad-auth.png
[8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
[9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
[10]:./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png

<!-- URLs. -->
[Azure の管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス Preview SDK のダウンロード]: http://www.windowsazure.com/

