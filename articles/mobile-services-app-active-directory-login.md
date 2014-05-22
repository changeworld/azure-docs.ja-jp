<properties linkid="develop-mobile-how-to-guides-register-for-microsoft-waad-authentication" urlDisplayName="Azure Active Directory 認証用の登録" pageTitle="Azure Active Directory 認証用の登録 - モバイル サービス" metaKeywords="Azure でのアプリケーションの登録, Azure Active Directory 認証, アプリケーションの認証, モバイル サービスの認証" description="Azure モバイル サービス アプリケーションで Azure Active Directory 認証に登録する方法について説明します。" metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Azure Active Directory アカウント ログインを使用するためのアプリケーションの登録" authors="" />


# Azure Active Directory アカウント ログインを使用するためのアプリケーションの登録

このトピックでは、Azure モバイル サービスの認証プロバイダーとして Azure Active Directory を使用できるようにアプリケーションを登録する方法について説明します。

<div class="dev-callout"><b>注</b>
<p>また、Windows ストア アプリからシングル サインオン (SSO) またはプッシュ通知に対応するためにクライアント主体の認証を提供する場合は、アプリケーションを Windows ストアに登録することも検討します。詳細については、「<a href="/ja-jp/develop/mobile/how-to-guides/register-for-single-sign-on">Windows Live Connect シングル サインオンを使用するための Windows ストア アプリの登録</a>」を参照してください。</p>
</div>

1. [Azure 管理ポータル]にログインします。

2. 管理ポータルの **[Active Directory]** に移動し、目的のディレクトリをクリックします。

   ![][1] 

3. **[アプリケーション]** タブをクリックし、**[アプリケーションの追加]** をクリックします。

   ![][2]


4. XXX の **[Web アプリケーションや Web API]** を選択して、新しいアプリケーション ウィザードの指示に従います。シングル サインオンを有効にします。**[アプリケーション URL]** を求めるメッセージが表示されたら、モバイル サービス アプリケーションの URL を貼り付けます。


5. *** (さらに続く)***


これで、クライアント ID とクライアント シークレットの値をモバイル サービスに渡すことにより、Azure Active Directory を使用してアプリケーションで認証を使用する準備ができました。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-app-active-directory-login/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-app-active-directory-login/mobile-live-connect-app-api-settings.png

<!-- URLs. -->
[Azure 管理ポータル]: https://manage.windowsazure.com/

