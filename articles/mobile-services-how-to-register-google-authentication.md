<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Register for Google Authentication" pageTitle="Register for Google authentication - Mobile Services" metaKeywords="Azure registering application, Azure authentication, Google application authenticate, authenticate mobile services" description="Learn how to register your apps to use Google to authenticate with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Google login with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# モバイル サービスでの Google ログイン用のアプリケーションの登録

このトピックでは、Google を使用して Azure のモバイル サービスで認証できるようにアプリケーションを登録する方法について説明します。

<div class="dev-callout"><b>注</b>
<p>このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。新しい Google アカウントを作成するには、<a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> にアクセスしてください。</p>
</div>

1.  [Google apis][Google apis] Web サイトに移動して、Google アカウント資格情報でサインインします。**[プロジェクトの作成]** をクリックして**プロジェクト名**を入力し、**[作成]** をクリックします。

    ![][0]

2.  **[API & Auth]**、**[Credentials]**、**[Create new Client ID]** の順にクリックします。

    ![][1]

3.  **[Web アプリケーション]** を選択して、**[Authorized JavaScript Origins]** にモバイル サービスの URL を入力し、**[Authorized Redirect URI]** で生成された URL をモバイル サービスの URL の末尾に */login/google* を付けたものと置き換えて、**[Create client ID]** をクリックします。

    > [WACOM.NOTE] Visual Studio を使用して Azure に発行された .NET バックエンド モバイル サービスについては、モバイル サービスのリダイレクト URL は、そのモバイル サービスの URL の末尾に .NET サービスとしてパス *signin-google* を付加したものです。例：`https://todolist.azure-mobile.net/signin-google`.

    ![][2]

4.  **[Client ID for web applications]** で、**[クライアント ID]** と **[クライアント シークレット]** の値を書き留めます。

    ![][3]

    <div class="dev-callout"><b>セキュリティに関する注意</b>
<p>クライアント シークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。</p>
</div>

これで、クライアント ID とクライアント シークレットの値をモバイル サービスに渡すことにより、Google ログインを使用してアプリケーションで認証を使用する準備ができました。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
  [0]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
  [1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
  [2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
  [3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
