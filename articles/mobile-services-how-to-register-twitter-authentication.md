<properties linkid="develop-mobile-how-to-guides-register-for-twitter-authentication" urlDisplayName="Register for Twitter Authentication" pageTitle="Register for Twitter authentication - Mobile Services" metaKeywords="Azure registering application, Azure Twitter authentication, application authenticate, authenticate mobile services, Mobile Services Twitter" description="Learn how to use Twitter authentication with your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Twitter login with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# モバイル サービスでの Twitter ログイン用のアプリケーションの登録

このトピックでは、Twitter を使用して Azure のモバイル サービスで認証できるようにアプリケーションを登録する方法について説明します。

<div class="dev-callout"><b>注</b>
<p>このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Twitter アカウントが必要になります。新しい Twitter アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a> にアクセスしてください。</p>
</div>

1.  [Twitter デベロッパー][Twitter デベロッパー]の Web サイトに移動し、Twitter アカウント資格情報でサインインして、**[Create a new application]** をクリックします。

    ![][]

2.  アプリケーションの**名前**、**説明**、**Web サイト**を入力し、**[Callback URL]** にモバイル サービスの URL の末尾にパス */login/twitter* を付けて入力します。

    > [WACOM.NOTE] Visual Studio を使用して Azure に発行された .NET バックエンド モバイル サービスについては、リダイレクト URL はユーザーのモバイル サービスの URL の末尾にパス *signin-twitter* .NET サービスとしてのユーザーのモバイル サービスを追加したものです。例： `https://todolist.azure-mobile.net/signin-twitter`.

    ![][1]

3.  ページの下部に記載されている条項を読んで同意し、正しい CAPTCHA 文字列を入力して、**[Create your Twitter application]** をクリックします。

    ![][2]

    これでアプリケーションが登録され、アプリケーションの詳細が表示されます。

4.  **[コンシューマー キー]** と **[コンシューマー シークレット]** の値を書き留めます。

    ![][3]

    <div class="dev-callout"><b>セキュリティに関する注意</b>
<p>コンシューマー シークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。</p>
</div>

5.  **[設定]** タブをクリックして下方向へスクロールし、**[Allow this application to be used to sign in with Twitter]** チェック ボックスをオンにして、**[Update this Twitter application's settings]** をクリックします。

    ![][4]

これで、コンシューマー キーとコンシューマー シークレットの値をモバイル サービスに渡すことにより、アプリケーションで Twitter ログインを認証に使用する準備ができました。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [twitter.com]: http://go.microsoft.com/fwlink/p/?LinkID=268287
  [Twitter デベロッパー]: http://go.microsoft.com/fwlink/p/?LinkId=268300
  []: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
  [1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
  [2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
  [3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
  [4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png
