<properties linkid="develop-mobile-how-to-guides-register-for-microsoft-waad-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use an Azure Active Directory Account login" authors="dwrede" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="dwrede" />

# アプリケーションを登録して Azure Active Directory アカウント ログインを使用する

このトピックでは、Azure モバイル サービスの認証プロバイダーとして Azure Active Directory を使用できるようにアプリケーションを登録する方法について説明します。

<div class="dev-callout"><b>注</b>
<p>また、Windows ストア アプリからシングル サインオン (SSO) またはプッシュ通知に対応するためにクライアント主体の認証を提供する場合は、アプリを Windows ストアに登録することも検討します。詳細については、「<a href="/ja-jp/develop/mobile/how-to-guides/register-for-single-sign-on">Windows Live Connect シングル サインオンを使用するための Windows ストア アプリの登録</a>」を参照してください。</p>
</div>

1.  [Azure の管理ポータル][Azure の管理ポータル]にログインします。

2.  管理ポータルの **[Active Directory]** に移動し、目的のディレクトリをクリックします。

	![][0]

3.  **[アプリケーション]** タブをクリックし、**[アプリケーションの追加]** をクリックします。

	![][1]

4.  XXX の **[Web アプリケーションや Web API]** を選択して、新しいアプリケーション ウィザードの指示に従います。シングル サインオンを有効にします。**[アプリケーション URL]** を求めるメッセージが表示されたら、モバイル サービス アプリケーションの URL を貼り付けます。

5.  **(さらに続く)**

これで、クライアント ID とクライアント シークレットの値をモバイル サービスに渡すことにより、Azure Active Directory を使用してアプリケーションで認証を使用する準備ができました。

 
 


  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-app-active-directory-login/mobile-services-live-connect-add-app.png
  [1]: ./media/mobile-services-app-active-directory-login/mobile-live-connect-app-api-settings.png
