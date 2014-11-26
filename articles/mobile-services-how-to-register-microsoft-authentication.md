<properties pageTitle="Register for Microsoft authentication - Mobile Services" metaKeywords="Azure registering application, Azure Microsoft authentication, application authenticate, authenticate mobile services" description="Learn how to register for Microsoft authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use a Microsoft Account login" authors="glenga" services="mobile-services" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Microsoft アカウント ログインを使用するためのアプリケーションの登録

このトピックでは、Azure のモバイル サービスの認証プロバイダーとして Live Connect を使用できるようにアプリケーションを登録する方法について説明します。

> [WACOM.NOTE] Windows 8.1 または Windows Phone 8.1 アプリの Microsoft アカウント認証を設定するには、[Windows ストア アプリケーション パッケージを Microsoft 認証に登録する][Windows ストア アプリケーション パッケージを Microsoft 認証に登録する]を参照してください。

1.  [Live Connect デベロッパー センター][Live Connect デベロッパー センター] アプリケーション管理のページに移動し、必要な場合は Microsoft アカウントでログオンします。

2.  **[アプリケーションの作成]** をクリックし、**[アプリケーション名]** に名前を入力して、**[同意する]** をクリックします。

    ![][0]

    これでアプリケーションが Live Connect に登録されます。

3.  **[API 設定]** をクリックして、**[リダイレクト URL]** に `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` の値を入力し、**[保存]** をクリックします。

    > [WACOM.NOTE] Visual Studio を使用して Azure に発行された .NET バックエンド モバイル サービスについては、リダイレクト URL はユーザーのモバイル サービスの URL の末尾にパス *signin-microsoft* .NET サービスとしてのユーザーのモバイル サービスを追加したものです。例: `https://todolist.azure-mobile.net/signin-microsoft`.

    ![][1]

    これで、アプリでの Microsoft アカウント認証が可能になります。

    > [WACOM.NOTE] 既存の Live Connect アプリの登録には、まず **[リダイレクト セキュリティ強化]** を有効にする必要があります。

4.  **[アプリ設定]** をクリックして、**[クライアント ID]** と **[クライアント シークレット]** の値を書き留めます。

    ![][2]

    <div class="dev-callout"><b>セキュリティに関する注意</b>
<p>クライアント シークレットは、重要なセキュリティ資格情報です。クライアント シークレットは、他のユーザーと共有したり、アプリケーションで配信したりしないでください。</p>
</div>

これで、クライアント ID とクライアント シークレットの値をモバイル サービスに渡すことにより、Microsoft アカウントを使用してアプリケーションで認証を使用する準備ができました。

 
 


  [Windows ストア アプリケーション パッケージを Microsoft 認証に登録する]: /ja-jp/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
  [Live Connect デベロッパー センター]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  [0]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
  [1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png
  [2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
